# Start the project 
---

Now you have finish the part of the server-side of your Application.

We will start to create a login page and a home page who will contains a list of sales documents of a customer order.

## Create a Login Page 
---
So now create a folder `models`. Inside create `customer.ts` file. 

![models](/doc/Angular/models.PNG)

#### Customer Model
---
The customer model is a small class that defines the properties of a customer.

```export 
export class Customer {
    id: number;
    order: string;
    password: string;
    token?: string;
}
```


Create a folder `services`. Inside create `authentication.service.ts` file and `customer.service.ts` file. 

 
#### Customer Service
---
The customer service contains a method for getting all customers from the api, I included it to demonstrate accessing a secure api endpoint with the http authorization header set after logging in to the application, the auth header is automatically set with basic authentication credentials by the basic authentication interceptor.   

The secure endpoint in the example is a fake one implemented in the fake backend provider.

```import
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

import { environment } from '@environments/environment';
import { Customer } from '@app/_models';

@Injectable({ providedIn: 'root' })
export class CustomerService {
    constructor(private http: HttpClient) { }

    getAll() {
        return this.http.get<Customer[]>(`${environment.apiUrl}/Customers`);
    }
}
```

Create now a login component.

![login](/doc/Angular/login1.PNG)

#### Login.component.html
---
The login component template contains a number of custom order form with cusand password fields. It displays validation messages for invalid fields when the submit button is clicked.   

The form submit event is bound to the `onSubmit()` method of the login component.

The component uses reactive form validation to validate the input fields.

``` 
<div class="col-md-6 offset-md-3 mt-5">
    <div class="alert alert-info">
         Costumer number : 0001007260 <br />
        Costumer order : 0100010237
    </div>
    <div class="card">
        <h4 class="card-header">Authentication</h4>
        <div class="card-body">
            <form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
                <div class="form-group">
                    <label for="number">Customer number</label>
                    <input type="text" formControlName="number" class="form-control" [ngClass]="{ 'is-invalid': submitted && f.number.errors }" />
                    <div *ngIf="submitted && f.number.errors" class="invalid-feedback">
                        <div *ngIf="f.order.errors.required">Customer number is required</div>
                    </div>
                </div>
                <div class="form-group">
                    <label for="order">Customer order</label>
                    <input type="order" formControlName="order" class="form-control" [ngClass]="{ 'is-invalid': submitted && f.order.errors }" />
                    <div *ngIf="submitted && f.order.errors" class="invalid-feedback">
                        <div *ngIf="f.order.errors.required">Customer order is required</div>
                    </div>
                </div>
                <button [disabled]="loading" class="btn btn-primary">
                    <span *ngIf="loading" class="spinner-border spinner-border-sm mr-1"></span>
                    Login
                </button>
                <div *ngIf="error" class="alert alert-danger mt-3 mb-0">{{error}}</div>
            </form>
        </div>
    </div>
</div>
```
 
#### Login.component.ts
---
The login component uses the authentication service to login to the application. If the customer is already logged in they are automatically redirected to the home page.

`The loginForm` : FormGroup object defines the form controls and validators, and is used to access data entered into the form. The FormGroup is part of the Angular Reactive Forms module and is bound to the login template above with the `[formGroup]="loginForm" directive`.

