# Install to Ubuntu (18.04)

## install docker
```sh
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable test edge"
sudo apt-key fingerprint 0EBFCD88
sudo apt install docker-ce
```

***

## change permission of docker "to run without sudo" 
```sh
# check permission
cat /etc/group | grep docker

# add user
sudo gpasswd -a ${USER} docker

# conform
cat /etc/group | grep docker

# change permission
sudo chmod 666 /var/run/docker.sock
```
[reference](https://qiita.com/iganari/items/fe4889943f22fd63692a)

***

## pull image
```sh
docker pull ubuntu:18.04
# docker pull ${IMAGE}:${TAG}
```
[official image](https://github.com/docker-library/official-images/tree/master/library)

***

## build from Dockerfile
```sh
docker build -t ${IMAGE}[:${TAG}] ./
# in the directory that has your Dockerfile

docker run -it --rm --name ${CONTAINER} -v /host/path:/container/path ${IMAGE} -p 8080:8080 /bin/bash
# -it: コンテナを作ってログインする (あったほうが良い)
# --rm: コンテナをexitした後，コンテナを破棄する (必要に応じて)
# --name ${CONTAINER}: コンテナの名前を${CONTAINER}に指定する (--rm するならなくて良い)
# -v: /host/pathを/container/pathで共有する（必要に応じて）
# -p: 8080:8080 dockerのポート8080をホストのポート8080と対応させる（あったほうが良い）
===>
Work in the container
===<

# after working in your container 
exit
# 
docker ps -a # list of all container
docker start ${CONTAINER}
docker attach ${CONTAINER}
```
[about_Dockerfile](http://docs.docker.jp/engine/reference/builder.html)

### About docker-compose
```yaml
version: "3"

services:

    uwsgi:
        build: ./app
        container_name: mp-lime-uwsgi
        volumes:
            - ./app:/var/www/
        ports:
            - "3031:3031"
        environment:
            TZ: "Asia/Tokyo"

    nginx:
        build: ./nginx
        container_name: mp-lime-nginx
        volumes:
            - ./nginx/nginx.conf:/etc/nginx/nginx.conf
        links:
            - uwsgi
        ports:
            - "80:80"
        environment:
            TZ: "Asia/Tokyo"
```
[docker-compose](https://docs.docker.com/compose/overview/)


### コンテナで作ったファイル/ディレクトリの権限について
[docker-uid-gid](https://stackoverflow.com/questions/44683119/dockerfile-replicate-the-host-user-uid-and-gid-to-the-image/44683248#44683248)


### このディレクトリについて
このディレクトリはDockerfile, docker-composeのテンプレート置き場
各プロジェクトにコピーして使うこと
ボリュームのマウント場所などを要指定
