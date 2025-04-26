# Home Assistant Media Player Selector Dashboard

This repository provides a Home Assistant Lovelace dashboard configuration for a dynamic media player selector. It features a dropdown with friendly names (e.g., "Living Room Volumio", "Spotify") and uses the `custom:mini-media-player` card to display the active media player. The setup includes automations and scripts to:
- Sync friendly names and entity IDs for a user-friendly interface.
- Automatically switch to the appropriate media player when Spotify plays, based on its source (e.g., Chromecast, Volumio, Fire TV Cube).
- Prioritize a specific media player (e.g., Kitchen Echo) when it’s playing.
- Switch Spotify’s source to a default device (e.g., "This Phone") when Fire TV Cubes return to their home screen.
- Refresh the dashboard when the dropdown is tapped.

This configuration is ideal for users with multiple media players, including Spotify, Fire TV Cubes, Chromecasts, and other devices, who want a seamless, prioritized media control interface.

## Features
- **Friendly Name Dropdown**: Select media players using human-readable names via `input_select.media_player_selector`.
- **Dynamic Media Player Display**: Uses `custom:state-switch` to show the selected media player’s `mini-media-player` card, driven by `input_select.media_player_selector_internal` (entity IDs).
- **Spotify Integration**: Enhanced configuration for Spotify with progress bar, source selector, and stateful volume control. Automatically selects the corresponding media player (e.g., Chromecast for "Art Frame Chromecast", Fire TV Cube for "Living Room Cube") when Spotify plays.
- **Priority Handling**: Prioritizes the Kitchen Echo when it’s playing, ensuring it takes precedence over other media players.
- **Source Switching**: Switches Spotify’s source to "This Phone" when Fire TV Cubes stop playing and return to the home screen (`com.amazon.tv.launcher`).
- **Dashboard Refresh**: Tapping the dropdown triggers `script.refresh_dashboard` to refresh specific browser instances using `browser_mod`.
- **Custom Artwork**: Supports custom artwork URLs for Fire TV and Echo devices.
- **Two-Way Sync**: Automations sync friendly names (`input_select.media_player_selector`) and entity IDs (`input_select.media_player_selector_internal`).

## Prerequisites
Before setting up, ensure you have:
- **Home Assistant**: Version 2023.1 or later recommended.
- **HACS**: For installing custom components (optional, can install manually).
- **Media Players**: Configured media player entities in Home Assistant (e.g., Spotify, Fire TV Cubes, Chromecast, Volumio, Echo, etc.).
- **Spotify Integration**: A Spotify media player entity (e.g., via the Spotify integration) with source selection support.
- **Fire TV Integration**: Fire TV devices with app state attributes (e.g., `app_id` for detecting the home screen).

## Required Integrations
The following integrations are necessary to fully replicate this setup:

### Built-in Home Assistant Integrations
- **Spotify**: Provides the Spotify media player entity (e.g., `media_player.spotify_user`) with source selection for switching between devices like Chromecast or Fire TV. Set up via Settings > Devices & Services > Add Integration > Spotify.
- **Android Debug Bridge (ADB)**: Enables control and state monitoring of Fire TV Cube devices (e.g., `media_player.living_room_cube`), providing `app_id` attributes to detect the home screen (`com.amazon.tv.launcher`) or Spotify app (`com.spotify.music`). Set up via Settings > Devices & Services > Add Integration > Android Debug Bridge. Requires enabling ADB debugging on Fire TV devices (Settings > My Fire TV > About > Developer Options > ADB Debugging) and installing the ADB server in Home Assistant.
- **Google Cast** (optional): Supports Chromecast media players (e.g., `media_player.art_frame`) for Spotify playback. Automatically detected if Chromecast devices are on your network.
- **Input Select**: Enables the `input_select` entities for the media player dropdowns. Included in Home Assistant by default.
- **Script**: Powers the dashboard refresh and Spotify source handling scripts. Included in Home Assistant by default.
- **Automation**: Supports the automations for syncing selectors, handling Spotify sources, prioritizing media players, and switching Fire TV sources. Included in Home Assistant by default.