```import
import { Component, OnInit } from '@angular/core';
import { Router, ActivatedRoute } from '@angular/router';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { first } from 'rxjs/operators';

import { AuthenticationService } from '@app/_services';

@Component({ templateUrl: 'login.component.html' })
export class LoginComponent implements OnInit {
    loginForm: FormGroup;
    loading = false;
    submitted = false;
    returnUrl: string;
    error = '';

    constructor(
        private formBuilder: FormBuilder,
        private route: ActivatedRoute,
        private router: Router,
        private authenticationService: AuthenticationService
    ) { 
        // redirect to home if already logged in
        if (this.authenticationService.currentCustomerValue) { 
            this.router.navigate(['/']);
        }
    }

    ngOnInit() {
        this.loginForm = this.formBuilder.group({
            number: ['', Validators.required],
            order: ['', Validators.required]
        });

        // get return url from route parameters or default to '/'
        this.returnUrl = this.route.snapshot.queryParams['returnUrl'] || '/';
    }

    // convenience getter for easy access to form fields
    get f() { return this.loginForm.controls; }

    onSubmit() {
        this.submitted = true;

        // stop here if form is invalid
        if (this.loginForm.invalid) {
            return;
        }

        this.loading = true;
        this.authenticationService.login(this.f.number.value, this.f.order.value)
            .pipe(first())
            .subscribe(
                data => {
                    this.router.navigate([this.returnUrl]);
                },
                error => {
                    this.error = error;
                    this.loading = false;
                });
    }
}
```
Here the result :

![login](/doc/Angular/login.PNG)
 
#### App.component.html
---
The app component template is the root component template of the application, it contains the main nav bar which is only displayed for authenticated customers, and a router-outlet directive for displaying the contents of each view based on the current route / path.

```
<!-- nav -->
<nav class="navbar navbar-expand navbar-dark bg-dark" *ngIf="currentCustomer">
    <div class="navbar-nav">
        <a class="nav-item nav-link" routerLink="/">Home</a>
        <a class="nav-item nav-link" (click)="logout()">Logout</a>
    </div>
</nav>

<!-- main app container -->
<div class="container">
    <router-outlet></router-outlet>
</div>
``` 
 
#### App.component.ts
---
The app component is the root component of the application, it defines the root tag of the app as <app></app> with the selector property of the `@Component()` decorator.

It subscribes to the currentCustomer observable in the authentication service so it can reactively show/hide the main navigation bar when the customer logs `in/out` of the application.  
  
I didn't worry about unsubscribing from the observable here because it's the root component of the application, the only time the component will be destroyed is when the application is closed which would destroy any subscriptions as well.

The app component contains a `logout()` method which is called from the logout link in the main nav bar above to log the customer out and redirect them to the login page.

```
import { Component } from '@angular/core';
import { Router } from '@angular/router';

import { AuthenticationService } from './_services';
import { Customer } from './_models';

@Component({ selector: 'app', templateUrl: 'app.component.html' })
export class AppComponent {
    currentCustomer: Customer;

    constructor(
        private router: Router,
        private authenticationService: AuthenticationService
    ) {
        this.authenticationService.currentCustomer.subscribe(x => this.currentCustomer = x);
    }

    logout() {
        this.authenticationService.logout();
        this.router.navigate(['/login']);
    }
}
```
 
#### App.module.ts
---
The app module defines the root module of the application along with metadata about the module. For more info about Angular 8 modules check out this page on the official docs site.

This is where the fake backend provider is added to the application, to switch to a real backend simply remove the providers located below the comment // provider used to create fake backend.

```import
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { ReactiveFormsModule } from '@angular/forms';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';

// used to create fake backend
import { fakeBackendProvider } from './_helpers';

import { AppComponent } from './app.component';
import { appRoutingModule } from './app.routing';

import { BasicAuthInterceptor, ErrorInterceptor } from './_helpers';
import { HomeComponent } from './home';
import { LoginComponent } from './login';

@NgModule({
    imports: [
        BrowserModule,
        ReactiveFormsModule,
        HttpClientModule,
        appRoutingModule
    ],
    declarations: [
        AppComponent,
        HomeComponent,
        LoginComponent
    ],
    providers: [
        { provide: HTTP_INTERCEPTORS, useClass: BasicAuthInterceptor, multi: true },
        { provide: HTTP_INTERCEPTORS, useClass: ErrorInterceptor, multi: true },

        // provider used to create fake backend
        fakeBackendProvider
    ],
    bootstrap: [AppComponent]
})
export class AppModule { }
```

## Create the list of sales order
---

Firstly, we will create a home component and also into the folder `home`, a `home.service.ts` in the folder home.

![home](/doc/Angular/home.PNG)

