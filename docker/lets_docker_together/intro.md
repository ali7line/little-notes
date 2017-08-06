#LET US DOCKER TOGETHER

##Just Getting started !

---

First thing you want to check to make sure everything is installed:

`$ docker version`

> This command shows if the demon is running and also check that the client and server versions match. (VPN access is needed to download containers)

Lets start our first container:

`$ docker pull debian`

`$ docker run debian 'ls'`

`$ docker run debian uname -a`

`$ docker run debian ipconfig    # error`

> Very nice. We can run commands in a virtual environment! These containers are minimalistic. It means most packages are not installed by default. These commands are send to a container and after the command ouput, the container is deleted. This functionality is very useful for testing purposes. But this is not all it can do.

Lets not send commands and just get into the container.

`$ docker run -i debian`

`ls`

`exit`


> Container was interactive because of the **"-i"** parameter. 

To make it more like a real terminal lets add **"-t"** command (stands for tty).

`$ docker run -i -t debian`

`ls`

`Ctrl+D`

> Much better!

Lets open another terminal and monitor the running container.

`$ debian run -it debian`

In another terminal

`$ docker ps`

> You can see some details about currently running containers,
> *CONTAINER ID* and *NAME* can be used to address containers for later use.


##Essencial Functions

---

There are many parameters to run with **docker run**. Lets look at them.

`$ docker run –help | less`

> Give yourself 2-3 Minutes and skim through these commands.

The ones that we are interested in for now, are the following:

-i , -t , -d, --name, --hostname, -p, -P, --rm, -v, --link, -e, --entrypoint, --ip, -q, --workdir

> We will cover most of these in this chapter.

Lets create another container in interactive mode:

`$ docker run -it debian`

Inside the container

`# mv /bin /bin2`

> we have root priviliges inside the container and we can do whatever we want.

Lets go to another terminal:

`$ docker ps   # notice the container <name>`

`$ docker diff <name>`

> Can you guess what C, D, and A stand for? Changed , Deleted, Added.

Lets get more info about the currently running container:

`$ docker inspect <name>`

> a lot of information. If you know a little grep you can find the info you are looking for.

`$ docker inspect <name> | grep IPAdd`

> This IP is a local ip, try pinging it from the terminal.

Getting the information can be done in a cleaner more professional way:

`$ docker inspect --format {{.NetworkSettings.IPAddress}} <name>`

> This is much cleaner. All the information is in JSON format. Branches that can be accessed using . To go deeper.

Also we can see what commands we ran on the container.

`$ docker logs <name>`


But what happens to the containers after you eixt them? Are they completely deleted? Can we still access them?

`$ docker ps -a`

> While checking the output it can be seen that all the containers we ran are exited. The exited ones take little resourses to store the informations but nothing to worry about on our personal computer. Like me if you have ODD you can clean them using the following command:

`$ docker rm -v $(docker ps -aq -f status=exited)`

`$ docker ps -a`

> **-f** stands for fillter and the $() operator in bash runs a command and spits out the result, how about -q?

Lets check:

`$ docker ps -aq -f staus=exited`

>It prints out the ID numbers of exited containers and pipes it to docker rm command to remove them.

$ docker ps -a

> All cleaned!

To not do this every time you can use the **--rm** command when creating containers in docker run command.

`$ docker run --rm debian echo “hi ther!”`

`$ docker ps -a`

> Its not there. Deleted completely right after running.

Finding and remembering docker names are not something you want to do. So just name them yourself!

`$ docker run --rm -i -t --name=debian5 --hostnmae=debina5 debian`

> Now, what if I want to make some changes to original debian image and save it.

Lets make some changes and save it:

`# apt-get update && apt-get install -y cowsay fortune`

`# /usr/games/cowsay hi dummy`

> so we made the changes and we want to save our master peaice.

`$ docker commit debian5 test/random-cow`

lets try it:

`$ docker run --rm test/random-cow /usr/games/cowsay hi again`

##Docker Everywhere

---

But imagin you made some chamges amd wamted to twick it? Well docker file is to save our cow.
Create an empty directory to work in it ! ( it is important for it to be empty)

`$ mkdir random-cow && cd random-cow`

`$ touch Dockerfile`

`$ vim Dockerfile`

`FROM debian:wheezy`

`RUN apt-get update && apt-get install -y cowsay fortune`

`:wq`

`$ docker build -t test/random-cow-dockerfile .`

> this is just like the stuff we did previously but better documented and flexible for future changes.
> speaking of changes lets change our container to a blackbox that sends out the wise/funny cow 's ideas

`ENTRYPOINT [“ /usr/games/fortune | /usr/games/cowsay”]`

`:wq`

`$ docker build -t test/random-cow-dockerfile .`

`$ docker run –rm test/random-cow-dockerfile`

>Sometimes entrypont is more than one command is more like a scipt.

Lets run a script in a container:

`$ touch script.sh`

`:wq`

`COPY ./script.sh /`

`ENTRYPONT [“/script.sh”]`

>If you knwow a little git, there is something called .gitignore to not include files in repo. Docker has same thing. When you build a container image all the files including the Dockerfiles in the current directory is send to docker demon. You sometimes do not want to send all files ! Use .dockerignore file to specify those files.

## Sharing Data

---

In *Dockerfile* you can create a volume in a container. To connect it to your host for security/portability reasons it cannot be done through *Dockerfile* directly but through command line instead. In Dockerfile add the following:

`VOLUME /data`

and using the command line you can connect the **VOLUME**:

`$ docker run -v /data debian`

`$ docker run --rm -it -v /tmp/dockerdir:/data`

`# last >> /data/testfile`

In host go to /tmp/dockerdir folder:

`$ tail -f /tmp/dockerdir/testfile`

> The good thing is that after the container is exited the files will still remain on host computer.


