# Ball Detection & Removal Project

## Overview

This project automatically detects and removes balls from images using advanced computer vision techniques. It leverages YOLOv8 for object detection and segmentation, combined with inpainting algorithms to seamlessly remove detected balls from images.


## Sample Input vs Output

<table>
  <tr>
    <th>Input Image</th>
    <th>Output Image (Ball Removed)</th>
  </tr>
  <tr>
    <td align="center">
      <img src="assets/input_.jpg" alt="Input Image" width="380">
    </td>
    <td align="center">
      <img src="assets/output_.jpg" alt="Output Image" width="380">
    </td>
  </tr>
</table>



## Features

- **Automatic Ball Detection**: Uses YOLOv8 segmentation model to detect balls in images
- **Fallback Detection**: Implements Hough Circle Transform as a backup detection method
- **Intelligent Inpainting**: Combines Telea and Navier-Stokes inpainting algorithms for natural-looking results
- **Mask Refinement**: Applies morphological operations and Gaussian blurring to refine detection masks
- **Batch Processing**: Processes multiple images in one workflow
- **Google Drive Integration**: Seamlessly downloads images from and uploads results to Google Drive
- **Automatic Zipping**: Compresses all processed images for easy download

## Requirements

The project automatically installs the following dependencies:
- `ultralytics` - YOLOv8 model framework
- `opencv-python-headless` - Computer vision library
- `google-api-python-client` - Google Drive API client
- `google-auth-httplib2` - Google authentication support
- `google-auth-oauthlib` - OAuth authentication for Google
- `numpy` - Numerical computing

## Setup Instructions

### Prerequisites
- Google Colaboratory account (Colab environment)
- Access to a Google Drive folder containing images

### Configuration

Update the following variables in the notebook:

```python
FOLDER_ID = "your_google_drive_folder_id"  # Your Drive folder ID containing images
LOCAL_INPUT = "/content/input_images"       # Temporary local directory for downloaded images
LOCAL_OUTPUT = "/content/output_images"     # Temporary local directory for processed images
DRIVE_OUTPUT_DIR = "/content/drive/MyDrive/ball_removed_results"  # Drive folder for outputs
ZIP_PATH = "/content/removed_images.zip"    # Path for the output ZIP file
```

### Supported Image Formats
- `.jpg`
- `.jpeg`
- `.png`

## How It Works

### 1. **Authentication & Setup**
   - Authenticates with Google Drive API
   - Creates necessary local and Drive directories

### 2. **Image Download**
   - Lists all files in the specified Google Drive folder
   - Downloads valid image files (JPG, PNG) to local input directory

### 3. **Ball Detection**
   - Loads YOLOv8-X segmentation model with ball class filtering
   - Performs inference on each image
   - Filters detections by:
     - Area ratio (0.00001 to 0.015 of image size)
     - Aspect ratio (0.7 to 1.3 for circular objects)
   
### 4. **Fallback Detection**
   - If YOLO detection fails, uses Hough Circle Transform
   - Automatically detects circular objects in the image

### 5. **Mask Refinement**
   - Applies morphological closing to fill small gaps
   - Dilates mask to ensure complete ball coverage
   - Applies Gaussian blur for smooth edges
   - Binary thresholding for clean mask boundaries

### 6. **Image Inpainting**
   - **First Pass**: Telea algorithm for initial inpainting
   - **Second Pass**: Navier-Stokes algorithm for refinement
   - **Border Blending**: Gaussian blur applied at mask edges for seamless blending

### 7. **Output & Upload**
   - Saves processed images to local output directory
   - Attempts to save to Google Drive mounted path
   - Falls back to Drive API upload if mounting fails
   - Creates/uses `ball_removed_results` folder in Google Drive
   - Compresses all outputs into a ZIP file
   - Prompts browser download of the ZIP archive

## Output Structure

```
ball_removed_results/
├── removed_image1.jpg
├── removed_image2.png
└── ...
```

All processed images are prefixed with `removed_` to distinguish them from originals.

## Key Parameters

| Parameter | Value | Purpose |
|-----------|-------|---------|
| YOLO Confidence | 0.25 | Minimum detection confidence threshold |
| YOLO IoU | 0.45 | Intersection over Union for NMS |
| Area Ratio Min | 0.00001 | Minimum ball size relative to image |
| Area Ratio Max | 0.015 | Maximum ball size relative to image |
| Aspect Ratio | 0.7 - 1.3 | Ball circularity constraint |
| Inpaint Radius | 3 | Neighborhood radius for inpainting |
| Morphology Kernel | 7x7 | Structuring element size for mask refinement |


## Usage Example

1. Place your images in a Google Drive folder
2. Copy the folder ID from the Drive URL
3. Update `FOLDER_ID` in the notebook
4. Run the notebook in Google Colaboratory
5. Authenticate with your Google account when prompted
6. Wait for processing to complete
7. Download the ZIP file with processed images

## Notes

- **Processing Time**: Varies based on number and size of images
- **Model Size**: YOLOv8-X (~133MB) is auto-downloaded on first run
- **GPU**: Google Colab provides free GPU acceleration
- **Storage**: Requires sufficient Drive quota for output images

## Project Structure

```
Intern Task/
├── ball_detection_removal.ipynb    # Main notebook with all processing logic
├── missed_dataset/                 # Directory for images that were skipped
├── removed_images/                 # Directory containing processed images
├── removed_images.zip              # Compressed archive of processed images
└── README.md                        # This file
```

## Future Improvements

- Support for multiple object types beyond balls
- Batch processing with progress bars
- Configurable inpainting algorithms selection
- Quality metrics and before/after comparison
- Video processing support

## Troubleshooting

**Issue**: "No files found in that Drive folder"
- **Solution**: Verify the FOLDER_ID is correct and you have access to the folder

**Issue**: "Could not load local weights"
- **Solution**: Model will auto-download weights from Ultralytics on first run

**Issue**: Processing is slow
- **Solution**: Ensure you're running in Google Colab with GPU enabled (Runtime → Change runtime type)

**Issue**: Drive save failures
- **Solution**: Check Drive quota and folder permissions; the API fallback will attempt to upload instead

## License

This project uses pre-trained models from Ultralytics YOLOv8 and Google Drive APIs.

## Author

Intern Task Project