```
export class Salesorder
{
    Id : number;
    OrderId : string;
    Status: string;
    Date: string;
    Ship: string;
    Purchase: string;
    Soldto: string;
    Shipdate: string;
    Material: string;
    Quantities: string;
    Tracknum: string;

}
```
#### Home.component.html
---
The home component template will contains html and angular 8 template syntax for displaying a list of sales document.

```
<div class="card mt-4">

    <h4 class="card-header">Customer order </h4>
    <input type="text" #myInput [(ngModel)]="OrderId" (input)="Search()" />

    <div class="card-body">
      
        <table class="table">
            <tr>
                <th>Sales document</th>
                <th>Status</th>
                <th>Order received date</th>
                <th>Ship-to</th>
                <th>Purchase order number</th>
                <th>Sold-to party</th>
                <th>Shipped date</th>
                <th>Material</th>
                <th>Quantities</th>
                <th>Tracking number</th>
            </tr>
            <tr *ngFor='let o of salesorders'>
                <td>{{o.OrderId}}</td>
                <td>{{o.Status}}</td>
                <td>{{o.Date}}</td>
                <td>{{o.Ship}}</td>
                <td>{{o.Purchase}}</td>
                <td>{{o.Soldto}}</td>
                <td>{{o.Shipdate}}</td>
                <td>{{o.Material}}</td>
                <td>{{o.Quantities}}</td>
                <td>{{o.Tracknum}}</td>
            </tr>
        </table>
        
    </div>

</div>
```
#### Home.component.ts
---
The home will show the list of sales document. We can also search the sales document we want.

```
import { Component } from '@angular/core';
import { first } from 'rxjs/operators';
import { salesorder } from '../home/home.service';

import { Customer } from '@app/_models';
import { CustomerService } from '@app/_services';

@Component({ templateUrl: 'home.component.html' })
export class HomeComponent {
    salesorders : salesorder[] = [];
    OrderId : string;
    loading = false;
    customers: Customer[];

    constructor(private customerService: CustomerService) { }

    ngOnInit() {
        this.salesorders = [
         
            
        ];
        this.loading = true;
        this.customerService.getAll().pipe(first()).subscribe(customers => {
            this.loading = false;
            this.customers = customers;

        });
    }

    Search(){
        if(this.OrderId !="")  {
            this.salesorders = this.salesorders.filter(res=>{
                return res.OrderId.toLocaleLowerCase().match(this.OrderId.toLocaleLowerCase());
            });
        }else if (this.OrderId == ""){
          this.ngOnInit();  
        }
       
    }
}
```

Into `this.salesorders = [];` put the data from [this file](/img/data.txt).

Here the final result :  

![salesorder](/doc/Angular/salesorders.PNG)

## Adding PWA
---
Run the following command to install everything that's needed

```ng
ng add @angular/pwa --project yourProject
```

> **Note :**  the project part is necessary if you have a multi project setup

But what does it do ?

- It adds serviceWorker: true in the production configuration.  

- It creates two files at the root of the project: manifest.json and ngsw-config.json.  

- It adds the manifest.json that was just created in the registered assets of the project.  

- It adds two lines in the index.html: A <meta name="theme-color"> tag (you'll want to change its value) and a <link> tag pointing to the manifest.json file.

> **Note :** if you already had these tags in your index, it will not replace them. You'll have to do it yourself.

- It imports the ServiceWorkerModule in your app (only in production). 

> **Note :** if you are using a base-href in production, you'll need to change the `/ngsw-worker.js` path to `./ngsw-worker.js` to prevent a 404 error.

- It adds icons in your assets folder. You will of course need to change them if you don't want your app to sport Angular logos as icons.

> **Note :** More information about [PWA Here.](https://medium.com/poka-techblog/turn-your-angular-app-into-a-pwa-in-4-easy-steps-543510a9b626)

---

You have finish your PWA application ! You have a fonctionnal login page and a searcheable list of sales documents.

The code is available [here](/doc/Angular/angularpwa.zip).

If you want to try it do `npm install` and `npm start`.