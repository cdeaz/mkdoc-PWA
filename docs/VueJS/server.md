# Server-side application
---

## Generate your Angular project


```ng
ng new angularpwa
```

Get inside the project directory

```cd
cd angularpwa
```

## Adding Angular Material Design UI Library

Adding a Material design library in Angular is very easy, It can be done by using just a single command. Run the following command from your terminal.

```cd
ng add @angular/material
```

Create a folder `helpers`. Get inside this folder and create `auth.guard.ts` file, `basic-auth.interceptor.ts` file,  `error.interceptor.ts` file and `fake-backend.ts` file.

![helpers](/img/helpers.PNG)


### Auth Guard

The auth guard is an angular route guard that's used to prevent unauthenticated users from accessing restricted routes, it does this by implementing the CanActivate interface which allows the guard to decide if a route can be activated with the canActivate() method. 
If the method returns true the route is activated (allowed to proceed), otherwise if the method returns false the route is blocked.

The auth guard uses the authentication service to check if the user is logged in, if they are logged in it returns true from the canActivate() method, otherwise it returns false and redirects the user to the login page.
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
        const currentUser = this.authenticationService.currentUserValue;
        if (currentUser) {
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

The Basic Authentication Interceptor intercepts http requests from the application to add basic authentication credentials to the Authorization header if the user is logged in.

It's implemented using the HttpInterceptor class included in the HttpClientModule, by extending the HttpInterceptor class you can create a custom interceptor to modify http requests before they get sent to the server.

Http interceptors are added to the request pipeline in the providers section of the app.module.ts file.

import { Injectable } from '@angular/core';
import { HttpRequest, HttpHandler, HttpEvent, HttpInterceptor } from '@angular/common/http';
import { Observable } from 'rxjs';

import { AuthenticationService } from '@app/_services';

@Injectable()
export class BasicAuthInterceptor implements HttpInterceptor {
    constructor(private authenticationService: AuthenticationService) { }

    intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        // add authorization header with basic auth credentials if available
        const currentUser = this.authenticationService.currentUserValue;
        if (currentUser && currentUser.authdata) {
            request = request.clone({
                setHeaders: { 
                    Authorization: `Basic ${currentUser.authdata}`
                }
            });
        }

        return next.handle(request);
    }
}

### Http Error Interceptor

The Error Interceptor intercepts http responses from the api to check if there were any errors. If there is a 401 Unauthorized response the user is automatically logged out of the application, all other errors are re-thrown up to the calling service so an alert with the error can be displayed on the screen.

It's implemented using the HttpInterceptor class included in the HttpClientModule, by extending the HttpInterceptor class you can create a custom interceptor to catch all error responses from the server in a single location.

Http interceptors are added to the request pipeline in the providers section of the app.module.ts file.

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

In order to run and test the Angular application without a real backend API, the example uses a fake backend that intercepts the HTTP requests from the Angular app and send back "fake" responses. 

