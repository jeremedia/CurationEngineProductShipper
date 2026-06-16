# Curation Engine Architecture Documentation

> **Status audit (2026-06-12):** Historical/conceptual ship-tracker material.
> Current schedule and priorities live in GitHub issues for
> `jeremedia/CurationEngineProductShipper`; live tracker proof on 2026-06-12
> still showed open ship-blockers. This file is not current implementation
> authority.

## For Coding Assistants & AI Code Generation

> **Status (verified 2026-06-12): Historical assistant orientation.**
> Use root `AGENTS.md`, `CE_QUICK_REFERENCE.md`, `CE_ARCHITECTURE_TRUTH.md`,
> and direct `ce-core` source as the current authority. The code blocks below
> are historical sketches, not copy/paste API examples.
>
> Known snippet drift: current `OK_Client_Database_Manager` source exposes
> `presetDatabase`, `materialsDatabase`, `tourDatabase`, `decorDatabase`, and
> related singular database properties, not the older `presetsDatabase`,
> `decorationsDatabase`, `toursDatabase`, or `exhibitionDatabase` examples from
> earlier versions.
> Current frame-layout source uses `arrangeData`, `layoutMode`, and
> `mountPointConfigs`; older `layout_type`, `columns`, and `rows` examples are
> not current `OK_Frame_Layout_Config` fields. Current hierarchy/config
> creation uses parent- and preset-aware `Init(...)` paths; the parameterless
> setup snippets below are not safe current creation APIs.

### Core Architecture Pattern: Preset → Config → Builder

The Curation Engine follows a strict three-tier architecture:

1. **Presets** (`*_Preset.cs`): Template definitions with default values
2. **Configs** (`*_Config.cs`): Data instances that hold state (serializable with Odin)
3. **Builders** (`*_Builder.cs`): MonoBehaviours that create Unity GameObjects from configs

### Universe Hierarchy (Physical Structure)

The CE universe literally represents a hierarchical world structure:

```
OK_Universe_Config (singleton, accessible via OK_Universe_Config.Instance)
├── worldConfigs: List<OK_World_Config>
│   ├── landConfigs: List<OK_Land_Config>
│   │   ├── parcelConfigs: List<OK_Parcel_Config>
│   │   │   ├── developmentConfigs: List<OK_Development_Config>
│   │   │   │   ├── structureConfigs: List<OK_Structure_Config>
│   │   │   │   │   ├── levelConfigs: List<OK_Structure_Level_Config>
│   │   │   │   │   │   ├── roomConfigs: List<OK_Room_Config>
│   │   │   │   │   │   │   └── roomWallSegmentConfigsByWallSide: Dictionary<OK_Room_Side, OK_Room_Wall_Segment_Config>
│   │   │   │   │   │   │       └── frameLayoutConfigs: List<OK_Frame_Layout_Config>
│   │   │   │   │   │   │           └── mountPointConfigs: List<OK_Frame_Layout_Mount_Point_Config>
│   │   │   │   │   │   │               └── framedPrintConfig: OK_Framed_Print_Config
```

### Critical Property Names & Patterns

**IMPORTANT**: CE uses specific property naming conventions that differ from typical patterns:

#### Identity Properties
- Use `guidString` not `id` for unique identifiers
- Use `label` not `name` for display names (though some configs have both)

#### Collection Properties
- `worldConfigs` not `worlds`
- `landConfigs` not `lands`
- `roomConfigs` not `rooms`
- Child config collections commonly use plural `*Configs` names. Do not
  generalize this to every collection property; source also contains other
  collection shapes such as tags, studs, and geometry lists.

#### Room Properties
```csharp
// Correct room property usage:
roomConfig.label = "Gallery A";                          // Display name
roomConfig.guidString = System.Guid.NewGuid().ToString(); // Unique ID
roomConfig.levelRoomRect = new Rect(0, 0, 10, 8);       // Room bounds in level space
roomConfig.roomHeight = 4.0f;                           // Height of room
roomConfig.structureLevelConfig = parentLevel;          // Parent reference
```

### Historical Database Integration Sketch

The CE uses multiple specialized databases accessed through
`OK_Client_Database_Manager`. Current source exposes singular properties and no
`exhibitionDatabase` property.

```csharp
// Current source-backed static access examples:
OK_Client_Database_Manager.presetDatabase       // Preset templates
OK_Client_Database_Manager.materialsDatabase    // Material definitions
OK_Client_Database_Manager.decorDatabase        // Decoration/external asset items
OK_Client_Database_Manager.tourDatabase         // Tour configurations
```

### Material System

Materials are referenced by name and loaded from the database:

```csharp
// Materials are referenced by name, not direct Material references
public class OK_Material_Config : OK_Root_Config
{
    // Material name references item in database
    protected string _materialItemName = "defaultMat";

    // Database lookup happens automatically
    public string materialItemName
    {
        get => _materialItemName;
        set {
            _materialItem = OK_Root_Builder.materialsDatabase.GetMaterialItem(value);
        }
    }
}
```

