pipeline {
    agent any
    parameters {
        string(name: 'RequiredFreeSpace', defaultValue: '10', description: 'The percentage of free space required')
        string(name: 'LogFilePath', defaultValue: 'jenkinslog.txt', description: 'Log file path (relative to workspace)')
        string(name: 'ErrorString', defaultValue: 'mvn', description: 'Error string to search for')
    }
    stages {
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

                    echo "Free space: ${FREESPACE}%"
                    if (FREESPACE.toFloat() < params.RequiredFreeSpace.toFloat()) {
                        echo "Not enough free space. Checking for errors in the log file."
                        if (fileContent.contains(params.ErrorString)) {
                            error "Error found: '${params.ErrorString}' in file '${params.LogFilePath}'"
                        } else {
                            echo "No errors found with '${params.ErrorString}' in file '${params.LogFilePath}'"
                        }
                    } else {
                        echo "Sufficient storage space available."
                    }
                }
            }
        }
    }
}