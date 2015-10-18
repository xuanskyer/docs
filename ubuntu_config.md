# Ubuntu 常用设置
> 系统版本：14.04



## 修改初始root密码

**user@ubuntu:~#:** `sudo passwd`

**Enter new UNIX password:** `输入新密码`

**Retype new UNIX password:** `再次新密码`

显示：`passwd: password updated successfully` 则root用户密码修改成功。


## 重新安装完整版的vim编辑器

> Ubuntu上面默认安装的是vim tiny版本，一般我使用的快捷键都是vim full版本中的。所以需要卸载预装的版本，重新安装

```
sudo apt-get -y remove vim-common

sudo apt-get -y install vim
```

that’s all,enjoy it~


## 设置sudo用户

1. 切换到root用户

	```su - root```

**Password:** `输入root用户密码`

2. 设置/etc/sudoers文件可写

**root@ubuntu:~#:** `chmod u+w /etc/sudoers`

3. 添加sudo用户设置（假设需要设置sudo权限的用户为tester）

	* 打开`/etc/sudoers`文件： `vi /etc/sudoers`
	
	* 找到：`root	ALL=(ALL:ALL) ALL` 这一行，在下面添加一行要sudo的用户设置：
	`tester ALL=(ALL:ALL) ALL`。保存退出。
	
4. 撤销/etc/sudoers文件可写

	`chmod u-w /etc/sudoers`
	


## 安装并开启ssh登录

* 安装ssh-server：`apt-get -y install openssh-server`

* 修改**/etc/ssh/sshd_config**文件：

	注释这一行：`PermitRootLogin without-password`
	
	添加一行：`PermitRootLogin yes`
	
	保存退出。
	
* 重启ssh：`server ssh restart`

## 安装git

`apt-get -y install git`

## 设置PS 配色

1. 在文件 /etc/profile 中添加如下代码：

	```
function parse_git_branch_and_add_brackets {
    git branch --no-color 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\ \[\1\]/'
}
PS1='\[\e[1;32m\]\u\[\e[m\]@\[\e[1;35m\]\h:\[\e[0m\e[1;34m\]\W\[\e[m\]$\[\e[1;31m\]$(parse_git_branch_and_add_brackets)\[\e[m\] '
```

2. 保存后，重载文件：`source /etc/profile`



## paralles 虚拟机里面的Ubuntu设置静态IP

* 设置Parallels里的Ubuntu虚拟机的网络类型为默认适配器

* 在虚拟机里面设置IP分配为手动分配

	

