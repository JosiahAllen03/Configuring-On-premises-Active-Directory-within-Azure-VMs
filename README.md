<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />





<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

  

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)





<h2>Deployment and Configuration Steps</h2>

<p>
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/6a47cf51-f490-443d-a880-cff419164203)

</p>
<p>
  
 - Create the Domain Controller VM (Windows Server 2022) named “DC-1” 
    - Remember the Resource Group and Virtual Network (Vnet) that get created at this time
</p>
<br />

<p>
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/c88020c6-cca4-4e71-8b51-c42b39891324)

</p>
<p>

- Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in Step 1.
    - Ensure that both VMs are in the same Virtual network (Vnet)

</p>
<br />

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/e1ae2ed4-2b06-4bfa-86fa-1c1aa842b61f)

<p>

</p>
<p>

- Set the Domain Controller’s NIC Private IP address to be static. To do this head to the network section of DC-1 and select IP configurations then change the IP address to static. 

</p>
<br />

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/16576ea2-6341-41ad-92d9-279452eb280a)

- Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/b38e76c7-90bf-48fb-9a3f-3e6a7a20cf85)

- Once the ping starts you should notice that it fails. If it fails that means you have done everything so far correctly, it fails because we have not set up permission yet.

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/ce8653d3-5cf3-4bab-ba5a-6d8c9bac6d54)

- We then want to sign into our DC-1 using its public IP address

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/3ce7b09b-bdb9-4b60-9bde-fb653a402d1e)

- After signing in we want to open the Windows Defender firewall with advanced security
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/0a8f9d51-6831-4e77-a529-140af814e08f)

- From there you will select inbound rules and enable the highlighted rules. The rules will be under the Core Networking rule we can highlight both then right-click and enable them.
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/f8efbc17-96a8-410c-86bb-d9e5cb2cd477)

- After enabling those rules, if you head back to client-1 your ping request from earlier should finally go through.


### Installing Active Directory ###


![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/7f63c9a7-0796-4418-8776-8bebfa931ea0)

- We are now going to install Active Directory. Head over to your domain controller and open up the server manager. From here you will select add roles and features
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/9a84823f-6a00-40ed-8df0-86f0c898ae53)

- You can press next through all of the steps and make sure on server roles you select Active Directory Domain services then press add feature. 
  - We want to add the File and Storage Service feature. After adding press through the next steps then finally install. 
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/a410c2d8-5bc1-43eb-9ccd-3ee652771826)

- After the installation is complete head over to the top right and you should see a flag. Click this then press "promote server to a domain controller"

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/84557365-d785-4991-95fd-1356f6395486)

- After pressing promote a new screen will pop up here is where we will press the option "add a new forest". You can put the root name as anything just to make it easy to remember, in my example, I use the root domain name "mydomain.com"
    - After this, you will create a password and continue through by pressing next till prompted to install 

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/c5e82896-bd58-4fb5-864e-a08d8cef4e64)

- Installing will cause you to sign out which is done so the VM can get rebooted
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/5e808360-0522-49fa-96a2-24265b9fd39c)

- We then want to sign back into DC-1 using its public IP address. When signing in you also want to do your rootname \ whatever your username is.
    - My root name is "mydomain.com" and my username is Labuser so an example is ( mydomain.com\Labuser)
      - Once signed in head over to active directory users and computers by searching it

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/98fbcccc-c754-46a6-9ce6-bd0569a0e135)

- We will be making some organizational units or OUs. We want to make one called _employees and _Admins ( I put an underscore before the name so I know which are the ones I made) 

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/8e92246a-f7ed-4cfc-8689-532762ff84d1)

- After creating the OUs we then want to create a new admin. We can do that by selecting the admin OU and right clicking then selecting new then user. Here we will create a fake user called Jane Doe
  - For the password just put something you will remember
    
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/cdd900ee-accf-444b-b6d3-0b48a6871acc)

- After creating right-click on the Jane account you just made then head down to properties. We will now be giving Jane Doe Admin access.
    - We do this by selecting the member of category, and then pressing add. Here we can write Domain admins, then check names, then finally press okay. Jane should now have Admin access.
      
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/905fb1b8-6fb2-4785-87c8-7d9169db9450)

