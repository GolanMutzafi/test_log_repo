pipeline {
    agent any
    parameters {
        string(name: 'RequiredFreeSpace', defaultValue: '10', description: 'The percentage of free space required')
        string(name: 'LogFilePath', defaultValue: '/home/golan/Desktop/gptmission/log_test_repo/jenkinslog.txt', description: 'Log file path')
        string(name: 'ErrorString', defaultValue: 'mvn', description: 'Error string for search')
    }
    stages {
        stage("space_test") {
            steps {
                script {
                    def fileContent = readFile(params.LogFilePath)
                    def FREESPACE = sh(
                        script: "df / | tail -1 | awk '{print \$4/\$2 * 100}'",
                        returnStdout: true
                    ).trim()

                    if (fileContent.contains(params.ErrorString)) {
                        echo "The text '${params.ErrorString}' was found in ${params.LogFilePath}"
                    }
                    
                    if (FREESPACE.toFloat() < params.RequiredFreeSpace.toFloat()) {
                        def result = sh(
                            script: "grep -q '${params.ErrorString}' '${params.LogFilePath}'",
                            returnStatus: true
                        )
                        if (result == 0 && fileExists(params.LogFilePath)) {
                            error "Error found: '${params.ErrorString}' in file '${params.LogFilePath}'"
                        } else {
                            echo "No errors found with: '${params.ErrorString}' in file '${params.LogFilePath}'"
                        }
                    } else {
                        echo "There is sufficient storage space on the disk"
                    }
                }
            }
        }
    }
}