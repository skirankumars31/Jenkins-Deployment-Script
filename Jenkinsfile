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
        processID = ''
		repo = ''
    }
    stages {
	
		stage("Get Process Details") {
            agent any
            steps {
			script {
                echo "Getting the process details"
				processID = powershell(returnStdout: true, script:"""Get-UiPathAuthToken -URL '${orchestratorURL}' -WindowsCredentials -Session | Out-Null ; Get-UiPathProcess -Name Kiran-Test_01_KiranTestMiljo | Select-Object -Property Id | Select -ExpandProperty Id """)
				echo "The Process ID is ${processID}"
				}
            }
        }
	
	
        stage("Code Checkout") {
            agent any
            steps {
			script {
			def props = readProperties  file: 'repo.properties'
			repo= props["${params.Project_Name}"]
			}
                echo "Checking out the code from the repo ${repo}"
				echo "Project name is ${params.Project_Name}"
                checkout([$class: 'GitSCM',
                branches: [[name: '*/master']],
                doGenerateSubmoduleConfigurations: false,
                extensions: [[$class: 'WipeWorkspace']],
                submoduleCfg: [],
                userRemoteConfigs: [[credentialsId: '569219a0-53b5-440f-a9c7-cd296d696641', url: "${repo}"]]
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
                 
                deployPackage = powershell(returnStdout: true, script:"""Get-UiPathAuthToken -URL '${orchestratorURL}' -WindowsCredentials -Session
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
                Get-UiPathAuthToken -URL '${orchestratorURL}' -WindowsCredentials -Session
                Update-UiPathProcess -Id '${processID}' -Latest""")
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