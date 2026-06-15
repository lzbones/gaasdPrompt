# C++/MBD 代码改写与测试 Prompt 模板集

本仓库包含一套用于 C++ 控制系统代码改写和测试生成的 Prompt 模板，可按顺序使用以完成从原始代码到规范化 MBD 架构的完整转换。

---

## 📁 文件结构

```
gaasdPrompt/
├── README.md              # 本说明文件
├── 01_cpp_coding.md       # Step 1: C++ 面向过程代码改写规范
├── 02_cpp_testing.md      # Step 2: C++ 测试用例生成与验证
├── 03_mbd_refactor.md     # Step 3: MBD FuncModule 架构重构
└── 04_mbd_testing.md      # Step 4: MBD 专项测试验证
```

---

## 🚀 使用流程

### Step 1: C++ 代码改写
**文件**: `01_cpp_coding.md`

将原始 C++ 控制算法代码改写为符合面向过程规范的形态。

**核心规范**:
- 面向过程（Procedural C 风格）开发范式
- 一个函数一个文件（One Function Per File）
- 静态单赋值（SSA）原则
- 单一出口原则（Single Return Point）
- 物理/数学语义导向的元件拆分

**使用方法**:
```
复制 01_cpp_coding.md 的内容作为系统 Prompt，然后提供你的原始 C++ 代码
```

---

### Step 2: C++ 测试生成
**文件**: `02_cpp_testing.md`

为已改写的面向过程代码生成完整的测试体系。

**包含内容**:
- 函数级单元测试（一对一原则）
- 模块级集成测试
- 程序级系统测试
- 测试用例 JSON 格式规范
- CMake 测试配置
- 测试结果可视化（Python + matplotlib）

**使用方法**:
```
复制 02_cpp_testing.md 的内容作为系统 Prompt，然后提供 Step 1 改写的代码
```

---

### Step 3: MBD 架构重构
**文件**: `03_mbd_refactor.md`

将 C++ 代码重构为符合 C++20 FuncModule 架构的形态，接入 MBD 图形化代码生成流。

**核心规范**:
- Traits 五元结构（Input, Output, Param, State, Sub）
- 继承自 FuncModule 基类
- 值语义与依赖注入
- "先配置，后移动"原则
- MBD_AUTO_GEN 注解规范

**使用方法**:
```
复制 03_mbd_refactor.md 的内容作为系统 Prompt，然后提供 Step 1/2 的代码
```

---

### Step 4: MBD 测试验证
**文件**: `04_mbd_testing.md`

为已重构的 FuncModule 架构代码生成专项测试。

**包含内容**:
- Traits 级单元测试（叶子节点）
- 复合模块集成测试（基于 JSON 拓扑）
- 系统级闭环仿真测试
- MBD 回归测试与持续集成
- MBD 测试结果可视化

**使用方法**:
```
复制 04_mbd_testing.md 的内容作为系统 Prompt，然后提供 Step 3 重构的代码
```

---

## 📊 流程图

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Step 1    │ →  │   Step 2    │ →  │   Step 3    │ →  │   Step 4    │
│ C++ 改写     │    │ C++ 测试    │    │ MBD 重构    │    │ MBD 测试    │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
      ↓                  ↓                  ↓                  ↓
  面向过程规范        单元测试/可视化     FuncModule 架构     拓扑验证/闭环仿真
```

---

## 🎯 适用场景

- **控制系统开发**: PID 控制器、滤波器、状态观测器等控制算法的规范化实现
- **嵌入式代码生成**: 符合 MISRA C++ 规范的嵌入式控制代码
- **MBD 模型驱动开发**: 从 Simulink/图形化模型到 C++ 代码的自动化映射
- **形式化验证准备**: 满足静态分析和形式化验证前提的代码结构

---

## 📝 核心规范速查

### 命名规范
| 类型 | 格式 | 示例 |
|------|------|------|
| 函数/变量 | 小驼峰 | `calculateAccel`, `inputValue` |
| 结构体/类 | 大驼峰 | `PidController`, `StateData` |
| 命名空间 | 小写下划线 | `control_system`, `math_utils` |
| 宏/常量 | 大写下划线 | `MAX_VALUE`, `PI_CONST` |

### SSA 原则要点
- 所有局部变量必须使用 `const` 限定符
- 必须在声明时初始化
- 禁止后续重新赋值
- 分支逻辑通过条件表达式合并初始化

### MBD 组装顺序（CRITICAL）
```cpp
// 1. 先实例化子模块并配置参数/状态
Func1 f1(Func1Traits::Param{...});
f1.setState({...});  // ← 必须在 std::move 之前！

// 2. 最后通过 std::move 移动注入
Parent parent(
    ParentTraits::Sub{.f1 = std::move(f1)}
);
```

---

## 🧪 测试覆盖率要求

| 类型 | 覆盖要求 |
|------|----------|
| 关键安全函数 | 100% MC/DC 分支覆盖 |
| 普通业务函数 | ≥80% 行覆盖 |
| MBD 复合模块 | 拓扑覆盖率 + 数据流覆盖率 |

---

## 📄 License

本 Prompt 模板集可自由使用和修改。