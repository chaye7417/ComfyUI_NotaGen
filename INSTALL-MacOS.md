# ComfyUI NotaGen macOS 安装指南

> **NotaGen** 是一个 AI 音乐乐谱生成工具，可以生成巴洛克、古典、浪漫时期风格的乐谱，支持 60+ 位作曲家风格。

**模型来源：** [ElectricAlexis/NotaGen](https://github.com/ElectricAlexis/NotaGen)

---

## 系统要求

| 项目 | 要求 |
|------|------|
| 系统 | macOS 13+ (Apple Silicon 或 Intel) |
| Python | 3.10+ |
| 内存 | 16GB+（推荐 32GB+） |
| 存储 | 至少 10GB 可用空间（模型 6.2GB） |
| 依赖软件 | MuseScore 4（用于生成乐谱图片和音频） |

---

## 安装步骤

### 1. 安装 ComfyUI（如果还没有）

```bash
# 克隆 ComfyUI
cd ~/Documents  # 或你喜欢的目录
git clone https://github.com/comfyanonymous/ComfyUI.git
cd ComfyUI

# 创建虚拟环境（推荐使用 uv）
uv venv .venv
source .venv/bin/activate

# 安装 PyTorch（macOS 版本，MPS 自动支持）
uv pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu

# 安装 ComfyUI 依赖
uv pip install -r requirements.txt
```

### 2. 安装 ComfyUI_NotaGen 节点

```bash
cd ~/Documents/ComfyUI/custom_nodes
git clone https://github.com/chaye7417/ComfyUI_NotaGen.git
cd ComfyUI_NotaGen

# 激活 ComfyUI 的虚拟环境
source ~/Documents/ComfyUI/.venv/bin/activate

# 安装依赖
uv pip install -r requirements.txt

# 安装音频解码支持（重要！）
uv pip install torchcodec
```

### 3. 下载模型

从 HuggingFace 下载模型文件，放到 `ComfyUI/models/TTS/NotaGen/` 目录：

```bash
# 创建目录
mkdir -p ~/Documents/ComfyUI/models/TTS/NotaGen

# 下载模型（选择一个）
cd ~/Documents/ComfyUI/models/TTS/NotaGen

# 推荐：NotaGen-X（6.2GB，效果最好）
wget https://huggingface.co/ElectricAlexis/NotaGen/resolve/main/weights_notagenx_p_size_16_p_length_1024_p_layers_20_h_size_1280.pth -O notagenx.pth

# 或者小模型（110MB，速度快）
# wget https://huggingface.co/ElectricAlexis/NotaGen/resolve/main/weights_notagen_pretrain_p_size_16_p_length_2048_p_layers_12_c_layers_3_h_size_768_lr_0.0002_batch_8.pth -O notagen_small.pth
```

**可用模型：**

| 模型 | 大小 | 说明 |
|------|------|------|
| notagenx.pth | 6.2GB | 最新优化版，推荐 |
| notagen_small.pth | 110MB | 小模型，速度快 |
| notagen_medium.pth | 244MB | 中等模型 |
| notagen_large.pth | 516MB | 大模型 |

### 4. 安装 MuseScore 4（关键步骤）

NotaGen 需要 MuseScore 4 将 ABC notation 转换为 MP3 和乐谱图片。

**4.1 下载安装 MuseScore 4**

从官网下载：https://musescore.org/zh-hans/download

**4.2 创建命令行符号链接**

```bash
# 创建目录
mkdir -p ~/.local/bin

# 创建符号链接
ln -sf "/Applications/MuseScore 4.app/Contents/MacOS/mscore" ~/.local/bin/MuseScore4

# 添加到 PATH（如果还没有）
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# 验证安装
which MuseScore4
# 应该输出: /Users/你的用户名/.local/bin/MuseScore4
```

---

## 启动 ComfyUI

```bash
cd ~/Documents/ComfyUI
source .venv/bin/activate
export PATH="$HOME/.local/bin:$PATH"
python main.py
```

然后在浏览器访问：**http://127.0.0.1:8188**

---

## 使用方法

### 1. 加载工作流

在 ComfyUI 中，点击 **Load** → 选择 `user/default/workflows/NotaGen_Workflow.json`

或者手动添加节点：**右键** → **Add Node** → **🎤MW** → **MW-NotaGen** → **NotaGen Run**

### 2. 连接节点

```
NotaGen Run
    ├── audio → PreviewAudio (播放音频)
    └── score → PreviewImage (显示乐谱)
```

### 3. 设置参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| model | 模型文件 | notagenx.pth |
| period | 时期（Baroque/Classical/Romantic） | Romantic |
| composer | 作曲家 | Bach, Johann Sebastian |
| instrumentation | 乐器类型 | Keyboard |
| custom_prompt | 自定义提示（格式: `时期 \| 作曲家 \| 乐器`） | - |
| temperature | 生成随机性（越高越随机） | 0.8 |
| seed | 随机种子（0=随机） | 0 |
| unload_model | 生成后卸载模型（节省内存） | true |

### 4. 运行生成

点击 **Queue Prompt** 按钮，等待生成完成（约 1-2 分钟）。

**输出文件位置：**
- `ComfyUI/output/notagen_interleaved/` - 处理后的文件
- `ComfyUI/output/notagen_original/` - 原始生成文件

---

## 可用作曲家风格

### 巴洛克时期 (Baroque)
Bach, Corelli, Handel, Scarlatti, Vivaldi

### 古典时期 (Classical)
Beethoven, Haydn, Mozart, Paradis, Reichardt, Saint-Georges, Schroter

### 浪漫时期 (Romantic)
Chopin, Liszt, Brahms, Schumann, Debussy, Ravel, Tchaikovsky, Rachmaninoff, Scriabin, Mendelssohn, Schubert, Grieg, Dvorak, Sibelius, Prokofiev, Shostakovich 等 40+ 位作曲家

### 乐器类型
- **Chamber** - 室内乐
- **Choral** - 合唱
- **Keyboard** - 键盘乐器（钢琴等）
- **Orchestral** - 管弦乐
- **Vocal-Orchestral** - 声乐与管弦乐
- **Art Song** - 艺术歌曲

---

## 常见问题

### Q1: ShowText 节点缺失
**错误：** `该工作流包含您未安装的自定义节点 ShowText|pysssss`

**解决：** 从工作流中移除该节点，或安装 [ComfyUI-Custom-Scripts](https://github.com/pythongosssss/ComfyUI-Custom-Scripts)

### Q2: torchcodec 缺失
**错误：** `TorchCodec is required for load_with_torchcodec`

**解决：**
```bash
source .venv/bin/activate
uv pip install torchcodec
# 重启 ComfyUI
```

### Q3: 找不到 MuseScore4
**错误：** `MuseScore4 not found`

**解决：** 确保符号链接正确创建，并且 PATH 已更新：
```bash
ls -la ~/.local/bin/MuseScore4
export PATH="$HOME/.local/bin:$PATH"
```

### Q4: 生成过程中界面无反应
**原因：** NotaGen 生成需要 1-2 分钟

**解决：**
- 刷新浏览器页面 (Cmd+R)
- 检查节点边框是否变绿（表示正在执行）
- 查看终端日志

### Q5: 内存不足
**解决：**
- 使用较小的模型（notagen_small.pth）
- 确保 `unload_model` 设置为 true
- 关闭其他占用内存的应用

---

## 常用命令速查

```bash
# 启动 ComfyUI
cd ~/Documents/ComfyUI && source .venv/bin/activate && python main.py

# 后台启动
cd ~/Documents/ComfyUI && source .venv/bin/activate && nohup python main.py > /tmp/comfyui.log 2>&1 &

# 查看日志
tail -f /tmp/comfyui.log

# 停止 ComfyUI
pkill -f "python main.py"

# 播放最新生成的音乐
open ~/Documents/ComfyUI/output/notagen_interleaved/*.mp3
```

---

## 相关链接

- **ComfyUI_NotaGen：** https://github.com/chaye7417/ComfyUI_NotaGen
- **NotaGen 原始项目：** https://github.com/ElectricAlexis/NotaGen
- **模型下载：** https://huggingface.co/ElectricAlexis/NotaGen
- **MuseScore 4：** https://musescore.org/zh-hans/download

---

*文档整理于 2026-01-30*
