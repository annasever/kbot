pipeline {
    agent {
        docker {
            image 'golang:latest'
            args '-u root'
        }
    }
    
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
    
    stages {
        stage('Checkout') {
            steps {
                echo "Cloning repository: https://github.com/annasever/kbot.git"
                git branch: 'main', url: 'https://github.com/annasever/kbot.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                echo "Installing dependencies for OS=${params.OS}, ARCH=${params.ARCH}"
                sh '''
                    apt-get update
                    apt-get install -y make
                    if [ -f Makefile ]; then
                        make install-linux
                    else
                        echo "Makefile not found, skipping install-linux"
                    fi
                '''
            }
        }
        
        stage('Lint') {
            when {
                expression { !params.SKIP_LINT }
            }
            steps {
                echo "Running linter for OS=${params.OS}, ARCH=${params.ARCH}"
                sh '''
                    if [ -f Makefile ]; then
                        make lint OS=${OS} ARCH=${ARCH}
                    else
                        echo "Makefile not found, skipping lint"
                    fi
                '''
            }
        }
        
        stage('Test') {
            when {
                expression { !params.SKIP_TESTS }
            }
            steps {
                echo "Running tests for OS=${params.OS}, ARCH=${params.ARCH}"
                sh '''
                    if [ -f Makefile ]; then
                        make test OS=${OS} ARCH=${ARCH}
                    else
                        echo "Makefile not found, skipping tests"
                    fi
                '''
            }
        }
        
        stage('Build') {
            steps {
                echo "Building for OS=${params.OS}, ARCH=${params.ARCH}"
                sh '''
                    if [ -f Makefile ]; then
                        make build OS=${OS} ARCH=${ARCH}
                    else
                        echo "Makefile not found, performing default Go build"
                        go build -o bin/kbot-${OS}-${ARCH} .
                    fi
                '''
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                echo "Archiving artifacts: bin/kbot-${params.OS}-${params.ARCH}"
                archiveArtifacts artifacts: "bin/kbot-${params.OS}-${params.ARCH}", fingerprint: true, allowEmptyArchive: true
            }
        }
    }
    
    post {
        always {
            echo "Cleaning workspace"
            cleanWs()
        }
        success {
            echo "Build for ${params.OS}-${params.ARCH} completed successfully!"
        }
        failure {
            echo "Build for ${params.OS}-${params.ARCH} failed!"
        }
    }
}