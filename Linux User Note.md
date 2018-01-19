# linux 使用

## 一些命令
1查看内核版本和信息
uname -a  

2查看发行版本
lsb_release -a 

3 修改源 Debian内核
Kali经历了1.0到2.0，再到现在Rolling版本的转变，三者对应着三种不同的源。建议使用最新的Rolling版本。

```
vi /etc/apt/sources.list 

```

```

#中科大kali源
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb http://mirrors.ustc.edu.cn/kali-security kali-current/updates main contrib non-free
deb-src http://mirrors.ustc.edu.cn/kali-security kali-current/updates main contrib non-free

#阿里云kali源
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
deb http://mirrors.aliyun.com/kali-security kali-rolling/updates main contrib non-free
deb-src http://mirrors.aliyun.com/kali-security kali-rolling/updates main contrib non-free

#官网kali源
deb http://http.kali.org/kali kali-rolling main contrib non-free
```



