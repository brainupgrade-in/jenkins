# Deploy jenkins
kubectl apply -f https://raw.githubusercontent.com/brainupgrade-in/dockerk8s/main/kubernetes/lab/03-deployment/jenkins.yaml
kubectl apply -f https://raw.githubusercontent.com/brainupgrade-in/dockerk8s/main/kubernetes/lab/03-deployment/jenkins-efs.yaml

## Jenkins with STS
k apply -f https://raw.githubusercontent.com/brainupgrade-in/dockerk8s/refs/heads/main/kubernetes/lab/07-statefulset/jenkins/jenkins.yaml


# Deploy docker agent
kubectl apply -f https://raw.githubusercontent.com/brainupgrade-in/dockerk8s/main/kubernetes/lab/03-deployment/jenkins-agent.yaml

# To cache repo for maven builds
kubectl apply -f https://raw.githubusercontent.com/brainupgrade-in/jenkins/main/examples/03-kubernetes/05-request-logger/maven-cache.yaml

# To run Junit tests
Add pipeline project https://github.com/jenkinsci/performance-plugin 

# Sonar Qube
withSonaryQubeEnv('sonarqube'){
    sh ''' 
    $SCANNER_HOME/bin/sonar-scanner \ 
     -Dsonar.projectName=${env.JOB_NAME} \ 
     -Dsonar.java.binaries=. \ 
      -Dsonar.projectKey=${env.JOB_NAME}
    '''
}

Update Jenkins settins and tools accordingly

# OWASP
dependencyCheck additionalArguments: '', odcInstallation: 'owasp-dc' dependencyCheckPublisher patter: '**/dependency-check-report.xml'

Specify in Jenkins tools: owasp-dc and use dependency-check as installation options

# Horizontally scalable software
- Software should be designed for horizontal scalability (session data managemetn across replicas etc)
- Load Balancer should have appropriate config to allow passing of session info ( info like cookie, header, client ip etc) to the software
- Deploy software in scalable cluster environment like Kubernetes cluster (AWS EKS etc)

# Highly scalable & available Jenkins setup
- Deploy Jenkins controller using k8s deploy spec - example spec is above 
- Use scalable and distributed storage for Jenkins controller
- Use pods as build agents
## Benefits of running Jenkins on  Kubernetes
- Auto / self healing of Jenkins controller
- Run builds in parallel / Load distribution / build scalability
- Hot deployment (upgrade Jenkins controller to new version on the fly)

## Enable cookie on the ingress object - annotate ingress object
kubectl annotate ingress jenkins nginx.ingress.kubernetes.io/affinity="cookie" nginx.ingress.kubernetes.io/session-cookie-name="jenkins"  nginx.ingress.kubernetes.io/session-cookie-max-age="172800"

Also add below snippet (optional) under annotations in the ingress object

    nginx.ingress.kubernetes.io/configuration-snippet: |
      more_set_headers "use-forwarded-headers true";

## Enable sticky session on service that exposes Jenkins controller
  sessionAffinity: ClientIP
  sessionAffinityConfig:
    clientIP:
      timeoutSeconds: 3600
## Use EFS to store settings and jobs
- Create Storage class
- Use this storage classin PVC that will be used by Jenkins deployment

# References
https://github.com/fabric8io/fabric8-jenkinsfile-library/
https://github.com/cloudogu/jenkinsfiles
https://github.com/hoto/jenkinsfile-examples/blob/master/jenkinsfiles/050-shared-library-where-is-it-cloned.groovy

# Deployment strategy 
- Hot Deployment (rollout new change on the fly) - zero downtime
- Restart x3 (Hot Deployment) - zero downtime
- High availability - (usnig Hot Deployment and probes)
- Blue Green (x3 and x4 version running in parallel using two different for user testing)

# Kubernetes role and rolebinding for jenkins

# controller sizing
m3.xlarge
c3.xlarge
kubernetes node groups
jenkins: nodeSelector - node labels nodegroups-jenkins c3.large c3.xlarge  c3.x3large
Auto scalability : instance types to use: .....
jenkins: resources - computing capacity requirements: cpu intesive : 10 core cpu, 20GB RAM  General 1cpu 4 GB RAM  memory:  2 core cpu 20GB RAM

# Regions
ap-south-1  jenkins active controller 1  EFS
# AZs
ap-south-1a ap-south-1b ap-south1-c
# DC
dc1 dc2.... (1a)
dc1 dc2... (1b)

ap-southeast-1  jenkins passive controller-1 EFS

# Rout 53
jenkins.jpmorgan.com  - ap-southeast-1

# Kubernetes - AWS EKS
namespaces - TEST  UAT PROD

TEST  jenkins-test.jpm.com   (jenkins-414.x2)
UAT  jenkins-uat  jenkins-414.x1  k set image...x2
PROD   jenkins-prod  jenkins-414.x1

multi-branch pipeline example

jenkins github argocd   CICDCR

# Pod clean up jenkins jobs
kubectl get pod -l jenkins=kubernetes -o=json | jq -r '.items[]|select(any( .status.containerStatuses[]; .ready==true))|.metadata.name'
kubectl get pods -l jenkins=kubernetes | xargs -I {} kubectl delete {}
kubectl get pods -l jenkins/label=jenkins-k8s-agent | xargs -I {} kubectl delete {}