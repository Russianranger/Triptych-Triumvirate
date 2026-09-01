# Triptych Triumvirate — LAN server release

A complete, working **multiclass EverQuest server** (EQEmu-based, RoF2 client), released as a
LAN-oriented community server. Everything in this repo is tuned for play on a local network.

> **LAN only.** This release is intended for private/local-network play, of course it can be tweaked for network at discretion.

---

## This update's changes (8/30)

- **spell_effects** fixes (chadw)
- **Leap** movement fix (chadw)
- **#illusion** storage (new `character_illusions` table) (chadw)
- **Target restriction** 99 → 95 (chadw)
- **XTarget** fixes (partial, chadw's EQEmu repo)
- **Offline bazaar** (valorith/nekkola, chadw's EQEmu repo)
- **Mount glamour merchant** and **languages**
- **Tome trainers** moved to bazaar backrooms
- **Bestial alignment AA racial fix** (idunknown)
- **Godmode rune bug** fix, particularly on pets (Doraj & Kree)
- **2H damage / #attack** fix and the **permanent glowing hand** bug (hawk & animal)
- Item **discoverability on summon** (not entirely working yet)

## What is in here

| Folder | What it is |
| --- | --- |
| [`Release-NMS-Server/`](Release-NMS-Server/) | The server (EQEmu-based), pre-built binaries, and the database dump |
| [`Release-NMS-Client/`](Release-NMS-Client/) | `dinput8.dll` client add-on + the modified UI files |
| [`Release-NMS-Quests/`](Release-NMS-Quests/) | Quest scripts (Perl / Lua) |
| [`Release-NMS-Plugins/`](Release-NMS-Plugins/) | Perl plugins the quests depend on |

Each folder has its own README with detailed instructions. Start with the server.

## What makes it different

- **Multiclassing** — a character can take up to three classes at once
- **Multiple pets** — pet classes control several pets, with a custom pet window
- **Echo of Memory** — an alternate currency that drops from kills and buys unlocks
- **Item upgrade tiers** — drops can roll as Enchanted or Legendary versions
- **Offline bazaar** — offline trader/buyer/barter support
- **Glamour & languages** — mount glamour merchant, armour glamour, and a languages trainer
- **Tome trainers** — located in the bazaar backrooms
- Assorted client-side quality-of-life fixes, shipped as `dinput8.dll`

---

## Quick start (LAN)

**1. Get a client.** EverQuest client files are Daybreak's — not included and cannot be. You
will need the RoF2-era client this server was built against. See
[the client README](Release-NMS-Client/README.md) for what to do with it.

**2. Get the map files.** The `maps/` folder is **not** included in this repository (too large
for GitHub). Sourcing map packs is a one-time download:
- `.map` files live under `maps/base/` and `maps/legacy/base/`
- `.nav` files live under `maps/nav/` and `maps/legacy/nav/`
- `.zon` water mesh files live under `maps/water/`

Any current EQEmu map pack for the zones in this server works. Drop them into `maps/` in the
server folder.

**3. Set up the database.** Unzip `Release-NMS-Server/database/release-peq.zip` and import it
into an empty schema. It contains **no player data** — it is a fresh world.

**4. Run the server.** Pre-built Windows binaries are in `Release-NMS-Server/bin/Release/`
(`world.exe`, `zone.exe`, `ucs.exe`, `queryserv.exe`, `loginserver.exe`, `eqlaunch.exe` and
`shared_memory.exe`, plus their DLLs and opcode/patch configs). Copy
`eqemu_config.json.example` and `login.json.example` to their real names and set your DB
credentials, then start `world.exe` (or use the included `start-servers.bat`).

Prefer to build from source instead? See **Building** below.

**5. Install quests and plugins.** Copy `Release-NMS-Quests/` into your server's `quests/` folder
and `Release-NMS-Plugins/` into `quests/plugins/`. If you do not have a quests folder, create one in the root directory of the server. For plugins, that should be created in the quests folder, as noted above.

**6. Copy lua_modules folder.** Copy lua_modules folder from 'quests/' directory into your root server folder. Ensure it exists in both your server/ and your server/quests/ folder.

**7. Client data files.** Fresh `spells_us.txt`, `dbstr_us.txt`, `SkillCaps.txt` and
`BaseData.txt` are already exported in `Release-NMS-Server/export/` — copy them into your client's root directory
(and its `Resources\` folder). If you change the DB, re-run `export_client_files` to regenerate
them. To run the 'export_client_files' command, you will need to create a folder called 'export' in your root server directory (server/export).

**8. Install the client add-on.** Copy `Release-NMS-Client/ClientFiles/` over your client.
See [the client README](Release-NMS-Client/README.md) — it also covers the known art gaps.

**9. Configure your eqemu_config.json and login.json files.** Ensure that these files are configured to the correct database and IP address. 
eqemu_config.json has TWO locations for your database information - ensure both reflect the correct database name and credentials. login.json only has one location for database.
Ensure both are setup with the correct IP address for connecting to the server. If hosting and connecting locally on the same machine, it will be 127.0.0.1. If on a LAN network, ensure that it is pointing to the computer IP that is hosting the server. Do not change the IP addresses for database/other server information, as it should all be on the same machine.

---

## Building

The build produces the usual EQEmu binaries: `world`, `zone`, `ucs`, `queryserv`,
`loginserver` and `eqlaunch`.

Verified to compile clean with **MSVC 2022**, **clang 14**, and **GCC 12**.

### Windows

You need **Visual Studio 2022** with the *Desktop development with C++* workload. That
workload includes CMake, so there is usually nothing else to install.

Run:

```
build_server.bat
```

It generates `Build\EQEmu.sln` for your machine — then open that solution, pick
**Release / x64**, and Build. Binaries land in `Build\bin\Release\`.

Prefer to do it by hand?

```
cmake -S . -B Build -G "Visual Studio 17 2022" -A x64 -DEQEMU_BUILD_LOGIN=ON
cmake --build Build --config Release
```

The **first** configure needs an internet connection: CMake downloads the prebuilt Windows
dependencies into `vcpkg\` on its own.

### Linux

Install the dependencies (Debian/Ubuntu):

```
sudo apt install build-essential cmake ninja-build git \
     libmysqlclient-dev libperl-dev libboost-dev liblua5.1-0-dev \
     zlib1g-dev uuid-dev libssl-dev
```

# Triune / NMS Post-Install Fixes

This document contains post-install fixes and modifications required for the Triune / NMS server.

---

## 1. Disable/Enable Discord Webhook Integration

By default - this is already disabled. For those that do not want it disabled, just delete the "//" in the specified code lines. The NMS server includes Discord webhook functionality in UCS. If no valid Discord webhook is configured, UCS may repeatedly produce errors such as:

    UCS | Error | SendWebhookMessage [Discord Client] Code [404]
    Error [{"message": "Unknown Webhook", "code": 10015}]

If Discord integration is not desired, it can be disabled in the NMS source.

> NOTE: Do not globally remove references to "Discord." EverQuest contains legitimate content using the name, including Priest of Discord, Gates of Discord, Discordant Energy, etc. The changes below specifically target the DiscordManager webhook integration.

### File 1: Release-NMS-Server/ucs/worldserver.cpp

Around line 94, locate:

    DiscordManager::Instance()->QueuePlayerEventMessage(n);

Comment it out:

    // DiscordManager::Instance()->QueuePlayerEventMessage(n);

Around line 101, locate:

    DiscordManager::Instance()->QueueWebhookMessage(
        q->webhook_id,
        q->message
    );

Comment out the entire call:

    // DiscordManager::Instance()->QueueWebhookMessage(
    //     q->webhook_id,
    //     q->message
    // );

This prevents UCS from adding player events and explicit webhook messages to the Discord message queue.

### File 2: Release-NMS-Server/ucs/ucs.cpp

At line 174, locate:

    std::thread(PlayerEventQueueListener).detach();

Comment it out:

    // std::thread(PlayerEventQueueListener).detach();

The PlayerEventQueueListener function itself begins around line 93 and contains:

    void PlayerEventQueueListener() {
        while (caught_loop == 0) {
            DiscordManager::Instance()->ProcessMessageQueue();
            Sleep(100);
        }
    }

The function does not need to be deleted or modified. Commenting out the thread creation at line 174 prevents the Discord message-processing loop from starting.

### Rebuild UCS

After making the changes:

    cd ~/NMS-Release/Release-NMS-Server
    cmake --build build --target ucs -j2

Restart the Triune server afterward.

---

## 2. Fix Perl DBD::mysql Door/Quest Error

Triune quest scripts use the Perl DBD::mysql module. If it is missing, clicking doors or triggering certain quest events may produce an error similar to:

    quest_global_player Event EVENT_CLICKDOOR
    install_driver(mysql) failed: Can't locate DBD/mysql.pm
    Perhaps the DBD::mysql perl module hasn't been fully installed
    at ./quests/plugins/MySQL.pl line 49

Install the required package:

    apt update
    apt install -y libdbd-mysql-perl

Optionally verify that Perl can load the module:

    perl -MDBD::mysql -e 'print "DBD::mysql loaded OK\n"'

Expected output:

    DBD::mysql loaded OK

No server source modification is required for this error. Installing `libdbd-mysql-perl` resolves it.

---

## 3. Fix Nektulos Forest NPC Spawning / Pathing

### Problem

In The Nektulos Forest, NPCs may exhibit severely incorrect movement and spawning behavior.

Observed symptoms include:

- NPCs appearing to fall from the sky when the zone loads.
- NPCs traveling through the air while following their path grids.
- NPCs moving underneath or through the terrain.
- NPCs appearing at seemingly incorrect elevations.
- NPC movement along established paths appearing erratic or "janky."
- The problem may affect a significant portion of the NPC population while other NPCs appear normal.

The issue appears to result from a mismatch between the Nektulos geometry/navigation files being used by the server and the Nektulos spawn/pathing data contained in the Triune database.

### Existing Nektulos Map Files

The Triune installation contains both current and legacy Nektulos map files.

The active files are located at:

    ~/triune-server/maps/base/nektulos.map
    ~/triune-server/maps/nav/nektulos.nav

Legacy versions are located at:

    ~/triune-server/maps/legacy/base/nektulos.map
    ~/triune-server/maps/legacy/nav/nektulos.nav


The Triune database contains a large amount of Nektulos spawn and path-grid data whose coordinates appear to correspond to the legacy Nektulos geometry.

### Back Up the Existing Nektulos Maps

Before replacing anything, create a backup of the currently active files.

    cd ~/triune-server

    mkdir -p maps/nektulos-backup

    cp -a maps/base/nektulos.map maps/nektulos-backup/
    cp -a maps/nav/nektulos.nav maps/nektulos-backup/

Verify that the backup exists:

    ls -lh maps/nektulos-backup/

The directory should contain:

    nektulos.map
    nektulos.nav


### Replace Active Nektulos Maps With Legacy Versions

Copy the legacy Nektulos files over the active versions:

    cd ~/triune-server

    cp -f maps/legacy/base/nektulos.map maps/base/nektulos.map
    cp -f maps/legacy/nav/nektulos.nav maps/nav/nektulos.nav
    cp -f maps/legacy/water/nektulos.wtr maps/water/nektulos.wtr

Verify the active files:

    ls -lh \
    maps/base/nektulos.map \
    maps/nav/nektulos.nav \


The legacy files should be approximately:

    nektulos.map    100 KB
    nektulos.nav    1.2 MB


For comparison, the previously active files observed during troubleshooting were approximately:

    nektulos.map    2.0 MB
    nektulos.nav    968 KB


The large difference in the `.map` file is an important indication that the two sets contain substantially different Nektulos geometry.

### Restart the Server (If Running)

Perform a complete Triune server restart after replacing the files.

Do not simply leave Nektulos and zone back into it.

The Nektulos zone process needs to be restarted so that the replacement `.map`, `.nav`, and `.wtr` files are loaded from disk.

After restarting, enter Nektulos and check:

1. NPCs should spawn at the appropriate terrain elevation instead of falling from above.
2. Roaming/pathing NPCs should remain approximately on the ground.
3. NPCs following path grids should follow the terrain rather than moving through the air or underground.
4. General NPC movement should appear substantially more natural.

If the legacy files need to be reverted, restore the backup:

    cd ~/triune-server

    cp -f maps/nektulos-backup/nektulos.map maps/base/nektulos.map
    cp -f maps/nektulos-backup/nektulos.nav maps/nav/nektulos.nav

Perform another complete server restart afterward.

---

## Post-Install Fix Summary

The following fixes have been identified during installation and testing of Triune / NMS:

### Discord / UCS

Problem:

    UCS | Error | SendWebhookMessage [Discord Client] Code [404]

Fix:

- Disable the two DiscordManager queue calls in `ucs/worldserver.cpp`.
- Disable creation of the `PlayerEventQueueListener` thread in `ucs/ucs.cpp`.
- Rebuild the UCS target.

### Perl / Quest Scripts

Problem:

    install_driver(mysql) failed: Can't locate DBD/mysql.pm

Fix:

    apt install -y libdbd-mysql-perl

### Nektulos Forest

Problem:

- NPCs falling from the sky.
- NPCs traveling through the air.
- NPCs traveling through terrain.
- Erratic path-grid movement.

Fix:

Replace:

    maps/base/nektulos.map
    maps/nav/nektulos.nav

with the corresponding files from:

    maps/legacy/

and completely restart the server.

---

## Important Notes

- Always back up files before replacing or modifying them.
- The Discord modifications require UCS to be rebuilt.
- The Perl fix does not require a source-code modification.
- The Nektulos fix does not require recompiling the server.
- The Nektulos database spawn coordinates should not be mass-modified to compensate for incorrect map geometry.
- A complete server/zone restart should be performed after changing map or navigation files.


---

## Future Plans
- More modernization to match more current versions of EQEMU
- Various changes that catch eye
- Deity quest gated weapon and spell procs
---

## Requirements

- **Server:** CMake 3.12+ (4.x works), a C++17 compiler, MariaDB 10.6+ or MySQL 8.0, Perl
- **Client add-on:** Visual Studio 2022 with the *Desktop development with C++* workload
  (build as **Win32/x86** — the client is 32-bit)

## Licensing

The server is derived from the [EQEmu project](https://github.com/EQEmu/Server) and carries its
GPL licensing. The client add-on and the quest scripts are MIT, with their original copyright
notices intact — see the `LICENSE` file in each folder.

EverQuest is a registered trademark of Daybreak Game Company. This project is not affiliated with
or endorsed by Daybreak, and contains no EverQuest client files.
