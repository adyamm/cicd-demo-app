pipeline {
    agent any
    
    parameters {
        string(
            name: 'REPO_URL',
            defaultValue: 'https://github.com/adyamm/cicd-demo-app.git',
            description: 'HTTPS-адрес репозитория'
        )
        
        string(
            name: 'GIT_BRANCH',
            defaultValue: 'master',
            description: 'Ветка для сборки'
        )
        
        booleanParam(
            name: 'RUN_TESTS',
            defaultValue: true,
            description: 'Запускать unit-тесты'
        )
    }
    
    stages {
        stage('Checkout') {
            steps {
                git url: params.REPO_URL, branch: params.GIT_BRANCH
            }
        }
        
        stage('Unit tests') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                sh 'python3 -m unittest discover -s tests -v'
            }
        }
    }
}
