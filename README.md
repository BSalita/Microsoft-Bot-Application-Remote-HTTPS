# Microsoft-Bot-Application-Remote-HTTPS
This is an example Microsoft Bot application, a simple echo app, that uses HTTPS (not HTTP) to communicate with a remote server (e.g. www.example.com). This example is intended for use as a proof of concept only.

This document assumes that you have installed the requisites for a Microsoft Bot application and have a general understanding of how they are work, and how they are registered. See https://dev.botframework.com/ for more info.

The project was created in Visual Studio 2015 Community Edition using New->Project->Bot Application. The following changes were made to the newly created project to enable HTTPS over the internet.

1. In .vs/applicationhost.config, an XML file, removed "localhost" from binding which enables wildcarding. Note: Wildcarding requires that the program/Visual Studio run in Administrator mode.

  ```xml
  <binding protocol="http" bindingInformation="*:3978:" />
  ```
  
2. I see that .vs/applicationhost.config contains some hard coded paths my dev system. You may need to change them to correctly compile.

3. Note that Web.config has not been changed. If we were using HTTPS, YourAppId and YourAppSecret would have to be updated by the documented procedure. Since HTTP is being used, and [BotAuthentication] is disabled, these values are ignored.

  ```xml
    <add key="AppId" value="YourAppId" />
    <add key="AppSecret" value="YourAppSecret" />
  ```

Steps to execute:

1. Open this repos in Visual Studio 2015 as Administrator. Administrator is required for wildcarding domains, although not for just localhost.

2. Execute. You should see IIS Express appear in the status bar. Your default browser will be invoked using http://localhost:3978.

3. Use Microsoft Bot Framework Emulator to communicate with the app. You should see echoing of anything that you've entered. Enter the following, use default values for other boxes.

  ```
  Url: http://www.example.com:3978/api/messages
  App Id: YourAppId
  App Secret: YourAppSecret
  ```

Steps to Make Your Home PC Publicly Accessible over Internet Using ngrok.com TLS Tunneling Feature

1. Signup for Pro version of ngrok

2. Download and install ngrok from ngrok.com.

3. Open Visual Studio in Administrator mode.

4. Load solution file.

5. Execute app (F5)

6. The following commands will make your PC accessible over the internet. The above app should be running in Adminstrator mode, waiting for input on port 3978.

To use an ngrok domain to access your PC (e.g. https://mydomain.ngrok.io), register a Reserved Domain name of your choice using ngrok.com's dashboard (e.g. mydomain).
  ```
  ngrok http -subdomain mydomain 3978 
  ```

Steps to use a your own custom domain to access your PC (e.g. https://ngrok.example.com). The trick here is to use ngrok's TLS Tunneling feature to terminate the SSL chain with your own SSL certificate.
  A. Register your internet domain (e.g. example.com).
  B. Register an ngrok Reserved Domain of your choice (e.g. ngrok.example.com).
  C. Use ngrok's newly created CNAME address (e.g. 123456890.cname.us.ngrok.io) for your internet domain's CNAME record (value).
  D. Create an SSL certificate for your internet sub-domain (e.g. ngrok.example.com). I started with a free for 90 days SSL cert from www.gogetssl.com. They have a very impressive purchase process. You may wish to get a wildcarding SSL if you want SSL on multiple subdomains. I've seen them as low as $40/year.
  E. Put your SSL certificate's key into a file (e.g. ngrok.example.com.key) and the certificate into another file (e.g. ngrok.example.com.crt).
  F. Start ngrok tunneling. 3978 is the port from the above running app.
  G. Use your internet sub-domain to access the app (e.g. https://ngrok.example.com).
  ```
  ngrok tls -hostname ngrok.example.com -key ngrok.example.com.key -crt ngrok.example.com.crt 3978 
  ```
