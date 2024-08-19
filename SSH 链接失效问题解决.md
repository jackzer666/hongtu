# SSH 链接失效问题解决

[参考资料传送门1](https://blog.csdn.net/qq_53528578/article/details/120897053)

[参考资料传送门2](https://www.jianshu.com/p/d34a7369c85c)

## 生成密钥对

**`ssh-keygen -t rsa -b 4096 -C "xxxxxx@email.com"`用于生成密钥对**

* `ssh-keygen`: 这是用于生成、管理和转换认证密钥的命令。

* `-t rsa`: 指定密钥类型为 RSA（Rivest–Shamir–Adleman），一种常见的非对称加密算法。

* `-b 4096`: 指定密钥的长度为 4096 位。较长的密钥长度通常提供更高的安全性。

* `-C "xxxxxx@email.com"`: 提供一个注释（通常是电子邮件地址），以便在多个密钥时可以更容易地识别这个密钥。

生成过程当提示"Enter a file in which to save the key”时，点击Enter键即可使用默认路径来保存公钥与私钥文件，在mac地址中的 ~/.ssh/ 文件夹下；要求输入一个密码，可以不输入，直接点击Enter键继续。

## 拷贝公钥文件

将 ~/.ssh/id_rsa.pub中的内容拷贝至gitlab SSH密钥进行添加

## 问题处理

git使用ssh拉取时报错 Permission denied (publickey,gssapi-keyex,gssapi-with-mic)，尝试使用以下方案解决：

1.  `ssh -vvv git@xxxx` 使用SSH协议连接到服务器，输出详细的调试信息，发现访问/etc/ssh/目录下找不到相关文件

2. 修改 /etc/ssh/ssh_config文件中没有对应配置，补充以下代码

   ```bash
    Host *
       IdentityFile ~/.ssh/id_rsa  # 写明 key 的位置
       HostkeyAlgorithms +ssh-rsa  # 使 openssh 支持 rsa 算法
       PubkeyAcceptedAlgorithms +ssh-rsa
       PubkeyAcceptedKeyTypes +ssh-rsa
   ```

3. 检查是否链接成功

   ```
   ssh -T git@gitlab.com
   ```

4. 尝试修改本地文件的权限

   确保 `~/.ssh` 目录及其内容的权限正确。一般来说，私钥文件权限应该是 `600`，`.ssh` 目录权限应该是 `700`