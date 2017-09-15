timestamps {
  ansiColor('xterm') {
    node('docker') {
      stage('Start Docker')
      docker.image('node:8.2.1').inside('-u 0:0') {
        stage('Checkout') {
          checkout scm
        }
        stage('Build') {
          sh 'npm install --unsafe-perm'
        }
        stage('Publish NPM') {
          wrap([$class: 'BuildUser']) {
            withCredentials([usernameColonPassword(credentialsId: 'nexus', variable: 'auth')]) {
              def ver = sh(returnStdout: true, script: "npm version | grep \"{\" | tr -s ':'  | cut -d \"'\" -f 2").trim()
              sh "npm version ${ver}-${BRANCH_NAME}-${BUILD_NUMBER} --no-git-tag-version"
              def _auth = env.auth.bytes.encodeBase64().toString()
              def registry = 'http://cart:8081/repository/npm-hosted/'
              sh "npm publish --unsafe-perm --_auth=${_auth} --registry=${registry} --email=${BUILD_USER_EMAIL}"
            }
          }
        }
      }
    }
  }
}
