# Start the project 
---

Now you have finish the part of the server-side of your Application.

We will start to create a login page and a home page who will contains a list of sales documents of a customer order.

## Create a Login Page 
---

First of all, your file `App.vue` will contains this :

##### App.vue

```
<template>
  <div id="app">
    <div class="m-flexContainer">
      <div class="m-formContainer">
        <LoginForm :title="headerText" />
      </div>
    </div>
    <div id="nav">
            <router-link 
            v-if="authenticated" 
            to="/LoginForm" 
            v-on:click.native="logout()" 
            replace>Logout
            </router-link>
        </div>
    <router-view @authenticated="setAuthenticated" />
  </div>
</template>

<script>
import LoginForm from './components/LoginForm.vue';

export default {
  name: 'app',
  data() {
    return {
      headerText: 'Authentification',
      authenticated: false,
      mockAccount: {
          cnum: "test",
          cord: "test"
      }
    }
  },
 mounted() {
     if(!this.authenticated) {
         this.$router.replace('/LoginForm');
     }
 },
methods: {
    setAuthenticated(status) {
        this.authenticated = status;
    },
    logout() {
        this.authenticated = false;
    },
},
components: {
    LoginForm,
  },
};
</script>

<style lang="scss">
/* GLOBAL SCSS IMPORT */
@import './scss/main.scss';
</style>

``` 

Then create a file `main.js` and `config.js` who will contains this :

##### main.js

```
import Vue from 'vue';
import App from './App.vue';
import VueRouter from 'vue-router';

import LoginForm from './components/LoginForm.vue';
import SalesList from './SalesList.vue';

import axios from 'axios';
import VueMaterial from 'vue-material';
import 'vue-material/dist/vue-material.min.css';
import 'vue-material/dist/theme/default.css';

Vue.use(VueMaterial);
Vue.use(VueRouter);

Vue.prototype.$axios = axios;

Vue.config.productionTip = false;


const routes = [
  
    {
      path: '/login',
      name: 'login',
      component: LoginForm,
    },
    {
      path: '/saleslist',
      name: 'saleslist',
      component: SalesList,
    },
  ];
  const router = new VueRouter({
    routes, // raccourci pour `routes: routes`
  });

  
new Vue({
    render: (h) => h(App),
    router,
  }).$mount('#app');
  

```

##### config.js
```
System.config({
    transpiler: 'plugin-babel',
    meta: {
      '*.vue': {
        loader: 'vue-loader'
      },
    },
    paths: {
      'npm:': 'https://unpkg.com/'
    },
    map: {
      'vue': 'npm:vue@2.6.3/dist/vue.esm.browser.js',
      'vue-loader': 'npm:dx-systemjs-vue-browser@latest/index.js',
      'devextreme': 'npm:devextreme@20.1',
      'devextreme-vue': 'npm:devextreme-vue@20.1',
      'jszip': 'npm:jszip@3.1.3/dist/jszip.min.js',
      'quill': 'npm:quill@1.3.7/dist/quill.js',
      'devexpress-diagram': 'npm:devexpress-diagram@1.0.9',
      'devexpress-gantt': 'npm:devexpress-gantt@1.0.7',
      'plugin-babel': 'npm:systemjs-plugin-babel@0/plugin-babel.js',
      'systemjs-babel-build': 'npm:systemjs-plugin-babel@0/systemjs-babel-browser.js'
    },
    packages: {
      'devextreme-vue': {
        main: 'index.js'
      },
      'devextreme': {
        defaultExtension: 'js'
      },
      'devextreme/events/utils': {
        main: 'index'
      },
      'devextreme/events': {
          main: 'index'
      },
    },
    babelOptions: {
      sourceMaps: false,
      stage0: true
    }
  });
  
```

After that in your terminal, do a :
`npm install`

#### LoginForm.vue
---

Create First a file `LoginForm.vue` into your project, that will contain our authentication form.

