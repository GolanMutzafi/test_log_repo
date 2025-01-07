pipeline {
    agent any
    parameters {
        string(name: 'RequiredFreeSpace', defaultValue: '10', description: 'The percentage of free space required')
        string(name: 'LogFilePath', defaultValue: 'jenkinslog.txt', description: 'Relative path to the log file')
        string(name: 'ErrorString', defaultValue: 'mvn', description: 'Error string to search for')
    }
    stages {
        stage("space_test") {
            steps {
                script {
                    def filePath = "${params.LogFilePath}"
                    if (!fileExists(filePath)) {
                        error "Log file '${filePath}' not found in workspace."
                    }
                    def fileContent = readFile(filePath)
                    def freeSpace = sh(
                        script: "df / | tail -1 | awk '{print \$4/\$2 * 100}'",
                        returnStdout: true
                    ).trim()

                    if (fileContent.contains(params.ErrorString)) {
                        echo "The text '${params.ErrorString}' was found in '${filePath}'"
                    }

                    if (freeSpace.toFloat() < params.RequiredFreeSpace.toFloat()) {
                        def result = sh(
                            script: "grep -q '${params.ErrorString}' '${filePath}'",
                            returnStatus: true
                        )
                        if (result == 0) {
                            error "Error found: '${params.ErrorString}' in file '${filePath}'"
                        } else {
                            echo "No errors found with '${params.ErrorString}' in file '${filePath}'"
                        }
                    } else {
                        echo "There is sufficient storage space on the disk."
                    }
                }
            }
        }
    }
}