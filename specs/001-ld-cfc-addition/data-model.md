# 数据模型：IEC61131-3 图形化编程加法演示

**日期**: 2026-01-18
**特性**: 001-ld-cfc-addition
**目的**: 定义核心实体的数据结构、关系和验证规则

---

## 实体关系图 (Entity Relationship)

```
┌─────────────────────┐
│   ProgramProject    │
│  (程序项目)          │
├─────────────────────┤
│ - id: string        │
│ - name: string      │
│ - type: ProgramType │
│ - version: string   │
│ - created: Date     │
│ - modified: Date    │
└──────────┬──────────┘
           │
           │ 1:N
           │
┌──────────▼──────────┐
│      Variable       │
│     (变量)           │
├─────────────────────┤
│ - name: string      │
│ - dataType: DataType│
│ - direction: VarDir │
│ - value: number     │
│ - defaultValue: num │
└──────────┬──────────┘
           │
           │ M:N (引用关系)
           │
┌──────────▼──────────┐
│   FunctionBlock     │
│    (功能块)          │
├─────────────────────┤
│ - id: string        │
│ - type: BlockType   │
│ - position: Point   │
│ - inputs: string[]  │
│ - outputs: string[] │
└──────────┬──────────┘
           │
           │ 1:N
           │
┌──────────▼──────────┐
│  GraphicElement     │
│   (图形元素)         │
├─────────────────────┤
│ - id: string        │
│ - elementType: Type │
│ - position: Point   │
│ - size: Size        │
│ - connections: []   │
│ - visualProps: {}   │
└─────────────────────┘
```

---

## 1. ProgramProject (程序项目)

代表一个完整的 LD 或 CFC 程序。

### 数据结构

```typescript
interface ProgramProject {
  id: string;                    // UUID
  name: string;                  // 程序名称（例如："加法演示"）
  type: 'LD' | 'CFC';           // 程序类型
  version: string;               // 数据模型版本（例如："1.0.0"）
  created: Date;                 // 创建时间
  modified: Date;                // 最后修改时间
  variables: Variable[];         // 变量列表
  functionBlocks: FunctionBlock[]; // 功能块列表
  graphicElements: GraphicElement[]; // 图形元素列表
  compiledCode?: string;         // 编译后的 JavaScript 代码（可选，缓存）
  compileStatus: 'uncompiled' | 'compiled' | 'error'; // 编译状态
}
```

### 验证规则

- **name**: 必填，1-50 字符，支持中文
- **type**: 必须为 'LD' 或 'CFC'
- **version**: 遵循语义化版本（x.y.z）
- **variables**: 至少包含 1 个输入和 1 个输出变量
- **functionBlocks**: 至少包含 1 个功能块（ADD）
- **created <= modified**: 修改时间不能早于创建时间

### 业务规则

1. 新建程序时自动设置 `compileStatus = 'uncompiled'`
2. 任何修改（变量、功能块）后自动设置 `compileStatus = 'uncompiled'`
3. 编译成功后设置 `compileStatus = 'compiled'` 并缓存 `compiledCode`
4. 编译失败设置 `compileStatus = 'error'`

---

## 2. Variable (变量)

代表程序中的数据变量。

### 数据结构

```typescript
type DataType = 'INT'; // 当前仅支持 INT

type VariableDirection = 'INPUT' | 'OUTPUT' | 'INTERMEDIATE';

interface Variable {
  name: string;                  // 变量名（例如："input1", "output"）
  dataType: DataType;            // 数据类型
  direction: VariableDirection;  // 变量方向
  value: number;                 // 当前值
  defaultValue: number;          // 默认值
  readonly: boolean;             // 是否只读（OUTPUT 变量为只读）
}
```

### 验证规则

- **name**:
  - 必填
  - 1-20 字符
  - 仅允许字母、数字、下划线
  - 不能以数字开头
  - 不能使用保留字（如 "var", "function", "return"）
  - 同一程序内唯一

- **dataType**: 当前仅支持 'INT'

- **value / defaultValue**:
  - 必须在 INT 范围内：-32768 到 32767
  - 整数（不允许小数）

- **direction**:
  - INPUT: 用户可修改，作为功能块输入
  - OUTPUT: 只读，作为功能块输出
  - INTERMEDIATE: 中间变量，功能块间传递

- **readonly**:
  - OUTPUT 变量必须为 true
  - INPUT 和 INTERMEDIATE 必须为 false

### 业务规则

1. OUTPUT 变量的 `value` 仅能由程序执行引擎修改
2. INPUT 变量的 `value` 可由用户在运行时动态修改
3. 变量值改变时触发 `变量值变更事件`（事件驱动）
4. 删除变量前检查是否被功能块引用（如引用则拒绝）

---

## 3. FunctionBlock (功能块)

代表图形化编程中的逻辑单元。

### 数据结构

