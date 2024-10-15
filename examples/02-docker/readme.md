
# How to setup docker agent
Run Jenkins agent having docker and openjdk11

kubectl apply -f https://raw.githubusercontent.com/brainupgrade-in/dockerk8s/main/kubernetes/lab/03-deployment/jenkins-agent.yaml

Add Agent named docker and launch method - Launch agent via SSH

# Add docker plugin (to use as pipeline step)
Add Docker node using Jenkins Add Node option
Specify docker host and agent label (docker)
