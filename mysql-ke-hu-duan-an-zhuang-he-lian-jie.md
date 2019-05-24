---
description: 操作系统是MAC
---

# mysql客户端 安装和连接

## MAC需要安装一个客户端

### 我安装命令是 $ brew install mysql-client 

### 安装完成后 brew安装信息有提示, 按提示操作, 将PATH环境变量加入进去. \(下面是一个例子,不要死复制, 需要查看安装信息!!!\)

```bash
 #我使用的是 zshrc,  如果是 bash 就把后面换成 .bash_profile
 $echo 'export PATH="/usr/local/opt/mysql-client/bin:$PATH"' >> ~/.zshrc 
 $echo ' export LDFLAGS="-L/usr/local/opt/mysql-client/lib"
     export CPPFLAGS="-I/usr/local/opt/mysql-client/include" ' >> .zshrc
```

