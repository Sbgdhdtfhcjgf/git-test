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
>* gg
>  remotes/origin/test
# 当前分支重命名
git branch -m gg
# 删除远程分支，因为本地的分支名称和远程的分之一名称对应不上了
# 见删除远程分支
git push origin --delete test
git push origin --delete fix/authentication
# 重新上传本地分支
git push origin gg
# 本地分支与远程分支相关联
git branch --set-upstream-to origin/gg 
```

# 删除远程分支

```bash
# 这样写不对
git push origin --delete remotes/origin/test
# 直接写远程的分支名称就可以了
git push origin --delete test
```

> ```bash
> error: unable to delete 'remotes/origin/test': remote ref does not exist
> error: failed to push some refs to 'https://github.com/Sbgdhdtfhcjgf/git-test.git
> ```

> ```bash
> To https://github.com/Sbgdhdtfhcjgf/git-test.git
>  ! [remote rejected] test (refusing to delete the current branch: refs/heads/test)
> error: failed to push some refs to 'https://github.com/Sbgdhdtfhcjgf/git-test.git'
> ```

```bash
![remote rejected] dev_test (refusing to delete the current branch: refs/heads/dev_test)
错误原因是删除命令被远程仓库拒绝，因为远程分支 dev_test 是当前分支。
那么，如何查看远程仓库的当前分支是什么？
1.登录你的 github 中进入 Repository 页面，
2.找到对应的远程仓库
3.点击 Setting页面，
4.选择 Branchs，找到 Default branch 项，就能查看和修改默认分支了。
修完完毕之后，不要忘记点击旁边的 update 按钮，update一下才能生效。
```

```bash
git push origin --delete test
# 这样远程和本地的test都被删除了
```

# checkout

```bash
# 如果你还在一个分支上，那么 Git 是不允许你删除这个分支的。所以，请记得退出分支
# git checkout -b 从当前分支clone出一个分支
git checkout -b aa
# 查看当前的本地分支
git branch 
# 查看当前的远程分支
git branch -a
```

# 删除本地分支

```bash
git branch -d <branch>
git branch -d gg
# 查看当前的本地分支
git branch 
```

