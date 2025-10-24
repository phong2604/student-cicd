pipeline {
    agent { label 'student-code' }

    environment {
        BUILD_DIR = 'dist'
    }

    stages {

        stage('Setup') {
            steps {
                sh '''
                    apt-get update -y
                    apt-get install -y curl
                    curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
                    apt-get install -y nodejs
                    node -v
                    npm -v
                    npm install || true
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    if [ -f ".eslintrc.js" ] || [ -f ".eslintrc.json" ]; then
                        npx eslint . || true
                    else
                        echo "No ESLint config found, skipping lint."
                    fi

                    if [ -f ".stylelintrc" ]; then
                        npx stylelint "**/*.css" || true
                    fi
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    if npm run | grep -q "build"; then
                        npm run build
                    else
                        echo "No build script, copying files instead."
                        mkdir -p ${BUILD_DIR}
                        cp -r *.html *.css *.js ${BUILD_DIR}/ 2>/dev/null || true
                    fi
                '''
            }
            post {
                success {
                    archiveArtifacts artifacts: '${BUILD_DIR}/**', fingerprint: true
                }
            }
        }

        stage('Test') {
            steps {
                sh '''
                    if npm run | grep -q "test"; then
                        npm test
                    else
                        echo "No test script defined, skipping tests."
                    fi
                '''
            }
        }
    }

    post {
        always {
            sh 'rm -rf * || true'
        }
    }
}
