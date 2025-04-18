---
long_title: "任天堂娱乐系统（Nintendo Entertainment System）架构"
short_title: "任天堂娱乐系统（NES）架构"
name: NES
long_name: 任天堂娱乐系统 （Nintendo Entertainment System, NES）
subtitle: 不止 6502
date: 2019-01-25
release_date: 1983-07-15
generation: 3
cover: nes
top_tabs:
  Models:
    - 
      title: "国际版"
      file: international
      caption: "NES<br> 于 1985 年 10 月 18 日在美国发售，于 1986 年 9 月 1 日在欧洲发售."
      active: true
    - 
      title: "日版（Japanese）"
      file: japanese
      caption: "Famicom（FC）<br> 于 1983 年 7 月 15 日在日本发售."
  Motherboard:
    caption: "一种 NES 版本"
  Diagram: { }
#Historical
aliases:
  - /projects/consoles/nes
---

## 引言

初看上去，NES 只不过是一台带有精致外壳和手柄的 6502 兼容机。

*技术上*说，的确如此。但是我将展示给你看，为什么说 CPU 不是这个系统的 *核心* 部分。

## {.supporting-imagery}

## 型号和变种

![典型的Betamax录像机。 这种和类似的设备影响了 NES 的国际设计。 我在 2024 年 8 月访问英国剑桥的计算历史中心时看到的这个特别的例子。](betacord.webp)

对于同一款游戏主机，任天堂总是在全世界不同地区发行不同的变体 [@general-variants]，这就产生了很多看起来不一样，但是架构相同（组件可能不同）的主机。 简要起见，我只介绍两种最流行的版本：

- **家庭电脑**（又称*红白机*）是第一代产品，但只在日本发布。 这个玩具外观设计的主机配有两个不可拆卸的手柄（其中第二个手柄内置了麦克风）、一个前端插座用于连接光枪（称为*Zapper*）、一个射频视频输出口（使用NTSC-J信号），以及用于扩展音频功能的额外卡槽插针。
- **任天堂娱乐系统**（又称*NES*）是为北美、欧洲和大洋洲的西方观众重新设计的版本，其外观和机制与常见的VHS/Betamax播放器类似。 从技术上看，NES 的手柄可以插拔（没有麦克风），视频输出增加了 NTSC 或 PAL 制式的复合 RCA 接口，但是音频扩展引脚则被反盗版系统占用了。 作为补偿，外壳的底部安装有一个从未被使用的“扩展端口”，卡带可以从额外引脚与该端口通信 [@general-cartridge]。

因为“NES”这个名字伴随着作者一起长大，我将用它来泛指这台主机。当介绍某些只有日版机器才有的功能时，我会改用“Famicom”这个名字。

## 中央处理器 (CPU)

NES 的 CPU 型号为 **理光（Ricoh）2A03** [@cpu-cpu] ，基于当时流行的 8 位 CPU **MOS Technology 6502**，能以 **1.79 MHz** 的速度运行（或 PAL 系统上为 1.66 MHz）。

### 相关背景

70 年代末到 80 年代初的 CPU 市场可谓是百花齐放。

::: {.subfigures .side-by-side}

![搭载了6502 CPU 的 Commodore PET。](pet.webp) {.toleft}

![搭载了 Z80 CPU 的 Tandy TRS-80。](tandy.webp) {.toright}

由剑桥计算历史中心（英国）提供的 70 年代末计算机全景。

:::

如果某公司想制造一款便宜的微型电脑，它可以有以下选项:

- **Intel 8080**：一款流行的 CPU，应用于第一台"个人"电脑 *Altair*。 它有 8 位的数据总线和 16 位的地址总线。
- **Zilog Z80**：一款"非官方"版本的 8080 增强型处理器，较原版有更多的指令、寄存器和内部组件。 它售价更低且兼容 8080 程序 [@cpu-re_1977, p. 86]。 Amstrad 和 Sinclair（等公司）选择了这款CPU。
- **Motorola 6800**：另一款由摩托罗拉（Motorola）设计的 8 位 CPU，包含完全不同的指令集。 许多 DIY 电脑套件、合成器以及一体机使用了 6800。

如果这些选项还不够，市场上出现了一家名叫 **MOS** 的公司，他们基于 6800 设计了一款新的 CPU：**6502**。 虽然与其他 CPU 不兼容，但这款新的芯片造价 *非常*便宜 \[@cpu-summary_costs\] \[@cpu-re_1981, p. 76\]，那些标志性的电脑制造商（Commodore、Tandy、Apple、Atari、Acorn 等）选择 6502 来生产他们的机器只是时间问题。

说回日本，彼时的任天堂正需要一款廉价但是开发者熟悉的 CPU，于是他们相中了 6502。 **理光**，他们的 CPU 供应商，也成功地制造了一款兼容 6502 的 CPU。

#### 理光的许可之谜

关于理光 *如何* 设法克隆 6502，时至今日已无人能说清了。 有人认为 MOS 向理光授权了芯片设计，但此事存在诸多矛盾之处：

- 虽然理光和 MOS 的芯片都有相同的布局，但理光的版本包含被切断的总线（这禁用了某些功能）[@cpu-differences]。 后面我会详细介绍这一点。
- 没有找到明确说明 MOS 向理光授权 6502 的文档。
- Nikkei Trendy 在 2008 年发表的一篇文章中说，理光从罗克韦尔（Rockwell）获得了许可，罗克韦尔是被授权的芯片制造商 [@cpu-trendi]。 然而，第二方能否向第三方提供IP仍有争议，更不用说得到MOS的批准了。
- 这不是任天堂第一次逃避知识产权，在日本 *Ikegami Tsushinki 诉 Nintendo* 一案中任天堂就被裁定不拥有原版《咚奇刚》（Donkey Kong）的源代码 [@cpu-dk]。

#### 被阉割的功能

