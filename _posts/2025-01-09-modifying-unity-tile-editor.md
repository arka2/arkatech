---
layout: post
title: "Modifying Unity's 2D Tile Editor"
author: "Arka Tu"
categories: tools
tags: [tools]
image: tile-editor_03.png
---
Shards Between Us is an isometric game, and all the game assets are 2D. Our team has been using Unity's 2D Tile Editor package to manage and place our tiles. I started looking into how to modify the Tile Editor to be more useful for us and to explore Unity's tooling.

One of the first things I looked into was the Tile Editor's shortcuts. Because of how our tilemaps are set up, the default shortcuts aren't very helpful for us. Our tiles are placed on even Z-positions, and we have sloped tiles placed at the Z-positions in between. For example, one layer of tiles might be on Z-position 0, and the next layer up will be at 2. A sloped tile might be placed at 1, which would allow the player to move between positions 0 and 2. The default shortcuts included with the Tile Editor only change the Z-position by 1. I modified them to include shortcuts that change the Z-position by 2 using the minus and equals keys. The user can change it by 1 by holding down 'Control' and using the same keys.

```c#        
// Added to make moving between Z-axis by 2 easier
[Shortcut(ShortcutIds.k_IncreaseZBy2, typeof(TilemapEditorTool.ShortcutContext), KeyCode.Minus)]
static void IncreaseBrushZBy2()
{
    if (GridPaintingState.gridBrush != null
        && GridPaintingState.activeGrid != null
        && GridPaintingState.activeBrushEditor != null
        && GridPaintingState.activeBrushEditor.canChangeZPosition)
        ChangeBrushZ(2);
}
```

```c#        
// Added to make moving between Z-axis by 2 easier
[Shortcut(ShortcutIds.k_DecreaseZBy2, typeof(TilemapEditorTool.ShortcutContext), KeyCode.Equals)]
static void DecreaseBrushZBy2()
{
    if (GridPaintingState.gridBrush != null
        && GridPaintingState.activeGrid != null
        && GridPaintingState.activeBrushEditor != null
        && GridPaintingState.activeBrushEditor.canChangeZPosition)
        ChangeBrushZ(-2);
}
```

![Screenshot of Unity's Tile Editor, showing a tooltip describing the new shortcuts.](https://arkatu.com/arkatech/assets/img/tile-editor_01.png)

Another issue we sometimes have is when we create levels and then come back to them. Sometimes we'll have placed tiles and want to change them, but we might have placed them at weird Z-positions. This can make it difficult to erase them since the tools only work at a particular Z-position. I added debug logs to the Tile Picker Tool to output what Z-positions tiles are located at within a particular cell.

```c#
foreach (var pos in position.allPositionsWithin)
{
    var brushPosition = new Vector3Int(pos.x - position.x, pos.y - position.y, 0);

    // Added to check +10 and -10 Z positions below the selected cell
    for (int i = 0; i < 6; i++)
    {   
        // Get the next cell
        var nextCellUp = CalculateNextCell(pos, i);
        // Check if there's a tile there
        if (tilemap.HasTile(nextCellUp))
        {
            Debug.Log("Tile found at Z position " + nextCellUp.z);
        }

        // Other than the first pass, check if there's a tile below
        if (i != 0)
        {
            var nextCellDown = CalculateNextCell(pos, -i);
            if (tilemap.HasTile(nextCellDown))
            {
                Debug.Log("Tile found at Z position " + nextCellDown.z);
            }
        }
    }
    
    PickCell(pos, brushPosition, tilemap);
}
```

```c#
/// <summary>Calculate cell directly on top of or under a tile.</summary>
/// <param name="position">The coordinates of the current tile.</param>
/// <param name="Zindex">How much to increment new position.</param>
private Vector3Int CalculateNextCell(Vector3Int position, int Zindex)
{
    return new Vector3Int(position.x - Zindex, position.y - Zindex, Zindex * 2);
}
```

![Screenshot of the game's scene view and console, showing the Z-positions where tiles exist.](https://arkatu.com/arkatech/assets/img/tile-editor_02.png)

## Improvements
---
Currently, we place characters and interactable game objects using custom grid brushes. By default, these are accessed via a dropdown menu under the palette window, but our environment rule tiles are accessed in the palette window. This is a bit confusing, especially for team members that aren't as familiar with the Tile Editor. I'm currently working on adding tiles assets to the palette window to make it easier to paint characters and game objects.