### HACS Integrations (Custom Components)
Install these via HACS or manually:
- [**mini-media-player**](https://github.com/kalkih/mini-media-player): A Lovelace card for displaying media player controls with customizable artwork, progress bars, and source selection.
- [**state-switch**](https://github.com/thomasloven/lovelace-state-switch): A Lovelace component that dynamically switches between media player cards based on the state of `input_select.media_player_selector_internal`.
- [**browser_mod**](https://github.com/thomasloven/hass-browser_mod): Enables browser-specific actions, such as refreshing the dashboard UI for specific browser instances.

### Optional Integrations
Depending on your media players, you may need:
- **Volumio**: For Volumio-based media players (e.g., `media_player.living_room_volumio`). Available via Settings > Devices & Services > Add Integration > Volumio.
- **Alexa Media Player**: For Amazon Echo devices (e.g., `media_player.kitchen_echo`) with media playback support. Install via HACS or manually from [github.com/alandtse/alexa_media_player](https://github.com/alandtse/alexa_media_player).
- **Onkyo**: For Onkyo receivers (e.g., `media_player.bedroom_receiver`). Available via Settings > Devices & Services > Add Integration > Onkyo.
- **Google Home**: For Google Home/Nest devices (e.g., `media_player.game_room_speaker`). Available via Settings > Devices & Services > Add Integration > Google Home.
- **Other Media Player Integrations**: Any integration providing a `media_player` entity compatible with Spotify source selection (e.g., Sonos, Roku).

## Installation

Follow these steps to recreate the media player selector dashboard in your Home Assistant instance.

### Step 1: Install Custom Components and Integrations
1. **Install HACS Components**:
   - In Home Assistant, go to HACS > Frontend (for `mini-media-player`, `state-switch`) and Integrations (for `browser_mod`).
   - Search for and install `mini-media-player`, `state-switch`, and `browser_mod`.
   - Restart Home Assistant after installation.
2. **Manual Installation** (if not using HACS):
   - Download the component files from their GitHub repositories.
   - Place them in your `custom_components/` directory (e.g., `custom_components/mini_media_player/`).
   - Restart Home Assistant.
3. **Set Up Built-in Integrations**:
   - **Spotify**: Go to Settings > Devices & Services > Add Integration > Spotify. Authenticate with your Spotify account.
   - **Android Debug Bridge (ADB)**:
     - Enable ADB debugging on each Fire TV Cube: On the device, go to Settings > My Fire TV > About > Developer Options > ADB Debugging > Enable.
     - Install the ADB server in Home Assistant:
       - If using Home Assistant OS, install the ADB add-on via Settings > Add-ons > Add-on Store > Android Debug Bridge.
       - For other setups, install ADB on your server (e.g., `sudo apt-get install android-tools-adb` on Linux) and ensure it’s running.
     - Add the ADB integration: Settings > Devices & Services > Add Integration > Android Debug Bridge. Enter the IP addresses of your Fire TV devices (e.g., `192.168.1.100:5555`).
   - **Google Cast** (if using Chromecast): Ensure Chromecast devices are on your network; Home Assistant auto-detects them.
   - **Other Integrations** (if needed): Add Volumio, Alexa Media Player, Onkyo, Google Home, or other integrations as required for your media players.
4. **Verify Installation**:
   - Check Settings > Devices & Services to confirm all integrations are loaded.
   - Confirm `browser_mod` is active by checking for `browser_mod` entities in Developer Tools > States.
   - Verify Fire TV devices report `app_id` attributes (e.g., `com.amazon.tv.launcher`) in Developer Tools > States.
   - Ensure the Spotify media player exposes a `source` attribute listing your devices (e.g., "Living Room Chromecast").

### Step 2: Configure Media Players
1. **List Your Media Players**:
   - Identify your media player entities in Home Assistant (e.g., `media_player.living_room_chromecast`, `media_player.bedroom_speaker`).
   - Ensure your Spotify integration is set up with a media player entity (e.g., `media_player.spotify_user`) and supports source selection.
   - Verify Fire TV devices report `app_id` attributes (e.g., `com.amazon.tv.launcher` for the home screen).
2. **Check Spotify Sources**:
   - In Developer Tools > States, inspect your Spotify media player’s `source` attribute (e.g., `state_attr('media_player.spotify_user', 'source')`).
   - Note the source names for your devices (e.g., "Living Room Chromecast", "Bedroom Fire TV").
3. **Custom Artwork** (Optional):
   - If your media players (e.g., Fire TV, Echo) support custom artwork, ensure the `custom_artwork_url` attribute is available or fall back to `entity_picture`.

### Step 3: Add Input Selects
1. **Copy `input_select.yaml`**:
   - Add the contents of `input_select.yaml` to your Home Assistant configuration:
     - If using a single `configuration.yaml`, add under the `input_select:` section.
     - If using packages, create a new package file (e.g., `packages/media_player_selector.yaml`) and include the content.
   - Example:
     ```yaml
     input_select:
       media_player_selector:
         name: Media Player Selector
         options:
           - Living Room Volumio
           - Kitchen Echo
           # ... other options
         initial: Living Room Volumio
       media_player_selector_internal:
         name: Internal Media Player Selector
         options:
           - media_player.living_room_volumio
           - media_player.kitchen_echo
           # ... other options
         initial: media_player.living_room_volumio
     ```
2. **Customize Input Selects**:
   - Replace the entity IDs in `media_player_selector_internal` with your media player entities (e.g., `media_player.living_room_chromecast` instead of `media_player.art_frame`).
   - Update the friendly names in `media_player_selector` to match your preferences (e.g., "Main Room Speaker" instead of "Living Room Volumio").
   - Ensure the friendly names and entity IDs correspond (e.g., "Kitchen Echo" maps to `media_player.kitchen_echo`).
3. **Reload Configuration**:
   - Go to Settings > Devices & Services > YAML Configuration > Reload Input Selects.
   - Alternatively, restart Home Assistant.
4. **Verify**:
   - Check Developer Tools > States for `input_select.media_player_selector` and `input_select.media_player_selector_internal`.
   - Ensure the options match your media players and the initial states are set.

### Step 4: Add Scripts
1. **Copy `scripts.yaml`**:
   - Add the contents of `scripts.yaml` to your configuration:
     - In `configuration.yaml`, add under the `script:` section.
     - Or use a separate file (e.g., `scripts.yaml`) and include it via `script: !include scripts.yaml`.
   - Example:
     ```yaml
     script:
       refresh_dashboard:
         alias: Refresh Dashboard
         sequence:
           - action: browser_mod.refresh
             data:
               browser_id:
                 - browser_id_1
                 - browser_id_2
                 - browser_id_3
         mode: single
         description: Refreshes specific browser instances for dashboard updates
       # ... set_media_player_based_on_spotify_source
     ```
2. **Customize Scripts**:
   - **Refresh Dashboard**:
     - Find your browser IDs in Developer Tools > States under `browser_mod` entities (e.g., `browser_mod.1234567890`).
     - Replace `browser_id_1`, `browser_id_2`, `browser_id_3` with your browser IDs, or remove the `browser_id` list to refresh all browsers:
       ```yaml
       data: {}
       ```
   - **Set Media Player Based on Spotify Source**:
     - Update the `source_to_entity` dictionary with your Spotify source names and corresponding entity IDs (e.g., replace `'Audio Rack Cube': 'media_player.audio_rack_cube'` with `'Living Room Fire TV': 'media_player.living_room_fire_tv'`).
     - Adjust the condition `'Cube' in source` if your Spotify sources use a different keyword (e.g., `'Fire TV' in source`).
3. **Reload Scripts**:
   - Go to Settings > Devices & Services > YAML Configuration > Reload Scripts.
   - Or restart Home Assistant.
4. **Verify**:
   - Test `script.refresh_dashboard` in Developer Tools > Services to ensure the dashboard refreshes.
   - Test `script.set_media_player_based_on_spotify_source` to confirm it sets `input_select.media_player_selector_internal` based on Spotify’s source.

### Step 5: Add Automations
1. **Copy `automations.yaml`**:
   - Add the contents of `automations.yaml` to your `automations.yaml` file or via the UI (Settings > Automations > Create Automation > Edit in YAML).
   - Example:
     ```yaml
     - alias: Friendly Name Sync
       description: Updates friendly name dropdown based on internal selector
       triggers:
         - trigger: state
           entity_id: input_select.media_player_selector_internal
       # ... rest of automation
     ```
2. **Customize Automations**:
   - **Friendly Name Sync & Reverse Friendly Name Sync**:
     - Update the `entity_to_friendly` and `friendly_to_entity` dictionaries to match your entity IDs and friendly names from `input_select.yaml`.
   - **Switch Spotify Source to This Phone on Cube Home Screen**:
     - Replace the Fire TV Cube entity IDs (e.g., `media_player.living_room_cube`) with your Fire TV entities.
     - Update `source: This Phone` to your desired Spotify source (e.g., "Mobile Device").
     - Ensure your Fire TV integration provides `app_id` attributes.
   - **Media Player Selector Spotify**:
     - Update the `source_to_entity` dictionary with your Spotify source names and corresponding entity IDs (e.g., replace `'Art Frame Chromecast': 'media_player.art_frame'` with `'Living Room Chromecast': 'media_player.living_room_chromecast'`).
   - **Media Player Selector Priority**:
     - Replace the entity IDs in the `triggers` list with your media players.
     - Update `media_player.kitchen_echo` to the entity ID of the media player you want to prioritize (or remove the priority logic if not needed).
3. **Reload Automations**:
   - Go to Settings > Automations > Reload Automations.
   - Or restart Home Assistant.
4. **Verify**:
   - Test each automation:
     - Change `input_select.media_player_selector_internal` and check if `input_select.media_player_selector` updates.
     - Select a friendly name in `input_select.media_player_selector` and verify `input_select.media_player_selector_internal` updates.
     - Play Spotify on a mapped source and confirm the correct media player is selected.
     - Play the prioritized media player (e.g., Kitchen Echo) and ensure it takes precedence.
     - Stop playback on a Fire TV Cube and verify Spotify’s source switches.

### Step 6: Add Dashboard
1. **Copy `dashboard.yaml`**:
   - In Home Assistant, go to Settings > Dashboards > Edit Dashboard > Edit in YAML mode.
   - Paste the contents of `dashboard.yaml`.
   - Alternatively, add to a Lovelace YAML file (e.g., `ui-lovelace.yaml` if using YAML mode).
2. **Customize Dashboard**:
   - Replace all entity IDs in the `state-switch` states with your media player entities (e.g., `media_player.art_frame` to `media_player.living_room_chromecast`).
   - Update the `default` card to use your default media player (e.g., `media_player.living_room_speaker`).
   - For Spotify, adjust the `mini-media-player` settings (e.g., `artwork`, `source`) to match your preferences.
   - For Fire TV and Echo devices, verify the `custom_artwork_url` attribute exists or remove the `picture` template if not needed:
     ```yaml
     picture: "{{ state_attr('media_player.kitchen_echo', 'entity_picture') }}"
     ```
3. **Save and Reload**:
   - Save the dashboard and reload the Lovelace UI (Settings > Dashboards > Reload).
   - Or restart Home Assistant.
4. **Verify**:
   - Open the dashboard and select a media player from the dropdown.
   - Ensure the correct `mini-media-player` card appears via `state-switch`.
   - Test Spotify playback and verify the Spotify player displays when appropriate.

### Step 7: Test the Setup
1. **Spotify Playback**:
   - Play Spotify on a mapped source (e.g., Chromecast, Fire TV Cube).
   - Verify `input_select.media_player_selector_internal` updates to the corresponding entity (e.g., `media_player.art_frame` for "Art Frame Chromecast").
   - Check that the dashboard shows the correct media player card.
2. **Priority Media Player**:
   - Play media on the prioritized player (e.g., Kitchen Echo).
   - Ensure `input_select.media_player_selector_internal` selects it over other playing devices.
3. **Fire TV Cube Source Switching**:
   - Stop playback on a Fire TV Cube and return to the home screen.
   - Confirm Spotify’s source switches to "This Phone".
4. **Dropdown and Refresh**:
   - Select a friendly name in the dropdown and verify the dashboard updates.
   - Tap the dropdown to trigger `script.refresh_dashboard` and ensure the UI refreshes.

## Customization
- **Media Players**:
  - Add or remove media players in `input_select.yaml`, `automations.yaml`, `scripts.yaml`, and `dashboard.yaml`.
  - Update friendly names to suit your setup (e.g., "Bedroom Speaker" instead of "Bedroom Receiver").
- **Spotify Sources**:
  - Check your Spotify media player’s `source` attribute in Developer Tools > States.
  - Update the `source_to_entity` dictionaries in `Media Player Selector Spotify` and `set_media_player_based_on_spotify_source` to match your source names and entity IDs.
- **Fire TV Cubes**:
  - Replace Fire TV Cube entity IDs in `Switch Spotify Source to This Phone on Cube Home Screen` and `set_media_player_based_on_spotify_source`.
  - Adjust the source-switching logic (e.g., change "This Phone" to another source).
- **Priority**:
  - Modify `Media Player Selector Priority` to prioritize a different media player or remove the priority logic.
- **Browser Refresh**:
  - Update `browser_id` values in `refresh_dashboard` or remove them to refresh all browsers.
- **Artwork**:
  - Customize the `picture` template for devices with specific artwork attributes.
- **Spotify Settings**:
  - Adjust the Spotify `mini-media-player` settings in `dashboard.yaml` (e.g., `artwork: cover`, `show_progress: false`).

## Troubleshooting
- **Spotify Not Switching**:
  - Verify Spotify source names in `Media Player Selector Spotify` and `set_media_player_based_on_spotify_source` match `state_attr('media_player.spotify_user', 'source')`.
  - Check Developer Tools > States for the Spotify entity’s source attribute.
- **Priority Not Working**:
  - Ensure the prioritized media player (e.g., `media_player.kitchen_echo`) is correctly set in `Media Player Selector Priority`.
  - Test with multiple media players playing to confirm prioritization.
- **Source Not Switching**:
  - Confirm the target source (e.g., "This Phone") is valid for your Spotify media player.
  - Verify Fire TV Cube entities report `app_id` attributes via the ADB integration and match the automation’s conditions.
- **Dashboard Not Refreshing**:
  - Ensure `browser_mod` is installed and active.
  - Check `browser_id` values or remove them to refresh all browsers.
  - Test `script.refresh_dashboard` in Developer Tools > Services.
- **Fire TV Not Detected**:
  - Confirm ADB debugging is enabled on Fire TV devices and the ADB server is running.
  - Check Developer Tools > States for Fire TV entities and verify `app_id` attributes (e.g., `com.amazon.tv.launcher`).
- **Dropdown Mismatch**:
  - Ensure `input_select` options match the mappings in `Friendly Name Sync` and `Reverse Friendly Name Sync`.
  - Check Developer Tools > States for `input_select.media_player_selector` and `input_select.media_player_selector_internal`.
- **Custom Components**:
  - Confirm `mini-media-player`, `state-switch`, and `browser_mod` are installed and loaded (check Settings > Devices & Services).
- **Debugging**:
  - Add a debug card to `state-switch` in `dashboard.yaml`:
    ```yaml
    default:
      type: entities
      entities:
        - entity: sensor.dummy
          name: "Unknown state: {{ states('input_select.media_player_selector_internal') }}"
    ```
  - Use Developer Tools > Events to monitor state changes for `input_select` and media player entities.
  - Enable debug logging for integrations in `configuration.yaml`:
    ```yaml
    logger:
      default: info
      logs:
        custom_components.mini_media_player: debug
        custom_components.state_switch: debug
        custom_components.browser_mod: debug
        homeassistant.components.adb: debug
    ```

## Notes
- The `state-switch` uses `input_select.media_player_selector_internal` (entity IDs) to match the Spotify, priority, and source-based automations/scripts, while the UI dropdown uses `input_select.media_player_selector` (friendly names).
- The `Friendly Name Sync` and `Reverse Friendly Name Sync` automations ensure two-way sync between the selectors.
- The `Media Player Selector Spotify` automation maps Spotify sources to media players (e.g., "Art Frame Chromecast" to `media_player.art_frame`).
- The `set_media_player_based_on_spotify_source` script maps Spotify’s Fire TV Cube sources to media players (e.g., "Audio Rack Cube" to `media_player.audio_rack_cube`).
- The `Media Player Selector Priority` automation prioritizes the Kitchen Echo when it’s playing.
- The `Switch Spotify Source to This Phone on Cube Home Screen` automation switches Spotify’s source when Fire TV Cubes stop playing.
- The `refresh_dashboard` script refreshes the dashboard UI for a seamless experience.

## License
MIT License. Feel free to use, modify, and share this configuration. See the `LICENSE` file for details.

## Credits
Based on Home Assistant community components (`mini-media-player`, `state-switch`, `browser_mod`) and contributions. Developed with inspiration from advanced media control setups.
