#### 准备工作
test
1. 克隆 [HG项目](https://github.com/heavengifts/HG.git)
2. 进入 HG 项目根目录,
3. 执行`cp config_base.yml config.yml`, 复制出一份 config.yml 文件, 做个性化配置  
项目的最终配置是config_base.yml 和 config.yml 的 merge 结果, 其中 config.yml 里的内容会覆盖 config_base.yml里相同的内容.
4. 初始化 HG 项目用到的一些js文件 php apps/command/cli.php command initTasks
5. 如果需要访问后台, 需执行 
```
cd src
npm run build (开发模式下可使用 npm run dev 代替)
```
6. config.yml 中的域名配置需与 hg.conf 中的 server_name 相同, 例如域名是 dev.newhg.com, 则配置如下
```
site:                       
     domain      : newhg
     subdomain   : newhg.com
     domainroot  : http://dev.newhg.com
     langSuffix  : dev
     protocol    : http
 ```

#### 搭建docker环境
```
brew update
brew cask install docker
git clone https://github.com/gengsa/docker-library.git
```

#### 配置 HG 运行环境 
##### 进入 hg-old 环境配置目录 
```
cd docker-library/run/hg-old
```

##### 修改配置文件
修改 docker-compose.yml 中的项目目录, 指向 HG 项目所在目录  
修改暴露出来的端口, 也可以不改  

##### 部署，默认会进行pull
```
docker-compose up -d
```
如果慢的话, 使用下面句子可以加速下载  
docker pull registry.docker-cn.com/library/nginx:stable-alpine  
docker pull registry.docker-cn.com/gengsa/gengsa/php-phalcon:5.6.36-2.0.13

##### 停止和删除
```
docker-compose down
```
##### 注意事项
* 所有以 docker-compose 开头的命令，都需要在 docker-library/run/hg-old 目录中执行
* 如果在容器里面运行的php项目，想使用宿主机的资源，比如mysql，memcached，elasticsearch，需要将项目中 config/config.yml 中的 ip 改为 docker.for.mac.host.internal, 如: 

```
memcache:
    host: docker.for.mac.host.internal
    #host: 172.100.9.239

```

##### 本地配置/etc/hosts
添加 127.0.0.1 dev.newhg.com (如果 hg.conf 中的 server_name 没改的话)  
然后访问 dev.newhg.com:8083就可以了 (8083 就是上面提到的 docker-compose.yml 中的端口)

#### 其他
##### 查看log
```
docker-compose logs -f # 查看全部日志
```

##### 进入php容器，并执行交互式的shell
```
docker exec -it hg-old_php_1 sh
```

##### 直接执行命令，注意后面的命令跟参数的话，不要放在引号里面
```
docker exec -it hg-old_php_1 composer update -vvv
docker-compose exec php php apps/command/cli.php command initTasks
```

##### 安装 bash completion, 可选
```
etc=/Applications/Docker.app/Contents/Resources/etc
ln -s $etc/docker.bash-completion $(brew --prefix)/etc/bash_completion.d/docker
ln -s $etc/docker-machine.bash-completion $(brew --prefix)/etc/bash_completion.d/docker-machine
ln -s $etc/docker-compose.bash-completion $(brew --prefix)/etc/bash_completion.d/docker-compose
```
