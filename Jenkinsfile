
pipeline {
    agent any
    parameters {
    string(name: 'docker_image_name')
    string(name: 'release_version_tag_id' )
  }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Checkout from SCM") {
               steps {
                   git changelog: false, poll: false, url: 'https://github.com/umersyed98488/k8-manifest-files.git'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${params.docker_image_name}.*/${params.docker_image_name}:${params.release_version_tag_id}/g' deployment.yaml
                """
            }
        }

         stage("New Deploymnet File") {
            steps {
                sh " cat deployment.yaml " 
            }
        }
        stage("Commit Changes") {
    steps {
        script {
            echo "Configuring Git user..."
            sh 'git config --global user.name "umersyed98488"'
            sh 'git config --global user.email "syedumer8087@gmail.com"'

            echo "Adding changes to Git staging area..."
            sh 'git add deployment.yaml'

            echo "Committing changes..."
            try {
                sh 'git commit -m "Updated Deployment Manifest"'
            } catch (Exception e) {
                echo "Failed to commit changes: ${e.message}"
                currentBuild.result = 'FAILURE' // Mark build as failed explicitly
                error "Failed to commit changes"
            }
        }
    }
}
        stage("Push") {
            steps {
                withCredentials([gitUsernamePassword(credentialsId: '8', gitToolName: 'git-tool')]) {
                    sh "git push https://github.com/umersyed98488/k8-manifest-files.git master"
                }
            }
        }
    }
}
