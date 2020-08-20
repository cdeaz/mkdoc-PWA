# Star with VueJS

--- 



### Install the tools

You must install the following tools if you do not already have them on your machine:

## VueJS
---
Download and install the latest LTS version of VueJS here:

 [VueJS Download Website](https://vuejs.org/v2/guide/installation.html)

## NPM
---

NPM is a package manager that allows the installation of a lot of tools and libraries that you will need for any type of development. To install it, open a command line and type the following command:

```npm
npm install -g npm @latest
```

> **Note :** If the command fails for a permission problem on Mac or Linux, you can launch it in superuser mode with  sudo or consult the following documentation.

NPM is the recommended installation method when building large scale applications with Vue. It pairs nicely with module bundlers such as Webpack or Browserify. Vue also provides accompanying tools for authoring Single File Components.

```npm
npm install vue
```

## CLI
---
Vue provides an official CLI for quickly scaffolding ambitious Single Page Applications. It provides batteries-included build setups for a modern frontend workflow. It takes only a few minutes to get up and running with hot-reload, lint-on-save, and production-ready builds. See the [Vue CLI docs](https://cli.vuejs.org) for more details.

```npm
npm install -g @vue/cli

# OR

yarn global add @vue/cli
```


# Create your PWA

---

To create a new VueJS project, navigate to the desired folder from a command line and enter the following command :

```vue
vue create my-project
# OR
vue ui
```

Then navigate to the project folder and launch the development server :

```cd 
cd my-project
```
```ng 
npm run serve
```

  App running at:
  - Local:   http://localhost:8080/
  - Network: http://10.250.16.14:8080/

Result :

![app](/doc/VueJS/app.PNG)

---
#### Installing in an Already Created Project
---

```PWA

vue add pwa

```


##### Your development environment is now ready.

> **Note :** I advise you tu use an editor like VS Code or Atom. Also, use Chrome as your default browser for development, because the Developer Tools are very complete, and you also have the possibility of installing Augury, a specific Chrome plugin for Angular development.


# Install Mkdocs

---

Start the live-reloading docs server.
```mkdocs
mkdocs serve 
```

Build the documentation site.
```mkdocs
mkdocs build
```


```
##### Your development environment is now ready.

> **Note :** I advise you tu use an editor like VS Code or Atom. Also, use Chrome as your default browser for development, because the Developer Tools are very complete, and you also have the possibility of installing Augury, a specific Chrome plugin for Angular development.


