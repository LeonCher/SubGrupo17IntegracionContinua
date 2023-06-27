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
		sh 'rsync -av $(pwd)/. /var/www --exclude .git --exclude  vendor --exclude node_modules'
		sh 'sshpass -p ic17root rsync -av /var/www root@172.18.0.2:/var/'
		sh 'sshpass -p ic17root ssh -tt root@172.18.0.2 -p 22 "cd /var/www/; composer install; npm install; php artisan optimize; php artisan storage:link"'
		sh 'sshpass -p ic17root ssh -tt root@172.18.0.2 -p 22 "chown -R www-data: /var/www/storage"'
		sh 'sshpass -p ic17root ssh -tt root@172.18.0.2 -p 22 "chown -R www-data: /var/www/bootstrap/cache"'
		sh 'sshpass -p ic17root ssh -tt root@172.18.0.2 -p 22 "chown -R www:www /var/www"'
        sh 'sshpass -p ic17root ssh -tt root@172.18.0.2 -p 22 "chown -R www-data:www-data /var/www"'
        sh 'sshpass -p ic17root ssh -tt root@172.18.0.2 -p 22 "chmod -R 777 /var/www/storage/"'
      }
    }
  }
}