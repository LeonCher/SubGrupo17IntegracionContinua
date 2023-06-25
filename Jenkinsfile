pipeline {
  agent any
  stages {
    stage('Build') {
		environment {
			DIFF_DB_HOST = credentials('laravel-host')
			DIFF_DB_DATABASE = credentials('laravel-database')
			DIFF_DB_USERNAME = credentials('laravel-user')
			DIFF_DB_PASSWORD = credentials('laravel-password')
		}
		steps {
			sh 'composer install'
			sh 'cp .env.example .env'
			sh 'echo DB_HOST=${DIFF_DB_HOST} >> .env'
			sh 'echo DB_USERNAME=${DIFF_DB_USERNAME} >> .env'
			sh 'echo DB_DATABASE=${DIFF_DB_DATABASE} >> .env'
            sh 'echo DB_PASSWORD=${DIFF_DB_PASSWORD} >> .env'
			sh 'php artisan key:generate'
			sh 'php artisan optimize'
			sh 'npm install'
			sh 'php artisan migrate'
		}
    }

    stage('Test') {
      steps {
        sh './vendor/bin/phpunit'
      }
    }
  }
}