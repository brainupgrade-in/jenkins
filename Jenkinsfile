pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: jmeter
    image: egaillardon/jmeter
    command:
    - cat
    tty: true
'''
        }
    }

    stages {
        stage('test') {
            steps {
                script {
                    container('jmeter') {
                        sh "rm -rf result && rm -rf result.jtl && jmeter -n -t ${env.WORKSPAE}/src/test/resources/jmeter-e2e.jmx -l result.jtl -e -o result"
                    }
                }
            }
        }

        stage('report') {
            steps {
                script {
                    container('jmeter') {
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'result', reportFiles: 'index.html', reportName: 'JMeter-Report', reportTitles: ''])
                    }
                }
            }
        }
    }
}
