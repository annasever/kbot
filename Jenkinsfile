pipeline {
    agent any
    
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
                // Клонуємо ваш репозиторій
                git branch: 'main', url: 'https://github.com/annasever/kbot.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    if (params.OS == 'linux') {
                        sh '''
                            apt-get update
                            apt-get install -y make
                            make install-linux
                        '''
                    } else if (params.OS == 'darwin') {
                        sh '''
                            brew install make
                            make install-darwin
                        '''
                    } else if (params.OS == 'windows') {
                        bat '''
                            choco install make
                            make install-windows
                        '''
                    }
                }
            }
        }
        
        stage('Lint') {
            when {
                expression { !params.SKIP_LINT }
            }
            steps {
                sh "make lint OS=${params.OS} ARCH=${params.ARCH}"
            }
        }
        
        stage('Test') {
            when {
                expression { !params.SKIP_TESTS }
            }
            steps {
                sh "make test OS=${params.OS} ARCH=${params.ARCH}"
            }
        }
        
        stage('Build') {
            steps {
                sh "make build OS=${params.OS} ARCH=${params.ARCH}"
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: "bin/kbot-${params.OS}-${params.ARCH}", fingerprint: true
            }
        }
    }
    
    post {
        always {
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