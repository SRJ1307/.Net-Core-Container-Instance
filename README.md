
# Deploying a .Net Core Application to Azure Container Instance(ACI)
Hello Everyone, In this project we will see how can we deploy an application using Container Instance.

Pre-Requisite:  
                
                1. Docker Desktop (Install and Run it)
                2. Azure Account with free trial subscription or Pay as You Go Subscription.

To Deploy the application on Container Instance first we need to store it somewhere. For this, we will use ACR(Azure Container Registry).

So First thing First.
Let's create a project, for my case I will download it.

    1. Open the Visual Studio code, Select a blank folder where you would like to keep your project.
    2. Now press the "ctrl" + "`" key, it will open the command line. from the right side of the prompt Switch it to the command prompt from the drop-down menu, and by default PowerShell gets open.
    3. Run this command "dotnet new webapi -o projectname" (without quotes). This will create a basic default .net project.

Since our basic project is ready let's move to create Azure Container Registry. We will add the docker file later in this project.

    1. Open Azure Portal.
    2. From the top search menu, search for Container Registry.
    3. Click on "+" icon to start creating a new one.
            The first section is basics.
    4. In Resource Group click on Create New and name it "Container".
    5. Now Enter the registry name, for me, I am writing "srjcontainerinstance", you can give any relevant name.
    6. Chosse the location as EastUS. You can choose any location based on your requirements and subscription plan.
    7. Keep the pricing plan as Standard.
    8. Move to the Networking section.
    9. Here you can choose the scope of the registry, whether you want it to be publicly available or make it private. Since I am using a Free Trail subscription, this option is disabled for me. So make it public and move to the next section as Encryption.
    10. Here you can choose the Encryption method, whether you want it to encrypt using your own key or let it be managed by Azure itself. I am choosing the second option as the first one is disabled.
    11. Now move to the last section "Review + Create". Here microsoft will perform validation for your selected choices and inputs based on your subscription and feasibility.
    12. once the Validation passed, click on "Create" on the left bottom side indicated in blue color.
 
 <img width="740" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/cfb7e82a-646a-489d-a8bc-c07cea05c3ec">

 While the registry is being created and deployed. Let's move back to VS Code.

    1. Now we need to add the Docker file to our project.
    2. Click the "ctrl" +" Shift" +"P" key together. It will open a dropdown box from the top.
    3. There choose " Docker: ADD Docker File to workSpace".
    4. Once you choose the option next is to provide the type of application you are deploying, choose .NetCore.
    5. Choose Linux in the next step.
    6. Now here is a very important step, you need to choose the port number on which you want to deploy the application. This port no is of the Docker image which you will be creating. For our ease let's choose 80 as port no.
    7. Choose "NO" as an option when it is asking for compose.
    8. Now your Docker file is created.
 By This time Registry must have been deployed.

    1. Go to your created registry in the Azure portal.
    2. In the left Pane check for the "Access Key" option. Inside this make sure the "Admin User" checkbox is ticked, by default it is not ticked.
    3. Now go to Access Control(IAM) in the same left pane of your registry.
    4. Inside IAM move to Role Assignments and click on the "+" icon to create a role assignment.
    5. When you will click the + icon, a new window will open, inside that under the roles tab go to Privileged Administrator Roles and select "owner".
    6. Now under the same newly opened window go to the members tab and click on add member.
    7. Now choose your ID from the right-hand side and click ok.
    8. Now go to conditions and choose the option "Allow user to assign all roles (highly privileged)" and then click on review + assign and create a role.
 The above steps are to make sure that you can see your images when they get uploaded, by default "owner privilege" is disabled in Azure even if you are creating it only.

<img width="785" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/6912f4c8-373f-4783-a99b-aa62ae784b2a">
<img width="1175" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/a8f4c42e-8155-4721-b358-b8fe40fd3cfd">
<img width="1212" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/3b9c3fb8-44f2-4fed-9c2c-53aa7bca6c6d">
<img width="1229" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/1cb06e1b-0ea8-4752-a9c6-4e2a7d54209d">
<img width="911" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/d2430cd4-a4af-4cf7-ba14-d330c8020b6a">



 Now let's deploy the application
    

    1. In the command Prompt in VS Code type " az login " and press enter. It will ask you to log in to Azure and log in with your Azure account.
    2. Now run the command docker "login your-login-server -u username -p password".(These details you will get from the "Access Key" section from your Registry in Azure Portal).
    3. Now we need to build the project, for this run the command " docker build -t myapp . "(Here myapp is the build name, you can give any name).
    4. Next step is to tag the build from your local to Register, i.e.... to map it(since there could be many registers in real life and all will have different login servers and details, so we map it). To do this use the command "docker tag myapp:latest your-login-server/myapp:latest."
    In the above command in the first part myapp is the build name and the latest is the tag name(you can think of tag as a version of the application), and in the second part, we are mapping the whole thing as to this particular login server(your-login-server) and appname and then tag name.
    5. Now we need to push the image to registery, for this use command "docker push your-login-server/myapp".


 Now since our image is pushed to our registry, let's create a container instance.
  1. Search for Container Instance in the Azure portal.
  2. Click on the + icon to start creating an instance.
  3. In subscription choose the same subscription which was in others(Registery) while creating them.
  4. Give container name I am giving "containerinstace" you can give any.
  5. You can leave Region, SKU, and Availability Zones as it is, Although make sure the Region is the same as Registry and Resource Group (This is just to maintain uniformity).
  6. In Image source choose " Azure Container Registry". Once you choose this option the registry name, image, and image tag will get enabled and it will be auto-filled, if you have only one image in the registry, if you have multiple you can make changes accordingly.
  7. Choose the size as per your CPU requirement of the application, I am leaving it as default.
  8. Next is the Networking Section. Inside this make sure the Networking type is Public.
  9. In the port section make sure it is the same as port no which you set while creating the docker file. I am leaving it as 80, if you would have chosen the port 5000, you need to make here 5000 also.
  10. Now review + create. Leave everything as it is. Since they are not required for now.
  


<img width="1214" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/d551463a-c322-49df-bea0-3899b69145da">
<img width="598" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/af7a31a4-4156-42a2-9f4d-f1c16b8436f1">


Once the deployment is complete for the container instance, go to it and open it.
In the overview section, you will find the public IP. Copy it and paste it into a new tab in the browser.
Suppose your ip is "http://20.241.292.56" then make it http://20.241.292.56/weatherforecast since weatherforecast is the name of the controller of the application we are deploying. Now your application will be visible in the browser.

Note:- Here all the requests are made on port 80 by default all http requests are made on it through the browser, so it is hidden. If we had given a different port no, then we need to mention it in the URL like "http://20.241.292.56:5000/weatherforecast".
Also, I have used port no 5000, so I redirected it in the browser while accessing it. I took the screenshots later. so might be possible that there is some difference in the screenshot compared with the points written for it.

<img width="1198" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/7e62b0d2-0805-43ff-b564-c4e6f13f0159">
<img width="549" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/59f46e10-7ad6-449e-9a03-93ca7fa99238">


Congratulations, You just deployed a dot net core application through a container instance.

