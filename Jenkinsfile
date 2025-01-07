pipeline {
    agent any
    parameters {
        string(name: 'RequiredFreeSpace', defaultValue: '10', description: 'The percentage of free space required')
        string(name: 'LogFilePath', defaultValue: 'jenkinslog.txt', description: 'Log file path')
        string(name: 'ErrorString', defaultValue: 'mvn', description: 'Error string for search')
    }
    stages {
        stage("Prepare Environment") {
            steps {
                script {
                    // Copy the log file to the workspace if needed
                    if (!fileExists(params.LogFilePath)) {
                        echo "Attempting to copy log file from outside workspace..."
                        sh "cp ../jenkinslog.txt ${params.LogFilePath}" // Adjust the path if necessary
                    }
                }
            }
        }
        stage("space_test") {
            steps {
                script {
                    if (!fileExists(params.LogFilePath)) {
                        error "Log file '${params.LogFilePath}' not found in workspace."
                    }
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