理光 2A03 阉割了原先包含在 6502 中的 **Binary-Coded Decimal**  （BCD）模式 [@cpu-visualbcd]。 BCD 将每个十进制数字编码为独立的 4 位二进制数。 6502 的字（word）大小为 8 bits（8 个二进制位）——这意味着每个字可以存储两个十进制数。

举个例子，十进制数字 `42` 可以表示为：

- `0010 1010` （二进制）
- `0100 0010` （BCD 码）

我还可以举更多的例子，但是只需记住一个概念：BCD 对那些需要分别处理每个十进制数的应用很有用（例如，数字时钟）（译注：使用汇编表示数值字面量时，BCD 更好用）。 但是，使用BCD 编码需要更多存储空间：每个 8-bit 字最大只能表示到十进制数的 `99`。而传统二进制编码中，同样大小的字最大能表示到 `255`。

无论如何，理光故意切断了激活 BCD 模式的控制线，破坏了 BCD 模式。 这样做可能是为了避免向 MOS 支付版税，因为 BCD 是 MOS 的专利（美国针对集成电路布局的版权法直到 1984 年才颁布 [@cpu-protection_act]）。

### 内存

理光 2A03 和 MOS 6502 都有 **8 位数据总线** 和 **16 位地址总线**，这使得它们能访问最大 **64 KB 的内存**。 那么，任天堂是如何填充地址空间的？

在一方面，主板上包含一块提供了**2 KB 的静态 RAM**(SRAM) [@cpu-sample_ram] 的芯片。 任天堂称这个区域为“工作内存”（Work RAM，WRAM），它可以用来存储：

- 用来处理游戏状态或查找信息的变量。
- 当 CPU 执行子例程时，用来临时保存寄存器值的“栈（Stack）”。
- 作为“缓冲区”，CPU 可以利用它在两个位置之间复制大量数据。

其二，系统组件进行了**内存映射（memory-mapped）** [@cpu-cpu_map] ，这意味着需要使用内存地址来访问它们，这也占用了一部分 CPU 的地址空间。 因此 CPU 的内存空间包含了游戏卡带、WRAM、PPU、APU 和两个手柄 （不用害怕这些名词，因为它们在这篇文章都有解释）。

#### 卡带 / 游戏数据

以防万一你不知道，NES 游戏以卡带（cartridge）的形式分发，而卡带总线又直接连接到 CPU。

任天堂连接卡带的方式使得 CPU 只能访问 49120 字节（约 49.97 KB）的卡带数据（cartridge data） [@cpu-cpu_map] 。 那么，“卡带数据”是什么意思？ 呃，任何连接到这些总线的芯片都算，例如：

- 游戏程序所在的 **程序 ROM（Program ROM）**。 其中不包含任何图形数据，稍后你会在“图形”部分看到。 自然，不像其它芯片，这个是芯片是必须要有的。
- 用于扩展 WRAM 的 **RAM 芯片**。
- 用于保存存档的**内含电池的 RAM 芯片**

之所以有这么多不同的组合，是因为 CPU 不关心正在读取的组件类型，它只关心内存位置。 所以游戏开发商可以选择（或者设计出）一种适合他们游戏的可行布局。

![《超级马力欧兄弟》[@photography-nrom] 的PCB。](nrom.png){.tabs-nested .active title="原始的"}

![带有重要部件标签的相同 PCB。 “锁定”芯片的含义会在“反盗版”部分解释。](nrom_marked.png){.tabs-nested-last title="标记"}

例如，任天堂的《超级马里奥兄弟》使用了他们称之为*NES-NROM-256*的布局，包括32 KB的程序ROM和用于图形的8 KB“字符ROM”（我们将在“图形”部分了解更多）[@cpu-nrom]。 *NES-NROM-256*也被设计为最多容纳3 KB的额外WRAM，尽管游戏没有使用它。

#### 超越限制

