# Server-side application
---

## Generate your Angular project


To create a new Angular project, navigate to the desired folder from a command line and enter the following command :

```ng
ng new angularpwa
```

Get inside the project directory

```cd
cd angularpwa
```
```ng 
ng serve --open
```

You will see the information of the server that starts at `localhost: 4200` and your browser will automatically launch with the message "Welcome to app !!" and the Angular logo.

## Adding Angular Material Design UI Library

Adding a Material design library in Angular is very easy, It can be done by using just a single command. Run the following command from your terminal.

```cd
ng add @angular/material
```

Create a folder `helpers`.   

Get inside this folder and create :  

- `auth.guard.ts` file,   

- `basic-auth.interceptor.ts` file,
- `error.interceptor.ts` file,   

- `fake-backend.ts` file.

![helpers](/doc/Angular/helpers.PNG)


### Auth Guard
---
The auth guard is an angular route guard that's used to prevent unauthenticated customers from accessing restricted routes, it does this by implementing the CanActivate interface which allows the guard to decide if a route can be activated with the canActivate() method.  

If the method returns true the route is activated (allowed to proceed), otherwise if the method returns false the route is blocked.

The auth guard uses the authentication service to check if the customer is logged in, if they are logged in it returns true from the canActivate() method, otherwise it returns false and redirects the customer to the login page.  

Angular route guards are attached to routes in the router config, this auth guard is used in app.routing.ts to protect the home page route.

```
import { Injectable } from '@angular/core';
import { Router, CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';

import { AuthenticationService } from '@app/_services';

@Injectable({ providedIn: 'root' })
export class AuthGuard implements CanActivate {
    constructor(
        private router: Router,
        private authenticationService: AuthenticationService
    ) { }

    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
        const currentcustomer = this.authenticationService.currentcustomerValue;
        if (currentcustomer) {
            // logged in so return true
            return true;
        }

        // not logged in so redirect to login page with the return url
        this.router.navigate(['/login'], { queryParams: { returnUrl: state.url } });
        return false;
    }
}
```
### Basic Authentication Interceptor
---
The Basic Authentication Interceptor intercepts http requests from the application to add basic authentication credentials to the Authorization header if the customer is logged in.

It's implemented using the `HttpInterceptor` class included in the `HttpClientModule`, by extending the `HttpInterceptor` class you can create a custom interceptor to modify http requests before they get sent to the server.

`Http interceptors` are added to the request pipeline in the providers section of the app.module.ts file.

```
import { Injectable } from '@angular/core';
import { HttpRequest, HttpHandler, HttpEvent, HttpInterceptor } from '@angular/common/http';
import { Observable } from 'rxjs';

import { AuthenticationService } from '@app/_services';

@Injectable()
export class BasicAuthInterceptor implements HttpInterceptor {
    constructor(private authenticationService: AuthenticationService) { }

    intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        // add authorization header with basic auth credentials if available
        const currentcustomer = this.authenticationService.currentcustomerValue;
        if (currentcustomer && currentcustomer.authdata) {
            request = request.clone({
                setHeaders: { 
                    Authorization: `Basic ${currentcustomer.authdata}`
                }
            });
        }

        return next.handle(request);
    }
}
```

### Http Error Interceptor
---
The Error Interceptor intercepts http responses from the api to check if there were any errors.   

If there is a `401 Unauthorized` response the customer is automatically logged out of the application, all other errors are re-thrown up to the calling service so an alert with the error can be displayed on the screen.

It's implemented using the `HttpInterceptor` class included in the `HttpClientModule`, by extending the `HttpInterceptor` class you can create a custom interceptor to catch all error responses from the server in a single location.

`Http interceptors` are added to the request pipeline in the providers section of the `app.module.ts` file.

``` import 
import { Injectable } from '@angular/core';
import { HttpRequest, HttpHandler, HttpEvent, HttpInterceptor } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

import { AuthenticationService } from '@app/_services';

@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
    constructor(private authenticationService: AuthenticationService) { }

    intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        return next.handle(request).pipe(catchError(err => {
            if (err.status === 401) {
                // auto logout if 401 response returned from api
                this.authenticationService.logout();
                location.reload(true);
            }

            const error = err.error.message || err.statusText;
            return throwError(error);
        }))
    }
}
```
### Fake Backend Provider
---  
  
In order to run and test the Angular application without a real backend API, the example uses a fake backend that intercepts the `HTTP requests` from the Angular app and send back "fake" responses. 
  

