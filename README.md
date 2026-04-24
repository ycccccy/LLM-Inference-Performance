# 🚀 TensorRT-LLM 性能测试结果

## 📋 测试环境

### 🖥️ 服务器端

| 项目 | 配置 |
| ---- | ------------------------- |
| 🟢 GPU | NVIDIA RTX 3090 Ti |
| 💾 GPU 显存 | 24 GB GDDR6X |
| ⚡ GPU 算力 (FP16) | 320 TFLOPS |
| 🔢 GPU 算力 (INT8) | 600 TOPS |
| 📊 显存带宽 | 936 GB/s |
| 🔧 CUDA | 12.1 / 12.2 |
| 🧠 CPU | Intel Xeon W-3323 |
| 📌 CPU 规格 | 12核 / 24线程 |
| 📅 测试日期 | 2026-04-24 |

### 📱 边缘端

| 项目 | 配置 |
| ---- | ------------------------ |
| 📱 设备 | Jetson Orin Nano |
| ⚡ GPU 算力 (INT8) | 40 TOPS (稀疏) / 20 TOPS (稠密) |
| 📊 显存带宽 | 102.4 GB/s |
| 🧠 CPU | 6核 Cortex-A78AE @ 1.7GHz |
| 💿 内存 | 共享 8GB |
| 💾 存储 | SD卡 (UHS-I) |
| 🛠️ 推理框架 | llama.cpp (GGUF) |

> ⚠️ **注意**：Nano 加载模型速度较慢，首次加载需等待数分钟~数十分钟（应该是sd卡加载的问题）

---

## 📊 性能测试结果汇总

### 📖 格式说明

- 📦 **GGUF**: llama.cpp 使用的模型格式
- ⚙️ **Engine**: TensorRT-LLM 编译后的模型格式
- 📁 **SafeTensors**: HuggingFace 模型格式
- 🔢 **FP16/INT8/INT4**: 模型量化精度

| 模型 | 部署平台 | GPU/设备 | 框架 | 模型格式 | 量化 | 模型大小 | Token 速度 | 500 tokens 耗时 |
| --- | ------ | ---------------------------- | ------------- | ----------- | ------------ | ------- | ---------------- | ------------- |
| **Meta-Llama-3-8B-Instruct** | 🖥️ 服务器 | RTX 3090 Ti | HuggingFace | SafeTensors | FP16 | 15.1 GB | 44.11 tokens/s | ~11.3 秒 |
| **Meta-Llama-3-8B-Instruct** | 🖥️ 服务器 | RTX 3090 Ti | TensorRT-LLM | Engine | INT8 | 8.5 GB | ~101 tokens/s | ~4.9 秒 |
| **Meta-Llama-3-8B-Instruct** | 🖥️ 服务器 | RTX 3090 Ti | Triton Server | Engine | INT8 | 8.5 GB | ~100.8 tokens/s | ~5.0 秒 |
| <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ 服务器 | RTX 3090 Ti | HuggingFace | SafeTensors | FP16 | 14.0 GB | 46.45 tokens/s | ~10.8 秒 |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ 服务器 | RTX 3090 Ti | TensorRT-LLM | Engine | INT8 | 8.2 GB | ~106 tokens/s | ~4.7 秒 |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ 服务器 | RTX 3090 Ti | llama.cpp | GGUF | INT8 (Q8_0) | 8.1 GB | ~106 tokens/s | ~4.8 秒 |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ 服务器 | Intel Xeon W-3323 | llama.cpp | GGUF | INT8 (Q8_0) | 8.1 GB | ~12 tokens/s | ~42 秒 |
| <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> |
| **DeepSeek-R1-Distill-Qwen-7B** | 📱 边缘端 | Orin Nano | llama.cpp | GGUF | Q4_K_M | 4.7 GB | ~2.2 tokens/s | ~227 秒 |
| **DeepSeek-R1-Distill-Qwen-1.5B** | 📱 边缘端 | Orin Nano | llama.cpp | GGUF | INT4 | 1.1 GB | ~20 tokens/s | ~25 秒 |
| **Qwen2-VL-2B-Instruct** | 📱 边缘端 | Orin Nano | llama.cpp | GGUF | INT4 | 1.0 GB | ~11 tokens/s | ~45 秒 |

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

### 📐 硬件差距

| 指标 | RTX 3090 Ti | Orin Nano | 差距 |
| --- | ----------- | ----------- | ------- |
| ⚡ 算力 | 320 TFLOPS | ~10 TFLOPS | **32 倍** |
| 📊 带宽 | 936 GB/s | 102.4 GB/s | **9 倍** |
| 💾 显存 | 24 GB | 8 GB (共享) | **3 倍** |


---

## 🛠️ 部署方式对比

| 方式 | 模型格式 | 适用场景 |
| --- | ------- | -------- |
| 🤗 HuggingFace 原生 | SafeTensors | 开发测试 |
| ⚡ TensorRT-LLM 直接运行 | Engine | 单用户推理、研发 |
| 🌐 Triton Server | Engine | 生产环境、多用户并发 |
| 🐐 llama.cpp (GGUF) | GGUF | 边缘端部署、嵌入式设备、离线推理 |

---

<div align="center">

### 📌 总结

| 场景 | 推荐方案 | 预期性能 |
| --- | -------- | -------- |
| 🏠 个人研发 | TensorRT-LLM | ~100 tokens/s |
| 🏢 生产环境 | Triton Server | ~100 tokens/s |
| 📱 边缘部署 | llama.cpp (INT4) | ~2-20 tokens/s |
| ☁️ CPU 备用 | llama.cpp (CPU) | ~10 tokens/s |

</div>
