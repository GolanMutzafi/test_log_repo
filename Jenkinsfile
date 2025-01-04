pipeline{
    agent any
    parameters{
        string(name: 'RequiredFreeSpace', defaultValue: '10', description: 'The percentage of free space required')
        string(name: 'LogFilePath', defaultValue: '/var/log/jenkins/jenkins.log ', description: 'log file path')
        string(name: 'ErrorString', defaultValue: 'ERROR', description: 'error string for search')
    }
        stages{
            stage("space_test"){
                steps{
                    script{
                        def FREESPACE = sh(
                        script: "df / | tail -1 | awk '{print \$4/\$2 * 100}'",
                        returnStdout: true
                    ).trim()
                    if (FREESPACE.toFloat() < RequiredFreeSpace.toFloat()){
                        def result = sh(
                            script: "grep -q '${params.ErrorString}' '${params.LogFilePath}'",
                            returnStatus: true
                        )
                        if (result == 0){
                            error "Error found: '${params.ErrorString}' in file '${params.LogFilePath}'"
                        }else{
                            echo "no errors found with: '${params.ErrorString}' in file '${params.LogFilePath}'"
                        }
                    }
                    else{
                        echo "there is sufficient storage space in the disk"
                    }
                }
            }
        }
    }
}