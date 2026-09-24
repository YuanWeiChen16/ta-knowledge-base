# Python Pipeline Automation

**Stability Tag**: `[STABLE]` concepts, `[ENGINE-VERSIONED]` API

---

## Why TAs Need Python

A good pipeline script can compress 8 hours of repetitive artist work down to 30 seconds.  
**This is one of the most visible gaps between Senior TAs and Mid TAs.**

---

## DCC Scripting Environments

### Maya Python (maya.cmds / PyMEL / OpenMaya API)
```python
import maya.cmds as cmds

# Batch rename all meshes in the scene
meshes = cmds.ls(type='mesh')
for i, mesh in enumerate(meshes):
    cmds.rename(mesh, f"SM_{i:03d}")

# Batch export selected objects as FBX
cmds.select("MySword")
cmds.file("E:/Export/MySword.fbx", 
          force=True, 
          type="FBX export",
          exportSelected=True)

# Query polygon count
poly_count = cmds.polyEvaluate("MySword", face=True)
print(f"Face count: {poly_count}")
```

### Blender Python (bpy)
`polygon.use_smooth = True` shades every face smoothly; it does not reproduce the old 30° Auto Smooth edge split. For angle-based sharp edges, use the workflow for your target Blender version.

```python
import bpy

# Batch set smoothing for all meshes
for obj in bpy.data.objects:
    if obj.type == 'MESH':
        for polygon in obj.data.polygons:
            polygon.use_smooth = True

# Batch export FBX
bpy.ops.export_scene.fbx(
    filepath="E:/Export/scene.fbx",
    use_selection=True,
    mesh_smooth_type='FACE'
)
```

### Houdini Python (hou)
```python
import hou

# Traverse all nodes and find specific types
for node in hou.node("/obj").children():
    if node.type().name() == "geo":
        print(f"Found geo: {node.name()}")

# Set parameter
node = hou.node("/obj/myGeo")
node.parm("tx").set(10.0)

# Execute cook
node.cook(force=True)
```

---

## Common Pipeline Script Scenarios

### Batch Texture Processing (Pillow/OpenCV)
Only convert dimensions when required by the target platform or texture-streaming workflow; rounding width and height independently can change aspect ratio and resamples the image.

```python
from PIL import Image
import os
import math

def batch_convert_to_power_of_two(input_dir, output_dir):
    """Resize each texture dimension to the nearest power of two (may change aspect ratio)"""
    os.makedirs(output_dir, exist_ok=True)
    for filename in os.listdir(input_dir):
        if filename.endswith(('.png', '.jpg', '.tga')):
            img = Image.open(os.path.join(input_dir, filename))
            w, h = img.size
            # Find the nearest power of two
            new_w = 2 ** round(math.log2(w))
            new_h = 2 ** round(math.log2(h))
            if new_w != w or new_h != h:
                img = img.resize((new_w, new_h), Image.LANCZOS)
                print(f"Resized {filename}: {w}×{h} → {new_w}×{new_h}")
            img.save(os.path.join(output_dir, filename))
```

### Asset Naming Convention Check
```python
import re

NAMING_RULES = {
    'StaticMesh': r'^SM_[A-Z][a-zA-Z0-9_]+$',
    'Texture':    r'^T_[A-Z][a-zA-Z0-9_]+_(D|N|R|M|AO|E)$',
    'Material':   r'^M_[A-Z][a-zA-Z0-9_]+$',
}

def check_asset_name(name, asset_type):
    pattern = NAMING_RULES.get(asset_type)
    if pattern and not re.match(pattern, name):
        print(f"WARNING: '{name}' does not follow {asset_type} naming convention")
        return False
    return True
```

---

## Unity Editor Scripts (C#)

```csharp
// Editor tool: batch set texture Import Settings
using UnityEditor;
using UnityEngine;

public class TextureImportTool : EditorWindow {
    [MenuItem("Tools/TA/Batch Set Texture Settings")]
    static void BatchSetTextures() {
        string[] guids = AssetDatabase.FindAssets("t:Texture2D", new[] {"Assets/Textures"});
        foreach (string guid in guids) {
            string path = AssetDatabase.GUIDToAssetPath(guid);
            TextureImporter importer = AssetImporter.GetAtPath(path) as TextureImporter;
            
            // Normal maps
            if (path.Contains("_N.")) {
                importer.textureType = TextureImporterType.NormalMap;
                importer.sRGBTexture = false;
            }
            // Roughness/Metallic
            else if (path.Contains("_R.") || path.Contains("_M.")) {
                importer.textureType = TextureImporterType.Default;
                importer.sRGBTexture = false; // Linear!
            }
            
            importer.SaveAndReimport();
        }
        Debug.Log($"Processed {guids.Length} textures");
    }
}
```

---

## Learning Resources

- 📖 [Maya Python Documentation](https://help.autodesk.com/view/MAYAUL/2024/ENU/?guid=__CommandsPython_index_html)
- 📖 [Blender Python API](https://docs.blender.org/api/current/)
- 📖 [Houdini Python API](https://www.sidefx.com/docs/houdini/hom/index.html)
- 🎥 [Tech Art Aid — Pipeline Scripting](https://www.youtube.com/@TechArtAid)
- 📖 [Python for Maya Artists](https://www.packtpub.com/product/python-scripting-for-maya-artists) — recommended by game TAs

---

## Hands-On Exercises (project-ideas)

1. **Naming convention checker**: Write a Unity Editor window tool that scans all assets in the Project, flags assets that don't follow naming conventions, and provides a one-click fix. Must have a UI listing the issues.
2. **Texture import automation**: Write a Unity AssetPostprocessor that automatically sets the correct Import Settings (sRGB, compression format) based on filename suffix (_N, _R, _M, _D) when a texture is imported.
