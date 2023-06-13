pipeline {
  agent any
  stages {
    stage('Build') {
		environment {
			DB_HOST = credentials("laravel-host")
			DB_DATABASE = credentials("laravel-database")
			DB_USERNAME = credentials("laravel-user")
			DB_PASSWORD = credentials("laravel-password")
		}
		steps {
			bat 'composer install'
			bat 'cp .env.example .env'
			bat 'echo DB_HOST=${DB_HOST} >> .env'
			bat 'echo DB_USERNAME=${DB_USERNAME} >> .env'
			bat 'echo DB_DATABASE=${DB_DATABASE} >> .env'
            bat 'echo DB_PASSWORD=${DB_PASSWORD} >> .env'
			bat 'php artisan key:generate'
			bat 'npm install'
			bat 'npm run prod'
		}
    }

    stage('Test') {
      steps {
        bat './vendor/bin/phpunit'
      }
    }
  }
}