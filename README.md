# VbFcgi
FCGI Host/Server Framework for Visual Basic 6 (VB6)/COM FCGI Applications

# Introduction
VbFcgi is a framework that allows you to easily get VB6 code onto the web. It was developed against Nginx, but should work with any web server that implements the FCGI spec.

# Included Executables
There are 3 main components of the VbFcgi framework:
1. VbFcgi.dll - This is the main framework library that includes all of the code for listening and responding to FCGI requests from the web server, as well as parsing out records for FCGI parameters, HTTP cookies, etc...
2. VbFcgiHost.exe - This is main executable file that will spawn FCGI listeners (multiple listeners support for load balancing) and monitor for terminated listeners that need respawning. It also acts as a shutdown co-ordinator.
3. VbFcgiApp.dll - This is the FCGI Application code. Typically this will be the only file you need to work with in order to create your FCGI application (the VbFcgi.dll and VbFcgiHost.exe projects will hopefully be reusable from project to project). The version inclded here is just a very basic proof-of-concept that will send an HTML page upstream with a table of the FCGI parameters that were received.

While the above DLLs are COM ActiveX libraries, you do NOT need to register them with regsvr32 since this code uses Olaf Schmidt's registration-free DirectCOM library. No harm will be done if you do register the DLLs though, it's just not required.

Also included is a binary build of Nginx with a basic configuration to support a single FCGI host server listener on localhost:9100. This is included for the sake of convenience and to demonstrate a minimal configuration. You should have your own properly configured Nginx (or other web server) running in most cases.

Lastly, I've also bundled Olaf Schmidt's excellent vbRichClient5 library (http://www.vbrichclient.com/), again for the sake of convenience. You can always get the latest version from the vbRichClient5 website.

# Demo Usage
1. If you don't already have a web server running, start nginx from the command-line by going to the .\VbFcgi\bin\nginx folder and then rnning the nginx.exe command. If you already have a web server running, make sure it is configured to pass *.fcgi requests from the browser upstream to 127.0.0.1 port 9100.
2. From the command line, start VbFcgiHost.exe with the following command: vbfcgihost.exe /host 127.0.0.1 /port 9100 /spawn 1
3. Open your browser and go to http://127.0.0.1/test.fcgi - you should see the HTML response from the demo FCGI application.

# Creating your own FCGI Application
You can use the included VbFcgiApp source code as a starting point - all the work is done in the IFcgiApp_BuildResponse method, so give it a thorough review.

In order to write your own FCGI application from scratch, you must:

1. Start a new ActiveX DLL project in VB6.
2. Change the name of the project from "Project1" to "MyFcgiApp" (or whatever name you would like it to have).
3. Change the name of "Class1" to "CFcgiApp".
4. Add a reference to VbFcgiLib from the Projects menu > References.
5. In the General section of the "CFcgiApp" class, type; Implements VbFcgiLib.IFcgiApp
6. Select "IFcgiApp" from the drop down list in code view. It will create the IFcgiApp_BuildResponse method for you.
7. Code your app in the IFcgiApp_BuildResponse method (the rest of the f*cking owl).
8. Build your app, but when prompted for the name of the DLL, call it "VbFcgiApp.dll" and build it next to the VbFcgiHost.exe and VbFcgi.dll files.

When you subsequently run the VbFcgiHost.exe, it will use your VbFcgiApp.dll as a "plugin" of sorts for responding to FCGI requests.

Enjoy!