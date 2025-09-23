# NYU AppSec Homework 1 - Part 2 交付

## 包含内容

### 核心文件
- `giftcardreader.c` - 修复后的主程序
- `giftcard.h` - 头文件
- `Makefile` - 构建脚本
- `runtests.sh` - 测试运行脚本

### 漏洞分析
- `part2.txt` - 漏洞分析报告（详细说明发现的3个漏洞）

### 测试用例
- `testcases/invalid/crash1.gft` - 触发输入验证漏洞
- `testcases/invalid/crash2.gft` - 触发类型混淆和缓冲区溢出
- `testcases/valid/hang.gft` - 修复后的无限循环测试（现在安全处理）
- `testcases/valid/` - 原始有效测试用例
- `testcases/invalid/` - 原始无效测试用例

### 自动化测试
- `.github/workflows/hello.yml` - GitHub Actions CI/CD配置

### 工具脚本
- `gengift.py` - 礼品卡生成工具
- `genanim.py` - 动画礼品卡生成工具

## 主要成就

1. **发现并修复了关键安全漏洞**：
   - 整数类型混淆错误（行210, 225, 229）
   - 内存分配大小错误（行221）
   - 指针比较逻辑错误（行240）
   - 动画函数无限循环漏洞

2. **创建了针对性测试用例**：
   - crash1.gft 和 crash2.gft 触发程序崩溃
   - hang.gft 导致无限循环（修复后安全处理）

3. **实现了自动化测试**：
   - 所有测试用例通过：7/7 (100%)
   - AddressSanitizer 和 UBSan 验证通过

## 使用方法

```bash
# 构建程序
make

# 运行测试套件
make test

# 单独测试
./giftcardreader.original 1 testcases/valid/example.gft
```