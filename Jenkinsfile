pipeline {
  agent any

  tools {
    nodejs 'node'
  }

  environment {
    IMAGE_TAG  = "v1.0"
    RAW_BRANCH = "${env.BRANCH_NAME ?: 'dev'}"
    BRANCH     = "${(RAW_BRANCH == 'main' || RAW_BRANCH == 'dev') ? RAW_BRANCH : 'dev'}"
    IMAGE_NAME = "node${BRANCH}"
  }

  stages {
    stage('Install') {
      steps {
        echo "🔧 Installing dependencies…"
        sh 'npm install'
      }
    }

    stage('Test') {
      steps {
        echo "🧪 Running tests…"
        sh 'npm test'
      }
    }

    stage('Docker Build') {
      steps {
        script {
          def srcLogo = "src/logos/logo-${BRANCH}.svg"
          def dstLogo = 'src/logo.svg'
          echo "📋 Copying ${srcLogo} → ${dstLogo}"
          sh "cp ${srcLogo} ${dstLogo}"
          def fullImage = "${IMAGE_NAME}:${IMAGE_TAG}"
          echo "🐳 Building Docker image ${fullImage}"
          sh "docker build -t ${fullImage} ."
        }
      }
    }

    stage('Deploy') {
      steps {
        script {
          def img      = "${IMAGE_NAME}:${IMAGE_TAG}"
          def name     = "${CONTAINER_NAME}"
          def hostPort = (BRANCH == 'main') ? 3000 : 3001

          echo "🔁 Stopping & removing old container (if any): ${name}"
          sh "docker rm -f ${name} || true"

          echo "🚀 Launching new container ${name} on port ${hostPort}"
          sh """
            docker run -d \
              --name ${name} \
              --expose 3000 \
              -p ${hostPort}:3000 \
              ${img}
          """

        }
      }
    }
  }

  post {
    always {
      cleanWs()
    }
    success {
      echo '✅ Pipeline completed successfully!'
    }
    failure {
      echo '❌ Pipeline failed. Check the logs above.'
    }
  }
}