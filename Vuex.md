# Vuex
[Site oficial](https://vuex.vuejs.org/#what-is-a-state-management-pattern)

[Markdown](https://medium.com/@habbema/markdown-b430ebbe6e2c)

## Instalação
- npm i --save vuex
- yarn add vuex

Após a instalação é necessário registrar:
/plugins/index.js

```javascript
/**
 * plugins/index.js
 *
 * Automatically included in `./src/main.js`
 */

// Plugins
import vuetify from './vuetify'
import router from '@/router/router'
import axios from './axios'
import store from '@/store/store'

export function registerPlugins (app) {
  app.use(store)
  app.use(axios)
  app.use(router)
  app.use(vuetify)
}
```

Exemplo básico de uma estrutura: 
```javascript
import { createStore } from 'vuex';

export default createStore({
    state: {
        produtos: [
            { ... }
        ]
        // Representa o estado central
        // equivale ao data(){} do componente vue.js
    },
    getter: {
        valorTotal(state) {
            return state.produtos.map(p => p.quantidade * p.preco)
                .reduce((total, atual) => total + atual, 0)
        }
        //Você pode pensar neles como dados computados (propriedade computada) para os stores (store no state)
        //faz cálculos para os objetos que estão no state 
        //são colocados dentro de propriedades computadas

        //getters são colocadas dentro das propriedades computadas
    },
    mutations: {
        //equivale ao setter em POO - usado para criar regra de validação 
        // commit é usado em mutations
        // a mutation só pode passar apenas uma variável como parâmetro,
        // ou ele vai receber apenas o state ou vai receber o state e o payload (pode ser um objeto ex: produtos)
        // são colocados dentro dos métodos


        //as mutations são colocadas dentro de methods - objetivo apenas de alterar o estado centralizado
    },
    actions: {
        //chamada simples da mutations - a mutations equivale ao services com as regras e validações e as actions equivale aos controllers, com as chamadas das services
        //dispatch é usado em actions
        //as actions são colocadas dentro de methods - local para colocar regra para alterar o estado centralizado
    }
});
```
***mutations*** - objetivo apenas alterar o estado centralizado
***actions*** - objetivo: local para colocar regras para alterar o estado central 

**Exemplo de acesso a variável dentro do state:**

```javascript
export default {
    computed: {
        produtos() {
            return this.$store.state.produtos
        }
    },
}
```
**Exemplo de alteração da variável dentro do state:**
```javascript
export default {
    methods: {
        adicionar() {
            return this.$store.state.produtos.push(produto)
        }
    },
}
```
**Exemplo de uso do getters:**
```javascript
export default {
    computed: {
        total() {
            return this.$store.getters.valorTotal
        }
    },
}
```
**Exemplo de uso do mapGetters:**
```javascript
import { mapGetters } from 'vuex'

export default {
    computed: mapGetters(['valorTotal']),

}
```
No exemplo abaixo o nome da função foi renomeada para **total**
```javascript
import { mapGetters } from 'vuex'

export default {
    computed: mapGetters({
        total: 'valorTotal'
    }),

}
```