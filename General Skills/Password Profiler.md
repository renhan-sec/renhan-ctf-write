# Password Profiler — General Skills (Easy) | picoCTF 2026

> OSINT + 字典攻击：用目标个人信息生成候选密码表（CUPP 思路），逐个算 SHA-1 撞出原密码。

## 题目描述

We intercepted a suspicious file from a system, but instead of the password itself, it only contains its SHA-1 hash. Using OSINT techniques, you are provided with personal details about the target. Your task is to leverage this information to generate a custom password list and recover the original password by matching its hash.

**翻译**：截获的文件里只有密码的 SHA-1 哈希。利用 OSINT（开源情报）拿到目标个人信息，生成候选密码表，逐个算哈希去比对，撞出原密码。

题目提供三个文件：`userinfo`（个人信息）、`hash`（SHA-1 哈希值）、`check_password`（比对脚本）。

## 解题过程

### 1. 查看三个文件

```bash
cat userinfo.txt    # Alice Johnson，昵称 AJ，生日 15-07-1990，伴侣 Bob，孩子 Charlie
cat hash.txt        # 3d92bc3071ba0fb04cf57eaabc3d23135dbce2d0
cat check_password.py
```

`check_password.py` 的逻辑：读 `passwords.txt`（候选密码表，一行一个），逐行算 SHA-1 和 hash.txt 比对，撞上就打印 `academy{密码}`。

### 2. 生成候选字典（踩坑与复盘）

先自己手写生成器（名字 + 合理日期格式的组合），几十万候选全部不中。

复盘：`check_password.py` 注释提到字典是「用 CUPP 生成的」。CUPP（Common User Passwords Profiler）处理生日的方式是——把 `15071990` 拆成 `90 / 990 / 1990 / 5 / 7 / 15 / 07` 七个碎片，再**任意排列拼接**，于是产生 `7515`（`7`+`5`+`15`）这种人类想不到的组合。

按 CUPP 逻辑复刻后撞出密码：`aliceaj7515` = 名字 alice + 昵称 aj + 生日碎片 7515。

### 3. 验证拿 flag

```bash
echo aliceaj7515 > passwords.txt
python3 check_password.py
```

输出：

```
Password found: academy{aliceaj7515}
```

> 正路：`python3 cupp.py -i` 交互式输入个人信息生成 `alice.txt`，复制为 `passwords.txt` 后跑检查脚本，同样能撞出。

## Flag

`academy{aliceaj7515}`

## 漏洞原理 / 安全启示

- SHA-1 是**单向哈希**：同一输入永远得到同一哈希，但无法从哈希反推原文，只能「猜 → 算哈希 → 比对」。
- **字典攻击的威力来自机械全排列**：工具会覆盖人脑想不到的组合。
- 因此「姓名 + 生日碎片」这类密码会被 CUPP 秒破——**别用个人信息设密码**（防御方视角）。

## 踩坑记录

- 自己写的生成器太「人类思维」（只用合理的日期格式），撞不出；机器的无脑全排列才是正道。
- Windows 下载的文件在 WSL 里的路径是 `/mnt/c/Users/lenovo/Downloads`；直接跑 `.py` 会 command not found，要用 `python3 文件名.py`。
