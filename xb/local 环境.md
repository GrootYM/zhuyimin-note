[docker-compose.yml](<file:////Users/zhuyimin/docker/compose-api-7.3/docker-compose.yml>)
nginx配置： [nginx](<file:////Users/zhuyimin/docker/compose-api-7.3/nginx>)
PHP 配置： [php](<file:////Users/zhuyimin/docker/compose-api-7.3/php>)
redis 配置： [redis](file:////Users/zhuyimin/docker/compose-api-7.3/redis)
rabbitmq配置： [rabbitmq](<file:////Users/zhuyimin/docker/compose-api-7.3/rabbitmq>)
mysql配置： [mysql](file:////Users/zhuyimin/docker/compose-api-7.3/mysql)
Run:  docker-compose up -d


k8s:
k8s部署yaf业务， php+nginx+redis
php :
    version: 7.3
    ext: mysql + redis + yaf + amqp

k8s version:
    1.25.2
kubectl version:
    Client Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.0", GitCommit:"a866cbe2e5bbaa01cfd5e969aa3e033f3282a8a2", GitTreeState:"clean", BuildDate:"2022-08-23T17:36:43Z", GoVersion:"go1.19", Compiler:"gc", Platform:"darwin/arm64"}
    Kustomize Version: v4.5.7
    Server Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.2", GitCommit:"5835544ca568b757a8ecae5c153f317e5736700e", GitTreeState:"clean", BuildDate:"2022-09-21T14:27:13Z", GoVersion:"go1.19.1", Compiler:"gc", Platform:"linux/arm64"}

配置文件路径： [k8s](<file:////Users/zhuyimin/docker/k8s/docker-desktop/yaf>)

