# azure-rust

Repo to test azure devops releases with Rust.

Checkout the Github Releases tab.

Now it's totally managed by the Azure Devops visual designer, which generates this `.yaml`: 

```yml
resources:
- repo: self
phases:
- phase: Phase_1
  displayName: Windows

  condition: and(succeeded(), startsWith(variables['Build.SourceBranch'], 'refs/tags/v'))
  queue:
    name: Hosted VS2017
    demands: npm

  steps:
  - task: spontoreau.rust-vsts.rust-install.Rust@1
    displayName: 'Rust Tool Installer'

  - task: spontoreau.rust-vsts.rust-cargo.Cargo@1
    displayName: Cargo
    inputs:
      cargoCommand: build
      cargoCommandOptions: '--release'

  - bash: 'ls target/release'
    displayName: 'Bash Script'
    continueOnError: true

  - task: ArchiveFiles@2
    displayName: 'Archive target/release/azure-rust.exe'
    inputs:
      rootFolderOrFile: 'target/release/azure-rust.exe'
      archiveFile: 'azure-rust-windows.zip'

  - task: marcelo-formentao.github-tools.github-release-publish-task.GitHubReleasePublish@1
    displayName: 'Release Windows'
    inputs:
      githubEndpoint: rob
      githubRepository: 'robertohuertasm/azure-rust'
      githubTag: '$(Build.BuildNumber)'
      githubReleaseTitle: '$(Build.BuildNumber)'
      githubReleaseDraft: false
      githubReleaseAsset: 'azure-rust-windows.zip'
      githubReuseDraftOnly: false


- phase: Phase_2
  displayName: Linux

  condition: and(succeeded(), startsWith(variables['Build.SourceBranch'], 'refs/tags/v'))
  queue:
    name: Hosted Ubuntu 1604
    demands: npm

  steps:
  - task: spontoreau.rust-vsts.rust-install.Rust@1
    displayName: 'Rust Tool Installer'

  - task: spontoreau.rust-vsts.rust-cargo.Cargo@1
    displayName: Cargo
    inputs:
      cargoCommand: build
      cargoCommandOptions: '--release'

  - bash: 'ls target/release'
    displayName: 'Bash Script'

  - task: ArchiveFiles@2
    displayName: 'Archive target/release/azure-rust'
    inputs:
      rootFolderOrFile: 'target/release/azure-rust'
      archiveFile: 'azure-rust-linux.zip'

  - task: marcelo-formentao.github-tools.github-release-publish-task.GitHubReleasePublish@1
    displayName: 'Release Linux'
    inputs:
      githubEndpoint: rob
      githubRepository: 'robertohuertasm/azure-rust'
      githubTag: '$(Build.BuildNumber)'
      githubReleaseTitle: '$(Build.BuildNumber)'
      githubReleaseDraft: false
      githubReleaseAsset: 'azure-rust-linux.zip'
      githubReuseDraftOnly: false

- phase: Phase_3
  displayName: MacOS

  condition: and(succeeded(), startsWith(variables['Build.SourceBranch'], 'refs/tags/v'))
  queue:
    name: Hosted macOS
    demands: npm

  steps:
  - task: spontoreau.rust-vsts.rust-install.Rust@1
    displayName: 'Rust Tool Installer'


  - task: spontoreau.rust-vsts.rust-cargo.Cargo@1
    displayName: Cargo
    inputs:
      cargoCommand: build
      cargoCommandOptions: '--release'

  - task: ArchiveFiles@2
    displayName: 'Archive target/release/azure-rust'
    inputs:
      rootFolderOrFile: 'target/release/azure-rust'
      archiveFile: 'azure-rust-osx.zip'

  - task: marcelo-formentao.github-tools.github-release-publish-task.GitHubReleasePublish@1
    displayName: 'Release MacOS'
    inputs:
      githubEndpoint: rob
      githubRepository: 'robertohuertasm/azure-rust'
      githubTag: '$(Build.BuildNumber)'
      githubReleaseTitle: '$(Build.BuildNumber)'
      githubReleaseDraft: false
      githubReleaseAsset: 'azure-rust-osx.zip'
      githubReuseDraftOnly: false

```
