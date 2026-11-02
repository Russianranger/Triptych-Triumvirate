# Upstream sync — November 2, 2026

Integrated all nine new upstream commits through
`saltamontes5k/Triptych-Triumvirate` main at
`5f4cb116a42bb8dcd4c0fe1214845f6e0affc3fc` into Russianranger main at
`38a4f2ad03203cb2f12dbff26fd613cc47efba0a`.

The merge preserves both histories. The pre-sync source is retained on
`backup/pre-upstream-sync-2026-11-02` at the original Russianranger main SHA.

## Integration and retained changes

- Import the complete upstream November 1 content/server/client refresh, including
  quests, plugins, housing/fellowship code, release binaries, sanitized database
  snapshot, migrations, and updated documentation/ignore rules.
- Retain the four existing source adaptations exactly: disabled client-version
  warnings/forced Bazaar transfer, disabled UCS Discord queue calls and listener
  thread, and the Android Lua include path.
- Resolve the only three-way conflict in `zone/CMakeLists.txt` by keeping both the
  fork's `${LUA_INCLUDE_DIR}` and upstream's guarded `${LUA_LIBRARY_INCLUDE}`.
  Keep all seven new housing/fellowship source entries.
- Preserve all fork-specific Android/LAN, database, client export, Perl dependency,
  and Nektulos setup instructions. Update the README's sync revision.
- Retain the previous sync record below.

## Verification and scope

- All 16 fetched source/document snapshots matched their Git blob SHA-1 hashes.
- For each of the four customized source files, the byte-level delta from new
  upstream to merged source is identical to the old base-to-fork customization.
  Every other upstream byte in those files is retained.
- All 479 listed source/header references in the merged zone CMake file exist in
  the complete upstream server tree, including the seven new source entries.
- All original README customization blocks and the new upstream November release
  notes remain present. No conflict markers remain in the merged files.
- The merge tree uses the complete upstream tree and overrides only the four
  customized source files, README.md, and this document. All other upstream
  files, including binary assets and database migrations, are retained exactly.

A full server/client rebuild and physical-device testing were not performed in
this environment; CMake and a C++/ARM64 toolchain are unavailable. The included
upstream Windows binaries do not contain this fork's source adaptations. Rebuild
and deploy the affected server binaries in the existing Android proot setup.
This source synchronization does not import a database, replace a running
installation, or modify characters/saves. Do not replace an existing player
database with the fresh-install sanitized snapshot.

---

# Upstream sync — October 17, 2026

Merged upstream `saltamontes5k/Triptych-Triumvirate` main at
`9e62980552d6ef484a974df87e8342a673319c73` into Russianranger main at
`8f6ca0795f424a7b4eab750ff38fc6473d48375c`.

The merge imports 11 upstream commits, including the October 4, 9, and 16 content,
client add-on, database, maps, and server changes. No database import or runtime
replacement is performed by the source merge.

## Retained Android and LAN adaptations

- Keep `target_include_directories(lua_zone PRIVATE ${LUA_INCLUDE_DIR})` for the
  configured Lua 5.1 include path in the Android ARM64 proot build.
- Keep client-version warnings and forced Bazaar relocation disabled while
  retaining `Custom:ServerAuthStats` for the other multiclass updates.
- Keep UCS Discord player-event/webhook queueing and its processing thread disabled.
- Keep the Android build, database setup, client export, Perl dependency, and
  Nektulos troubleshooting instructions.

The only content conflict was `zone/CMakeLists.txt`: upstream changed line endings
and added achievement/vault source entries. The resolution uses the complete
upstream file plus the existing Lua include line, keeping every new source entry.

## Client memory requirement

Upstream's October 16 note requires the 4 GB patch for the expanded 1,440-spell
spellbook: <https://ntcore.com/4gb-patch/>. The client is 32-bit RoF2. The relevant
PE executable setting is `IMAGE_FILE_LARGE_ADDRESS_AWARE` (`0x20`) in the COFF
Characteristics field. Retain an original executable backup before changing it.
The flag permits a larger address space when the runtime supports it; it does not
allocate 4 GB of physical memory on launch.

## Verification

- The merged tree matches upstream exactly outside the four source customization
  files and this fork's documentation.
- Byte-level comparisons confirmed the UCS/thread, client enforcement, and Lua
  changes preserve every other upstream byte in those source files.
- All 473 source/header references in the zone CMake source lists exist in the
  merged index, including the new upstream entries.
- The Android/setup instructions and all October release notes remain present.

A full ARM64 server rebuild and physical AYN Thor qualification were not performed
in this environment: CMake, Ninja, and the ARM64 toolchain are unavailable. The
prebuilt upstream Windows binaries do not contain this fork's source adaptations.
Rebuild and deploy the server binaries in the existing Android proot environment;
pulling source alone does not replace a running server.

