The calculator app is a simple node.js app, so the defaults are good _but_
we want to create a test XML and publish the results.  Therefore, during
the pipeline creation, set the YAML to:

```
# Node.js
# Build a general Node.js application with npm.
# Add steps that analyze code, save build artifacts, deploy, and more:
# https://docs.microsoft.com/vsts/pipelines/languages/javascript

pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'
  displayName: 'Install Node.js'

- script: npm install
  displayName: 'npm install'

- script: npm run build
  displayName: 'npm run build'

- script: npm test
  displayName: 'npm test'

- task: PublishTestResults@2
  displayName: 'Publish Test Results'
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: 'out/test-results.xml'
```

The existing `npm test` step will produce both console output and the
JUnit-style test output.