```typescript
type BlockType = 'ADD'; // 当前仅支持 ADD

interface Point {
  x: number; // 像素坐标
  y: number;
}

interface FunctionBlock {
  id: string;            // UUID
  type: BlockType;       // 功能块类型
  position: Point;       // 画布中的位置（左上角）
  inputs: string[];      // 输入变量名列表（引用 Variable.name）
  outputs: string[];     // 输出变量名列表（引用 Variable.name）
}
```

### 验证规则

- **id**: UUID 格式

- **type**: 当前仅支持 'ADD'

- **position**:
  - x, y 必须 >= 0
  - 不能与其他功能块重叠（保留 10px 间距）

- **inputs**:
  - ADD 功能块必须恰好 2 个输入
  - 引用的变量名必须存在于 `ProgramProject.variables`
  - 引用的变量 `direction` 必须为 'INPUT' 或 'INTERMEDIATE'

- **outputs**:
  - ADD 功能块必须恰好 1 个输出
  - 引用的变量名必须存在于 `ProgramProject.variables`
  - 引用的变量 `direction` 必须为 'OUTPUT' 或 'INTERMEDIATE'

### 业务规则

1. ADD 功能块执行逻辑：`outputs[0] = inputs[0] + inputs[1]`
2. 执行过程中进行溢出检测
3. 功能块位置改变时触发图形重绘
4. 删除功能块时自动断开所有连接

---

## 4. GraphicElement (图形元素)

代表画布上的可视化图形（用于 LD 梯形图的触点、线圈等）。

### 数据结构

```typescript
type ElementType =
  | 'CONTACT'      // 触点（LD）
  | 'COIL'         // 线圈（LD）
  | 'BLOCK'        // 功能块外框
  | 'CONNECTION';  // 连接线

interface Size {
  width: number;
  height: number;
}

interface Connection {
  from: string;     // 起点元素 ID 或变量名
  to: string;       // 终点元素 ID 或变量名
  fromPort?: string; // 起点端口（如 "input1", "output"）
  toPort?: string;   // 终点端口
}

interface VisualProperties {
  color?: string;       // 颜色
  strokeWidth?: number; // 线宽
  label?: string;       // 标签文本
  highlighted?: boolean; // 是否高亮（选中状态）
}

interface GraphicElement {
  id: string;                   // UUID
  elementType: ElementType;     // 元素类型
  position: Point;              // 位置
  size: Size;                   // 尺寸
  connections: Connection[];    // 连接关系
  visualProps: VisualProperties; // 视觉属性
  relatedBlockId?: string;      // 关联的功能块 ID（如果是 BLOCK 类型）
  relatedVariableName?: string; // 关联的变量名（如果是触点/线圈）
}
```

### 验证规则

- **id**: UUID 格式

- **elementType**:
  - LD 程序仅允许：CONTACT, COIL, BLOCK, CONNECTION
  - CFC 程序仅允许：BLOCK, CONNECTION

- **position, size**:
  - 所有数值 >= 0
  - size 对于 CONNECTION 类型可以为 0

- **connections**:
  - CONNECTION 类型必须至少有 1 个 Connection
  - BLOCK 类型根据功能块的 inputs/outputs 自动生成端口
  - `from` 和 `to` 必须引用存在的元素或变量

### 业务规则

1. LD 梯形图的 CONTACT 必须水平连接（y 坐标相同）
2. CFC 的 CONNECTION 支持任意方向
3. 选中元素时设置 `highlighted = true`
4. 删除功能块时自动删除关联的 GraphicElement

---

## 5. 关系约束

### Variable ↔ FunctionBlock

- **引用完整性**: 功能块引用的变量名必须存在
- **级联删除**: 删除变量时，先检查是否被功能块引用
- **类型匹配**: 输入端只能引用 INPUT/INTERMEDIATE，输出端只能引用 OUTPUT/INTERMEDIATE

### FunctionBlock ↔ GraphicElement

- **1:1 关系**: 每个功能块对应 1 个 BLOCK 类型的 GraphicElement
- **同步创建**: 创建功能块时自动创建对应的图形元素
- **同步删除**: 删除功能块时自动删除对应的图形元素和相关连接线

### GraphicElement 连接规则

- **无环约束**: CONNECTION 不能形成循环引用（对于简单加法程序不适用）
- **端口唯一性**: 每个输出端口只能连接到一个输入端口
- **悬空检测**: 连接线的两端必须有效（连接到功能块端口或变量）

---

## 6. 状态转换图

### ProgramProject 编译状态

```
  [创建]
    │
    ▼
┌─────────────┐
│ uncompiled  │ ◄───┐
└──────┬──────┘     │
       │            │ [修改变量/功能块]
       │ [编译]     │
       ▼            │
┌─────────────┐     │
│  compiled   │─────┘
└──────┬──────┘
       │
       │ [编译失败]
       ▼
┌─────────────┐
│    error    │
└─────────────┘
```

### Variable 生命周期

