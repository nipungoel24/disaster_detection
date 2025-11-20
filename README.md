# SAR Flood Detection Project - Setup Instructions

This guide will help you set up the flood detection project on a new system.

---

## Prerequisites

- **Python**: Version 3.8 or higher (recommended: 3.10 or 3.11)
- **Operating System**: Windows, Linux, or macOS
- **Disk Space**: At least 10 GB for dependencies and data
- **RAM**: Minimum 8 GB (16 GB recommended for large districts)

---

## Step 1: Install Python

### Windows:
1. Download Python from [python.org](https://www.python.org/downloads/)
2. During installation, **check "Add Python to PATH"**
3. Verify installation:
   ```cmd
   python --version
   ```

### Linux/macOS:
```bash
# Check if Python is installed
python3 --version

# If not installed (Ubuntu/Debian):
sudo apt update
sudo apt install python3 python3-pip python3-venv

# macOS with Homebrew:
brew install python3
```

---

## Step 2: Install GDAL (Required for Rasterio)

GDAL is a critical dependency for geospatial data processing.

### Windows:
1. **Option A - Using OSGeo4W (Recommended):**
   - Download OSGeo4W installer from [trac.osgeo.org](https://trac.osgeo.org/osgeo4w/)
   - Run installer and select "Express Install"
   - Select GDAL package
   - Add GDAL to PATH: `C:\OSGeo4W64\bin`

2. **Option B - Using Conda (Easier):**
   ```cmd
   conda install -c conda-forge gdal
   ```

3. **Option C - Using pip with precompiled wheels:**
   ```cmd
   pip install GDAL-3.6.4-cp310-cp310-win_amd64.whl
   ```
   Download wheels from: [www.lfd.uci.edu/~gohlke/pythonlibs/#gdal](https://www.lfd.uci.edu/~gohlke/pythonlibs/#gdal)

### Linux (Ubuntu/Debian):
```bash
sudo apt update
sudo apt install gdal-bin libgdal-dev python3-gdal
export CPLUS_INCLUDE_PATH=/usr/include/gdal
export C_INCLUDE_PATH=/usr/include/gdal
```

### macOS:
```bash
brew install gdal
export CPLUS_INCLUDE_PATH=/opt/homebrew/include
export C_INCLUDE_PATH=/opt/homebrew/include
```

### Verify GDAL Installation:
```cmd
gdalinfo --version
```

---

## Step 3: Clone or Copy the Project

```bash
# If using Git:
git clone https://github.com/nipungoel24/disaster_detection.git
cd disaster_detection

# Or manually copy the project folder to your system
```

---

## Step 4: Create a Virtual Environment (Recommended)

### Windows:
```cmd
cd c:\path\to\Minor_Project
python -m venv venv
venv\Scripts\activate
```

### Linux/macOS:
```bash
cd /path/to/Minor_Project
python3 -m venv venv
source venv/bin/activate
```

You should see `(venv)` prefix in your terminal after activation.

---

## Step 5: Install Python Dependencies

With the virtual environment activated:

```cmd
# Upgrade pip first
python -m pip install --upgrade pip

# Install all required packages
pip install -r requirements.txt
```

### Common Installation Issues:

**Issue 1: GDAL fails to install**
```cmd
# Solution: Install using conda
conda install -c conda-forge rasterio gdal fiona geopandas
```

**Issue 2: scikit-image build errors**
```cmd
# Install precompiled version
pip install --only-binary :all: scikit-image
```

**Issue 3: Numpy compatibility errors**
```cmd
# Install compatible versions
pip install "numpy>=1.24,<2.0" "scipy>=1.11,<1.12"
```

---

## Step 6: Set Up Google Earth Engine (For Data Retrieval)

The project uses Google Earth Engine for downloading Sentinel-1 SAR data.

### 6.1 Create a GEE Account:
1. Go to [earthengine.google.com](https://earthengine.google.com/)
2. Sign up with your Google account
3. Create a new GEE project (note the project ID)

### 6.2 Authenticate Earth Engine:
```python
# Run this in Python or Jupyter
import ee
ee.Authenticate()
```
Follow the browser authentication flow.

### 6.3 Initialize with Your Project:
```python
# Replace with your GEE project ID
ee.Initialize(project='your-project-id')
```

**Update the project ID in `Data_retreival.ipynb`:**
- Find line: `ee.Initialize(project = "cobalt-vector-470207-a5")`
- Replace with: `ee.Initialize(project = "your-project-id")`

---

## Step 7: Configure Project Paths

### 7.1 Update Directory Paths in Notebooks:

Open `Data_retreival.ipynb` and update:
```python
# Line ~620 in the SAR change detection cell
OUTPUT_CHIPS_DIR = r'C:\Your\Path\To\Minor_Project\Output_chips'
FLOOD_MASKS_DIR = r'C:\Your\Path\To\Minor_Project\Flood_Masks'
FINAL_MAPS_DIR = r'C:\Your\Path\To\Minor_Project\Final_Flood_Maps'
```

### 7.2 Create Required Directories:
```cmd
mkdir Output_chips
mkdir Flood_Masks
mkdir Final_Flood_Maps
mkdir flood_analysis\results
```

---

## Step 8: Verify Installation

### 8.1 Test Python Environment:
```python
# Create test_install.py
import numpy
import rasterio
import scipy
import skimage
from earthengine import ee

print("✅ All core libraries imported successfully!")
print(f"Numpy: {numpy.__version__}")
print(f"Rasterio: {rasterio.__version__}")
print(f"Scipy: {scipy.__version__}")
print(f"Scikit-image: {skimage.__version__}")
```

Run: `python test_install.py`

### 8.2 Test GDAL:
```cmd
gdalinfo --version
python -c "from osgeo import gdal; print(gdal.__version__)"
```

### 8.3 Test Earth Engine:
```python
import ee
ee.Initialize(project='your-project-id')
print("✅ Earth Engine initialized successfully!")
```

---

## Step 9: Download Satellite Data (Optional)

If you need to download new Sentinel-1 data:

1. Open `Data_retreival.ipynb` in Jupyter
2. Update district geometries and date ranges (lines 30-50)
3. Run cells to export data to Google Drive
4. Download from Drive to `Output_chips/` directory

**Note**: The project already has data in `Output_chips/` directories for 5 districts.

---

## Step 10: Run the Flood Detection Pipeline

### 10.1 Launch Jupyter Notebook:
```cmd
cd c:\path\to\Minor_Project
jupyter notebook
```

### 10.2 Open `Data_retreival.ipynb`

### 10.3 Run the SAR Change Detection Cell:
- Navigate to the cell starting with "# SAR CHANGE DETECTION FOR FLOOD MAPPING"
- Press `Shift+Enter` to execute
- Processing time: 5-30 minutes depending on district size

### 10.4 Monitor Output:
```
🌊 Processing Barpeta...
   Processing 96 chip pairs...
   ✅ Successfully processed: 96/96 chips
   Stitching 96 mask chips...
   ✅ Final flood map saved: Final_Flood_Maps\Barpeta_Flood_Map.tif
   📊 Flooded area: 15.23% of total pixels
```

---

## Step 11: Visualize Results in QGIS

### 11.1 Install QGIS:
Download from [qgis.org](https://qgis.org/en/site/forusers/download.html)

### 11.2 Load Flood Maps:
1. Open QGIS
2. `Layer` → `Add Layer` → `Add Raster Layer`
3. Navigate to `Final_Flood_Maps/`
4. Load `{District}_Flood_Map.tif`

### 11.3 Style the Flood Layer:
1. Right-click layer → `Properties` → `Symbology`
2. Set render type to "Paletted/Unique values"
3. Value 0: Transparent (non-flooded)
4. Value 1: Blue (flooded areas)

---

## Step 12: Parameter Tuning (If Needed)

If flood detection is too sensitive (>50% flooded) or not sensitive enough (<5%):

### Adjust in the notebook configuration:
```python
# Make detection LESS sensitive (fewer false positives):
FIXED_THRESHOLD = -4.0  # Changed from -1.5 (more conservative)
MIN_OBJECT_SIZE = 100   # Changed from 10 (removes small artifacts)

# Make detection MORE sensitive (catch more floods):
FIXED_THRESHOLD = -1.0  # Changed from -1.5 (less conservative)
MIN_OBJECT_SIZE = 5     # Changed from 10 (keep smaller regions)
```

Re-run the cell after adjustments.

---

## Project Structure

```
Minor_Project/
├── Data_retreival.ipynb          # Main notebook with GEE data download & SAR analysis
├── requirements.txt              # Python dependencies
├── SETUP_INSTRUCTIONS.md         # This file
├── Output_chips/                 # Input: Satellite image chips
│   ├── Barpeta/
│   │   ├── pre_flood/           # Pre-flood SAR chips
│   │   └── post_flood/          # Post-flood SAR chips
│   ├── Dhemaji/
│   ├── Lakhimpur/
│   ├── Nalbari/
│   └── Sonitpur/
├── Flood_Masks/                  # Output: Individual chip flood masks
│   ├── Barpeta/
│   ├── Dhemaji/
│   └── ...
├── Final_Flood_Maps/             # Output: Stitched district-level flood maps
│   ├── Barpeta_Flood_Map.tif
│   ├── Dhemaji_Flood_Map.tif
│   └── ...
└── flood_analysis/               # Additional analysis scripts
    └── results/
```

---

## Troubleshooting

### Issue: "ModuleNotFoundError: No module named 'rasterio'"
**Solution**: 
```cmd
pip install rasterio
# If fails, use conda:
conda install -c conda-forge rasterio
```

### Issue: "ERROR 4: Unable to open file"
**Solution**: Check file paths are correct with raw strings `r'C:\path'`

### Issue: "Memory Error" during processing
**Solution**: 
- Process districts one at a time
- Reduce chip size in GEE export
- Increase system RAM or use smaller test region

### Issue: "Earth Engine authentication failed"
**Solution**: 
```python
import ee
ee.Authenticate()  # Re-authenticate with browser
```

### Issue: All areas showing as flooded (>90%)
**Solution**: 
- Increase `FIXED_THRESHOLD` to -4.0 or -5.0
- Ensure `APPLY_DESPECKLE = True`
- Increase `MIN_OBJECT_SIZE` to 100 or 200

### Issue: No flood detected (0-2%)
**Solution**:
- Decrease `FIXED_THRESHOLD` to -1.0 or -0.5
- Check if pre/post dates are correct (need actual flood event)
- Verify SAR data quality in QGIS

---

## Additional Resources

- **Google Earth Engine Documentation**: [developers.google.com/earth-engine](https://developers.google.com/earth-engine)
- **Rasterio Documentation**: [rasterio.readthedocs.io](https://rasterio.readthedocs.io/)
- **GDAL Installation Guide**: [gdal.org/download.html](https://gdal.org/download.html)
- **SAR Change Detection Theory**: [earthobservatory.nasa.gov](https://earthobservatory.nasa.gov/)

---

## Support

For issues specific to this project:
- GitHub Issues: [github.com/nipungoel24/disaster_detection/issues](https://github.com/nipungoel24/disaster_detection/issues)
- Email: Contact project maintainer

---

## License

MIT License - See LICENSE file for details

---

**Last Updated**: November 2025