- After adding Jane to Domain Admins we can exit out of DC-1 and we will sign back in using our Jane account which will have admin privileges. We will still use DC-1s public IP address when signing in

  ### Joining Client-1 to the Domain Controller ###

  
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/8bebe5ae-7f7b-4127-bb97-33f8df85d55a)

- Head back to Azure and grab the DC-1's private NIC IP address. Feel free to have this pulled up or copy down DC-1s Private NIC address as we will need it in a few steps.
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/5358b728-e715-4bb6-81ed-7a601ac562bf)

- Open up client-1 in Azure and head over to the networking setting. Then press the Network interface, here is where we will change our DNS settings.
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/88205b32-f22d-408f-968c-ed06538856c1)

- Head over to DNS servers then Custom, and this is where we add our DC-1s private IP address. Finally, press save!
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/4224689e-252b-4ad7-aca5-ac1f2e2b03bc)

- We will once again sign back into our Client-1 account using our standard username an password that we created at the start

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/64de659f-edfd-47bc-8167-82738abf13be)

- Sign back into or pull up your client-1 VM. Right-click the start and pull-up system. Here we will press "Rename this PC Advanced"
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/74fd7dc3-5993-4ad2-92d1-503978010973)

- After pressing rename this PC, we want to press the option to change Domain /workgroup.
    - Then select domain and type in whatever your domain root name is mine is (mydomain.com)
      
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/b6df6b2d-ffc8-4cc7-bc5b-366c7d4e23fc)

- Here you will be prompted to sign in. We will sign in using the admin account we created earlier.
  - It may tell you the VM will restart just press okay. By doing this we are now able to sign into client-1 with any Admin account as long as it was connected to our Domain controller.
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/6a355a49-f211-4a1d-a9cf-6ffaf6c70d87)

- Sign back into client-1 using our Jane_admin account

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/50e96f9f-2214-483d-bc9d-acb10b7e2c23)

- After signing back in we can head over to the Remote Desktop setting. You can get here by searching it in the settings. Then head down to select users that can remotely access this pc. 
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/a445e647-dbaa-45b2-bdcd-6ed74b99d926)

- Once that completes head back over to our DC-1 and search again for Active Directory users and computers.  From here click Users then Domain User.
   - This allows you to check who is considered a Domain user and also who would have access to client-1


### (Bonus Step) Mass Creating Users to Test Privileges ###


![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/fd7578a7-9f53-4ade-9258-724f0b5e2b69)

- Open your DC-1 and then open Powershell ISE as an Administrator
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/4d7ad1f2-b1c5-42aa-8640-13845d2b0957)

- once open at the top press new script. Here is where we will enter our Account creating script
    - (feel free to copy it from here https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 ) Important script was not made by me!
      
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/efaced81-7012-44df-90ed-72e0f6b1516b)

- Paste the raw code that we copied to mass-make accounts. Feel free to adjust the number in the code to the amount of accounts you want. In the script, all accounts will be put into the OU for _employees.
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/c19fa94c-52dd-4903-9e22-f4d5df8e1b91)

- At the top of the code is the amount of accounts and the password. Then below it shows where the accounts will head to the path of _employees.
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/3033342a-5c42-4a3a-b972-3a1852915ce8)

- From there you can run the script and it will make the accounts
  - - To double-check if everything is working. Head back to active directory users and computers then head to employees. Here you should see all the new users we just created
  
![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/ff0d4462-dfe4-4f81-96f7-9593d1ded930)

- Now we can take our random user account, and copy their display name. From the rules we set earlier, we now should be able to log in using these random accounts on our Client-1 VM.
  - So head back to client-1 and try to sign in with one of the random user accounts.
  

![image](https://github.com/JosiahAllen03/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/147882549/71aed28b-90e3-48a9-a126-1dd6b942bb13)

- Sign in like normal, then open up the command line. By opening the command line we can write "whoami" to double-check and make sure we are signed into our new user.

  #### Lab Overview and Clean Up ####


- The lab is now over. A brief overview of things we accomplished during this lab. We have ensured connectivity between our two machines, Installed Active Directory, Created admin and normal user accounts using AD, We joined our Client-1 vm to our Domain controller, Setup remote desktop access to non-admin accounts, and lastly, Created a bunch of users to use to log into client-1

  - Feel free to test other permissions with the users we created before deleted the lab!

 - For clean up delete, all of your virtual machines/resource groups. Feel free to delete anything we created to save your free credits/money!