> **Note :** This is done by a class that implements the Angular HttpInterceptor interface, for more information on Angular HTTP Interceptors see [this Article](https://angular.io/api/common/http/HttpInterceptor).

The fake backend contains a handleRoute function that checks if the request matches one of the faked routes in the switch statement, at the moment this includes `POST` requests to the `/users/authenticate` route for handling authentication, and `GET` requests to the `/users` route for getting all users.

Requests to the authenticate route are handled by the `authenticate()` function which checks the username and password against an array of hardcoded users. If the username and password are correct then an ok response is returned with the user details, otherwise an error response is returned.

Requests to the get users route are handled by the `getUsers()` function which checks if the user is logged in by calling the new `isLoggedIn()` helper function. If the user is logged in an `ok()` response with the whole users array is returned, otherwise a 401 Unauthorized response is returned by calling the new `unauthorized()` helper function.

If the request doesn't match any of the faked routes it is passed through as a real HTTP request to the backend API.

```import
import { Injectable } from '@angular/core';
import { HttpRequest, HttpResponse, HttpHandler, HttpEvent, HttpInterceptor, HTTP_INTERCEPTORS } from '@angular/common/http';
import { Observable, of, throwError } from 'rxjs';
import { delay, mergeMap, materialize, dematerialize } from 'rxjs/operators';

import { User } from '@app/_models';

const users: User[] = [{ id: 1, order: '0001008355', password: 'test', firstName: 'Test', lastName: 'User' }];

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
                case url.endsWith('/users/authenticate') && method === 'POST':
                    return authenticate();
                case url.endsWith('/users') && method === 'GET':
                    return getUsers();
                default:
                    // pass through any requests not handled above
                    return next.handle(request);
            }    
        }

        // route functions

        function authenticate() {
            const { username, password } = body;
            const user = users.find(x => x.username === username && x.password === password);
            if (!user) return error('Username or password is incorrect');
            return ok({
                id: user.id,
                username: user.username,
                firstName: user.firstName,
                lastName: user.lastName
            })
        }

        function getUsers() {
            if (!isLoggedIn()) return unauthorized();
            return ok(users);
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
            return headers.get('Authorization') === `Basic ${window.btoa('0001008355:test')}`;
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

The authentication service is used to login & logout of the Angular app, it notifies other components when the user logs in & out, and allows access the currently logged in user.

RxJS Subjects and Observables are used to store the current user object and notify other components when the user logs in and out of the app. 

The RxJS BehaviorSubject is a special type of Subject that keeps hold of the current value and emits it to any new subscribers as soon as they subscribe, while regular Subjects don't store the current value and only emit values that are published after a subscription is created. 

> **Note :** For more info on communicating between components with RxJS Observables see [this post](https://jasonwatmore.com/post/2019/06/21/angular-8-communicating-between-components-with-observable-subject).

The `login()` method sends the user credentials to the API via an HTTP POST request for authentication. If successful the user's basic authentication data (base64 encoded order and password) is added to the user object and stored in localStorage to keep the user logged in between page refreshes. The user object is then published to all subscribers with the call to this.currentUserSubject.next(user);.

The `constructor()` of the service initialises the currentUserSubject with the currentUser object from localStorage which enables the user to stay logged in between page refreshes or after the browser is closed. The public currentUser property is then set to `this.currentUserSubject.asObservable();` which allows other components to subscribe to the currentUser Observable but doesn't allow them to publish to the currentUserSubject, this is so logging in and out of the app can only be done via the authentication service.

The `currentUserValue` getter allows other components an easy way to get the value of the currently logged in user without having to subscribe to the currentUser Observable.

The `logout()` method removes the current user object from local storage and publishes null to the `currentUserSubject` to notify all subscribers that the user has logged out.



```import 
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { BehaviorSubject, Observable } from 'rxjs';
import { map } from 'rxjs/operators';

import { environment } from '@environments/environment';
import { User } from '@app/_models';

@Injectable({ providedIn: 'root' })
export class AuthenticationService {
    private currentUserSubject: BehaviorSubject<User>;
    public currentUser: Observable<User>;

    constructor(private http: HttpClient) {
        this.currentUserSubject = new BehaviorSubject<User>(JSON.parse(localStorage.getItem('currentUser')));
        this.currentUser = this.currentUserSubject.asObservable();
    }

    public get currentUserValue(): User {
        return this.currentUserSubject.value;
    }

    login(order: string, password: string) {
        return this.http.post<any>(`${environment.apiUrl}/users/authenticate`, { order, password })
            .pipe(map(user => {
                // store user details and basic auth credentials in local storage to keep user logged in between page refreshes
                user.authdata = window.btoa(order + ':' + password);
                localStorage.setItem('currentUser', JSON.stringify(user));
                this.currentUserSubject.next(user);
                return user;
            }));
    }

    logout() {
        // remove user from local storage to log user out
        localStorage.removeItem('currentUser');
        this.currentUserSubject.next(null);
    }
}
```