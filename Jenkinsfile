pipeline {
   agent any

    environment {
        // Variables
        SOLUTION = '**/*.csproj'
        BUILD_CONFIGURATION = 'Release'
        ARTIFACT_NAME = 'dotnet9app'
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
    }

    stages {
        stage('Install .NET SDK 9.0.300') {
            steps {
                powershell '''
                    Invoke-WebRequest -Uri https://dotnet.microsoft.com/download/dotnet/scripts/v1/dotnet-install.ps1 -OutFile dotnet-install.ps1
                    ./dotnet-install.ps1 -Version 9.0.300 -InstallDir "$env:USERPROFILE\\dotnet"
                    $env:PATH = "$env:USERPROFILE\\dotnet;" + $env:PATH
                '''
            }
        }

        stage('Restore Dependencies') {
            steps {
                powershell '''
                    $projects = Get-ChildItem -Recurse -Filter *.csproj | ForEach-Object { $_.FullName }
                    foreach ($proj in $projects) {
                        dotnet restore "$proj"
                    }
                '''
            }
        }

        stage('Build Project') {
            steps {
                powershell '''
                    $projects = Get-ChildItem -Recurse -Filter *.csproj | ForEach-Object { $_.FullName }
                    foreach ($proj in $projects) {
                        dotnet build "$proj" --configuration $env:BUILD_CONFIGURATION
                    }
                '''
            }
        }

        stage('Publish App') {
            steps {
                powershell '''
                    $publishDir = "publish_output"
                    dotnet publish --configuration $env:BUILD_CONFIGURATION --output $publishDir
                    Compress-Archive -Path "$publishDir\\*" -DestinationPath "$env:ARTIFACT_NAME.zip"
                '''
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: "${ARTIFACT_NAME}.zip", fingerprint: true
            }
        }
    }
}
