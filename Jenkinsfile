
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out multi-page website'
                checkout scm
            }
        }

        stage('Validate Files') {
            steps {
                sh '''
                    set -eu
                    for file in index.html about.html services.html contact.html style.css
                    do
                        test -f "$file" || {
                            echo "Missing file: $file"
                            exit 1
                        }
                    done
                    echo "All website files found"
                '''
            }
        }

        stage('HTML Validation') {
            steps {
                sh '''
                    set -eu
                    for file in index.html about.html services.html contact.html
                    do
                        grep -qi '<html' "$file"
                        grep -qi '</html>' "$file"
                    done
                    echo "Basic HTML checks passed"
                '''
            }
        }

        stage('Package Website') {
            steps {
                sh '''
                    set -eu
                    mkdir -p dist
                    cp index.html about.html services.html contact.html style.css dist/
                '''
                archiveArtifacts artifacts: 'dist/**',
                                 fingerprint: true
            }
        }

        stage('Deploy to Apache') {
            steps {
                sh '''
                    set -eu
                    test -d /var/www/html/multi
                    cp dist/index.html \
                       dist/about.html \
                       dist/services.html \
                       dist/contact.html \
                       dist/style.css \
                       /var/www/html/multi/
                    echo "Multi-page website deployed successfully"
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Website deployed'
        }
        failure {
            echo 'FAILED: Check Console Output'
        }
        always {
            echo 'Pipeline execution finished'
        }
    }
}