16 位地址总线的一大限制（影响第三代和第四代游戏主机）是其紧凑的地址空间。 如今，32 位计算机最大可以寻址 4 GB 的内存（而 64 位计算机可以奢侈地享用最大 16 EB 的内存）所以不再有人关心这个问题。但回到当时，NES 只有 64 KB 的地址空间，而且其中很大一部分被内存映射硬件占用（[竞争对手避免了这个问题](master-system#accessing-the-rest-of-the-components)）。

所以，这是否意味着游戏开发商只能开发不超过 49.97 KB 限制的游戏？ 绝对不是！ 如果说我们能从历史中学到什么，那就是对挑战性的问题总有一个聪明的解决办法；而这个问题是被 **Mapper** 解决的。

![映射器如何扩展 CPU 的寻址能力的简化表示图。 通过引入 Mapper，CPU 可以访问超过大程序 ROM 的额外内存组（地址组）。 不过游戏或程序现在有了在需要时手动切换 Bank 的新任务。](mapper/mapper.png){.tabs-nested .active title="带有映射器"}

![相同的配置但是没有安装映射器。 虽然简单且便宜，但是 CPU 只能访问有限数量的 Banks。](mapper/no_mapper.png){.tabs-nested-last title="没有映射器"}

Mapper 是卡带中包含的额外芯片，位于内存芯片和主机地址线之间。 它的主要工作是扩展地址空间，以便开发人员可以安装更多的芯片。 这是通过 **Bank (地址组) 切换**实现的：内存地址被组织成 Bank，Mapper 提供开关（通过读写特定内存地址控制）在 Bank 之间切换。 现在，CPU 仍然看到相同数量的内存，所以游戏需要针对操作 Mapper 进行编程。 由于成本效益问题，Mapper 是 80 年代至 90 年代初的主流技术。

![《超级马力欧兄弟2》（Super Mario Bros 2）的PCB [@photography-tsrom]。 超级马力欧兄弟3也使用这种布局，但包含了一个256 KB的程序ROM。](tsrom.png){.tabs-nested .active title="原始的"}

![带有重要部件标签的相同图片。 一开始，我以为额外的 WRAM 是用来存储存档的，但后来我意识到这个游戏没有存档（而且也没有电池）。 事实上，这个 RAM 芯片是用来储存解压后的关卡数据的。](tsrom_marked.png){.tabs-nested-last title="标记"}

回到 NES，《超级马力欧兄弟 2》和《超级马力欧兄弟 3》这样的游戏的卡带中包含了“MMC3” Mapper（由任天堂制造）。 作为比较，MMC3 提供了最大 512 KB 的程序 ROM，最大 256 KB 的 Character memory 和最大 8 KB 的额外 WRAM [@cpu-mmc3]。 您现在可以看出为什么《超级马力欧兄弟 3》的游戏内容比初代《超级马力欧兄弟》多很多了。

总而言之，虽然这台主机的内置功能存在一些限制，但是任天堂能确保它适应技术的发展。 另一方面，虽然这种技术有助于降低游戏机的成本，但它将部分成本转嫁到了游戏卡带上。 所以，游戏开发商必须在游戏质量和卡带成本之间做出权衡。

## 图形

图形是由一个名为**图像处理单元（Picture Processing Unit，PPU）**的专有芯片生成的。 这是 NES 的*标志性*芯片 换句话说，所有人都可以从硬件商店购买 6502 CPU，那为什么说 NES 与 Apple 2 或 Commodore 64 不同呢？ 嗯，NES 与其他机器的区别就在于 CPU 外围的芯片：PPU 和 APU。 这些分别造就了 NES 独特的图形和音频功能。

就是说，PPU 渲染被称为**精灵图（sprite）**和**背景（Background）**的 2D 图形，将渲染结果输出为视频信号。

### 硬件组织

![PPU的内存架构](ppu_content.png)

为了在屏幕上渲染图形，PPU 必须知道要绘制*什么*图形，将它们放置在屏幕上的*哪个*位置；以及*如何*绘制它们（即，使用哪个调色板）。

为了回答这些问题，PPU 被预编程为使用不同内存映射，查找下列类型的数据：

- 图形数据是从游戏卡带中提取的。它包含一个专用芯片，叫做 **Character Memory** ，用于存储 2D 绘图（被称为 **图块 (tile)**），这些数据被组织成一个名为**Pattern table** 的数据结构。 Character Memory 以“只读内存”（ROM）或“随机存取内存”（RAM）的形式存在，具体使用哪种取决于游戏使用不可变的图形，还是使用可以被 CPU 修改的图形。
  - PPU 可以访问被分为两组的，**最大 8 KB** 的 Character Memory，其中每组 4 KB。
- 告诉 PPU “在哪里”和“如何”绘制图形的元数据可以在其他区域找到：
  - 主板上安装了一个单独的 **2 KB SRAM**，专用于图形相关的数据。 任天堂称之为 **Video RAM**（VRAM），它存储两个称为 **Nametable** 的数据结构。
  - PPU 内置 **256 B** 的 DRAM 用于存储 **Object Attribute Memory**（OAM）。
  - 最后，PPU 还拥有 **4 B**的内存用于定义调色盘。

不用担心新术语，这些数据结构的含义将在下面的段落中逐步讨论。

### 构造帧

与其同时代产品一样，该芯片专为 CRT 显示器特性而设计。 没有帧缓冲：PPU 与 CRT 的电子枪同步渲染，即时构建图像。

PPU 绘制尺寸固定为 **256x240 像素**的帧[@graphics-overscan]。 哎呀，因为世界各地模拟电视标准不同，图像会因为显示设备的制式（NTSC 或 PAL）而存在差异。 简而言之，NTSC 电视会裁剪顶部和底部边缘以适应过扫描（只有约 244 行扫描线可见），所以开发人员在游戏中放置元素的时候将这些边缘视为“危险区域。” 另一方面，PAL 电视不会裁剪边缘，但是会用黑边来填充比 NTSC 多出来的扫描线（PAL使用 288 行扫描线）。

在幕后，PPU 输出的帧由两层组成。 出于演示目的，让我们用 *《超级马力欧兄弟》* 来展示它是如何工作的：

#### 图块 {.tabs.active}

::: {.subfigures .tabs-nested .tab-float .pixel .desktop-margined max_subfigures=1}

![两个由很多图块组成的 pattern table 上下拼接在一起。](ppu_mario/chr_map.png){.active title="全部"}

![一个图块。](ppu_mario/single.png){title="单个"}

在 Character ROM 中找到的图块。（为了演示目的，使用了默认调色板）。

:::

首先，PPU 使用**图块（tile）**作为精灵图（sprite）和背景（background）的构成。

NES 将图块定义为基本的 **8x8 像素图**，它们存储在 **Character Memory**（位于游戏卡带中）并组织成被为 **Pattern Table** [@graphics-rust] 的大型数据结构。 每个图块占用 16 B，一个 Pattern Table 包含 256 个图块 [@graphics-pattern]。 由于 PPU 最大可寻址 8 KB 的 Character Memory，因此它最多可以同时访问两个 Pattern Table。

在图块内部，每个像素使用 2-bits 编码，每个像素可以引用调色板 4 种颜色中的一种。 程序员最多可以定义 8 个调色板（4 个用于背景，其它的用于精灵图）。 每个调色板上引用的颜色指向由 64 种颜色 [@graphics-palettes] 组成的“主调色板”，主调色板代表该主机可以生成的所有颜色。 调色板由 4 种颜色组成，其中一个被保留用来 `表示透明`。

要开始在屏幕上绘制一些东西，游戏会设置一些表格，其中引用了 Character Memory 中的图块。 每个表负责每一帧中的一层（精灵图或者背景）。 然后，PPU 读取这些表，将其组装成 CRT 电子枪生成的扫描线。

我现在将解释每个层/表是如何工作的，以及它们在功能方面有何不同。

#### 背景图层 {.tab}

::: {.subfigures .tabs-nested .tab-float .pixel max_subfigures=1}

![填充好的背景图。](ppu_mario/nametable.png){.active title="全部"}

![填充好的背景图，选定区域被标记。](ppu_mario/nametable_marked.png){title="选定的"}

设置有垂直镜像的背景图，可实现平滑的水平卷动。 但是，只有一半可用。

:::

背景层是一个静态图块组成的 512x480 的像素图 [@graphics-nametables]。 你可能还记得可见帧要小得多，所以由游戏来决定显示哪一部分图层。 游戏还可以在运行过程中移动可见区域：**卷轴效果**就是这样实现的。

为了节约内存，4 个图块被组合成称为 **块（Block）**的 16x16 像素图，其中所有图块共享同一个调色板。

**Nametable**（保存在 VRAM中）指定在背景层中显示哪些图块。 PPU 查找 4 个 1024 Bytes 大小的 Nametable，每个 Nametables 对应背景层的一个象限。 但是，只有 2 KB 的 VRAM 可用！ 因此，如果卡带中没有额外的 VRAM，则只能存储 2 个 Nametable。 然而，还需要处理另外两个 Nametable 的地址：大多数游戏只是简单的让后两个 Nametable 与前两个 Nametable 使用相同的地址（这被称之为**镜像**）。

这种架构初看可能存在缺陷，但是它旨在降低成本，同时提供简单的 **可扩展性**：如果游戏需要更宽的背景，可以在卡带中包含额外的 VRAM。

每个 Nametable 的最后 64 字节用来存储**属性表（Attribute table）**，该表用来指定每个块（Block，由 4 个图块组成）的调色板 [@graphics-attribute_table]。

#### 精灵层 {.tab}

![渲染精灵图层.](ppu_mario/sprite_layer.png) {.tab-float.pixel.latex-framed}

精灵图（Sprite）是可以在屏幕上移动的图块（Tile）。 它们可以相互重叠，或者出现在背景后面。 可见性由它自身优先级数值决定（与传统图形设计软件中的“图层”概念相同）。

**Object Attribute Memory**（OAM）表指定了哪些图块被用作精灵图 [@graphics-oam]。 除了图块索引外，每个条目还包含了 (x, y) 坐标以及多个属性（调色盘，优先级和翻转标志）。 该表保存在 PPU 芯片的 256 Bytes DRAM 中。

OAM 表可以由 CPU 填充。 但是实践中这样做会很慢（并且如果没有在正确的实时间点完成，会有破坏帧图像的风险），因此，PPU 包含了一个名为**直接内存访问（Direct Memory Access）**或“DMA”的小组件，可以通过对它编程（方法是修改 PPU 的寄存器）来实现从 WRAM 复制数据到 OAM。 使用 DMA，可以保证数据表会在下一帧绘制开始前传输结束，但请记住，CPU 会在传输过程中暂停工作！

PPU 被限制为每条扫描线 **八个精灵图**，每帧最多 **64 个精灵图**。 由于一种名为“OAM 轮替顺序”的技术，可以突破扫描线限制，游戏手动改变OAM中条目的顺序。 这使得PPU在每一帧渲染不同的精灵集，同时CRT光束的速度会欺骗人眼，使其看到超出限制的精灵图。 然而，它们也会在屏幕上出现闪烁。

#### 背景分屏 {.tab}

![渲染结束的背景层，注意着重标记两个部分，这两个部分使用了不同的卷轴坐标值。 只有第二个部分会随着马力欧的移动而卷动。](ppu_mario/split.png) {.tab-float.pixel}

在继续之前，有一件事我还没告诉你。 如果你玩《超级马力欧兄弟》，你会注意到马力欧移动的时候，场景滚动很流畅。 但是，你会注意到顶部区域（统计数据所在的位置）保持静止，**即使这两个部分都是同一个背景层！**那么，这里发生了什么？ 好吧，游戏在帧生成的过程中（即所谓的 mid-frame）改变了卷轴滚动值，这样就可以显示当前世界名和统计数据（位于背景顶部的固定部分）。 NES 本身不提供这个功能，但是游戏可以通过观察 PPU 状态（主要是通过其状态寄存器[@graphics-ppustatus]）来推断时机。

为了实现这一点，游戏会使用一种叫 **Sprite 0 Hit** 的技术。 超级马力欧兄弟控制 PPU 在顶部状态栏的硬币后面渲染一个隐藏的精灵图，这恰好是一帧内绘制的第一个精灵图。 在 PPU 控制的电子束绘制完成第一个精灵图后，PPU 会在它的状态寄存器更新一个标志，该标志表示第一个精灵图（又名“Sprite 0”）绘制完成。 同时，游戏会在帧生成过程中不停检测 PPU 是否标记了“Sprite 0”状态（又被称为“Hit”，命中），如果命中，游戏会更新背景表的卷轴滚动属性，将可见区域移动到马力欧所在的位置。

总的来说，“Sprite 0 Hit”是一个非常微妙的操作，因为它很容易错过时机（Sprite 0 标志如果没有被及时清除，会导致重复的错误操作[@graphics-chibiakumas]）。 此外，由于检测 PPU 状态需要循环执行，执行的成本会很昂贵（就 CPU 周期而言）。 从好的方面说，后续的 Mapper 通过使用自动中断接管了这一操作，当任意扫描线命中时会触发中断[@graphics-nesdoug]（这是更高效的技术）。例如，《超级马力欧兄弟 3》使用这一技术显著提高了图像表现。

#### 结果 {.tab}

![嗒哒——！](ppu_mario/result.png) {.tab-float.pixel}

完成了一帧，是时候继续下一帧了！

但是，CPU 不能修改 PPU 正在使用的任何表的内容，否则屏幕上会出现伪影。 所以，当所有扫描线完成后，PPU 会在 CPU 上触发**垂直消隐（Vertical Blank，V-Blank）**中断 [@graphics-vblank]。 这会通知游戏，现在可以开始更新表而不会破坏当前显示的图像了。 此时，CRT 电子束指向屏幕的可见区域下方，进入了过扫描区域（或底部边框区域）。

只有少数 PPU 寄存器可以在垂直消隐期间以外 [@graphics-outside_vblank] 被修改，这解释了为什么在帧生成过程中修改背景卷轴位置不会破坏图像。

### 秘密和限制 {.tabs-close}

如果你认为分配内存来存储完整帧缓冲的设计更可取：这会导致非常高的内存消耗，而游戏主机的设计目标是合理的价格。 让我展示给你看为什么 NES 的设计是非常有效且灵活的。

#### 多重卷轴 {.tabs.active}

![超级马里奥2。 为垂直卷轴设置的 Nametable（水平镜像）。](secrets/multiscrolling_mirror.png) {.tab-float.pixel}

![超级马里奥3。 马里奥可以跑和飞，所以需要 PPU 能沿对角线方向卷动。 注意右侧边缘显示了错误的调色板！ 左侧边缘的错误被遮住了。](secrets/multiscrolling.png) {.tab-float.pixel}

有些游戏需要主角能垂直移动——因此 Nametable 被设置为**水平镜像**。 另一些游戏需要主角左右移动，所以使用**垂直镜像**。

任何一种镜像方式都允许 PPU 在不引起用户注意的情况下更新背景图块：在可见区域外的一定距离上更新图块。

但是如果角色想沿对角线方向移动怎么办？ PPU 可以向任何方向滚动，但是如果没有额外的 VRAM，边缘会被迫共享同样的调色板（记住，图块被组织成块 ）。

这就是为什么类似*《超级马力欧兄弟 3》*这样的游戏在马力欧移动的时候会在屏幕边缘显示奇怪的图形（游戏被配置为垂直卷轴）[@graphics-seam]。 这可能是因为他们需要最小化每个卡带的硬件成本（因为这个游戏已经使用了功能非常强大的 Mapper）。

一个有趣的*修复*方案：PPU 允许开发者在图块上应用一个垂直蒙版，可以有效地隐藏部分错误渲染的区域。

#### 图块转换 {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1 .pixel}

![初始扫描线。](secrets/multiplexing_1.png){.pixel .active title="初期"}

![后期扫描线。](secrets/multiplexing_2.png){.pixel title="后期"}

![用户实际看到的帧图像。](secrets/multiplexing_complete.png){.pixel title="显示的"}

假设帧渲染使用特定扫描线期间可用的图块。

:::

《超级马力欧兄弟 3》的另外一个特点是它能显示的图形数量。

这个游戏显示的背景图块种类数量超出了 NES 的严格限制。 那么它是怎么做到的？ 如果能在图像生成时捕获两张截图，我们就可以看到最终显示的帧实际上是由*两个*不同的帧合成的。

这是 MMC3 Mapper 的另外一个神奇之处，它不仅提供了额外的程序 ROM 空间，还通过连接两个 Character ROM 芯片扩展了 Character ROM 空间。 通过检查 PPU 正在请求屏幕的哪一个部分，Mapper 会重定向 PPU 到两个芯片的访问，比起原始设计，屏幕上现在能同时显示更多的图块种类 [@graphics-n3s]。

#### 奇怪的行为 {.tab}

在我的研究中，我遇到了许多描述 PPU 不常见行为的有趣文章，所以我想在这里提及其中的一些：

- 与生成 RGB 颜色并转换为 NTSC/PAL 信号进行广播的 [Master System 的 VDP](master-system#graphics) 不同，**NES 的 PPU 一次性完成所有工作** [@graphics-palettes]。 因此，PPU 主调色板的颜色和标准 RGB 色彩空间（目前的主流技术）之间没有一一对应的关系。 这为使用 RGB 定义 NES 的色彩留下了一定的空间，因此各种模拟器将显示不同的调色板。
  - RGB 调色板之间的差异在 Tim Worthington 的 DIY 套件中最为明显，该套件为 NES 添加了 RGB 信号输出，它还实现了一个在预定义调色板之间切换的开关[@graphics-nesrgb]。
- 主调色板中有一个 **“被诅咒”的颜色**（`$0D`），这种颜色会搞乱 NTSC 电视信号 [@graphics-cursed_colour]。 其实，这是因为一些电视错误地将这种颜色的信号理解为消隐信号，所以屏幕可能会出现闪烁。
- PPU 依赖 DRAM 存储 Object Attribute Memory（OAM）。 DRAM 需要不停的刷新来保持数据（不同于 SRAM），而 PPU 不渲染帧的时候 DRAM 不会刷新 [@graphics-oam]。 这主要发生在垂直消隐期间。 出于这个原因，不推荐在垂直消隐期间以外更新 OAM，因为垂直消隐期间的不刷新行为会破坏部分 OAM 数据。
  - PPU 的 PAL 变体不受该问题影响，因为 PAL 版在垂直消隐期间会自动刷新 DRAM （PAL 制式的垂直消隐时间更长）。

## 音频 {.tabs-close}

被称为**音频处理单元（Audio Processing Unit）**（APU）的专用组件提供这项服务[@audio-apu]。 理光将其嵌入 CPU 芯片中，大概是为了避免 CPU 和 APU 的未经许可的克隆。

### 功能

这个音频电路通常被称为**可编程声音发生器**（PSG），这大致意味着它只能生成一组预定义的波形，这在这个案例中*大部分*是正确的。 APU通过**五个音频通道**来排列音频数据——每个通道分别保留用于特定波形或信号。 每个通道包含不同的属性，这些属性会改变波形的音调、声音、音量和/或持续时间。 它们被连续混合并通过输出音频信号发送。

APU的功能通过内存地址暴露出来，CPU读取程序ROM中的与音乐相关的数据，并相应地编程APU。

此外，尤其是Famicom型号提供了卡带引脚，这些引脚将混合音频信号发送到卡带，使后者能与**额外通道**（需要额外芯片）混合[@general-cartridge]。

现在让我们回顾一下APU提供的五个通道 [@audio-review]：

#### 脉冲 {.tabs.active}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1}

![脉冲1通道的示波器视图。](pulse_single_1){video="true" title="脉冲1"}

![脉冲2通道的示波器视图。](pulse_single_2){.active video="true" title="脉冲2"}

![所有音频通道的示波器视图。](pulse_full){video="true" title="完整"}

Mother (1989).

:::

第**两个通道**生成**脉冲波** [@audio-apupulse]。 当听到时，它们表现出非常明显的*哔*声，主要用于**旋律或音效**。 各自的音序器可以生成三种类型的脉冲波，通过改变脉冲宽度（也称为 **占空比**）。 这些电路也连接到一个**扫频单元**（允许调整音高）和一个**包络生成器**以随时间降低音量（也称为 **衰减**）。

大多数游戏使用一个脉冲通道播放旋律，另一个播放伴奏。 当游戏需要播放音效时，播放伴奏的频道改为播放音效，然后切换回伴奏。 这样可以避免在游戏过程中打断旋律。

我认为可以公平地说，脉冲波是这一代游戏主机的象征之一。 我认为其采用的原因纯粹是为了节省成本: (有限的) CPU一次只能处理这么多数据，而脉冲波是理想的选择，因为它们不需要很多参数就能演奏简单的旋律(这反过来又释放了CPU周期用于其他操作)。

#### 三角波 {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1}

![三角波通道的示波器视图。](triangle_single){.active video="true" title="三角波"}

![所有音频通道的示波器视图。](triangle_full){video="true" title="完整"}

Mother (1989).

:::

与竞争对手相比，APU 的一个特色是能够产生**三角波**。 这种波形常用作旋律中的**低音部分**。 此外，通过显著修改其音高，也可以将其用于**打击乐**。

APU 为这种类型的波形保留了一个通道。 在幕后，专用的序列器需要 32 个周期来生成一个三角波信号[@audio-aputriangle]，这一限制使得生成的三角波形呈现阶梯状。

另一方面，相应的电路不提供音量控制。 无论如何，一些游戏通过调整混音器的音量控制找到了其他方法。

#### 噪声 {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1}

![噪声通道的示波器视图。](noise_single){.active video="true" title="噪音"}

![所有音频通道的示波器视图。](noise_full){video="true" title="完整"}

Mother (1989).

:::

“噪音”的概念是指一系列没有遵循任何模式或顺序的波形。 我们的耳朵将其解释为白噪声。 也就是说，APU 分配了一个通道可以播放不同种类的噪音。

在幕后，噪声发生器依赖于信封发生器(类似于脉冲通道)，它通过OR门随机静音[@audio-apunoise]。 静音的条件取决于连接到反馈回路的 15 位移位寄存器的值。 总而言之，这使得电路输出具有*伪不可预测*模式的信号，从而产生噪音。

在控制方面，4 位更改包络发生器的周期，1 位更改移位寄存器的“模式”。 那就只剩下 32 种噪音预设可供选择。 其中一半（16）种预设用来产生**干净的静电噪音**，另外一半用来产生**机器噪音**。

一般而言，游戏使用噪音通道来产生打击乐或环境效果。

#### 采样 {.tab}

::: {.subfigures .tabs-nested .tab-float max_subfigures=1}

![采样通道的示波器视图。](sample_single){.active video="true" title="采样"}

![所有音频通道的示波器视图。](sample_full){video="true" title="完整"}

Mother (1989).

:::

采样是可以回放的录制的音乐片段。 如你所见，采样不限制于单个波形，但是它们占用了很多空间。

APU 有一个专门的通道用来播放采样。 这里的采样被限制为 **7-bit 解析度**（编码值从 `0` 到 `127`）和 **约 15.74 KHz 采样率** [@audio-2a03ref]。 要对该通道编程，游戏可以串流 7-bit 数据（这会占用大量的 CPU 周期和存储空间）或使用 **delta 调制**，只编码下一个采样和前一个采样之间的变化。

APU 中的 delta 调制实现只接受 1-bit 值，这意味着游戏只能在每次计数器增加时说明采样增加减少`1`。 所以，以保真度为代价，delta 调制可以让游戏不必将连续值串流到 APU。

由于对该通道编程需要消耗较大的空间和更多的 CPU 周期，游戏通常会储存可以重复播放的小片段（比如鼓声采样）。 不管怎样，在 NES 的整个生命周期中，众多开发者想出了巧妙的办法来利用这个通道。

### 秘密和限制 {.tabs-close}

虽然 APU 的音质无法和黑胶唱片、磁带或 CD 相媲美，但程序员确实找到了扩展其功能的方法，主要得益于 NES 的模块化架构。

#### 额外通道 {.tabs.active}

![Castlevania III (USA/Europe, 1989) 的示波器视图。](castlevania_usa){.tabs-nested .tab-float .active video="true" title="美国"}

![恶魔城传说（Castlevania III 日本版，1989） 的示波器视图。](castlevania_jap){.tabs-nested-last video="true" title="日本"}

还记得 Famicom 提供了独占的卡带引脚用来扩展音频吗？ 嗯，像*悪魔城伝説*这样的游戏就利用了这一点，它捆绑了一个被称为 **Konami VRC6** 的芯片，添加了**两个额外的脉冲波形（Pulse wave）和一个锯齿波（Sawtooth wave）**。

看看这两个例子，展示了游戏的日版和美版之间的差异（后者运行在没有提供音频扩展功能的 NES 变体上）。

#### 颤音 {.tab}

![最终幻想 III (1990) 的示波器视图。](tremolo_full){.tab-float video="true"}

比起增加卡带成本，有些游戏创造性的应用现有技术增加了更多通道。

例如，《最终幻想III》想出了使用颤音效果来制造额外通道感觉的办法。

### 更深入的观察 {.tabs-close}

现在您已经了解了 APU 的能力，让我向您展示另一种观察其声音行为的方法。 这不仅会补充您已经了解的关于 APU 的知识，还会提供更客观的检查，尤其是不再依赖您的耳朵。

首先，让我们快速介绍一下声音理论。

感谢**傅里叶分析**的原理，我们可以将我们听到的每一个声音分解成不同频率和幅度的**正弦波之和**[@audio-complexwaveforms]。 最低频率的正弦波被称为**基本音频**，其余的称为**倍音**。 话是这么说，对于具有可辨音高的声音，您会发现大多数（如果不是全部）泛音的频率是基频的倍数。 话虽如此，对于有可辨音高的声音，您会发现大多数(如果不是全部)倍音的频率都是基本频率的倍数。 因此，这些泛音被称为**谐波** [@audio-harmonics]。

在本节中，谐波将成为一个经常出现的话题，因为脉冲波、三角波和锯齿波等波形遵循决定其谐波成分的公式。 否则，这些波形可能偏离其‘完美’形状。

#### 频谱图简介

由于正弦波现在是构成任何声音的关键成分，我们现在可以通过其正弦波分析我们听到的声音。 现在，对于任何类型的数据分析，没有什么比绘制图表来组织大量信息更方便的了。 那么，在声音分析的情况下，我们有了**频谱图**。 它们将音频样本的所有信息编码在一个图表上。 X轴表示时间（单位：秒），Y轴表示在此期间产生的正弦波频率（单位：赫兹），Z轴（每个点的颜色亮度）表示每个频率的功率/响度（单位：分贝）。

![可视化六秒钟脉冲通道的频谱图示例。](spectrogram_example){video="true"}

从这个例子中可以看到，每条水平线（也称为点序列）对应一个正弦波（最低的是基频，其余的是谐波），它们的亮度表示振幅。 有了这个前提，我们可以提取以下信息：

- 随着时间的推移，水平线往往会大幅位移。 这是脉冲频道音调的变化。
- 每个音符的起始音量大（点亮），随后迅速减弱。 这是APU的包络控制在起作用。
  - 在衰减期结束时，会出现一条明亮的垂直线。 这段声音很短，因此不容易听到，但无论如何，那是噪音（短暂的爆裂声），我怀疑是音频处理单元（APU）的缺陷。
- 持续按住一个音符超过0.25秒钟会让**颤音**出现（Y轴的连续波动）。 我不确定这是有意为之（使用了扫频功能）还是音频处理单元（APU）的不良效果。

注意，大多数这些观察结果仅凭听音频样本是不容易推导出来的，这也是编写本节的原因。

#### 绘制APU

为了研究 NES 的 APU，我编译了五个频谱图，每个频谱图对应 APU 的一个通道，并使用前面的例子。 在它们旁边，你会发现我试图解开数据展示的内容。

在开始之前，我必须承认，为了能够准确收集数据（例如减少噪音），做了一些妥协。 这些记录是使用名为 'towave' 的 Windows 程序获得的，该程序使用**带限合成**来解决 PSG 假声芯片仿真中的基本问题。 基本上，脉冲波、三角波和锯齿波由**无限谐波**组成。 然而，这与现代声卡的44.1kHz采样不太吻合。 因此，使用一种名为“带限合成”的技术来选择在声卡限制内的适当谐波。 总而言之，这种技术在性能、准确性和抗混叠之间提供了一个可行的平衡。 然而，数据可能与其模拟版本不完全相同（相比之下，模拟版本也会引入其他问题，例如录音设备的噪音），但我认为它的精确度在可接受的范围内，并且最重要的是，它可以完成本文的任务。

说了这么多，让我们继续分析吧。

##### 脉冲 {.tabs.active}

![脉冲1通道的频谱图。](spectrograms/eb0_pulse_nes.png) {.tab-float}

理论上说，脉冲音调只包含奇次谐波。 换句话说，基频与其第三谐波、第五谐波等组合在一起。 此外，每个谐波的振幅随着离基频的距离增加而减小。 振幅公式是 `amplitude = 1 ÷ harmonic number` [@audio-pulse]。

因此，注意频谱图上每个谐波的亮度在 Y 轴上越高越暗。 然而，APU 的脉冲波还似乎表现出前述的颤音效应，该效应在每个谐波数上增加。 此外，频谱图中本应没有任何声音的区域包含了微弱的泛音（可能是噪声和其他不完美的结果）。

##### 三角波 {.tab}

![三角通道的频谱图。](spectrograms/eb0_triangle_nes.png) {.tab-float}

三角波也由奇次谐波组成，但它们的振幅衰减更快（公式：`振幅 = 1 ÷ 谐波数²` [@audio-triangle]）。

然而，这并不是这里展示的情况，APU 产生的阶梯形三角波导致额外的谐波和振幅增加。

##### 噪声 {.tab}

![噪声通道的频谱图。](spectrograms/eb0_noise_nes.png) {.tab-float}

自然，噪声不遵循谐波规则，可能随机填满整个频率空间，因此缺乏易于识别的音高。

但是，通过查看时间线可以区分 APU 提供的不同噪声预设，每个预设都表现出一个独特的泛音集。

##### 采样 {.tab}

![样本通道的频谱图。](spectrograms/eb0_dcm_nes.png) {.tab-float}

与前几个通道不同，样本通道只播放开发者提供给 APU 的任何低分辨率录音。 考虑到这个例子播放的是鼓组，我在频谱图上看不到任何可识别的特征（除了类似于白噪声的部分）。

##### 锯齿波 {.tab}

![VRC6的锯齿波通道的频谱图。](spectrograms/castlevania_saw_nes.png) {.tab-float}

作为补充，我们也来看看VRC6扩展中的锯齿波通道。 首先，一个完美的锯齿波由所有谐波组成，每个谐波都显示出衰减的幅度（其中 `幅度 = 1 ÷ 谐波数` [@audio-sawtooth]）。

这是对数字设备的相当高要求，自然对于游戏卡带来说是负担不起的（可能根本不需要这样的完美！）。 因此，与 APU 的三角波类似，VRC6 在 7 个周期内顺序生成锯齿波（因此产生类似的阶梯效应）。

结果，相应的频谱图非常混乱，VRC6的近似技术在很多地方填充了额外的谐波。

#### 结论 {.tabs-close}

嗯，看来 NES 的合成波形远没有理论规定的形状那么接近。 这是否意味着APU存在缺陷？ 不！ APU的设计方式最终赋予该主机独特且可识别的声音——这些属性，无论是有意还是无意，使得频谱图显示了不寻常的结果。

作为旁注，完美的几何形状可能在视觉上令人愉悦，但有趣的是，我们的耳朵并不特别喜欢边缘完美的波形！ （你可能会开始听到爆裂声）。

展望未来，使用频谱图进行声音分析在其他文章中将会派上用场，无论是用于简单分析还是与其他系统进行比较。 请注意，这些图表并不一定是*主要工具*，尤其是在声音样本被混合了太多信道/乐器时（极大地增加了其分解的难度）。 但我认为它们将为任何类型的客观研究提供一个坚实的起点。

## 游戏

NES 游戏主要用 6502 汇编编写，并保存在**程序 ROM**中，而游戏的图形（Tile）保存在 **Character Memory** 中。

此外，游戏在任天堂许可的零售店销售（或出租）。

### 其它存储介质

尽管它只在日本发售，但是我想这是介绍这个寿命短暂但奇特的附加组件的好机会。就像 Mapper 一样，它为这台主机增加了更多功能。 这个外围设备被称为 **Famicom Disk System**（Famicom 磁碟机，FDS），它于 1986 年上架（约为 Famicom 发售后第三年）。 它有着外置软盘读取器一样的形状，并且捆绑了一个被称为“RAM 适配器”的奇怪形状的卡带。

::: {.subfigures .side-by-side max_subfigures=1}

![插入软盘的驱动器（图片中插入的是保护驱动器用的软盘形状的硬纸板）[@photography-amos]。 它可以使用 6 节 C 型电池（即 2 号电池，每节 1.5 V）或 3.6 W 的交流适配器运行。](fds/drive.png) {.toleft.no-borders}

![RAM 适配器 [@photography-amos]，安装在 Famicom 卡带插槽上，通过电缆连接到驱动器。](fds/ram.png) {.toright.no-borders}

组成 Famicom Disk System（FDS）的两个组件。

:::

Famicom Disk System 为 Famicom 增加了以下服务：

- 一种名为 **Famicom Disk** 的新游戏分发介质[@games-fds]。 基于三美（Mitsumi）公司的“Quick Disk”，它提供了每一面**约 64 KB** 的可读写数据存储功能。
- 一个**额外音频通道**，使用了[波表合成（wavetable synthesis）](game-boy#tab-3-2-wave)技术 [@games-fds_audio]。

![安装在 Famicom 上的 FDS [@photography-amos]。](fds/mounted.png) {.open-float.no-borders}

因为软盘是单一介质（相对于多芯片卡带），所有游戏数据需要被压缩进软盘。FDS 使用专有**文件系统**组织文件。

尽管如此，Famicom/NES 需要 Program Memory 和 Character Memory 才能工作，因此需要“RAM 适配器”来完成这个工作。 该组件包含 **32 KB 的Program RAM** 和 **8 KB 的 Character RAM** 来缓存软盘上的游戏数据。这样，主机就能从软盘读取，如同这是个基于卡带的游戏一样。

{.close-float}

为了操作驱动器，RAM 适配器嵌入了额外的 8 KB ROM 用来存储 **BIOS** [@games-fds_bios]。 该程序用来执行以下任务：

- 加载初始动画。
- 从软盘启动游戏。 在幕后，BIOS 包含了一个从软盘向各个内存芯片复制数据的例程，这样主机就能读取这些数据。
- 为游戏提供 I/O API，例如遍历磁盘的文件系统。

![FDS的两款零售游戏的例子。 磁盘的蓝色“风味”还具有防尘功能。](fds/floppies.jpg) {.toleft}

![FDS 初始动画，等待用户来……呃……插入游戏。](fds_bios){.toright video="true"}

在那个年代，任天堂在零售店部署了一些“信息亭（kiosks）”，这样用户就能带上他们的软盘，以较低的价格用新游戏覆盖软盘。

不幸的是，几年以后，Famicom Disk System 停产，随后游戏又回到了卡带介质。 好的一面是，与 FDS 的功能相比，一些新 Mapper 具有相似（或更强）的功能。

## 反盗版和锁区

任天堂能阻止未授权的游戏发行，这要归功于在 NES 中包含了一个叫**Checking Integrated Circuit**（CIC）[@anti_piracy-cic] 的专有**锁定**芯片。 它位于主机中，连接了复位信号（因此不容易被移除）。

该芯片运行一个叫 **10NES** 的内部程序，用来检查游戏卡带中是否存在另外一个锁定芯片。 如果检查失败，主机就会被无限复位。

主机正常运行期间，两个锁定芯片都在不停通信。 可以通过切断主机锁定芯片上的一个引脚来破坏该系统，这会让芯片处于空闲状态。 或者，向它发送 -5V 信号就可以冻结它。

CIC 的存在的原因是 1983 年电子游戏大崩溃引起的恐惧。 任天堂时任社长山内溥决定，为了推行高质量游戏，它们将负责审批每一个游戏 [@anti_piracy-vindicator]。

你会注意到日版 Famicom 主机是在 1983 年大崩溃之前发布的。 这就是为什么游戏卡带和主机都不包含 CIC 电路 [@anti_piracy-nocic]，相反，引脚用于可选的音频扩展。

## 这就是全部了，伙计们。
