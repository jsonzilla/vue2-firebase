# Vue.js 2 + Firebase
Nova app do livro "Vue.js na prática"

## Instalação Inicial

Adicionar o `vue-cli`

```console
npm i -g vue-cli
```

Criar o projeto

```console
 vue init webpack blog
```

![](images/vue-init-webpack-blog.png)

```console
cd blog
npm install
npm run dev
```

Adicionar bibliotecas Vue

```console
npm i -S vuex vue-resource bulma firebase vuefire
```

Adicionar o arquivo css do Bulma no projeto

```javascript
// src/main.js
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
})

require('../node_modules/bulma/css/bulma.css')
```

Otimizar o router, para que o import seja carregado sozinho

```js
// src/router/index.js
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

function load (component) {
  return () => System.import(`../components/${component}.vue`)
}

export default new Router({
  routes: [
    {path: '/', name: 'Hello', component: load('Hello')},
    { path: '*', component: load('Error404') }
  ]
})
```
Criar o componente `Error404`

```html
<!-- src/components/Error404.vue -->
<template>
  <div>
    <h1>Not Found</h1>
  </div>
</template>

<script>
export default {

}
</script>
```

Alterar `App.vue` para o seguinte desing:

```html
<!-- src/App.vue -->
<template>
  <div id="app">
    <nav class="nav has-shadow" id="top">
      <div class="container">
        <div class="nav-left">
          <a class="nav-item" href="../index.html">
            <h2>Vue.js na prática</h2>
          </a>
        </div>
        <span class="nav-toggle">
        <span></span>
        <span></span>
        <span></span>
        </span>
        <div class="nav-right nav-menu">
          <a class="nav-item is-tab is-active">
          Home
        </a>
          <a class="nav-item is-tab">
          Posts
        </a>
          <a class="nav-item is-tab">
          Comments
        </a>
        </div>
      </div>
    </nav>
    <section class="section">
      <div class="container content">
        <router-view></router-view>
      </div>
    </section>

  </div>
</template>

<script>
  export default {
    name: 'app'
  }

</script>

<style>

</style>
```

Criar o arquivo `src/bradcomp.js`

```js
// src/bradcomp.js
// The following code is based off a toggle menu by @Bradcomp
// source: https://gist.github.com/Bradcomp/a9ef2ef322a8e8017443b626208999c1
(function () {
  var burger = document.querySelector('.nav-toggle')
  var menu = document.querySelector('.nav-menu')
  burger.addEventListener('click', function () {
    burger.classList.toggle('is-active')
    menu.classList.toggle('is-active')
  })
})()
```

Incluir o `bradcomp.js` no `main.js`:

```js
// src/main.js
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
})

require('../node_modules/bulma/css/bulma.css')
require('./bradcomp.js')
```

## Implementando o Vuex

Crie o diretório `src/store` e os arquivos:

```js
// src/store/actions.js

export const simpleAction = ({ commit }, text) => {
  commit('SimpleMutation', { text })
}

```

```js
// src/store/getters.js

export const getSimpleText = state => {
  return state.simpleText
}
```

```js
// src/store/index.js

import Vue from 'vue'
import Vuex from 'vuex'

import * as getters from './getters'
import * as actions from './actions'
import { state, mutations } from './mutations'

Vue.use(Vuex)

export default new Vuex.Store({
  state,
  getters,
  actions,
  mutations
})

```

```js
// src/store/mutations.js

/* STATE */
export const state = {
  simpleText: 'Hello From Vuex'
}

export const mutations = {

  SimpleMutation (state, { text }) {
    state.simpleText = text
  }

}

```

Seguindo o fluxo vuex:

![](https://raw.githubusercontent.com/vuejs/vuex/dev/docs/en/images/vuex.png)

Temos:

```html
<template>
  <div>
    {{$store.getters.getSimpleText}}
    <button @click="changeSimpleText">Change</button>
  </div>
</template>

<script>
  export default {
    name: 'hello',
    data () {
      return {
        msg: 'Welcome to Your Vue.js App'
      }
    },
    methods: {
      changeSimpleText: function () {
        this.$store.dispatch('simpleAction', 'Text Changed')
      }
    }
  }

</script>
```

## Vue resource

```js
// src/main.js
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'
import store from './store'
import VueResource from 'vue-resource'

Vue.config.productionTip = false

Vue.use(VueResource)

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  store,
  template: '<App/>',
  components: { App }
})

require('../node_modules/bulma/css/bulma.css')
require('./bradcomp.js')
```

## Arquivo de configuração

Criar o arquivo `src/config.js`

```js
// src/config.js
export default {
  url: 'http://localhost:8080'
}
```

Usar onde quiser:

```js
import config from '../config';
```

## Firebase (Database)

Acessar https://console.firebase.google.com e criar uma conta caso seja necessário.  

Crie o projeto

![](images/firebase-create-project.png)

Clique para adicionar o firebase no projeto web

![](images/add-firebase-to-project.png)

Observe o JSON de configuração. Copie-o!

Permita que qualquer um possa acessar o Database:

![](images/firebase-rules.png)

```json
{
  "rules": {
    ".read": "auth == null",
    ".write": "auth == null"
  }
}
```
Clique em `publicar`.

Vamos configurar o arquivo de configuração do Firebase:

```js
// src/firebase.js
import Firebase from 'firebase'

var FirebaseApp = Firebase.initializeApp({
  apiKey: 'AIzaSsyC17mIsDkk38TZGnI9mBjzFIoCu904snn0',
  authDomain: 'vue2napratica.firebaseapp.com',
  databaseURL: 'https://vue2napratica.firebaseio.com',
  projectId: 'vue2napratica',
  storageBucket: 'vue2napratica.appspot.com',
  messagingSenderId: '779719236528'
})
export const db = FirebaseApp.database()
```

E a configuração do VueFire:

```js
// src/main.js
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'
import store from './store'
import VueResource from 'vue-resource'
import VueFire from 'vuefire'

Vue.config.productionTip = false

Vue.use(VueResource)
Vue.use(VueFire)

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  store,
  template: '<App/>',
  components: { App }
})

require('../node_modules/bulma/css/bulma.css')
require('./bradcomp.js')
```

Para usar o firebase:

```html
<template>
  <div>
    <input type="text" v-model="post.title"></input>
    <button @click="addPost">Add</button>
    <pre>{{posts}}</pre>
  </div>
</template>

<script>
  import { db } from '../firebase'

  export default {
    name: 'hello',
    data () {
      return {
        post: {
          title: ''
        }
      }
    },
    firebase: {
      posts: db.ref('posts')
    },
    methods: {
      addPost: function () {
        this.$firebaseRefs.posts.push(this.post).then(
          (r) => {
            this.post = { title: '' }
          }
        )
      }
    }
  }

</script>

```

## Autenticação pelo Github




