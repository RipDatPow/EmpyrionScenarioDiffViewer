# Empyrion Scenario Diff Viewer

A Windows desktop application built with **.NET 9 WPF** for comparing Empyrion scenario files across multiple formats  including .ecf, .epb, and .yaml.

This tool is useful for scenario developers and multiplayer server admins who have built or customized their own scenario and want a quick and convenient way to compare it against other scenarios like Invader v Defender or Reforged Eden without having to slowly and manually diff individual files with tools like Beyond Compare.

## Features

### 🎯 Core Functionality

- **Dual Scenario Comparison**: Load and compare two Empyrion scenario directories side-by-side
- **8 Comparison Modes** (alphabetically sorted):
  - **Content/Configuration** - ECF configuration files (BlocksConfig, ItemsConfig, etc.) and dialogue CSV files (Dialogues.csv, etc.)
  - **Extras/PDA** - PDA YAML and CSV files
  - **Extras/Localization** - Localization.csv string translations
  - **Playfields** - Playfield folders with YAML files
  - **Prefabs** - EPB blueprint files with metadata comparison
  - **RandomPresets** - RandomPresets folder YAML files
  - **Sectors** - Sectors.yaml file comparison
  - **SharedData/Content** - Shared asset folders and files (manifests, configs, etc.)
- **Advanced Parsing**: 
  - ECF: Nested brace structures `{ ... }`, single-line (`#`) and multi-line (`/* */`) comments
  - EPB: Binary blueprint parsing with JSON-formatted metadata
  - YAML: Full content parsing for Sectors and Playfields
  - CSV: Per-KEY row parsing for Localization.csv
- **Smart File Discovery**: Automatically finds files using standard Empyrion directory structure
- **Entry-Level Comparison**: View and compare individual entries within files
- **Unified Diff View**: Color-coded difference display with:
  - 🟢 **Green**: Added lines (+ prefix)
  - 🔴 **Red**: Removed lines (- prefix)
  - 🟠 **Orange**: Modified lines (~ prefix)
  - ⚪ **Black**: Unchanged lines

### 🪟 User Interface

![Screenshot](EmpyrionScenarioDiffViewer.png)

- **3-Panel Layout**:
  - **Left Panel**: First scenario (files/folders and entries)
  - **Center Panel**: Diff viewer with syntax highlighting
  - **Right Panel**: Second scenario (files/folders and entries)
- **Mode Selector**: Dropdown to easily switch between all 5 comparison modes
- **Smart Highlighting**: 
  - 🟥 **Pink Background**: Files/entries that exist only on one side
  - ⚪ **Normal**: Items that exist on both sides
- **Synchronized Selection**: Selecting a file on one side automatically selects the matching file on the other
- **Auto-Scrolling**: Lists automatically scroll to keep selected items visible
- **Modern UI**: Powered by ModernWpfUI for a clean, contemporary look
- **Resizable Panels**: Adjust panel sizes with draggable splitters
- **Copy to Clipboard**: Export diff results with one click

## Architecture

The application follows the **MVVM (Model-View-ViewModel)** pattern:

```
/Models          - Data structures (EcfEntry, EcfFile, FileComparisonMode)
                 - EPB models (BluePrint, DeviceGroup, BlockList, etc.)
/Services        - Business logic
                 - EcfParser: ECF file parsing
                 - EpbFileService: EPB file discovery and parsing
                 - SectorsService: Sectors.yaml handling
                 - SectorsYamlParser: Sectors.yaml per-sector entry extraction
                 - LocalizationService: Localization.csv handling
                 - LocalizationCsvParser: Localization.csv per-KEY entry extraction
                 - CsvKeyRowParser: Shared KEY-based CSV row parsing
                 - PdaService: PDA YAML/CSV handling
                 - PdaYamlParser: PDA YAML per-chapter entry extraction
                 - RandomPresetsService: RandomPresets YAML handling
                 - RandomPresetsYamlParser: Auto-detected per-entry extraction
                 - SharedDataContentService: SharedData/Content folder discovery
                 - PlayfieldsService: Playfield folder discovery
                 - DiffService: Text comparison engine
/ViewModels      - Presentation logic (MainViewModel, ScenarioViewModel)
/Controls        - Custom UI controls (DiffTextViewer)
/Converters      - Value converters for data binding
                 - DiffLineToColorConverter: Diff syntax coloring
                 - ExistenceToBackgroundConverter: Highlight unique items
                 - StringToVisibilityConverter: Conditional visibility
/Behaviors       - Attached behaviors (ScrollIntoViewBehavior)
/Views           - XAML views (MainWindow)
```

## Getting Started

### Prerequisites

- Windows 10 or later
- .NET 9.0 SDK or Runtime

### Building from Source

```bash
git clone <repository-url>
cd EmpyrionEcfTools
dotnet restore
dotnet build
```

### Running the Application

```bash
dotnet run
```

Or open `EmpyrionScenarioDiffViewer.sln` in Visual Studio 2022.

## Usage

### Basic Workflow

