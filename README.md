# FaceFormer

## 目录

  - [1. 简介](#1-简介)
  - [2. 特性](#2-特性)
  - [3. 运行环境准备](#3-运行环境准备)
  - [4. 准备模型](#4-准备模型)
  - [5. 例程测试](#5-例程测试)
  - [3. 程序性能测试](#6-程序性能测试)

## 1. 简介

Faceformer是一个基于Transformer的自回归模型，专门设计用于驱动3D面部动画的任务。这个模型通过处理语音数据来生成动画的3D面部网格序列，解决了传统方法在面部动画生成中存在的一些限制。关于它的特性，请前往源repo查看：[Faceformer仓库链接](https://github.com/EvelynFan/FaceFormer)。 本例程对FaceFormer进行移植，使之能在SOPHON BM1684X上进行推理测试。

该例程支持在V24.04.01(libsophon_0.5.1)及以上的SDK上运行，支持在插有1684X加速卡(SC7系列)的x86主机上运行，也可以在1684X SoC设备（如SE7、SM7、Airbox等）上运行。在SoC上运行需要额外进行环境配置，请参照[运行环境准备](#3-运行环境准备)完成环境部署。

## 2. 特性
* 支持BM1684X(x86 PCIe、SoC)
* 支持FP32模型编译和推理
* 支持基于SAIL推理的Python例程


## 3. 运行环境准备

## 4. 准备模型

该模型目前只支持在1684X上运行，已提供编译好的bmodel。

### 4.1 使用提供的模型

​本例程在`scripts`目录下提供了下载脚本`download.sh`

```bash
# 安装unzip，若已安装请跳过，非ubuntu系统视情况使用yum或其他方式安装
sudo apt install unzip
chmod -R +x scripts/
./scripts/download.sh
```

执行下载脚本后，当前目录下的文件如下：

```bash
├── docs
│   └── FaceFormer_Export_Guide.md    #FaceFormer onnx导出和bmodel编译指南
├── models
│   └── BM1684X                     #download.sh下载的bmodel
│       ├── glm4-9b_int4_1dev.bmodel
│       └── glm4-9b_int8_1dev.bmodel
├── python
│   ├── FaceFormer.py                 #FaceFormer python推理脚本
│   ├── README.md                   #python例程执行指南
│   ├── requirements.txt            #python例程的依赖模块
│   └── token_config                #download.sh下载的tokenizer
│       ├── tokenization_chatglm.py
│       ├── tokenizer_config.json
│       └── tokenizer.model
├── README.md                       #FaceFormer例程指南
├── scripts                         
│   ├── download.sh                 #下载脚本
│   └── gen_bmodel.sh               #模型编译脚本
└── tools
    ├── glm-4-9b-chat               #修改过的FaceFormer源码
    │   ├── config.json
    │   └── modeling_chatglm.py
    └── export_onnx.py              #FaceFormer导出onnx脚本。
```


### 4.2 自行编译模型

此部分请参考[FaceFormer模型导出与编译](./docs/FaceFormer_Export_Guide.md)

## 5. 例程测试

- [Python例程](./python/README.md)

## 6. 程序性能测试

这里的测试输入为："请使用C++写一段冒泡排序算法。"
|    测试平台   |     测试程序       |           测试模型             |first token latency(s)|token per second(tokens/s)| 
| -----------  | ---------------- | ---------------------------     | --------------------- | ----------------------- | 
| SE7-32       | FaceFormer.py      | FaceFormer-9b_int8.bmodel         |    1.181              |    4.471          | 
| SE7-32       | FaceFormer.py      | FaceFormer-9b_int4.bmodel         |    1.114              |    7.489          | 

> **测试说明**：  
> 1. 性能测试结果具有一定的波动性，建议多次测试取平均值；
> 2. SE7-32的主控处理器为8核 ARM A53 42320 DMIPS @2.3GHz，PCIe上的性能由于处理器的不同可能存在较大差异；
> 3. 这里使用的SDK版本是V24.04.01；
