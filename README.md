# Dean's Automation Tool 

![Screenshot 2023-12-05 161433](https://github.com/DeanGarofalo/Automation-Tool-Winapp/assets/49959557/d6209b6c-3e81-42d2-9b9d-57614c59a5ba)

This is a showcase of an application I built in 2022 to serve a very specific workflow for my colleagues. It's designed to perform various infrastructure tasks that would have had to been done manually by a user and now would be automated by this application. 
I'm choosing to not publish the code for this but to showcase what the Automation tool did. If you would like to get an ideal how it worked, please check out my Python version. It was written to imitate this and is pretty much how this was coded apart from a GUI and in another language as a concept.


# What does it take in?
The Automation Tool takes in input of a Microsoft Excel file, which in this workflow acts as a "server workbook" containing details of various types of software ranging from large scale distributed applications to single database servers, to micro-service clients.

Example Excel file:
![Screenshot 2023-12-05 182419](https://github.com/DeanGarofalo/Automation-Tool-Winapp/assets/49959557/4fc4a618-a674-4cf4-b6ac-6ff45b75e0f1)

Basically, it's a document that has server credentials for a given app in each environment and also information on the different networks in play. Workbooks contain multiple apps and environments. 

This application takes that Excel file and scans it for the relevant information pertaining to which application the user selects in the first screen. After that the available options are now selectable by the user. 

# What does it do now?

Regardless of what options are selected the Automation Tool will primarily create a PuTTY profile with SSH tunnels for each of the servers within the selected App.

### Why is a PuTTY profile important? 
Well again I want to reiterate that this was designed for a very specific type of workflow. The important thing to consider is that access to these environments is unique. Typically, a user must launch the proprietary VPN. That VPN connects to the environment and creates a tunnel for the user to connect to. So, the only way the user can connect to the environment is through a SSH connection on localhost via :22. Therefore there is never direct 1 to 1 access to any of the servers. Think something like Cloudflare tunnels, Tailscale, etc. but not exactly.

Taking that into account and the fact that users would be using Windows as their OS, and by nature of the access predicament, utilizing SSH tunnels is paramount and a real hassle when you are talking about large amounts of servers you may have to connect to at a single time. The number of tunnels you would have to create manually is a real pain since just 1 server could require 20 tunnels, since each port you might need to work on would require a tunnel. Multiple that by each server and you have a nightmare setting up a SSH configuration for an environment. PuTTY provides a nice simple interface for saving SSH configuration settings such as SSH tunnels as profiles. 

---

The main part of the Automation Tool is to automatically make those tunnels for you in the form of a PuTTY profile. So, after you run the Automation Tool and possibly select some of the options, you have a profile you can load which has all the SSH configuration done for you. All with a literal one button click from the user.

When running the Automation Tool, you can select optional infrastructure tasks to be done as well.
The options are:
- Checking servers' hardware specifications according to the selected app.
  - Each application has its own highly specific requirements which differ based on the exact type of deployment detected. This option checks every single server and reports back to the user which server are valid and which are not.
- Building and deploying a Unix Hosts file for each server.
  - All of the applications require a Hosts file with all the servers that make up an application in it. This will build that file and deploy it on each and every server in it.
- Opening the required firewall ports.
  - Each application has specific network ports that need to be opened. This option runs commands to open those ports on each server. Each server can also have unique port requirements depending on what type of server it is within the larger part of the deployment.
- Open the connection to the environment through the proprietary VPN automatically.
  - The Automation Tool will connect to the environment automatically by first launching the VPN to the specific site, then open a SSH connection to a jump server it finds in the Excel file using the newly created PuTTY profile done moments before this option.
    - If this was not selected the Automation Tool assumes the user already has the VPN launched and connected. This is sometimes commonly used with newer versions of the VPN. In this case the Automation tool just launches the newly created PuTTY profile to connect.


When you click run, the Automation Tool opens a progress window and updates the user on what exactly is being done. If there is missing information the program prompts the user to give it the needed information or the option to quit.
![Screenshot 2023-12-05 162937](https://github.com/DeanGarofalo/Automation-Tool-Winapp/assets/49959557/5701a2d3-e067-48a4-a60f-9cc6e454e2b8)

There is extensive error checking as well. Since the Automation Tool has to know a lot about the individual environment and the specific type of application deployment, sometimes not all the information is properly listed in the Excel file. So, if there is something missing or out of place the program will pause and prompt the user for the correct input and then continue to validate that it makes sense, is valid, and is supported.

# Advanced mode
I also included an Advanced mode section which just let the user bypass the main function of the Automation Tool to just run some commands on servers without opening a SSH session and using the apps GUI to do it.
![Screenshot 2023-12-05 164819](https://github.com/DeanGarofalo/Automation-Tool-Winapp/assets/49959557/3e59a1ab-684d-4f62-a294-fc957b869c46)

# SuperPuTTY templates
![image](https://github.com/DeanGarofalo/Automation-Tool-Winapp/assets/49959557/cb2f1590-2996-4f19-8f9f-cd679f51d5e6)

This option requires knowledge of what SuperPuTTY is. Basically, its PuTTY but with tabs, so you can work on multiple servers at the same time and even run one command on all at the same time. 
This option has the Automation Tool generate a SuperPuTTY template that corresponds with the tunnels created for the supported apps. So, for example let’s say an app has 6 different types of support deployment types. Each deployment type has X number of servers and split between 2 data centers (sometimes more). This creates a template for all possible configurations and saves that in SuperPuTTY. So, if a user wants to connect to all the servers in a deployment all at once, they can just launch the corresponding SuperPuTTY profile for it and open multiple PuTTY sessions all at once. This works because during the PuTTY SSH profile creation, I create extra tunnels solely for this purpose.

# Packaging
The application was then packaged as a .MSIX application for installation & distribution, which I was a big fan of. I really enjoyed how simple it made things, including updating and just the breeze it was to distribute.
![Screenshot 2023-12-05 163045](https://github.com/DeanGarofalo/Automation-Tool-Winapp/assets/49959557/0829c2bf-993c-4a3b-8fc6-451bfedc84b2)

---

If you're still reading and are confused:
At a very high level you can think of this as doing work that Ansible would do in terms of server automation, but tailored to a very specific workflow and with focus on the initial server build. The Automation Tool significantly reduced deployment and build time for servers with the added plus of reducing complexity for users. By making a GUI with 1 button click functionality it greatly reduced training times as well.

