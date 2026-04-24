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
| 🛠️ 推理框架 | TensorRT-LLM / Triton Server | llama.cpp (GGUF) |


> ⚠️ **注意**：Nano 加载模型速度较慢，首次加载需等待数分钟~数十分钟（应该是sd卡加载的问题）

---

## 📊 性能测试结果汇总

### 📖 格式说明

- 📦 **GGUF**: llama.cpp 使用的模型格式
- ⚙️ **Engine**: TensorRT-LLM 编译后的模型格式
- 📁 **SafeTensors**: HuggingFace 模型格式
- 🔢 **FP16/INT8/INT4**: 模型量化精度

| 模型 | 部署平台 | GPU/设备 | 框架 | 模型格式 | 量化 | 模型大小 | Token 速度 |
| --- | ------ | ---------------------------- | ------------- | ----------- | ------------ | ------- | ---------------- |
| **Meta-Llama-3-8B-Instruct** | 🖥️ 服务器 | RTX 3090 Ti | HuggingFace | SafeTensors | FP16 | 15.1 GB | 44.11 tokens/s |
| **Meta-Llama-3-8B-Instruct** | 🖥️ 服务器 | RTX 3090 Ti | TensorRT-LLM | Engine | INT8 | 8.5 GB | ~101 tokens/s |
| **Meta-Llama-3-8B-Instruct** | 🖥️ 服务器 | RTX 3090 Ti | Triton Server | Engine | INT8 | 8.5 GB | ~100.8 tokens/s |
| <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ 服务器 | RTX 3090 Ti | HuggingFace | SafeTensors | FP16 | 14.0 GB | 46.45 tokens/s |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ 服务器 | RTX 3090 Ti | TensorRT-LLM | Engine | INT8 | 8.2 GB | ~106 tokens/s |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ 服务器 | RTX 3090 Ti | llama.cpp | GGUF | INT8 (Q8_0) | 8.1 GB | ~106 tokens/s |
| **DeepSeek-R1-Distill-Qwen-7B** | 🖥️ 服务器 | Intel Xeon W-3323 | llama.cpp | GGUF | INT8 (Q8_0) | 8.1 GB | ~12 tokens/s |
| <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> | <br /> |
| **DeepSeek-R1-Distill-Qwen-7B** | 📱 边缘端 | Orin Nano | llama.cpp | GGUF | Q4_K_M | 4.7 GB | ~2.2 tokens/s |
| **DeepSeek-R1-Distill-Qwen-1.5B** | 📱 边缘端 | Orin Nano | llama.cpp | GGUF | INT4 | 1.1 GB | ~20 tokens/s |
| **Qwen2-VL-2B-Instruct** | 📱 边缘端 | Orin Nano | llama.cpp | GGUF | INT4 | 1.0 GB | ~11 tokens/s |

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

### 🎯 性能差距 ~48 倍的原因

1. ⚡ **算力差距**: RTX 3090 Ti 算力是 Orin Nano 的 32 倍
2. 📊 **显存带宽**: RTX 3090 Ti 带宽 936 GB/s vs Orin Nano 102.4 GB/s (约9倍)
3. 🔢 **量化精度**: 服务器用 INT8，边缘端用 INT4
4. 🛠️ **框架优化**: TensorRT-LLM 深度优化 vs llama.cpp 侧重兼容性

---

## 🎯 关键发现

| 发现 | 描述 |
| --- | --- |
| 🖥️ **服务器端** | TensorRT-LLM + INT8 量化提速约 **2.3 倍** |
| 📱 **边缘端** | INT4 量化显著降低显存占用，适合嵌入式设备 |
| 🔄 **服务器 vs 边缘端** | 算力差距约 32 倍，导致性能差距约 48 倍 |
| ⚡ **边缘端** | 小模型 Qwen-1.5B 比 7B 快约 **9 倍** |
| 📦 **模型压缩** | 边缘端 INT4 量化可节省 63-68% 存储空间 |
| 🔄 **Triton Server** | 与直接运行性能基本一致 |
| 🚀 **优化技术** | 启用 Paged KV Cache 和 Context FMHA 显著提升性能 |

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
