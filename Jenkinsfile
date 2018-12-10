pipeline {
    agent {
        label 'master'
    }
    triggers {
        upstream(upstreamProjects: '../Reference/ref_alcohol',
                 threshold: hudson.model.Result.SUCCESS)
    }
    stages {
        stage('Clean') {
            steps {
                sh 'rm -rf out'
            }
        }
        stage('Transform') {
            agent {
                docker {
                    image 'cloudfluff/databaker'
                    reuseNode true
                }
            }
            steps {
                script {
                    for (def file : findFiles(glob: '*.ipynb')) {
                        sh "jupyter-nbconvert --output-dir=out --ExecutePreprocessor.timeout=None --execute '${file.name}'"
                    }
                }
            }
        }
        stage('Review') {
            steps {
                error "Needs review"
            }
        }
    }
    post {
        always {
            archiveArtifacts 'out/*'
        }
        success {
            build job: '../GDP-tests', wait: false
        }
    }
}
