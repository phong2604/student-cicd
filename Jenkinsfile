pipeline {
    agent { label 'student-code' }

    environment {
        BUILD_DIR = 'dist'
    }

    stages {

        stage('Setup') {
            steps {
                sh '''
                    sudo apt-get update -y
                    sudo apt-get install -y curl
                    curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
                    sudo apt-get install -y nodejs
                    node -v
                    npm -v
                    npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    # Run ESLint if configuration exists
                    if [ -f ".eslintrc.js" ] || [ -f ".eslintrc.json" ]; then
                        npx eslint . || true
                    else
                        echo "No ESLint configuration found, skipping lint."
                    fi

                    # Optional: Check CSS formatting with stylelint if present
                    if [ -f ".stylelintrc" ]; then
                        npx stylelint "**/*.css" || true
                    fi
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    # If a build script is defined in package.json, run it
                    if npm run | grep -q "build"; then
                        npm run build
                    else
                        echo "No build script found, copying source files instead."
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
                    # Run tests if defined in package.json
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
