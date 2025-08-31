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
        stage('Build') {
            steps {
                echo "Building for ${params.OS}/${params.ARCH}"
                sh "make build OS=${params.OS} ARCH=${params.ARCH}"
            }
        }

        stage('Lint') {
            when {
                expression { return !params.SKIP_LINT }
            }
            steps {
                echo "Running linter..."
                sh "make lint"
            }
        }

        stage('Test') {
            when {
                expression { return !params.SKIP_TESTS }
            }
            steps {
                echo "Running tests..."
                sh "make test"
            }
        }
    }
}
