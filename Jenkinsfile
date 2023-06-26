pipeline {
  agent any
  stages {
    stage('Build') {
		environment {
			DIFF_DB_HOST = credentials('laravel-host')
			DIFF_DB_DATABASE = credentials('laravel-database')
			DIFF_DB_USERNAME = credentials('laravel-user')
			DIFF_DB_PASSWORD = credentials('laravel-password')
			DIFF_APP_HOST = credentials('app-host')
		}
		steps {
			sh 'composer install'
			sh 'cp .env.example .env'
			sh "sed -i 's/^DB_DATABASE=.*/&${DIFF_DB_DATABASE}/' .env"
            sh "sed -i 's/^DB_USERNAME=.*/&${DIFF_DB_USERNAME}/' .env"
            sh "sed -i 's|^DB_HOST=.*|&${DIFF_DB_HOST}|' .env"
			sh "sed -i 's|^DB_PASSWORD=.*|&${DIFF_DB_PASSWORD}|' .env"
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
	stage('deploy') {
      steps {
        sh 'cp -r $(pwd)/. /ic17app'
		sshagent(credentials : ['ssh-credentials-id']) {
			sh 'ssh -t root@172.18.0.3 -p 22 "cd /var/www && php artisan optimize"'
        }
      }
    }
  }
}