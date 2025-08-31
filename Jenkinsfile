pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins/label: jenkins-jenkins-agent
spec:
  containers:
  - name: jnlp
    image: jenkins/inbound-agent:3327.v868139a_d00e0-6
    args: ["$(JENKINS_SECRET)", "$(JENKINS_AGENT_NAME)"]
    workingDir: /home/jenkins/agent
    resources:
      requests:
        memory: "256Mi"
        cpu: "100m"
      limits:
        memory: "512Mi"
        cpu: "512m"
    volumeMounts:
    - name: workspace-volume
      mountPath: /home/jenkins/agent
  - name: builder
    image: ubuntu:20.04
    command: ["/bin/sh", "-c"]
    args: ["apt-get update && apt-get install -y make golang-go && sleep infinity"]
    workingDir: /home/jenkins/agent
    tty: true
    resources:
      requests:
        memory: "256Mi"
        cpu: "100m"
      limits:
        memory: "512Mi"
        cpu: "512m"
    volumeMounts:
    - name: workspace-volume
      mountPath: /home/jenkins/agent
  volumes:
  - name: workspace-volume
    emptyDir: {}
  nodeSelector:
    kubernetes.io/os: linux
'''
            defaultContainer 'jnlp'
        }
    }
    parameters {
        choice(name: 'OS', choices: ['linux', 'darwin', 'windows'], description: 'Target operating system')
        choice(name: 'ARCH', choices: ['amd64', 'arm64'], description: 'Target architecture')
        booleanParam(name: 'SKIP_TESTS', defaultValue: false, description: 'Skip running tests')
        booleanParam(name: 'SKIP_LINT', defaultValue: false, description: 'Skip running linter')
    }
    stages {
        stage('Build') {
            steps {
                echo "Building for ${params.OS}/${params.ARCH}"
                container('builder') {
                    sh "make build OS=${params.OS} ARCH=${params.ARCH}"
                }
            }
        }
        stage('Lint') {
            when {
                expression { return !params.SKIP_LINT }
            }
            steps {
                container('builder') {
                    echo "Running linter..."
                    sh "make lint"
                }
            }
        }
        stage('Test') {
            when {
                expression { return !params.SKIP_TESTS }
            }
            steps {
                container('builder') {
                    echo "Running tests..."
                    sh "make test"
                }
            }
        }
    }
    post {
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}