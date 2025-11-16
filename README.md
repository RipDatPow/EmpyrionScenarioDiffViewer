# Quick Start Guide -- Empyrion Scenario Diff Viewer

**This app requires .NET 9.0 Core runtimes.**

This guide will help you get the application running in minutes and
start comparing scenarios across multiple file formats, including
`.ecf`, `.epb`, and `.yaml`.

This tool is useful for scenario developers and multiplayer server
admins who have built or customized their own scenario and want a quick,
convenient way to compare it against other scenarios like *Invader vs
Defender* or *Reforged Eden*---without manually diffing individual files
with tools like Beyond Compare.

I originally built this tool back in the Alpha 8--10 days in an earlier
.NET version when I was getting started with scenario development and
wanted an easy way to compare my scenario's configurations with others
to quickly identify differences and missing configurations/entries. I've
recently rebuilt it in a modern .NET 9.0 Core WPF app to share with the
community.

This runs on Windows. You can probably run it on Linux if you know how
:) This app doesn't write to any files---it only reads files for
comparison. No database needed. Everything operates entirely in-memory.

I've got some enhancements in mind that I may add in the future for
aggregate metrics that would be useful for scenario developers. We'll
see if I get around to them eventually---I'm mostly busy messing around
with Unity these days.

## 🚀 Running the Application

### Run the Compiled Executable

``` bash
.\EmpyrionScenarioDiffViewer.exe
```

## 📊 Understanding the Diff Display

The diff viewer uses color coding:

  Symbol   Color       Meaning
  -------- ----------- --------------------------------
  `+`      🟢 Green    Line added in right version
  `-`      🔴 Red      Line removed from left version
  `~`      🟠 Orange   Line modified between versions
  (none)   ⚪ Black    Line unchanged

## 🎯 Using with Empyrion Scenarios

### Finding Empyrion Scenarios

Typical Empyrion scenario locations:

