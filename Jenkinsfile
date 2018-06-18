podTemplate(label: 'node-selenium', containers: [
  containerTemplate(name: 'node-firefox', image: 'node:8', ttyEnabled: true, command: 'cat'),
  containerTemplate(name: 'node-chrome', image: 'node:8', ttyEnabled: true, command: 'cat'),
  containerTemplate(name: 'selenium-hub', image: 'selenium/hub:3.4.0'),
  // because containers run in the same network space, we need to make sure there are no port conflicts
  // we also need to adapt the selenium images because they were designed to work with the --link option
  containerTemplate(name: 'selenium-chrome', image: 'selenium/node-chrome:3.4.0', envVars: [
    containerEnvVar(key: 'HUB_PORT_4444_TCP_ADDR', value: 'localhost'),
    containerEnvVar(key: 'HUB_PORT_4444_TCP_PORT', value: '4444'),
    containerEnvVar(key: 'DISPLAY', value: ':99.0'),
    containerEnvVar(key: 'SE_OPTS', value: '-port 5556'),
  ]),
  containerTemplate(name: 'selenium-firefox', image: 'selenium/node-firefox:3.4.0', envVars: [
    containerEnvVar(key: 'HUB_PORT_4444_TCP_ADDR', value: 'localhost'),
    containerEnvVar(key: 'HUB_PORT_4444_TCP_PORT', value: '4444'),
    containerEnvVar(key: 'DISPLAY', value: ':98.0'),
    containerEnvVar(key: 'SE_OPTS', value: '-port 5557'),
  ])
  ]) {

  node('node-selenium') {
    stage('Checkout') {
      git 'https://github.com/carlossg/selenium-example.git'
      parallel (
        firefox: {
          container('node-firefox') {
            stage('Test firefox') {
              sh 'npm -v'  
              sh 'npm selenium-standalong --v'
              sh 'curl http://localhost:4444/status'
              sh 'npm run test:unit'
            }
          }
        },
        chrome: {
          container('node-chrome') {
            stage('Test chrome') {
              sh 'pwd'  
              sh 'npm selenium-standalong --v'             
              sh 'curl http://localhost:4444/status'
              sh 'npm run test:unit'
            }
          }
        }
      )
    }

    stage('Logs') {
      containerLog('selenium-chrome')
      containerLog('selenium-firefox')
    }
  }
}
