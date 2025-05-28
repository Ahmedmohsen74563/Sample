pipeline {
    agent any

    environment {
        SOLUTION = '**/*.csproj'
        BUILD_PLATFORM = 'Any CPU'
        BUILD_CONFIGURATION = 'Release'
        ARTIFACT_NAME = 'dotnet9app'
        // You can also load credentials here using withCredentials
    }

    stages {
        stage('Build') {
            steps {
                // Install .NET SDK 9.0.300
                bat 'dotnet-install.ps1 -Version 9.0.300'
                // or ensure it's pre-installed on your agents

                // Restore dependencies
                bat "dotnet restore ${SOLUTION}"

                // Build the project
                bat "dotnet build ${SOLUTION} --configuration ${BUILD_CONFIGURATION}"

                // Publish the app to a folder
                bat "dotnet publish --configuration ${BUILD_CONFIGURATION} --output ${env.WORKSPACE}\\artifacts"
                
                // Archive artifacts (similar to PublishBuildArtifacts)
                archiveArtifacts artifacts: 'artifacts/**/*', fingerprint: true
                
                // Alternative: Stash for use in other stages
                stash name: 'dotnet-artifacts', includes: 'artifacts/**/*'
            }
        }
    }
}
