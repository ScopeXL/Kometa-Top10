# Kometa Top 10 Streaming Collections

A [Kometa](https://kometa.wiki) integration that adds **Top 10 Movies** and/or **Top 10 TV Show** collections to your Plex library for major streaming platforms. Collections are sourced from curated TMDb lists that are updated multiple times a day.

## Supported Platforms

| Platform       | Kometa Key | Movies | TV Shows |
| -------------- | ---------- | ------ | -------- |
| Netflix        | netflix    | ✓      | ✓        |
| HBO Max        | hbo        | ✓      | ✓        |
| Disney+        | disney     | ✓      | ✓        |
| Prime Video    | prime      | ✓      | ✓        |
| Amazon         | amazon     | ✓      | ✓        |
| Apple TV       | apple      | ✓      | ✓        |
| Hulu           | hulu       | ✓      | ✓        |
| Vudu           | vudu       | ✓      | ✓        |
| Google Play    | google     | ✓      |          |
| Apple TV Store | applestore | ✓      | ✓        |
| Starz          | starz      | ✓      | ✓        |
| Paramount+     | paramount  | ✓      | ✓        |
| Peacock        | peacock    | ✓      | ✓        |
| Pluto TV       | pluto      | ✓      |          |

## Installation

Copy the `url:` from each library you wish to use. This will always fetch the latest version of the collections.
This is the easiest and fastest way to get going.

```yaml
libraries:
  Movies:
    collection_files:
      - url: https://raw.githubusercontent.com/ScopeXL/Kometa-Top10/main/Movies.yml
        template_variables:
          # optional overrides — see Customization below

  TV Shows:
    collection_files:
      - url: https://raw.githubusercontent.com/ScopeXL/Kometa-Top10/main/TVShows.yml
        template_variables:
          # optional overrides — see Customization below
```

## How It Works

Each collection uses a local `topten` template that:

- Pulls items from a public TMDb list maintained for that streaming service.
- Sets `sync_mode: sync` so items are added and removed automatically as the Top 10 changes.
- Sets `collection_order: custom` to preserve the ranked order from the TMDb list.
- Applies a Top 10 chart poster for each platform.

The `shared` and `arr` Kometa built-in templates are also applied, giving you access to all standard visibility, sorting, and Radarr/Sonarr integration variables.

## Customization

All customization is done through `template_variables` in your `config.yml`.

### Disable specific collections

The default template is an `opt-out` model. All collections are enabled by default. Use the `use_<<key>>` variables from the [shared](https://kometa.wiki/en/latest/defaults/chart/basic/#shared-template-variables) template to control which collections are managed.

```yaml
template_variables:
  use_starz: false
  use_hulu: false
```

### Enable specific collections

The default template is an `opt-out` model. All collections are enabled by default. Switching to an `opt-in` model is possible with the `use_all` variable from the [shared](https://kometa.wiki/en/latest/defaults/chart/basic/#shared-template-variables) template. For example, to disable all collections except `Netflix` you would use:

```yaml
template_variables:
  use_all: false # disable all Top 10 collections
  use_netflix: true # enable the Top 10 Netlix collection
```

### Control collection visibility

Use the `visible_*` variables from the [shared](https://kometa.wiki/en/latest/defaults/chart/basic/#shared-template-variables) template to control where a collection appears.

```yaml
template_variables:
  visible_library_netflix: false # hide from Plex library recommended view
  visible_home_netflix: true # show on Plex home (local users)
  visible_shared_netflix: true # show on Plex home (shared users)
```

**Note:** collection visibility settings are extremely powerful and useful if configured correctly. It gives you the ability to show/hide individual collections in multiple areas of the Plex app. I highly encourage you to spend some time learning how to use them. I've provided an advanced configuration example at the bottom to illustrate one way to utilize these settings.

### Change the poster style

The `style` variable controls which poster variant is used. The default is `color`; Kometa also ships a `white` variant.

```yaml
template_variables:
  style: white
```

### Radarr / Sonarr integration (arr template)

Because the [arr](https://kometa.wiki/en/latest/defaults/chart/basic/#shared-template-variables) template is applied, you can pass any standard Radarr or Sonarr `template_variables` to trigger automatic search/add behavior for items in the Top 10 lists. For example:

```yaml
template_variables:
  radarr_add_missing: true
  radarr_search: true
  sonarr_add_missing: true
  sonarr_search: true
```

### Pin a collection to a specific section

All collections default to `collection_section: "031"` (after the default [streaming collections](https://kometa.wiki/en/latest/defaults/both/streaming) if enabled). Override to reorder them relative to your other Kometa collections:

```yaml
template_variables:
  collection_section: "020"
```

### Advanced configuration example

This is one way to configure these collections that will take advantage of many `template_variables` for a unique and thorough user experience. It utilizes YAML anchors so we can re-use the variables in multiple parts of the config.

```yaml
##############################################################################
#                              YAML Anchors                                  #
# Anchors (&) define reusable blocks. Aliases (*) reference them elsewhere.  #
##############################################################################

# Top 10 Visibility Schedule (Movies & TV Shows) Settings
x-topten-visibility-settings: &topTenVisibilitySettings
  # ── Home Screen (Local & Shared Users) ────────────────────────────────────
  visible_home_netflix: weekly(friday|saturday|sunday)
  visible_shared_netflix: weekly(friday|saturday|sunday)
  visible_home_hbo: weekly(thursday|friday|saturday)
  visible_shared_hbo: weekly(thursday|friday|saturday)
  visible_home_apple: weekly(wednesday|thursday)
  visible_shared_apple: weekly(wednesday|thursday)
  visible_home_applestore: weekly(wednesday|thursday)
  visible_shared_applestore: weekly(wednesday|thursday)
  visible_home_hulu: weekly(tuesday|wednesday)
  visible_shared_hulu: weekly(tuesday|wednesday)
  visible_home_prime: weekly(monday|tuesday)
  visible_shared_prime: weekly(monday|tuesday)
  visible_home_amazon: weekly(monday|tuesday)
  visible_shared_amazon: weekly(monday|tuesday)
  visible_home_starz: weekly(monday|friday)
  visible_shared_starz: weekly(monday|friday)
  visible_home_vudu: weekly(sunday|monday)
  visible_shared_vudu: weekly(sunday|monday)
  visible_home_disney: weekly(wednesday|saturday)
  visible_shared_disney: weekly(wednesday|saturday)
  visible_home_paramount: weekly(tuesday|thursday)
  visible_shared_paramount: weekly(tuesday|thursday)
  visible_home_peacock: weekly(wednesday|sunday)
  visible_shared_peacock: weekly(wednesday|sunday)
  # ── Library Recommended Tab ───────────────────────────────────────────────
  visible_library_netflix: true
  visible_library_hbo: weekly(thursday|friday|saturday|sunday)
  visible_library_apple: weekly(wednesday|thursday|friday|saturday)
  visible_library_applestore: weekly(wednesday|thursday|friday|saturday)
  visible_library_hulu: weekly(tuesday|wednesday|thursday|friday)
  visible_library_prime: weekly(monday|tuesday|wednesday|thursday)
  visible_library_amazon: weekly(monday|tuesday|wednesday|thursday)
  visible_library_starz: weekly(monday|wednesday|friday|sunday)
  visible_library_vudu: weekly(sunday|monday|tuesday|wednesday)
  visible_library_disney: weekly(tuesday|wednesday|saturday|sunday)
  visible_library_paramount: weekly(monday|thursday|friday|saturday)
  visible_library_peacock: weekly(tuesday|wednesday|friday|saturday)

# Top 10 Visibility Schedule (Movies Only) Settings
# These platforms only list their "Top 10" movies
# only include this in the "Movies" library
x-topten-visibility-movie-settings: &topTenVisibilityMovieSettings
  visible_home_pluto: weekly(wednesday|thursday)
  visible_shared_pluto: weekly(wednesday|thursday)
  visible_library_pluto: weekly(wednesday|thursday|friday|saturday)
  visible_home_google: weekly(thursday|friday|saturday)
  visible_shared_google: weekly(thursday|friday|saturday)
  visible_library_google: weekly(thursday|friday|saturday|sunday)

libraries:
  Movies:
    collection_files:
      - url: https://raw.githubusercontent.com/ScopeXL/Kometa-Top10/main/Movies.yml
        template_variables:
          <<: *topTenVisibilitySettings # use the schedule defined above
          <<: *topTenVisibilityMovieSettings # include platforms with top 10 movies only
          radarr_add_missing_netflix: true # add missing movies from Netflix Top 10
          radarr_search_netflix: true # start a search for the missing movies

  TV Shows:
    collection_files:
      - url: https://raw.githubusercontent.com/ScopeXL/Kometa-Top10/main/TVShows.yml
        template_variables:
          <<: *topTenVisibilitySettings # use the schedule defined above
          style: white # use the white variant collection posters
          use_paramount: false # disable the Paramount+ Top 10 Shows collection
```

## Requirements

- [Kometa](https://kometa.wiki) v1.20 or later
- A TMDb API key configured in Kometa

## Credits

TMDb Top 10 lists are maintained by **ScopeXL**. Many collection posters sourced through the [Kometa Default Images](https://github.com/Kometa-Team/Default-Images) repository.
