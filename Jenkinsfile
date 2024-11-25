pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Restoring dependencies
                    bat "dotnet restore"

                    // Building the application
                    bat "dotnet build --configuration Release"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Running tests
                    bat "dotnet test --no-restore --configuration Release"
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    // Publishing the application
                    bat "dotnet publish --no-restore --configuration Release --output .\\publish"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'coreuser', passwordVariable: 'CREDENTIAL_PASSWORD', usernameVariable: 'CREDENTIAL_USERNAME')]) {
                        powershell '''
                        # Create a PSCredential object using the Jenkins credentials
                        $credentials = New-Object System.Management.Automation.PSCredential($env:CREDENTIAL_USERNAME, (ConvertTo-SecureString $env:CREDENTIAL_PASSWORD -AsPlainText -Force))

                        # Map the network drive
                        New-PSDrive -Name X -PSProvider FileSystem -Root "\\\\172.16.14.79\\coreapp3" -Persist -Credential $credentials

                        # Copy the published files to the network share
                        Copy-Item -Path '.\\publish\\*' -Destination 'X:\\' -Force

                        # Remove the network drive
                        Remove-PSDrive -Name X
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build, test, and publish successful!'
        }

        failure {
            echo 'Build, test, or publish failed.'
        }
    }
}
