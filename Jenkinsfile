pipeline {

  agent none

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
	sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}"
 
      }
    }

    stage ('Running on CentOS') {

      agent {
        label 'CentOS'
      }
     
      steps {

	sh "wget http://ubz8a1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
	sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }

    stage ('Running on Debian') {

      agent {
        docker 'openjdk:8u121-jre'
      }

      steps {

        sh "wget http://ubz8a1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 5"
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
        sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
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
	sh 'git checkout master'
	echo "Merging development into master"
        sh 'git merge development'
	echo "Push Origin to Master"
	sh 'git push origin master'
      }
    }
  }
}

