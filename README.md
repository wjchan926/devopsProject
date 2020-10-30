# DevOps Team 1
## DevOps Team Project

**Source Code Repo:** https://github.com/wjchan926/devopsProject

### Running Containers
---

### Getting Updated Images:
* Running `docker-compose pull` will pull all the latest images required from Docker Repos.
* Running this before starting the docker containers is good practice if you want the most updated changes

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

