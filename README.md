# Microsoft-Bot-Application-Remote-HTTPS
This repos contains an example Microsoft Bot application, a simple echo app. You'll be using HTTPS to communicate with the bot. Information is provided on how to use your own PC to run the bot, no need for Azure or AWS, and have it accessible from the internet. You can access the bot with your own domain (e.g. ngrok.example.com). You won't need to open ports on your router or PC, although you'll have to run your bot in Administrator mode. To do so, you'll need service from ngrok.com. This repos is intended as a proof of concept only.

This document assumes that you have installed the requisites for a Microsoft Bot application and have a general understanding of how they are work, and how they are registered. See https://dev.botframework.com/ for more info. If you want to use your own custom domain (e.g. ngrok.example.com), you'll need to be familiar with setting up DNS.

The project was created in Visual Studio 2015 Community Edition using New->Project->Bot Application. The following changes were made to the newly created project to enable HTTPS over the internet.

1. In .vs/applicationhost.config, an XML file, remove "localhost" from the binding tag. This enables domain wildcarding. Note: wildcarding requires that the program/Visual Studio run in Administrator mode. Here's what the line looks like after localhost is removed.

  ```xml
  <binding protocol="http" bindingInformation="*:3978:" />
  ```
  
2. I see that .vs/applicationhost.config contains some hard coded paths to my dev system. You may need to change them to correctly compile but Visual Studio seems to do this automatically.

3. Replace YourAppId and YourAppSecret, following Bot Framework's documented procedure.

  ```xml
    <add key="AppId" value="YourAppId" />
    <add key="AppSecret" value="YourAppSecret" />
  ```

Steps to execute:

1. Open this repos in Visual Studio 2015 as Administrator. Administrator is required for wildcarding domains, although not for just localhost.

2. Execute. You should see IIS Express appear in the status bar. The app may then launch a webpage of http://localhost:3978. It's just a handy confirmation that things are working.

3. Use Microsoft Bot Framework Emulator to communicate with the app. You should see echoing of anything that you've entered. The emulator requires the URL (e.g. ngrok.example.com) of your running bot and the bot's Id and Secret.

  ```
  Url: http://ngrok.example.com:3978/api/messages
  App Id: YourAppId
  App Secret: YourAppSecret
  ```

Steps to Make Your Home PC Publicly Accessible over Internet Using ngrok.com TLS Tunneling Feature

1. Signup at ngrok.com for the Pro version of ngrok.

2. Download and install the ngrok app.

3. Open Visual Studio in Administrator mode.

4. Load this repos' solution file.

5. Start the app running (F5). Hopefully you followed instructions and are running in Administrator mode. The app will start listening on port 3978.

The following commands will make your PC accessible over the internet.

To use ngrok to access your own PC, register a Reserved Domain name of your choice using ngrok.com's dashboard (e.g. mydomain.ngrok.io). The following command starts ngrok tunneling of http. It will feed your PC's port 3978.
  ```
  ngrok http -subdomain mydomain 3978 
  ```

Here are the steps to enable use of your own sub-domain (e.g. https://ngrok.example.com) to access your PC's bot (instead of using an ngrok domain). The trick is to use ngrok's TLS Tunneling feature to terminate the SSL chain with your own SSL certificate. This avoids those annoying SSL errors seen in browser's and apps.

A. Setup an internet domain (e.g. example.com) that allows sub-domains (e.g. ngrok.example.com).

B. Use ngrok's dashboard to register an ngrok Reserved Domain of your choice (e.g. ngrok.example.com).

C. Use ngrok's newly created CNAME address (e.g. 123456890.cname.us.ngrok.io) as the value for your internet domain's CNAME record. This causes your sub-domain to point at your ngrok Reserved Domain (ngrok.example.com -> 123456890.cname.us.ngrok.io).

D. Create an SSL certificate for your internet sub-domain (e.g. ngrok.example.com). I obtained a free 90 day SSL cert from www.gogetssl.com. They have a very impressive SSL purchase and setup process. You may wish to get a wildcarding SSL if you want SSL on multiple sub-domains. I've seen them for as low as $40/year.

E. Put your SSL certificate's private key into a text file (e.g. ngrok.example.com.key) and the certificate into another file (e.g. ngrok.example.com.crt). This will enable SSL termination thus avoiding the dreaded SSL errors in browsers and apps.

F. Start ngrok's tls tunneling. 3978 is the port used by the repos app (as described above).

  ```
  ngrok tls -hostname ngrok.example.com -key ngrok.example.com.key -crt ngrok.example.com.crt 3978 
  ```

G. You should now be able to user your internet sub-domain (e.g. https://ngrok.example.com) to access the bot.
