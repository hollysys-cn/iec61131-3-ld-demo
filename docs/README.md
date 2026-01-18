# IEC61131-3 LD/CFC 加法示例 - 快速入门

## 📚 项目简介

本项目提供符合 IEC61131-3 第3版标准的图形化编程语言（LD 梯形图和 CFC 连续功能图）的**加法示例程序**，用于教学演示和学习。

**核心价值**：
- ✅ 零依赖，手动创建的 PLCopen XML 文件
- ✅ 完整的中文注释和文档
- ✅ 兼容主流 IEC61131-3 开发工具（CODESYS、TIA Portal、CX-Programmer）
- ✅ 包含溢出行为演示和测试用例

---

## 🎯 适用人群

- 工业自动化工程师学习 IEC61131-3 标准
- PLC 编程初学者了解图形化编程语言
- 自动化专业学生进行课程实践
- 需要对比 LD 和 CFC 两种编程方式的开发者

---

## 📂 项目结构

```
iec61131-3-ld-demo/
├── examples/              # 示例程序文件
│   ├── ld/               # LD 梯形图示例
│   │   ├── addition.xml      # LD 加法程序（PLCopen XML 格式）
│   │   ├── addition.md       # LD 示例说明文档
│   │   └── test-cases.json   # 测试用例数据
│   ├── cfc/              # CFC 功能块图示例
│   │   ├── addition.xml      # CFC 加法程序（PLCopen XML 格式）
│   │   ├── addition.md       # CFC 示例说明文档
│   │   └── test-cases.json   # 测试用例数据
│   └── shared/           # 共享资源
│       ├── docs/             # 技术文档
│       │   ├── overflow-behavior.md      # INT 溢出行为说明
│       │   └── tool-compatibility.md     # 工具兼容性指南
│       └── schemas/          # PLCopen XML Schema（参考）
├── docs/                 # 项目文档（本文件）
│   └── README.md
└── specs/                # 规格说明（开发者参考）
    └── 001-ld-cfc-addition/
```

---

## 🚀 快速开始

### 步骤 1：选择示例程序

根据您想学习的编程语言类型选择：

- **LD 梯形图**：`examples/ld/addition.xml`（推荐初学者）
- **CFC 功能块图**：`examples/cfc/addition.xml`（推荐有 LD 基础的用户）

### 步骤 2：准备开发工具

#### 推荐工具：CODESYS（免费）

1. 下载 CODESYS Development System：https://www.codesys.com/download.html
2. 安装（支持 Windows、Linux、macOS）
3. 首次启动，选择"标准版"（免费）

#### 其他工具

- **Siemens TIA Portal**：商业授权，适合 Siemens PLC 项目
- **Omron CX-Programmer**：商业授权，适合 Omron PLC 项目

**详细兼容性信息**：查看 [`examples/shared/docs/tool-compatibility.md`](../examples/shared/docs/tool-compatibility.md)

### 步骤 3：导入示例程序

#### CODESYS 导入步骤

1. 打开 CODESYS Development System
2. 点击菜单 **文件** → **新建项目**
3. 选择设备类型（例如：CODESYS Control Win V3）
4. 在项目树中右键点击 **Application** → **Import PLCopen XML...**
5. 选择 `examples/ld/addition.xml`（或 `examples/cfc/addition.xml`）
6. 点击**打开**，程序将自动导入

#### TIA Portal 导入步骤

1. 打开 TIA Portal
2. 创建新项目或打开现有项目
3. 右键点击 **Program blocks** → **External source** → **Generate blocks from source**
4. 选择 `addition.xml` 文件
5. 按照向导完成导入（可能需要手动调整数据类型）

### 步骤 4：运行和测试

#### 设置输入值

在工具的变量监视窗口或仿真环境中：

1. 找到变量 `A` 和 `B`
2. 设置初始值：
   - `A = 10`
   - `B = 20`

#### 运行程序

1. 点击**编译**按钮（确保无错误）
2. 点击**下载到设备**（如果使用仿真器，选择仿真模式）
3. 点击**启动**或**运行**
4. 观察输出变量 `Result`，应显示 `30`

#### 测试其他用例

尝试以下输入值组合（参考 `test-cases.json`）：

