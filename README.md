# Combined Science 双语讲义与自学平台

本项目包含两个相互独立、共享教学资源的产品：

1. `notes/`：中英双语讲义，适合阅读、复习、备课和打印。
2. `platform/`：可售课的自学平台，未来提供分步课程、测验、进度、账号与购买功能。

两个产品共享 `shared/` 中的图片、课程大纲和题库，但不混用页面代码。

## 目录

```text
.
├─ notes/
│  └─ biology/
│     └─ index.html
├─ platform/
│  └─ README.md
├─ shared/
│  ├─ images/biology/
│  └─ syllabus/
├─ resources/
├─ docs/
│  ├─ DESIGN_SYSTEM.md
│  └─ BILINGUAL_CONTENT_STANDARD.md
└─ robots.txt
```

当前主讲义页面：`notes/combined-science/index.html`。旧的 Biology 单科页面保存在 `notes/archive/biology-only.html`，仅供回看。

Biology 的统一教学顺序记录在 `shared/syllabus/README.md`。讲义与自学平台必须共同遵守该顺序。

三科综合教学的 Phase 0–6 路线记录在 `shared/syllabus/TEACHING_ROADMAP.md`，其优先级高于各科单独的编号顺序。

Lower Secondary 与 IGCSE 标签的判定规则和资料版本记录在 `docs/SYLLABUS_TAGGING_SOURCES.md`。
