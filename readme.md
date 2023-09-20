# Deploy jenkins
kubectl apply -f https://raw.githubusercontent.com/brainupgrade-in/dockerk8s/main/kubernetes/lab/03-deployment/jenkins.yaml

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

## Enable cookie on the ingress object - annotate ingress object
kubectl annotate ingress app nginx.ingress.kubernetes.io/affinity="cookie" nginx.ingress.kubernetes.io/session-cookie-name="jenkins"  nginx.ingress.kubernetes.io/session-cookie-max-age="172800"

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