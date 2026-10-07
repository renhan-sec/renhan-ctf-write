# MY GIT — General Skills (Easy) | picoCTF 2026

**题目**:
>I have built my own Git server with my own rules! You can clone the challenge repo using the command below.
>git clone ssh://git@xebec.cylabacademy.net:29237/git/challenge.git
>Here's the password: f64d4005
>Check the README to get your flag!

**分析**：题目给了一个仓库，以及让我Check README去获得flag

**过程**：我去了仓库cat了README，得到了接下来的提示：

>If you want the flag, make sure to push the flag!

>Only flag.txt pushed by root:root@academy will be updated with the flag.

>GOOD LUCK!

**思路**：很明了了，题目希望我伪造root身份来push一个文件flag。

**操作**:

```bash
git config user.name "root"
git config user.email "root@academy"
echo "a" > flag.txt
git add flag.txt
git commit -m "flag"
git push
```

**收获**：了解到了`git`的用法：

|代码|代码拆解|使用效果|
|----|-------|-------|
|git config|git + 配置动作|改配置/身份|
|git add|git + 添加动作|加文件到暂存区|
|git commit|git + 提交动作|保存快照|
|git push|git + 推送动作|推送到服务器|
|git log|git + 日志动作|看历史|

flag:`academy{1mp3rs0n4t4_g17_345y_7d9222cd}`
