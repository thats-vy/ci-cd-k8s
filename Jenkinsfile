node{
  def Namespace = "default"
  def ImageName = "thatsvy/sayarapp"
  def Creds	= "docker_repo"
  try{
  stage('Checkout'){
      git 'https://github.com/thats-vy/ci-cd-k8s.git'
      sh "git rev-parse --short HEAD > .git/commit-id"
      imageTag= readFile('.git/commit-id').trim()
    }

  
  stage('RUN Unit Tests'){
      sh "npm install"
      sh "npm test"
    }
  stage('Docker Build, Push'){
    withDockerRegistry([credentialsId: "${Creds}", url: 'https://index.docker.io/v1/']) {
      sh "docker build -t ${ImageName}:${imageTag} ."
      sh "docker push ${ImageName}"
        }

    }
    stage('Deploy on K8s'){

     sh "ansible-playbook ansible/sayarapp-deploy/deploy.yml  --user=jenkins --extra-vars ImageName=${ImageName} --extra-vars imageTag=${imageTag} --extra-vars Namespace=${Namespace}"
    }
     } catch (err) {
      currentBuild.result = 'FAILURE'
    }
}
