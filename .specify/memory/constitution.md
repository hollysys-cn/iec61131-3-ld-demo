<!--
====================================================================================================
SYNC IMPACT REPORT - Constitution Amendment
====================================================================================================
Version Change: [INITIAL] → 1.0.0
Ratification: This is the initial ratification of the project constitution
Amendment Date: 2026-01-18

Modified Principles:
  - N/A (Initial creation)

Added Sections:
  ✅ 核心原则 (Core Principles) - 13 principles defined:
     I. 简体中文优先 (Simplified Chinese First)
     II. 模块化与 SOLID 原则 (Modularity and SOLID Principles)
     III. 架构保守主义 (Architecture Conservatism)
     IV. 工作空间隔离 (Workspace Isolation - MANDATORY)
     V. 现代工程实践 (Modern Engineering Practices)
     VI. 国内镜像源优先 (Domestic Mirror Sources Priority)
     VII. 人工审批机制 (Manual Approval for Dependencies)
     VIII. 许可合规 (License Compliance - MANDATORY)
     IX. 代码质量优先 (Code Quality First)
     X. 最小化修改范围 (Minimal Change Scope)
     XI. 标准化编码规范 (Standardized Encoding)
     XII. 简洁实用主义 (Pragmatic Simplicity)
     XIII. 跨平台开发体验 (Cross-Platform Development Experience)
  ✅ 架构与工程实践 (Architecture and Engineering Practices)
  ✅ 依赖管理与许可 (Dependency Management and Licensing)
  ✅ Governance (治理)

Removed Sections:
  - N/A (Initial creation)

Templates Status:
  ✅ plan-template.md - Updated Constitution Check section with concrete checklist
  ✅ spec-template.md - Reviewed, requirements structure compatible with principles
  ✅ tasks-template.md - Reviewed, task organization aligned with quality standards
  ℹ️  Commands directory (.specify/templates/commands/) - Not found in current structure

Follow-up TODOs:
  [ ] Create project README.md with quickstart guide (simplified Chinese)
  [ ] Verify all existing files use UTF-8 encoding with LF line endings
  [ ] Setup .env.example template for environment configuration
  [ ] Add dependency installation guide with domestic mirror sources
  [ ] Create PR/MR template with constitution compliance checklist

====================================================================================================
-->

# IEC61131-3 LD Demo 项目宪章
<!-- IEC61131-3 Ladder Diagram Demo Project Constitution -->

## 核心原则 (Core Principles)

### I. 简体中文优先 (Simplified Chinese First)
本项目主要面向中国大陆用户，所有文档、推理过程、代码注释及软件界面必须优先使用简体中文。
技术术语可保留英文原文，但须在首次出现时提供中文注释。

**理由**: 降低认知负担，提升团队协作效率和用户体验。

### II. 模块化与 SOLID 原则 (Modularity and SOLID Principles)
必须遵循 SOLID 原则进行架构设计，采用清晰的模块化结构。每个模块必须具备：
- 单一职责，边界清晰
- 独立可测试
- 完整的接口文档
- 合理运用设计模式，避免过度设计

**理由**: 保证代码可维护性、可扩展性和团队协作效率。

### III. 架构保守主义 (Architecture Conservatism)
必须充分利用现有代码架构和目录结构，除新建项目外，不得随意修改现有文件组织方式。

**理由**: 保持项目稳定性，减少迁移成本和潜在风险。

### IV. 工作空间隔离 (Workspace Isolation - 强制 MANDATORY)
开发环境、依赖工具和配置必须自动安装且限制在当前工作空间内，不得污染本地系统环境。
具体要求：
- 环境变量配置使用项目根目录的 `.env` 文件
- 依赖包安装在项目本地（如 `node_modules`、虚拟环境等）
- 构建产物输出到项目指定目录

**理由**: 确保开发环境可重现、可移植，避免环境冲突。

### V. 现代工程实践 (Modern Engineering Practices)
必须结合开源社区最佳实践，遵循现代软件工程标准流程：
- 语义化版本控制 (Semantic Versioning)
- Git 工作流（Feature Branch Workflow）
- 持续集成/持续交付 (CI/CD)
- 自动化测试和代码质量检查

**理由**: 提升开发效率和软件质量。

### VI. 国内镜像源优先 (Domestic Mirror Sources Priority)
所有开发依赖包和工具必须优先从国内镜像源获取（推荐阿里云镜像站
https://developer.aliyun.com/mirror/），仅在镜像源不可用时使用官方源。

**理由**: 提升下载速度，减少网络问题导致的构建失败。

### VII. 人工审批机制 (Manual Approval for Dependencies)
引入新的第三方依赖前，必须提前通知人工确认，说明依赖用途、许可协议和潜在风险。

**理由**: 避免依赖膨胀和许可风险，保持项目轻量化。

