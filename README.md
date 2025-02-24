# Real Time Semantic Segmentation
Deployment has been the biggest bottleneck for large language models. I decided to explore two frameworks used for deploying machine learning models; ONNX Runtime and Torchscript. I exported a semantic segmentation model from TorchHub to the respective frameworks. I compared their FPS during runtime and found that ONNX Runtime is the winner with an average of 18 FPS compared to Torchscript's 6 FPS on the Apple M1 Silicon Chip.

## Deployment Frameworks
ML deployment frameworks in general has aimed to make ML algorithms optimized for a specific hardware. A computational graph is created so the framework can optimize the model by eliminating redundant and fusing operations to reduce computational and memory costs. Here are some videos explaining the two deployment frameworks used for this project.

Torchscript Framework Explained

[![Torchscript Framework Explained:](http://i3.ytimg.com/vi/2awmrMRf0dA/hqdefault.jpg)](https://www.youtube.com/watch?v=2awmrMRf0dA&ab_channel=PyTorch)

ONNX Runtime Framework Explained

[![ONNX Runtime Framework Explained:](http://i3.ytimg.com/vi/Ij5MoUnLQ0E/hqdefault.jpg)](https://www.youtube.com/watch?v=Ij5MoUnLQ0E&t=2109s&ab_channel=MicrosoftResearch)

### Torchscript vs ONNX Runtime
The main difference between the two frameworks comes down to hardware acceleration. ONNX Runtime has an additional step where the framework partitions the computational graph into sub-graphs and assign matching hardware accelerators to further optimize the model. In addition, the ONNX Runtime tensors are materialized for the single-purpose of inferencing, you can't do additional operations on the tensors making it somewhat tedious to parse results or perform something like softmax over a specific dimension. On the other hand, Torch tensors are loaded with functionalities but at the sacrifice of computational efficiency.

## Prerequisites
The required libraries for this projects are:
- libtorch
- ONNX Runtime
- OpenCV

To install libtorch, use the following [link](https://pytorch.org/cppdocs/installing.html) and follow PyTorch's official installation guide. To install ONNX Runtime, I personally built the package for inference only, to do that follow the instructions [here](https://onnxruntime.ai/docs/build/inferencing.html). Finally, you can install OpenCV on C++ by following the instructions [here](https://gist.github.com/nucliweb/b2a234c673221af5ec24508da7d8b854). Keep in mind that my installations were specific to Apple's M1 silicon chip. Build your libraries that suit your intended hardware.

## Building the applications
After git cloning the repositories, to build the ONNX Runtime version of the object segmentator:
```
cd ort && mkdir build && cd build
cmake ..
cmake --build . --config Release
```
To build the Torchscript version:
```
cd libtorch && mkdir build && cd build
cmake -DCMAKE_PREFIX_PATH=`python3 -c 'import torch;print(torch.utils.cmake_prefix_path)'`  ..
cmake --build . --config Release
```

## Results
The contrast is night and day. The Torchscript application is significantly slower than the ONNX Runtime application. On the left is the ONNX Runtime Framework running the LRSAPP semantic segmentation model with 18 FPS and on the right is the Torchscript framework deploying the same model with 6 FPS.
<p align="center">
  <img src="assets/onnx_model_example.png" title="ONNX Model Demo" width=224 height=224/>
  <img src="assets/torchscript_model_example.png" title="Torchscript Model Demo" width=224 height=224/>
</p>
