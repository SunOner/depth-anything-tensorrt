<div align="left">

[Depth Anything TensorRT](https://github.com/spacewalk01/depth-anything-tensorrt) feat [sunone_aimbot_cpp](https://github.com/SunOner/sunone_aimbot_cpp)
===========================

[![python](https://img.shields.io/badge/python-3.10.12-green)](https://www.python.org/downloads/release/python-31012/)
[![cuda](https://img.shields.io/badge/cuda-13.0-green)](https://developer.nvidia.com/cuda-downloads)
[![trt](https://img.shields.io/badge/TRT-10%2B-green)](https://developer.nvidia.com/tensorrt)
[![mit](https://img.shields.io/badge/license-MIT-blue)](https://github.com/spacewalk01/depth-anything-tensorrt/blob/main/LICENSE)

</div>

<p align="center">
  Depth-Anything-V2
  <img src="assets/ferris_wheel_result.gif" height="225px" width="720px" />
</p>

> [!NOTE]
> Inference was conducted using `FP16` precision, with a warm-up period of 10 frames. The reported time corresponds to the last inference. By default, engine builds from ONNX now attempt `INT8` (falls back to `FP16` if unavailable); use `--no-int8` to force `FP16`.

## 🚀 Quick Start

#### C++

- **Step 1**: Create an engine from an onnx model and save it (defaults to INT8 with FP16 fallback; use `--no-int8` to force FP16):
``` shell
depth-anything-tensorrt.exe -model <onnx model>
```
- **Step 2**: Deserialize an engine. Once you've built your engine, the next time you run it, simply use your engine file:
``` shell
depth-anything-tensorrt.exe -model <engine file> -input <input image or video>
```
- Alternatively, you can skip immediately to running the model with just an onnx file, however, it will still generate a engine file.
``` shell
depth-anything-tensorrt.exe -model <onnx model> -input <input image or video>
```

Example:
``` shell
# infer image
depth-anything-tensorrt.exe -model depth_anything_vitb14.engine -input test.jpg
# infer folder(images/videos)
depth-anything-tensorrt.exe -model depth_anything_vitb14.engine -input data # folder containing videos/images
# infer video
depth-anything-tensorrt.exe -model depth_anything_vitb14.engine -input test.mp4 # the video path
# specify output location
depth-anything-tensorrt.exe -model depth_anything_vitb14.engine -input test.mp4 -output result # rendered depth maps will go into the "results" directory
# display progress in one line rather than multiple
depth-anything-tensorrt.exe -model depth_anything_vitb14.engine -input test.mp4 -one-line
# modify prefix of generated files (default: "depth_")
depth-anything-tensorrt.exe -model depth_anything_vitb14.engine -input test.mp4 -prefix "depthify_" # rendered depth map will have the name "depthify_test.mp4"
# show preview including before and after (may slow down performance)
depth-anything-tensorrt.exe -preview -model depth_anything_vitb14.engine -input test.mp4
# modify fps of footage (does not interpolate, will speed up or slow down footage if original video file has a different fps value)
depth-anything-tensorrt.exe -model depth_anything_vitb14.engine -input test.mp4 -fps 60
# use an existing engine file if found
depth-anything-tensorrt.exe -model depth_anything_vitb14.onnx -input test.mp4 -find-engine
# force FP16 when building from ONNX
depth-anything-tensorrt.exe -model depth_anything_vitb14.onnx -input test.mp4 -no-int8
```

<p align="center">
  <img src="assets/usage-example.png"/>
</p>

#### Python

```
cd depth-anything-tensorrt/python

# infer image
python trt_infer.py --engine <path to trt engine> --img <single-img> --outdir <outdir> [--grayscale]
```

## 🛠️ Build

#### C++

Refer to our [docs/INSTALL.md](https://github.com/spacewalk01/depth-anything-tensorrt/blob/main/docs/INSTALL.md) for C++ environment installation.

Set paths for OpenCV and TensorRT via CMake cache or environment variables:

``` shell
cmake -S . -B build -DOpenCV_DIR="C:\path\to\opencv\build" -DTENSORRT_DIR="C:\path\to\TensorRT"
```

Or set environment variables `OpenCV_DIR` and `TENSORRT_DIR` before configuring.

## 🤖 Model Preparation

1. Clone [Depth-Anything-V2](https://github.com/DepthAnything/Depth-Anything-V2.git) 
   ``` shell
   git clone https://github.com/DepthAnything/Depth-Anything-V2.git
   cd Depth-Anything-v2
   pip install -r requirements.txt
   ```
2. Download the pretrained models from the [readme](https://github.com/DepthAnything/Depth-Anything-V2.git) and put them in checkpoints folder:
3. Copy [dpt.py](https://github.com/spacewalk01/depth-anything-tensorrt/blob/main/depth_anything_v2/dpt.py) in depth_anything_v2 from this repo to `<Depth-Anything-V2>/depth_anything_v2` folder. And, Copy [export_v2.py](https://github.com/spacewalk01/depth-anything-tensorrt/blob/main/depth_anything_v2/export_v2.py) in depth_anything_v2 from this repo to `<Depth-Anything-V2>` folder.
4. Run the following to export the model:
    ``` shell
    python -m venv .venv
    ".venv/scripts/activate"
    pip install torch torchvision opencv-python onnxscript
    cd Depth-Anything-V2
    python export_v2.py --encoder vits --input-size 224
    ```

> [!TIP]
> The width and height of the model input should be divisible by 14, the patch height.