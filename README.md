# HXIPresence

HXIPresence is a source-only Ashita v4 addon prototype that publishes a
privacy-controlled Discord Rich Presence for HorizonXI. Version 0.4.1 keeps
both UI windows closed when loaded. The single, addon-specific
`/hxipresence` command opens or closes the compact control window; its Settings
button opens the full settings window.

Created by **DragoHorse**. Repository and support:
[github.com/ryuutatsuo23-max/HXIPresence](https://github.com/ryuutatsuo23-max/HXIPresence)

It is intentionally separate from FishAssist, HorizonScout, and Horizon Assist.
It reads the same local player/party fields already exposed to Ashita addons and
only writes activity data to Discord's local desktop IPC pipe. It does not use a
bot, webhook, Discord login, account token, external server, helper executable,
game input, packets, or game-memory writes.

## Important approval boundary

HorizonXI's rules currently state that an addon or third-party tool is not
allowed unless it appears on the official approved-addons page. This prototype
must not be installed or loaded against the live HorizonXI client unless the
HorizonXI team approves it.

Source review and offline testing are separate from approval and live use.

## Installation after approval

Do not load HXIPresence on HorizonXI unless the HorizonXI team has approved it.
For an approved installation:

1. Download `HXIPresence-v0.4.1.zip` from the repository's Releases page.
2. Extract its included `HXIPresence` folder into Ashita's `addons` folder.
3. Confirm the resulting path is `addons\HXIPresence\HXIPresence.lua`.
4. Start the game and run `/addon load HXIPresence`.
5. Run `/hxipresence` to open the compact control and configure the addon.

## Privacy-safe defaults

The addon starts disabled. The shared HorizonXI Discord application ID is
configured as `1543549056184360960`, and every optional detail is off:

- character name
- main job
- main-job level
- subjob
- subjob level
- zone
- Looking for Party status
- party size
- full session elapsed time

Character name is read and published only when its separate option is enabled.
It remains off by default like every other optional detail.

With all details off, Discord shows the configured application's game title,
which should be `HorizonXI`, plus the neutral `Adventuring` activity. Each
detail can be enabled independently.

## Settings UI

Neither window opens automatically when the addon loads. Presence continues to
run while the windows are closed. Run `/hxipresence` to open or close the
movable compact control, which shows connection status, provides an enable
switch, and has the only control that opens the full settings window. The full
window controls every optional detail and previews the current presence. The
enable switch exists only in the compact control.

Discord displays the application title automatically, followed by two main
activity lines. HXIPresence uses the first activity line for the optional
character name and job information, with the name first when both are enabled.
Zone, Looking for Party, and party status share the second activity line. When
party-size display is enabled and the player is in a party, Discord adds the
fill after that line, for example `In Party (3 of 6)`. A solo player does not
show party fill.

Discord can still show time since its most recent presence update even when
`Use full session elapsed time` is off. Enabling that setting instead preserves
one timer from the start of the current logged-in session.

Artwork is maintainer-controlled rather than user-selectable. The Discord
application icon is currently used automatically. If a separate approved Rich
Presence asset is added later, its key will be fixed in the addon source.

## Discord application setup

1. Create an application in the Discord Developer Portal.
2. Name the application `HorizonXI` so Discord displays `Playing HorizonXI`.
3. Use Application ID `1543549056184360960`.
4. Use the approved application icon. If separate Rich Presence art is approved
   later, configure its fixed asset key in the addon source rather than exposing
   it as a user setting.

The Application ID is public metadata, not a bot token. Do not create or place a
bot token in this addon.

## Command

After approval and installation, this one deliberately unique command toggles
the compact control window:

```text
/hxipresence
```

There are no short aliases or command subcommands, reducing the chance of a
conflict with FFXI commands or another addon. All settings remain in the UI.

Examples:

- all optional settings off: `Playing HorizonXI` with `Adventuring`
- character name, job, and levels on: `Yasukoi - BLM 29 / WHM 14`
- job and level on: `WAR 50`
- job, level, subjob, and sublevel on: `WAR 50 / NIN 25`
- zone on: the current zone appears on Discord's second activity line
- Looking for Party on while flagged: `Looking for Party` on the second line
- party size on while grouped: `In Party (3 of 6)` on the second line
- all three second-line details: `South Gustaberg | Looking for Party | In Party (3 of 6)`

Updates are change-driven and throttled to at most one publish every 15 seconds.
If Discord is closed, the addon retries the local connection every 15 seconds.
Disabling or unloading the addon clears the activity it published.

## Current validation boundary

Development builds were tested manually on a private/local server and were not
configured to autoload. The full version 0.2.1 presence checklist, version
0.3.0 renamed UI flow, version 0.3.1 simplified settings window, version 0.4.0
Looking for Party and party-size displays, and version 0.4.1 optional
character-name display were confirmed working.
Before HorizonXI approval, any further testing may only be performed on a
private/local server:

- Discord desktop IPC connection from the 32-bit HorizonXI/Ashita process
- continued compatibility with Discord's local RPC framing, which is not a
  separately documented public protocol surface
- the exact Rich Presence layout in the current Discord desktop client
- control and settings-window layout at supported game resolutions
- zone and job transitions in-game
- Looking for Party status changing off, on, and off
- party-size changes when members join or leave
- disconnect, logout, disable, and unload clearing behavior

## Private local-server test procedure

This procedure is only for a private/local server. Do not perform it while
connected to HorizonXI.

1. Start Discord desktop and confirm the intended local-server client is open.
2. In Ashita, run `/addon load HXIPresence` manually. Do not add it to an
   autoload or boot configuration.
3. Confirm neither UI window opens automatically and the saved presence state
   continues operating in the background.
4. Run `/hxipresence`; confirm only the compact control opens. Click its
   Settings button and confirm the full settings window opens.
5. Close both windows and confirm the Discord presence continues. Run
   `/hxipresence` twice and confirm it opens, then closes, the compact control.
6. Enable Discord presence from the compact control if it is disabled. Within
   15 seconds, confirm the status becomes `Discord acknowledged active` and
   Discord shows `Playing HorizonXI` with `Adventuring` and the application
   icon.
7. Enable one option at a time, allowing up to 15 seconds after each change:
   character name, main job, main-job level, subjob, subjob level, zone,
   Looking for Party, party size, and elapsed time.
8. Change zone and, if practical, job on the local server. Confirm Discord
   updates and that the character name appears only while its option is enabled.
9. Disable presence from the compact control and confirm the activity clears
   from Discord.
10. Re-enable it, then run `/addon unload HXIPresence`; confirm the activity
   clears again.
11. Before reconnecting to HorizonXI, verify HXIPresence remains unloaded.

## Support and feature requests

Use the repository's
[issue tracker](https://github.com/ryuutatsuo23-max/HXIPresence/issues) for bug
reports and feature requests. The same fixed repository link is available in
the addon's Settings window. HXIPresence does not submit requests, telemetry,
character data, or search-comment text from inside the game.

For a failure, record the control status, the last HXIPresence message in the
Ashita console, the option being tested, and what Discord displayed. Do not
share Discord secrets or unrelated game/account data.

Official references:

- [Discord Social SDK activity](https://discord.com/developers/docs/social-sdk/classdiscordpp_1_1Activity.html)
- [Discord `UpdateRichPresence`](https://discord.com/developers/docs/social-sdk/classdiscordpp_1_1Client.html#af0a85e30f2b3d8a0b502fd23744ee58e)
- [HorizonXI approved addons](https://horizonxi.com/addons)
- [HorizonXI rules](https://horizonxi.com/rules)
