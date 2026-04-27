# 🚀 TensorRT-LLM 性能测试结果

## 📋 测试环境

### 硬件配置对比

| 项目 | 🖥️ 服务器端 | 📱 边缘端 |
| --- | --- | --- |
| 🔧 设备 | RTX 3090 Ti | Jetson Orin Nano |
| 💾 显存 | 24 GB GDDR6X | 共享 8GB |
| ⚡ 算力 (FP16) | 320 TFLOPS | ~10 TFLOPS (等效) |
| ⚡ 算力 (INT8) | 600 TOPS | 20 TOPS (稠密) / 40 TOPS (稀疏) |
| 📊 显存带宽 | 936 GB/s | 102.4 GB/s |
| 🧠 CPU | Intel Xeon W-3323 (12核/24线程) | 6核 Cortex-A78AE @ 1.7GHz |
| 💾 存储 | - | SD卡 (UHS-I) |
| 🔧 CUDA | 12.1 / 12.2 | - |
| 🛠️ 推理框架 | TensorRT-LLM / llama.cpp | llama.cpp (GGUF) |


> ⚠️ **注意**：Nano 加载模型速度较慢，首次加载需等待数分钟~数十分钟（应该是sd卡加载的问题）

---

## 📊 性能测试结果汇总

### 📖 格式说明

- 🖥️ **CPU**: Intel Xeon W-3323 (12核/24线程)
- 🟢 **GPU**: NVIDIA RTX 3090 Ti
- 📱 **边缘端**: Jetson Orin Nano
- 📦 **GGUF**: llama.cpp 使用的模型格式
- ⚙️ **Engine**: TensorRT-LLM 编译后的模型格式
- 📁 **SafeTensors**: HuggingFace 模型格式
- 🔢 **FP16/INT8/INT4/Q4_K_M**: 模型量化精度

| 模型 | 部署平台 | GPU/设备 | 框架 | 模型格式 | 量化 | 模型大小 | Token 速度 (tokens/s) |
| --- | ------ | ---------------------------- | ------------- | ----------- | ------------ | ------- | ---------------- |
| **Meta-Llama-3-8B-Instruct** | 🟢 | RTX 3090 Ti | HuggingFace | SafeTensors | FP16 | 15.1 GB | 44.11 |
| **Meta-Llama-3-8B-Instruct** | 🟢 | RTX 3090 Ti | TensorRT-LLM | Engine | INT8 | 8.5 GB | ~101 |
| **Meta-Llama-3-8B-Instruct** | 🟢 | RTX 3090 Ti | Triton Server | Engine | INT8 | 8.5 GB | ~100.8 |
| - |- |- |- |- |-| - |-|
| **DeepSeek-R1-Distill-Qwen-7B** | 🟢 | RTX 3090 Ti | HuggingFace | SafeTensors | FP16 | 14.0 GB | 46.45 |
| **DeepSeek-R1-Distill-Qwen-7B** | 🟢 | RTX 3090 Ti | TensorRT-LLM | Engine | INT8 | 8.2 GB | ~106 |
| **DeepSeek-R1-Distill-Qwen-7B** | 🟢 | RTX 3090 Ti | llama.cpp | GGUF | INT8 (Q8_0) | 8.1 GB | ~106 |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ | Intel Xeon W-3323 | llama.cpp | GGUF | INT8 (Q8_0) | 8.1 GB | ~12 |
| **DeepSeek-R1-Distill-Qwen-7B** | 🟢 | RTX 3090 Ti | llama.cpp | GGUF | Q4_K_M | 4.7 GB | ~158 |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ | Intel Xeon W-3323 | llama.cpp | GGUF | Q4_K_M | 4.7 GB | ~17 |
| - |- |- |- |- |-| - |-|
| **DeepSeek-R1-Distill-Qwen-7B** | 📱 | Orin Nano | llama.cpp | GGUF | Q4_K_M | 4.7 GB | ~2.2 |
| **DeepSeek-R1-Distill-Qwen-1.5B** | 📱 | Orin Nano | llama.cpp | GGUF | INT4 | 1.1 GB | ~20 |
| **Qwen2-VL-2B-Instruct** | 📱 | Orin Nano | llama.cpp | GGUF | INT4 | 1.0 GB | ~11 |

---

## 📈 性能差距分析

### 💻 硬件算力对比

| GPU | 算力 (FP16) | 算力 (INT8) | 显存带宽 | 显存 |
| --- | ----------- | ----------- | ------- | ---- |
| 🟢 RTX 3090 Ti | ~320 TFLOPS | ~600 TOPS | 936 GB/s | 24 GB |
| 📱 Orin Nano | ~10 TFLOPS (等效) | 20 TOPS (稠密) / 40 TOPS (稀疏) | 102.4 GB/s | 共享 8GB |

### 📝 单位说明

- 💨 **TFLOPS**: Tera Floating Point Operations Per Second，每秒万亿次浮点运算
- 🔢 **TOPS**: Tera Operations Per Second，每秒万亿次整数运算



