# azure-publish-profile-github-action

このアクションは Azure の WebApp や Azure Functions で発行に使用する発行プロファイルを取得するアクションです。

## Requirement

You will need to install [azure cli](https://docs.microsoft.com/en-us/cli/azure/)

### Installation

[How to install the Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)

```sh
brew update && brew install azure-cli
```

## Usage

### Simple usage

#### iOS builds

```yml
- uses: akiojin/azure-publish-profile-github-action@v3
  id: publish-profile
  with:
    app-id: ${{ secrets.SERVICE_PRINCIPAL_APP_ID }}
    password: ${{ secrets.SERVICE_PRINCIPAL_PASSWORD }}
    tenant: ${{ secrets.SERVICE_PRINCIPAL_TENANT_ID }}
    subscription: "Azure subscription 1"
    app-name: <AppName>
    resource-group: <Resource Group>
```

#### Team ID & Provisioning Profile UUID

```yml
- uses: akiojin/setup-xcode-environment-github-action@v2
  id: setup-xcode-environment
  with:
    type: ${{ env.DISTRIBUTION }}
    app-identifier: <App ID>
    team-id: <Team ID>
    git-url: ${{ secrets.APPLE_CERTIFICATE_GIT_URL }}
    git-passphrase: ${{ secrets.APPLE_CERTIFICATE_GIT_PASSPHRASE }}
    git-branch: "develop"
    keychain: ${{ steps.setup-temporary-keychain.outputs.keychain }}
    keychain-password: ${{ steps.setup-temporary-keychain.outputs.keychain-password }}

- uses: akiojin/unity-build-github-action@v3
  with:
    build-target: 'iOS'
    project-directory: ${{ github.workspace }}
    output-directory: ${{ runner.temp }}/Unity
    export-method: ${{ env.DISTRIBUTION }}
    team-id: <Team ID>
    provisioning-profile-uuid: ${{ steps.setup-xcode-environment.outputs.provisioning-profile-uuid }}
```

#### Android builds

```yml
- uses: akiojin/unity-build-github-action@v3
  with:
    build-target: 'Android'
    project-directory: ${{ github.workspace }}
    output-directory: ${{ runner.temp }}/Unity
```

#### Keystore

```yml
- uses: akiojin/unity-build-github-action@v3
  with:
    build-target: 'Android'
    project-directory: ${{ github.workspace }}
    output-directory: ${{ runner.temp }}/Unity
    keystore-base64: ${{ secrets.GOOGLE_KEYSTORE_BASE64 }}
    keystore-password: ${{ secrets.GOOGLE_KEYSTORE_PASSWORD }}
    keystore-alias: 'development'
    keystore-alias-password: ${{ secrets.GOOGLE_KEYSTORE_ALIAS_DEVELOPMENT_PASSWORD }}
```

## Arguments

### Common

|Name|Required|Type|Default|Description|
|:--|:--|:--|:--|:--|
|additional-arguments|`false`|`string`|""|Specify additional required arguments.|
|build-target|`true`|`string`||Allows the selection of an active build target before loading a project.<br><br>Possible options are:<br>Standalone, Win, Win64, OSXUniversal, Linux, Linux64, LinuxUniversal, iOS, Android, Web, WebStreamed, WebGL, XboxOne, PS4, WindowsStoreApps, Switch, N3DS, tvOS.|
|configuration|`false`|`string`|Debug|The configuration to use when building the app.<br><br>Possible options are:<br>Debug, Release.|
|execute-method|`false`|`string`|""|Execute the static method as soon as Unity opens the project, and after the optional Asset server update is complete.|
|log-file|`false`|`string`|`"-"`|Specify where Unity writes the Editor or Windows/Linux/OSX standalone log file.<br>To output to the console, specify - for the path name.<br>On Windows, specify - option to make the output go to stdout, which is not the console by default.|
|output-directory|`false`|`string`|$RUNNER_TEMP|The directory in which the ipa/apk file should be stored in.|
|output-name|`false`|`string`|"Build"|Specifies the output file name.|
|project-directory|`true`|`string`||Open the project at the given path. If the pathname contains spaces, enclose it in quotes.|
|unity-version|`false`|`string`|""|Specify the Unity version to be used.<br>If omitted, the project version is used.|

### iOS

|Name|Required|Type|Default|Description|
|:--|:--|:--|:--|:--|
|export-method|`false`|`string`|development|Define the profile type, can be appstore, adhoc, development, enterprise, developer_id, mac_installer_distribution.|
|include-bitcode|`false`|`boolean`|`false`|Should the ipa file include bitcode?|
|include-symbols|`false`|`boolean`|`false`|Should the ipa file include symbols?|
|scheme|`false`|`string`|Unity-iPhone|The project's scheme.|
|sdk|`false`|`string`|iphoneos|The SDK that should be used for building the application.|
|team-id|`false`|`string`|""|The ID of your Developer Portal team if you're in multiple teams.|

### Android

|Name|Required|Type|Default|Description|
|:--|:--|:--|:--|:--|
|keystore|`false`|`string`|""|Specify the path to the keystore file.<br>For Android, if `keystore` or `keystore-base64`, `keystore-password`, `keystore-alias`,`keystore-alias-password` is not specified, will be signed with a debug key.|
|keystore-base64|`false`|`string`|""|Specifies Base64 data for the keystore.<br>If you do not specify a file path in `keystore`, you must specify this parameter.<br>Also, if this value is specified, the value of `keystore` is ignored.|
|keystore-password|`false`|`string`|""|Specify the password for the keystore.|
|keystore-alias|`false`|`string`|""|Specifies the name of the keystore alias.|
|keystore-alias-password|`false`|`string`|""|Specify the password for the keystore alias.|

## License

Any contributions made under this project will be governed by the [MIT License][3].

[0]: https://github.com/akiojin/unity-build-github-action/actions/workflows/Test.yml/badge.svg
[1]: https://docs.fastlane.tools/
[2]: https://github.com/akiojin/unity-build-github-action/blob/main/action.yml
[3]: https://github.com/akiojin/unity-build-github-action/blob/main/LICENSE
