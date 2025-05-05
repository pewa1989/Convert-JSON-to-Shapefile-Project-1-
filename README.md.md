
 Project Title: Convert New Orleans Parcel JSON to Shapefile

 Summary
This project demonstrates how to convert a JSON file containing land parcel data for New Orleans (in WKT format) into a shapefile using a Python Toolbox integrated with ArcGIS Pro. The final output is a shapefile visualized and exported as a professional layout map.

---

Dataset
- Source: [Market Value Analysis 2018 - data.nola.gov](https://data.nola.gov/)
- Format: JSON with `meta` and `data` sections
- Geometry Format: WKT (MULTIPOLYGON)

---

 Tools and Technologies
- Python 3 (within ArcGIS Pro environment)
- ArcGIS Pro + Python Toolbox (`.pyt`)
- Libraries used:
  - `arcpy`
  - `json`
  - `pandas`
  - `geopandas`
  - `shapely`

---

 Steps

### 1. Jupyter Notebook Testing
```python
import json
import pandas as pd
import geopandas as gpd
from shapely import wkt

with open("no_tax.json", "r") as f:
    data = json.load(f)

meta = data["meta"]
records = data["data"]
columns = [col["name"] for col in meta["view"]["columns"]]
df = pd.DataFrame([dict(zip(columns, rec)) for rec in records])
df["geometry"] = df["the_geom"].apply(wkt.loads)
gdf = gpd.GeoDataFrame(df, geometry="geometry", crs="EPSG:4326")
gdf.to_file("converted.shp")
```

### 2. Python Toolbox Development
```python
import arcpy
import json
import pandas as pd
import geopandas as gpd
from shapely import wkt

class Toolbox(object):
    def __init__(self):
        self.label = "JSON to Shapefile Toolbox"
        self.alias = "json2shp"
        self.tools = [JsonToShapefile]

class JsonToShapefile(object):
    def __init__(self):
        self.label = "Convert JSON to Shapefile"
        self.description = "Converts a JSON file with WKT geometries to a shapefile"

    def getParameterInfo(self):
        return [
            arcpy.Parameter("Input JSON File", "input_json", "DEFile", "Required", "Input"),
            arcpy.Parameter("Output Shapefile", "output_shp", "DEFeatureClass", "Required", "Output")
        ]

    def execute(self, parameters, messages):
        input_json = parameters[0].valueAsText
        output_shp = parameters[1].valueAsText

        with open(input_json, 'r', encoding='utf-8') as f:
            data = json.load(f)
        meta = data.get('meta')
        records = data.get('data')
        field_names = [col['name'] for col in meta['view']['columns']]
        dict_list = [dict(zip(field_names, rec)) for rec in records]
        df = pd.DataFrame(dict_list)
        df['geometry'] = df['the_geom'].apply(wkt.loads)
        gdf = gpd.GeoDataFrame(df, geometry='geometry', crs="EPSG:4326")
        gdf.to_file(output_shp)
        messages.addMessage("✅ Shapefile successfully created!")
```

### 3. Visualization in ArcGIS Pro
- Added the shapefile to a map.
- Applied `Unique Values` symbology using the `"Cluster Le"` field.
- Designed a layout with title, legend, scale bar, and north arrow.

### 4. Export Output
- Exported the layout to PDF using ArcGIS Pro's `Export Layout` tool.
- Final map is ready.

---

## 🖼️ Screenshots

### Tool Open in ArcGIS Pro
![Tool Interface](images/tool_open.png)

### Shapefile Added to Map
![Map View](images/output_map.png)

### Layout View for Export
![Layout Export](images/layout_export.png)

---

## 📦 Deliverables

| File | Description |
|------|-------------|
| `JsonToShapefile.pyt` | Python Toolbox source code |
| `notebook.ipynb` | Initial code development in Jupyter Notebook |
| `converted.shp` | Final output shapefile |
| `layout.pdf` | Exported map layout |
| `README.md` | This report |

---

## ✅ How to Use
1. Open ArcGIS Pro and add the `.pyt` file.
2. Run the tool by selecting a JSON file and specifying an output shapefile.
3. Add the result to your map and customize layout.
