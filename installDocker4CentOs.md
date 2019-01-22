# 在CentOS7安装Docker

## 1.查看内核版本

```bash
uname -r
```

- Docker要求CentOS系统的内核版本高于3.1.0

## 2.使用root权限更新yum

```bash
sudo yum update
```

## 3.卸载旧版本

```bash
sudo yum remove docker  docker-common docker-selinux docker-engine
```

## 4.安装需要的软件包

- yum-util提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

## 5.设置yum源

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

## 6.查看所有仓库中所有docker的版本

```bash
yum list docker-ce --showduplicates | sort -r
```

## 7.安装docker

```bash
sudo yum install docker-ce #由于repo中默认只开启stable仓库，所以安装稳定版
sudo yum install <version> #例如 sudo yum install docker-ce-17.12.0.ce
```

## 8.启动并加入开机启动

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

## 9.验证是否安装成功

- 有client和service两部分表示安装启动都成功了

```bash
docker version
```