## 前言

启动了第一个 docker 程序后，今天我们就来动手进入下该容器。

## 进入容器

### 使用指令进入容器

使用以下指令进入 nginx 容器：

``` bash
docker exec -it nginx bash
```

![进入容器](images/exec/1.png "进入容器")

### 查看容器的操作系统

使用以下指令查看容器的操作系统：

``` bash
cat /etc/os-release
```

![查看容器的操作系统](images/exec/2.png "查看容器的操作系统")

从结果来看，nginx 使用的是 debian 的操作系统，和 ubuntu 同源。

### 查看容器的启动进程

使用以下指令查看容器的启动进程：

``` bash
ps -ef
```

![ps not found](images/exec/3.png "ps not found")

可以发现，容器没装 ps，我们安装一下：

``` bash
apt-get update -y && apt-get install procps -y
```

![install ps](images/exec/4.png "install ps")

安装完成后，再重新查看启动进程：

![启动进程](images/exec/5.png "启动进程")

PID 为 1 的进程是 docker 容器中非常重要的一个进程，如果这个进程退出了，容器就会退出：

``` bash
# 杀掉 PID 为 1 的进程
kill 1

# 查看容器退出 
docker ps -a
```

![容器退出](images/exec/6.png "容器退出")

### 修改容器内容

重新启动并进入容器：

``` bash
# 重启容器
docker start nginx

# 进入容器
docker exec -it nginx bash
```

![重新启动并进入容器](images/exec/7.png "重新启动并进入容器")

查询 nginx 的 index.html 的路径：

``` bash
find / -name index.html
```

![查找路径](images/exec/8.png "查找路径")

修改 index.html 的内容：

``` bash
vim /usr/share/nginx/html/index.html
```

![vim not found](images/exec/9.png "vim not found")

没有安装 vim，安装一下：

``` bash
apt-get install vim -y
```

![install vim](images/exec/10.png "install vim")

继续修改 index.html 的内容：

![修改容器内容](images/exec/11.png "修改容器内容")

查看修改效果：

![查看修改效果](images/exec/12.png "查看修改效果")

## 总结

按照上述的操作，今天应该成功完成进入运行的 docker 容器相关操作。

如有问题可以添加公众号【跬步之巅】进行交流。

![跬步之巅](/images/qrcode.gif "跬步之巅")
