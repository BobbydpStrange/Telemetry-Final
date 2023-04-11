# Automated Releases:
We implemented a GitHub Actions pipeline that only runs on tagged commits and releases a build for Android APK and Windows.

*Note: If you need some help with writing the workflow file this blog could help.*
[Blog.Taranissoftware.com/building-net-maui-apps-with-github-actions](https://blog.taranissoftware.com/building-net-maui-apps-with-github-actions)

## __Steps__
1. __Add your workflow file__
     - In file view on Visual Sudio if you go to file view in solution explorer you will see a .github folder. Inside this folder are the workflows that you'll make or have. From here you can add the yml file and add in the text.
     (image1)

2. __Name the workflow and have it run on tags__
     - Name speifies the name of that section that will show up on GitHub. These can be named anything as long as you know and understand what it is and does.
     - On push will always start the file and specify that what is to come next will happen whenever the project is pushed. For instance in the following image we are specifying when ever pushed on the master branch or a version tag is made with the same format.
3. __Add the jobs you want to do__
     - text
     (image2)
     - If another job needs another one it will need the `needs:` header along with the name of that job. For instance this image is referencing *line 15* in the previous image.
     (image3)

4. __Check file paths and net versions__
     - a problem that can occur if your not careful is if your dotnet versions arn't the same as the one for the project.

5. __Push origin/Pull__
6. __Check GitHub__
     - To check if your workflow is running go to the projects repository in GitHub.

     <!-- ![catch and handle request](images/image2.png) -->
     push origin with tag.
     pull

