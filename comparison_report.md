# C++/MBD 代码改写与测试管线 Prompt 模板修改对比报告

本报告对比了当前工作区版本与 Git 仓库初始提交版本（Commit `1a83e11`）之间的差异。报告按逻辑分类列出了所有的修改内容，并在每类逻辑下按重要性（重要、一般、不重要）进行了排序。

---

## 一、 工程架构与流程调度 (Engineering Architecture & Flow Orchestration)

本类主要涵盖整个 Prompt 框架的物理目录结构规范、文件分类标准、管线运行的阶段划分以及产物交付清单的变化。

### 1. 重要修改
*   **【新增】全管线总调度入口 (00_gaasdPrompt.md)**
    *   **描述**：在根目录下新增了全流程编排 Prompt，作为整个大管线的总控入口。定义了 5 个核心步骤（Step 01 - Step 05）的输入输出依赖关系和自动化循环执行模式，支持 AI 一键执行完整的代码重构、测试与文档生成任务。
*   **【新增】多项目并行工作区设计 (Multi-Project Workspace)**
    *   **描述**：在 `00_gaasdPrompt.md` 与 `script/run_pipeline.py` 中，将原有的单一扁平化代码库结构重构为“多项目并行”的物理工作区结构（例如 Kalman Filter、PID Control 在独立的项目文件夹下并行存在，其下各自维护完整的 C++、MBD、测试和设计文档）。
*   **【重构】物理目录结构规范化与目录分离**
    *   **描述**：将原本扁平的 `include`、`src`、`test` 重构为严格的 C++（`include/cpp`, `src/cpp`, `tests/cppTest`）与 MBD（`include/mbd`, `src/mbd`, `tests/mbdTest`）的二级子目录隔离模式，并对测试用例按模块名建子目录，防止多模块生成时发生文件覆盖。

### 2. 一般修改
*   **【新增】输入边界过滤与只读约束**
    *   **描述**：在 `01_cpp_coding.md` 第一节新增了严格的输入校验。过滤掉了二进制产物、第三方库、协议文件、地图和配置数据；声明了用户输入目录的“只读性”，严禁 AI 原地修改或覆盖用户原始代码。
*   **【新增】参考资料目录规范 (ref/)**
    *   **描述**：在各模块根目录结构中引入了 `ref/` 文件夹（与 `src/` 等同级），用于存放用户提供的参考材料，保证输入信息的物理边界完整。

### 3. 不重要修改
*   **【修正】MBD 交付件清单对齐**
    *   **描述**：在 `00_gaasdPrompt.md` 的交付清单中同步补齐了手动模块验证报告 `[Module]_verify.txt` 与脚本自动生成的 `architecture_check.txt`，消除了清单陈述不一致的问题。

---

## 二、 构建与编译保障 (Build & Compilation Safeguards)

本类主要涵盖 CMake 的构建依赖、C++ 编译器标准的强制声明、Mac 平台文件系统大小写敏感漏洞的防御、以及链接与测试目标的管理。

### 1. 重要修改
*   **【新增】CMake 头文件隔离与防 macOS 大小写冲突约束 (CRITICAL)**
    *   **描述**：在 `00_gaasdPrompt.md`、`02_cpp_testing.md` 和 `05_mbd_testing.md` 中增加强约束，**严禁使用全局 `include_directories()`**。强制要求通过 `target_include_directories()` 实施 C++ 库（仅搜索 `include/cpp`）和 MBD 库（仅搜索 `include/mbd`）的包含路径靶向隔离。有效解决了在 macOS 大小写不敏感文件系统上，`clampValue.hpp`（小驼峰）与 `ClampValue.hpp`（大驼峰）同名冲突引发的编译崩溃。
*   **【新增】C++20 编译标准强制约束**
    *   **描述**：在 `02_cpp_testing.md` 和 `05_mbd_testing.md` 里的 CMakeLists.txt 模板中插入了 `set(CMAKE_CXX_STANDARD 20)` 和 `set(CMAKE_CXX_STANDARD_REQUIRED ON)`，强制所有改写后的程序与测试程序采用现代化 C++20 标准编译。
*   **【新增】CMake 源文件动态注册规范**
    *   **描述**：在两份测试 Prompt 模板的 CMake 章节中添加了 `【源文件注册与编译要求】` 强约束，指示 AI 每次生成/重构新源文件时，必须将其主动注册进对应的静态库源文件列表中，彻底解决了新增文件导致集成测试阶段报链接未定义符号的问题。

### 2. 一般修改
*   **【修正】编译预校验命令参数补全 (Step 0)**
    *   **描述**：补全了 `02_cpp_testing.md` 和 `05_mbd_testing.md` 中 `Step 0` 预检查阶段的裸 `g++` 编译命令，加入了 `-std=c++20` 标准声明和 `-Iinclude/cpp`、`-Iinclude/mbd` 头文件检索路径。解决了预检查阶段因找不到头文件而误报失败的漏洞。
*   **【修正】C++ 测试链接符号冲突**
    *   **描述**：在 `02_cpp_testing.md` 的测试程序模板中，将包含被测代码的 `#include "../src/calculateAcceleration.cpp"` 修正为规范的头文件包含 `#include "[FunctionName].hpp"`，避免了因重复定义（Duplicate Symbols）导致的 CMake 静态库链接报错。

### 3. 不重要修改
*   **无**

---

## 三、 健壮性与语法约束 (Robustness & Coding Syntax Constraints)

本类主要涵盖 AI 生成 C++ 代码时的安全编码规范、静态单赋值风格（SSA）、Doxygen 注释规范、以及标准数学库函数的严格依赖。

