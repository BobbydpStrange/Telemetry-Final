- Make sure paths match with current project.
- Change dotnet 7 and mainservice.csproj target framework matches run line.
```
name: CI Build

on:
  push:
    Tags:
        -"V*.*.*"
    Jobs:
        build-android:
            runs-on: windows-2022
            name: Android Build
            steps:
            - name: Checkout
                uses: actions/checkout@v2

            - name: Setup .NET 7
                uses: actions/setup-dotnet@v1
                with:
                dotnet-version: 6.0.x
                include-prerelease: true

            - uses: actions/setup-java@v2
                with:
                distribution: 'microsoft'
                java-version: '11'

            - name: Install MAUI Workloads
                run: |
                dotnet workload install android --ignore-failed-sources
                dotnet workload install maui --ignore-failed-sources

            - name: Restore Dependencies
                run: dotnet restore src/MauiBeach/MauiBeach.csproj

            - name: Build MAUI Android
                run: dotnet build src/MauiBeach/MauiBeach.csproj -c Release -f net6.0-android --no-restore

            - name: Upload Android Artifact
                uses: actions/upload-artifact@v2.3.1
                with:
                name: android-ci-build
                path: src/MauiBeach/bin/Release/net6.0-android/*Signed.a*
        build-windows:
            runs-on: windows-2022
            name: Windows Build
            steps:
            - name: Checkout
                uses: actions/checkout@v2

            - name: Setup .NET 7
                uses: actions/setup-dotnet@v1
                with:
                dotnet-version: 6.0.x
                include-prerelease: true

            - name: Setup MSBuild
                uses: microsoft/setup-msbuild@v1.1
                with:
                vs-prerelease: true

            - name: Install MAUI Workloads
                run: |
                dotnet workload install maui --ignore-failed-sources

            - name: Restore Dependencies
                run: dotnet restore src/MauiBeach/MauiBeach.csproj

            - name: Build MAUI Windows
                run: msbuild src/MauiBeach/MauiBeach.csproj -r -p:Configuration=Release -p:RestorePackages=false -p:TargetFramework=net7.0-windows10.0.19041 /p:GenerateAppxPackageOnBuild=true

            - name: Upload Windows Artifact
                uses: actions/upload-artifact@v2.3.1
                with:
                name: windows-ci-build
                path: src/MauiBeach/bin/Release/net7.0-windows*/**/MauiBeach*.msix
```