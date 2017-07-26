LET US DOCKER TOGETHER

Docker – Just getting started !

First this you want to check to make sure everything is fine. 
$ docker version
> This command shows if the demon is running and also if the client and server versions match.

Lets start our first container :
$ docker run debian 'ls'
$ docker run debian uname -a
$ docker run debian ipconfig 				<error>

> At this point I asked myself cool. I can run commands on another virtual box !
> Is this all it can do ? Send commands to it and get results?

Lets not send commands and just get to the container. 
$ docker run -i debian
> now you can time the commands yourself .
> Type ls
> exit to get out

Was not like ssh but it was  interactive (-i parameter) . To make it more like a real terminal
lets add -t command (stands for tty)

$ debian run -i -t debian
> I like this more!
>Without exiting this lets open another terminal and see the running container

2$ debian ps
> You can see some details , I want to check the status and nem 
> The name is one way that we can address it to future commads

There are other parameters for run lets look at all of them
$ docker run –help | less

> Give yourself 2-3 Minutes and skim through these commands
> The ones that we are interested for not are as follow:

-i , -t , -d, --name, --hostname, -p, -P, --rm, -v, --link, -e, --entrypoint, --ip, -q, --workdir

> We will cover all these in this chapter.
 
Lets go back to our first terminal we are still in interactive mode in debian
$$ mv /bin /bin2
$$ who 			<error>

> we have root priviliges in the container and we can do whatever we want.


Lets go to terminal 2:
2$ docker diff <name>
C
C
D
A

> Can you guess what C, D, and A stands for? 
> Changed , Deleted, Added.

2$ docker inspect <name>
….

> a lot of info. If you know a little grep you can fillter out most of the information you dont want
2$ docker inspect <name> | grep IPAdd

Lets do it in cleaner more professional way:
2$ docker inspect –format {{.NetworkSettings.IPAddress}} <name>

> This is much cleaner. All information is in JSON format.
> Branches that can be accessed using . To go deeper 

Also we can see what commands did we run on the container 
$ docker logs <name>



But what happens to the containers after you eixt them? Are they deleted? Can we still access them?
$ docker ps -a

> Check the status
> it has all the ones we ran but all are exited.
> The exited ones take some resourses to store the informations but nothing to worry about on our personal computer.
> Like me if you have ODD you can clean them using the following command:

$ docker rm -v $(docker ps -aq -f status=exited)
$ docker ps -a
> -f stands for ?! Yep you gurssed it fillter.
> the $() operator in bash runs a command and spits out the result
> how about -q?

Lets check
$ docker ps -aq -f staus=exited
>it prints our the ID numbers of exited containers and pipes it to docker rm command to remove them.

$ docker ps -a
>Clean as a babies …. :D
To not do this every time you can use –rm command when creating containers in docker run command.

$ docker run –rm debian echo “hi ther!”
$ docker ps -a
> Its not there.

Finding ard remembering docker names are not something you want to do. So just name them yourself!
$ docker run –rm -i -t –name=debian5 –hostnmae=debina5 debian 
2$ docker ps
> now what if I want to make some changes to original debian image and save it

Lets make some changes and save it
$$ apt-get update && apt-get install -y cowsay fortune
$$ /usr/games/cowsay hi dummy

> so we made the changes and we want to save our master peaice
$ docker commit debian5 text/random-cow

lets try it
$ docker run –rm test/random-cow /usr/games/cow hi again

But imagin you made some chamges amd wamted to twick it? Well docker file is to save our cow.
Create an empty directory to work in it ! ( it is important for it to be empty)
$ mkdir random-cow && cd random-cow
$ touch Dockerfile
$ vim Dockerfile

FROM debian:wheezy

RUN apt-get update && apt-get install -y cowsay fortune

:wq

$ docker build -t test/random-cow-dockerfile .

> this is just like the stuff we did previously but better documented and flexible for future changes.
> speaking of changes lets change our container to a blackbox that sends out the wise/funny cow 's ideas

ENTRYPOINT [“ /usr/games/fortune | /usr/games/cowsay”]

:wq
$ docker build -t test/random-cow-dockerfile .
$ docker run –rm test/random-cow-dockerfile

sometimes entrypont is more than one command is more like a scipt.
Lets do that 

$ touch script.sh
….

:wq

COPY ./script.sh /
ENTRYPONT [“/script.sh”]

Do you know git? If so there is something called .gitignore to not include files in repo. Docker has same thing. When you build a container image all the files including the Dockerfiles in the current directory is send to docker demon. You sometimes do not want to send all files ! Use .dockerignore file to specify those files.

Little networking
create a web server | a redis server

docker pull nginx
docker pull redis

docker run –name=myreedis-server -d redis
docker run –rm -i -t –link myredis-server:myredis-server redis /bin/bash
$$ redis-cli -h myredis-server -p 6379
>> PING
>> set 'abc' 123
>> get abc

exit

cat /etc/hosts 			< has myredis-server ip assigned >



Little Storing

in dockerfile you can create a volume in container but not directly connect it to your pc for security resons.

VOLUME /data

using the command like you can.
docker run -v /data debian
docker run –rm -it -v /tmp/dockerdir:/data …

tail -f /data/random-dump
