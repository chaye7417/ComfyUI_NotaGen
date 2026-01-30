# ComfyUI NotaGen - AI 乐谱生成节点

> **NotaGen** 是一个 AI 符号音乐生成工具，可以生成巴洛克、古典、浪漫时期风格的乐谱，支持 60+ 位作曲家风格。

**原项目：** [ElectricAlexis/NotaGen](https://github.com/ElectricAlexis/NotaGen)

https://github.com/user-attachments/assets/0671657f-e66b-4000-a0aa-48520f15b782

![界面截图](https://github.com/billwuhao/ComfyUI_NotaGen/blob/master/images/2025-03-10_06-24-03.png)

---

## 📣 更新日志

**[2025-04-09]** 不再需要手动输入 MuseScore4 路径，只需将安装目录添加到系统 PATH 环境变量即可。

**[2025-03-21]** 增加更多可调参数，可选是否卸载模型。

**[2025-03-15]** 支持 Linux Ubuntu/Debian 系列及服务器环境。

**[2025-03-13]** 生成 `.abc` 自动转换为 `.xml`、`.mp3`、`.png` 格式，可以听音乐并查看乐谱。

---

## 安装指南

### macOS 用户

请参阅 **[INSTALL-MacOS.md](INSTALL-MacOS.md)** - 完整的 macOS 安装步骤（推荐）

### Windows / Linux 用户

```bash
cd ComfyUI/custom_nodes
git clone https://github.com/chaye7417/ComfyUI_NotaGen.git
cd ComfyUI_NotaGen
pip install -r requirements.txt
```

**Windows python_embeded 环境：**
```bash
./python_embeded/python.exe -m pip install -r requirements.txt
```

**Linux 需要额外安装：**
```bash
sudo apt update
sudo apt install musescore
sudo apt install libxcb-xinerama0 libxcb-icccm4 libxcb-image0 libxcb-keysyms1 libxcb-render-util0 libxcb-xkb1 libxkbcommon-x11-0

# 服务器环境还需要安装虚拟显示器
sudo apt install xvfb
```

---

## 模型下载

将模型下载到 `ComfyUI/models/TTS/NotaGen/` 目录，并按要求重命名：

| 模型 | 大小 | 下载链接 |
|------|------|----------|
| **notagenx.pth**（推荐） | 6.2GB | [下载](https://huggingface.co/ElectricAlexis/NotaGen/blob/main/weights_notagenx_p_size_16_p_length_1024_p_layers_20_h_size_1280.pth) |
| notagen_small.pth | 110MB | [下载](https://huggingface.co/ElectricAlexis/NotaGen/blob/main/weights_notagen_pretrain_p_size_16_p_length_2048_p_layers_12_c_layers_3_h_size_768_lr_0.0002_batch_8.pth) |
| notagen_medium.pth | 244MB | [下载](https://huggingface.co/ElectricAlexis/NotaGen/blob/main/weights_notagen_pretrain_p_size_16_p_length_2048_p_layers_16_c_layers_3_h_size_1024_lr_0.0001_batch_4.pth) |
| notagen_large.pth | 516MB | [下载](https://huggingface.co/ElectricAlexis/NotaGen/blob/main/weights_notagen_pretrain_p_size_16_p_length_1024_p_layers_20_c_layers_6_h_size_1280_lr_0.0001_batch_4.pth) |

https://github.com/user-attachments/assets/229139bd-1065-4539-bcfa-b0c245259f6d

---

## 使用方法

### 1. 添加节点

在 ComfyUI 中：**右键** → **Add Node** → **🎤MW** → **MW-NotaGen** → **NotaGen Run**

### 2. 连接输出

```
NotaGen Run
    ├── audio → PreviewAudio (播放音频)
    └── score → PreviewImage (显示乐谱)
```

### 3. 参数设置

| 参数 | 说明 | 默认值 |
|------|------|--------|
| model | 模型文件 | notagenx.pth |
| period | 时期 | Romantic |
| composer | 作曲家 | Bach, Johann Sebastian |
| instrumentation | 乐器类型 | Keyboard |
| custom_prompt | 自定义提示（格式: `时期 \| 作曲家 \| 乐器`） | - |
| temperature | 生成随机性（0-5，越高越随机） | 0.8 |
| top_k | Top-K 采样 | 50 |
| top_p | Top-P 采样 | 0.95 |
| seed | 随机种子（0=随机） | 0 |
| unload_model | 生成后卸载模型 | true |

---

## 可用风格

### 时期 (Period)
- **Baroque（巴洛克）**：Bach, Corelli, Handel, Scarlatti, Vivaldi
- **Classical（古典）**：Beethoven, Haydn, Mozart 等
- **Romantic（浪漫）**：Chopin, Liszt, Brahms, Debussy, Ravel, Tchaikovsky 等 40+ 位作曲家

### 乐器类型 (Instrumentation)
- **Chamber** - 室内乐
- **Choral** - 合唱
- **Keyboard** - 键盘乐器（钢琴等）
- **Orchestral** - 管弦乐
- **Vocal-Orchestral** - 声乐与管弦乐
- **Art Song** - 艺术歌曲

---

## 输出文件

生成完成后，文件保存在：
- `ComfyUI/output/notagen_interleaved/` - 处理后的文件（.abc, .xml, .mp3, .png）
- `ComfyUI/output/notagen_original/` - 原始生成文件

---

## 常见问题

详见 [INSTALL-MacOS.md](INSTALL-MacOS.md) 中的常见问题章节。

---

## 相关链接

- **NotaGen 原始项目：** https://github.com/ElectricAlexis/NotaGen
- **模型下载：** https://huggingface.co/ElectricAlexis/NotaGen
- **MuseScore 4：** https://musescore.org/zh-hans/download

---

## 鸣谢

- [NotaGen](https://github.com/ElectricAlexis/NotaGen) - 原始模型
- [billwuhao/ComfyUI_NotaGen](https://github.com/billwuhao/ComfyUI_NotaGen) - 原始 ComfyUI 节点
