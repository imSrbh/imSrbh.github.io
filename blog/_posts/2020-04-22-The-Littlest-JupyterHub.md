---
layout: post
title: "Setting UP the Littlest JupyterHub"
subtitle: TLJH to provide Jupyter Notebooks to multiple users on a single server
tags: [blog, popular, docker, jupyter]
image: /blog/blog_img/tljh/tljh.png
share-img: /blog/blog_img/tljh/tljh.png
permalink: /SettingUPtljh/
gh-repo: imSrbh/TLJH_Docker
gh-badge: [fork, star, watch, follow]
---

We will see how to setup JupterHub to create multiple users where they can work on multiple individual Jupyter Notebook on a single server. 

---

# TLJH_Docker
The Littlest JupyterHub (TLJH) distribution helps you to provide Jupyter Notebooks to 1-100 users on a single server.  

1. Clone this Repo  
`git clone https://github.com/imSrbh/TLJH_Docker.git`

2. Build a docker image that has a functional systemd in it.  
```
docker build -t tljh-systemd . -f Dockerfile
```
​	___do not forget the period (.)___


3. Run a docker container with the image in the background, while bind mounting your TLJH repository under /srv/src.
```
docker run \
  --privileged \
  --detach \
  --name=tljh-dev \
  --publish 12000:80 \
  --mount type=bind,source=$(pwd),target=/srv/src \
  tljh-systemd
```

4. Get a shell inside the running docker container.
```
	docker exec -it tljh-dev /bin/bash
```

5. Run the bootstrapper from inside the container (see step above): The container image is already set up to default to a dev install, so it’ll install from your local repo rather than from github.
```python
	python3 /srv/src/bootstrap/bootstrap.py --admin admin:password
```


6. You should be able to access the JupyterHub from your browser now at `http://localhost:12000`. Congratulations, you are set up to develop TLJH!

>If you want to add more user, from admin panal you can create user and when user will be signing in the password of his choice which he uses at that first time sign in, that will will be the password for that user.

Inside the container `/home` -> You can see the user as `jupyter-abc`, `jupyter-xyz`

```
root@277863f1ab7e:/home# ls
jupyter-abc  jupyter-xyz
```

#### This was the basic testing setup.  
#### **Detailed info :** https://github.com/jupyterhub/the-littlest-jupyterhub



---

# Using docker-compose



1.  Clone the repo `git clone https://github.com/imSrbh/TLJH_Docker.git`
2.  Create the container for service
```
docker-compose up --build -d
```

3. Run the container

```
saurabh@srbh:~/Git/TLJH_Docker$ sudo docker run \
   --privileged \
   --detach \
   --name=tljh-dev_web \
   --publish 12000:80 \
   --mount type=bind,source=$(pwd),target=/srv/src \
   tljh_docker_web
```
4.  Run in interactive mode
```
sudo docker exec -it tljh-dev_web /bin/bash
```

5. Setup admin password

```
python3 /srv/src/bootstrap/bootstrap.py --admin admin:password
```

6. See the JupyterHub in browser goto 

   localhost:12000

---
## TODO
- [x] script file(.sh)  
  - [x] Multi admin setup bash script.  
        [admin_setup.sh](https://gist.github.com/imSrbh/0349a99b393f351061b4a9932258816b)  
    
    (This is not mounted yet with the docker-compose )
    
  - [ ]   ~~User setup bash script.~~
  
- [x] Basic Docker-Compose  file

-  We can modify it's html also inside the running container `/opt/tljh/hub/share/jupyterhub/templates# `.