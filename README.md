# Pokémon GO Events: TRMNL plugin

Shows the Pokémon GO events happening now and coming up. Each event gets a **prep tip** and a picture of the Pokémon it features. Covers Community Day, Spotlight Hour, Raid Hour, raid rotations, Max Battles, GO Battle League, GO Pass, ticketed events, and more.

Event data comes from [LeekDuck](https://leekduck.com/events/) via the free [ScrapedDuck](https://github.com/bigfoott/ScrapedDuck) JSON feed. Pokémon artwork comes from [PokéAPI](https://pokeapi.co/). You don't need an account or API key.

Unofficial fan project. Not affiliated with Niantic, Nintendo, The Pokémon Company, or LeekDuck.

## Install

**Option A: import the zip.** Build it from `src/` (the zip isn't committed):

```bash
(cd src && zip ../pokemon-go-events.zip settings.yml *.liquid)
```

Then in TRMNL go to Plugins → Private Plugin → **Import**, and upload `pokemon-go-events.zip`.

**Option B: manual.** Create a Private Plugin, then:
- Strategy: **Polling**, URL: `https://raw.githubusercontent.com/bigfoott/ScrapedDuck/data/events.min.json`, refresh every 60 min.
- Paste `src/shared.liquid` into the **Shared** markup tab.
- Paste each `src/<layout>.liquid` into the Full / Half horizontal / Half vertical / Quadrant tabs.
- Copy the custom fields from `src/settings.yml` into the form builder.

## Settings
- **Look-ahead (days)**: how far ahead to list upcoming events (1–60, default 14).
- **Hide event types**: drop noisy types, such as GO Battle League or raid rotations.

## How it works
- All the logic is in `src/shared.liquid`. It filters events into *now* and *upcoming*, converts times, picks a prep tip, and finds the featured Pokémon. It also defines two templates every layout renders: `event_item` (one event row) and `title_bar`.
- Event times without a trailing `Z` are in your local time. Times with `Z` (global events) are UTC. The plugin uses your TRMNL account's time zone (`trmnl.user.utc_offset`) for both.
- **Pokémon images:** raid bosses, Spotlight Hour and Community Day use LeekDuck's icons from the feed. For other events, the plugin looks for Pokémon names in the headline and uses PokéAPI official artwork. Events without a specific Pokémon get a Poké Ball.
- **Prep tips** depend on the event type. For some types they also use event details, such as the Spotlight Hour bonus (Stardust → Star Piece, XP → Lucky Egg, Candy → Pinap) or Shadow vs. Mega raids. To change the advice, edit the `case` block in `shared.liquid`.

## Layout notes
- Every layout uses framework utilities only: no custom CSS or JavaScript. Layout files start at `.layout` and `.title_bar`; TRMNL adds the `view` wrapper.
- How many events show is left to the framework's overflow engine (`data-overflow="true"` with `data-overflow-counter`). Each list fills its space on any device and ends with "and N more". Line limits use `data-clamp`.
- The full view shows two columns in landscape and stacks them in portrait (`portrait:flex--col`). Titles grow on large screens (`lg:title--base`).
- Checked in a local preview against framework 3.4 on TRMNL OG (1-bit), TRMNL X landscape and TRMNL X portrait: nothing is cut off, and each screen is 87–97% filled.
- Two framework quirks shaped the markup:
  - **Rows are marked `flex`.** Line limits measure against the nearest `flex` or `grid` ancestor. Marking each row `item flex` makes them measure the text area, not the whole row with its image.
  - **The generic overflow container is used, not `.columns`.** On the scaled TRMNL X preview, the `.columns` engine mixed scaled and unscaled pixels and let lists overflow. Spacing between rows is padding (`pb--2`), not gap, so it's counted in each row's height.

## Local development
Use [trmnlp](https://github.com/usetrmnl/trmnlp): `trmnlp serve` from this folder (it reads `.trmnlp.yml`).