### VIII. 许可合规 (License Compliance - 强制 MANDATORY)
所有第三方依赖必须为开源且对商业应用友好，优先选用 MIT 或 Apache 2.0 协议。
若涉及其他协议（如 GPL、LGPL、MPL 等），必须经过人工审核并记录批准理由。

**理由**: 规避法律风险，确保商业应用合规性。

### IX. 代码质量优先 (Code Quality First)
必须注重代码质量，具体要求：
- 完善的代码注释和文档（优先使用中文）
- 控制圈复杂度（单个函数/方法不超过 10）
- 最大化代码复用，避免重复逻辑
- 良好的可测试性设计
- 关注用户体验和系统性能

**理由**: 降低维护成本，提升长期可持续性。

### X. 最小化修改范围 (Minimal Change Scope)
修复 BUG 或添加新功能时，必须遵循最小化修改原则：
- 只修改必要的文件和代码行
- 避免连带修改非相关模块
- 重构需求必须单独提出，不得与功能开发混合

**理由**: 降低引入新问题的风险，提升代码审查效率。

### XI. 标准化编码规范 (Standardized Encoding)
所有文本文件必须使用：
- 编码格式：UTF-8 (without BOM)
- 换行符：LF (Unix/Linux style)

**理由**: 确保跨平台兼容性，避免版本控制中的差异噪音。

### XII. 简洁实用主义 (Pragmatic Simplicity)
设计哲学遵循：
- 简洁优于复杂
- 实用优于炫技
- 人类易懂优于机器优化

**理由**: 降低学习曲线，提升可维护性。

### XIII. 跨平台开发体验 (Cross-Platform Development Experience)
必须保持 Windows、Linux、macOS 三大平台开发体验的一致性：
- 优先选择跨平台工具和库
- 避免平台特定的硬编码路径
- 使用平台无关的构建脚本（如 npm scripts、Makefile with portable commands）

**理由**: 减少开发者认知负担，降低平台切换成本。

## 架构与工程实践 (Architecture and Engineering Practices)

### 目录结构约束 (Directory Structure Constraints)
除新建项目外，必须保持现有目录结构，新增模块需遵循以下约定：
- `.specify/` - 项目规范、模板和元数据
- `.github/` - CI/CD 配置、GitHub Actions 和 Issue 模板
- `src/` 或 `lib/` - 源代码主目录
- `tests/` 或 `__tests__/` - 测试代码目录
- `docs/` - 项目文档目录

### 配置文件管理 (Configuration Management)
- 敏感信息必须存储在 `.env` 文件中（不得提交到版本控制）
- 提供 `.env.example` 模板文件
- 使用 `.gitignore` 排除环境特定文件

### 错误处理与日志 (Error Handling and Logging)
- 必须提供清晰的错误信息（中文优先）
- 关键操作必须记录日志（包含时间戳、操作类型、结果）
- 日志级别：DEBUG、INFO、WARNING、ERROR、CRITICAL

## 依赖管理与许可 (Dependency Management and Licensing)

### 依赖引入流程 (Dependency Introduction Process)
1. 提出依赖需求（说明用途和替代方案对比）
2. 确认许可协议（MIT/Apache 优先）
3. 检查依赖健康度（维护活跃度、安全漏洞记录）
4. 人工审批确认
5. 更新依赖清单和文档

### 镜像源配置 (Mirror Configuration)
项目必须提供依赖安装指南，明确国内镜像源的配置方法：
- npm: 使用淘宝镜像或阿里云镜像
- pip: 使用阿里云 PyPI 镜像
- Maven/Gradle: 使用阿里云 Maven 仓库
- Docker: 使用国内 Docker Hub 镜像

## Governance (治理)

### 宪章优先级 (Constitution Priority)
本宪章高于所有其他开发规范和实践指南。如遇冲突：
1. 宪章原则优先
2. 项目现有配置次之（依据"配置冲突"条款）
3. 团队约定最后

### 修订流程 (Amendment Process)
宪章修订必须遵循以下流程：
1. 提出修订提案（包含理由、影响范围、迁移计划）
2. 团队评审和讨论
3. 人工批准确认
4. 更新版本号（语义化版本控制）
5. 通知所有相关方并更新文档

### 版本控制策略 (Versioning Policy)
- MAJOR: 向后不兼容的原则变更（如移除或重定义核心原则）
- MINOR: 新增原则或章节，扩展现有指南
- PATCH: 措辞优化、错误修正、非语义性调整

### 合规性检查 (Compliance Review)
所有代码审查和合并请求必须验证宪章合规性：
- 检查是否遵循核心原则
- 验证新依赖的许可协议
- 确认修改范围最小化
- 审查代码注释和文档完整性

### 例外情况处理 (Exception Handling)
如遇特殊情况需违反宪章原则，必须：
1. 在代码/文档中明确标注例外说明
2. 记录例外理由和批准人
3. 提供后续改进计划（如适用）

**Version**: 1.0.0 | **Ratified**: 2026-01-18 | **Last Amended**: 2026-01-18
