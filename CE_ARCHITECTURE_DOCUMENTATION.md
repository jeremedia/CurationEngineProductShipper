# Curation Engine Architecture Documentation
## For Coding Assistants & AI Code Generation

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
│   │   │   │   │   ├── structureLevelConfigs: List<OK_Structure_Level_Config>
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
- Collections are always plural with "Configs" suffix

#### Room Properties
```csharp
// Correct room property usage:
roomConfig.label = "Gallery A";                          // Display name
roomConfig.guidString = System.Guid.NewGuid().ToString(); // Unique ID
roomConfig.levelRoomRect = new Rect(0, 0, 10, 8);       // Room bounds in level space
roomConfig.roomHeight = 4.0f;                           // Height of room
roomConfig.structureLevelConfig = parentLevel;          // Parent reference
```

### Database Integration

The CE uses multiple specialized databases accessed through `OK_Client_Database_Manager`:

```csharp
// Static access to all databases:
OK_Client_Database_Manager.presetsDatabase      // All preset templates
OK_Client_Database_Manager.materialsDatabase    // Material definitions
OK_Client_Database_Manager.decorationsDatabase  // Decoration items
OK_Client_Database_Manager.toursDatabase        // Tour configurations
OK_Client_Database_Manager.exhibitionDatabase   // Exhibition templates
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

// Rooms inherit from structure level unless overridden
roomConfig.floorConfig.materialConfig = new OK_Material_Config("oak_floor");
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

### Async Initialization Pattern

All configs require async initialization with presets:

```csharp
// Creating a new config requires preset and async init:
var roomPreset = OK_Root_Preset.GetDefaultPresetFor<OK_Room_Preset>();
var roomConfig = new OK_Room_Config();
await roomConfig.Init(parentLevel, roomPreset);

// Configs cascade initialization to child configs:
var universeConfig = new OK_Universe_Config();
await universeConfig.Init(); // Initializes with default universe preset
```

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

### Frame Layouts and Artwork

Artwork is placed through frame layouts on wall segments:

```csharp
// Get or create wall segment for a room wall:
var wallSegment = roomConfig.roomWallSegmentConfigsByWallSide[OK_Room_Side.Top];

// Create frame layout:
var layoutConfig = new OK_Frame_Layout_Config();
await layoutConfig.Init();
layoutConfig.layout_type = OK_Frame_Layout_Type.Grid;
layoutConfig.columns = 3;
layoutConfig.rows = 1;

// Add mount points for artwork:
var mountPoint = new OK_Frame_Layout_Mount_Point_Config();
await mountPoint.Init();
mountPoint.position = new Vector3(0, 1.5f, 0);

// Create framed print:
var framedPrint = new OK_Framed_Print_Config();
await framedPrint.Init();
framedPrint.printConfig = new OK_Print_Config();
await framedPrint.printConfig.Init();
// Set image source...

mountPoint.framedPrintConfig = framedPrint;
layoutConfig.mountPointConfigs.Add(mountPoint);
wallSegment.frameLayoutConfigs.Add(layoutConfig);
```

### Serialization with Odin

CE uses Odin Serializer for saving/loading configurations:

```csharp
// Save universe to .okbin file:
await universeConfig.SaveAsync();

// Load universe from .okbin file:
var universe = await OK_Universe_Config.LoadByFilePath(filepath);
```

### Common Enums

```csharp
public enum OK_Room_Side
{
    Top,    // North wall
    Bottom, // South wall
    Left,   // West wall
    Right   // East wall
}

public enum OK_Frame_Layout_Type
{
    FreeForm,
    Grid,
    Salon,
    Linear
}

public enum OK_Units
{
    Metric,
    Imperial
}
```

### MCP Integration Corrections

When generating CE configs from MCP commands:

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

// CORRECT async patterns:
await config.Init();                               // ✓ All inits are async
await builder.Draw();                               // ✓ All draws are async
```

### Assembly Structure

MCP code should be in its own assembly definition:

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

### Testing Configuration Creation

```csharp
// Minimal viable exhibition:
var universe = new OK_Universe_Config();
await universe.Init();

var world = new OK_World_Config();
await world.Init();
universe.worldConfigs.Add(world);

var land = new OK_Land_Config();
await land.Init();
world.landConfigs.Add(land);

var parcel = new OK_Parcel_Config();
await parcel.Init();
land.parcelConfigs.Add(parcel);

var development = new OK_Development_Config();
await development.Init();
parcel.developmentConfigs.Add(development);

var structure = new OK_Structure_Config();
await structure.Init();
development.structureConfigs.Add(structure);

var level = new OK_Structure_Level_Config();
await level.Init();
structure.structureLevelConfigs.Add(level);

var roomPreset = OK_Root_Preset.GetDefaultPresetFor<OK_Room_Preset>();
var room = new OK_Room_Config();
await room.Init(level, roomPreset, new Rect(0,0,10,8), "Gallery");
level.roomConfigs.Add(room);

// Generate walls from room configurations:
await level.ResetToRoomConfigs();
```

This documentation ensures coding assistants generate correct CE code that compiles and functions properly within the Curation Engine architecture.