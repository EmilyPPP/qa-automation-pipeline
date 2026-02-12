pipeline {
  agent any

  tools {
    nodejs 'node18'
  }
  
  options { timestamps(); disableConcurrentBuilds() }

  environment {
    CI = 'true'
    PLAYWRIGHT_BROWSERS_PATH = '0'
  }

  stages {
    stage('Checkout (dev repo)') {
      steps {
        checkout scm
      }
    }

    stage('Checkout test repo') {
      steps {
        dir('test-automation') {
          git branch: 'feature/add-kurly-test',
              url: 'https://github.com/EmilyPPP/playwright_study.git',
              credentialsId: 'github-pat'
        }
      }
    }

    stage('Install') {
      steps {
        dir('test-automation') {
          sh 'corepack enable || true'
          sh 'yarn install --frozen-lockfile'
          sh 'npx playwright install'
        }
      }
    }

    stage('Run E2E') {
      steps {
        withCredentials([
          string(credentialsId: 'KURLY_ID', variable: 'KURLY_ID'),
          string(credentialsId: 'KURLY_PW', variable: 'KURLY_PW'),
          string(credentialsId: 'ADDRESS', variable: 'ADDRESS'),
          string(credentialsId: 'DETAILADDRESS', variable: 'DETAILADDRESS'),
          string(credentialsId: 'DOORPASSWORD', variable: 'DOORPASSWORD'),
          string(credentialsId: 'NAVER_ID', variable: 'NAVER_ID'),
          string(credentialsId: 'NAVER_PW', variable: 'NAVER_PW'),
          string(credentialsId: 'NAVERPAY_PW', variable: 'NAVERPAY_PW')
        ]) {
          dir('test-automation') {
            sh 'yarn test:setup'
            sh 'yarn test:e2e'
            sh 'ls -la . && ls -la playwright-report || true'
          }
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'test-automation/test-results/**,test-automation/playwright-report/**,test-automation/allure-results/**',
                       allowEmptyArchive: true
    }
  }
}
