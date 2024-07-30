# Gitlab CI/CD pipeline project

### In this project, we will see how to deploy a Nodejs application using Gitlab CI/CD.

### Pre-requisites to implement this project:
-  Create 1 virtual machine on AWS with 2 CPU, 4GB of RAM (t2.medium)
- Setup it as runner in Gitlab <a href="https://github.com/DevMadhup/GitLab-Zero-to-Hero/blob/main/Day-4/README.md">Runner</a>.
- Install docker
  ```bash
  sudo apt-get update
  sudo apt-get insatll docker.io
  ```
  - Sudo permission
  ```bash
  sudo usermod -aG docker $USER
  ```
#
## Steps for CI/CD in Gitlab:

1) Become root user :
    ```bash
    sudo su
    ```
#
2) Provide Docker socket permission : 
    ```bash
    chmod 777 /var/run/docker.sock
    ```
#
3) Go to UI and create .gitlab-ci.yml file in the root directory of your repository
    ```bash
    vim .gitlab-ci.yml
    ```

#
4) Create two variables for docker login, to create variables <a href="https://github.com/DevMadhup/GitLab-Zero-to-Hero/blob/main/Day-3/README.md">Variables</a>

#
5) Paste the below code in .gitlab-ci file :
```bash

  stages:
  - build
  - test
  - push_to_dockerhub
  - deploy

build_job:
  stage: build
  script:
    - docker build -t node-app:latest .
  tags:
    - dev

test_job:
  stage: test
  script:
    - echo "testing.."
  tags:
    - dev

push_job:
  stage: push_to_dockerhub
  script:
    - docker login -u $DOCKERHUB_USER -p $DOCKERHUB_PASS
    - docker image tag node-app:latest $DOCKERHUB_USER/node-app:latest
    - docker push $DOCKERHUB_USER/node-app:latest
  tags:
    - dev

deploy_job:
  stage: deploy
  script:
    - docker compose up -d
  tags:
    - dev
```

#
6) Go to Build --> pipeline, to see the pipeline status.

If you get below error:

```bash
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/build?buildargs=%7B%7D&cachefrom=%5B%5D&cgroupparent=&cpuperiod=0&cpuquota=0&cpusetcpus=&cpusetmems=&cpushares=0&dockerfile=Dockerfile&labels=%7B%7D&memory=0&memswap=0&networkmode=default&rm=1&shmsize=0&t=node-app%3Alatest&target=&ulimits=null&version=1": dial unix /var/run/docker.sock: connect: permission denied
Cleaning up project directory and file based variables
00:00
ERROR: Job failed: exit status 1
```
Use below commands to fix that error in your insatance:

```bash
sudo usermod -aG docker gitlab-runner
sudo service docker restart
```

#
7) Once all jobs are successfully, copy the public IP of your instance and access it on 
    ```bash
    <public-ip>:8000
    ```

#
