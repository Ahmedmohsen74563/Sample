pipeline {
   agent any

    environment {
        // Environment variables
        SOLUTION = '**/*.csproj'
        BUILD_PLATFORM = 'Any CPU'
        BUILD_CONFIGURATION = 'Release'
        ARTIFACT_NAME = 'dotnet9app'

        // Credentials from Jenkins Credential Store (example)
        // DOTNET_AUTH_TOKEN = credentials('my-dotnet-nuget-token') // optional if you use private feeds
    }

    stages {
        stage('Install .NET SDK 9.0.300') {
            steps {
                // Assumes dotnet-install.ps1 or equivalent is used
                powershell '''
                    Invoke-WebRequest -Uri https://dotnet.microsoft.com/download/dotnet/scripts/v1/dotnet-install.ps1 -OutFile dotnet-install.ps1
                    ./dotnet-install.ps1 -Version 9.0.300 -InstallDir "$env:USERPROFILE\\dotnet"
                    $env:PATH = "$env:USERPROFILE\\dotnet;" + $env:PATH
                '''
            }
        }

        stage('Restore Dependencies') {
            steps {
                bat "dotnet restore ${env.SOLUTION}"
            }
        }

        stage('Build Project') {
            steps {
                bat "dotnet build ${env.SOLUTION} --configuration ${env.BUILD_CONFIGURATION}"
            }
        }

        stage('Publish App') {
            steps {
                bat "dotnet publish ${env.SOLUTION} --configuration ${env.BUILD_CONFIGURATION} --output publish_output"
                powershell "Compress-Archive -Path publish_output\\* -DestinationPath ${env.ARTIFACT_NAME}.zip"
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: "${env.ARTIFACT_NAME}.zip", fingerprint: true
            }
        }
    }
}
