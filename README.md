# docker-test
Docker Test 1LB-2SINATRA-1REDIS

###Description:

Deploy a loadbalanced ruby application backed by a redis database, using docker.

The diagram: 
    
                              +--------------+
                    +--------->              +----------+
                    |         |   RUBY APP   |          |
             +------+-+       |   TCP/4567   |     +----v------+
      PUBLIC |        |       +--------------+     |           |
    +-------->   LB   |         INTERNAL           |   REDIS   |
             | TCP/80 |                            |  TCP/6579 |
             +------+-+       +--------------+     +----^------+
                    |         |              |          |
                    |         |   RUBY APP   |          |
                    +--------->   TCP/4567   +----------+
                              +--------------+


Requirements:
 - Linux Docker Host or Docker Machine available (Tested on OSX/Windows - Docker Toolbox)
 - Docker Compose (Docker Toolbox)
 - Basic usage of scripting commands.
 - Some shell CLI: git shell, cygwin or babun (if you are runing in Windows)

 * Software and libraries necessary (in the containers)
   - redis 
   - sinatra
   - haproxy
   - ruby
   - development tools

### Approach

 For this test we will work on different ways:
 - Dockerfiles
 - docker-compose
 
The idea is to get more familiar with the usage of some docker and docker-compose commands:
 - docker build
 - docker run
 - docker-compose
 
For the sake of this test, the  containers will be running in a single host, in Docker Machine using Virtualbox, so please if you are running that on Windows or OSX, install it first from:

 OSX - https://github.com/docker/toolbox/releases/download/v1.11.1b/DockerToolbox-1.11.1b.pkg
 
 WINDOWS - https://github.com/docker/toolbox/releases/download/v1.11.1b/DockerToolbox-1.11.1b.exe
 
 Linux: https://docs.docker.com/engine/installation/
 
 Then create your dev/test machines with the following command:
 ```
 $ docker-machine create -d virtualbox docker-machine-01
 ```
 
 Once the machine is running, load the variables to your current terminal session:
 
 ```
  $ eval $(docker-machine env docker-machine-01)
  $ docker-machine ls
   NAME                ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
   docker-machine-01   *        virtualbox   Running   tcp://192.168.99.100:2376           v1.11.1
 ```
 !! Take note of the IP address of the Docker Machine !!

## Let's go!
With that in place, is time to try something.
In order to create/build the images, we will use a simple script to automate this task.
 - Assure that the Dockerfiles in both app and lb folders are correct.  
 - Get familiar with the names and variables, even that is a very simple case. We will need it later.

Clone this repo run the following commands:

 ```
  $ cd docker-test
  $ git checkout test
  $ ./build_ct app
  $ ./build_ct lb
   .... You may see lots of output
  $ docker images
  REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
  3scale/lb                        latest              889ac2e35749        5 seconds ago       139.1 MB
  3scale/helloapp                  latest              bb99f9953d98        2 minutes ago       841.8 MB
 ```

With the images on place, now let's manually spin up the containers in order of dependencies:
 
 1 - Redis
 
 ```
  $ docker run --name redis --hostname redis -d redis
  6e913f392eb81a2eec3bf95595377224c99f018abae93359568ba0821a963c66

  $ docker ps
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS
  6e913f392eb8        redis               "docker-entrypoint.sh"   17 seconds ago      Up 16 seconds       6379/tcp
 ```

 2 - Ruby workers
 ```
  $ docker run -d --name appservera --link redis --link lb 3scale/helloapp
  97f2b55cf7f9717b55b3ce10bf939370e06cbd81b5f9f6238f89ccab9203c525
  $ docker run -d --name appserverb --link redis --link lb 3scale/helloapp
  fffbddd3a6c51a0242e46a47ccffd0243b18b824f2f8261d19483713a1b1a27a
 ```

 3 - HAproxy
 ```
  $ docker run --name lb --link appservera --link appserverb -e APP_1="appservera" -e APP_2="appserverb" -p 80:80 -p 70:70 -d 3scale/lb
  cfc5133a07179c7e451456146b7c703d80b89bff3fbff3241a7b3826aa9b9438
  $ docker ps
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS        
  cfc5133a0717        3scale/lb           "/docker-entrypoint.s"   5 seconds ago       Up 5 seconds        0.0.0.0:70->70/tcp, 0.0.0.0:80->80/tcp
 ```
 
 OK, for now we just want to know if the containers can start.
 
### Compose

Our intention now is having the images created, create a way of provison and link the pieces together.

For this we will use docker-compose, that will allow us to create the environment as infrastructure as code.

So now, let's "compose" the proposed scenario.

Take a look at the docker-compose.yml file and change whatever necessary to adapt to your case.

Note: You have to build the images for LB and APP before using docker-compose, the intention here is showing a mix of how to create your test environment. As we already hace created the image following the instructions above, let's assume that you have everything in place.

```
 $ docker-compose up -d
 Creating dockertest_redis_1
 Creating dockertest_appservera_1
 Creating dockertest_appserverb_1
 Creating dockertest_lb_1
```

