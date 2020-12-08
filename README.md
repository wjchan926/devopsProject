# DevOps Team 1

## DevOps Team Project

**Source Code Repo:** https://github.com/wjchan926/devopsProject

</br>

### Getting Updated Images:

- Running `docker-compose pull` will pull all the latest images required from Docker Repos.
- Running this before starting the docker containers is good practice if you want the most updated changes

</br>

# Multi Container Setup

### Container Setup:

1. Git Clone this project to local repo
2. Add `export GITLAB_HOME="/srv/gitlab"` to your `~/.bashrc` file. Save and run `source ~./bashrc`.
   - This env variable must be set in Linux for hosted GitLab to work
3. Run `docker-compose up -d` in repo root. Wait for all containers to spin up, usually takes a few minutes

### GitLab Setup:

4. Go to the GitLab container by visitng http://localhost:8924 in a browser
5. It will prompt you for a root user password. Just use `password`
6. Create a new user using any credentials you like
7. You can now login with your credentials
8. Create a new project
9. Select `Import Project`
10. Select `GitHub`
    - For this part, I forked https://github.com/bkimminich/juice-shop into my own respository first
    - Then I created an access token in GitHub. The directions on how to do so are linked on the `Import Project` page in GitLab
11. Follow the steps for importing a project and select the `juice-shop` repo to import
12. Once imported, the project `juice-shop` should appear in the GitLab repo.
13. Add 2 files to the GitLab `juice-shop` repository: (Code for these files can be found in the later sections of this README)

- [.gitlab-ci.yml](#gitlab-ci.yml)
- [sonar-project.properties](sonar-project.properties)

### GitLab - SonarQube Config Setup:

14. Go to the Project in GitLab > Settings > CI/CD
15. Expand the `Variables` section
16. Add 2 variables:
    - `SONAR_HOST_URL=http://localhost:9000`
    - `SONAR_TOKEN`
      - This token has to be generated in SonarQube
      - Go to SonarQube at http://localhost:9000
      - Login as `admin`, password `admin`
      - Go to My Account > Security
      - Generate a token
      - Copy this value and use it as the `SONAR_TOKEN` in GitLab

### GitLab - GitLab Runner Config Setup:

17. Expand the `Runners` section. In the `Set up a specific Runner manually` section, there should be two values that can be copied, http://localhost:8924/ and some hash token. The hash token will be different for each instance of GitLab.
18. We will now register the runner. In the same `dir` where you ran `docker-compose.yml`, run `docker exec -it gitlab-runner /bin/bash`
19. In the shell, run `gitlab-runner register`
20. You can hit enter for everything except for the `Token` prompt. For this, copy and paste the token from the `Runners` section in GitLab. All other values were prefilled with the `docker-compose.yml` file
21. Confirm that the Runner was successfully registered
22. In GitLab, refresh the CI/CD page and the new runner should appear under the `Runners` section.

### Testing:

1. Make a change to the `juice-shop` repo in GitLab.
2. A job should automatically start running (the scanner)
3. You can click on the pie-like icon to see the status. It should take a few mins to run.
4. Confirm that the job has completed successfully.
5. Visit http://localhost:9000
6. Go to `Projects`
7. Confirm the pretty statistics in the `juice-shop` project in SonarQube

---

</br>
</br>

# Containers

## Making Changes to Docker Images:

- If you want your changes made in the docker images to persist for everyone, you need to tag the image and push it to the repo
- `docker tag <image id> wjchan/<image name>:<tag>`
- `docker push wjchan/<image name>`
- Everyone will have to `docker-compose pull` to get the updated changes

## Starting and Stopping Containers:

Run the entire suite of docker containers using `docker-compose up -d` in the same dir as the `docker-compose.yml` file.

Stop all containers with `docker-compose down`.

</br>
</br>

# GitLab:

**Docker Repo:** https://hub.docker.com/repository/docker/wjchan/gitlab_img

## GitLab Prerequisites:

Set up the volumes location:

Before setting everything else, configure a new environment variable `$GITLAB_HOME` pointing to the directory where the configuration, logs, and data files will reside. Ensure that the directory exists and appropriate permission have been granted.

For Linux users, set the path to `/srv/gitlab`:

`export GITLAB_HOME=/srv/gitlab`

In Ubuntu, you can add this to `$HOME/.bashrc` so you dont have to set the environment variable every session.

## Using the GitLab Image:

- Pull files from github repo
- Run `docker-compose up -d` in the same dir as the `docker-compose.yml` file
- Wait a few mins for the container to spin up
- Use `docker ps` to check the container status
- Container is ready when the status say `healthy`
- Visit http://localhost:8924 in a browser

</br>
</br>

# Sonarqube

**Docker Repo:** https://hub.docker.com/repository/docker/wjchan/sonarqube_img

## Using the Sonarqube Image:

- Pull files from github repo
- Run `docker-compose up -d` in the same dir as the `docker-compose.yml` file
- This container spins up much faster than GitLab
- Visit http://localhost:9000 in a browser

## **Important**

These variable must be set in hosted GitLab

- `SONAR_HOST_URL=http://localhost:9000`
- `SONAR_TOKEN` - See [Multi Container Setup](#multi-container-setup) for instructions

</br>
</br>

# Network

The docker network that links the two containers is called `devopsNet`.

You can test a connection with `docker-compose exec sonarqube /bin/sh`.

Then once in the containers shell `ping gitlab`.

If the network is up, you should be getting package notifications in the shell.

</br>
</br>

# GitLab Runner:

**Docker Repo:** https://hub.docker.com/repository/docker/gitlab/gitlab-runner"

## **Important** Using the GitLab-Runner Image:

The GitLab-Runner image has to be registered. See [Multi Container Setup](#multi-container-setup) for instructions.

<br>
<br>

# Files:

### .gitlab-ci.yml

```
sonarqube-check:
  image:
    name: sonarsource/sonar-scanner-cli:latest
    entrypoint: [""]
  tags:
    - latest
  variables:
    SONAR_USER_HOME: "${CI_PROJECT_DIR}/.sonar"  # Defines the location of the analysis task cache
    GIT_DEPTH: "0"  # Tells git to fetch all the branches of the project, required by the analysis task
  cache:
    key: "${CI_JOB_NAME}"
    paths:
      - .sonar/cache
  script:
    - sonar-scanner -Dsonar.qualitygate.wait=true
  allow_failure: true
  only:
    - master
```

### sonar-project.properties

```
sonar.projectKey=juice-shop
sonar.projectName=juice-shop
sonar.login=admin
sonar.password=admin
```
