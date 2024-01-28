
# Container Instance
Hello Everyone, In this project we will see how can we deploy an application using Container Instance.

Pre-Requisite:  
                
                1. Docker Desktop (Install and Run it)
                2. Azure Account with free trail subscription or Pay as you Go Subscription.

To Deploy the application on Container Instance first we need to store it somewhere. For this we will use ACR(Azure Container Registry).

So First thing First.
Let's create a project, for my case i will download it.

    1. Open the Visual studio code, Select a balnk folder where you would like to keep you project.
    2. Now press "ctrl" + "`" key, it will open the command line. from right side on prompt Switch it to command promt from drop-down menu, by-default powesherll gets open.
    3. Run this command "dotnet new webapi -o projectname" (without quotes). This will create a basic default .net project.

Since our basic project is ready lets move to create Azure Container Registry. We will add the docker file later in this project.

    1. Open Azure Portal.
    2. From top search menu, search for Container Registry.
    3. Click on "+" icon to start creating a new one.
            First section is basics.
    4. In Resource Group click on create New and name it as "Container".
    5. Now Enter the registry name, for me i am writing "srjcontainerinstance", you can give any relevant name.
    6. Chosse the location as EastUS. You can choose any location based on ypur requirement and subscription plan.
    7. Keep the pricing plan as Standard.
    <img width="740" alt="image" src="https://github.com/SRJ1307/.Net-Core-Container-Instance/assets/157812379/3ef22aa5-6cb7-41cc-a228-b2e4a3848c3d">

    8. Move to Networking section.
    9. Here you can choose the scope of registry, wether you want it to be publicly available or make it private. Since i am using Free Trail subscription, this option is disabled for me. So make it public and move to next section as Encryption.
    10. Here you can choose the Encryption method, wether you want it to encrypt using your own key or let it managed by Azure itself. I am choosing the second option as first one is disabled.
    11. Now move to last section as "Review + Create". Here microsoft will perform validation for you selected choices and inputs based on you subscription and feasibility.
    12. once the Validation passed, click on "create" on left bottom side indicated in blue color.
 
 While the registry is being created and deployed. Let's move back to VS Code.

    1. Now we need to add the Docker file in our project.
    2. Click "ctrl" +" Shift" +"P" key together. it will open a dropdown box from top.
    3. There choose " Docker: ADD Docker File to workSpace".
    4. Once you will choose the option next is to provide the type of application you are deploying, there choose .NetCore.
    5. Choose Linux in next step.
    6. Now here it is very important step, you need to choose the port number on which you want to deploy the application. This port no is of Docker image which you wil be creating. For our ease lets choose 80 as port no.
    7. Choose "NO" as option when it ias asking for compose.
    8. Now your Docker file is created.
 By This time Registry must have been deployed.

    1. Go to your created registry in azure portal.
    2. In left Pane check for "Access Key" option. .Inside the make sure "Admin User" checkbox is ticked, by default it is not ticked.
    3. Now go to Access Control(IAM) in the smae left pane of your registry.
    4. Inside IAM move to Role Assingments and click on "+" icon to create a role assingment.
    5. When you will click the + icon, a new window will open , inside that under roles tab go to Privilaged Administrator Roles and select "owner".
    6. Now under same newly opened window go to memebers tab and click on add member.
    7. Now choose ypur id from right hand side and click ok.
    8. Now go to conditions and choose option "Allow user to assign all roles (highly privileged)" and then click on review + assign and create a role.
 The above steps are to make sure that you are able to see your images when they get uploaded, by default owner privillage is disabled in Azure even though if you are creating it only.

 Now let's deploy the application
    

    1. In the command Prompt in VS Code type " az login " and press enter. It will ask you to login to azure, login with your azure account.
    2. Now run the command docker login  your-login-server -u username -p password.(These details you will gte from "Access Key" section from your Registery in Azure Portal).
    3. Now we need to build the project, for this run the command " docker build -t myapp . "(Here myapp is build name, you can give any name).
    4. Next step is to tag the build from your local to Registery, i.e.. to map it(since there could me many registery in real life and all will have different login servers and details, so we map it). To do this use command docker tag myapp:latest your-login-server/myapp:latest.
    In above command in first part myapp is build name and latest is tag name(you can think of tag as version of application), and in second part we are a mapping the whole thing as to this particular login server(your-login-server) and appname and then tag name.
    5. Now we need to push the image to registery, for this use command "docker push your-login-server/myapp".


 Now since our image is pushed to our registry, let's create a container instance.
  1. Search for Container Instance in azure portal.
  2. Click on + icon to start creating an instance.
  3. In subscription choose the same subscription which was in others(Registery) while creating them.
  4. Give container name i am gving "containerinstace" you can give any.
  5. You can leave Region, SKU and Avaliability Zones as it is, Although make sure Region is same as registery and Resource Group (This is just to maintain uniformity).
  6. In Image source choose " Azure Container Registry". Once you choose this options for registery name, image and image tag will get enabled and it will be auto filled, if you have only one image in registery, if you have multiple you can make changes accordingly.
  7. Choose size as per your CPU requirement of application, i am leaving it as default.
  8. Next is Networking Section. Inside this make sure Networking type is Public.
  9. In port section make it is sure port no which you set while creting the docker file. I am leaving it as 80, if you would have choosen the port 5000, you need to make here 5000 also.
  10. Now review + create. Leave everything as it is.Since they are not required for now.

Once the deployment is complete for container instance, go to it and open it.
In overview section you will find the public IP. Copy it and paste it in new tab in browser.
Suppose if your ip is "http://20.241.292.56" then make it http://20.241.292.56/weatherforecast since weatherforecast is the name of controller of application we are deploying. Now your application will be visible in browser.

Note:- Here all the requestes are made on port 80 by default all http requests are made on it through browser, so it is hidden. If we would have given different port no, then we need to mention it in url like "http://20.241.292.56:5000/weatherforecast".

Congratulations, You just deployed a dot net core application through container instance.

