HOL - Parts Unlimited WebSite Continuous Integration with Visual Studio Online Build
====================================================================================
In this lab we have an application called PartsUnlimited. We want to set up
Visual Studio Online to be able continuously integrate code into the master
branch of code. This means that whenever code is committed and pushed to the
master branch, we want to ensure that it integrates into our code correctly to
get fast feedback. To do so, we are going to be setting up a Continuous Integration build (CI) that
will allow us to compile and run unit tests on our code every time a commit is
pushed to Visual Studio Online.

###Pre-requisites:###

-   An active Visual Studio Online account

-   An active Azure subscription

-   Project Admin rights to the Visual Studio Online account

### Tasks Overview: ###

**Set up your Visual Studio Online account:** This step helps you download the source code, and then push it to your own Visual Studio Online account. If you need to learn how to setup a VSO account, click [https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online)

**Add PartsUnlimited source to the Main Repo:** In this step, you will create a new Windows machine, install all the dependencies required to be a build machine, and then configure a build agent on it.
<!-- We should not have them build a new build agent.  Use hosted build controller -->

**Create Continuous Integration Build:** In this step, you will create a build definition in Visual Studio Online that will be triggered every time a commit is pushed to your repository in Visual Studio Online. 

### Pre-Requisite: Connect to your Visual Studio Online account

We want to push the application code to your Visual Studio Online account in
order to use Build.

**1.** First, we need to open Team Explorer. Go to your **account home
page**:

	https://<account>.visualstudio.com
<!-- Let's avoid people from having to use Visual Studio at all.  They should be able to do CI with simply git and their web browser.-->

**2.** Connect to the VSO account project using Visual Studio.

![](<media/25.jpg>)

### 1: Using own VSO to host Parts Unlimited Project

**1.** Navigate to [https://github.com/Microsoft/PartsUnlimited/tree/aspnet45](https://github.com/Microsoft/PartsUnlimited/tree/aspnet45) and download the sample as a zip

> **Note:** For this lab is vitally IMPORTANT THAT YOU GET THE 4.5 BRANCH!

**2.** Create folder and save the download to this folder.

Create **Working Directory** to the following location:

`C:\Source\Repos\HOL`

**3.** Unzip the PartsUnlimited project, when unzipping be sure and “Unblock” the content or the deployment scripts won’t run

![](<media/21.jpg>)

**4.** Clone the repo of your team project to the location where you extracted the sample

Set the **Working Directory** to the following location:

`C:\Source\Repos\HOL`

![](<media/26.jpg>)

**5.** Click Open and navigate to the Parts Unlimited Project Solution in Solution Explorer

![](<media/27.jpg>)

**6.** Run the project and verify the site can be built.

![](<media/28.jpg>)

> **Note:** Need to build the project so all of the required dependencies get pulled down before being able to deploy. This may take a little time as it needs to pull from Nuget.

**7.** Now we know the site can be built and run, let add the source to the Git repo. Right click on the solution and click **Add to Source Control**.

![](<media/29.jpg>)

**8.** The Changes windows will appear, add in checkin text and verify the source is ready to be committed. Click on **Commit and Push**.

![](<media/30.jpg>)

**9.** Once the changes have been committed, click on the **Code** hub at the top of
the page. Verify the source is in the repo.

![](<media/31.jpg>)

**10.** Now it is time to create a local repo to work from, in the Team Explorer, click **Branches** -> Right click on **Master** -> **New Local Branch from**... 

![](<media/32.jpg>)

**10.** Add in the repo name (i.e. *HOLRepo*) and click **Create Branch**

![](<media/33.jpg>)

> **Note:** Publishing back to VSO when cloning a repo allow the build definition to see the new repo for building out the projects.

**11.** Now we need to make sure the branch is discoverable from build, click on **Publish Branch**.

![](<media/34.jpg>)

### 2. Create Continuous Integration Build

A continuous integration build will give us the ability check whether the code
we checked in can compile and will successfully pass any automated tests that we
have created against it.

**1.** Go to your **account’s homepage**: https://<account\>.visualstudio.com

**2.** Click **Browse** and then select your team project and click
**Navigate**.

![](<media/22.jpg>)

**3.** Once on the project’s home page, click on the **Build** hub at the top of
the page.

![](<media/23.jpg>)

**4.** Click the **green “plus” sign**, select **Visual Studio Build**, and then click **OK**.

![](<media/24.jpg>)

> **Note:** As you can see, you can now do Xamarin Android/IOS and Builds as well as Xcode builds.

**5.** Click on the **Repository** tab, and choose the git repository that
PartsUnlimited source is in.

![](<media/35.jpg>)

**6.** We are going to use the **HOLRepo** branch on the Repository tab

> **Note:** We have multiple repos and branches, so we need to select the correct Repo and Branch before we can select which Solution to build.

**7.** Click on the **Build** tab, and click the **ellipsis** in the Build Solution pane. Select the PartsUnlimited solution file.

![](<media/36.jpg>)

**8.** Now Enter the following information to **MSBuild** parameters:
    
    /p:DeployOnBuild=true 
    
    /p:WebPublishMethod=Package 
    
    /p:PackageAsSingleFile=true 
    
    /p:SkipInvalidConfigurations=true 
    
    /p:PackageLocation="C:\Agent\HOL"

![](<media/48.jpg>)

**9.** Select the **Visual Studio Test** Task and click **Continue on Error** checkbox.

![](<media/44.jpg>)

> **Note:** If any of your tests fail the build will fail. If you do not want the build to fail, click” Continue On Error” and the build will partially succeed.

**10.** Click on the **Trigger** tab, , click **Continuous Integration**. Make sure the filter to include **HOLRepo** and **Batch Changes** checkbox is unchecked

![](<media/38.jpg>)

> **Note:** So that the Build fires off every time there’s a check in, enable the Continuous integration trigger. You can select which branch you wish to monitor, as well.

**11.** Select the **Publish Build Artifacts** task, and fill in the input values
with the following:

	Copy Root: C:/Agent/HOL
	Contents: *.zip
	Artifact Name: drop
	Artifact Type: Server

![](<media/39.jpg>)

**12.** Click **Save** and give the build definition a name (i.e.
*“HOL Build”*).

![](<media/41.jpg>)

**13.** Queue the Build and view the progress in the console.

![](<media/42.jpg>)

![](<media/43.jpg>)

### 3. Test the CI Trigger in Visual Studio Online

We will now test the CI build we created in Exercise 2 by changing code in the Parts Unlimited project in Visual Studio Online. 

**1.** Click **Code** hub and then select your your repo, **HOLRepo**. Navigate to **Controllers/HomeController.cs** in the PartsUnlimited project, then click **Edit**.

![](<media/45.jpg>)

**2.** After clicking **Edit**, add in text to the top of the constuctor of the **HomeController.cs** file. Once complete, click **Save**.

![](<media/46.jpg>)

**3.** Click **Build** hub, then click the **Queue** link. This should have triggered the build definition we previously created, and you should get a build summary similar to this, which includes test results:

![](<media/47.jpg>)

<!-- Please put in a step + screenshot of the test executing -->

Next steps
----------

In this lab, you learned how to push new code to Visual Studio Online, setup a Git repo and create a Continuous
Integration build that runs when new commits are pushed to the master branch.
This allows you to get feedback as to whether your changes made breaking syntax
changes, or if they broke one or more automated tests, or if your changes are a
okay. 

Try these labs out for next steps:

-   HOL Parts Unlimited Continuous Delivery