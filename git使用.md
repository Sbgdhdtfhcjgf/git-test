```bash
ssh-keygen -t ed25519 -C "Gitee SSH Key"
-t key 类型
-C 注释
ls ~/.ssh/
私钥文件 id_ed25519
公钥文件 id_ed25519.pub
cat ~/.ssh/id_ed25519.pub
# 通过 ssh -T 测试，输出 SSH Key 绑定的用户名：
ssh -T git@gitee.com
```

