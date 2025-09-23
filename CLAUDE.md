# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是 NYU AppSec 课程作业 1 的代码库，包含一个存在安全漏洞的礼品卡读取程序。主要任务包括：
- 审计和修复 C 代码中的安全漏洞
- 创建测试用例来触发程序崩溃和挂起
- 使用模糊测试（AFL++）提高代码覆盖率
- 使用 GitHub Actions 进行自动化测试

## 核心命令

### 构建命令
```bash
# 构建所有版本的程序
make

# 构建原始版本
make giftcardreader

# 构建 AddressSanitizer 版本
make asan

# 构建 UndefinedBehaviorSanitizer 版本
make ubsan

# 清理构建文件
make clean
```

### 测试命令
```bash
# 运行完整测试套件
make test

# 运行单个测试文件
./giftcardreader.original 1 <test_file.gft>
./giftcardreader.asan 1 <test_file.gft>
./giftcardreader.ubsan 1 <test_file.gft>
```

### 覆盖率测试
```bash
# 构建带覆盖率的版本
gcc --coverage -g -o giftcardreader giftcardreader.c

# 运行测试并生成覆盖率报告
./giftcardreader 1 testcases/valid/*.gft
./giftcardreader 1 testcases/invalid/*.gft
lcov -c -d . -o coverage.info
lcov --remove coverage.info '/usr/*' -o coverage.info
genhtml coverage.info -o coverage_report
```

### AFL++ 模糊测试
```bash
# 使用 AFL++ 编译器构建
afl-gcc -o giftcardreader_fuzz giftcardreader.c

# 运行模糊测试（至少2小时）
afl-fuzz -i input -o output ./giftcardreader_fuzz 1 @@

# 测试生成的用例
for f in output/queue/id*; do ./giftcardreader 1 "$f"; done
```

## 项目架构

### 文件结构
- `giftcardreader.c` - 主程序，包含礼品卡解析和显示逻辑
- `giftcard.h` - 定义礼品卡文件格式和数据结构
- `testcases/valid/` - 应该被正确处理的礼品卡文件
- `testcases/invalid/` - 应该被拒绝的礼品卡文件
- `gengift.py` - 生成标准礼品卡文件
- `genanim.py` - 生成包含动画程序的礼品卡文件

### 礼品卡文件格式
礼品卡是二进制文件，包含：
1. 总字节数（32位）
2. 商家ID（32字节）
3. 客户ID（32字节）
4. 记录数量（32位）
5. 记录数据（可变长度）

每条记录包含：
- 记录字节数（32位）
- 记录类型（1字节）
- 记录内容（金额变更/消息/程序）

### 关键漏洞区域
- 文件读取和边界检查
- 动态内存分配和释放
- 字符串处理（特别是消息记录）
- 动画程序执行（THX-1138处理器模拟）
- 整数溢出和类型转换

## 作业进度跟踪

### Part 1 (20分)
- [ ] 设置 GPG 签名提交
- [ ] 创建 `.github/workflows/hello.yml`

### Part 2 (40分)
- [ ] 创建 `crash1.gft` - 触发第一种崩溃
- [ ] 创建 `crash2.gft` - 触发不同原因的崩溃
- [ ] 创建 `hang.gft` - 导致无限循环
- [ ] 编写 `part2.txt` 解释每个测试用例
- [ ] 修复相关漏洞
- [ ] 设置 GitHub Actions 自动测试

### Part 3 (40分)
- [ ] 测量初始代码覆盖率
- [ ] 创建 `cov1.gft` 和 `cov2.gft` 提高覆盖率
- [ ] 运行 AFL++ 模糊测试（至少2小时）
- [ ] 选择并修复两个模糊测试发现的漏洞（`fuzzer1.gft`, `fuzzer2.gft`）
- [ ] 编写 `part3.txt` 解释发现和修复的漏洞

## 调试技巧

### 使用 AddressSanitizer
```bash
# ASAN 可以检测内存访问错误
./giftcardreader.asan 1 test.gft
# 查看详细的错误报告，包括出错的具体代码行
```

### 使用 GDB 调试
```bash
gdb ./giftcardreader.original
(gdb) run 1 test.gft
(gdb) bt  # 查看调用栈
(gdb) print variable_name  # 查看变量值
```

### 检查程序是否崩溃
```bash
./giftcardreader 1 test.gft
echo $?  # 如果返回值 >= 128，说明程序崩溃
```