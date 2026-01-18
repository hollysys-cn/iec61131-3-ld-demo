# Research: IEC61131-3 LD/CFC Addition Example

**Date**: 2026-01-18
**Purpose**: 解决Technical Context中的所有NEEDS CLARIFICATION项

## 决策总结

| 决策项 | 选择方案 | 理由 |
|--------|----------|------|
| 实现语言 | 手动创建XML | 零依赖、最简单、易理解 |
| 主要依赖 | 无运行时依赖 | 符合简洁原则 |
| 测试工具 | xmllint（可选）+ 手动测试 | 最小工具集 |
| 目标平台 | IEC61131-3工具 + Web浏览器（可选）| 多层次方案 |
| XML格式 | PLCopen XML v2.01 | 标准格式，广泛支持 |

## 核心技术决策

### 1. XML文件手动创建（无代码生成工具）

**决策**: 手动创建PLCopen XML文件

**理由**:
- 项目范围小（仅2个示例程序）
- 零运行时依赖
- XML文件更易于学习者理解
- 无需人工审批第三方库

**拒绝的替代方案**:
- Python + lxml: 过度设计
- JavaScript + xml2js: 增加依赖

### 2. 最小依赖方案

**核心依赖**:
1. **PLCopen XML Schema v2.01** - 参考标准（非运行时）
2. **xmllint（可选）** - XML验证（MIT许可）

**交互式运行**: 延期至Phase 1决定
- 方案A: 纯文档 + 手动测试（MVP）
- 方案B: 简单HTML页面（增强）

### 3. 测试策略

**XML验证**: xmllint命令行工具
**功能测试**: 手动测试 + JSON测试用例
**验证方法**: 导入到CODESYS等工具运行

## IEC61131-3 最佳实践

### LD梯形图结构

```
PROGRAM Addition_LD
  VAR_INPUT
    A : INT;   (* 加数1，范围-32768到32767 *)
    B : INT;   (* 加数2 *)
  END_VAR
  VAR_OUTPUT
    Result : INT;   (* 和，可能溢出回绕 *)
  END_VAR

  (* Ladder Logic *)
  LD    A
  ADD   B
  ST    Result
END_PROGRAM
```

### CFC功能块图结构

```
PROGRAM Addition_CFC
  VAR
    ADD_Block : ADD;
  END_VAR

  (* Data Flow *)
  ADD_Block.IN1 := A;
  ADD_Block.IN2 := B;
  Result := ADD_Block.OUT;
END_PROGRAM
```

### INT溢出行为

标准回绕（Wrap-around）:
- `32767 + 1 = -32768`（正向溢出）
- `-32768 - 1 = 32767`（负向溢出）

**测试用例**:
```json
{
  "test_cases": [
    {"A": 10, "B": 20, "Expected": 30},
    {"A": -5, "B": 3, "Expected": -2},
    {"A": 32767, "B": 1, "Expected": -32768},
    {"A": -32768, "B": -1, "Expected": 32767}
  ]
}
```

## 工具兼容性

**目标工具**: CODESYS, TIA Portal, CX-Programmer

**兼容性要点**:
- PLCopen XML v2.01（最广泛支持）
- 标准数据类型（INT, BOOL等）
- UTF-8 without BOM编码
- LF换行符

## 技术风险评估

| 风险 | 等级 | 缓解措施 |
|------|------|----------|
| XML格式复杂 | 中 | 参考官方示例 + xmllint验证 |
| 工具兼容性 | 低 | 使用标准格式，避免扩展特性 |
| 交互实现复杂度 | 低 | 采用最简方案，可选增强 |

## Phase 0 结论

**所有NEEDS CLARIFICATION已解决**:
- ✅ Language/Version: XML 1.0（核心），HTML5+JS（可选）
- ✅ Primary Dependencies: 无运行时依赖
- ✅ Testing: xmllint（可选）+ 手动测试
- ✅ Target Platform: IEC61131-3工具 + Web浏览器（可选）

**技术栈**:
- 核心: PLCopen XML v2.01
- 文档: Markdown（中文）
- 验证: xmllint
- 测试: JSON + 手动验证

**宪章合规**:
- ✅ 零运行时依赖
- ✅ 无需第三方库审批
- ✅ 工作空间隔离
- ✅ 简体中文文档

**准备进入Phase 1**: ✅
