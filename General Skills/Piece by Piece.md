# Piece by Piece — General Skills (Easy) | picoCTF 2026

> SSH 登录远程机，把五个文件碎片按序拼接成 zip 并用密码解压，拿到 flag。

## 题目描述

After logging in, you will find multiple file parts in your home directory. These parts need to be combined and extracted to reveal the flag. SSH to chatelaine.cylabacademy.net:35869 and login as ctf-player with password a630e1f8.

**翻译**：登录远程机后，家目录里有多个文件碎片，需要拼接 + 解压才能看到 flag。

## 解题过程

### 1. SSH 登录

```bash
ssh ctf-player@chatelaine.cylabacademy.net -p 35869
```

密码 `a630e1f8`。登录成功后提示符变成 `ctf-player@academy-chall`，说明已经在那台远程机器里了。

### 2. 查看家目录

```bash
ls -la
```

看到 `instructions.txt` 和 `part_aa` ~ `part_ae` 五个碎片（4 个 51 字节 + 1 个 35 字节）。

### 3. 读说明书

```bash
cat instructions.txt
```

提示：碎片拼起来是个 **zip**；解压密码是 `supersecret`；解压后 flag 在文本文件里。

### 4. 验证碎片顺序

```bash
cat part_aa
```

开头是 `PK`——zip 文件的**魔数**（magic bytes），说明 `part_aa` 就是压缩包第一块，按 aa→ae 的字母序拼接即可。

### 5. 按序拼接

```bash
cat part_aa part_ab part_ac part_ad part_ae > flag.zip
```

`cat` 多个文件会按顺序连续输出，`>` 重定向存成 `flag.zip`。

> 这台精简容器里没有 `file` 命令（command not found），改用 `head -c 2 flag.zip` 看开头两个字节验证是 `PK`。

### 6. 带密码解压

```bash
unzip -P supersecret flag.zip
```

解出 `flag.txt`，`cat` 得到 flag。

## Flag

`academy{z1p_and_spl1t_f1l3s_4r3_fun_aefce0ac}`

## 学到的东西

- **SSH 远程登录**：`ssh 用户名@主机 -p 端口`，与 `nc` 连服务的区别
- **`cat A B C > 目标`**：把多个文件按顺序拼成一个文件
- **魔数**：`PK`=zip、`%PDF`=PDF……`file` 命令本质就是查魔数
- **`unzip -P 密码`**：带密码解压 zip
- 精简容器里常用工具可能缺失，用 `head -c` 看开头字节替代 `file`
