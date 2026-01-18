# Quickstart Guide: IEC61131-3 LD/CFC Addition Example

## 快速开始

### 1. 查看示例

```bash
examples/ld/addition.xml      # LD梯形图程序
examples/ld/addition.md       # LD说明文档
examples/cfc/addition.xml     # CFC功能块图程序
examples/cfc/addition.md      # CFC说明文档
```

### 2. 导入到开发工具

**CODESYS / TIA Portal / CX-Programmer**:
- File → Import → PLCopen XML
- 选择对应的.xml文件

### 3. 运行测试

参考 `test-cases.json` 中的测试用例验证功能

### 4. 学习要点

- LD与CFC的表达方式差异
- INT类型溢出回绕行为
- PLCopen XML标准格式
