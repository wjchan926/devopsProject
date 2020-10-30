# DevOps Team 1
## DevOps Team Project

**Source Code Repo:** https://github.com/wjchan926/devopsProject

### Prerequisites:
---
Set up the volumes location
Before setting everything else, configure a new environment variable `$GITLAB_HOME` pointing to the directory where the configuration, logs, and data files will reside. Ensure that the directory exists and appropriate permission have been granted.

For Linux users, set the path to `/srv/gitlab`:


`export GITLAB_HOME=/srv/gitlab`

In Ubuntu, you can add this to `$HOME/.bashrc` so you dont have to set the environment variable every session.

### Running Containers
---

### Getting Updated Images:
* Running `docker-compose pull` will pull all the latest images required from Docker Repos.
* Running this before starting the docker containers is good practice if you want the most updated changes

### Making Changes to Docker Images:
* If you want your changes made in the docker images to persist for everyone, you need to tag the image and push it to the repo
* `docker tag <image id> wjchan/<image name>:<tag>`
* `docker push wjchan/<image name>`
* Everyone will have to `docker-compose pull` to get the updated changes

### GitLab:
**Docker Repo:** https://hub.docker.com/repository/docker/wjchan/gitlab_img

#### Using the GitLab Image:
* Pull files from github repo
* Run `docker-compose up -d` in the same dir as the `docker-compose.yml` file
* Wait a few mins for the container to spin up
* Use `docker ps` to check the container status
* Container is ready when the status say `healthy`
* Visit `localhost:8924` in a browser

### Sonarqube
**Docker Repo:**

#### Using the Sonarqube Image:

