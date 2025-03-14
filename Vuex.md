# Vuex
[Site oficial](https://vuex.vuejs.org/#what-is-a-state-management-pattern)

[Markdown](https://medium.com/@habbema/markdown-b430ebbe6e2c)

## Instalação
- npm i --save vuex
- yarn add vuex

Após a instalação é necessário registrar:
/plugins/*index.js*

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
Exemplo 1
```javascript
import { mapGetters } from 'vuex'

export default {
    computed: mapGetters({
        total: 'valorTotal'
    }),

}
```
Exemplo 2
```javascript
import { mapGetters } from 'vuex'

export default {
    computed: {
        ...mapGetters({
            total: 'valorTotal'
        }),
    }
}
```
**Exemplo de uso da Mutation:**
```javascript
mutations: {
    adicionarProduto(state, payload) {
        state.produtos.push(payload)
    },
},
```
Exemplo 1
```javascript
methods: {
    adicionar() {
        const produto = {
            id: this.sequencia,
            nome: `Produto ${this.sequencia}`,
            quantidade: this.quantidade,
            preco: this.preco
        }
        this.sequencia++

        this.$store.state.produtos.push(produto)
    }
}
```
Exemplo 2
```javascript
methods: {
    ...mapMutations(['adicionarProduto']),
    adicionar() {
        const produto = {
            id: this.sequencia,
            nome: `Produto ${this.sequencia}`,
            quantidade: this.quantidade,
            preco: this.preco
        }
        this.sequencia++

        this.adicionarProduto(produto)
    }
}
```

**Exemplo de uso das Actions**
```javascript
actions: {
    adicionarProduto({ commit }, payload) {
        setTimeout(() => {
            commit('adicionarProduto', payload)
        }, 1000)
    }

    // adicionarProduto: {
    //     root: true,
    //     handler({ commit }, payload) {
    //         setTimeout(() => {
    //             commit('adicionarProduto', payload)
    //         }, 1000)
    //     }
    // }
},
```
```javascript
methods: {
    ...mapActions(['adicionarProduto']),
    adicionar() {
        const produto = {
            id: this.sequencia,
            nome: `Produto ${this.sequencia}`,
            quantidade: this.quantidade,
            preco: this.preco
        }
        this.sequencia++

        //this.$store.state.produtos.push(produto)
        //this.$store.commit('adicionarProduto', produto)
        //this.$store.dispatch('adicionarProduto', produto)
        this.adicionarProduto(produto)
    }
}
```

## Módulos com Vuex

Estrutura de diretório
- /store/modulos

**Exemplos:**
*carrinho.js*

```javascript
export default {
    namespaced: true,    
    state: {
        produtos: [],
    },
    getters: {
        valorTotal(state) {
            return state.produtos.map(p => p.quantidade * p.preco)
                .reduce((total, atual) => total + atual, 0)
        }
    },
    mutations: {
        adicionarProduto(state, payload) {
            state.produtos.push(payload)
        },
    },
    actions: {
        adicionarProduto({ commit }, payload) {
            setTimeout(() => {
                commit('adicionarProduto', payload)
            }, 1000)
        }

        // adicionarProduto: {
        //     root: true,
        //     handler({ commit }, payload) {
        //         setTimeout(() => {
        //             commit('adicionarProduto', payload)
        //         }, 1000)
        //     }
        // }
    },
}
```
*parametros.js*

```javascript
export default {
    state: {
        quantidade: 2,
        preco: 19.99
    },
    mutations: {
        setQuantidade(state, payload) {
            state.quantidade = payload
        },
        setPreco(state, payload) {
            state.preco = payload
        }
    },
}
```

Estrutura de diretório
- /store

*store.js*

```javascript
import Vue from "vue";
import Vuex from "vuex";

import carrinho from "./modules/carrinho";
import parametros from "./modules/parametros";

import * as getters from './modules/getters'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        nome: 'Maria',
        sobrenome: 'Silva'
    },
    getters,
    modules: { carrinho, parametros }
})
```
acessando
```javascript

export default {
    data() {
        return {
            sequencia: 1,
        }
    },
    computed: {
        quantidade() {
            return this.$store.state.parametros.quantidade //note que agora depois do state tem .parametros conforme configurado no módulo
        },
        preco() {
            return this.$store.state.parametros.preco
        }
    },
}
```

## Usando arquivos separados
Serve para colocar métodos gerais que não se encaixa em nenhum módulo pré-existente

*getters.js*
```javascript
export const getNome = state => state.getNome
export const getNomeCompleto = state => state.nome + state.sobrenome
```

*store.js*
```javascript
import Vue from "vue";
import Vuex from "vuex";

import carrinho from "./modules/carrinho";
import parametros from "./modules/parametros";

//importando os getters que foram criados de forma geral
import * as getters from './modules/getters'

Vue.use(Vuex)

export default new Vuex.Store({
    state: {
        nome: 'Maria',
        sobrenome: 'Silva'
    },
    //importando os getters que foram criados de forma geral
    getters,
    modules: { carrinho, parametros }
})
```
exemplo da chamado do getter direto em um módulo
```javascript
this.$store.getters.getNome
this.$store.getters.getNomeCompleto
```

## Namespaced

exemplo de uso do namespaced
```javascript
export default {
    namespaced: true,    
    state: {
        produtos: [],
    },
    getters: {
        valorTotal(state) {
            return state.produtos.map(p => p.quantidade * p.preco)
                .reduce((total, atual) => total + atual, 0)
        }
    },
    mutations: {
        adicionarProduto(state, payload) {
            state.produtos.push(payload)
        },
    },
    actions: {
        adicionarProduto({ commit }, payload) {
            setTimeout(() => {
                commit('adicionarProduto', payload)
            }, 1000)
        }
    }
}
```
Uso do namespaced no componente vue
```javascript
import { mapGetters } from 'vuex'

export default {
    computed: {
        ...mapGetters('carrinho',{
            total: 'valorTotal'
        }),
        
        produtos() {
            return this.$store.state.carrinho.produtos
        }
    },
}
```
