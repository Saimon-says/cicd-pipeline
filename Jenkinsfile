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
          // pull your env vars from `env`
          def branch        = env.BRANCH
          def containerName = "node${branch}"
          def image         = "${containerName}:${env.IMAGE_TAG}"
          def hostPort      = (branch == 'main') ? 3000 : 3001

          echo "🔁 Stopping & removing any old container named ${containerName}"
          sh "docker rm -f ${containerName} || true"

          echo "🚀 Launching new container ${containerName} on port ${hostPort}"
          sh """
              docker run -d \
              --name ${containerName} \
              --expose 3000 \
              -p ${hostPort}:3000 \
              ${image}
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