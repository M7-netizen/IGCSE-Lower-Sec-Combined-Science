# 项目背景：IGCSE/Lower Secondary 理科学习大纲网站

## 这是什么

一份双语（中英对照）理科学习网站，托管在 GitHub Pages。最初是为辅导一名学生（Y8, Brighton College）做的教学讲义，用户是这名学生的私人老师，本身是 UX/UI 设计师背景。

**当前唯一的活跃文件是 `index.html`**（单页应用，无构建流程，纯静态 HTML+CSS+JS，可以直接用浏览器打开）。配套一个 `images/` 文件夹放插图。没有其他依赖、没有 npm、没有框架。

## 文件结构

```
/index.html      主文件，~200KB，是给老师本人上课用的参考讲义
/images/*.jpg    4张插图（心脏结构、血管结构、呼吸系统、MRS GREN图解），相对路径引用
/robots.txt      屏蔽搜索引擎收录（用户不想付费开 GitHub 私有仓库，用这个折中）
```

**⚠️ 重要：图片必须保持独立文件，绝不要改回 base64 内嵌。** 之前试过把图片编码进 HTML，文件涨到 7.7MB，导致 GitHub 网页版编辑器加载/保存时把文件截断，页面变空白。教训是：**任何时候修改这个仓库，都用「删除旧文件 + 重新上传新文件」，绝不要用 GitHub 网页版的文件编辑器（那个内置的 CodeMirror 编辑器处理不了这种大小的单行 base64 内容）。** 如果是用 git 命令行/Claude Code 直接操作文件系统，这个问题不存在，只是提醒你别引导用户走网页编辑器这条路。

## 内容结构（读代码前先建立心智模型）

- 顶部 `<header>`：标题 + 图例说明（Core/Extended/背景/Stage 7-8-9/IGCSE专属 五种标签的含义）+ 筛选按钮栏
- `<aside>`：侧边栏导航，按 Biology / Chemistry / Physics 三个学科分组，每个链接 `data-target="b1"` 对应正文里 `<details class="topic" id="b1">`
- `<main>`：33个 `<details class="topic" id="...">` 章节，Biology B1–B16、Chemistry C1–C12、Physics P1–P5，每章内部由若干 `<div class="card">`（或 `card red` / `card green` 变体）组成，每张卡片一个 `<h3>` 标题
- 每个 `<h3>` 标题后面跟 0-2 个 `<span class="tag ...">` 标签：
  - `tag core` / `tag ext` / `tag bg` — 剑桥 0653 IGCSE 大纲的 Core/Extended/背景知识分级
  - `tag y8` — 标注这块内容同时也在 Cambridge Lower Secondary Science **0893**（Stage 7/8/9，对应 Year 7-9）的官方学习目标里，是学生**现在**这个阶段该学的
  - `tag igcse` — 标注这块内容**只在** 0653 IGCSE 里出现，0893 完全没有，属于超前两年的内容
- 常见的表格样式约定：如果某个表格的**整列**都是"有/无"这种布尔判断，用 `<td class="yn-cell"><span class="yn yes">✓</span></td>` / `yn no ✗`，配合 `<th class="yn-head">` 让表头居中对齐。**但如果一列里只有部分行是布尔判断、其他行是描述性文字，就不要用 yn-cell/yn-head**，保持默认左对齐——这是踩过坑之后定下的规则，混合列强行居中会导致文字行和勾叉行错位。

## 已实现的交互功能（都是纯 JS，不依赖任何库）

1. **侧边栏点击跳转 + 滚动自动高亮（scroll-spy）**：用 `IntersectionObserver` 监听哪个 `<details>` 章节滚动到视口顶部附近，自动给对应侧边栏链接加 `.active`
2. **内容筛选器**（`.filter-bar` 三个按钮）：全部 / 只看现在可教（0893）/ 只看 IGCSE 专属，根据每张卡片 `<h3>` 里的 `tag y8` / `tag igcse` 决定显示或隐藏该 `.card`，筛选状态存在 `localStorage`
3. **响应式侧边栏**：桌面端 `position:sticky` + `overflow-y:auto` 内部滚动；手机/平板端改成顶部横向滚动条 `overflow-x:auto`。**这两种写法故意分开**——sticky + 内部滚动同时出现在触屏设备上是已知的 WebKit bug（会导致点击被误判成滑动手势、链接点不动），所以移动端专门用媒体查询换了一套不会触发这个 bug 的样式。改动这块 CSS 之前一定要理解这个背景，别图省事合并成一套。
4. 有一个独立的、**目前没有部署在这个文件里**的 `self-study-demo.html` 原型（用户如果要做付费自学平台会用到），实现了 Learn → Quick Check → Mistake Feedback → Exam Question → Model Answer → Chapter Mastery 的六段式自测流程，用 `localStorage` 记录做题进度，目前只在 Biology B4 这一章做了完整示范。**这个文件不在当前仓库里，是另一条产品线，暂时搁置**，除非用户明确说要继续做自学平台才需要碰它。