| A 值 | B 值 | 期望 Result | 说明 |
|------|------|------------|------|
| 10 | 20 | 30 | 正常加法 |
| -5 | 3 | -2 | 负数加法 |
| 32767 | 1 | -32768 | 正向溢出（回绕） |
| -32768 | -1 | 32767 | 负向溢出（回绕） |
| 0 | 0 | 0 | 零值 |

**溢出行为说明**：查看 [`examples/shared/docs/overflow-behavior.md`](../examples/shared/docs/overflow-behavior.md)

---

## 📖 详细文档

### LD 梯形图示例

完整使用指南：[`examples/ld/addition.md`](../examples/ld/addition.md)

**要点**：
- 变量定义：2 个输入（A, B），1 个输出（Result）
- 逻辑结构：使用 ADD 功能块实现加法
- 适用场景：初学者首选，类似电气继电器逻辑

### CFC 功能块图示例

完整使用指南：[`examples/cfc/addition.md`](../examples/cfc/addition.md)

**要点**：
- 与 LD 示例功能相同，表达方式不同
- 数据流可视化，适合复杂控制逻辑
- 对比学习：理解 LD 与 CFC 的差异

---

## 🔍 学习路径建议

### 初学者（0-1周）

1. ✅ 阅读本 README 文档
2. ✅ 安装 CODESYS 开发工具
3. ✅ 导入并运行 LD 示例（`examples/ld/addition.xml`）
4. ✅ 修改输入值，观察输出变化
5. ✅ 阅读溢出行为文档，理解边界情况

### 进阶学习（1-2周）

1. ✅ 导入并运行 CFC 示例（`examples/cfc/addition.xml`）
2. ✅ 对比 LD 和 CFC 的程序结构
3. ✅ 尝试修改程序，实现减法或乘法
4. ✅ 阅读工具兼容性指南，测试其他开发工具

### 深入研究（2周以上）

1. ✅ 研究 PLCopen XML 文件结构
2. ✅ 手动创建自定义程序文件
3. ✅ 实现带溢出检测的安全加法
4. ✅ 贡献改进建议或新示例到项目

---

## ❓ 常见问题

### Q1：为什么导入时提示"无法识别文件格式"？

**原因**：工具不支持 PLCopen XML v2.01 标准。

**解决方案**：
1. 更新工具到最新版本
2. 使用 CODESYS（兼容性最好）
3. 查看工具兼容性指南寻找解决方案

### Q2：中文注释显示乱码怎么办？

**原因**：工具未使用 UTF-8 编码打开文件。

**解决方案**：
1. 在工具的选项中设置默认编码为 UTF-8
2. 使用文本编辑器（如 VS Code）打开 XML 文件，另存为 UTF-8（无 BOM）

### Q3：编译时提示"找不到 ADD 功能块"？

**原因**：工具的标准函数库未启用。

**解决方案**：
1. 在项目设置中启用"标准库"或"IEC 函数库"
2. 手动添加 ADD 功能块的引用

### Q4：如何在实际项目中使用这些示例？

**注意**：本示例仅用于教学演示，**不建议直接用于工业生产环境**。

**实际应用时需要**：
- 添加溢出检测和错误处理
- 使用更大的数据类型（DINT、LINT）避免溢出
- 进行充分的功能测试和安全验证

---

## 🤝 贡献与反馈

欢迎贡献改进建议、错误报告或新示例！

- 📧 提交 Issue：[项目仓库 Issues 页面]
- 🔀 提交 Pull Request：遵循项目的贡献指南
- 💬 讨论交流：[项目讨论区]

---

## 📜 许可与版权

本项目遵循 **MIT 许可协议**，可自由用于学习和教学目的。

**引用标准**：
- IEC 61131-3 第3版（工业自动化系统 - 可编程控制器编程语言）
- PLCopen XML v2.01（程序交换格式标准）

---

## 📞 联系方式

- **项目维护者**：[维护者信息]
- **邮箱**：[联系邮箱]
- **项目主页**：[GitHub 仓库链接]

---

**祝学习愉快！🎓**

---

**文档版本**: 1.0
**最后更新**: 2026-01-18
**语言**: 简体中文