```
<template>
  <form class="m-loginForm">
    <Header :title="title" />

    <div class="m-loginForm__group">
      <animated-input placeholder="Customer Number" animateBorder v-model="input.cnum"/>
    </div>
    <div class="m-loginForm__group">
      <animated-input placeholder="Customer Order" animateBorder inputType="password" v-model="input.cord"/>
    </div>
    
    <div class="m-loginForm__group">
      <router-link v-on:click="onloginTap()" to="/saleslist">
            <submit-button >Log In</submit-button>
      </router-link> 
    </div>

  </form>
</template>

<script>
import Header from "./inputs/Header.vue";
import AnimatedInput from "./inputs/AnimatedInput.vue";
import SubmitButton from "./buttons/SubmitButton.vue";

export default {
  name: 'LoginForm',
  data() {
    return {
      login: { cnum: '', cord: ''},
      input: {
        cnum: '',
        cord: '',
      }
    };
  },
  methods: {
    onLoginTap(){ 
       if(this.input.cnum != '' && this.input.cord != '') {
              if(this.login.input == this.$parent.mockAccount.cnum && this.input.cord == this.$parent.mockAccount.cord) {
                this.$emit("authenticated", true);
                this.$router.push('saleslist');
            } else {
                console.log("The customer order or number is incorrect");
            }
            } else {
                console.log("A customer order or number must be present");
            }
          
    }
  },
  components: {
    Header,
    AnimatedInput,
    SubmitButton
  },
  props: {
    title: String
  }
};
</script>

<style lang="scss" scoped>
@import "@/scss/components/forms/_loginForm.scss";
</style>

``` 

We will create a button and inputs, so we can call them into our file `LoginForm.vue`.

Into the folder `components` :

#### Folder Buttons
---
- Create a folder `buttons`,  `components`. Inside create  `SubmitButtons.vue` files. 


##### SubmitButtons.vue
--- 

```
<template>
  <button type="submit" class="a-submitButton">
    <slot></slot>
  </button>
</template>

<style lang="scss" scoped>
@import "@/scss/components/buttons/_submitButton.scss";

</style>

```


#### Folder Inputs
---
- Create a other folder `inputs` , into it create `AnimatedInput.vue`, `Header.vue` and `CustomCheckBox.vue` files.


##### AnimatedInput.vue
---
```
<template>
  <button type="submit" class="a-submitButton">
    <slot></slot>
  </button>
</template>

<style lang="scss" scoped>
@import "@/scss/components/buttons/_submitButton.scss";

</style>
```

##### Header.vue
---
```
<template>
  <h1 class="a-header">
    {{ title }}
  </h1>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      required: true
    }
  }
};
</script>

<style lang="scss" scoped>
@import "@/scss/components/inputs/_header.scss";

</style>
```

#### CustomCheckBox.vue
---
```
<template>
  <div class="a-customCheckbox">
    <input
      class="a-customCheckbox__confirm"
      type="checkbox"
      :checked="checked"
      :name="name"
      :id="id"
    />

    <label
      class="a-customCheckbox__confirmLabel"
      :data-content="checkbox"
      :for="id"
    >
      <span class="a-customCheckbox__labelText">
        {{ labelText }}
      </span>
    </label>
  </div>
</template>

<script>
export default {
  props: {
    name: {
      type: String,
      required: true
    },
    id: {
      type: String,
      required: true
    },
    labelText: {
      type: String,
      required: true
    },
    checkbox: {
      type: String,
      required: false
      // \2713
      // \f00c
    },
    checked: {
      type: Boolean,
      required: false,
      default: false
    }
  }
};
</script>

<style lang="scss" scoped>
@import "@/scss/components/inputs/_customCheckbox.scss";

</style>

```

### Design of our Login Page
---

We will do some design, some `SCSS`, create a folder name like that.

Then create all of this folder inside it : 

![scss](doc/VueJS/scss.PNG)

Here the folder scss with all of his folder and files, you juste have to copy into your project.

[scss files](doc/VueJS/scss.zip).

Result :

![login](/doc/VueJS/login.PNG)


## Create the list of sales order
---

Now, we will create our second page, it's a table with all the sales Orders of our costumer order and number choosen before in the login page.

Firstly, we will create a file `SalesList.vue`.

#### SalesList.vue
---

![saleslist](/doc/VueJS/salelist.PNG)

