# ezrknn-llm
This repo tries to make RKNN LLM usage easier for people who don't want to read through Rockchip's docs.

Main repo is https://github.com/Pelochus/ezrknpu where you can find more instructions, documentation... for general use.
This repo is intended for details in RKLLM and also how to convert models.

## Requirements
Keep in mind this repo is focused for:
- High-end Rockchip SoCs, mainly the RK3588
- Linux, not Android
- Linux kernels from Rockchip (as of writing 5.10 and 6.1 from Rockchip should work, if your board has one of these it will very likely be Rockchip's kernel)

## Quick Install
First clone the repo:

```bash
git clone https://github.com/Pelochus/ezrknn-llm
```

Then run:

```bash
cd ezrknn-llm && bash install.sh
```

## Test
Run (assuming you are on the folder where your `.rkllm` file is located):

```bash
rkllm qwen-chat-1_8B.rkllm # Or any other model you like
```

## Converting LLMs for Rockchip's NPUs
### Docker
In order to do this, you need a Linux PC x86 (Intel or AMD). Currently, Rockchip does not provide ARM support for converting models, so can't be done on a Orange Pi or similar.
Run:

`docker run -it pelochus/ezrkllm-toolkit:latest bash`

Then, inside the Docker container:

```bash 
cd ezrknn-llm/rkllm-toolkit/examples/huggingface/
```

Now change the `test.py` with your preferred model. This container provides Qwen-1.8B since it is the best working one and very lightweight. 
Before converting the model, remember to run `git lfs pull` to download the model.
To convert the model, run:

`python3 test.py`

## Fixing hallucinating LLMs
Check this reddit post if you LLM seems to be responding garbage:

https://www.reddit.com/r/RockchipNPU/comments/1cpngku/rknnllm_v101_lets_talk_about_converting_and/

## Older versions
There are dedicated branch containing the latest commit done by this fork before updating to a newer release from Rockchip. They are also on the releases of this repo. To use the latest version, always use the main branch.

- **v1.0.0-beta**: https://github.com/Pelochus/ezrknn-llm/tree/v1.0.0

# Original README starts below

<hr>
<hr>
<hr>

# Description

  RKLLM software stack can help users to quickly deploy AI models to Rockchip chips. The overall framework is as follows:
    <center class="half">
        <div style="background-color:#ffffff;">
        <img src="res/framework.jpg" title="RKLLM"/>
    </center>

  In order to use RKNPU, users need to first run the RKLLM-Toolkit tool on the computer, convert the trained model into an RKLLM format model, and then inference on the development board using the RKLLM C API.

- RKLLM-Toolkit is a software development kit for users to perform model conversionand quantization on PC.

- RKLLM Runtime provides C/C++ programming interfaces for Rockchip NPU platform to help users deploy RKLLM models and accelerate the implementation of LLM applications.

- RKNPU kernel driver is responsible for interacting with NPU hardware. It has been open source and can be found in the Rockchip kernel code.

# Support Platform

- RK3588 Series
- RK3576 Series

# Support Models

- [x] [LLAMA models](https://huggingface.co/meta-llama) 
- [x] [TinyLLAMA models](https://huggingface.co/TinyLlama) 
- [x] [Qwen models](https://huggingface.co/models?search=Qwen/Qwen)
- [x] [Phi models](https://huggingface.co/models?search=microsoft/phi)
- [x] [ChatGLM3-6B](https://huggingface.co/THUDM/chatglm3-6b/tree/103caa40027ebfd8450289ca2f278eac4ff26405)
- [x] [Gemma models](https://huggingface.co/collections/google/gemma-2-release-667d6600fd5220e7b967f315)
- [x] [InternLM2 models](https://huggingface.co/collections/internlm/internlm2-65b0ce04970888799707893c)
- [x] [MiniCPM models](https://huggingface.co/collections/openbmb/minicpm-65d48bf958302b9fd25b698f)
- [x] [TeleChat models](https://huggingface.co/Tele-AI)
- [x] [Qwen2-VL](https://huggingface.co/Qwen/Qwen2-VL-2B-Instruct)
- [x] [MiniCPM-V](https://huggingface.co/openbmb/MiniCPM-V-2_6)
- [x] [DeepSeek-R1-Distill](https://huggingface.co/collections/deepseek-ai/deepseek-r1-678e1e131c0169c0bc89728d)

# Model Performance Benchmark

| llm model      | dtype      | seqlen | max_context | new_tokens | TTFT(ms) | Tokens/s | memory(G) | platform |
| :------------- | :--------- | :----: | :---------: | :--------: | :------: | :------: | :-------: | :------: |
| TinyLLAMA-1.1B | w4a16      |   64   |     320     |    256     |  345.00  |  21.10   |   0.77    |  RK3576  |
|                | w4a16_g128 |   64   |     320     |    256     |  410.00  |  18.50   |    0.8    |  RK3576  |
|                | w8a8       |   64   |     320     |    256     |  140.46  |  24.21   |   1.25    |  RK3588  |
|                | w8a8_g512  |   64   |     320     |    256     |  195.00  |  20.08   |   1.29    |  RK3588  |
| Qwen2-1.5B     | w4a16      |   64   |     320     |    256     |  512.00  |  14.40   |   1.75    |  RK3576  |
|                | w4a16_g128 |   64   |     320     |    256     |  550.00  |  12.75   |   1.76    |  RK3576  |
|                | w8a8       |   64   |     320     |    256     |  206.00  |  16.46   |   2.47    |  RK3588  |
|                | w8a8_g128  |   64   |     320     |    256     |  725.00  |   7.00   |   2.65    |  RK3588  |
| Phi-3-3.8B     | w4a16      |   64   |     320     |    256     |  975.00  |   6.60   |   2.16    |  RK3576  |
|                | w4a16_g128 |   64   |     320     |    256     | 1180.00  |   5.85   |   2.23    |  RK3576  |
|                | w8a8       |   64   |     320     |    256     |  516.00  |   7.44   |   3.88    |  RK3588  |
|                | w8a8_g512  |   64   |     320     |    256     |  610.00  |   6.13   |   3.95    |  RK3588  |
| ChatGLM3-6B    | w4a16      |   64   |     320     |    256     | 1168.00  |   4.62   |   3.86    |  RK3576  |
|                | w4a16_g128 |   64   |     320     |    256     | 1582.56  |   3.82   |   3.96    |  RK3576  |
|                | w8a8       |   64   |     320     |    256     |  800.00  |   4.95   |   6.69    |  RK3588  |
|                | w8a8_g128  |   64   |     320     |    256     | 2190.00  |   2.70   |   7.18    |  RK3588  |
| Gemma2-2B      | w4a16      |   64   |     320     |    256     |  628.00  |   8.00   |   3.63    |  RK3576  |
|                | w4a16_g128 |   64   |     320     |    256     |  776.20  |   7.40   |   3.63    |  RK3576  |
|                | w8a8       |   64   |     320     |    256     |  342.29  |   9.67   |   4.84    |  RK3588  |
|                | w8a8_g128  |   64   |     320     |    256     | 1055.00  |   5.49   |   5.14    |  RK3588  |
| InternLM2-1.8B | w4a16      |   64   |     320     |    256     |  475.00  |  13.30   |   1.59    |  RK3576  |
|                | w4a16_g128 |   64   |     320     |    256     |  572.00  |  11.95   |   1.62    |  RK3576  |
|                | w8a8       |   64   |     320     |    256     |  205.97  |  15.66   |   2.38    |  RK3588  |
|                | w8a8_g512  |   64   |     320     |    256     |  298.00  |  12.66   |   2.45    |  RK3588  |
| MiniCPM3-4B    | w4a16      |   64   |     320     |    256     | 1397.00  |   4.80   |    2.7    |  RK3576  |
|                | w4a16_g128 |   64   |     320     |    256     | 1645.00  |   4.39   |    2.8    |  RK3576  |
|                | w8a8       |   64   |     320     |    256     |  702.18  |   6.15   |   4.65    |  RK3588  |
|                | w8a8_g128  |   64   |     320     |    256     | 1691.00  |   3.42   |   5.06    |  RK3588  |
| llama3-8B      | w4a16      |   64   |     320     |    256     | 1607.98  |   3.60   |   5.63    |  RK3576  |
|                | w4a16_g128 |   64   |     320     |    256     | 2010.00  |   3.00   |   5.76    |  RK3576  |
|                | w8a8       |   64   |     320     |    256     | 1128.00  |   3.79   |   9.21    |  RK3588  |
|                | w8a8_g512  |   64   |     320     |    256     | 1281.35  |   3.05   |   9.45    |  RK3588  |

| multimodal model | image input size | vision model dtype | vision infer time(s) | vision memory(MB) | llm model dtype | seqlen | max_context | new_tokens | TTFT(ms) | Tokens/s | llm memory(G) | platform |
|:-------------- |:---------- |:------:|:-----------:|:----------:|:--------:|:--------:|:---------:|:--------:|:---------:|:---------:|:---------:|:---------:|
| Qwen2-VL-2B | (1, 3, 392, 392) | fp16 | 3.55 | 1436.52 | w4a16 | 256 | 384 | 128 | 2094.17 | 13.23 | 1.75 | RK3576 |
|                              |    | fp16  | 3.28 | 1436.52 | w8a8 | 256 | 384 | 128 | 856.86 | 16.19 | 2.47 | RK3588 |
| MiniCPM-V-2_6 | (1, 3, 448, 448) | fp16 | 2.40 | 1031.30 | w4a16 | 128 | 256 | 128 | 2997.70 | 3.84 | 5.50 | RK3576 |
|                            |    | fp16  | 3.27 | 976.98 | w8a8 | 128 | 256 | 128 | 1720.60 | 4.13 | 8.88 | RK3588 |

- This performance data were collected based on the maximum CPU and NPU frequencies of each platform with version 1.1.0. 
- The script for setting the frequencies is located in the scripts directory.
- The vision model were tested based on all NPU core with rknn-toolkit2 version 2.2.0.

# Download

1. You can download the **latest package** from [RKLLM_SDK](https://console.zbox.filez.com/l/RJJDmB), fetch code: rkllm
2. You can download the **converted rkllm model**  from [rkllm_model_zoo](https://console.box.lenovo.com/l/l0tXb8), fetch code: rkllm

# Examples

1. Multimodel deployment demo:   [Qwen2-VL-2B_Demo](https://github.com/airockchip/rknn-llm/tree/main/examples/Qwen2-VL-2B_Demo)
2. API usage demo:  [DeepSeek-R1-Distill-Qwen-1.5B_Demo](https://github.com/airockchip/rknn-llm/tree/main/examples/DeepSeek-R1-Distill-Qwen-1.5B_Demo)
3. API server demo:  [rkllm_server_demo](https://github.com/airockchip/rknn-llm/tree/main/examples/rkllm_server_demo)

# Note

- The modifications in version 1.1 are significant, making it incompatible with older version models. Please use the latest toolchain for model conversion and inference.

- The supported Python versions are:
  
  - Python 3.8
  
  - Python 3.10

- Latest version: [ <u>v1.1.4](https://github.com/airockchip/rknn-llm/releases/tag/release-v1.1.4)</u>

# RKNN Toolkit2

If you want to deploy additional AI model, we have introduced a SDK called RKNN-Toolkit2. For details, please refer to:

https://github.com/airockchip/rknn-toolkit2

# CHANGELOG

## v1.1.0

- Support group-wise quantization (w4a16 group sizes of 32/64/128, w8a8 group sizes of 128/256/512).
- Support joint inference with LoRA model loading
- Support storage and preloading of prompt cache.
- Support gguf model conversion (currently only support q4_0 and fp16).
- Optimize initialization, prefill, and decode time.
- Support four input types: prompt, embedding, token, and multimodal.
- Add PC-based simulation accuracy testing and inference interface support for rkllm-toolkit.
- Add gdq algorithm to improve 4-bit quantization accuracy.
- Add mixed quantization algorithm, supporting a combination of grouped and non-grouped quantization based on specified ratios.
- Add support for models such as Llama3, Gemma2, and MiniCPM3.
- Resolve catastrophic forgetting issue when the number of tokens exceeds max_context.

for older version, please refer [CHANGELOG](CHANGELOG.md)
