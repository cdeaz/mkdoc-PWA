# Start with SAPUI5

--- 

Progressive Web Apps use some of the latest browser technologies, so there are some important requirements for PWAs to run properly. However, if these requirements are not met, the PWA will gracefully degrade to a regular web app, which means it will still work, but only online. Here is what is needed for full functionality:

- HTTPS-Access to the web server, where the app is hosted (localhost will work for development too)
- OpenUI5 version 1.54 or later (1.52 should work too, but might have limitations)
- A modern browser like Chrome or Firefox. Try my PWA-Checker to find out, if a specific browser will work.

## Install the tools
---  

You must install the following tools if you do not already have them on your machine:


### Node.js
---
Download and install the latest LTS version of Node.js here:

 [NodeJS Download Website](https://nodejs.org/en/download/)

### UI5 CLI
---
#### Installing the UI5 CLI

```
npm install --global @ui5/cli

# Verify installation
ui5 --help
```

More information on [this website](https://sap.github.io/ui5-tooling/pages/CLI/).


### Yeoman 
---  

What's Yeoman?  

Yeoman helps you to kickstart new projects, prescribing best practices and tools to help you stay productive.

It provide a generator ecosystem. It's basically a plugin that can be run with the `yo` command to scaffold complete projects or useful parts.

More information on the website [yeoman](https://yeoman.io).  

#### Install Yeoman

```
npm install -g yo
```

We need a generator :

```
npm install -g yo generator-easy-ui5
```

Verify your installation :

```
yo
```
  


### SAP Cloud Platform Cloud account
---
Create a free SAP Cloud Platform Cloud account [here](https://developers.sap.com/mena/tutorials/hcp-create-trial-account.html).

Set your environment up and connect to your Cloud Foundry endpoint

### Sub-generators to avoid recurring tasks
---
#### Add a new view

This sub-generator will create a new view (of the same type you specified during the generation of your project) and a new controller and route.

```
yo easy-ui5:newview
```

#### Create a custom control

Run the following command from your project’s root to scaffold a custom control.

```
yo easy-ui5:newcontrol
```

#### Add a new model

This sub-generator will create a new model in your manifest. Currently, JSON and OData v2 models are supported with various configuration options.

```
yo easy-ui5:newmodel
```

#### Add a new component usage

This sub-generator will add a new component usage for component reuse to your manifest.

```
yo easy-ui5:newcomponentusage
```

More information [here.](https://blogs.sap.com/2019/02/05/introducing-the-easy-ui5-generator/)



### Install Postman
---

Postman is collaboration Platform for API Development.

It simplify each step of building an API and streamline collaboration so you can create better APIs—faster.

Download Postman [Here](https://www.postman.com/downloads/).  


![Logo](/doc/SAPUI5/logopost.jpeg)
  

##### Your development environment is now ready.     


---

  
> **Note :** I advise you too use an editor like VS Code or Atom. Also, use Chrome as your default browser for development, because the Developer Tools are very complete, and you also have the possibility of installing Augury, a specific Chrome plugin for Angular development.


