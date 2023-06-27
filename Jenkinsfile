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
	  environment {
		DIFF_APP_HOST = credentials('app-host')
		DIFF_APP_USER = credentials('app-user')
		DIFF_APP_PASSWORD = credentials('app-password')		
	  }
      steps {
		sh 'rsync -av $(pwd)/. /var/www --exclude .git --exclude  vendor --exclude node_modules'
		sh 'sshpass -p $DIFF_APP_PASSWORD rsync -av /var/www $DIFF_APP_USER@$DIFF_APP_HOST:/var/'
		sh 'sshpass -p $DIFF_APP_PASSWORD ssh -tt $DIFF_APP_USER@$DIFF_APP_HOST -p 22 "cd /var/www/; composer install; npm install; php artisan optimize; php artisan storage:link"'
		sh 'sshpass -p $DIFF_APP_PASSWORD ssh -tt $DIFF_APP_USER@$DIFF_APP_HOST -p 22 "chown -R www-data: /var/www/storage"'
		sh 'sshpass -p $DIFF_APP_PASSWORD ssh -tt $DIFF_APP_USER@$DIFF_APP_HOST -p 22 "chown -R www-data: /var/www/bootstrap/cache"'
		sh 'sshpass -p $DIFF_APP_PASSWORD ssh -tt $DIFF_APP_USER@$DIFF_APP_HOST -p 22 "chown -R www:www /var/www"'
        sh 'sshpass -p $DIFF_APP_PASSWORD ssh -tt $DIFF_APP_USER@$DIFF_APP_HOST -p 22 "chown -R www-data:www-data /var/www"'
        sh 'sshpass -p $DIFF_APP_PASSWORD ssh -tt $DIFF_APP_USER@$DIFF_APP_HOST -p 22 "chmod -R 777 /var/www/storage/"'
      }
    }
  }
}