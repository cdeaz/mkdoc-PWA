# Start the project 
---

Now you have finish the part of the server-side of your Application.

We will start to create a login page and a home page who will contains a list of sales documents of a customer order.

## API connexion
--- 

#### Postman

Open POSTMAN and then Import this [file](/doc/SAPUI5/API.json) into it. 

![Postman](/doc/SAPUI5/postman.PNG)


### manifest.json

Add under `"description"` :
```
 "dataSources": {
			"salesOrder": {
				"uri": " https://dev.apimanagement.hana.ondemand.com:443/v0/salesOrder",
        "type": "odata",
        "settings": {
          "headers": {
            "APIKey": "lnsJ0j0B1sJ5trfTMbMqrmIqIarVApME"
          }
        }
			}
		}
```
And under `"models"` add :

```
      "salesorder": {
        "type": "sap.ui.model.odata.v2.ODataModel",
        "dataSource": "salesOrder"
      }
``` 

### Errors encountered

You will probably have encountered an error on CORS

![error](/doc/SAPUI5/error.png)

You have to create a `PROXY`.

Here more informations about [CORS](https://developer.mozilla.org/fr/docs/Web/HTTP/CORS).

- How to creat a Proxy ?

On this [website](https://blogs.sap.com/2019/08/26/ui5-tooling-custom-server-middleware-proxy-extension/) choose option 2.

## Create a Login Page 
---
Create a login controller, a login model and a login view.

### index.html
---
```
<!DOCTYPE HTML>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link href="/resources/img/favicon.ico" rel="shortcut icon" type="image/x-icon" />


  <title>SAPUI5PWA</title>

  <script id="sap-ui-bootstrap" src="https://sapui5.hana.ondemand.com/resources/sap-ui-core.js"
    data-sap-ui-theme="sap_fiori_3" data-sap-ui-resourceroots='{
        "com.myorg.SAPUI5PWA": "./"
      }' data-sap-ui-oninit="module:sap/ui/core/ComponentSupport" data-sap-ui-compatVersion="edge"
    data-sap-ui-async="true" data-sap-ui-frameOptions="trusted">
    </script>

    <script src="./login.js"></script>

</head>

<body class="sapUiBody" >
<div id='content'></div>


</body>

</html>
```

![login](/doc/SAPUI5/login.PNG)

### manifest.json
---

Add in `routes`and `targets` :

```
"routes": [
        {
          "name": "RouteLogin",
          "pattern": "",
          "target": [
            "TargetLogin"
          ]
        }
      ],
      "targets": {
        "TargetLogin": {
          "viewLevel": 0,
          "viewName": "Login"
      }
```
### Login.view.xml
---

```
<mvc:View 
    controllerName="com.myorg.SAPUI5PWA.controller.Login"
    displayBlock="true"
    xmlns="sap.m"
    xmlns:mvc="sap.ui.core.mvc">

    <Page title="Authentification">
        <content>

            <VBox fitContainer="true" justifyContent="Center" alignItems="Center" alignContent="Center">
                <items>
                    <Input id="cnum" placeholder="Customer number"></Input>
                    <Input id="cord" placeholder="Customer order"></Input>
                    <Button text="Log In" width="12rem" type="Emphasized" press="onLoginTap"></Button>
               </items>
            </VBox>
        </content>
    </Page>
</mvc:View>


``` 
### Login.controller.js
---

```
sap.ui.define([
  "com/myorg/SAPUI5PWA/controller/BaseController",
],  function (Controller) {
  "use strict";
  return Controller.extend("com.myorg.SAPUI5PWA.controller.Login", {
    onLoginTap:function(){
          var cnum = this.getView().byId("cnum").getValue();
          var cord = this.getView().byId("cord").getValue();
      
          console.log(cnum);
          console.log(cord);
            
          // Customer number and customer order identification
            const router = this.getOwnerComponent().getRouter();
          if (cnum==='0001007260' && cord==='0100010237') {
            router.navTo('RouteSalesOrders', {
              cnum: cnum
            }); 
          } else {
            alert('Fail to connect wrong customer number or customer order');
          }
        
        }
  });
});

```
### I18n_en.properties

```
# App Descriptor
title=Exemple PWA SAPUI5
appTitle=Exemple PWA SAPUI5
appDescription=App Description
homePageTitle=Exemple PWA SAPUI5

# Sales List

columnSalesNum=Sales Doc.
columnStatus=Status
columnDate=Order received Date
columnPurchaseNum=Ship-to
columnSoldTo=Sold-To party
columnShip=Shipped date
columnMaterial=Material
columnQuantities=Quantities
columnTrack=Tracking number


```

## Create the list of sales order
---

Create a Products controller, a style css, a SalesOrder view and a Products view.

### manifest.json
---

Add in `routes`and `targets` :

```
      "routes": [
        ...,
        {
          "name": "RouteSalesOrders",
          "pattern": "salesorders/{cnum}",
          "target": [
            "TargetSalesOrders"
          ]
        }
      ],
      ...,
        "TargetSalesOrders": {
          "viewLevel": 1,
          "viewName": "SalesOrder"
        }
      }
```

### style.css

```
html[dir="ltr"] .myAppDemoWT .myCustomButton.sapMBtn {
    margin-right: 0.125rem
}

html[dir="rtl"] .myAppDemoWT .myCustomButton.sapMBtn {
    margin-left: 0.125rem
}

.myAppDemoWT .myCustomText {
   display: inline-block;
   font-weight: bold;
}

/*  ProductRating */
.myAppDemoWTProductRating {
   padding: 0.75rem;
}

.myAppDemoWTProductRating .sapMRI {
   vertical-align: initial;
}

```


### SalesOrder.view.xml

```
<mvc:View
   controllerName="com.myorg.SAPUI5PWA.controller.SalesList"
   xmlns="sap.m"
   xmlns:mvc="sap.ui.core.mvc">
	<Page titleAlignment="Center" title="UI5 PWA Example - Product List" showNavButton="true" navButtonPress="onNavBack">
   <Table
      id="SalesList"
      headerText="{i18n>ListTitle}"
      class="sapUiResponsiveMargin"
	 
      width="auto"
      items="{
         path : 'order>/'
      }">

      <headerToolbar>
         <Toolbar>
            <Title text="{i18n>ListTitle}"/>
            <ToolbarSpacer/>
            <SearchField width="50%" search="onFilterSalesOrder"/>
         </Toolbar>
      </headerToolbar>
     <columns id="List">
			<Column minScreenWidth="Small"
				demandPopin="true">
				<Text text="{i18n>columnSalesNum}"/>
			</Column>
			<Column
            minScreenWidth="Small"
				demandPopin="true">
				<Text text="{i18n>columnStatus}"/>
			</Column>
						<Column
				minScreenWidth="Small"
				demandPopin="true">
				<Text text="{i18n>columnDate}"/>
			</Column>
         	<Column
				minScreenWidth="Small"
				demandPopin="false">
				<Text text="{i18n>columnshipto}"/>
			</Column>
			<Column
				minScreenWidth="Small"
				demandPopin="true">
				<Text text="{i18n>columnPurchaseNum}"/>
			</Column>
			<Column
				minScreenWidth="Small"
				demandPopin="true">
				<Text text="{i18n>columnSoldTo}"/>
			</Column>
			<Column
				minScreenWidth="Small"
				demandPopin="false">
				<Text text="{i18n>columnShipped}"/>
			</Column>
			<Column
				minScreenWidth="Small"
				demandPopin="false">
				<Text text="{i18n>columnMaterial}"/>
			</Column>
			<Column
				minScreenWidth="Small"
				demandPopin="true">
				<Text text="{i18n>columnQuantities}"/>
			</Column>
			<Column
				hAlign="End">
				<Text text="{i18n>columnTrack}"/>
			</Column>
		</columns>
		<items>
			<ColumnListItem
				press=".onPress">
				<cells>
					<ObjectNumber number="{order>salesOrderID}" emphasized="false"/>
					<ObjectIdentifier title="{order>Status}"/>
					<ObjectNumber number="{
						path: 'order>customerPODate',
						type: 'sap.ui.model.type.Date',
				        formatOptions: {
				          pattern: 'yyyy/MM/dd'
				        }
					}" emphasized="false"/>
					<ObjectIdentifier title="{order>soldTo}"/> 
					<Text text="{
						path: 'order>customerPONumber',
						formatter: '.formatter.statusText'
					}"/>
					<Text text="{order>soldTo}"/>
					<ObjectNumber number="{
						path: 'order>customerPODate',
						type: 'sap.ui.model.type.Date',
				        formatOptions: {
				          pattern: 'yyyy/MM/dd'
				        }
					}" />
					<Text text="{order>Ponumber} {order>Paymentterm}"/>

					<ObjectIdentifier />
					<Text text="{order>Track}"/>


				</cells>
			</ColumnListItem>
		</items>
	</Table>
	</Page>
</mvc:View>

```
Result :

![SalesOrder](/doc/SAPUI5/SalesOrder.PNG)



### SalesList.controller.js

```
sap.ui.define([
	"sap/ui/core/mvc/Controller",
	"../model/formatter",
	"sap/ui/model/Filter",
	"sap/ui/model/FilterOperator"
], function (Controller, formatter) {
	"use strict";

	return Controller.extend("com.myorg.SAPUI5PWA.controller.SalesList", {
		formatter: formatter,

		onInit() {
			const router = sap.ui.core.UIComponent.getRouterFor(this);
			router.getRoute('RouteSalesOrders').attachMatched(this._handleRouteMatched, this);
		},


		_handleRouteMatched(event) {
			const { cnum } = event.getParameter("arguments");
			const view = this.getView();

			const oFilter = new sap.ui.model.Filter({
				path: "soldTo",
				operator: sap.ui.model.FilterOperator.EQ,
				value1: cnum,
			});

			const oModelSales = this.getView().getModel("salesOrder");
			const oModelOrders = view.getModel("orderTracking");

			oModelSales.read("/salesOrderHeaders", {
				filters: [oFilter],
				success: function (oData) {
					const { results: resultsSales } = oData;

					const aPromises = resultsSales.map((x) => {
						return new Promise((resolve, reject) => {
							oModelOrders.read(`/Orders('${x.salesOrderID}')`, {
								success: (res) => {
									resolve(res);
								},
							});
						});
					});
					// Biding in the data
					Promise.all(aPromises)
						.then(function (resultsOrders) {
							const results = resultsSales.map((resultSales, index) => ({...resultSales, ...resultsOrders[index]}));
							const JSONModel = new sap.ui.model.json.JSONModel(results);
							view.setModel(JSONModel, "order");
						});
				},
				urlParameters: "$expand=to_salesOrderItem",
			});
		},


		// Search function in the list of customer orders

		onFilterSalesOrder: function (oEvent) {

			// build filter array
			const aFilters = [];
			const query = oEvent.getParameter("query");
			if (query) {
				const propsToFilter = ["salesOrderID", "Status", "soldTo", "customerPONumber"];
				const filters = propsToFilter.map(prop => new sap.ui.model.Filter(prop, sap.ui.model.FilterOperator.Contains, query));
				
				aFilters.push(new sap.ui.model.Filter({
					filters,
					and: false,
				}));
			}

			// filter binding
			const oList = this.getView().byId("SalesList");
			const oBinding = oList.getBinding("items");
			oBinding.filter(aFilters);
		},

		// Navigation go back on the login page
		
		onNavBack: function (oEvent) {
			var oRouter = sap.ui.core.UIComponent.getRouterFor(this);
			oRouter.navTo("RouteLogin");
		}
	});
});
```

## Adding PWA
---

To make an SAPUI5 web app work offline by using the workbox toolkit to convert it to a progressive web app (PWA) see on this 
[website.](https://a.kabachnik.info/pwa-with-sap-openui5.html)

> **Note :**  the project part is necessary if you have a multi project setup

### But what does it do ?

You can start building responsive UI5 PWA applications right now. PWA is not a special framework but a combination of existing Web technologies: Web App Manifest, responsive Web Apps, and service workers for offline persistence. Google Chrome combines all the three and integrates them into Android OS as a single concept. PWA is framework independent and works practically with any modern Web UI framework including OpenUI5.

In general, making a responsive Web application to a PWA is simple:

- Specify meta tags in the HTML header.
- Create and link a Web App Manifest.
- Create icons and a splash screen.
- Add a service worker.
- Check if it works.

![pwasteps](/doc/SAPUI5/PWASteps.PNG)

> **Note :** More information about [PWA Here.](https://blogs.sap.com/2017/08/30/ui5ers-buzz-13-build-a-progressive-web-app-with-openui5/)

---

You have finish your PWA application ! You have a fonctionnal login page and a searcheable list of sales documents.

The code is available [here](/img/sapui5pwa.zip).