-   **Steam**:
    `C:\Program Files (x86)\Steam\steamapps\common\Empyrion - Galactic Survival\Content\Scenarios\`
-   **Custom Scenarios**: Usually in the game's `Scenarios` folder

### Example: Comparing Content/Configuration

1.  **Select Content/Configuration**: Use the Mode dropdown (default
    selection)
2.  **Default Game Scenario**:
    -   Open Left:
        `...\Empyrion\Content\Scenarios\Invader vs Defender - Conflict of Cygnus\`\
    -   Files discovered: `Content/Configuration/*.ecf`
3.  **Reforged Eden 2**:
    -   Open Right: `...\Empyrion\Content\Scenarios\Reforged Eden 2\`\
    -   Files discovered: `Content/Configuration/*.ecf`
4.  **Select Files to Compare**:
    -   Common files include: `BlocksConfig.ecf`, `ItemsConfig.ecf`,
        `TraderNPCConfig.ecf`
    -   Files/entries are automatically compared on both sides when you
        click on one side
5.  **Browse Entries**:
    -   Select matching entries by Id or Name\
    -   Review differences in properties, values, and structure\
    -   Pink-highlighted entries exist only on one side

### Example: Comparing Extras/PDA

1.  **Select Extras/PDA** in the Mode dropdown\
2.  **Select Scenario Roots** as above
    -   Files discovered: `Extras/PDA/*.yaml` and `*.csv`
3.  **Compare PDA Files**:
    -   View mission definitions and data files\
    -   See which PDA files exist only in one scenario

### Example: Comparing Playfields

1.  **Select Playfields** from the Mode dropdown\
2.  **Select Scenario Roots**
    -   Folders discovered: `Playfields/*` subfolders
3.  **Select a Playfield Folder** (e.g., `EdenMoonNascent`)
    -   Both sides automatically sync
4.  **Compare YAML Files**:
    -   `playfield.yaml`: Main configuration\
    -   `playfield_dynamic.yaml`: Dynamic spawning rules\
    -   `playfield_static.yaml`: Static decorations\
    -   Pink-highlighted files exist only on one side

### Example: Comparing Prefabs

1.  **Select Prefabs** from the dropdown\
2.  **Select Scenario Roots**
    -   Files discovered: `Prefabs/*.epb` and `Templates/*.epb`
3.  **Compare Blueprint Metadata**:
    -   View dimensions, block counts, device groups\
    -   Compare spawn names and group assignments\
    -   See which blueprints exist only on one side

### Example: Comparing Sectors

1.  **Select Sectors**\
2.  **Select Scenario Roots**
    -   File discovered: `Sectors/Sectors.yaml`
3.  **Compare Full YAML Content**:
    -   Review solar system definitions\
    -   Compare orbit parameters and playfield assignments

## 💡 Tips

### Efficient Comparison Workflow

1.  **Choose the right mode** for what you want to compare\
2.  **Load both scenarios** (select root scenario folders)\
3.  **Let the app sync selections**: clicking on one side auto-selects
    the other\
4.  **Look for pink highlights**: these indicate unique files/entries\
5.  **Compare entries systematically**:
    -   Start from the top of the entry list\
    -   Pink items exist only on one side\
    -   Regular items exist on both sides

### Mode Selection Guide (Alphabetically Sorted)

-   **Content/Configuration**: Compare game mechanics (blocks, items,
    containers, traders)
-   **Extras/PDA**: Compare PDA missions and data files
-   **Playfields**: Compare planet/moon configurations
-   **Prefabs**: Compare blueprints and prefabs
-   **Sectors**: Compare the solar system structure

### Finding Specific Changes

-   **File/Folder List** (Top): Shows discovered files or folders
    -   🟥 Pink = exists only on one side
-   **Entry List** (Bottom): Shows parsed entries
    -   🟥 Pink = exists only on one side
-   **Display Formats**:
    -   Content/Configuration: `Type Id: X, Name: Y`\
    -   Extras/PDA: Filename\
    -   Playfields: YAML filenames\
    -   Prefabs: `Blueprint: filename`\
    -   Sectors: `Sectors`

### Copy Results

-   Click **"📋 Copy Diff"** to copy the entire diff to clipboard\
-   Paste into a text editor, documentation, or a bug report

## 🔍 Troubleshooting

### No Files Appear

-   **Check**: Ensure you selected the **scenario root folder**, not a
    subfolder\
-   **Content/Configuration**: Reads `Content/Configuration/*.ecf`\
-   **Extras/PDA**: Reads `Extras/PDA/*.yaml` and `*.csv`\
-   **Playfields**: Reads `Playfields/*` subfolders\
-   **Prefabs**: Reads `Prefabs/*.epb` and `Templates/*.epb`\
-   **Sectors**: Looks for `Sectors/Sectors.yaml`\
-   **Common Issue**: Selecting a wrong subfolder

### Entries Don't Show

-   **Check**: You must select a file/folder from the top list first\
-   **File may be empty or improperly formatted**\
-   **Playfields**: Select a playfield folder first, then YAML entries
    appear

### Diff Doesn't Update

-   **Check**: You must select entries from **both** panels\
-   Expected behavior: diff updates automatically when both selections
    change\
-   File selection auto-syncs when both sides contain the same file

## 🛠️ Development

### Adding Features

The codebase is structured for extensibility:

-   **New comparison modes**: Add to `FileComparisonMode` enum (keep
    alphabetical), create a new service class\
-   **New parsers**: Create a new service in the `Services/` folder
    (follow the pattern in `PdaService.cs`)\
-   **New diff formats**: Extend `DiffService.cs`\
-   **UI changes**: Modify `MainWindow.xaml`\
-   **New models**: Add to `Models/`\
-   **New behaviors**: Add to `Behaviors/`

## 📞 Support

For issues, suggestions, or contributions:\
- Check the main `README.md` for detailed documentation

## 🎉 Next Steps

Now that you're up and running:

1.  ✅ Test with two scenarios\
2.  ✅ Try all five comparison modes\
3.  ✅ Compare your own Empyrion scenario\
4.  ✅ Use pink highlighting to find unique files/entries\
5.  ✅ Take advantage of auto-sync and auto-scroll features\
6.  ✅ Copy and share diff results\
7.  🔜 Watch for future updates with search/filter and export options

Happy scenario comparing! 🚀

Shout-out to all the scenario developers, multiplayer server admins,
Eleon, and the wonderful players and workshop content creators in our
uniquely great galactic survivalist community.

Special thanks to the Transcendence server community (no longer online)
and Airelon, who helped me out a ton when I was getting started with
scenario development.
