# Data Model: IEC61131-3 LD/CFC Addition Example

**Date**: 2026-01-18

## 核心实体

### 1. Program（程序）
- `name`: String - 程序名称
- `type`: "LD" | "CFC"
- `variables`: Variable[]
- `functionBlocks`: FunctionBlock[]

### 2. Variable（变量）
- `name`: String - 变量名（A, B, Result）
- `dataType`: "INT"（-32768到32767）
- `direction`: "INPUT" | "OUTPUT"
- `comment`: String（中文）

### 3. FunctionBlock（功能块）
- `type`: "ADD"
- `inputs`: ["IN1", "IN2"]
- `output`: "OUT"

### 4. TestCase（测试用例）
```json
[
  {"inputs": {"A": 10, "B": 20}, "expected": {"Result": 30}},
  {"inputs": {"A": -5, "B": 3}, "expected": {"Result": -2}},
  {"inputs": {"A": 32767, "B": 1}, "expected": {"Result": -32768}},
  {"inputs": {"A": -32768, "B": -1}, "expected": {"Result": 32767}}
]
```

## PLCopen XML结构

- Program → `<pou pouType="program">`
- Variable (INPUT) → `<inputVars>`
- Variable (OUTPUT) → `<outputVars>`
- FunctionBlock → `<block typeName="ADD">`
