# 技术研究：IEC61131-3 图形化编程加法演示

**日期**: 2026-01-18
**特性**: 001-ld-cfc-addition
**目的**: 解决技术上下文中的所有未明确项，并研究关键技术选型

---

## 研究决策汇总

本节记录所有技术决策及其理由。

| 决策项 | 选择方案 | 理由 |
|--------|----------|------|
| 图形库 | Fabric.js | Canvas 操作成熟，MIT 许可，社区活跃 |
| UI 框架 | Vue 3 | 轻量级，响应式，MIT 许可，中文文档丰富 |
| 构建工具 | Vite | 快速冷启动，HMR 优秀，开发体验好 |
| 测试框架 | Jest + Playwright | Jest 单元测试，Playwright E2E，均为主流选择 |
| 状态管理 | 原生 Observable Pattern | 简化依赖，适合小型项目 |
| 包管理器 | npm | 标准工具，淘宝镜像支持完善 |

---

## 1. HTML5 Canvas 图形库选型

### 调研背景
需要在浏览器中绘制 LD 梯形图和 CFC 功能块图，支持拖拽、连线、选中等交互。

### 候选方案

#### 方案 A: Fabric.js
- **优势**:
  - 成熟的 Canvas 对象模型
  - 内置拖拽、缩放、旋转支持
  - 事件系统完善
  - 活跃维护，GitHub 25k+ stars
  - MIT 许可
- **劣势**:
  - 包体积较大（~200KB gzipped）
  - 学习曲线中等

#### 方案 B: Konva.js
- **优势**:
  - 轻量级，性能优秀
  - API 设计简洁
  - 支持 React/Vue 集成
  - MIT 许可
- **劣势**:
  - 功能相对 Fabric.js 简化
  - 社区规模较小

#### 方案 C: 原生 Canvas API
- **优势**:
  - 无额外依赖
  - 性能最优
- **劣势**:
  - 需要大量手写交互逻辑
  - 开发周期长
  - 不符合项目宪章的"简洁实用"原则

### 决策：选择 Fabric.js

**理由**:
1. 功能完整，减少开发工作量
2. 社区成熟，文档齐全（有中文教程）
3. MIT 许可，符合宪章要求
4. 对于演示项目，200KB 包体积可接受
5. 事件系统支持实现变量监控的事件驱动需求

### 备选方案
如包体积成为问题，可降级至 Konva.js。

---

## 2. UI 框架选型

### 调研背景
需要响应式 UI 框架，支持组件化开发和事件驱动的状态更新。

### 候选方案

#### 方案 A: Vue 3
- **优势**:
  - 轻量级（~40KB gzipped）
  - 响应式系统天然支持事件驱动
  - 中文官方文档和社区
  - MIT 许可
  - 学习曲线平缓
- **劣势**:
  - 对于极简项目可能过重

#### 方案 B: React
- **优势**:
  - 生态最成熟
  - 社区最大
- **劣势**:
  - 需要额外状态管理（Redux/Zustand）
  - 中文资源相对较少
  - MIT 许可

#### 方案 C: 原生 Web Components
- **优势**:
  - 无框架依赖
  - 标准 API
- **劣势**:
  - 响应式需手动实现
  - 开发效率低

### 决策：选择 Vue 3

**理由**:
1. 符合宪章的简洁实用原则
2. 响应式系统完美匹配事件驱动需求
3. 中文文档丰富，降低认知负担
4. Composition API 支持模块化设计
5. MIT 许可，社区健康

---

## 3. JSON Schema 设计研究

### 调研背景
需要设计 JSON 格式来持久化 LD/CFC 程序，包含变量、功能块、连接关系等。

### Schema 结构建议

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "version": "1.0.0",
    "programType": {
      "type": "string",
      "enum": ["LD", "CFC"]
    },
    "variables": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "name": "string",
          "dataType": "INT",
          "direction": {
            "enum": ["INPUT", "OUTPUT", "INTERMEDIATE"]
          },
          "defaultValue": "number"
        }
      }
    },
    "functionBlocks": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "id": "string",
          "type": "ADD",
          "position": { "x": "number", "y": "number" },
          "inputs": ["variableName1", "variableName2"],
          "outputs": ["variableName3"]
        }
      }
    },
    "graphicElements": {
      "type": "array",
      "items": "混合类型（触点、线圈、连接线等）"
    }
  }
}
```

### 决策要点
1. 使用标准 JSON Schema 格式便于验证
2. 版本字段支持未来扩展
3. 引用式设计（变量通过名称引用）
4. 分离逻辑（functionBlocks）和视觉（graphicElements）

---

## 4. JavaScript 执行引擎设计

### 调研背景
需要将 LD/CFC 图形程序编译为 JavaScript 代码并执行。

### 架构设计

#### 编译流程
```
LD/CFC JSON → AST → JavaScript Code → eval() 执行
```

#### 代码生成示例（ADD 功能块）
```javascript
// 输入: { type: "ADD", inputs: ["input1", "input2"], outputs: ["output"] }
// 生成代码:
function executeProgram(variables) {
  const result = variables.input1 + variables.input2;

  // 溢出检测
  if (result > 32767 || result < -32768) {
    console.warn('溢出警告：结果超出 INT 范围');
    return (result & 0xFFFF) - (result & 0x8000 ? 0x10000 : 0); // 回绕
  }

  variables.output = result;
  return variables;
}
```

### 安全考虑
- 沙箱执行（避免访问全局对象）
- 输入验证（防止代码注入）
- 仅支持白名单功能块

### 决策：使用 Function 构造函数
```javascript
const compiledFunction = new Function('variables', generatedCode);
```

**理由**:
1. 比 eval() 更安全（作用域隔离）
2. 性能可接受（对于演示项目）
3. 便于调试（可查看生成的代码）

---

## 5. 事件驱动状态管理研究

### 调研背景
变量值改变需要立即触发 UI 更新和程序重新计算。

### 实现方案：Proxy + EventEmitter

```javascript
class VariableStore extends EventTarget {
  constructor(variables) {
    super();
    this._data = new Proxy(variables, {
      set: (target, property, value) => {
        const oldValue = target[property];
        target[property] = value;

        // 触发变更事件
        this.dispatchEvent(new CustomEvent('change', {
          detail: { property, oldValue, newValue: value }
        }));

        return true;
      }
    });
  }

