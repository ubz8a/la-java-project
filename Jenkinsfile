pipeline {

  agent {

    label 'master'
  }

  stages {


    stage ('unit-test') {

      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }

    stage ('build') {

      steps {
        sh 'ant -f build.xml -v'
      }
    }

    stage ('deploy') {
 
      steps {

        sh "cp dist/rectangle_${env.BUID_NUMBER}.jar" /vr.www/html/rectangles/all/"
 
      }
    }

  }



  post {

    always {

      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true

    }

  }


}
