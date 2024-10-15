# Freestyle Project 
## Manage Jenkins - Tools
/manage/configureTools - Add maven (name: mvn) - Install from Apache automatically
## Job - Freestyle Project - probes
Git - https://github.com/brainupgrade-in/k8s-probes-springboot
Build Steps - Invoke top level maven targets - Maven version (mvn) - Goals (clean install)

# Pipeline 
## Manage Jenkins - Credentials - Create
/manage/credentials/store/system/domain/_/newCredentials
Kind - Username and Password
Username: <docker_hub_username>
Password: <docker_hub_password>
ID: docker-hub-credentials

## Job - Pipeline (logger) 
Pipeline from SCM
Git - https://github.com/brainupgrade-in/request-logger
Branches to build - main
Script Path: Jenkinsfile-docker-multi-push

## Job - Pipeline (logger-k8s-deploy)
Pipeline from SCM
Git - https://github.com/brainupgrade-in/request-logger
Branches to build - main
Script Path: Jenkinsfile-k8s-deploy

Manage Jenkins - system - 
Github project - project url - https://github.com/brainupgrade-in/request-logger
Build Triggers - Github PUll Request Builder 
API Credentials 
Admin list brainugprade-in
Use github hooks for build triggering
Pipeline - scm - git - repo url (...request-logger) and credentials
branch */main

### Pipeline
Create cloud - kubernetes (enable websocket)
Pod label: jenkins-k8s-label

examples/01-agent-any
examples/03-kubernetes/07c-git-docker-build-push
examples/03-kubernetes/10-code-analysis/Jenkinsfile
examples/05-apps/request-logger/Jenkinsfile.agent
examples/02-docker/09-parallel-multi-docker - need troubleshooting