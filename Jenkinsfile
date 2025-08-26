pipeline {
  agent {
    docker {
      image 'docker:20.10.7'
      args "--entrypoint='' -v /var/run/docker.sock:/var/run/docker.sock"
    }
  }
  options { skipDefaultCheckout(true) }

  environment {
    IMAGE_NAME     = 'jenkins-demo-app'
    CONTAINER_NAME = 'demo-app'
    APP_PORT       = '8081'  // จาก Exercise 2
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Thanaphum-6510110194/jenkins-demo-app.git'
      }
    }

    stage('Test') {
      steps {
        sh 'echo "Running tests..."'
        // รัน pytest ในคอนเทนเนอร์ python แยก (ไม่ต้องแก้ Dockerfile แอป)
        sh '''
          docker run --rm \
            -v "$WORKSPACE":/ws -w /ws \
            python:3.11-slim /bin/sh -lc "
              pip install --no-cache-dir -r requirements.txt pytest &&
              pytest -q
            "
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
          docker run -d --name $CONTAINER_NAME -p ${APP_PORT}:${APP_PORT} $IMAGE_NAME:latest
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
