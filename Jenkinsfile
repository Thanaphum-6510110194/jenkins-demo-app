pipeline {
  agent any

  environment {
    IMAGE_NAME     = 'jenkins-demo-app'
    CONTAINER_NAME = 'demo-app'
    APP_PORT       = '5000'
  }

  stages {
    stage('Show Workspace') {
      steps {
        sh 'pwd && ls -la'
      }
    }

    stage('Unit Test') {
      steps {
        // รัน pytest ในคอนเทนเนอร์ python โดยผูก workspace เข้าไป
        sh '''
          docker run --rm \
            -v "$WORKSPACE":/ws -w /ws \
            python:3.11-slim /bin/sh -lc "python -V && pip install --no-cache-dir -r requirements.txt && pytest -q"
        '''
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME:latest .'
      }
    }

    stage('Run Container') {
      steps {
        sh '''
          docker rm -f $CONTAINER_NAME || true
          docker run -d --name $CONTAINER_NAME -p $APP_PORT:$APP_PORT $IMAGE_NAME:latest
        '''
      }
    }
  }

  post {
    always {
      sh 'docker ps --format "table {{.Names}}\\t{{.Image}}\\t{{.Ports}}" || true'
    }
  }
}
