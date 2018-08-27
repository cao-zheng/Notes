# Github

## git上传至gitHub

> 为了把本地的仓库传到github，还需要配置ssh key。
在本地创建ssh key
`$ ssh-keygen -t rsa -C "your_email@youremail.com"`

1. 直接点回车，说明会在默认文件id_rsa上生成ssh key。然后系统要求输入密码，直接按回车表示不设密码。重复密码时也是直接回车，之后提示你shh key已经生成成功。
2. 然后我们进入提示的地址下查看sshkey文件。 我的电脑的地址是C:\Users\lilu\.ssh，其中lilu是我的电脑的名称。打开id_rsa.pub，复制里面的key。里面的key是一对看不懂的字符数字组合，不用管它，直接复制。
3. 回到github网站，进入Account Settings，左边选择SSH Keys，
Add SSH Key,title随便填，粘贴key。
验证是否成功，在git bash下输入。回车就会看到：
`You’ve successfully authenticated, but
GitHub does not provide shell access` 这就表示已成功连上github。
4. 接下来我们要做的就是把本地仓库传到github上去，在此之前还需要设置username和email，因为github每次commit都会记录他们
`$ git config --global user.name "your name"`
`$ git config --global user.email "your_email@youremail.com"`
5.进入要上传的仓库，右键git bash，添加远程地址
`$ git remote add origin git@github.com:yourName/yourRepo.git`
后面的yourName和yourRepo表示你再github的用户名和刚才新建的仓库，加完之后进入.git，打开config，
这里会多出一个remote “origin”内容，这就是刚才添加的远程地址，也可以直接修改config来配置远程地址。

