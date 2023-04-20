# Issue
After you deploy the solution and open Synapse Studio, you notice that there are no pipelines or data. You might also see a notification that "Publishing in workspace mode is disabled".

![image](https://user-images.githubusercontent.com/59613090/233481119-d184c4d3-27a2-4bb4-9b2a-90e3e6a5be4e.png)


# Resolution
Not all artifacts can be deployed if you don't have Synapse registered in your GitHub account.  You need to disconnect and reconnect GitHub from your Synapse workspace to force registration.  Once that is complete you will need to redploy the solution.

First you need to switch from Synapse Live mode to GitHub.
![image](https://user-images.githubusercontent.com/59613090/233480998-b4cb25aa-9973-43ca-8884-2e41887dd49f.png)

Next you need to head to the **Manage Hub** and select **GitHub Configuration** in the navigation pane.
![image](https://user-images.githubusercontent.com/59613090/233492816-ce4d13ca-11c1-4bd1-8612-21fa885cdcda.png)

Now you need to disconnect your workspace from your GitHub account.
![image](https://user-images.githubusercontent.com/59613090/233493184-878cfe0e-9fec-45ec-b1d5-c736212478af.png)

Once you have successfully disconnected GitHub from your workspace it should look like this.
![image](https://user-images.githubusercontent.com/59613090/233494498-5ad84f71-d4f7-4855-a2cf-7e669564e3bc.png)

Now that you have disconnected, it's time to reconnect so the Synapse libraries can be properly installed in your GitHub account.  Hit the configure button and select GitHub as your repository type.
![image](https://user-images.githubusercontent.com/59613090/233498108-e6e0af27-9ebd-4377-a667-750f9c5b5d49.png)

Enter your GitHub repository Owner Name and select **Continue**
![image](https://user-images.githubusercontent.com/59613090/233498324-a489eec1-a2d1-47e6-a0cc-ef3aedfe756e.png)

Now you will get a pop-up asking to authorize Azure Synapse to your GitHub account. Select **Authirize Azure Synapse**
![image](https://user-images.githubusercontent.com/59613090/233499635-cbde78cb-6505-4908-baa5-1cc4b1925c4b.png)

---
