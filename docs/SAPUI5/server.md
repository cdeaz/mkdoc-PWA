# Server-side application
---

## Generate your SAPUI5 project

Create you application like this :

```
yo
```

![yo](/doc/SAPUI5/yo.PNG)

Here is what you have in you repository :

![arbo](/doc/SAPUI5/arbo.PNG)

### Set your environment up and connect to your Cloud Foundry endpoint

```
cf login
```

### Run it locally 

```
cd <your project name>
```
and :
```
npm start
```

### Deploy it to SAP Cloud Platform (Make sure you set up your environment correctly)

```
npm run deploy
```

> **Note :**  If you want to deploy your Application on a plateform that is free here on [pivotal.](https://run.pivotal.io)