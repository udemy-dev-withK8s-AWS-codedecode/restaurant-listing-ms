pipeline {
  agent any

  environment {
    DOCKER_REGISTRY = "docker.io"
    DOCKERHUB_CREDENTIALS = credentials('DOCKER_HUB_CREDENTIAL')
    GIT_TOKEN = credentials('git-personal-token')
    REPO_URL = 'https://github.com/udemy-dev-withK8s-AWS-codedecode/deployment-folder.git'
    VERSION = "${env.BUILD_ID}"

  }

  tools {
    // Add Maven tool with name "M3"
    maven "Maven"
  }

  stages {
   /*  stage('Clone Repository') {
      steps {
      checkout scmGit(branches: [[name: '*//* master']], extensions: [], userRemoteConfigs: [[credentialsId: 'git-ssh', url: 'git@github.com:udemy-code-decode/restaurant-listing-service.git']])
      }
    } */

    stage('Maven Build'){
        steps{
        sh 'mvn clean package  -DskipTests'
        }
    }

    /*  stage('Run Tests') {
      steps {
        sh 'mvn test'
      }
    }

    stage('SonarQube Analysis') {
  steps {
    sh 'mvn clean org.jacoco:jacoco-maven-plugin:prepare-agent install sonar:sonar -Dsonar.host.url=http://3.26.201.237:9000/ -Dsonar.login=squ_64aeb95ed58d2043f2f70cd21b90066f6ea7d468'
  }
}


   stage('Check code coverage') {
            steps {
                script {
                    def token = "squ_64aeb95ed58d2043f2f70cd21b90066f6ea7d468"
                    def sonarQubeUrl = "http://3.26.201.237:9000/api"
                    def componentKey = "restaurantListing"
                    def coverageThreshold = 80.0

                    def response = sh (
                        script: "curl -H 'Authorization: Bearer ${token}' '${sonarQubeUrl}/measures/component?component=${componentKey}&metricKeys=coverage'",
                        returnStdout: true
                    ).trim()

                    def coverage = sh (
                        script: "echo '${response}' | jq -r '.component.measures[0].value'",
                        returnStdout: true
                    ).trim().toDouble()

                    echo "Coverage: ${coverage}"

                    if (coverage < coverageThreshold) {
                        error "Coverage is below the threshold of ${coverageThreshold}%. Aborting the pipeline."
                    }
                }
            }
        } */


  /*    stage('Docker Build and Push') {
      steps {
          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
          sh 'docker build -t codedecode25/restaurant-listing-service:${VERSION} .'
          sh 'docker push codedecode25/restaurant-listing-service:${VERSION}'
      }
    } */


     stage('Cleanup Workspace') {
      steps {
        deleteDir()
        script{

           sh "git clone ${env.REPO_URL}"
           sh 'cd deployment-folder'
           sh 'pwd'
           sh '''
          sed -i "s/image:.*/image: codedecode25\\/restaurant-listing-service:${VERSION}/" aws/restaurant-manifest.yml
        '''

          sh 'git checkout master'
          sh 'git add .'
          sh 'git commit -m "Update image tag"'
          sh "git -c http.extraheader='Authorization: Bearer ${env.GIT_TOKEN}' push origin master --quiet"
        }

       
      }
    }


    /*stage('Deploy to EKS') {
      environment {
        AWS_DEFAULT_REGION = "${AWS_REGION}"
      }
      steps {
        sh 'aws eks update-kubeconfig --name ${EKS_CLUSTER_NAME} --region ${AWS_REGION}'
        dir('k8s') {
           sh '/usr/local/bin/kubectl  version --short --client'
          sh '/usr/local/bin/kubectl  apply -f .'
        }
      }
    }*/

    stage('Update Image Tag in GitOps') {
      steps {
         checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[ credentialsId: 'git-personal-token', url: 'https://github.com/udemy-dev-withK8s-AWS-codedecode/deployment-folder.git']])
        script {
          // Set the new image tag with the Jenkins build number
       sh '''
          sed -i "s/image:.*/image: codedecode25\\/restaurant-listing-service:${VERSION}/" aws/restaurant-manifest.yml
        '''

          sh 'git checkout master'
          sh 'git add .'
          sh 'git commit -m "Update image tag"'
          sh 'git push'
        // sshagent(['git-ssh'])
        //     {
        //           sh('git push')
        //     }
        }
      }
    }

  }

}


