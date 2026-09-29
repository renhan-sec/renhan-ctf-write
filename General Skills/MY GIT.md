# MY GIT — General Skills (Easy) | picoCTF 2026

## 题目描述
I have built my own Git server with my own rules!
git clone ssh://git@xebec.cylabacademy.net:42944/git/challenge.git
密码：f64d4005
Check the README to get your flag!

## README 关键内容
> Only flag.txt pushed by `root:root@academy` will be updated with the flag.

## 解题思路
服务器只认「身份是 root:root@academy」的推送。
而 git commit 里的 author 信息（名字+邮箱）是**客户端自己填的、没有任何验证**，所以可以直接伪造。

## 步骤
```bash
git clone ssh://git@<host>:<port>/git/challenge.git
cd challenge
git config user.name "root"          # 不加 --global，只改本仓库
git config user.email "root@academy"
echo "hi" > flag.txt
git add flag.txt
git commit -m "push flag as root"
git push origin HEAD
