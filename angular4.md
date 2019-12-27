# Angular

## building project 
- In __angular.json__ under configurations add production and UAT section as shown below.
    ```json
    "configurations": {
            "production": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.PROD.ts"
                }
              ],
              "optimization": true,
              "outputHashing": "all",
              "sourceMap": false,
              "extractCss": true,
              "namedChunks": false,
              "aot": true,
              "extractLicenses": true,
              "vendorChunk": false,
              "buildOptimizer": true,
              "budgets": [
                {
                  "type": "initial",
                  "maximumWarning": "2mb",
                  "maximumError": "5mb"
                }
              ]
            },
            "UAT": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.UAT.ts"
                }
              ],
              "optimization": true,
              "outputHashing": "all",
              "sourceMap": false,
              "extractCss": true,
              "namedChunks": false,
              "aot": true,
              "extractLicenses": true,
              "vendorChunk": false,
              "buildOptimizer": true,
              "budgets": [
                {
                  "type": "initial",
                  "maximumWarning": "2mb",
                  "maximumError": "5mb"
                }
              ]
            }
          }
        }
    ```
- build production
  build production of the app by running the following command(more [info](https://angular.io/cli/build)).
  ```bash
  ng build --prod --configuration="production" --output-path ./dist_prod
  ```  
- build UAT
  build UAT of the app by running the following command(more [info](https://angular.io/cli/build)).
  ```bash
  ng build --prod --configuration="UAT" --output-path ./dist_UAT
  ```  
file contents of environment.PROD.ts
> export const environment = {
> production: true,
>  apiUrl: 'http://wd001PROD:5015/api/DataAccess'
> };

file contents of environment.UAT.ts
> export const environment = {
> production: true,
>  apiUrl: 'http://wd001UAT:5015/api/DataAccess'
> };

# Angular Environment [ref](https://www.jvandemo.com/how-to-use-environment-variables-to-configure-your-angular-application-without-a-rebuild/)

1. Create ```env.js``` in src/app with the following contents:
```js
(function (window) {
    window.__env = window.__env || {};  
    window.__env.apiUrl = 'https://localhost:44396/api/TelecomReportAPI';    
  }(this));  
```
2. Next, we add a ```<script>``` element to the ```<head>``` section in our ```index.html``` to load ```env.js``` before Angular is loaded:
```html
<html ng-app="app">

  <head>
    <!-- Load environment variables -->
    <script src="env.js"></script>
  </head>

  <body>
    ...
    <!-- Angular code is loaded here -->
  </body>  

</html> 
```
3. To make sure that the file is copied to the output directory when we run ```ng build``` or ```ng serve```, we must add it to the assets section of our application's build configuration in ```angular.json```:
```js
{
  "projects": {
    "app-name": {
      "architect": {
        "build": {
          "options": {
            "assets": [
              "src/favicon.ico",
              "src/assets",
              "src/env.js"
            ]
          }
          "configurations": {
            "production": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.prod.ts"
                }
              ],
              // ...
            }
          }
        }
      }
    }
  }
}
```
4. Create service ```env.service.ts```  file for our ```EnvService``` class and replace its contents with:
```ts
export class EnvService {
  // The values that are defined here are the default values that can
  // be overridden by env.js

  // API url
  public apiUrl = '';

  // Whether or not to enable debug mode
  public enableDebug = true;

  constructor() {
  }
}
```

5. manually create ```env.service.provider.ts``` in the same directory and add the following:

```ts
import { EnvService } from './env.service';

export const EnvServiceFactory = () => {  
  // Create env
  const env = new EnvService();

  // Read environment variables from browser window
  const browserWindow = window || {};
  const browserWindowEnv = browserWindow['__env'] || {};

  // Assign environment variables from browser window to env
  // In the current implementation, properties from env.js overwrite defaults from the EnvService.
  // If needed, a deep merge can be performed here to merge properties instead of overwriting them.
  for (const key in browserWindowEnv) {
    if (browserWindowEnv.hasOwnProperty(key)) {
      env[key] = window['__env'][key];
    }
  }

  return env;
};

export const EnvServiceProvider = {  
  provide: EnvService,
  useFactory: EnvServiceFactory,
  deps: [],
};
```
6. register ```EnvServiceProvider``` in the application's `providers` array:

```ts
// ...
import { NgModule } from '@angular/core';  
import { EnvServiceProvider } from './env.service.provider';

@NgModule({
  imports: [ // ... ],
  providers: [EnvServiceProvider],
})
export class AppModule {}  
```

7. environment variables are now accessible from anywhere by injecting the `EnvService`:

```ts
import { EnvService } from '../env.service';
```
