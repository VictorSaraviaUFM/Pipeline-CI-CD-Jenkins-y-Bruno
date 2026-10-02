pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }
  options {
    skipDefaultCheckout()
    disableConcurrentBuilds()
    timeout(time: 15, unit: 'MINUTES')
  }
  environment {
    IMAGE = "books-api:${env.BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Start API') {
      steps {
        sh '''
          set -eu
          export JENKINS_NODE_COOKIE=dontKillMe
          nohup uv run --frozen api.py > api.log 2>&1 < /dev/null &
          echo $! > api.pid
          for i in $(seq 1 60); do
            if curl -fsS http://127.0.0.1:8000/health > /dev/null; then
              exit 0
            fi
            if ! kill -0 "$(cat api.pid)" 2>/dev/null; then
              cat api.log
              exit 1
            fi
            sleep 1
          done
          cat api.log
          exit 1
        '''
      }
    }
    stage('Regresion') {
      steps {
        dir('bruno/regresion') {
          sh 'bru run --env local --sandbox=developer --reporter-junit results.xml'
        }
      }
    }
    stage('Build image') {
      steps {
        sh 'docker build -f Containerfile -t "$IMAGE" .'
      }
    }
    stage('Deploy') {
      steps {
        sh '''
          set -eu
          docker rm -f books-api 2>/dev/null || true
          docker run -d --name books-api -p 8001:8000 "$IMAGE"
          for i in $(seq 1 30); do
            if docker exec books-api python -c "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8000/health')"; then
              exit 0
            fi
            sleep 1
          done
          docker logs books-api
          exit 1
        '''
      }
    }
  }
  post {
    always {
      junit allowEmptyResults: true, testResults: 'bruno/regresion/results.xml'
      archiveArtifacts allowEmptyArchive: true, artifacts: 'api.log'
      sh 'if [ -f api.pid ]; then kill "$(cat api.pid)" 2>/dev/null || true; fi'
    }
  }
}