## 一个关键的、还没完全解决的问题

这份资料最初是照 **Cambridge IGCSE Combined Science (0653)** 大纲写的（面向 Year 10-11，备考 IGCSE 正式考试），但学生实际是 **Year 8**，按剑桥自己的体系应该在读 **Cambridge Lower Secondary Science (0893)**（Year 7-9，非考试性质的过渡课程）。这两个是完全不同的两套课程框架，不是版本新旧的关系。

已经做的补救：给全书 164 张卡片逐一打上了 `tag y8`（对应 0893 Stage 7/8/9 的官方学习目标，可以带着筛选器用）或 `tag igcse`（只在 0653 出现，可以先跳过）标签，并做了上面提到的筛选功能。

**还没做、值得关注的缺口：**
- 0893 Stage 8 有一个官方要求的独立子主题 **"Coordination and response"**（协调与反应：肾上腺素/激素、植物向光性向重力性等 tropisms）——这份资料**完全没有对应章节**，是真实存在的内容缺口，不是标签问题
- 之前把 `tag y8` 标注到 Stage 7/8/9 具体哪一个，是我（上一位 Claude）人工比对官方 PDF 逐条判断的，**没有做二次校验**，如果要长期依赖这份材料，建议找时间抽查几个标签是否准确
- 用户还没最终确认学校实际用的是不是 0893——这个判断目前是基于"Y8 通常对应 0893"的合理推测，不是 100% 已核实的事实

## 最新进展（2026年9月）

学生开学三周后，反馈学校目前教的**第一个内容是化学元素周期表**，对应本书 **Chemistry C8** 章节。接手后建议优先核对一下 C8 这一章的内容深度和范围是否贴合学生现在的实际学校进度（C8 目前打的标签是 Stage 7 周期表结构基础 + Stage 9 族的趋势规律，可以据此判断是否需要调整深浅）。

**2026-09-26 更新：** 已针对 C8 做了一轮补全，详见 `docs/SYLLABUS_TAGGING_SOURCES.md` 里「C8 元素周期表补充」一节。主要做了两件事：① 新增一张自绘的元素周期表全览图（Period 1–4 + 完整过渡元素排，纯 HTML/CSS Grid，不是图片，颜色区分金属/类金属/非金属，描边标出 Group I/VII/0），放在 C8 第一张卡片；② 对照 0653 syllabus 原文，补上了之前缺的几条 Core 考点——周期内金属性→非金属性渐变的显式表述、Group I 熔点密度趋势、Group VII 密度趋势、以及完全缺失的「过渡元素 Transition Elements」整节内容。**注意：** 过渡元素这一节的 `IGCSE专属` 标签是推测出来的，本地和网络都没找到 0893 Curriculum Framework v3.0 原文，以后有机会应该核实一下。

## 给接手的 Claude Code 的建议

1. 改动前先完整读一遍现有 `index.html`（文件不大，直接读没问题），不要凭这份文档的描述就动手，文档可能有遗漏
2. 任何新增内容，请沿用现有的 `card` / `tag` / `note` / `model-answer` 这套 CSS class 体系，不要引入新的视觉语言，保持全书风格一致
3. 新增章节或大段内容时，记得同步更新侧边栏 `<a class="side-link" data-target="...">` 列表，并给新内容打上合适的 `tag`（core/ext/bg，以及如果确实对应 0893 大纲的话，加 `tag y8` 或 `tag igcse`）
4. 每次改完，建议本地用浏览器直接打开文件验证一下（尤其是筛选器、侧边栏滚动这些 JS 交互），不要只看代码没测试
5. 图片继续放 `images/` 文件夹，相对路径引用，不要用 base64
