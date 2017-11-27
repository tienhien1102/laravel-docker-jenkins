node {
    checkout scm

    stage('Build') {
        parallel master:{
            node ('master'){
                checkout scm
                sh 'cd src && compose install'
                docker.build("kyo88kyo/nginx", "-f Dockerfile-nginx .")
                docker.build("kyo88kyo/blog")
            }
        }
    }

    stage('Test') {
        docker.image('kyo88kyo/blog').inside {
            sh 'sleep 2'
            sh 'php --version'
            sh 'cd /var/www/blog && ./vendor/bin/phpunit --testsuite Unit'
        }
    }


    stage('Deploy') {
        sh 'cd src && /usr/local/bin/docker-compose down'
        sh 'cd src && /usr/local/bin/docker-compose up -d'
        sh 'sleep 10 && cd src && /usr/local/bin/docker-compose run web php artisan migrate'
    }

    stage ('Test Feature') {
        sh 'cd src && /usr/local/bin/docker-compose run web ./vendor/bin/phpunit --testsuite Feature'
    }
}