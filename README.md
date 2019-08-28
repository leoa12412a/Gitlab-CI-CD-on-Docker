# Gitlab CI/CD on Docker

Gitlab上如果要CI(持續性整合)或是CD(持續性部屬)，通常會使用gitlab的執行器 Runner，以下介紹docker上的安裝方法，如果是直接使用yum的安裝方法請參考<a href="https://github.com/leoa12412a/Gitlab">這裡</a>

## Step1 : 安裝runner在docker上

其中 {runner-name} 替換成自己要的名字

方法一 :

docker compose
```
version: '3'

services:
  reverse-proxy:
    image: gitlab/gitlab-runner
    container_name: {runner-name}
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /srv/{runner-name}/config:/etc/gitlab-runner

```

方法二 :

docker
```
docker pull gitlab/gitlab-runner
docker run -d --name {runner-name} -v /var/run/docker.sock:/var/run/docker.sock -v /srv/{runner-name}/config:/etc/gitlab-runner --rm gitlab/gitlab-runner
```

## Step2 : 取得Runner url 跟 token

開啟gitlab並登入管理者帳號，管理區塊(Admin Area)->概纜(Overview)->執行器(Runner)，取得url跟token備用

![image](https://github.com/leoa12412a/Gitlab-CI-CD-on-Docker/blob/master/runner_token.png)

## Step3 : 註冊Runner

先開啟gitlab防火牆的port
```
firewall-cmd --zone=public --add-port=10080/tcp --permanent
firewall-cmd --reload
```

開始註冊runner
```
docker exec -it {runner-name} gitlab-runner register
```

輸入註冊的基本資料
```
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):     輸入URL
http://122.147.213.61:10080/
Please enter the gitlab-ci token for this runner:                          輸入token
dg_3Pox8F64apLvPn2pP
Please enter the gitlab-ci description for this runner:                    對runner的描述
[dec0e3aabfa3]: 
Please enter the gitlab-ci tags for this runner (comma separated):         輸入runner的tag

Registering runner... succeeded                     runner=dg_3Pox8
Please enter the executor: docker, docker-ssh, ssh, virtualbox, docker-ssh+machine, kubernetes, custom, parallels, shell,docker+machine:
shell                                                                      輸入想執行的方式  
Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded! 
```

如果沒開啟防火牆的話，post沒辦法過會註冊失敗

![image](https://github.com/leoa12412a/Gitlab-CI-CD-on-Docker/blob/master/runner_register_error.PNG)

## Step4 : Runner是否建立成功

![image](https://github.com/leoa12412a/Gitlab-CI-CD-on-Docker/blob/master/runner_list.PNG)

