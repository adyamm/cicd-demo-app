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
                echo "Branch: ${params.GIT_BRANCH}"
                sh 'git rev-parse HEAD'
            }
        }
        
        stage('Validate') {
            steps {
                sh '''
                    for file in README.md requirements.txt; do 
                        if [ ! -f "$file" ]; then
                            echo "Не найден файл $file"
                            exit 1
                        fi
                    done
                    
                    for directory in src tests; do
                        if [ ! -d "$directory" ]; then
                            echo "Не найден каталог $directory"
                            exit 1
                        fi
                    done
                    
                    echo "Все обязательные файлы и каталоги на месте"
                '''
            }
        }
        
        stage('Test') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                sh 'python3 -m unittest discover -s tests -v'
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    commit_sha=$(git rev-parse HEAD)
                    echo "BUILD_NUMBER=$BUILD_NUMBER" > build-info.txt
                    echo "COMMIT_SHA=$commit_sha" >> build-info.txt
                    
                    tar --exclude='__pycache__' -czf app.tar.gz src/ tests/ README.md requirements.txt build-info.txt
                    tar -tzf app.tar.gz
                    cat build-info.txt
                '''
            }
        }
        
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'app.tar.gz'
            }
        }
    }
    
    post {
        success {
            echo "Сборка #${env.BUILD_NUMBER} завершилась успешно."
        }
        unsuccessful {
            echo "Сборка #${env.BUILD_NUMBER} завершилась со статусом ${currentBuild.currentResult}."
        }
    }
}