```
<template>
  <div id="app">
  
   <nav class="navbar navbar-light bg-light justify-content-between">
    <span class="go-back">
      <md-button class="md-fab md-mini md-primary" @click="goBack">
        <md-icon>chevron_left</md-icon>
      </md-Button>
    </span>
    <a class="navbar-brand">Sales Orders</a>
    <form class="form-inline">
      <input class="form-control mr-sm-2" type="search" placeholder="Search" aria-label="Search">
      <button class="btn btn btn-outline-primary my-2 my-sm-0" type="submit">Search</button>
    </form>
    </nav>
      <Table v-if="tableData" :theData="tableData" :config="config" :style="{height: '600px'}"/>
    </div>

</template>

<script>
import Table from './components/Table'

export default {
  name: 'SalesList',

  components: {
    Table
  },
  data: () => ({
    tableData: undefined,
    config: [
      {
        key: 'salesdoc',
        title: 'SalesDoc.',
        type: 'number'
      },
      {
        key: 'status',
        title: 'Status',
        type: 'text'
      },
      {
        key: 'city',
        title: 'Ship-to',
        type: 'text'
      },
      {
        key: 'companyName',
        title: 'Order received Date',
        type: 'number'
      },
      {
        key: 'createdAt',
        title: 'Sold-To party',
        type: 'number'
      },
      {
        key: 'createdAt',
        title: 'Shipped Date',
        type: 'text'
      },
      {
        key: 'createdAt',
        title: 'Material',
        type: 'text'
      },
      {
        key: 'createdAt',
        title: 'Quantities',
        type: 'number'
      },
      {
        key: 'createdAt',
        title: 'Tracking Number',
        type: 'number'
      }
    ]
  }),
  mounted () {
    this.$axios.get('https://5e4b062d6eafb7001488c99e.mockapi.io/something123/users')
    .then(({data}) => {
      this.tableData = data
    })
  },
  methods: {
    goBack() {
      return this.$router.go(-1);
    }
  }
}
</script>

<style>

body {
  font-family: Helvetica, sans-serif;
  font-weight: 400;
}

.table thead th {
    vertical-align: middle;
}

.md-theme-default a:not(.md-button) {
    color: #000;
    color: var(--md-theme-default-primary-on-background, #000);
}

</style>

```

#### Table.vue

Into the folder `components` create a file `Table.vue` it will displaying a list of sales document into a table.

```
<template>
    <section class="table table-striped">
       <div class="table-responsive"> 
        <table class="table table-striped"> 
            <thead>
                <tr>
                    <th v-for="(obj, ind) in config" :key="ind">
                        {{ obj.title }}
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(row, index) in theData" :key="index">
                    <td v-for="(obj, ind) in config" :key="ind">
                        <span v-if="obj.type === 'text'">{{row[obj.key]}}</span>
                        <span v-if="obj.type === 'date'">{{new Date(row[obj.key]).toLocaleDateString()}} </span>
                        <figure v-if="obj.type === 'image'">
                            <img :src="row[obj.key]" height="60px">
                        </figure>
                    </td>
                </tr>
            </tbody>
        </table>
       </div>
    </section>
</template>

<script>
export default {
    props: ['theData', 'config'],
}
</script>

<style lang="scss">

.btn-outline-success {
    color: #3b28a7;
    border-color: #3b28a7;
}

.table {
    width: 100%;
    margin-bottom: 1rem;
    color: #505050;
}

</style>
```

## Adding PWA
---
Run the following command to install everything that's needed

```ng
ng add @angular/pwa --project yourProject
```

> **Note :**  the project part is necessary if you have a multi project setup

But what does it do ?

- It adds serviceWorker: true in the production configuration.
 -It creates two files at the root of the project: manifest.json and ngsw-config.json.
- It adds the manifest.json that was just created in the registered assets of the project.
- It adds two lines in the index.html: A <meta name="theme-color"> tag (you'll want to change its value) and a <link> tag pointing to the manifest.json file.

> **Note :** if you already had these tags in your index, it will not replace them. You'll have to do it yourself.

- It imports the ServiceWorkerModule in your app (only in production). 

> **Note :** if you are using a base-href in production, you'll need to change the `/ngsw-worker.js` path to `./ngsw-worker.js` to prevent a 404 error.

- It adds icons in your assets folder. You will of course need to change them if you don't want your app to sport Angular logos as icons.

> **Note :** More information about [PWA Here.](https://medium.com/the-web-tub/creating-your-first-vue-js-pwa-project-22f7c552fb34)

---

You have finish your PWA application ! You have a fonctionnal login page and a searcheable list of sales documents.

The code is available [here](/img/angularpwa.zip).