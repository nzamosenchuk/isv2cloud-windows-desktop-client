# How do I run Windows fat client in the Cloud? - **DRAFT**
**How do I migrate Windows desktop workloads to the Cloud?**
## Resolution
[Amazon AppStream](https://aws.amazon.com/appstream2/) is a cloud based service to centrally manage and stream your desktop applications securely to the end users via web browser or even embedding into existing web interface.

![Screenshot Application Streaming](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/appstream-journey.png?raw=true "Application Streaming")

To start streaming the application an image with preinstalled software needs to be created. The image is then used to configure the fleet of instances and finally define a stack - the combination of fleet, users policies and storage options.
### 1. Create an image with your application
1. With an existing AWS account in place, navigate to the console [https://console.aws.amazon.com/appstream2](https://console.aws.amazon.com/appstream2) and select the preferred region from the navigation panel in the top right corner.
1. To launch a graphical image builder. 
   ![Launch Image Builder](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/1_launch_image_builder.png?raw=true "Launch Image Builder")
   - Select **Images** on the lest and in **Image Builder** tab click **Launch Image Builder** 
   - But shall the console welcome you with **Get Started**, chose it and proceed with **Custom Setup**
1. Configure the image builder as giuded by the wizard, such as:  
   - Select the base image with desired Windows version and instance family;
   - Next give it a name and chose matching instance type;
   - Select the network to start an image builder instance in. If the instance is placed in the public subnet and requires internet select **Default Internet Access**. Please note, this network configuration is for image builder and will be configured sparately in the next section for the production use.
1. Image Builder instance will shortly be available and the status will change to **Running**. Click **Connect** to log into the instance.
   ![Connect](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/2_connect_image_builder.png?raw=true "Connect")
   Proceed with a local **Administrator** or personal user credentials if the Active Directory has been configured on the previous step. It's now time to install your application or applications together with all 3rd party dependencies if applicable. 
   ![Log in](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/3_log_in.png?raw=true "Log in")
1. Launch **Instance Assistant** from the desktop to configure the Application Catalog, the collection of applications that the user will see. Add your application(s) and proceed to the next step. 
1. Now it's time to clicking **Switch User** to log in into the **Template User** account without administrative rights to create the default configation for the application. Once logged in start application via **Image Assistant**, configure default settings, switch the user back to the Administrator.
1. Image Assistant should be already open, click **Save Settings**.
   - If using Active Directory, you will need to select the user you used in the previous step.
1. In order to verify the default configuration is properly persisted and the application behaves as expected switch to yet another user, a **Test User** if not using Active Directory, or another user otherwise. Start **Image Assistant** and select your application to perform the testing. Once done, switch back to the Administrator using **Switch User** button in **Image Assistant**.
1. Next step is to optimize the application startup, this is done automatically by **Image Assistant** all you need to do is to proceed with **Launch** on the **4 Optimize** step and follow the instruction to hit **Continue** after application properly started.
1. Finally, define the user-friend name for your image and allow the assistant to create an image. This will disconnect from the instance and it's a time to get back to AppStream console. Select **Images** - **Image Registry**, type the name of your image to check it's status and wait until it is reported as **Available**. Congratulations!
   ![Image is created](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/4_image_created.png?raw=true "Image is created")
### 2. Configure a Fleet of instances and define a Stack
1. On the left navigation panel select **Fleets**, chose **Create Fleet**, provide the fleet details and proceed to the next step.
   ![Create Fleet](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/5_create_fleet.png?raw=true "Create Fleet")
1. To quickly find the image previously created use a filter to show only **Private** images or filter by keywords.
1. The next steps provides the selection of the right instance type for your specific applications. The instances could be **Always-On** possibly resulting in higher charges or launched **On-Demand** reducing the bill if not in use, but the trade-off is a startup time of about a minute or two. When ready chose Next.
1. On this step it's a time to define the netowork. Similar to Section 1.3, but this time for the production configuration where your fleet will be placed.
1. Review the configuration and create a fleet that will host your application.
1. On the left navigation panel select **Stacks** and chose **Create Stack**. 
   ![Create Stack](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/6_creat_stack.png?raw=true "Create Stack")
   Provide the name and select the fleet that has just been created. Explore the optional parameters, such as **Redirect URL** to provide a seamless integration at the end of the session.
1. The next two steps allow you to configure home directory persistance in S3, Google Drive and OneDrive integration, and user settings, such as, printing, clipboard and file transfer. Finally, after reviwing the settings acknowledge the creation of the stack with **Create** button.

### 3. Invite users
1. Simplest and the fastest way to test the application or provide a trial access is using temporary streaming url.
   - Navigate to the stacks on the left panel and find your stack. Among actions chose **Create Streaming URL** and select the desired duration. 
   - Open the url in a browser or send to your trial customers. Chose the application to stream and start using it.
1. To invite users for a prolonged access to the application navigate to the **User Pool**. 
   ![Add user](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/7_create_user.png?raw=true "Add user")
   - Create a user with email and name. Once created, user will receive a welcoming email with necessary instructions to create a password;
   - Provide user with the access to the application by assigning the stack, select **Actions**-**Assign stack** from the dropdown menu.
   - User will receive an email with link to login and is ready to start using the application via AppStream 2.0.
1. Follow the login link and select the application from **Application Catalogue**
   ![Log in](https://github.com/nzamosenchuk/isv2cloud-windows-desktop-client/blob/master/8_login.png?raw=true "Log in")



## Related information
1. [Home folders, Google Drive and OneDrive](https://docs.aws.amazon.com/appstream2/latest/developerguide/persistent-storage.html)
1. [Custom branding](https://docs.aws.amazon.com/appstream2/latest/developerguide/branding.html)
1. [Access via AppStream Client for 4K resoluton and more](https://docs.aws.amazon.com/appstream2/latest/developerguide/client-application.html)
1. [Integration with Active Directory](https://docs.aws.amazon.com/appstream2/latest/developerguide/active-directory.html)
1. [Embed into your website or web application](https://docs.aws.amazon.com/appstream2/latest/developerguide/embed-streaming-sessions.html)
1. [Create Fleet and Stack](https://docs.aws.amazon.com/appstream2/latest/developerguide/set-up-stacks-fleets.html)
1. [Authentication with Single Sign-On](https://docs.aws.amazon.com/appstream2/latest/developerguide/external-identity-providers.html)
1. [User Pool Administration](https://docs.aws.amazon.com/appstream2/latest/developerguide/user-pool-admin.html)

