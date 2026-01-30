# NotaGen ComfyUI 部署运行记录

**日期：** 2026-01-28
**环境：** macOS (Darwin 25.1.0), Apple Silicon, 64GB 统一内存

---

## 1. 项目位置

- **ComfyUI_NotaGen 插件：** `/Users/liujiaye/ComfyUI/custom_nodes/ComfyUI_NotaGen`
- **模型文件：** `/Users/liujiaye/ComfyUI/models/TTS/NotaGen/notagenx.pth` (6.2GB)
- **输出目录：**
  - `/Users/liujiaye/ComfyUI/output/notagen_original`
  - `/Users/liujiaye/ComfyUI/output/notagen_interleaved`

---

## 2. 环境配置步骤

### 2.1 创建虚拟环境
```bash
cd /Users/liujiaye/ComfyUI
uv venv .venv
source .venv/bin/activate
```

### 2.2 安装依赖
```bash
# PyTorch (CPU 版本，MPS 自动支持)
uv pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu

# ComfyUI 依赖
uv pip install -r requirements.txt

# NotaGen 依赖
uv pip install -r custom_nodes/ComfyUI_NotaGen/requirements.txt

# 音频解码支持（重要！）
uv pip install torchcodec
```

### 2.3 配置 MuseScore4
NotaGen 需要 MuseScore4 来将 ABC notation 转换为 MP3 和乐谱图片。

```bash
# 创建符号链接
mkdir -p ~/.local/bin
ln -sf "/Applications/MuseScore 4.app/Contents/MacOS/mscore" ~/.local/bin/MuseScore4

# 添加到 PATH（如果还没有）
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
```

---

## 3. 启动 ComfyUI

```bash
cd /Users/liujiaye/ComfyUI
source .venv/bin/activate
export PATH="$HOME/.local/bin:$PATH"
python main.py
```

或后台启动：
```bash
nohup python main.py > /tmp/comfyui.log 2>&1 &
```

访问地址：http://127.0.0.1:8188

---

## 4. 工作流配置

### 4.1 工作流文件位置
`/Users/liujiaye/ComfyUI/user/default/workflows/NotaGen_Workflow.json`

### 4.2 节点结构
```
NotaGen Run (生成音乐)
    ├── audio → PreviewAudio (播放音频)
    └── score → PreviewImage (显示乐谱)
```

### 4.3 参数说明
| 参数 | 说明 | 默认值 |
|------|------|--------|
| model | 模型文件 | notagenx.pth |
| period | 时期 | Romantic |
| composer | 作曲家 | Chopin, Frederic |
| instrumentation | 乐器类型 | Keyboard |
| custom_prompt | 自定义提示 | 格式: `时期 \| 作曲家 \| 乐器` |
| temperature | 生成随机性 | 0.8 |
| top_k | Top-K 采样 | 50 |
| top_p | Top-P 采样 | 0.95 |
| seed | 随机种子 | 0 (随机) |
| unload_model | 生成后卸载模型 | true |

---

## 5. 遇到的问题与解决

### 5.1 ShowText 节点缺失
**错误：** `该工作流包含您未安装的自定义节点 ShowText|pysssss`

**解决：** 从工作流中移除该节点，它只是显示消息，不影响核心功能。

### 5.2 torchcodec 缺失
**错误：** `TorchCodec is required for load_with_torchcodec`

**解决：**
```bash
source .venv/bin/activate
uv pip install torchcodec
# 重启 ComfyUI
```

### 5.3 界面无反应
**现象：** 点击运行后界面没有显示进度

**原因：** NotaGen 生成需要 1-2 分钟，界面可能没有实时更新

**解决：**
- 刷新浏览器页面 (Cmd+R)
- 检查节点边框是否变绿（表示正在执行）
- 查看日志：`tail -f /tmp/comfyui.log`

---

## 6. 生成结果示例

**首次成功生成：**
- 风格：Romantic | Chopin, Frederic | Keyboard
- 时长：约 101 秒
- 文件：
  - `/Users/liujiaye/ComfyUI/output/notagen_interleaved/Romantic-Chopin-Frederic-Keyboard_20260128-203200_101.mp3`
  - `/Users/liujiaye/ComfyUI/output/notagen_interleaved/Romantic-Chopin-Frederic-Keyboard_20260128-203200_101.xml`
  - `/Users/liujiaye/ComfyUI/output/notagen_interleaved/Romantic-Chopin-Frederic-Keyboard_20260128-203200_101-1.png` (乐谱第1页)
  - `/Users/liujiaye/ComfyUI/output/notagen_interleaved/Romantic-Chopin-Frederic-Keyboard_20260128-203200_101-2.png` (乐谱第2页)
  - `/Users/liujiaye/ComfyUI/output/notagen_interleaved/Romantic-Chopin-Frederic-Keyboard_20260128-203200_101-3.png` (乐谱第3页)

---

## 7. 可用模型列表

| 模型文件 | 参数量 | 说明 |
|----------|--------|------|
| notagenx.pth | 516M | 最新优化版本（已下载） |
| notagen_small.pth | 110M | 小模型 |
| notagen_medium.pth | 244M | 中等模型 |
| notagen_large.pth | 516M | 大模型 |

下载地址：https://huggingface.co/ElectricAlexis/NotaGen

---

## 8. 可用作曲家风格

### 巴洛克时期 (Baroque)
Bach, Corelli, Handel, Scarlatti, Vivaldi

### 古典时期 (Classical)
Beethoven, Haydn, Mozart, Paradis, Reichardt, Saint-Georges, Schroter

### 浪漫时期 (Romantic)
Chopin, Liszt, Brahms, Schumann, Debussy, Ravel, Tchaikovsky, Rachmaninoff 等 40+ 位作曲家

### 乐器类型 (Instrumentation)
Chamber, Choral, Keyboard, Orchestral, Vocal-Orchestral, Art Song

---

## 9. 常用命令速查

```bash
# 启动 ComfyUI
cd /Users/liujiaye/ComfyUI && source .venv/bin/activate && python main.py

# 查看日志
tail -f /tmp/comfyui.log

# 检查进程
ps aux | grep "python main.py"

# 停止 ComfyUI
pkill -f "python main.py"

# 播放最新生成的音乐
open /Users/liujiaye/ComfyUI/output/notagen_interleaved/*.mp3
```

---

## 10. 后续探索方向

1. **微调模型**：添加新的作曲家风格，参考 https://github.com/ElectricAlexis/NotaGen/issues/18
2. **条件生成**：修改模型支持旋律输入 → 配器输出
3. **原始仓库**：https://github.com/ElectricAlexis/NotaGen

---

*记录完成于 2026-01-28*
