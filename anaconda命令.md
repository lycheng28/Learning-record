添加源 

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
源地址
	阿里云 https://mirrors.aliyun.com/pypi/simple/
	中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
	豆瓣(douban) http://pypi.douban.com/simple/
	清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
	中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/
```

设置搜索时显示通道地址 

```
 conda config --set show_channel_urls yes
```

查看当前cofig  

```
conda config --show
```

查看添加的镜像

```
conda config --get channels
```

删除源

```
conda config --remove-key channels
```

安装包

```
conda install xxx
```

