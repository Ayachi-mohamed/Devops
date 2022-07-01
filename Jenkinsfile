def commit_id
pipeline {
    agent any
    stages {
        stage('preparation') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage ('build') {
            steps {
                echo 'building maven workload'
                sh "mvn clean install"
                echo 'build complete'
            }
        }
       
        stage ("image build") {
            steps {
                echo 'building docker image'
                sh "docker build -t localhost:5000/position-simulator:${commit_id} ."
                echo 'docker image built'
            }
        }
        stage ('Image Push') {
            steps {
                sh "docker push localhost:5000/position-simulator:${commit_id}"
            }
        }
        stage('deploy') {
            steps {
                sh "sed -i -r 's|richardchesterwood/k8s-fleetman-position-simulator:release2|localhost:5000/position-simulator:${commit_id}|' workloads.yaml"
                sh 'kubectl apply -f workloads.yaml'
            }
        }
    }
}
