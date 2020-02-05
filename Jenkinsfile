pipeline {
    agent none
    environment {         
        branch = 'master'
        packagePath = ''
        packageFolder = "C:\\ProgramData\\UiPath\\Packages"
        orchestratorURL = "https://rpapreprod.oslo.kommune.no/"
        machineKey = "52b41bfa-ce1a-4350-874c-d3a19e8612af"
        nugetRepoURL = "https://www.myget.org/F/oslokommune/api/v2"
        nugetApiKey = "0791c8ce-4b4c-458f-9f29-a0be9df03f1d"
        updatePackage = ''
        deployPackage = ''
        processID = 125
    }
    stages {
        stage("Code Checkout") {
            agent any
            steps {
                echo "Checking out the code"
                checkout([$class: 'GitSCM',
                branches: [[name: '*/master']],
                doGenerateSubmoduleConfigurations: false,
                extensions: [[$class: 'WipeWorkspace']],
                submoduleCfg: [],
                userRemoteConfigs: [[credentialsId: '569219a0-53b5-440f-a9c7-cd296d696641', url: 'https://github.oslo.kommune.no/Kiran-Sukumar/Kiran-Test.git']]
                ])
            }
        }
 
        stage('Koble til Orchestrator') {
            agent any
            steps {
                echo "Connecting the Robot"
                bat "UiRobot.exe --connect -url ${orchestratorURL} -key ${machineKey}"
            }
        }
 
        stage('Bygge Pakke') {
            agent any
            steps {
            echo "Building the package"
            echo "The workspace is ${workspace}"
            bat "UiRobot.exe -pack ${workspace}\\project.json -o ${packageFolder}"
            }
        }
         
        stage("Publisere Pakke") {
            agent any
            steps {
                script {
                packagePath = powershell(returnStdout: true, script:"(Get-ChildItem '${packageFolder}' -Recurse -Filter '*.nupkg' | Sort-Object LastWriteTime -Descending)[0].Fullname")
                 
                deployPackage = powershell(returnStdout: true, script:"""Get-UiPathAuthToken -URL https://rpapreprod.oslo.kommune.no -WindowsCredentials -Session
                Add-UiPathPackage -PackageFile """+"${packagePath}")
            }
             
                echo "Deploying the package ${packagePath}"
                //bat "NuGet.exe push -ApiKey ${nugetApiKey} -Source ${nugetRepoURL} -NonInteractive ${packagePath}"
            }
        }
         
        stage("Oppdatere Process") {
            agent any
            steps {
                script {
                updatePackage = powershell(returnStdout: true, script: """
                Get-UiPathAuthToken -URL https://rpapreprod.oslo.kommune.no -WindowsCredentials -Session
                Update-UiPathProcess -Id 125 -Latest""")
            }
            }
        }
         
        stage("Koble fra Orchestrator") {
            agent any
            steps {
            echo "Disconnecting the Robot"
            bat 'UiRobot.exe disconnect'
            }
        }
    }
}