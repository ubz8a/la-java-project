pipeline {

  agent none

  environment {

    MAJOR_VERSION = 1
  }

  stages {


    stage ('build') {

      agent {
        label 'Apache'
      }

      steps {
        sh 'ant -f build.xml -v'
      }

      post {

        success {

      	  archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true

        }
      }
    }

    stage ('deploy') {

      agent {
        label 'Apache'
      }

      steps {
	sh "if ![ -d '/var/www/html/rectangles/all/${env.BRANCH_NAME}']; then mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}"
 
      }
    }

    stage ('Running on CentOS') {

      agent {
        label 'CentOS'
      }
     
      steps {

	sh "wget http://ubz8a1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
	sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }

    stage ('Running on Debian') {

      agent {
        docker 'openjdk:8u121-jre'
      }

      steps {

        sh "wget http://ubz8a1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 5"
      }
    }

    stage ('Promote to green'){

      agent {
        label 'Apache'
      }

      when {
        branch 'master'     
      }

      steps {
        sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      } 
    }

    stage ('Promote development to master') {

      agent {
	label 'Apache'
      }
      when {
	branch 'development'
      }
      steps {
        echo "Stashing Any Local Changes"
	sh 'git stash'
	echo "Checking Out Development Branch"
	sh 'git checkout development'
	echo "Checking Out Master Branch"
	sh 'git pull origin'
	sh 'git checkout master'
	echo "Merging development into master"
        sh 'git merge development'
	echo "Push Origin to Master"
	sh 'git push origin master'
	echo "Tag release"
	sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
	sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"

      }
    }
  }
}

