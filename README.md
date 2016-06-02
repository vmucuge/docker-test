docker-test in Ubuntu Linux

##  Cloning this repo
```
 $ git clone https://gitlab.com/vgm/docker-test.git
 Cloning into 'docker-test'...
 remote: Counting objects: 43, done.
 remote: Compressing objects: 100% (42/42), done.
 remote: Total 43 (delta 16), reused 0 (delta 0)
 Unpacking objects: 100% (43/43), done.
 Checking connectivity... done.
```
## Build the helloapp image
```
 $ cd docker-test/
 $ git checkout linux
 $ docker build -t vgm/helloapp app/.
  Sending build context to Docker daemon  5.12 kB
  Step 1 : FROM ruby:2.2.0
  2.2.0: Pulling from library/ruby

  ... LOTS OF OUTPUT ...

  Step 21 : ENTRYPOINT ruby /opt/3scale/hello-app/hello-world.rb
  ---> Running in 195a79f9fcc5
  ---> 5ffe18c0d604
  Removing intermediate container 195a79f9fcc5
  Successfully built 5ffe18c0d604
```

## Build images
```
 $ chmod +x build_ct
 $ ./build_ct all
```

## Run containers manually
```
 $ chmod +x run_ct
 $ ./run_ct
```
Then destroy it to run with docker-compose later
```
 $ chmod +x destroy_ct
 $ ./destroy_ct
```

## docker-compose
With the images created, let's run with docker-compose.

```
$ docker-compose up -d
Creating network "dockertest_default" with the default driver
Pulling redis (redis:latest)...
latest: Pulling from library/redis
51f5c6a04d83: Pull complete
a3ed95caeb02: Pull complete
647d1f6838b5: Pull complete
8152112cb8fb: Pull complete
eba38d612e23: Pull complete
dbe778655086: Pull complete
fec3d49dbc45: Pull complete
78fc0c16bb0c: Pull complete
Digest: sha256:54f2667d0acfc237e16957d3475d769baddceb79fd2a5d467e101cd8c4dde845
Status: Downloaded newer image for redis:latest
Creating dockertest_redis_1
Creating dockertest_appservera_1
Creating dockertest_appserverb_1
Building lb
Step 1 : FROM haproxy
latest: Pulling from library/haproxy
51f5c6a04d83: Already exists
a3ed95caeb02: Pull complete
2af6b107b407: Pull complete
a3251b3100e3: Pull complete
c7652a33f7e1: Pull complete
Digest: sha256:7d64ac1246c278dd90f1dcecbda6e2bed4e52ceead4e601a7a9b874fe4459140
Status: Downloaded newer image for haproxy:latest
 ---> 40d607545393
Step 2 : MAINTAINER Vinicius Mucuge <viniciusmucuge@gmail.com>
 ---> Running in 2a2837330f4a
 ---> fbe1eca00998
Removing intermediate container 2a2837330f4a
Step 3 : ENV PROXY 10.99.164.219:3128
 ---> Running in c63de6f9b3dc
 ---> 36626e0ff9cb
Removing intermediate container c63de6f9b3dc
Step 4 : ENV https_proxy http://$PROXY
 ---> Running in d0a13e8ff8b2
 ---> f43530da2a70
Removing intermediate container d0a13e8ff8b2
Step 5 : ENV http_proxy http://$PROXY
 ---> Running in ee37361c3725
 ---> 431587a56c66
Removing intermediate container ee37361c3725
Step 6 : ENV no_proxy "localhost, 127.0.0.1, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16"
 ---> Running in 4aee7947578b
 ---> 3d4ffc69fb8a
Removing intermediate container 4aee7947578b
Step 7 : ENV APP_1 ""
 ---> Running in e4ac5345eaae
 ---> 913c3b18dea8
Removing intermediate container e4ac5345eaae
Step 8 : ENV APP_2 ""
 ---> Running in 2ffaa0348236
 ---> 788f2188a57a
Removing intermediate container 2ffaa0348236
Step 9 : ADD haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg
 ---> ea1f218e779e
Removing intermediate container a6838fe2991d
Step 10 : RUN /usr/local/sbin/haproxy -c -f /usr/local/etc/haproxy/haproxy.cfg
 ---> Running in cc0b61cc9f4d
Configuration file is valid
 ---> 45eee518eda7
Removing intermediate container cc0b61cc9f4d
Step 11 : EXPOSE 80
 ---> Running in 9ad7c84136fc
 ---> 3185eec445c0
Removing intermediate container 9ad7c84136fc
Step 12 : EXPOSE 70
 ---> Running in 870b6e104833
 ---> 965fcb051950
Removing intermediate container 870b6e104833
Step 13 : EXPOSE 8080
 ---> Running in eb7bb4b74e8c
 ---> 9fe4c5d60671
Removing intermediate container eb7bb4b74e8c
Successfully built 9fe4c5d60671
Creating dockertest_lb_1
```

## Check the composes
```
$ docker-compose ps
         Name                        Command               State                                Ports
----------------------------------------------------------------------------------------------------------------------------------
dockertest_appservera_1   ruby /opt/3scale/hello-app ...   Up      4567/tcp
dockertest_appserverb_1   ruby /opt/3scale/hello-app ...   Up      4567/tcp
dockertest_lb_1           /docker-entrypoint.sh hapr ...   Up      0.0.0.0:70->70/tcp, 0.0.0.0:80->80/tcp, 0.0.0.0:32768->8080/tcp
dockertest_redis_1        docker-entrypoint.sh redis ...   Up      6379/tcp
```
