# Testing Docker Release 19.03.0 Beta 1

[How to install latest Docker 19.03.0 Beta 1 Test Build](https://github.com/collabnix/dockerlabs/blob/master/beginners/install/from-source/README.md)<br>
[Support for ```docker context```](https://github.com/collabnix/dockerlabs/tree/master/beginners/install/from-source#1903-context-feature)<br>
[Support for rootless Docker](https://github.com/collabnix/dockerlabs/blob/master/beginners/install/from-source/README.md#testing-rootless-docker-under-docker-19031)<br>

# How to install latest Docker 19.03.0 Beta 1 Test Build?


## Downloading the static binary archive. 

Go to https://download.docker.com/linux/static/stable/ (or change stable to nightly or test), 
choose your hardware platform, and download the .tgz file relating to the version of Docker CE you want to install.




```
Captain'sBay==>wget https://download.docker.com/linux/static/test/x86_64/docker-19.03.0-beta1.tgz
--2019-04-10 09:20:01--  https://download.docker.com/linux/static/test/x86_64/docker-19.03.0-beta1.tgz
Resolving download.docker.com (download.docker.com)... 54.230.75.15, 54.230.75.117, 54.230.75.202, ...
Connecting to download.docker.com (download.docker.com)|54.230.75.15|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 62701372 (60M) [application/x-tar]
Saving to: ‘docker-19.03.0-beta1.tgz’
docker-19.03.0-beta1.tgz  100%[=====================================>]  59.80M  10.7MB/s    in 7.1s    
2019-04-10 09:20:09 (8.38 MB/s) - ‘docker-19.03.0-beta1.tgz’ saved [62701372/62701372]
```

## Extract the archive

You can use the tar utility. The dockerd and docker binaries are extracted.


```
Captain'sBay==>tar xzvf docker-19.03.0-beta1.tgz 
docker/
docker/ctr
docker/containerd-shim
docker/dockerd
docker/docker-proxy
docker/runc
docker/containerd
docker/docker-init
docker/docker
Captain'sBay==>
```

## Move the binaries to a directory on your executable path

It could be such as /usr/bin/. If you skip this step, you must provide the path to the executable when you invoke docker or dockerd commands.

```
Captain'sBay==>sudo cp -rf docker/* /usr/bin/
Captain'sBay==>sudo cp -rf docker/* /usr/local/bin/
```

## Start the Docker daemon:

```
$ sudo dockerd &
```

```
Client: Docker Engine - Community
 Version:           19.03.0-beta1
 API version:       1.40
 Go version:        go1.12.1
 Git commit:        62240a9
 Built:             Thu Apr  4 19:15:07 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.0-beta1
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.1
  Git commit:       62240a9
  Built:            Thu Apr  4 19:22:34 2019
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.2.5
  GitCommit:        bb71b10fd8f58240ca47fbb579b9d1028eea7c84
 runc:
  Version:          1.0.0-rc6+dev
  GitCommit:        2b18fe1d885ee5083ef9f0838fee39b62d653e30
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
Captain'sBay==>
```

## Testing with hello-world

```
Captain'sBay==>sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete 
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest
INFO[2019-04-10T09:26:23.338596029Z] shim containerd-shim started                  address="/containerd-shim/m
oby/5b23a7045ca683d888c9d1026451af743b7bf4005c6b8dd92b9e95e125e68134/shim.sock" debug=false pid=2953
Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/
For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

# Support for ```docker context```

I assume you have Docker already installed on Node-2(10.140.0.3) .You can configure the Docker daemon to listen to multiple sockets at the same time using multiple -H options:

```
sudo dockerd -H unix:///var/run/docker.sock -H tcp://10.140.0.3
```


To test drive, let us first remove available context if any to keep it clean

```
Captain'sBay==>sudo docker context rm -f context2
context2
Captain'sBay==>sudo docker context rm -f context1
context1
Captain'sBay==>sudo docker context rm -f context3
context3
```

## Creating a new Context

```
Captain'sBay==>sudo docker context create --docker host=tcp://10.140.0.3:2375 context2
context2
Successfully created context "context2"
```

##

```
Captain'sBay==>sudo docker context use context2
context2
Current context is now "context2"
```

## Listing out the available contexts

```
Captain'sBay==>sudo docker context ls
NAME                DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOIN
T   ORCHESTRATOR
context2 *                                                    tcp://10.140.0.3:2375                           
    
default             Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                     
    swarm
```

## Inspecting the new Context

```
Captain'sBay==>sudo docker context inspect context2
[
    {
        "Name": "context2",
        "Metadata": {},
        "Endpoints": {
            "docker": {
                "Host": "tcp://10.140.0.3:2375",
                "SkipTLSVerify": false
            }
        },
        "TLSMaterial": {},
        "Storage": {
            "MetadataPath": "/home/ajeetraina/.docker/contexts/meta/53c5077690eb97802a2b4c62bdebcf287d32b1
9fdfe625ed4e215e96fdb42378",
            "TLSPath": "/home/ajeetraina/.docker/contexts/tls/53c5077690eb97802a2b4c62bdebcf287d32b19fdfe6
25ed4e215e96fdb42378"
        }
    }
]
```

## Verify if you are able to access Swarm Mode Cluster running on other node

```
Captain'sBay==>sudo docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      
ENGINE VERSION
w49axti8f1soh6egkcqzoph69 *   node2               Ready               Active              Leader              
19.03.0-beta1
Captain'sBay==>
```

Awesome !

## Switching the Context to PWD

TBD<br>
 
 
## Switching the Context to PWK

TBD<br>
 
# Testing rootless Docker under Docker 19.03.0 Beta 1

End Goal:

```
Start daemon: dockerd-rootless.sh --experimental
Start client: docker -H unix://$XDG_RUNTIME_DIR/docker.sock run ..
```

### Pre-requisite:

- Install Ubuntu 18.10 on Google Cloud Platform

Download the below bits:

- https://download.docker.com/linux/static/test/x86_64/docker-19.03.0-beta1.tgz
- https://download.docker.com/linux/static/test/x86_64/docker-rootless-extras-19.03.0-beta1.tgz


### Steps to install rootless Docker

```
wget https://download.docker.com/linux/static/test/x86_64/docker-rootless-extras-19.03.0-beta1.tgz
```

## Extracting the bits

```
tar xvf docker-rootless-extras-19.03.0-beta1.tgz
```

```
$pwd;ls
/home/ajeetraina/docker-rootless-extras
dockerd-rootless.sh  rootlesskit  vpnkit
```

```
 ls
docker  docker-19.03.0-beta1.tgz  docker-rootless-extras  docker-rootless-extras-19.03.0-beta1.tgz
```

```
dockerd-rootless.sh --experimental
```

## Downloading Client

```
wget https://download.docker.com/linux/static/test/x86_64/docker-19.03.0-beta1.tgz
```

## Extracting the bits

```
tar xvf docker-19.03.0-beta1.tgz
```



## Putting it under right executables

```
cp -rf docker/* /usr/local/bin/
```


## Start client: 


```
docker -H unix://$XDG_RUNTIME_DIR/docker.sock run hello-world
INFO[2019-04-10T17:58:18.279144392Z] shim containerd-shim started                  address="/containerd-shim/moby/497e1a69744999dbd05c095cb3fe1bd614
e2f044f6a4243160ea911698fffdd2/shim.sock" debug=false pid=4185
Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/
For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 ```






