pipeline {
  agent {
    docker {
      image 'docker:20.10.7'
      // แก้ปัญหา ENTRYPOINT ของ docker image และผูก docker.sock ให้คอนเทนเนอร์นี้สั่ง docker ของโฮสต์ได้
      args "--entrypoint='' -v /var/run/docker.sock:/var/run/docker.sock"
    }
  }

  // ปิด default checkout ของ Declarative เพื่อไม่ให้ซ้ำกับ stage Checkout ด้านล่าง
  options { skipDefaultCheckout(true) }

  environment {
    IMAGE_NAME     = 'jenkins-demo-app'
    CONTAINER_NAME = 'demo-app'
    APP_PORT       = '8081'   // ตาม Exercise 2
  }

  stages {
    stage('Checkout') {
      steps {
        // ถ้า repo เป็น public ใช้แบบนี้ได้เลย (ไม่ต้องใส่ credentials)
        git branch: 'main', url: 'https://github.com/Thanaphum-6510110194/jenkins-demo-app.git'
        // ถ้าเป็น private ให้เพิ่ม credentialsId: 'xxx-yyy-zzz'
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker version && docker build -t $IMAGE_NAME:latest .'
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

    stage('Verify') {
      steps {
        sh 'docker ps --filter name=$CONTAINER_NAME --format "table {{.Names}}\\t{{.Ports}}"'
        // อยากเช็ค log แอปก็เปิดคอมเมนต์บรรทัดล่างได้
        // sh 'docker logs --tail=50 $CONTAINER_NAME || true'
      }
    }
  }

  post {
    always {
      sh 'docker ps --format "table {{.Names}}\\t{{.Image}}\\t{{.Ports}}" || true'
    }
  }
}
