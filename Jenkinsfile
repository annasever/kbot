pipeline {
    agent { label 'go-docker' }

    parameters {
        choice(
            name: 'OS',
            choices: ['linux', 'darwin', 'windows'],
            description: 'Target operating system'
        )
        choice(
            name: 'ARCH',
            choices: ['amd64', 'arm64'],
            description: 'Target architecture'
        )
        booleanParam(
            name: 'SKIP_TESTS',
            defaultValue: false,
            description: 'Skip running tests'
        )
        booleanParam(
            name: 'SKIP_LINT',
            defaultValue: false,
            description: 'Skip running linter'
        )
    }

    environment {
        TARGETOS = "${params.OS}"
        TARGETARCH = "${params.ARCH}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Cloning repository"
                git branch: 'main', url: 'https://github.com/annasever/kbot.git'
            }
        }

        stage('Lint') {
            when { expression { !params.SKIP_LINT } }
            steps {
                echo "Running linter"
                sh 'make lint TARGETOS=${TARGETOS} TARGETARCH=${TARGETARCH}'
            }
        }

        stage('Test') {
            when { expression { !params.SKIP_TESTS } }
            steps {
                echo "Running tests"
                sh 'make test TARGETOS=${TARGETOS} TARGETARCH=${TARGETARCH}'
            }
        }

        stage('Build') {
            steps {
                echo "Building application"
                sh 'make build TARGETOS=${TARGETOS} TARGETARCH=${TARGETARCH}'
            }
        }

        stage('Docker Image') {
            steps {
                echo "Building Docker image"
                sh 'make image TARGETARCH=${TARGETARCH}'
            }
        }

        stage('Push Image') {
            steps {
                echo "Pushing Docker image"
                sh 'make push TARGETARCH=${TARGETARCH}'
            }
        }

        stage('Archive Artifacts') {
            steps {
                echo "Archiving binary"
                archiveArtifacts artifacts: "kbot", fingerprint: true, allowEmptyArchive: true
            }
        }
    }

    post {
        always { echo "Cleaning workspace"; cleanWs() }
        success { echo "Build for ${TARGETOS}-${TARGETARCH} completed successfully!" }
        failure { echo "Build for ${TARGETOS}-${TARGETARCH} failed!" }
    }
}
