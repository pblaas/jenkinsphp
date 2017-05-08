podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'docker', image: 'docker:latest', ttyEnabled: true, command: 'cat'),
  ]) {

  node('mypod') {
    stage ('Retrieve Code'){
    git url: 'https://github.com/pblaas/jenkinsphp.git'
    } 
    stage ('Build Docker image'){
    sh 'which docker;docker version'
    def image = docker.build('pblaas/simplephp:snapshot', '.')
    } 
    stage ('Push Docker image'){
    docker.withRegistry("https://registry.hub.docker.com", "docker-registry") {
        image.push()
      }
    }
  }
}