```
  [创建]
    │
    ▼
┌─────────────┐
│   defined   │
└──────┬──────┘
       │
       │ [被功能块引用]
       ▼
┌─────────────┐
│   in-use    │
└──────┬──────┘
       │
       │ [解除引用]
       ▼
┌─────────────┐
│   unused    │ ──[删除]──> [销毁]
└─────────────┘
```

---

## 7. 事件模型

### 变量值变更事件

```typescript
interface VariableChangeEvent {
  type: 'variable-change';
  variableName: string;
  oldValue: number;
  newValue: number;
  timestamp: Date;
}
```

**触发时机**:
- 用户修改 INPUT 变量
- 程序执行引擎更新 OUTPUT 变量

**订阅者**:
- 变量监控 UI 组件
- 程序执行引擎（重新计算依赖此变量的功能块）

### 溢出警告事件

```typescript
interface OverflowWarningEvent {
  type: 'overflow-warning';
  variableName: string;
  calculatedValue: number;
  wrappedValue: number; // 回绕后的值
  timestamp: Date;
}
```

**触发时机**:
- 加法结果超出 -32768 到 32767 范围

**订阅者**:
- 警告通知 UI 组件
- 日志记录服务

---

## 8. 数据验证规范

### 输入验证层次

1. **前端验证**（用户输入时）
   - 即时反馈
   - 数据类型检查
   - 范围检查

2. **模型验证**（数据保存前）
   - 完整性检查
   - 关系约束检查
   - 业务规则检查

3. **执行前验证**（编译前）
   - 引用完整性
   - 逻辑正确性
   - 无悬空连接

### 错误消息规范

所有错误消息必须使用简体中文，格式：

```
[错误类型] 变量名 "xxx": 具体错误描述

示例：
[范围错误] 变量 "input1": 值 50000 超出 INT 类型范围（-32768 到 32767）
[引用错误] 功能块 "ADD_1": 输入变量 "temp" 不存在
[类型错误] 功能块 "ADD_1": 输出端不能引用 INPUT 类型变量 "input2"
```

---

## 9. JSON 序列化示例

### 完整程序 JSON

```json
{
  "id": "proj-001",
  "name": "简单加法",
  "type": "LD",
  "version": "1.0.0",
  "created": "2026-01-18T10:00:00Z",
  "modified": "2026-01-18T10:30:00Z",
  "variables": [
    {
      "name": "input1",
      "dataType": "INT",
      "direction": "INPUT",
      "value": 10,
      "defaultValue": 0,
      "readonly": false
    },
    {
      "name": "input2",
      "dataType": "INT",
      "direction": "INPUT",
      "value": 20,
      "defaultValue": 0,
      "readonly": false
    },
    {
      "name": "output",
      "dataType": "INT",
      "direction": "OUTPUT",
      "value": 30,
      "defaultValue": 0,
      "readonly": true
    }
  ],
  "functionBlocks": [
    {
      "id": "fb-001",
      "type": "ADD",
      "position": { "x": 100, "y": 100 },
      "inputs": ["input1", "input2"],
      "outputs": ["output"]
    }
  ],
  "graphicElements": [
    {
      "id": "ge-001",
      "elementType": "BLOCK",
      "position": { "x": 100, "y": 100 },
      "size": { "width": 80, "height": 60 },
      "connections": [],
      "visualProps": {
        "label": "ADD",
        "color": "#4CAF50"
      },
      "relatedBlockId": "fb-001"
    }
  ],
  "compileStatus": "compiled",
  "compiledCode": "function executeProgram(vars) { vars.output = vars.input1 + vars.input2; return vars; }"
}
```

---

## 10. 索引和查询优化

### 常用查询

1. **按名称查找变量**: `O(1)` 使用 Map 存储
   ```javascript
   const variableMap = new Map(variables.map(v => [v.name, v]));
   ```

2. **查找功能块引用的变量**: `O(1)` 通过 inputs/outputs 数组
   ```javascript
   const inputVars = functionBlock.inputs.map(name => variableMap.get(name));
   ```

3. **查找关联的图形元素**: `O(n)` 遍历 graphicElements
   ```javascript
   const blockElement = graphicElements.find(e => e.relatedBlockId === blockId);
   ```

### 性能考虑

- 变量数量 < 10，无需复杂索引
- 功能块数量 < 5，线性查找足够
- 图形元素可能较多（每个功能块多个连接线），考虑按 `elementType` 分组

---

## 结论

本数据模型覆盖了规格说明中定义的所有关键实体：

- ✅ 程序项目 (ProgramProject)
- ✅ 变量 (Variable)
- ✅ 功能块 (FunctionBlock)
- ✅ 图形元素 (GraphicElement)

所有实体均包含：
- 完整的数据结构定义
- 验证规则
- 业务规则
- 关系约束
- 事件模型
- JSON 序列化格式

**下一步**: 生成 API/数据合约文档（contracts/）。