> **Note :** This is done by a class that implements the Angular HttpInterceptor interface, for more information on Angular HTTP Interceptors see [this Article](https://angular.io/api/common/http/HttpInterceptor).

The fake backend contains a handleRoute function that checks if the request matches one of the faked routes in the switch statement, at the moment this includes `POST` requests to the `/customers/authenticate` route for handling authentication, and `GET` requests to the `/customers` route for getting all customers.

Requests to the authenticate route are handled by the `authenticate()` function which checks the customername and number against an array of hardcoded customers.   

If the number and number are correct then an ok response is returned with the customer details, otherwise an error response is returned.

Requests to the get customers route are handled by the `getcustomers()` function which checks if the customer is logged in by calling the new `isLoggedIn()` helper function.  

 If the customer is logged in an `ok()` response with the whole customers array is returned, otherwise a 401 Unauthorized response is returned by calling the new `unauthorized()` helper function.

If the request doesn't match any of the faked routes it is passed through as a real HTTP request to the backend API.

```import
import { Injectable } from '@angular/core';
import { HttpRequest, HttpResponse, HttpHandler, HttpEvent, HttpInterceptor, HTTP_INTERCEPTORS } from '@angular/common/http';
import { Observable, of, throwError } from 'rxjs';
import { delay, mergeMap, materialize, dematerialize } from 'rxjs/operators';

import { customer } from '@app/_models';

const customers: customer[] = [{ id: 1, number: '0001007260', order: '0100010237' }];

@Injectable()
export class FakeBackendInterceptor implements HttpInterceptor {
    intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        const { url, method, headers, body } = request;

        // wrap in delayed observable to simulate server api call
        return of(null)
            .pipe(mergeMap(handleRoute))
            .pipe(materialize()) // call materialize and dematerialize to ensure delay even if an error is thrown (https://github.com/Reactive-Extensions/RxJS/issues/648)
            .pipe(delay(500))
            .pipe(dematerialize());

        function handleRoute() {
            switch (true) {
                case url.endsWith('/customers/authenticate') && method === 'POST':
                    return authenticate();
                case url.endsWith('/customers') && method === 'GET':
                    return getcustomers();
                default:
                    // pass through any requests not handled above
                    return next.handle(request);
            }    
        }

        // route functions

        function authenticate() {
            const { number, order } = body;
            const customer = customers.find(x => x.number === number && x.number === number);
            if (!customer) return error('Customer order or customer number is incorrect');
            return ok({
                id: customer.id,
                number: customer.number,
                firstName: customer.firstName,
                lastName: customer.lastName
            })
        }

        function getcustomers() {
            if (!isLoggedIn()) return unauthorized();
            return ok(customers);
        }

        // helper functions

        function ok(body?) {
            return of(new HttpResponse({ status: 200, body }))
        }

        function error(message) {
            return throwError({ error: { message } });
        }

        function unauthorized() {
            return throwError({ status: 401, error: { message: 'Unauthorised' } });
        }

        function isLoggedIn() {
            return headers.get('Authorization') === `Basic ${window.btoa('0001007260:0100010237')}`;
        }
    }
}

export let fakeBackendProvider = {
    // use fake backend in place of Http service for backend-less development
    provide: HTTP_INTERCEPTORS,
    useClass: FakeBackendInterceptor,
    multi: true
};
```
### Authentication Service
---
The authentication service is used to login & logout of the Angular app, it notifies other components when the customer logs in & out, and allows access the currently logged in customer.

RxJS Subjects and Observables are used to store the current customer object and notify other components when the customer logs in and out of the app. 

The RxJS BehaviorSubject is a special type of Subject that keeps hold of the current value and emits it to any new subscribers as soon as they subscribe, while regular Subjects don't store the current value and only emit values that are published after a subscription is created. 

> **Note :** For more info on communicating between components with RxJS Observables see [this post](https://jasonwatmore.com/post/2019/06/21/angular-8-communicating-between-components-with-observable-subject).

The `login()` method sends the customer credentials to the API via an HTTP POST request for authentication. If successful the customer's basic authentication data (base64 encoded order and number) is added to the customer object and stored in localStorage to keep the customer logged in between page refreshes.   

The customer object is then published to all subscribers with the call to `this.currentcustomerSubject.next(customer);`.

The `constructor()` of the service initialises the `currentcustomerSubject` with the `currentcustomer` object from localStorage which enables the customer to stay logged in between page refreshes or after the browser is closed.   

The public currentcustomer property is then set to this `currentcustomerSubject.asObservable();` which allows other components to subscribe to the currentcustomer Observable but doesn't allow them to publish to the `currentcustomerSubject`, this is so logging in and out of the app can only be done via the authentication service.

The `currentcustomerValue` getter allows other components an easy way to get the value of the currently logged in customer without having to subscribe to the currentcustomer Observable.

The `logout()` method removes the current customer object from local storage and publishes null to the `currentcustomerSubject` to notify all subscribers that the customer has logged out.

```import 
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { BehaviorSubject, Observable } from 'rxjs';
import { map } from 'rxjs/operators';

import { environment } from '@environments/environment';
import { customer } from '@app/_models';

@Injectable({ providedIn: 'root' })
export class AuthenticationService {
    private currentcustomerSubject: BehaviorSubject<customer>;
    public currentcustomer: Observable<customer>;

    constructor(private http: HttpClient) {
        this.currentcustomerSubject = new BehaviorSubject<customer>(JSON.parse(localStorage.getItem('currentcustomer')));
        this.currentcustomer = this.currentcustomerSubject.asObservable();
    }

    public get currentcustomerValue(): customer {
        return this.currentcustomerSubject.value;
    }

    login(order: string, number: string) {
        return this.http.post<any>(`${environment.apiUrl}/customers/authenticate`, { order, number })
            .pipe(map(customer => {
                // store customer details and basic auth credentials in local storage to keep customer logged in between page refreshes
                customer.authdata = window.btoa(order + ':' + number);
                localStorage.setItem('currentcustomer', JSON.stringify(customer));
                this.currentcustomerSubject.next(customer);
                return customer;
            }));
    }

    logout() {storage to log customer out
        localStorage.removeItem('currentcustomer');
        this.currentcustomerSubject.next(null);
    }
}
```