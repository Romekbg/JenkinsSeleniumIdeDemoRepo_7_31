pipeline {
    agent any

    stages {
        stage('Checkout code') {
            steps {
                git branch: 'main', url: 'https://github.com/Romekbg/JenkinsSeleniumIdeDemoRepo_7_31'
            }
        }
        stage('Set up .Net Core') {
            steps {
                bat '''
                echo Downloading .Net 6 SDK
                curl -L -o dotnet-install.ps1 https://dot.net/v1/dotnet-install.ps1
                echo Installing .Net 6 SDK
                powershell -ExecutionPolicy Bypass -File dotnet-install.ps1 -Version 6.0.132
                echo Verifying .Net SDK installation
                dotnet --version
                '''
            }
        }
        stage('Restore nuget packages') {
            steps {
                bat 'dotnet restore SeleniumIde.sln'
            }
        }
        stage('Build') {
            steps {
                bat 'dotnet build SeleniumIde.sln --configuration Release'
            }
        }
        stage('Run tests') {
            steps {
                bat 'dotnet test SeleniumIde.sln --logger "trx;LogFileName=TestResults.trx"'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/TestResults/*.trx', allowEmptyArchive: true
            step([
                $class: 'MSTestPublisher',
                testResultsFile: '**/TestResults/*.trx'
            ])
        }
    }
}
