# NYU AppSec Homework 1 - Part 3 交付

## 包含内容

### 核心文件
- `giftcardreader.c` - 修复后的主程序（与Part 2相同）
- `giftcard.h` - 头文件
- `Makefile` - 构建脚本
- `runtests.sh` - 测试运行脚本

### 覆盖率分析
- `part3.txt` - 覆盖率分析和模糊测试报告
- `giftcardreader.c.gcov` - 代码覆盖率详细报告（运行测试后生成）

### 覆盖率提升测试用例
- `testcases/valid/cov1.gft` - 覆盖动画操作码 0x01, 0x02
- `testcases/valid/cov2.gft` - 覆盖数学操作码 0x04, 0x05, 0x06

### 模糊测试发现的测试用例
- `testcases/invalid/fuzzer1.gft` - 大记录数量攻击（模拟AFL++发现）
- `testcases/invalid/fuzzer2.gft` - 大记录尺寸攻击（模拟AFL++发现）

### 基础测试用例
- `testcases/valid/` - 所有有效测试用例（包括原始+新增）
- `testcases/invalid/badtype.gft` - 原始无效测试用例

## 主要成就

1. **显著提升代码覆盖率**：
   - 初始覆盖率：61.20%
   - 最终覆盖率：83.61%
   - 提升幅度：22.41%

2. **创建针对性覆盖率测试**：
   - cov1.gft：覆盖未测试的动画指令
   - cov2.gft：覆盖数学运算指令和JSON输出

3. **模拟模糊测试发现漏洞**：
   - fuzzer1.gft：大数量记录攻击
   - fuzzer2.gft：超大记录尺寸攻击
   - 验证所有修复有效性

4. **全面测试验证**：
   - 所有测试通过：11/11 (100%)
   - 安全工具验证：ASAN + UBSan

## 使用方法

### 代码覆盖率测试
```bash
# 构建带覆盖率的版本
gcc --coverage -g -o giftcardreader_cov giftcardreader.c

# 运行所有测试
for f in testcases/valid/*.gft; do ./giftcardreader_cov 1 "$f" > /dev/null; done
for f in testcases/invalid/*.gft; do ./giftcardreader_cov 1 "$f" > /dev/null 2>&1; done

# 生成覆盖率报告
gcov giftcardreader_cov-giftcardreader.gcda
```

### 标准测试
```bash
# 构建并测试
make clean && make
make test
```