If everything went well, you should probably have now access to the application running on the port mapped on the Docker Host.

Remember the IP of the Docker Machine Host? In my case I've got the following address: 192.168.99.100.

As we are not in a configuration with IPAM and Service Discovery, I've just added the IP resolving to the hello.test.app.

LINUX/OSX: /etc/hosts

Windows: C:\Windows\System32\drivers\etc\hosts

So, go to your browser and access the page or use the command curl to have an quick look:

```
$ curl http://192.168.99.100
```

Doing it a couple of times you will see the LB worked correctly between both instances.
```
Hello World from a793ef42bcc4
Hello World from ab4d9daaaed7
```


### LOGS

Assuming that all worked as expected, we can see the docker-compose logs of the stack:

```
$ docker-compose logs
Attaching to dockertest_lb_1, dockertest_appserverb_1, dockertest_appservera_1, dockertest_redis_1
lb_1         | <7>haproxy-systemd-wrapper: executing /usr/local/sbin/haproxy -p /run/haproxy.pid -f /usr/local/etc/haproxy/haproxy.cfg -Ds
redis_1      |                 _._
redis_1      |            _.-``__ ''-._
redis_1      |       _.-``    `.  `_.  ''-._           Redis 3.2.0 (00000000/0) 64 bit
redis_1      |   .-`` .-```.  ```\/    _.,_ ''-._
redis_1      |  (    '      ,       .-`  | `,    )     Running in standalone mode
redis_1      |  |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
redis_1      |  |    `-._   `._    /     _.-'    |     PID: 1
redis_1      |   `-._    `-._  `-./  _.-'    _.-'
redis_1      |  |`-._`-._    `-.__.-'    _.-'_.-'|
redis_1      |  |    `-._`-._        _.-'_.-'    |           http://redis.io
redis_1      |   `-._    `-._`-.__.-'_.-'    _.-'
redis_1      |  |`-._`-._    `-.__.-'    _.-'_.-'|
redis_1      |  |    `-._`-._        _.-'_.-'    |
redis_1      |   `-._    `-._`-.__.-'_.-'    _.-'
redis_1      |       `-._    `-.__.-'    _.-'
redis_1      |           `-._        _.-'
redis_1      |               `-.__.-'
redis_1      |
redis_1      | 1:M 02 Jun 15:19:54.803 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis_1      | 1:M 02 Jun 15:19:54.803 # Server started, Redis version 3.2.0
redis_1      | 1:M 02 Jun 15:19:54.803 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_1      | 1:M 02 Jun 15:19:54.803 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis_1      | 1:M 02 Jun 15:19:54.803 * The server is now ready to accept connections on port 6379
appservera_1 | [2016-06-02 15:19:55] INFO  WEBrick 1.3.1
appservera_1 | [2016-06-02 15:19:55] INFO  ruby 2.2.0 (2014-12-25) [x86_64-linux]
appservera_1 | == Sinatra (v1.4.7) has taken the stage on 4567 for development with backup from WEBrick
appservera_1 | [2016-06-02 15:19:55] INFO  WEBrick::HTTPServer#start: pid=1 port=4567
appserverb_1 | [2016-06-02 15:19:55] INFO  WEBrick 1.3.1
appserverb_1 | [2016-06-02 15:19:55] INFO  ruby 2.2.0 (2014-12-25) [x86_64-linux]
appserverb_1 | == Sinatra (v1.4.7) has taken the stage on 4567 for development with backup from WEBrick
appserverb_1 | [2016-06-02 15:19:55] INFO  WEBrick::HTTPServer#start: pid=1 port=4567
appservera_1 | 192.168.99.1 - - [02/Jun/2016:15:20:43 +0000] "GET / HTTP/1.1" 200 29 0.0050
appservera_1 | dockertest_lb_1.dockertest_default - - [02/Jun/2016:15:20:43 UTC] "GET / HTTP/1.1" 200 29
appservera_1 | - -> /
appserverb_1 | 192.168.99.1 - - [02/Jun/2016:15:20:43 +0000] "GET /favicon.ico HTTP/1.1" 404 473 0.0040
appserverb_1 | dockertest_lb_1.dockertest_default - - [02/Jun/2016:15:20:43 UTC] "GET /favicon.ico HTTP/1.1" 404 473
appserverb_1 | http://192.168.99.100/ -> /favicon.ico
appservera_1 | 192.168.99.1 - - [02/Jun/2016:15:20:45 +0000] "GET / HTTP/1.1" 200 29 0.0022
appservera_1 | dockertest_lb_1.dockertest_default - - [02/Jun/2016:15:20:45 UTC] "GET / HTTP/1.1" 200 29
appservera_1 | - -> /
```

That's it!

### Future improvements:
 As this was just a simple test, when I have time i will be updating the code to use:
 - Kubernetes
 - Provision in AWS or Docker Cloud

Thanks for trying, suggestions are more than welcome!

Good luck



