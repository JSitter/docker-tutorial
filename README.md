# Docker Tutorial

Docker allows for interaction of the host environment through several means. You can start a simple hello world example by running:

```
docker run -t hello-world hello-world
```

You should get an output similar to this

```
...
Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```

This runs a container using the name `hello-world`.

You can see this container if you run `docker ps`.

## View Containers
Above was run a hello-world container. Even though it automatically exits after it run, the image file still exists on the machine. You can view all the containers of your machine using a handy command:

```
docker ps -a --format "table {{.ID}}\t{{.Status}}\t{{.Names}}"
```

## Auto Remove Containers

Having a lot of containers laying around will start to eat up your hard drive pretty quickly. You can avoid leaving old containers around by using the `--rm` flag.

```
docker run --rm hello-world
```

This will remove the container after it has finished running, leaving you with your hard disk space again.

## Running Other Commands

You can run bash commands in your container with Docker as well.

```
docker run busybox ls -la
total 44
drwxr-xr-x   18 root     root          4096 Mar 18 17:06 .
drwxr-xr-x   18 root     root          4096 Mar 18 17:06 ..
-rwxr-xr-x    1 root     root             0 Mar 18 17:06 .dockerenv
drwxr-xr-x    2 root     root         12288 Mar  9 00:05 bin
drwxr-xr-x    5 root     root           340 Mar 18 17:06 dev
drwxr-xr-x    2 root     root          4096 Mar 18 17:06 etc
drwxr-xr-x    2 nobody   nogroup       4096 Mar  9 00:05 home
dr-xr-xr-x   85 root     root             0 Mar 18 17:06 proc
drwxr-xr-x    2 root     root          4096 Mar  9 00:05 root
dr-xr-xr-x   13 root     root             0 Mar 18 17:06 sys
drwxrwxrwt    2 root     root          4096 Mar  9 00:05 tmp
drwxr-xr-x    3 root     root          4096 Mar  9 00:05 usr
drwxr-xr-x    4 root     root          4096 Mar  9 00:05 var
```

## Passing Environment Variables

Add environment variables to your containers with this command:

```
docker run --rm it -e ENV_FROM_HOST="213" busybox
/ # env
HOSTNAME=8e7672bce5a7
SHLVL=1
HOME=/root
ENV_FROM_HOST=213
TERM=xterm"
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PWD=/
/ #
```

This command sets an environment variable  named `ENV_FROM_HOST`. When run, this command will drop you into the command line interface for the container. By running `env`, you'll see all the set environment variables in the container which will show you that it is set properly.

You can also pass in your machines environment variables as well..

```
$ export VAR_1=1
$ export VAR_2=2
$ docker run --rm -it -e VAR_1="$VAR_1" -e VAR_2="$VAR_2" busybox
```

You'll see that they exist inside the container by running `env` again.

```
/ # env | grep VAR
VAR_1=1
VAR_2=2
```

## Mount Host Directories

Share storage between containers running on the host or have persistence of data using hosted directories.

Create an awesome file on your host bny using this command:

```
$ echo "The oldest tree in the world is over 4000 years old." > ~/data/facts.txt
```

Run the busybox image mounting the data directory.

```
$ docker run --rm -v ~/data:/data busybox cat /data/facts.txt
The oldest tree in the world is over 4000 years old.
```

## Expose ports to the Host Machine

Expose ports to your host machine by using this simple command.

```
docker run --name nginx --rm -d -p 9000:80 nginx
```

This container will expose port 9000 on the host machine. View the nginx instance by visiting `localhost:9000` in your favorite browser.

## Listing Your Containers

List all your running containers by using the `docker ps` command. 

This command has many flags that can be used to give in depth results about the containers on your system.

One useful flag is the `--no-trunc` command. This command will prevent the truncation of text in the output of `docker ps`.

Use:

```
$ docker ps --no-trun
```

This will give out output that is probably harder to read.

```
CONTAINER ID                                                       IMAGE                   COMMAND                                        CREATED             STATUS              PORTS                            NAMES
402da5211cfcfbb21039702d4a225c07cf3457cfbc7b6e475d83ae1d1bf6c520   nginx                   "nginx -g 'daemon off;'"                       5 minutes ago       Up 5 minutes        0.0.0.0:9000->80/tcp             nginx
8b13af6e8c3dd355bd67521fe481ccc2e5efbedf95adc69e9a3d72d623b1b8e3   hdpred                  "python app.py"                                2 days ago          Up 2 days           0.0.0.0:8002->8000/tcp           wizardly_hermann
3f103696ad8c7617c9130a1e6347c5df66d7f85b3d9cb2524fd1c74c7093af04   flask_keras_docker      "python app.py"                                2 days a
```

One way to make it easier to read is by using the `--format` flag.

## Formatting Your Output

Format the output of your docker containers by using this flag.

```
$ docker ps --no-trunc --format '{{.Names}}\t{{.Command}}'
nginx   "nginx -g 'daemon off;'"
```

This is okay, but we can make it better by using the table command.

```
$ docker ps -a --no-trunc --format 'table {{.Names}}\t{{.Command}}'
NAMES               COMMAND
busybox             "cat /data/1.txt"
ecstatic_jones      "cat /data/1.txt"
hopeful_spence      "/hello"
infallible_curie    "ls -la"
nginx               "nginx -g 'daemon off;'"
```

## Filter the Output of the PS command to find more of your containers

There are even more tools to find the container that you need.

```
$ docker ps -a -f "name=busybox" --format 'table {{.ID}}\t{{.Status}}\t{{.Names}}'
CONTAINER ID        STATUS                      NAMES
cc797c61dc21        Exited (0) 11 minutes ago   busybox
```

## The Quieter You Become, the More You Can Hear

Sometimes all that extra text gets in the way. Use the `-q` command to use the output in scripts or cli commands.

```
$ docker ps -aq
cc797c61dc21 67f3cb5a9647 898fb562e535 6dd210fda2d8 2f542e1cb88b
```

## Check Out All Your Containers

Containers that aren't run with the `--rm` command are stored on the host machine until they are manually deleted. View all of those forgotten containers by using the `-a` flag.

```
docker ps -a
```

## Stop, Start, and Restart Containers

It's handy to be able to start, stop, and restart containers in order to do your work.

```
$ docker stop nginx
```

This will stop the nginx server.

```
$ docker ps -a -f "name=nginx" --format 'table {{.Names}}\t{{.Status}}'
NAMES               STATUS
nginx               Exited (0) 2 minutes ago
```

We can see that the container is stopped, lets start it up again.

```
$ docker start nginx
```

Wow, that was easy. Lets restart it.

```
$ docker restart nginx
```

Easy peasie.


## Attach a Running Container

```
$ docker attach nginx
```

## Remove Containers For the Greater Good

```
$docker rm container_image_name
```

Easy as that if the container is not active. Containers can be force removed by using the `-f` flag

```
$ docker rm -nginx
Error response from daemon: You cannot remove a running container
3dbffa955f906e427298fbeb3eadfd229d64365dd880c9771a31b0aedb879d6d.
Stop the container before attempting removal or use -f

$ docker rm -f -nginx
nginx
```

## Remove All The Containers!

```
docker rm -r $(docker ps -aq)
```

## Execute command inside container

