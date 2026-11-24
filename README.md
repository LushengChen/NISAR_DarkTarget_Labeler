# NISAR Dark Target Labeler (Portable Windows Edition)

> Copyright © 2026 Lusheng Chen. All rights reserved.
>
> This tool is maintained by Lusheng Chen. Parts of the code were written with assistance from OpenAI's ChatGPT.

## 1. System Requirements

- 64-bit Windows 10 or 11.
- Microsoft Edge, Google Chrome, or Mozilla Firefox.
- Python, VS Code, Jupyter, and WSL are optional.
- At least 8 GB of RAM is recommended. For large regions of interest (ROIs), 16 GB is recommended.

## 2. Getting Started

1. **Extract the entire ZIP archive** to a local folder before launching the tool.
2. Keep `NISAR_DarkTarget_Labeler.exe` and the `_internal` folder together in that folder.
3. Double-click `NISAR_DarkTarget_Labeler.exe`.
4. Select the GeoTIFF input folder and the sample output folder, then click **Start Labeling** (启动标注).
5. The labeling page opens in your default browser. Keep the launcher window open while you work.

If Windows Firewall prompts you for network access, you can dismiss the prompt or allow access for private networks only. The tool listens exclusively on `127.0.0.1`, so the page and data are accessible only from your computer.

## 3. Input Data

- Place `.tif` or `.tiff` files directly in the selected input folder. The tool scans only the top level.
- The tool supports single-band and multiband GeoTIFFs. Select a band, such as HH, HV, VV, or VH, before running the analysis.
- Area calculations and Shapefile export require geocoded imagery with a valid coordinate reference system (CRS) and affine transform.
- You can preview RSLC or other slant-range images referenced only by ground control points (GCPs). Geocode them before calculating areas in km² or exporting Shapefiles.

## 4. Labeling Workflow

1. Select a scene and an analysis band. Zoom with the mouse wheel or zoom buttons, and use **Pan View** (平移视图) to move around the image. As you pan or zoom, the tool recalculates the display stretch over the visible area using either 2.5 standard deviations (2.5σ) or percentile clipping.
2. If the input has strong speckle, enable **Lee Despeckling** (Lee 去斑) and select the filter window size and equivalent number of looks (ENL). The tool converts dB values to linear power, applies the Lee filter, and converts the result back to dB. This setting affects the preview, ROI segmentation, and YOLO training image. **Leave this option off for imagery that has already been Lee-filtered to avoid excessive smoothing.**
3. Draw a rectangular or polygonal ROI. In polygon mode, click each vertex in order, then click **Finish Polygon** (完成多边形). The tool analyzes only the pixels inside the polygon and crops the GeoTIFF to the polygon's bounding rectangle.
4. Click **Analyze ROI** (分析 ROI). Adjust the threshold offset, background window, minimum area, and grouping distance as needed.
5. Unclassified candidates appear in red. Click multiple polygons to select them, then assign a class (OS, BS, LW, or SI) or delete false detections in a batch.
6. Once you have reviewed all candidates, click **Save TIF + SHP + YOLO** (保存 TIF + SHP + YOLO).

YOLO class IDs

- `OS` (class 0) — oil spill
- `BS` (class 1) — biogenic slicks
- `LW` (class 2) — low-wind-speed area
- `SI` (class 3) — sea ice

## 5. Outputs

Each save produces the following files.

- A cropped GeoTIFF with the original pixel values.
- A single-band Float32 `_lee.tif` in dB when Lee despeckling is enabled.
- A classification mask GeoTIFF.
- A separate Shapefile for each class present.
- A YOLO training image and a `.txt` annotation file.
- A JSON file recording each bounding box and its pixel coordinates in the source image.

Saving multiple samples from the same scene automatically adds sequential suffixes such as `_0`, `_1`, and `_2` to keep each sample in its own set of files.

## 6. Time and Activity Statistics

The dashboard at the top of the labeling page shows the local time, a greeting, session and cumulative statistics, and an activity heatmap for the past 26 weeks. The tool stores cumulative statistics locally in the file below.

`%LOCALAPPDATA%\NISARDarkTargetLabeler\usage_stats.json`

The dashboard uses the following counting rules.

- **Scenes viewed.** Each scene counts once per session. The cumulative total counts unique file paths.
- **TIF.** Each successful save contributes one original cropped TIF to the count.
- **SHP.** Each class-specific Shapefile produced counts once.
- **YOLO.** Each exported bounding box counts once.
- **Usage time.** The tool tracks elapsed time while it runs and writes the totals to the local statistics file on normal exit.

## 7. Troubleshooting

- **The browser did not open automatically.** Click **Reopen Page** (重新打开页面) in the launcher.
- **Loading is slow.** Set **Preview Longest Side** (预览最长边) to 800 pixels and draw a smaller ROI.
- **The port is already in use.** The tool automatically switches to an available port.
- **The program does not start.** Check that you extracted the full archive and that the `_internal` folder is next to the EXE. The application log is at `%LOCALAPPDATA%\NISARDarkTargetLabeler\application.log`.
