# How to deal with data persistence ?

During the previous exercise, you modified a file inside a container, do you think it
is something persistent ?

## Data persistence

- First, create a container HTTPD, modify a file, and then restart it.

```shell
echo "<h1>It works better with Docker...</h1>" > index.html
docker run --name "my-httpd-server" -p="80:80" -d httpd:2.4 # Maybe you need to stop another instance before
docker cp index.html my-httpd-server:/usr/local/apache2/htdocs/index.html
docker restart my-httpd-server
```

Check in your browser if restarting a container affected the data inside ?

- Next, remove this container, and create a new one with the same information.

```shell
docker stop my-httpd-server
docker rm my-httpd-server
docker run --name "my-httpd-server" -p="80:80" -d httpd:2.4
```

And now ? check in your browser what happened.

## Containers are stateless

By default, containers are stateless. 
Their lifetime may be very short.
So they don't have any persistence context !
Data AND configurations will be lost at the container removal.

What about databases or others cache system that need to persist some data ? Don't worry, you can tell docker
to use (read and/or write) on a disk outside the container ! That is what we call **volumes**.

## Volumes

You can tell docker to share a folder or simply a file between the host and a container.
Just like you need a port mapping if you would like to expose a port on your host, you need
to add a volume mapping if you would like to share data.

Use option *-v*, put on the left the folder on the host and put on the right the folder inside the container.

- Create a folder to persist the *index.html* file on your host. 

```shell
mkdir -p $HOME/data/htdocs
echo "<h1>It works better with Docker...</h1>" > $HOME/data/htdocs/index.html
```

- Start a new HTTPD container with the volume option and check in your browser.
Modify the file on your host and check again in your browser.

```shell
docker run --name "my-httpd-server" -p="80:80" -d -v=$HOME/data/htdocs:/usr/local/apache2/htdocs httpd:2.4
# Check in your browser
echo "<h1>It works better with Docker and volumes...</h1>" > $HOME/data/htdocs/index.html
# Check in your browser
```

- Stop and remove current container, start a new one with the same volume option to see what happen.

```shell
docker stop my-httpd-server
docker rm my-httpd-server
docker run --name "my-httpd-server" -p="80:80" -d -v=$HOME/data/htdocs:/usr/local/apache2/htdocs httpd:2.4
# Check in your browser
```

### Types of volumes

On this exercise, you bind mount a simple volume.
But they are others important ways to create and use volumes.
If you have a distributed architecture, you will have to mount NFS or other cloud storage, and
register these volumes with *docker volume* command.

Learn more about volumes by reading this page (16 minutes reading.) https://docs.docker.com/storage/volumes/

### Permissions

Now you know that a container, easily to start and access, can write some data onto your host !
So be careful of what you do, security of data is very important, you need to manage permission on disk
just like if you were not using containers !

There are a lot of security options that you need to use at runtime in order to preserve permissions !
- https://success.docker.com/article/security-best-practices
- https://docs.docker.com/engine/reference/run/#user
- https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities

## Next Step

That's all for now. Thanks and don't forget to send a feedback ! 

&raquo; (WIP)

Or go back to [Docker commands and options](./04-docker-commands.md)