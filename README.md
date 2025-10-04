# npSAR-dl Project

> **Deep learning model for semantic segmentation of glacial lakes from Sentinel-1 SAR imagery using U-Net architecture with EfficientNet backbone.**

A deep learning-based system for automated glacial lake detection and monitoring in Nepal using Synthetic Aperture Radar (SAR) satellite imagery from Sentinel-1. This project focuses on monitoring critical glacial lakes including Imja Tsho, Gokyo Tsho, Chamlang Tsho, Tilicho Tsho, and Tsho Rolpa.
## Installation

### Requirements

Install the required dependencies using pip:

```bash
pip install torch torchvision segmentation-models-pytorch
pip install rasterio albumentations opencv-python matplotlib
pip install asf-search hyp3-sdk torchgeo
pip install scikit-learn tqdm pathlib
pip install numpy pandas pillow
```

### Configuration

1. Update the `src/config.ini` file with your credentials and parameters:
   - Set your NASA Earthdata username and password
   - Configure date ranges and lake locations (WKT polygons)
   - Specify data storage locations

2. Ensure you have the required directory structure:
   ```
   SAR_model_data/
   ├── Prelabelled/     # Raw SAR images (256x256, normalized 0-255, uint8)
   └── Labelled/        # Masked lake images (256x256, normalized 0-1, float32)
   ```

### Key Features:

- **Automated Data Pipeline**: Downloads and processes Sentinel-1 SAR data from ASF/NASA
- **Intelligent Preprocessing**: Crops, normalizes, and prepares satellite imagery for analysis
- **Advanced Segmentation**: Uses state-of-the-art deep learning for precise lake boundary detection
- **Multi-Lake Support**: Monitors multiple critical glacial lakes simultaneously

## SAR Data Processing Pipeline

The system processes raw Sentinel-1 SAR data through multiple stages:

### Data Acquisition & Processing
- **Automated Download**: Retrieves Sentinel-1 GRD products via ASF Search API
- **Radiometric Terrain Correction**: Uses ASF HyP3 for preprocessing
- **Geographic Cropping**: Clips data to specific lake areas of interest
- **Histogram Equalization**: Normalizes pixel values for consistent analysis

### Deep Learning Pipeline
- **Custom Dataset Handling**: Manages paired SAR imagery and lake masks
- **Data Augmentation**: Applies rotation, flipping, and elastic transforms
- **Model Training**: U-Net with EfficientNet-B3 backbone for semantic segmentation
- **Performance Monitoring**: Tracks IoU metrics and validation loss

### Output Generation
- **Lake Area Calculation**: Computes precise area measurements from segmentation masks
- **Change Detection**: Identifies temporal variations in lake boundaries  
- **Visualization**: Generates comparison images and analysis reports

## Model Architecture

The system uses a **U-Net segmentation model** with:
- **Backbone**: EfficientNet-B3 (pre-trained on ImageNet)
- **Input**: Single-channel SAR imagery (256x256 pixels)
- **Output**: Binary segmentation masks (water vs. non-water)
- **Loss Function**: Combined BCE + Dice + Focal Loss for handling class imbalance

**Training Configuration**:
- Batch Size: 32
- Learning Rate: 0.0001 (AdamW optimizer)
- Data Augmentation: 20x multiplication with geometric transforms
- Early Stopping: Based on validation IoU performance

## Data Organization

### Training Dataset Structure
```
Training_Dataset/
├── chamlangTsho/          # Chamlang Lake training data
├── gokyoTsho/             # Gokyo Lake training data  
├── imjaTsho/              # Imja Lake training data
├── tilichoTsho/           # Tilicho Lake training data
├── tshoRolpa/             # Tsho Rolpa Lake training data
├── Labelled/              # Manually labeled lake masks
├── Prelabelled/           # Raw SAR imagery
└── *.geojson              # Area of interest boundaries
```

### Processing Workflow
```
Raw Sentinel-1 Data → Download & RTC → Crop to AOI → Normalize → 
Pad to 256x256 → Model Training/Inference → Lake Area Calculation
```

## Usage

### 1. Data Preparation
```bash
cd src/
python DownloadProcessing.py  # Download and process SAR data
python Crop_all.py           # Crop images to lake areas
python Normalize.py          # Apply histogram equalization
python Pad_all.py           # Standardize to 256x256 pixels
```

### 2. Model Training
```bash
# Open and run the Jupyter notebook
jupyter notebook TrainTest.ipynb
```

### 3. Lake Area Analysis
```bash
python CheckArea.py          # Calculate lake areas from processed data
```

## Monitoring Capabilities

The system tracks key metrics for each glacial lake:
- **Lake Surface Area**: Precise measurements in hectares
- **Temporal Changes**: Area variations over multiple years (2015-2025)
- **Seasonal Patterns**: Monthly/seasonal lake level fluctuations
- **Long-term Trends**: Multi-year expansion or contraction patterns

## Target Lakes

Currently monitoring five critical glacial lakes in Nepal:

1. **Imja Tsho** - High-risk GLOF potential, Everest region
2. **Gokyo Tsho** - Popular trekking area, Everest region  
3. **Chamlang Tsho** - Remote monitoring location
4. **Tilicho Tsho** - World's highest lake at 4,919m
5. **Tsho Rolpa** - Large glacial lake, Rolwaling Valley

Each lake has dedicated GeoJSON boundary files and historical training data.

## Technical Specifications

- **Satellite Data**: Sentinel-1 C-band SAR (VV polarization)
- **Spatial Resolution**: 20m ground resolution
- **Temporal Coverage**: 2015-2025
- **Processing Framework**: PyTorch, Segmentation Models PyTorch
- **Geospatial Libraries**: Rasterio, GDAL, ASF Search SDK
- **Model Performance**: IoU > 0.8 for water detection

## Project Structure

```
npSAR-dl/
├── src/                   # Core processing scripts
│   ├── DownloadProcessing.py    # SAR data download & processing
│   ├── Crop_Product.py          # Image cropping utilities
│   ├── Normalize.py             # Histogram equalization
│   ├── padding.py               # Image padding to standard size
│   ├── CheckArea.py             # Lake area calculations
│   └── config.ini               # Configuration parameters
├── TrainTest.ipynb        # Main training/testing notebook
├── Training_Dataset/      # Organized training data by lake
├── SAR_model_data/       # Model input data (Prelabelled/Labelled)
├── For_website/          # Processed outputs for web display
└── README.md             # This documentation
```

