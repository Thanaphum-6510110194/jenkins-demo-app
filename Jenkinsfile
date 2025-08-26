pipeline {
  agent {
    docker {
      image 'docker:20.10.7'
      args '-v /var/run/docker.sock:/var/run/docker.sock'
    }
  }
  options { timestamps() }
  environment {
    IMAGE_NAME     = 'jenkins-demo-app'
    CONTAINER_NAME = 'demo-app'
    APP_PORT       = '5000'
  }

  stages {
    // ไม่มี stage('Checkout') แล้ว — ใช้ default checkout ของ Jenkins ที่ทำให้อัตโนมัติ

    stage('Unit Test') {
      steps {
        sh '''
          docker run --rm \
            -v "$PWD":/app -w /app \
            python:3.11-slim sh -c "
              python -V &&
              pip install --no-cache-dir -r requirements.txt &&
              pytest -q
            "
        '''
      }
    }

    stage('Build Image') {
      steps { sh 'docker build -t $IMAGE_NAME:latest .' }
    }

    stage('Run Container') {
      steps {
        sh 'docker rm -f $CONTAINER_NAME || true'
        sh 'docker run -d --name $CONTAINER_NAME -p $APP_PORT:$APP_PORT $IMAGE_NAME:latest'
      }
    }
  }

  post {
    always {
      sh 'docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Ports}}" || true'
    }
  }
}
