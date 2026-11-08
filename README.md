# TechGrid

**Advanced Java, GUI & data persistence showcase mod for Minecraft 1.20.1 Forge**

---

## Overview

TechGrid is a technical machinery mod demonstrating advanced Java patterns: Forge capability system (`IEnergyStorage`, `IItemHandler`), custom GUI with `AbstractContainerMenu` + `AbstractContainerScreen`, `WorldSavedData`-style persistence via NBT, server-authoritative tick processing, and a BFS-based energy transfer network.

---

## Features

| Feature | Status |
|---|---|
| Energy Generator block (40 FE/tick, 10k buffer) | ✅ |
| Energy Storage block (100k FE buffer) | ✅ |
| Processor machine (iron ingot → 9 nuggets) | ✅ |
| Energy transfer (BFS neighbor scan) | ✅ |
| Custom machine GUI (progress arrow + energy bar) | ✅ |
| Inventory management (2 slots: input + output) | ✅ |
| Processing progress (100 ticks, stalls without energy) | ✅ |
| Persistent machine state (NBT on chunk save) | ✅ |
| Server-authoritative processing tick | ✅ |
| Forge IEnergyStorage capability on all blocks | ✅ |
| Forge IItemHandler capability on processor | ✅ |
| Shift-click quick-move support | ✅ |
| ContainerData sync (4 integers: progress, energy) | ✅ |

---

## Blocks

### Energy Generator
- Generates **40 FE per tick** (passive, no fuel)
- Internal buffer: **10,000 FE**
- Automatically pushes energy to adjacent blocks each tick
- Craft with: 4x Iron Block + 4x Redstone Block + 1x Diamond

### Energy Storage
- Passive buffer: **100,000 FE**
- Accepts up to **500 FE/tick** per face
- Provides up to **500 FE/tick** per face
- Acts as network relay

### Processor
- Right-click to open GUI
- Requires **20 FE/tick** to operate
- Process time: **100 ticks (5 seconds)**
- Recipe: **Iron Ingot → 9 Iron Nuggets**
- Progress drains slowly if energy supply is interrupted (not instant reset)

---

## Getting Started

1. Place an **Energy Generator** adjacent to an **Energy Storage**
2. Place a **Processor** adjacent to the Energy Storage
3. Right-click the Processor to open the GUI
4. Insert an Iron Ingot into the input slot (left)
5. Watch the progress arrow fill as the machine processes
6. Collect Iron Nuggets from the output slot (right)

---

## Installation

1. Install **Minecraft Forge 1.20.1-47.3.0**
2. Place `techgrid-1.0.0.jar` in your `mods/` folder
3. No additional dependencies required

---

## Building

```bash
./gradlew build
# Output: build/libs/techgrid-1.0.0.jar
```

---

## Architecture

```
com.navdeep.techgrid/
├── TechGrid.java                       # Mod entry, all registry DeferredRegisters
├── block/
│   ├── EnergyGeneratorBlock.java       # BaseEntityBlock, ticks via getTicker()
│   ├── EnergyStorageBlock.java         # Passive block
│   └── ProcessorBlock.java             # Opens GUI on use
├── blockentity/
│   ├── EnergyGeneratorBlockEntity.java # Generate + push energy each tick
│   ├── EnergyStorageBlockEntity.java   # Buffer with IEnergyStorage capability
│   └── ProcessorBlockEntity.java       # Process items + ContainerMenu provider
├── energy/
│   ├── TechEnergyStorage.java          # Full IEnergyStorage implementation
│   └── EnergyNetwork.java              # BFS energy distribution
├── menu/
│   └── ProcessorMenu.java              # AbstractContainerMenu, ContainerData sync
└── screen/
    └── ProcessorScreen.java            # AbstractContainerScreen, GuiGraphics
```

**Key design decisions:**
- `BlockEntityType.TYPE` uses a static field populated during registration to avoid circular initialization in DeferredRegister lambdas.
- `ContainerData` syncs 4 integers (progress, total ticks, energy, capacity) to the client each tick — no custom packets needed.
- `EnergyNetwork` uses BFS rather than a graph/cable system, trading network complexity for simplicity appropriate for a portfolio demonstration.
- All processing logic is in `serverTick()` — the client screen is purely read-only, preventing client-side state divergence.
- Progress drains (not resets) on energy starvation — better player experience and demonstrates nuanced state machine design.

---

## Known Limitations

- No crafting recipes defined (use `/give` or creative mode)
- GUI texture is programmatically drawn — no custom PNG asset (would need a texture artist)
- Energy network does not support cables/pipes (direct adjacency only)
- Only one recipe implemented (expandable via a recipe registry in a future version)
