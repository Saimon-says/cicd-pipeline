pipeline {
  agent any

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
          def fullImage = "${IMAGE_NAME}:${IMAGE_TAG}"
          echo "🚀 Deploying ${fullImage}"
          //sh "docker push ${fullImage}"
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