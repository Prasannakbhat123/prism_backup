# Samsung-Prism: Interactive Image Segmentation & Annotation

This is a comprehensive toolkit for interactive image segmentation, annotation, and video object tracking. It combines modern deep learning models (RITM, XMem) with a user-friendly web interface and powerful Python utilities for mask/JSON conversion and dataset management.

![Samsung-Prism Overview](working.png)

---

## Features

- **Interactive Image Segmentation**: Annotate images using polygons or advanced AI models.
- **RITM (Reviving Iterative Training with Mask guidance)**: Fast, interactive segmentation with clicks.
- **XMem**: State-of-the-art video object segmentation and tracking.
- **Polygon Annotation Tools**: Draw, edit, and export polygons for precise labeling.
- **Mask ↔ JSON Conversion**: Seamlessly convert between segmentation masks and JSON polygon annotations.
- **Meta Tools**: Generate and use metadata for advanced workflows.
- **Multi-format Support**: RGB, grayscale, and paletted masks.
- **Modern Web UI**: Built with React (Vite), Tailwind, and Flask backend.

---

## Directory Structure

- `/frontend` — React web app (Vite, Tailwind)
- `/backend` — Python Flask/Express backend for annotation storage and API
- `/ritm_interactive_segmentation` — RITM web demo, mask-to-JSON, and helper scripts
- `/XMem2-cpu-web` — XMem video object segmentation/tracking
- `/src/scripts` — Python utilities for mask/JSON conversion, meta, etc.
- `/src/JPEGImages` — Input images
- `/src/mask-ritm` — RITM-generated masks
- `/src/json` — Output JSON annotations
- `/src/Annotations` — Additional mask storage

---

## Setup Instructions

### 1. Install Node/Frontend & Backend Dependencies
Run the following in BOTH the `frontend` and `backend` directories:
```bash
cd frontend
npm install --legacy-peer-deps
cd ../backend
npm install --legacy-peer-deps
cd ..
```

### 2. Set Up Python Environments

#### a. XMem (root venv)
- In the project root, create a virtual environment for XMem:
```bash
python -m venv venv
source venv/bin/activate
pip install -r XMem2-cpu-web/requirements.txt
```

#### b. RITM (ritm_interactive_segmentation/env)
- In `ritm_interactive_segmentation`, create a separate virtual environment for RITM (required for numpy 1.x compatibility):
```bash
cd ritm_interactive_segmentation
python -m venv env
source env/bin/activate
pip install -r requirements.txt
# (Make sure numpy==1.x is installed)
cd ..
```

