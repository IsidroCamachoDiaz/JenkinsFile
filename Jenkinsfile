pipeline {
    agent any
    stages {
        stage('Run JMeter Tests') {
            steps {
                script {
                    // Define la ruta completa al archivo JMX en tu máquina local (indicar archivo jmx)
                    def jmxFile = ""
                    
                    // Verifica si el archivo existe
                    if (fileExists(jmxFile)) {
                        echo "Archivo JMeter encontrado en: ${jmxFile}"
                    } else {
                        error "El archivo JMeter no se encontró en la ruta especificada."
                    }

                    // Ejecuta JMeter desde la línea de comandos en Windows (indicar ruta de jmeter)
                    bat """
                        set JMETER_HOME=
                        set PATH=%JMETER_HOME%\\bin;%PATH%

                        jmeter -n -t ${jmxFile} -l results.jtl
                    """

                    // Verifica si el archivo de resultados ha sido generado
                    if (fileExists('results.jtl')) {
                        echo "Prueba JMeter completada y resultados guardados en results.jtl"
                    } else {
                        error "La prueba JMeter no generó el archivo de resultados esperado."
                    }
                }
            }
        }
    }
    post {
        always {
            // Archivar los resultados
            archiveArtifacts artifacts: 'results.jtl', allowEmptyArchive: true
        }
        failure {
            // Manejar el fallo del pipeline
            echo 'La ejecución del pipeline falló.'
        }
        success {
    // Generar gráfico de rendimiento (indicar ruta de jmeter)
    script {
        def result = bat returnStatus: true, script: """
            set JMETER_HOME=
            set PATH=%JMETER_HOME%\\\\bin;%PATH%
            
            jmeter -g results.jtl -o dashboard
        """
        if (result != 0) {
            error "Hubo un error al generar el gráfico de rendimiento"
        }
    }
    // Archivar el gráfico generado
    archiveArtifacts artifacts: 'dashboard', allowEmptyArchive: true
}
    }
}
