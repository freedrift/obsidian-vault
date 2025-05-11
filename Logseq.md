## Logseq 同步方式

### 新建 Gitee 仓库

使用 Gitee 创建仓库，仓库名称随意：

![[屏幕截图 2025-05-11 171056.png]]

打开 Git Bash，配置自己使用 Git 的身份：

```bash
git config --global user.name 'Gitee的用户名'
git config --global user.email 'Gitee的主邮箱'
```

生成对应身份的 RSA 密钥：

```bash
ssh-keygen -t rsa
```

接着访问主机的用户目录下的 `.ssh` 文件夹，复制 `id_rsa.pub` 文件中的内容。接着到 Gitee 的 `设置>安全设置> SSH 公钥` 将复制的内容粘贴进去。

完成以上操作后后，每次往 Gitee 仓库推送内容时，Gitee 都能通过公钥识别出你是可信赖的用户，从而无需手动验证身份。

### 同步 Logseq 图谱

将创建的 Gitee 仓库克隆到本地你想放置 Logseq 图谱的位置：

`````bash
git clone 仓库地址
`````

或者你也可以直接在已经创建的 Logseq 图谱目录中：

```bash
git init
git remote add origin 仓库地址
```

下载自动提交脚本文件 [Logseq-Git-Sync-101](https://github.com/CharlesChiuGit/Logseq-Git-Sync-101) ，解压后将 `git-hooks` 中的 `post-commit` 和 `pre-commit` 文件复制到 Logseq 图谱下的 `.git/hooks` 文件夹中。

将 `post-commit` 中的 main 改为 master。若使用的是 Github，则无需改变，因为 Github 的默认分支是 main，而 Gitee 的默认分支是 master。

接着在 Logseq 中打开上述目录，设置如下：

![[Pasted image 20250511172931.png]]

这样每隔一段时间，Logseq 就会自动往 Gitee 中上传最新的内容。