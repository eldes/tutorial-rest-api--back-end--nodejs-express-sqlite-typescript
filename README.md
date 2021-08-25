# Tutorial: Aplicação REST API com Node, em Typescript, usando Express e SQLite

![Foto decorativa](https://miro.medium.com/max/1400/1*fHW0SthZkIPoo0U9CgdJNw.jpeg)
Photo by [Goran Ivos](https://unsplash.com/@goran_ivos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)


Nesse tutorial você vai aprender a criar uma aplicação REST API com [Node.js](https://nodejs.org/), em linguagem [Typescript](https://www.typescriptlang.org/) , usando o framework [Express](https://expressjs.com/) e base de dados [SQLite](https://www.sqlite.org/index.html).

Originalmente foi escrito para meus alunos das turmas de desenvolvimento web, como conteúdo introdutório a programação back-end com base de dados SQL.

As instruções apresentadas aqui independem do editor de código ou IDE utilizados, mas para elaborar e testar o código desse tutorial foi utilizado o [Visual Studio Code](https://code.visualstudio.com/).

Os comandos utilizados nesse tutorial são baseados diretamente no [yarn](https://yarnpkg.com/), mas caso você prefira [npm](https://www.npmjs.com/) use:

- ***npm init*** em vez de ***yarn init***
- ***npm install*** *módulo* em vez de ***yarn add*** *módulo*
- ***npm run*** *script* em vez de ***yarn*** *script*
- ***npx*** *comando* em vez de ***yarn*** *comando*

### Requisitos

Para seguir esse tutorial você vai precisar dos seguintes softwares instalados no seu sistema:

- [Node.js](https://nodejs.org/)
- [yarn](https://yarnpkg.com/)
- Typescript compiler (tsc)
- [Git](https://git-scm.com/)

## 1. Projeto Node base

O primeiro passo é criar um projeto Node base, ou seja:

1. criar uma pasta vazia para o projeto
2. criar um projeto Node vazio dentro dessa pasta

A partir do terminal de comandos:

```sh
mkdir app-express
cd app-express
yarn init --yes
```

Isso irá criar o arquivo de configuração geral do seu projeto, ***package.json***.

> Você também pode criar a pasta a partir da interface do seu sistema operacional, como do Explorer, no Windows, ou do Finder, no macOS.
>
> Caso você use use um editor de código, como o [Visual Studio Code](https://code.visualstudio.com/) ou o [Sublime Text](https://www.sublimetext.com/), você já pode abrir a pasta recém criada e usar o terminal desses editores para executar o comando de inicialização do projeto Node, assim como os próximos comandos.

### Configuração do Typescript

Em seguida, para usar Typescript no seu projeto, você deve adicionar a configuração, executando o seguinte comando pelo terminal:

```sh
tsc --init
```

Após executado, um arquivo ***tsconfig.json*** será criado na pasta do seu projeto.

Esse tutorial irá se basear na seguinte configuração desse arquivo:

```json
"compilerOptions": {
    "target": "es6",   
    "module": "commonjs",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
}
```

Ajuste o seu arquivo para essa configuração.

Para facilitar a rotina de desenvolvimento, em seguida adicione os scripts *start*, *build* e *dev* ao arquivo de configuração do projeto Node, ***package.json***, dentro da propridade *scripts*:

```json
"scripts": {
    "dev": "ts-node ./src/index.ts",
    "build": "tsc",
    "start": "node ./dist/index.js"
},
```

Aproveite para definir o nome do script principal do projeto, trocando ***index.js*** por ***./src/index.ts*** nesse mesmo arquivo de configuração:

```json
"main": "./src/index.ts"
```

Para finalizar a configuração do Typescript, devem ser instalados o módulos dos *types* para o Node, e o módulo *ts-node*, por meio do seguinte comando no terminal:

```sh
yarn add @types/node typescript
yarn add -D ts-node
```

## 2. Módulos Express e Cors

O próximo passo é instalar os módulos do Express e do Cors no seu projeto, usando os seguintes comandos no terminal:

```sh
yarn add express
yarn add cors
```

E, como nosso projeto será codificado em Typescript, também deverão ser instalados os types para esses módulos:

```sh
yarn add @types/express
yarn add @types/cors
```

### Express

O Express é um módulo Node.js que fornece uma estrutura mínima para implementar um servidor web.

### Cors

O *CORS* (*Corss-rigin Resource Sharing*) é um mecanismo utilizado pelos navegadores para compartilhar recursos entre diferentes origens.

Por causa desse mecanismo, assumindo que você esteja rodando o seu *app* front-end em http://localhost:3000 e o seu servidor, numa porta e ou domínio diferentes, como em http://locahost:4000 (de acordo com este tutorial), ao tentar se conectar ao back-end, poderá receber uma mensagem de erro.

Isso acontece porque o sistema de segurança do navegador bloqueia a interação do script de uma origem com recursos de outra origem diferente.


O Cors é um pacote Node.js para fornecer um *middleware* do Express que permite definir quais domínios e métodos HTTP o servidor permite conexão.

## 3. Script principal do projeto

Para conseguir — finalmente — rodar nosso servidor pela primeira vez, é preciso criar a pasta ***src*** na pasta do seu projeto, e o script principal, ***index.ts***, dentro da pasta ***src***, com o seguinte código:

```js
import express from 'express'
import cors from 'cors'

// Porta do servidor
const PORT = process.env.PORT || 4000

// Host do servidor
const HOSTNAME = process.env.HOSTNAME || 'http://localhost'

// App Express
const app = express()

// Endpoint raiz
app.get('/', (req, res) => {
	res.send('Bem-vindo!')
})

// Cors
app.use(cors({
	origin: ['http://localhost:3000']
}))

// Resposta padrão para quaisquer outras requisições:
app.use((req, res) => {
	res.status(404)
})

// Inicia o sevidor
app.listen(PORT, () => {
	console.log(`Servidor rodando com sucesso ${HOSTNAME}:${PORT}`)
})
```

Até esse ponto, nosso projeto deverá ter a seguinte estrutura de pastas e arquivos:

```txt
├─ 📁 node_modules/
├─ 📁 src/
│   └─ 📄 index.ts
├─ 📄  package.json
├─ 📄  tsconfig.json
└─ 📄  yarn.lock
```

Agora já podemos rodar nosso projeto, executando no terminal:

```sh
yarn dev
```

Feito isso, e abrindo o endereço [http://localhost:4000](http://localhost:4000/) na janela do navegador, a mensagem *"Bem-vindo!"* deverá ser exibida.

## 4. Modelo de dados

Numa aplicação web, normalmente faz uso de muitas entidades de dados, como *Produto*, *Usuário*, *Postagem* etc..

Esse tutorial vai utilizar a entidade genérica, chamada *Item*, para compor o modelo de dados do projeto.

Você pode entender o *Item* como sendo o produto de um comércio eletrônico, ou o usuário de um sistema web com autenticação, ou uma postagem de um blog.

Assim, vamos criar os respectivos types para esse modelo em um novo script chamado ***item.ts***, numa nova pasta, ***models***, criada dentro da pasta ***src***:

```js
type Item = {
    id?: number
    nome: string
    descricao: string
}

export default Item
```

## 5. Endpoints do CRUD

Em um servidor REST, os dados são manipulados por meio de chamadas HTTP a URLs predefinidas: os *endpoints*.

Cada operação é definida por uma dessas URLs e pelo método HTTP usado, como por exemplo GET, POST, PUT, UPDATE e DELETE.

Assim, para o projeto desse tutorial, teremos as seguintes operações *CRUD* de persistência (*Create*, *Read*, *Update* e *Delete*) para a entidade *Item*:

- **Criar um novo item:** POST /api/itens
- **Ler todos os itens:** GET /api/itens
- **Ler um item específico:** GET /api/itens/{id}
- **Atualizar um item:** PUT /api/itens/{id}
- **Apagar um item:** DELETE /api/itens/{id}

### Rotas

Para organizar melhor nosso projeto, é interessante criar um conjunto de rotas para cada entidade.

Assim vamos criar uma pasta ***routers***, dentro de ***src***, e um script ***itens-router.ts***, dentro dessa pasta, com o seguinte conteúdo inicial:

```js
import express from 'express'

const itensRouter = express.Router()

itensRouter.post('/itens', (req, res) => {
    res.send('Cria novo item')
})

itensRouter.get('/itens', (req, res) => {
    res.send('Lê todos os itens')
})

itensRouter.get('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    res.send(`Lê o item ${id}`)
})

itensRouter.put('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    res.send(`Atualiza o item ${id}`)
})

itensRouter.delete('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    res.send(`Apaga o item ${id}`)
})

export default itensRouter
```

Em seguida, ajustamos o script principal, ***index.ts***, para usar esse conjunto de rotas:

```js
import express from 'express'
import cors from 'cors'
import itensRouter from './routers/itens-router'

// Porta do servidor
const PORT = process.env.PORT || 4000

// Host do servidor
const HOSTNAME = process.env.HOSTNAME || 'http://localhost'

// App Express
const app = express()

// Endpoint raiz
app.get('/', (req, res) => {
    res.send('Bem-vindo!')
})

// Cors
app.use(cors({
	origin: ['http://localhost:3000']
}))

// Rotas
app.use('/api', itensRouter)

// Resposta padrão para quaisquer outras requisições:
app.use((req, res) => {
    res.status(404)
})

// Inicia o sevidor
app.listen(PORT, () => {
    console.log(`Servidor rodando com sucesso ${HOSTNAME}:${PORT}`)
})
```

### Vamos testar?

Para testar é preciso parar o servidor, dando um *Ctrl+C* no terminal, e rodar novamente o dev:

```sh
yarn dev
```

No navegador é possível testar os dois endpoints que usam o método GET do HTTP, ou seja o endpoint para ler todos itens e o endpoint para ler um item específico, abrindo as suas respectivas URLs:

```txt
http://localhost:4000/api/itens
http://localhost:4000/api/itens/123
```

Para testar os demais endpoints, recomendo o uso de um aplicativo para essa finalidade específica, como o [Postman](https://www.postman.com) ou [Insomnia](https://insomnia.rest).

## 6. Nodemon

É preciso repetir esse processo, de parar e reiniciar novamente o servidor, sempre que precisarmos testar uma nova alteração no código?

Felizmente a resposta é não.

Mas, para isso, vamos precisar instalar um novo módulo no projeto: [Nodemon](https://nodemon.io).

Essa é uma ferramenta que ajuda o desenvolvimento reiniciando automaticamente a aplicação Node quando algum arquivo do projeto for alterado.

Para instalar esse módulo, executamos então, no terminal, lembrando de parar o servidor com *Ctrl+C* primeiro:

```sh
yarn add -D nodemon
```

Em seguida, ajustamos a propriedade *scripts* do arquivo de configuração geral do nosso projeto, ***package.json***, para o Nodemon ficar de olho na pasta ***src*** e executar a antiga ação do script *dev* sempre que algum de seus arquivos TS ou JSON forem alterados e salvos:

```json
"scripts": {
    "dev": "nodemon --watch \"src/**\" --ext \"ts,json\" --exec \"ts-node ./src/index.ts\"",
    "build": "tsc",
    "start": "node ./dist/index.js"
},
```

Pronto! Agora é só rodar novamente o dev:

```sh
yarn dev
```

## 7. Simulando com JSON

Nosso servidor REST vai usar o formato padrão, JSON, para os dados usados nas suas operações.

Para tanto vamos ajustar o código das operações do CRUD no script itens-routers.ts para usar esse formato de dados.

Primeiro devemos configurar o nosso app Express para identificar o formato JSON. Isso é feito adicionando algumas linhas no script ***index.ts***:

```js
import express from 'express'
import cors from 'cors'
import itensRouter from './routers/itens-router'

// Porta do servidor
const PORT = process.env.PORT || 4000

// Host do servidor
const HOSTNAME = process.env.HOSTNAME || 'http://localhost'

// App Express
const app = express()

// JSON
app.use(express.json())
app.use(express.urlencoded({ extended: true }))

// Endpoint raiz
app.get('/', (req, res) => {
    res.send('Bem-vindo!')
})

// Cors
app.use(cors({
	origin: ['http://localhost:3000']
}))

// Rotas
app.use('/api', itensRouter)

// Resposta padrão para quaisquer outras requisições:
app.use((req, res) => {
    res.status(404)
})

// Inicia o sevidor
app.listen(PORT, () => {
    console.log(`Servidor rodando com sucesso ${HOSTNAME}:${PORT}`)
})
```

### Ler todos os itens

A operação que lê todos os itens da aplicação irá retornar um array de objetos do tipo *Item*, em formato JSON.

Assim vamos criar um array com dados estáticos para testar esse novo formato e modificar o modo de resposta do endpoint.

Para tanto vamos ajustar código do respectivo endpoint, no arquivo ***itens-router.ts***, para:

```js
itensRouter.get('/itens', (req, res) => {
    const itens: Item[] = [
        {
            id: 1,
            nome: 'Produto 1',
            descricao: 'Descrição do produto 1'
        },
        {
            id: 2,
            nome: 'Produto 2',
            descricao: 'Descrição do produto 2'
        }
    ]
    res.json(itens)
})
```

Não esquecendo de importar o type *Item*, no início do código desse arquivo:

```js
import express from 'express'
import Item from '../models/item'
```

### Ler um item

A operação que lê um item da aplicação irá retornar um objeto do tipo *Item*, em formato JSON.

Assim vamos modificar também o modo de resposta desse endpoint, criando e retornando um objeto com dados estáticos de teste.

Para tanto vamos ajustar código do respectivo endpoint, no arquivo ***itens-router.ts***, para:

```js
itensRouter.get('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    const item: Item = {
        id: id,
        nome: `Produto ${id}`,
        descricao: `Descrição do produto ${id}`
    }
    res.json(item)
})
```

Caso o id informado não exista no sistema, o endpoint pode simplesmente retornar o código 404, e assim a resposta seria:

```js
res.status(404).send()
```

### Criar um item

O endpoint para criar um novo item deve receber um objeto do tipo *Item*, contendo valores para os campos nome e descrição, salvar na base de dados e recuperar o *id* que será gerado pelo SQLite.

No caso de sucesso na operação, normalmente as chamadas a endpoints de uma API REST respondem, por padrão, com o valor 200 para o código de status HTTP.

Mas, no caso da operação de criação, o valor do código de status HTTP comumente usado numa API REST, para indicar sucesso, é 201.

Por esse motivo, vamos ajustar o código desse endpoint, no arquivo ***itens-router.ts***, tanto para retornar, no cabeçalho HTTP *Location*, a rota para ler o item criado, como também para indicar explicitamente o valor 201 para o código de status HTTP retornado:

```js
itensRouter.post('/itens', (req, res) => {
    const item: Item = req.body
    //TODO: Criar e salvar um novo item
    const id = 123
    res.status(201).location(`/itens/${id}`).send()
})
```

### Atualizar um item

De forma análoga, para o caso da operação de atualização, o valor do código de status HTTP comumente usado, indicando sucesso e sem retornar nada no corpo de resposta, é o 204.

Caso o id informado não exista no sistema, o endpoint pode simplesmente retornar o código 404.

Assim, vamos ajustar o código do respectivo endpoint no arquivo ***itens-router.ts***:

```js
itensRouter.put('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    res.status(204).send()
})
```

### Apagar um item

De forma análoga, para o caso da operação de apagar, o valor do código de status HTTP comumente usado, indicando sucesso e sem retornar nada no corpo de resposta, é o 204.

Caso o id informado não exista no sistema, o endpoint pode simplesmente retornar o código 404.

E caso a operação ainda não foi completada, mas foi aceita e adicionada numa fila para processamento, então pode retornar o código 202. Esse mesmo código pode ser retornado repetidamente para chamadas posteriores a esse endpoint para o mesmo id, enquanto o processamento não esteja finalizado; e o código 204 quando estiver finalizado.

Assim, vamos ajustar o código do respectivo endpoint no arquivo ***itens-router.ts***:

```js
itensRouter.delete('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    res.status(204).send()
})
```

## 8. Conectando com a base de dados

O próximo passo é conectar nossa API com uma base de dados e ajustar os endpoints trabalhar com a partir dessa base.

Para esse tutorial iremos usar uma única tabela de exemplo, chamada ***itens***, seguindo a seguinte estrutura:

```txt
┌──────────────────────┐
│ itens                │
├────┬─────────────────┤
│ PK │ id: INTEGER     │
│    │ nome: TEXT      │
│    │ descricao: TEXT │
└────┴─────────────────┘
```

E, portanto, o seguinte script SQL para a criação da tabela:

```sql
CREATE TABLE itens (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT,
    descricao TEXT
)
```

### Módulo SQLite

Antes de mais nada precisamos instalar o módulo Node.js do SQLite. No terminal:

```sh
yarn add sqlite3
yarn add @types/sqlite3
```


### Código Typescript

Vamos então criar um novo arquivo, chamado ***database.ts***, dentro de uma nova pasta ***repositories***, em ***src***, com o seguinte código para efetuar a conexão à base de dados e a criação das tabelas:

```js
import sqlite3 from 'sqlite3'

const DBSOURCE = 'db.sqlite'

const SQL_ITENS_CREATE = `
    CREATE TABLE itens (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        nome TEXT,
        descricao TEXT
    )`

const database = new sqlite3.Database(DBSOURCE, (err) => {
    if (err) {
        console.error(err.message)
        throw err
    } else {
        console.log('Base de dados conectada com sucesso.')
        database.run(SQL_ITENS_CREATE, (err) => {
            if (err) {
                // Possivelmente a tabela já foi criada
            } else {
                console.log('Tabela itens criada com sucesso.')
            }
        })
    }
})

export default database
```

### Criar item

Criaremos também um novo arquivo, ***itens-repository.ts***, dentro dessa pasta, com o seguinte código, implementando, num primeiro momento, a operação para criar um novo Item no sistema:

```js
import Item from '../models/item'
import database from './database'

const itensRepository = {
    criar: (item: Item, callback: (id?: number) => void) => {
        const sql = 'INSERT INTO itens (nome, descricao) VALUES (?, ?)'
        const params = [item.nome, item.descricao]
        database.run(sql, params, function(_err) {
            callback(this?.lastID)
        })
    },
}

export default itensRepository
```

Para completar, ajustamos o endpoint da respectiva operação no arquivo ***itens-router.ts***:

```js
itensRouter.post('/itens', (req, res) => {
    const item: Item = req.body
    itensRepository.criar(item, (id) => {
        if (id) {
            res.status(201).location(`/itens/${id}`).send()
        } else {
            res.status(400).send()
        }
    })
})
```

Lembrando de importar a constante *itensRepository*, no início do código desse arquivo:

```js
import express from 'express'
import Item from '../models/item'
import itensRepository from '../repositories/itens-repository'
```

### Ler todos os itens

Do mesmo modo, vamos implementar a operação para ler todos os itens gravados no sistema.

Assim, complementaremos o arquivo ***itens-repository.ts***:

```js
import Item from '../models/item'
import database from './database'
const itensRepository = {
    criar: (item: Item, callback: (id?: number) => void) => {
        const sql = 'INSERT INTO itens (nome, descricao) VALUES (?, ?)'
        const params = [item.nome, item.descricao]
        database.run(sql, params, function(_err)) {
            callback(this?.lastID)
        })
    },
    lerTodos: (callback: (itens: Item[]) => void) => {
        const sql = 'SELECT * FROM itens'
        const params: any[] = []
        database.all(sql, params, (_err, rows) => callback(rows))
    },
}
export default itensRepository
```

Assim como ajustamos a respectiva operação no arquivo ***itens-router.ts***:

```js
itensRouter.get('/itens', (req, res) => {
    itensRepository.lerTodos((itens) => res.json(itens))
})
```

### Ler um item

Vamos implementar também a operação para ler um item específico, a partir do seu *id*, no arquivo ***itens-repository.ts***:

```js
ler: (id: number, callback: (item?: Item) => void) => {
    const sql = 'SELECT * FROM itens WHERE id = ?'
    const params = [id]
    database.get(sql, params, (_err, row) => callback(row))
},
```

E no arquivo ***itens-router.ts***:

```js
itensRouter.get('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    itensRepository.ler(id, (item) => {
        if (item) {
            res.json(item)
        } else {
            res.status(404).send()
        }
    })
})
```

### Atualizar um item

A próxima operação que vamos implementar é a de atulizar um item específico, a partir do seu *id* e seus dados, no arquivo ***itens-repository.ts***:

```js
atualizar: (id: number, item: Item, callback: (notFound: boolean) => void) => {
    const sql = 'UPDATE itens SET nome = ?, descricao = ? WHERE id = ?'
    const params = [item.nome, item.descricao, id]
    database.run(sql, params, function(_err) {
        callback(this.changes === 0)
    })
},
```

E no arquivo ***itens-router.ts***:

```js
itensRouter.put('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    itensRepository.atualizar(id, req.body, (notFound) => {
        if (notFound) {
            res.status(404).send()
        } else {
            res.status(204).send()
        }
    })
})
```

### Apagar um item

Por fim, vamos implementar a operação de apagar um item específico, a partir do seu *id*, no arquivo ***itens-repository.ts***:

```js
apagar: (id: number, callback: (notFound: boolean) => void) => {
    const sql = 'DELETE FROM itens WHERE id = ?'
    const params = [id]
    database.run(sql, params, function(_err) {
        callback(this.changes === 0)
    })
},
```

E no arquivo ***itens-router.ts***:

```js
itensRouter.delete('/itens/:id', (req, res) => {
    const id: number = +req.params.id
    itensRepository.apagar(id, (notFound) => {
        if (notFound) {
            res.status(404).send()
        } else {
            res.status(204).send()
        }
    })
})
```

Nesse ponto você já tem a base de um servidor REST API com Node, em Typescript, usando Express e SQLite funcional.

Tentei deixar o mais enxuto possível e, até mesmo por isso, essa base pode ter o tratamento de exceções aprofundada e melhorada a estrutura do código, seguindo, por exemplo o padrão [Generic Repository](https://medium.com/@erickwendel/generic-repository-with-typescript-and-node-js-731c10a1b98e).

## 9. Controle de versão

Nesse ponto, é interessante configurarmos o nosso projeto para fazermos o controle de versão do código.

Nesse tutorial, sugerimos que se use o Git para isso e, em complemento, o [GitHub](https://github.com) ou [BitBucket](https://bitbucket.org) como repositório remoto.

Mas nem todos os arquivos do projeto têm necessidade de controle de versão, por isso podemos criar um arquivo [***.gitignore***](https://github.com/github/gitignore), na pasta do projeto, com o seguinte conteúdo:

```txt
node_modules/
dist/
db.sqlite
```

Você pode complementar o conteúdo desse arquivo à medida em que o código avança e de acordo com as ferramentas e módulos adotados no projeto.

Após criado o arquivo ***.gitignore***, para iniciar o controle de versão, execute em terminal, dentro da pasta do projeto:

```sh
git init
```

E, na sequência, adicione a pasta ao controle de versão, executando o seguinte comando no terminal:

```sh
git add .
```

E para efetuar o primeiro *commit*, o seguinte comando:

```sh
git commit -m "Inicia projeto de servidor Node com Express e SQLite"
```

Caso queria adicionar esse projeto no GitHub ou BitBucket, crie um novo repositório em um desses servidores e, em seguida, execute os seguinte comandos:

```sh
git remote add origin {url}
git branch -M main
git push -u origin main
```

Substituindo ***{url}*** pela URL do repositório criado no servidor.

> Você pode encontrar o código completo desse projeto na minha conta do GitHub, em: [https://github.com/eldes/tutorial-rest-api-nodejs-express-sqlite-typescript](https://github.com/eldes/tutorial-rest-api-nodejs-express-sqlite-typescript)

## Referências

- [https://restfulapi.net/http-methods/](https://restfulapi.net/http-methods/)
- [https://developerhowto.com/2018/12/29/build-a-rest-api-with-node-js-and-express-js/](https://developerhowto.com/2018/12/29/build-a-rest-api-with-node-js-and-express-js/)


## Contribuição

Comentários e dúvidas são bem-vindas, tanto aqui quanto no artigo original no [Medium](https://link.medium.com/CpyQ06cRDib).