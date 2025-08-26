pipeline {
  agent any
  environment {
    IMAGE_NAME     = 'jenkins-demo-app'
    CONTAINER_NAME = 'demo-app'
    APP_PORT       = '5000'
    APP_DIR        = '.'   // ถ้าไฟล์อยู่โฟลเดอร์ย่อย เปลี่ยนเป็นชื่อโฟลเดอร์ เช่น 'src'
  }

  stages {
    stage('Show Workspace') {
      steps { sh 'pwd && ls -la' }
    }

    stage('Unit Test') {
      steps {
        sh '''
          docker run --rm \
            -v "$PWD":/ws -w /ws/$APP_DIR \
            python:3.11-slim sh -c "
              python -V &&
              pip install --no-cache-dir -r requirements.txt &&
              pytest -q
            "
        '''
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME:latest $APP_DIR'
      }
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
v