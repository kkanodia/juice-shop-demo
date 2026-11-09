pipeline {
    agent any

    environment {
        NPM_CONFIG_REGISTRY = 'http://localhost:8081/repository/npm-group/'
    }

    stages {
        stage('Install dependencies') {
            steps {
                nodejs(nodeJSInstallationName: 'NodeJS-26.13.0') {
                    bat 'node --version'
                    bat 'npm --version'
                    bat 'npm ci'
                }
            }
        }

        stage('Build application') {
            steps {
                nodejs(nodeJSInstallationName: 'NodeJS-26.13.0') {
                    bat 'npm run build'
                }
            }
        }

        stage('Lifecycle Build scan with JavaScript reachability') {
            steps {
                nodejs(nodeJSInstallationName: 'NodeJS-26.13.0') {
                    nexusPolicyEvaluation(
                        iqApplication: 'juice-shop-demo__kkanodia',
                        iqInstanceId: 'iq-local',
                        iqStage: 'build',
                        iqScanPatterns: [
                            [scanPattern: '**/package-lock.json'],
                            [scanPattern: '!**/node_modules/**']
                        ],
                        reachability: [
                            jsAnalysis: [
                                enable: true,
                                sourceFiles: [
                                    [pattern: 'app.ts'],
                                    [pattern: 'server.ts'],
                                    [pattern: 'routes/**/*'],
                                    [pattern: 'lib/**/*'],
                                    [pattern: 'models/**/*'],
                                    [pattern: 'frontend/src/**/*']
                                ],
                                excludeFiles: [
                                    [pattern: '**/node_modules/**'],
                                    [pattern: '**/test/**'],
                                    [pattern: '**/tests/**']
                                ]
                            ]
                        ]
                    )
                }
            }
        }
    }
}