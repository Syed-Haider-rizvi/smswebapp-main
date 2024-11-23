pipeline {
    agent any

    environment {
        DOTNET_CLI_HOME = "C:\\Program Files\\dotnet"
    }

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
                    //bat "cd ${DOTNET_CLI_HOME} && dotnet restore"
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
    }
        stage('Deploy') {
            steps {
                // Deploy the application (example for IIS)
                script {
                    bat """
                    powershell -Command "
                    Stop-WebAppPool -Name 'coreapp3';
                    Copy-Item -Path ./publish/* -Destination 'C:\\inetpub\\wwwroot\\coreapp3' -Recurse -Force;
                    Start-WebAppPool -Name 'coreapp3';
                    "

                }
                }
            }
        }
    }


    post {
        success {
            echo 'Build, test, and publish successful!'
        }
    }