#### Setting Materials on Rooms/Walls
```csharp
// Materials cascade from Structure → Level → Room
structureConfig.wallMaterialConfig = new OK_Material_Config("white_wall");
structureConfig.floorMaterialConfig = new OK_Material_Config("polished_concrete");
structureConfig.ceilingMaterialConfig = new OK_Material_Config("white_ceiling");

// Rooms inherit from structure level unless overridden.
// Current room source exposes roomFloorConfig and floorMaterialConfig.
roomConfig.floorMaterialConfig = new OK_Material_Config("oak_floor");
```

### Wall System Architecture

**CRITICAL**: Walls are shared between rooms at the Level, not owned by individual rooms:

1. **Rooms define volumes** - they request wall segments but don't own walls
2. **Levels generate walls** - analyzing all room requests and creating minimal wall segments
3. **Room Wall Segments** - reference portions of level walls with room-specific properties

```csharp
// Room requests walls but doesn't create them:
roomConfig.drawTopWall = true;    // Request north wall
roomConfig.drawBottomWall = true; // Request south wall
roomConfig.drawLeftWall = true;   // Request west wall
roomConfig.drawRightWall = true;  // Request east wall

// Level processes all room requests and generates shared walls
await structureLevelConfig.ResetToRoomConfigs(); // Generates walls from room requests
```

### Historical Async Initialization Pattern

Most config creation flows use async initialization with parent and preset
context, but exact overloads vary by class. Do not copy this section as current
creation code; verify the owning `ce-core` config, builder, factory, MCP tool,
or test before creating configs directly.

Current source example: `OK_Universe_Config` initialization requires person and
preset context through its public initializer; its parameterless initializer is
not a public creation API.

### Property Change Notification Pattern

All configs use reactive properties with change notifications:

```csharp
// Standard property pattern in configs:
[OdinSerialize, HideInInspector]
private float _width;

[ShowInInspector]
public float width
{
    get => _width;
    set => SetProperty(ref _width, value); // Triggers PropertyChanged events
}
```

### Historical Frame Layouts and Artwork Sketch

Artwork is placed through frame layouts on wall segments. Current
frame-layout source uses `arrangeData`, `layoutMode`, and `mountPointConfigs`;
`layout_type`, `columns`, and `rows` are not current
`OK_Frame_Layout_Config` fields.

```csharp
// Historical shape only. Recheck exact Init(...) overloads in ce-core before use.
var wallSegment = roomConfig.roomWallSegmentConfigsByWallSide[OK_Room_Side.Top];

var layoutConfig = new OK_Frame_Layout_Config();
// Current configuration surface:
layoutConfig.layoutMode = OK_Wall_Print_Mounter_Layout_Mode.Auto;
layoutConfig.arrangeData.x.cells = 3;
layoutConfig.arrangeData.y.cells = 1;

// Mounted artwork flows through mountPointConfigs after parent/preset-aware init.
```

### Serialization with Odin

CE uses Odin Serializer for saving/loading configurations:

```csharp
// Save universe to .okbin file:
await universeConfig.SaveAsync();

// Load universe from .okbin file:
var universe = await OK_Universe_Config.LoadByFilePath(filepath);
```

### Historical Enum Sketches

The enum sketches below are source-backed examples, not the full enum surface.
Current `OK_Frame_Layout_Type` is defined in
`ce-core/Runtime/GalleryLoader/OK_Enums.cs`.

```csharp
public enum OK_Room_Side
{
    Left,
    Right,
    Top,
    Bottom,
    All,
    None,
    Unknown
}

public enum OK_Frame_Layout_Type
{
    Grid,
    Row,
    Column,
    Custom
}

public enum OK_Units
{
    Metric,
    Imperial
}
```

### MCP Integration Corrections

Historical guidance when generating CE configs from MCP commands. Recheck these
snippets against current `ce-core` source before use:

```csharp
// CORRECT property usage:
universeConfig.guidString = exhibitionId;           // ✓ not .id
universeConfig.worldConfigs.Add(world);            // ✓ not .worlds
roomConfig.label = roomName;                       // ✓ not .name
roomConfig.levelRoomRect = new Rect(x,y,w,h);     // ✓ not .bounds

// CORRECT hierarchy navigation:
var structure = universe.worldConfigs[0]
    .landConfigs[0]
    .parcelConfigs[0]
    .developmentConfigs[0]
    .structureConfigs[0];

// Async patterns:
// Init visibility/signatures vary by class; verify the owning ce-core source.
// Builder draw paths are often async, but verify the concrete builder API.
```

### Assembly Structure

Historical assembly guidance for the old local MCP split. Current active MCP
source lives under `ce-core/Runtime/MCP`; recheck assembly boundaries before
copying this snippet:

```json
// CurationEngine.MCP.asmdef
{
    "name": "CurationEngine.MCP",
    "rootNamespace": "CurationEngine.MCP",
    "references": [
        "OK_ALL",                        // For CE configs
        "com.Tivadar.Best.HTTP",        // For HTTP
        "com.Tivadar.Best.WebSockets",  // For WebSockets
        "UniTask"                        // For async/await
    ]
}
```

### Historical Configuration Creation Sketch

This snippet is a historical hierarchy sketch, not a current creation API.
Current Land/Parcel/Development/Level creation uses parent- and preset-aware
initializers or factory/builder paths in `ce-core`.

Current creation proof should be taken from the owning builders, factories, MCP
tools, or tests in `ce-core`, not from this historical Product Shipper file.

This document is retained as historical orientation. It does not prove that the
snippets compile against current CE source.