  get(key) {
    return this._data[key];
  }

  set(key, value) {
    this._data[key] = value; // 触发 Proxy setter
  }
}
```

### 决策要点
1. 使用 ES6 Proxy 实现响应式
2. 继承原生 EventTarget 利用浏览器事件系统
3. 与 Vue 3 响应式系统解耦（但可集成）

---

## 6. 测试策略

### 单元测试（Jest）
- 模型层：变量、功能块的业务逻辑
- 服务层：编译器、执行引擎、存储
- 工具函数：验证、格式化

### E2E 测试（Playwright）
- 用户故事 1：完整 LD 程序创建和运行
- 用户故事 2：完整 CFC 程序创建和运行
- 边界情况：溢出、负数、未编译执行

### 测试覆盖率目标
- 核心逻辑（编译器、执行引擎）：> 80%
- UI 组件：> 60%

---

## 7. 性能优化策略

### Canvas 渲染优化
- 使用离屏 Canvas 缓存复杂图形
- 仅重绘变更区域（Dirty Rectangle）
- 节流拖拽事件（16ms/60fps）

### 变量更新优化
- 使用 requestAnimationFrame 批量更新 UI
- 防抖用户输入（减少重新计算频率）

### 代码分割
- 编辑器组件懒加载
- 按路由分割（如有多页面）

---

## 8. 国内开发环境配置

### npm 镜像配置
```bash
# 使用淘宝镜像
npm config set registry https://registry.npmmirror.com

# 或使用 .npmrc 文件
echo "registry=https://registry.npmmirror.com" > .npmrc
```

### CDN 备选方案
如 npm 安装仍有问题，可使用国内 CDN：
- BootCDN: https://www.bootcdn.cn/
- 字节跳动 CDN: https://cdn.bytedance.com/

---

## 9. 开发工具链

### 必需工具
- Node.js 18+ (LTS)
- npm 9+
- 现代浏览器（Chrome/Edge/Firefox）
- VS Code（推荐）

### VS Code 扩展推荐
- Volar (Vue 3 支持)
- ESLint
- Prettier
- EditorConfig

### 代码规范配置
```javascript
// .editorconfig
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
indent_style = space
indent_size = 2

// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "printWidth": 100,
  "endOfLine": "lf"
}
```

---

## 10. 风险与缓解

### 风险 1：Canvas 性能问题
- **缓解**: 限制同时显示的图形元素数量（< 20 个）
- **回退**: 如性能不足，简化动画效果

### 风险 2：浏览器兼容性
- **缓解**: 使用 Vite 自动 polyfill
- **回退**: 明确支持的浏览器版本（Chrome 90+）

### 风险 3：学习曲线（新手开发者）
- **缓解**: 提供详细中文注释和 quickstart.md
- **回退**: 录制视频教程

---

## 结论

所有技术上下文中的"NEEDS CLARIFICATION"已通过研究解决：

- ✅ 语言/版本：JavaScript ES2020+ / HTML5 / CSS3
- ✅ 主要依赖：Fabric.js, Vue 3, Vite, Jest, Playwright
- ✅ 存储方案：JSON 文件（localStorage + 下载）
- ✅ 测试方案：Jest (单元) + Playwright (E2E)
- ✅ 目标平台：现代浏览器
- ✅ 性能目标：< 100ms 操作响应，< 50ms 变量更新
- ✅ 约束条件：纯前端，INT 数据类型，简化 IEC61131-3
- ✅ 规模范围：单用户，< 10 变量，演示用途

所有决策均符合项目宪章要求（MIT/Apache 许可，国内镜像源，简洁实用，跨平台）。

**下一步**: 进入 Phase 1 - 数据模型设计和合约定义。
