# Setup Guide: LivePortrait on Mac Mini M4 (16GB RAM)

## ⚠️ Important Performance Note
LivePortrait on Apple Silicon (M4) will be **significantly slower** than NVIDIA GPUs - approximately 20x slower according to the official documentation. The 16GB RAM should be sufficient for the models.

## Prerequisites

### 1. Install Homebrew (if not already installed)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. Install Python 3.10
The project requires Python 3.10 specifically:
```bash
brew install python@3.10
```

### 3. Install FFmpeg
FFmpeg is required for video processing:
```bash
brew install ffmpeg
```

Verify FFmpeg installation:
```bash
ffmpeg -version
ffprobe -version
```

## Installation Steps

### 1. Navigate to Project Directory
```bash
cd /path/to/LivePortrait
```

### 2. Create Virtual Environment
```bash
python3.10 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
Install the macOS-specific requirements (without X-Pose, which doesn't support macOS):
```bash
pip install -r requirements_macOS.txt
```

**If you encounter "No module named 'requests'" error:**
```bash
pip install requests transformers
```

### 4. Download Pretrained Weights
The models are approximately 3GB. Download using HuggingFace CLI:

```bash
# Install huggingface_hub
pip install -U "huggingface_hub[cli]"

# Download the weights
huggingface-cli download KwaiVGI/LivePortrait --local-dir pretrained_weights --exclude "*.git*" "README.md" "docs"
```

**Alternative**: If you can't access HuggingFace, use the mirror:
```bash
export HF_ENDPOINT=https://hf-mirror.com
huggingface-cli download KwaiVGI/LivePortrait --local-dir pretrained_weights --exclude "*.git*" "README.md" "docs"
```

**Manual Alternative**: Download from [Google Drive](https://drive.google.com/drive/folders/1UtKgzKjFAOmZkhNK-OYT0caJ_w2XAnib) or [Baidu Yun](https://pan.baidu.com/s/1MGctWmNla_vZxDbEp2Dtzw?pwd=z5cn) and unzip into `./pretrained_weights`

## Running LivePortrait

### Option 1: Quick Test (Command Line)
Test with the default example:
```bash
PYTORCH_ENABLE_MPS_FALLBACK=1 python inference.py
```

This will generate `animations/s6--d0_concat.mp4`

### Option 2: Custom Input (Command Line)
Animate your own images:
```bash
PYTORCH_ENABLE_MPS_FALLBACK=1 python inference.py \
  -s assets/examples/source/s9.jpg \
  -d assets/examples/driving/d0.mp4
```

### Option 3: Gradio Web Interface (Recommended)
Launch the interactive web interface:
```bash
PYTORCH_ENABLE_MPS_FALLBACK=1 python app.py
```

Then open your browser to: `http://localhost:7860`

Optional flags:
```bash
# Custom port
PYTORCH_ENABLE_MPS_FALLBACK=1 python app.py --server_port 8080

# Make accessible on local network
PYTORCH_ENABLE_MPS_FALLBACK=1 python app.py --server_name 0.0.0.0

# Public share link (via Gradio)
PYTORCH_ENABLE_MPS_FALLBACK=1 python app.py --share
```

## 🚫 What Doesn't Work on macOS

**Animals Mode is NOT supported** on macOS because:
- X-Pose dependency requires CUDA
- Only works on Linux/Windows with NVIDIA GPU
- Stick to humans mode only

## Tips for Best Results

### Input Guidelines
1. **Source Image/Video**:
   - Clear frontal face
   - Good lighting
   - Minimal motion (for videos)

2. **Driving Video**:
   - Use `--flag_crop_driving_video` for auto-cropping
   - 1:1 aspect ratio works best (512x512)
   - First frame should be neutral expression, frontal face
   - Minimize shoulder movement

### Performance Optimization
- **First run will be slow** as models load
- Close other memory-intensive applications
- Expect 20x slower performance than GPU
- Shorter videos (5-10 seconds) recommended
- Lower resolution inputs process faster

### Example Commands

**Portrait Animation:**
```bash
PYTORCH_ENABLE_MPS_FALLBACK=1 python inference.py \
  -s assets/examples/source/s9.jpg \
  -d assets/examples/driving/d0.mp4
```

**Video-to-Video Editing:**
```bash
PYTORCH_ENABLE_MPS_FALLBACK=1 python inference.py \
  -s assets/examples/source/s13.mp4 \
  -d assets/examples/driving/d0.mp4
```

**Using Motion Templates (faster, privacy-friendly):**
```bash
PYTORCH_ENABLE_MPS_FALLBACK=1 python inference.py \
  -s assets/examples/source/s9.jpg \
  -d assets/examples/driving/d5.pkl
```

**Auto-crop driving video:**
```bash
PYTORCH_ENABLE_MPS_FALLBACK=1 python inference.py \
  -s assets/examples/source/s9.jpg \
  -d assets/examples/driving/d13.mp4 \
  --flag_crop_driving_video
```

## Troubleshooting

### "FFmpeg is not installed"
Make sure FFmpeg is in your PATH:
```bash
which ffmpeg
which ffprobe
```

### "CUDA not available" warnings
This is normal on macOS - the system will use MPS (Metal Performance Shaders) instead.

### Out of Memory
- Close other applications
- Try smaller input videos
- Reduce video resolution before processing

### Slow Performance
- This is expected on Apple Silicon (20x slower than NVIDIA GPU)
- Consider using the HuggingFace Space for faster results
- Or use motion templates (.pkl) to skip driving video processing

## Expected Output Location

Generated videos will be saved to:
- `animations/` directory
- Filename format: `{source_name}--{driving_name}_concat.mp4`

## Getting Help

- Official repo: https://github.com/KwaiVGI/LivePortrait
- HuggingFace Space: https://huggingface.co/spaces/KwaiVGI/liveportrait
- Issues: https://github.com/KwaiVGI/LivePortrait/issues

---

**Ready to start?** Run these commands:
```bash
# 1. Activate virtual environment
source venv/bin/activate

# 2. Download weights (if not done)
huggingface-cli download KwaiVGI/LivePortrait --local-dir pretrained_weights --exclude "*.git*" "README.md" "docs"

# 3. Launch Gradio interface
PYTORCH_ENABLE_MPS_FALLBACK=1 python app.py
```
