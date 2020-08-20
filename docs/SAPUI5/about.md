# Introduction

---

![Logo](/doc/SAPUI5/logo.PNG)


## What is SAPUI5 ?
---
SAPUI5 is a framework that includes a collection of libraries that you can use to build applications that run in a desktop or mobile browser – while only maintaining one code base. Using the SAPUI5 JavaScript toolkit, it’s easy to build HTML5 applications that are accessible and responsive without additional coding.  
  
Developing open source apps? You also get the benefits of responsive UIs with OpenUI5, the open source version of SAPUI5.

More information on [SAP plateform](https://developers.sap.com/topics/ui5.html).

## Why choose SAPUI5 ?
---

#### The advantages of SAPUI5?


Multiple Technology Flavors used in SAPUI5 :

Since SAPUI5 is supporting HTML 5 i
Only latest versions of browsers (Chrome 29, Firefox 24, Safari 6.0, IE9 and Opera16) are supporting HTML5. Even Though HTML 5 is not supported by all the browsers, there are various reason web applications are being developed in HTML 5.

- There is many advantages of using SAPUI5 :  

    - It support cross-platform (Desktop & Mobile)  

    - Interactive user experience like Drag and Drop, Document editing, Timed Media player, etc.  
    
    - It has new tag Canvas, SVG which is helpful in animation or graphical representation (Chart, Flow Diagram, etc.,)  

    - Audio/Video support.


2.  SAPUI5 is extended from HTML 5 and provides several UI controls which is helpful in developing rich and interactive web application. GUI can be designed rapidly as there are SAP customized JS libraries available. Without much of coding UI Controls can be added in the web pages.

3.  SAPUI5 is supporting any data transfer like JSON, XML, oData, etc. Since it is using these web page can be rendered faster.

4.  SAP has released more products which can be used along with SAPUI5 like (SAP HANA, SAP Fiori, SAP Netweaver Gatway, etc.,) This shows clearly that       SAPUI5 is dominating technology.

5.  Most of the users now want their application to be viewed on their mobile devices.

It is a short list of simple and straightforward steps. A mobile application can be adapted as a PWA in a matter of minutes. When you open such am application on a mobile device, Google Chrome will propose to install it on the home screen, and, when installed, it will be indistinguishable from any other full screen native app.

#### The disadvantages of SAPUI5?
---
There are some limitations though. Despite OpenUI5 did a good job switching to asynchronous loading of resources recently, it still loads messagebundle.properties files with standard UI texts synchronously. An OpenUI5 PWA application should replace standard UI texts for dialog buttons, input placeholders etc. with specific ones. `The sap.ui.model.* `library does not have any fail-and-retry smart handlers for application data, and a special logic is needed for request handling in offline situations.

### Personal thoughts

---

I think Angular have better performance than SAPUI5 both in initial loading and rendering. Both have much smaller dependences file size which affect the initial loading of the application. 

Moreover, Angular rendering is also faster than SAPUI5 due to Angular Change Detection.

However, in SAPUI5, performance improvements can be achieved by explicitly setting the value of changed DOM element instead of re-rendering of the whole component. In addition, initial loading time can be improved.   


For my experience, I prefer Angular because I am more comfortable with this technology. I find that Angular is simpler because I have already studied this technology. But however I discovered SAPUI5 little by little and I find that there are a lot of documentation, tutorials available or even courses to train on it. And I found that maybe with more knowledge SAPUI5 remains easier to use for mobile applications because it is a technology created by SAP.