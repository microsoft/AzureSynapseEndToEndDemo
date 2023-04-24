<a name="issue-list" />

## Jump To Issue
1. [Missing Pipelines or Data](#missing-pipes)

<a name="missing-pipes" />

----

# Missing Pipelines or Data
After you deploy the solution and open Synapse Studio, you notice that there are no pipelines or data. You might also see a notification that "Publishing in workspace mode is disabled".

![image](https://user-images.githubusercontent.com/59613090/233481119-d184c4d3-27a2-4bb4-9b2a-90e3e6a5be4e.png)


# Resolution - Missing Pipelines or Data
Not all artifacts can be deployed if you don't have Synapse registered in your GitHub account.  You need to disconnect and reconnect GitHub from your Synapse workspace to force registration.  Once that is complete you will need to redploy the solution.

First you need to switch from Synapse Live mode to GitHub.
![image](https://user-images.githubusercontent.com/59613090/233480998-b4cb25aa-9973-43ca-8884-2e41887dd49f.png)

Next you need to head to the **_Manage Hub_** and select **_GitHub Configuration_** in the navigation pane.
![image](https://user-images.githubusercontent.com/59613090/233492816-ce4d13ca-11c1-4bd1-8612-21fa885cdcda.png)

Now you need to disconnect your workspace from your GitHub account.
![image](https://user-images.githubusercontent.com/59613090/233493184-878cfe0e-9fec-45ec-b1d5-c736212478af.png)

Once you have successfully disconnected GitHub from your workspace it should look like this.
![image](https://user-images.githubusercontent.com/59613090/233494498-5ad84f71-d4f7-4855-a2cf-7e669564e3bc.png)

Now that you have disconnected, it's time to reconnect so the Synapse libraries can be properly installed in your GitHub account.  Hit the configure button and select GitHub as your repository type.
![image](https://user-images.githubusercontent.com/59613090/233498108-e6e0af27-9ebd-4377-a667-750f9c5b5d49.png)

Enter your GitHub repository Owner Name and select **_Continue_**

![image](https://user-images.githubusercontent.com/59613090/233498324-a489eec1-a2d1-47e6-a0cc-ef3aedfe756e.png)

Now you will get a pop-up asking to authorize Azure Synapse to your GitHub account. Select **_Authorize Azure Synapse_**
![image](https://user-images.githubusercontent.com/59613090/233499635-cbde78cb-6505-4908-baa5-1cc4b1925c4b.png)

Next you need to grab the URL for your repository to finish the configuration.  You can get this by clicking the **_<>Code_** button toward the top of your repository and then selecting the **_Local_** tab.  Now you can just click the copy button next to your HTTPS URL.
![image](https://user-images.githubusercontent.com/59613090/234100272-c92007d6-3622-45ac-b518-4b03cd8414ee.png)

Now go back to your repository configuration and make sure you have the following things set and hit **_Apply_** when done:
- "**_Use Repository Link_**" should be selected
- Paste your URL in the **_Git Repository Link_** field
- Use "**_main_**" for the **_Collaboration Branch_** field
- Choose a Publish Branch like **_workspace_publish_**
- #### You MUST set the **_Root Folder_** field to **_/artifacts_** including the slash that precedes it
- #### The **_Import Existing Resources_** field MUST BE UNCHECKED
![image](https://user-images.githubusercontent.com/59613090/234103325-dcf52e98-6e85-4f14-b32e-b4e1c9618a10.png)

If you get an error about not having permissions to import the repository you need to **_UNCHECK_** the import setting.
![image](https://user-images.githubusercontent.com/59613090/234103995-00e54a20-66cf-4367-b787-5cc113a3dcf0.png)

[Back to Issue List](#issue-list)
---
