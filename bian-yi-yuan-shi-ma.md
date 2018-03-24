# 編譯原始碼

以下流程皆是在CentOS 7下進行，其他系統雖有不同，但都大同小異。

## 編譯前的準備

安裝編譯所需套件

```
$ yum install -y git wget
```

```
$ yum -y groupinstall "Development Tools"
```

## 下載原始碼

### nginx原始碼

目前穩定版本是 1.12.2

```
$ wget https://nginx.org/download/nginx-1.12.2.tar.gz
```

解壓縮

```
$ tar zxvf nginx-1.12.2.tar.gz
```

### Nchan原始碼

```
$ wget https://nchan.io/download/nginx-nchan-latest.tar.gz
$ tar zxvf nginx-nchan-latest.tar.gz
```

## 產生Makefile

bbb

## 編譯與安裝



