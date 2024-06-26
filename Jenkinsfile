pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        DOCKERTAG = "${env.BUILD_NUMBER}" // or however you want to define this
    }
    stages {
        stage('Git Checkout') {
            steps {
                echo 'Cloning the Project from Github to Jenkins Workspace'
                git branch: 'main', credentialsId: 'github_cred', url: 'https://github.com/er-sanjaysah/update-manifest-file.git'
            }
        }
        
        stage("Update Deployment Manifest") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github_cred', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh """
                        git config user.email sanjaysah@prodios.com
                        git config user.name er-sanjaysah
                        cat deployment.yml
                        sed -i 's+ssah6694/devops.*+ssah6694/devops:${DOCKERTAG}+g' dev/deployment.yml
                        cat deployment.yml
                        git add .
                        git commit -m 'Jenkins update in manifest: ${env.BUILD_NUMBER}'
                        
                        # Print the constructed URL with password masked
                        echo "Pushing to: https://${GIT_USERNAME}:****@github.com/${GIT_USERNAME}/update-manifest-file.git"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/update-manifest-file.git HEAD:main
                    """
                }
            }
        }
    }
}
