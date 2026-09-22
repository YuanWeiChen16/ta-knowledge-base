# Python Pipeline 自動化

**穩定性標籤**：`[STABLE]` 概念，`[ENGINE-VERSIONED]` API

---

## 為什麼 TA 需要 Python

一個好的 pipeline 腳本可以把美術師重複做 8 小時的工作縮短到 30 秒。  
**這是 Senior TA 和 Mid TA 最明顯的差距之一。**

---

## DCC 腳本環境

### Maya Python (maya.cmds / PyMEL / OpenMaya API)
```python
import maya.cmds as cmds

# 批次重命名場景中所有 mesh
meshes = cmds.ls(type='mesh')
for i, mesh in enumerate(meshes):
    cmds.rename(mesh, f"SM_{i:03d}")

# 批次匯出選取物件為 FBX
cmds.select("MySword")
cmds.file("E:/Export/MySword.fbx", 
          force=True, 
          type="FBX export",
          exportSelected=True)

# 查詢 polygon 數
poly_count = cmds.polyEvaluate("MySword", face=True)
print(f"Face count: {poly_count}")
```

### Blender Python (bpy)
```python
import bpy

# 批次設定所有 mesh 的 smoothing
for obj in bpy.data.objects:
    if obj.type == 'MESH':
        bpy.ops.object.shade_smooth()
        obj.data.use_auto_smooth = True
        obj.data.auto_smooth_angle = 0.523599  # 30 degrees

# 批次匯出 FBX
bpy.ops.export_scene.fbx(
    filepath="E:/Export/scene.fbx",
    use_selection=True,
    mesh_smooth_type='FACE'
)
```

### Houdini Python (hou)
```python
import hou

# 遍歷所有節點找特定類型
for node in hou.node("/obj").children():
    if node.type().name() == "geo":
        print(f"Found geo: {node.name()}")

# 設定參數
node = hou.node("/obj/myGeo")
node.parm("tx").set(10.0)

# 執行 cook
node.cook(force=True)
```

---

## 常用 Pipeline 腳本情境

### 批次貼圖處理（Pillow/OpenCV）
```python
from PIL import Image
import os

def batch_convert_to_power_of_two(input_dir, output_dir):
    """將所有貼圖調整為最近的 2 次方尺寸"""
    for filename in os.listdir(input_dir):
        if filename.endswith(('.png', '.jpg', '.tga')):
            img = Image.open(os.path.join(input_dir, filename))
            w, h = img.size
            # 找最近的 2 次方
            new_w = 2 ** round(math.log2(w))
            new_h = 2 ** round(math.log2(h))
            if new_w != w or new_h != h:
                img = img.resize((new_w, new_h), Image.LANCZOS)
                print(f"Resized {filename}: {w}×{h} → {new_w}×{new_h}")
            img.save(os.path.join(output_dir, filename))
```

### 資產命名規範檢查
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
        print(f"WARNING: '{name}' 不符合 {asset_type} 命名規範")
        return False
    return True
```

---

## Unity Editor 腳本（C#）

```csharp
// Editor 工具：批次設定貼圖 Import Settings
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

## 學習資源

- 📖 [Maya Python Documentation](https://help.autodesk.com/view/MAYAUL/2024/ENU/?guid=__CommandsPython_index_html)
- 📖 [Blender Python API](https://docs.blender.org/api/current/)
- 📖 [Houdini Python API](https://www.sidefx.com/docs/houdini/hom/index.html)
- 🎥 [Tech Art Aid — Pipeline Scripting](https://www.youtube.com/@TechArtAid)
- 📖 [Python for Maya Artists](https://www.packtpub.com/product/python-scripting-for-maya-artists) — 遊戲 TA 推薦

---

## 實作練習 (project-ideas)

1. **命名規範檢查工具**：寫一個 Unity Editor 視窗工具，掃描 Project 裡所有資產，標記不符合命名規範的資產並提供一鍵修復。需要有 UI 列出問題清單。
2. **貼圖 Import 自動化**：寫一個 Unity AssetPostprocessor，當貼圖匯入時自動根據檔名後綴（_N, _R, _M, _D）設定正確的 Import Settings（sRGB、壓縮格式）。