1. **Select Mode**: Use the dropdown to select a comparison mode (alphabetically sorted)
2. **Open Left Scenario**: Click "📁 Open Left" and select your first scenario **root folder**
3. **Open Right Scenario**: Click "📁 Open Right" and select your second scenario **root folder**
4. **Select Files/Folders**: Choose an item from either the left or right list (both sides will sync)
5. **View Entries**: Browse the parsed entries for each file/folder
6. **Compare**: Select entries from both sides to view their differences in the center panel
7. **Export**: Use "📋 Copy Diff" to copy the diff results to your clipboard

### Mode-Specific Usage

#### Content/Configuration
- **Discovers**: `Content/Configuration/*.ecf` and `*.csv` files
- **Compares**: Block configurations, items, NPCs, loot tables, dialogue strings, etc.
- **Entries**: Individual ECF blocks (e.g., `Block Id: 1, Name: Stone`) or CSV KEY rows (e.g., `Dialogue_CSV, Id: dialogue_RMBiGd8, Name: Mission Briefing`)

#### Extras/PDA
- **Discovers**: `Extras/PDA/*.yaml` and `*.csv` files
- **Compares**: PDA mission chapters and string table rows
- **Entries**: One entry per YAML chapter (ChapterTitle) or CSV KEY row
  - YAML: `PDA_Chapter, Id: pda_8mWwuoC, Name: SoloMission`
  - CSV: `PDA_CSV, Id: pda_Gm4qW8C, Name: Return to Arcadia Station`

#### Extras/Localization
- **Discovers**: `Extras/Localization.csv` file
- **Compares**: Individual localization strings across all language columns
- **Entries**: One entry per KEY row, matched by KEY (e.g. `Localization, Id: AlienBlocks, Name: Alien Hull Blocks`)

#### Playfields
- **Discovers**: `Playfields/*` subfolders
- **Compares**: Playfield YAML files within each folder
- **Entries**: Individual YAML files (playfield.yaml, playfield_dynamic.yaml, etc.)

#### Prefabs
- **Discovers**: `Prefabs/*.epb` and `Templates/*.epb` blueprint files
- **Compares**: Blueprint metadata (dimensions, block counts, device groups)
- **Entries**: JSON-formatted metadata from each blueprint

#### RandomPresets
- **Discovers**: `RandomPresets/*.yaml` files
- **Compares**: Solar system configs and random generation rules (auto-detected format per file)
- **Entries**: SolarSystems blocks by Name, SectorPlanets/SectorPOIs list items, or Sectors coordinates; whole file if unrecognized

#### Sectors
- **Discovers**: `Sectors/Sectors.yaml` file
- **Compares**: Individual galaxy sectors (Coordinates, Color, Icon, Playfields, etc.)
- **Entries**: One entry per sector under the `Sectors:` list, matched by galaxy coordinates

#### SharedData/Content
- **Discovers**: `SharedData/Content/**` folders recursively (including empty folders)
- **Compares**: Which folders and files exist on each side; text diffs for readable files
- **Entries**: One entry per file in the selected folder (e.g. `SharedDataFile, Id: Bundles/crosshairs.manifest, Name: crosshairs.manifest`)
- **SampleFiles**: `SampleFiles/SharedDataContent/` maps to the Content root for local testing

## ECF File Format

The parser handles Empyrion's ECF format:

```
{ Block Id: 1, Name: Stone
  Material: stone
  Shape: Terrain
  Mesh: terrain
  Texture: 175
  ShowUser: No
  Group: stone
  
  { Child DropOnDestroy
    Item: CrushedStone
    Count: "1,3"
    Prob: 0.18
  }
}
```

### Parser Features

- **Brace Matching**: Correctly handles nested `{ }` structures
- **Comment Removal**: Strips `#` single-line and `/* */` multi-line comments
- **Metadata Extraction**: Automatically identifies entry Type, Id, and Name
- **Robust**: Handles various ECF file formats from different scenarios

## Dependencies

- **[CommunityToolkit.Mvvm](https://github.com/CommunityToolkit/dotnet)** (8.4.0) - MVVM helpers
- **[DiffPlex](https://github.com/mmanela/diffplex)** (1.9.0) - Text diffing engine
- **[ModernWpfUI](https://github.com/Kinnara/ModernWpf)** (0.9.6) - Modern UI styling

## Implemented Features ✅

- ✅ **8 Comparison Modes**: Content/Configuration, Extras/PDA, Extras/Localization, Playfields, Prefabs, RandomPresets, Sectors, SharedData/Content
- ✅ **Mode Dropdown**: Alphabetically sorted for easy navigation
- ✅ **Smart Entry Matching**: Automatically sync file/entry selection across both sides
- ✅ **Existence Highlighting**: Visually identify files/entries unique to one scenario
- ✅ **Auto-Scrolling**: Keep selected items visible in lists
- ✅ **Relative Path Discovery**: Automatic file discovery using standard Empyrion structure
- ✅ **Copy to Clipboard**: Export diff results

## License

This project is provided as-is for use with Empyrion - Galactic Survival.

## Acknowledgments

- Built for the Empyrion Galactic Survival community
