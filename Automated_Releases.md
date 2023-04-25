# Automated Releases:
We implemented a GitHub Actions pipeline that only runs on tagged commits and releases a build for Android APK and Windows.

*Note: If you need some help with writing the workflow file this blog could help.*
[Blog.Taranissoftware.com/building-net-maui-apps-with-github-actions](https://blog.taranissoftware.com/building-net-maui-apps-with-github-actions)

## __Steps__
1. __Add your workflow file__
     - In file view on Visual Studio if you go to file view in solution explorer you will see a .github folder. Inside this folder are the workflows that you'll make or have. From here you can add the yml file and add in the text.
     
          - ![catch and handle request](images/Automated_1.png)

2. __Name the workflow and have it run on tags__
     - Name specifies the name of that section that will show up on GitHub. These can be named anything as long as you know and understand what it is and does.
     - On push will always start the file and specify that what is to come next will happen whenever the project is pushed. For instance, in the following image, we are specifying whenever pushed on the master branch or a version tag is made with the same format this file will run.

          - ![catch and handle request](images/Automated_2.png)
     - You will also want to make sure to add the environment.

          - ![catch and handle request](images/Automated_3.png)
3. __Add the jobs you want to do__
     - Jobs are the tasks the file will do. For instance, the first job in this image is built and runs on Ubuntu-latest. This job has specific steps that we have given it to do. When the file runs the system will go through each item in a job and if there is any exception or error that happens on the steps the job will fail. Just because a job fails however doesn't mean that the whole workflow has failed unless everything depends on that one job.

          - ![catch and handle request](images/Automated_4.png)
     - If another job needs another one it will need the `needs:` header along with the name of that job. For instance, this image is referencing *line 15* in the previous image.

          - ![catch and handle request](images/Automated_5.png)
     - # __*How to make the workflow file for separate files and update pictures to match current workflow file*__

     3.1 __Android Job__
   
     - In this job we have six steps.
          - Checkout
               -    ```cs
                         - name: Checkout
                           uses: actions/checkout@v2 
                    ``` 
               - This specifies that the checkout version of the Github actions uses the second version. 
               
          - Setup .Net 7
               -    ```cs
                         - name: Setup .NET 7
                         uses: actions/setup-dotnet@v1
                         with:
                         dotnet-version: 7.0.x
                         include-prerelease: true

                         - uses: actions/setup-java@v2
                         with:
                         distribution: 'microsoft'
                         java-version: '11'
                    ```
               -    This specifies that project needs .NET and Java to run and specifies their versions.

          - Install Maui Workloads
               -    ```cs
                         - name: Install MAUI Workloads
                         run: |
                         dotnet workload install android --ignore-failed-sources
                         dotnet workload install maui --ignore-failed-sources
                    ```
               - This step installs the android and maui workloads to be able to build the MAUI app specifically on android.

          - Restore Dependencies
               -    ```cs
                         - name: Restore Dependencies
                         run: dotnet restore PianoLessons/PianoLessons.csproj
                    ```
               - This step will restore any dependencies the PianoLessons.csproj has.

          - Build MAUI Android
               -    ```cs
                         - name: Build MAUI Android
                         run: dotnet build PianoLessons/PianoLessons.csproj -c Release -f net7.0-android --no-restore
                    ```
               - This step will build the PianoLessons.csproj for android and will be built on release mode and no dependencies will need to be restored.

          - Upload Android Artifact
               -    ```cs
                         - name: Upload Android Artifact
                         uses: actions/upload-artifact@v2.3.1
                         with:
                         name: android-ci-build
                         path: PianoLessons/bin/Release/net7.0-android/*Signed.a*
                    ```
               - This step uses the specific upload artifact to complete and the path that the files that should be uploaded. The *Signed.a* will include any file that matches that pattern.

     
     3.2 __Windows Job__
     
     - In this job we have eight steps.
          - Checkout & Setup .Net 7
               - The Checkout and Setup will be the same as android.

          - Setup MSBuild
               -    ```cs
                         - name: Setup MSBuild
                           uses: microsoft/setup-msbuild@v1.1
                           with:
                             vs-prerelease: true
                    ```
               - This step specifies that the microsfot setup should be used along with its specific version and allows the prerealeses of visual studio to be used too.
          - Install MAUI Workloads
               -    ```cs
                         - name: Install MAUI Workloads
                           run: |
                             dotnet workload install maui --ignore-failed-sources
                    ```
               - This is just like the android workloads though it only installs the MAUI not the android.
          - Restore Dependencies
               -    This is also the same as the android dependencies.
          - Build MAUI Windows
               -    ```cs
                           - name: Build MAUI Windows
                             run: msbuild PianoLessons/PianoLessons.csproj -r -p:Configuration=Release -p:RestorePackages=false -p:TargetFramework=net7.0-windows10.0.19041.0 /p:GenerateAppxPackageOnBuild=true
                    ```
               - This uses the msbuild we've setup for the PianoLessons.csproj using the net7.0 framework.
          - Output folder
               -    ``` cs
                         - name: Output folder
                           run: |
                             dir PianoLessons
                             dir PianoLessons/bin
                             dir PianoLessons/bin/Release
                             dir PianoLessons/bin/Release/net7.0-windows10.0.19041.0
                             dir PianoLessons/bin/Release/net7.0-windows10.0.19041.0/win10-x64
                    ```
               - This step specifies the files that will have a build output for  the project and files for the windows platform.
          - Upload Windows Artifact
               -    ```cs
                         - name: Upload Windows Artifact
                         uses: actions/upload-artifact@v2.3.1
                         with:
                              name: windows-ci-build
                              path: PianoLessons/bin/Release/net7.0-windows/**/PianoLessons*.msix
                    ```
               - Like the android this will upload this step uses the specific upload artifact to complete and the path that the files that should be uploaded. Though specifically for the windows.


4. __Check file paths and net versions__
     - a problem that can occur if you're not careful is if your dotnet versions aren't the same as the one for the project.

5. __Push origin/Pull__
     - To push your file so your workflow will run you can simply push it to GitHub and this can be in the terminal. If you are using tabs I would suggest using the following terminal commands.
          -    ```c#
               //This will simply push to a specific branch
               git add .
               git commit -m "message"
               git push origin master // you can replace master with the branch you want to push to.

               //This shows how you would push with specific tags
               git tag V1.0
               git push --tags
               ```

6. __Check GitHub__
     - To check if your workflow is running go to the project's repository in GitHub.
     - Then click on the actions tab

          - ![catch and handle request](images/Automated_6.png)
     - On the left side of the window you should see an Actions section. This will show all individual workflows you have or all of them and can simply click on the one you want to see.
     
          - ![catch and handle request](images/Automated_7.png)
     - From there you will see all the commits that had been pushed to GitHub and that ran the workflow file. When you click on a given commit you can see how the different jobs went when pushing the project.
     - If you scroll down to the bottom of the page you should see an artifacts section that has those versions of the apps/jobs.
     
          - ![catch and handle request](images/Automated_8.png)
     
7. __Possible problems__
     - Make the app not dependent on the shared project and instead make a shared folder with a data folder inside and link existing files to the shared files.
     - If you get a .net 7 error for the project check in the maui.csproj and make sure you have .net7.0 for the target framework and 
     -    ```
          <OutputType Condition="'$(TargetFramework)' != 'net7.0'">Exe</OutputType>
          ```
     - .net workloads install maui, windows.

8. __Run file__
     - open the emulator, go to the GitHub page, download apk, and trust resources to run that version. This will install that version of the app onto your device.

