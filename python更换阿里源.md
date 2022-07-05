python使用阿里源

临时使用

```
pip install -i https://mirrors.aliyun.com/pypi/simple/
```

永久更换

```
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
```

查看设置的结果

```
pip config list
```

取消设置

```
pip config unset global.index-url
```



Python 国内镜像：

http://pypi.doubanio.com/simple/ 豆瓣
http://pypi.hustunique.com 华中科技大学
https://pypi.tuna.tsinghua.edu.cn/simple清华大学开源软件镜像站
https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple 清华大学开源软件镜像站
http://pypi.mirrors.ustc.edu.cn/simple/ 中国科学技术大学
https://mirrors.aliyun.com/pypi/simple/ 阿里