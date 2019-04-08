The calculator app is a simple node.js app, so the defaults are good _but_
we want to create a test XML and publish the results.  Therefore, during
the pipeline creation, set the YAML to:

```
# Node.js
# Build a general Node.js project with npm.
# Add steps that analyze code, save build artifacts, deploy, and more:
# https://docs.microsoft.com/azure/devops/pipelines/languages/javascript

trigger:
- master

pool:
  vmImage: 'Ubuntu-16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '10.x'
  displayName: 'Install Node.js'

- script: |
    npm install
    npm run build
    npm run test
  displayName: 'npm install and build'

- task: PublishTestResults@2
  displayName: 'Publish Test Results'
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: 'out/test-results.xml'

- task: PublishCodeCoverageResults@1
  displayName: 'Publish Code Coverage Results'
  inputs:
    codeCoverageTool: 'cobertura'
    summaryFileLocation: 'coverage/cobertura-coverage.xml'
    reportDirectory: 'coverage/report'
```

The existing `npm test` step will produce both console output, the
JUnit-style test output and code coverage reports.

To package this up for release:

```
- task: ArchiveFiles@1
  displayName: Archive Files 
  inputs:
    includeRootFolder: false

- task: PublishBuildArtifacts@1
  displayName: Publish Site
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip'
    ArtifactName: calculator
```