### 1. 重要修改
*   **【新增】静态单赋值（SSA-like）与单一出口风格要求**
    *   **描述**：在 `01_cpp_coding.md` 第三节增加了非常严格的变量和控制流约束：要求局部变量必须声明时初始化、优先声明为 `const`、同一语义变量连续变换使用递增后缀（如 `speed0`, `speed1`）；严禁在循环体和函数体中间提前使用 `return`、`break` 或 `continue`，确保生成代码对编译器极其友好且极易进行静态形式化验证。
*   **【新增】业务类 Class 拆解为值语义结构体规范**
    *   **描述**：在 `01_cpp_coding.md` 与 `04_mbd_refactor.md` 中，规范了将原代码中的业务 `class` 解构为 `Input`、`Output`、`Param`、`State`、`Sub` 结构体和纯独立函数的标准，严禁在类中保留任何形式的指针或手动管理生命周期。
*   **【新增】禁止手写基础数学算法约束**
    *   **描述**：强制 AI 优先复用 C++ 标准库中的数学运算函数（如 `std::sqrt`、`std::fabs` 等），严禁 AI 自己手写逼近迭代算法（如牛顿法），保证底层算法的精确性与可证性。

### 2. 一般修改
*   **【新增】Doxygen 四级分类规范化注释**
    *   **描述**：在 `01_cpp_coding.md` 第七节新增了强制的 Doxygen 函数头模板。要求包含 `@brief`、`@cn_name`、`@tag_level0` 至 `@tag_level3`（必须对应《基础模块组件目录.txt》中的四级分类体系），规范了行尾字段注释的物理单位说明。
*   **【修正】std::clamp 头文件包含举例纠偏**
    *   **描述**：在 `01_cpp_coding.md` 与 `04_mbd_refactor.md` 中追加了专门针对 C++17 特性 `std::clamp` 的头文件依赖说明（明确其属于 `<algorithm>` 库而不是 `<cmath>`），以举例形式强力纠正了 AI 容易将限幅运算误分入数学库的认知偏差。

### 3. 不重要修改
*   **【新增】头文件保护规范**
    *   **描述**：要求所有头文件必须首行使用 `#pragma once` 保护，禁止使用传统的 `#ifndef / #define`。

---

## 四、 自动化脚本与工具链 (Automation Scripts & Toolchains)

本类主要涵盖提升全自动测试和设计文档编译管线在 macOS/Linux 上健壮性的辅助 Python 脚本、环境隔离、以及 LaTeX 编译器的设置。

### 1. 重要修改
*   **【新增】LaTeX 设计文档生成 Prompt (03_design_doc_gen.md)**
    *   **描述**：全新设计的 LaTeX 格式函数设计文档模板生成 Prompt，详细定义了 Doxygen 注释到 LaTeX 章节、Mermaid 到 TikZ 图表的转换规范。
*   **【新增】MBD 架构规范合规性校验脚本 (check_funcmodule_arch.py)**
    *   **描述**：使用 Python 编写的静态分析工具。自动校验重构后的 MBD 代码是否符合 `FuncModule` 继承、构造函数引入、裸指针限制、以及 `run()` 方法签名的合规性，在正式构建前拦截非法代码。
*   **【重构】自动化管线脚本 (run_pipeline.py)**
    *   **描述**：重构了管线运行逻辑，由原本的单层循环重构为支持多项目的两层循环调度；同时增加了对画图脚本和文档文件的存在性防崩保护（`exists()` 保护），使管线在部分阶段缺失时仍能平稳运行。

### 2. 一般修改
*   **【新增】跨平台 Python 虚拟环境与路径硬编码 (~/.ai-env)**
    *   **描述**：在 `run_pipeline.py`、`02_cpp_testing.md` 和各 Python 绘图脚本首行的 Shebang 中，统一将 Python 解析器硬编码指向用户要求的 `~/.ai-env/bin/python3`，保障了管线在 Mac 和 Linux 上的无缝移植。
*   **【新增】LaTeX 数学支持宏包包含**
    *   **描述**：在 `03_design_doc_gen.md` 中为 LaTeX 模板追加了 `\usepackage{amsmath}` 与 `\usepackage{amssymb}` 宏包，解决了 AI 自动生成的数学公式（如实数域 $\mathbb{R}$）导致 xelatex 编译崩溃的兼容性问题。
*   **【新增】内置项目忽略文件模板 (.gitignore)**
    *   **描述**：在 `00_gaasdPrompt.md` 中内置了标准 `.gitignore` 模板规范（包含 `**/build/` 和 `**/.DS_Store`，但不忽略 Makefile 与 PDF）。同时删除了根目录下易被遗漏的物理隐藏文件 `.gitignore`，利用提示词内置实现了 100% 自动生成。
*   **【修正】LaTeX 自动化编译静默化 (-interaction=nonstopmode)**
    *   **描述**：在 `compile_latex.py` 中强制使用非交互式模式 `-interaction=nonstopmode` 调用 xelatex，防止编译 LaTeX 出错时进程永久卡死在后台。
*   **【修正】C++ 测试绘图脚本 Python 导入 NameError 修复**
    *   **描述**：在 `02_cpp_testing.md` 的 Python 可视化绘图模板中，补上了缺失的 `from datetime import datetime` 导入声明，消除了保存图片时报运行时未定义错误的 Bug。

### 3. 不重要修改
*   **【修正】TikZ 语法现代化**
    *   **描述**：在 `03_design_doc_gen.md` 中将过时的定位语法 `below of=xxx` 更正为现代的 `below=of xxx`。
*   **【优化】跨平台 Shebang 规范化**
    *   **描述**：将 Python 脚本的首行统一修正为符合 Unix 规范的 `#!/usr/bin/env python3`。
