# Compositor — Intel / macOS 14 Compatibility Fork

这是 [coyote-cynthia/Compositor](https://github.com/coyote-cynthia/Compositor) 维护的实验性兼容分支，基于 [robbietilton/Compositor](https://github.com/robbietilton/Compositor) 的开源图像编辑器。原作者及贡献者保留原有署名，项目继续遵循 MIT 许可证。

本分支保留 Swift、SwiftUI、AppKit 和 Metal 架构，目标是让 Intel Mac（x86_64）上的 macOS 14 使用 Compositor。它不是上游官方发行版，也不是跨平台重写。

## 下载与安装

请从 **[本仓库 Releases](https://github.com/coyote-cynthia/Compositor/releases)** 下载 Intel 兼容包。首个版本为 `v1.5.4-intel-macos14.1`，标记为 **Pre-release**。

- `Compositor-Intel-macOS14.dmg`：推荐安装包，打开后把应用拖入 Applications。
- `Compositor-Intel-macOS14.zip`：应用压缩包。
- `SHA256SUMS.txt`：下载完整性校验。在三个文件所在目录执行 `shasum -a 256 -c SHA256SUMS.txt`。

安装包采用临时签名（ad-hoc），没有 Apple Developer ID 签名或公证，macOS 可能拦截首次打开。确认下载来源后可通过系统“隐私与安全性”中的允许打开选项处理。

上游 Homebrew 配方和上游 Releases 不提供本分支的 Intel 兼容包。本包已禁用上游自动更新，请从本仓库手动下载后续版本。

## 当前验证状态

- 应用版本：1.5.4，构建号 50；兼容发行标签独立于应用内版本。
- 源码分支：`codex/intel-macos14`。
- 目标：Intel x86_64，最低 macOS 14.0。
- [提交 af58986 的 Release 编译与打包](https://github.com/coyote-cynthia/Compositor/actions/runs/39711588199)成功，生成 DMG 和 ZIP。
- 下载后的 SHA-256 和 DMG 镜像完整性验证通过。
- [Verify 工作流](https://github.com/coyote-cynthia/Compositor/actions/runs/39711588573)未通过：`DeepLayerTests.swift` 有类型检查超时，截图测试报告应用未加载 accessibility。
- 尚未完成 macOS 14.1 实机启动、编辑、保存重开与导出验收；构建成功不代表全部功能已验证。

## 兼容改动与限制

- 替换或保护 macOS 15/26 专用 UI API，保留普通工具栏和旧系统可用的缩放光标。
- Metal 缓冲区使用 macOS 14 可用的 `OSAllocatedUnfairLock`。
- 修复 PDF 导入比例和 Camera Raw 阴影系数的类型检查问题。
- macOS 26 以下禁用 RAW Highlight Recovery，其他 RAW 功能仍需实机验证。
- 首次窗口使用默认尺寸；部分新版工具栏外观、斜向/旋转缩放光标与上游不同。

以下为继承自上游的功能说明，不表示每项都已在本兼容版本验收。

## Features

### Layers
- Layers and folders, with opacity and Photoshop's full set of blend modes in its order — a folder's opacity dims everything inside it
- Layer masks: paint, fill, invert, blur and feather them anywhere on the canvas, past the layer's own pixels; link or unlink them to transform a mask on its own
- Clipping masks and folder masks
- Adjustment layers: Hue/Saturation, Levels, Curves, Exposure, Gradient Map, Grain, Black & White, Color Balance, Invert, Gaussian Blur, Motion Blur and Noise
- Layer effects: Stroke, Drop Shadow, Color Overlay, Inner Shadow, Outer Glow and Inner Glow, rendered on the GPU and editable at any time
- Merge Down, Merge Layers and Merge Group (⌘E)
- Duplicate, rename inline, reorder and nest by drag and drop; Option-drag to duplicate; a right-click menu in the Layers panel
- Copy and paste whole layers and folders (⌘C/⌘V with no selection), within a project or between projects, or drag them between projects

### Transform
- Non-destructive move, scale, rotate and flip — images keep their full resolution however small you make them
- Free distort (⌘-drag a handle), with Shift to lock to an axis
- Transform several layers, or a whole folder, together
- Snapping to canvas and layer edges and centers, with guides
- Exact values for position, size, scale and angle, stepped with the arrow keys
- Flip Layer and Flip Canvas, horizontal and vertical

### Selections
- Rectangle and Ellipse Marquee, Freehand and Polygonal Lasso, and the Magic tool — Wand selects by color, Object traces whatever you click (Tab switches)
- Select Subject, and Expand, Contract and Feather on any selection
- Add to and subtract from selections, move the outline, or move and duplicate the pixels inside
- Load a layer's pixels or a mask as a selection
- Content-Aware Fill, which can also extend an image past its edges

### Painting and retouching
- Brush with size, hardness, opacity and smoothing, in Paint or Erase mode (B and E), and Shift for straight lines
- Pen pressure for the Brush's size and opacity, on a graphics tablet
- Spot Healing Brush (content-aware)
- Clone Stamp, aligned or not, sampling one layer or all of them
- Blur tool, on pixels or masks
- Gradient tool and Shape tool (rectangles, rounded rectangles, ellipses and lines), which stay editable rather than being rasterized
- Type tool (T): inline multiline editing in draggable, resizable paragraph boxes; font, size, color, alignment and spacing in the tool header; transform text and use it as a clipping mask
- Eyedropper and a full color picker

### Adjustments and filters
- Camera Raw filter: light, color, curves, color mixer, color grading, detail, optics and geometry, in a panel beside the canvas; each section resets to its defaults from its header
- Levels (with Auto), Curves, Hue/Saturation, Exposure, Gradient Map, Grain, Black & White, Color Balance and Invert
- Gaussian Blur and Motion Blur that spread past a layer's edges
- Add Noise, Vignette, Bloom / Glow, Dither, Scanlines, Tonal Contrast, Lens Correction and Remove Background
- Live previews, limited to the selection when there is one
- Last Filter (⌃⌘F) runs the last filter again with the same settings

### Canvas and files
- Multiple projects in tabs
- Search Commands (⌘F): find every menu command and tool by name, as in Raycast or Obsidian, and run it with Return
- Toggle Fullscreen (F): the canvas alone on black over the whole screen, with every panel put away; F or Esc brings them back
- Rulers (⌘R), guides dragged from them, a layout grid with adjustable spacing and subdivisions, and Snap To for guides, grid, layers and document bounds
- Crop with snapping, ratios including 3:4 and 9:16, and Option for symmetric cropping; with a selection, the crop starts at it
- Canvas Size, Image Size and Trim
- A Navigator minimap in the canvas's corner from 300% zoom: the whole document in small with a box around the view; click or drag to move there (View › Navigator)
- Sharp high-quality downsampling when zoomed out, and a pixel grid when zoomed in
- Import JPEG, PNG, HEIC, TIFF, WebP, SVG, PDF (every page its own layer, at 300 DPI or fitted to the canvas), camera RAW (with a develop step first) and Photoshop PSD and PSB (8-bit RGB; not CMYK). Photoshop folders, masks, blend modes, fill rectangles/ellipses, and simple horizontal text stay editable; other vectors and vertical text become pixels. A conversion report is shown before anything is applied.
- Large documents: the memory budget scales with your Mac, and a Photoshop file too big to open has its layers cropped to the canvas instead
- Export PNG (⇧⌘E), Export JPEG (⇧⌥⌘S), and Export As (⇧⌥⌘W) for PNG, JPEG or a one-page PDF at the print size, scaled if you like, with a live preview, JPEG quality and file size; Copy Merged
- Keep working while a project saves
- Photoshop-style keyboard shortcuts throughout, remappable in Edit > Keyboard Shortcuts
- Drag a number's label to scrub its value, as in Photoshop
- This fork disables upstream automatic updates; compatibility packages use ad-hoc signing and are not notarized

### Works with AI agents
- AI agents and scripts can build and edit projects directly: a `.comp` is a folder of PNG layers and a manifest, and an open project updates live as it's written. See [Writing Compositor projects](docs/writing-comp-files.md)

## 构建

本分支的 CI 使用 `macos-15-intel` 和 Xcode 26.1.1，目标系统设为 macOS 14.0。构建机版本与最低运行系统版本是两个独立设置；不要仅为支持 macOS 14 而降低 Swift 编译器版本。

```sh
xcodebuild build -project Compositor.xcodeproj -scheme Compositor \
  -configuration Release -destination 'generic/platform=macOS' \
  -derivedDataPath build -onlyUsePackageVersionsFromResolvedFile \
  ARCHS=x86_64 ONLY_ACTIVE_ARCH=NO MACOSX_DEPLOYMENT_TARGET=14.0 \
  CODE_SIGNING_ALLOWED=NO DEVELOPMENT_TEAM=
```

完整打包步骤见 [.github/workflows/build-intel.yml](.github/workflows/build-intel.yml)。该工作流生成 DMG、ZIP 和 SHA-256 文件。测试工作流使用相同 Intel 运行器和 Xcode 版本。

## 发布

兼容版本发布至本仓库，使用独立标签并在实机验收前标为 Pre-release。发行说明应记录实际构建提交、Actions 链接、已知问题及产物校验值。文档可以晚于二进制构建提交，必须明确区分。

首个发行说明见 [docs/releases/v1.5.4-intel-macos14.1.md](docs/releases/v1.5.4-intel-macos14.1.md)。

`scripts/release.sh` 是上游的 Developer ID 签名、公证发布流程；本分支目前使用 Actions 的个人测试包流程。

## 语言与许可证

应用界面目前为英文，本文档提供中文说明。

MIT — 见 [LICENSE](LICENSE)。感谢 [robbietilton/Compositor](https://github.com/robbietilton/Compositor) 原作者和所有贡献者。
