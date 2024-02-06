```bash
ssh-keygen -t ed25519 -C "Gitee SSH Key"
# ssh-keygen -t rsa -C
-t key 类型
-C 注释
ls ~/.ssh/
私钥文件 id_ed25519
公钥文件 id_ed25519.pub
cat ~/.ssh/id_ed25519.pub
# 通过 ssh -T 测试，输出 SSH Key 绑定的用户名：
ssh -T git@gitee.com
ssh -T git@github.com
git remote add origin https://github.com/Sbgdhdtfhcjgf/git-test.git
# 重命名本地分支
git branch -M main
git push -u origin main
```

## git branch -m 分支改名

```bash
# 查看当前分支
git branch
# 查看所有的远程分支
git branch -a
# 当前分支重命名
git branch -m gg
# 删除远程分支，因为本地的分支名称和远程的分之一名称对应不上了


```