### 3. Download Model Weights
- Download the model weights from the following Google Drive link (placeholder):
  **[Download Weights & Saves (Google Drive)](https://drive.google.com/drive/folders/1UDfkl1ZAH_wpMlgMg6tJaDaHJMDPRgVl?usp=drive_link)**
- After downloading, place the folders as follows:
  - Place the `saves` folder inside `XMem2-cpu-web/`
  - Place the `weights` folder inside `ritm_interactive_segmentation/`

### 4. Start the Application
- In the project root, run:
```bash
npm run start
```

This will start all necessary servers and the web UI.

---

## How Everything Works Together

### **User Workflow**
1. **Load Image**: Select or upload an image from the UI.
2. **Annotate**: Use polygon tools or RITM interactive segmentation (click-based) to segment objects.
3. **RITM Mode**: Click to add foreground/background points. RITM generates a mask in real time.
4. **Switch Modes**: Toggle between RITM and manual polygon annotation. When switching from RITM, the mask is auto-converted to JSON polygons.
5. **Export**: Download/export JSON annotations or masks.
6. **Video/Sequence**: Use XMem for video object tracking and segmentation (see XMem section).

### **Data Flow**
- **Masks** are saved in `/src/mask-ritm` (RITM) or `/src/Annotations` (manual/other tools).
- **JSON** annotations are saved in `/src/json`.
- **Conversion**: Use provided scripts to convert between mask and JSON formats, or to generate meta files.

---

## Advanced Tools & Scripts

### **Mask to JSON**
- Convert segmentation masks (grayscale or RGB) to polygon-based JSON annotations.
- Supports both single-channel and multi-class color masks.
- Color mapping (BGR):
  - (0, 0, 255): "1" (Red)
  - (255, 0, 0): "2" (Blue)
  - (0, 255, 0): "3" (Green)
  - (255, 255, 0): "4" (Cyan)
  - (255, 0, 255): "5" (Magenta)
  - (0, 255, 255): "6" (Yellow)
  - (128, 0, 128): "7" (Purple)
  - (255, 165, 0): "8" (Orange)

### **JSON to Mask**
- Convert polygon JSON annotations back to color masks for training or visualization.
- See `/src/scripts/create_masks.py` and related scripts.

### **Meta Tools**
- Generate `meta.json` files for advanced workflows (e.g., matching instances across frames).
- See `/src/scripts/generate_meta_json.py`.

### **Helper Scripts**
- `/ritm_interactive_segmentation/web_demo/mask_to_json.py`: Main mask-to-JSON logic for web demo.
- `/src/scripts/mask_to_json_fixed.py`: Robust mask-to-JSON for batch processing.
- `/ritm_interactive_segmentation/p2m-m2p/`: Point-to-mask and mask-to-point conversion utilities.

### **XMem Integration**
- `/XMem2-cpu-web/`: Video object segmentation and tracking.
- Use for propagating masks across video frames.
- See XMem README for details.

### **Frontend and Backend Setup**
- For both the `/frontend` and `/backend` directories, make sure to install dependencies using:
  ```bash
  npm install --legacy-peer-deps
  ```
- This ensures compatibility with any conflicting or outdated peer dependencies in the project setup.

---

## Usage Guide

1. **Start all servers (backend, frontend, RITM/XMem as needed).**
2. **Open the web UI** (usually at http://localhost:5173 or http://localhost:3000).
3. **Load an image** and annotate using polygons or RITM.
4. **Switch modes** as needed; RITM masks are auto-converted to JSON.
5. **Export** your work as JSON or mask images.
6. **For video:** Use XMem for tracking/propagation.
7. **Use scripts** for batch conversion or dataset management.

**Note:**  
Before starting, make sure to update the hardcoded paths below (MASK_RITM_DIR, JPEGIMAGES_DIR, JSON_DIR, PROCESS_SINGLE_FRAME_SCRIPT) to match the correct locations on your system. These are currently set for the original developer's environment and may not work on your machine without modification.

# ==== HARDCODED PATHS (centralized for future refactor) ====
MASK_RITM_DIR = '/home/aravinthakshan/Projects/Samsung2/Samsung-Prism/backend/src/mask-ritm'
JPEGIMAGES_DIR = '/home/aravinthakshan/Projects/Samsung2/Samsung-Prism/backend/src/JPEGImages'
JSON_DIR = '/home/aravinthakshan/Projects/Samsung2/Samsung-Prism/backend/src/json'
PROCESS_SINGLE_FRAME_SCRIPT = '/home/aravinthakshan/Projects/Samsung2/Samsung-Prism/backend/src/scripts/process_single_frame.py'


---

## Important Notes and Best Practices

### Model Switching Delay
When switching between RITM and XMem workflows, please allow a few seconds for the APIs to initialize. This delay occurs because the system dynamically switches between the main Express thread (used by XMem) and the Flask-based RITM API.

### RITM Click Behavior
If you place two points that correspond to the same object but are very far apart in the RITM interface, the model may interpret them as separate instances. This can be corrected manually in polygon editing mode after the initial segmentation.

### Virtual Environment Setup
The `server.js` file expects two separate Python virtual environments:

- One named `env` for XMem  
- Another named `venv` for RITM  

This separation is necessary due to conflicting dependencies between the two models. The backend handles switching between these environments automatically, but you must ensure that `requirements.txt` files in both submodules are installed correctly.

### Session Persistence and Export
All masks and annotations created during a session are persisted. Users can export both the segmentation masks and the corresponding JSON polygon annotations at any time.

---

## Troubleshooting & Tips
- Ensure all dependencies (Python, Node, model weights) are installed.
- For RITM/XMem, download the required model weights as per their READMEs.
- Use only lossless PNG masks for best results.
- For custom color mappings, update the color dictionaries in the relevant scripts.
- If you encounter errors, check the console/logs for details.

---
#   p r i s m _ b a c k u p  
 #   p r i s m _ b a c k u p  
 #   p r i s m _ b a c k u p  
 