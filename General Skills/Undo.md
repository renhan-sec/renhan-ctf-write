# Undo — General Skills (Easy) | picoCTF 2026

## 题目描述
Can you reverse a series of Linux text transformations to recover the original flag?
实例地址：`nc xebec.cylabsacademy.net 43390`（每次启动地址会变）

## 解题过程
交互式题目：每步给出「变换后的 flag」+ 提示，输入对应的逆向命令。

| 步骤 | 提示（做了什么变换） | 我的逆向命令 |
|------|---------------------|--------------|
| 1 | Base64 编码 | `base64 -d` |
| 2 | 文本倒序 | `rev` |
| 3 | 下划线 → 短横线 | `tr '-' '_'` |
| 4 | 花括号 → 圆括号 | `tr '()' '{}'` |
| 5 | 字母 ROT13 | `tr 'A-Za-z' 'N-ZA-Mn-za-m'` |

## Flag
`academy{Revers1ng_t3xt_Tr4nsf0rm@t10ns_050753f6}`

## 学到的东西
- `base64 -d` 解码、`rev` 倒序
- `tr '源' '目标'` 字符替换，引号不能省
- ROT13 是自逆运算（加解密同一条命令）
- 遇到「被加工过」的数据：先识别特征 → 再做相反操作
