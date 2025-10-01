
## 🚀 Começando

O repositório possui 3 pastas importantes:
- backend
- frontend
- instalador

O backend é feito em Express e possui toda a estrutura organizada dentro dessa pasta para que seja aplicado no ambiente do cliente. A pasta de frontend contém todo o framework do React.js que gerencia toda a interação com o usuário do sistema.

A pasta de instalador dentro dessa repositório é uma cópia do instalador usado para que os clientes de sistemas possam fazer o clone dentro da pasta home de seus servidores e seguirem com a instalação automática de todas as dependências do projeto

Link para o repositório do instalador atualizado:
- [Instalador](https://github.com/atendechat-org/instalador)

Consulte **[Implantação](#-implanta%C3%A7%C3%A3o)** para saber como implantar o projeto.

### 📋 Pré-requisitos

```
- Node.js v20.x
- Postgres (release)
- Npm ( latest )
- Docker (bionic stable)
- Redis
```

### 🔧 Instalação

Para iniciar a instalação do projeto é necessário ter todas as ferramentas de pré-requisitos disponíveis para uso

#### Redis
```
- su - root
- docker run --name redis-${instancia_add} -p ${redis_port}:6379 --restart always --detach redis redis-server --requirepass ${root_password}
```

#### Postgres
```
- sudo su - postgres
- createdb ${instancia_add};
- psql
- CREATE USER ${instancia_add} SUPERUSER INHERIT CREATEDB CREATEROLE;
- ALTER USER ${instancia_add} PASSWORD '${root_password}';
```

#### .env backend
```
NODE_ENV=
BACKEND_URL=${backend_url}
FRONTEND_URL=${frontend_url}
PROXY_PORT=443
PORT=${backend_port}

DB_DIALECT=postgres
DB_HOST=localhost
DB_PORT=5432
DB_USER=${instancia_add}
DB_PASS=${mysql_root_password}
DB_NAME=${instancia_add}

JWT_SECRET=${jwt_secret}
JWT_REFRESH_SECRET=${jwt_refresh_secret}

REDIS_URI=redis://:${mysql_root_password}@127.0.0.1:${redis_port}
REDIS_OPT_LIMITER_MAX=1
REGIS_OPT_LIMITER_DURATION=3000

USER_LIMIT=${max_user}
CONNECTIONS_LIMIT=${max_whats}
CLOSED_SEND_BY_ME=true

GERENCIANET_SANDBOX=false
GERENCIANET_CLIENT_ID=Client_Id_Gerencianet
GERENCIANET_CLIENT_SECRET=Client_Secret_Gerencianet
GERENCIANET_PIX_CERT=certificado-Gerencianet
GERENCIANET_PIX_KEY=chave pix gerencianet

# EMAIL
 MAIL_HOST="smtp.gmail.com"
 MAIL_USER="seu@gmail.com"
 MAIL_PASS="SuaSenha"
 MAIL_FROM="seu@gmail.com"
 MAIL_PORT="465"

```

#### .env frontend
```
REACT_APP_BACKEND_URL=${backend_url}
REACT_APP_HOURS_CLOSE_TICKETS_AUTO = 24
```

#### Instalando dependências
```
cd backend/
npm install --force
cd frontend/
npm install --force
```

### Rodando localmente
```
cd backend/
npm run watch
npm start

cd frontend/
npm start
```

## ⚙️ Executando os testes

//

### 🔩 Analise os testes de ponta a ponta

//

## 📦 Implantação em produção

Para correta implantação é necessário realizar uma atualização do código fonte da aplicação e criar novamente os arquivos da pasta dist/

Atenção: é necessário acessar utilizando o usuário de deploy

```
su - deploy
```

```
cd /home/deploy/${empresa_atualizar}
pm2 stop ${empresa_atualizar}-frontend
git pull
cd /home/deploy/${empresa_atualizar}/frontend
npm install
rm -rf build
npm run build
pm2 start ${empresa_atualizar}-frontend
pm2 save
```

```
cd /home/deploy/${empresa_atualizar}
pm2 stop ${empresa_atualizar}-backend
git pull
cd /home/deploy/${empresa_atualizar}/backend
npm install
npm update -f
npm install @types/fs-extra
rm -rf dist 
npm run build
npx sequelize db:migrate
npx sequelize db:migrate
npx sequelize db:seed
pm2 start ${empresa_atualizar}-backend
pm2 save 
```

## 🛠️ Construído com


* [Express](https://expressjs.com/pt-br/) - O framework backend usado
* [React](https://react.dev/) - Framework frontend usado
* [NPM](https://www.npmjs.com/) - Gerenciador de dependências

## 🖇️ Colaborando

//

## 📌 Versão

Versão 1.1.0



##

Documentação do que já foi feito
1. Banco de Dados
•	Criamos as tabelas no PostgreSQL:
o	urgency_categories → categorias de urgência vinculadas a empresas.
o	todolist → lista de tarefas por usuário (no MVP, sempre 1 lista “default”).
o	todotasks → tarefas com vínculo a empresa, criador, responsável e categoria.
o	todotaskresponses → respostas/comentários em tarefas.
•	Aplicamos as foreign keys para garantir integridade:
o	company_id → "Companies"
o	user_id / created_by / assigned_to / responded_by → "Users"
o	urgency_category_id → urgency_categories
o	todolist_id → todolist
o	task_id → todotasks
o   Mudou de snake_case para camelCe
________________________________________
2. Models (Sequelize + TypeScript)
Criados em src/models/:
•	UrgencyCategory.ts
•	ToDoList.ts
•	ToDoTask.ts
•	ToDoTaskResponse.ts

Todos refletem 1:1 as tabelas do banco, com tipagem e atributos (id, company_id, etc.).
________________________________________
3. Services (lógica de negócio)
Criados em src/services/ToDoTaskService/:
•	CreateService.ts → cria nova tarefa.
•	ListService.ts → lista tarefas com filtros (empresa, usuário, status, categoria).
•	UpdateService.ts → atualiza título, descrição, status, responsável, etc.
•	DeleteService.ts → remove tarefa com checagem por empresa.
👉 Services isolam a lógica de negócio, mantendo controllers limpos.
________________________________________
4. Controllers
Criado em src/controllers/ToDoTaskController.ts:
•	store → chama CreateService, responde 201.
•	index → chama ListService, responde lista em JSON.
•	update → chama UpdateService, responde a tarefa atualizada.
•	remove → chama DeleteService, responde mensagem de sucesso.
________________________________________
5. Routes
Organização escolhida: pasta src/routes/ToDo/.
•	Criado arquivo ToDoTaskRoutes.ts:
•	routes.post("/", ToDoTaskController.store);
•	routes.get("/", ToDoTaskController.index);
•	routes.put("/:id", ToDoTaskController.update);
•	routes.delete("/:id", ToDoTaskController.remove);
•	Registrado no src/routes/index.ts:
•	import ToDoTaskRoutes from "./ToDo/ToDoTaskRoutes";
•	routes.use("/todo/tasks", ToDoTaskRoutes);
👉 Endpoints finais:
•	POST /todo/tasks → cria tarefa
•	GET /todo/tasks → lista tarefas
•	PUT /todo/tasks/:id → atualiza tarefa
•	DELETE /todo/tasks/:id → remove tarefa


📋 Módulo ToDoList
1. Tabela no banco
todolist
id SERIAL PRIMARY KEY,
company_id INT NOT NULL,
user_id INT NOT NULL,
name VARCHAR(100) DEFAULT 'default',
created_at TIMESTAMP DEFAULT NOW()
•	Cada lista pertence a uma empresa (company_id).
•	Cada lista pertence a um usuário (user_id).
•	Nome default: "default".
•	Timestamp automático de criação.
________________________________________
2. Model
📂 src/models/ToDoList.ts
•	Representa a tabela todolist.
•	Campos: id, company_id, user_id, name, created_at.
•	timestamps: false (pois usamos created_at manual).
________________________________________
3. Services
📂 src/services/ToDoListService/
•	CreateService.ts → cria lista para usuário/empresa (nome default se não for passado).
•	ListService.ts → lista listas com filtros (company_id, user_id).
•	UpdateService.ts → renomeia a lista.
•	DeleteService.ts → remove a lista (checando se pertence à empresa).
________________________________________
4. Controller
📂 src/controllers/ToDoListController.ts
•	store → chama CreateService.
•	index → chama ListService.
•	update → chama UpdateService.
•	remove → chama DeleteService.
•	Responde JSON e trata erros.
________________________________________
5. Rotas
📂 src/routes/ToDo/ToDoListRoutes.ts
POST   /todo/lists      → cria lista
GET    /todo/lists      → lista listas
PUT    /todo/lists/:id  → atualiza lista
DELETE /todo/lists/:id  → remove lista




Estrutura lógica atual: 

1. Banco de Dados 
•	Criar tabelas todolist, todotasks, urgency_categories e todotaskresponses
👉 Motivo: são a base de dados, guardam as informações que o backend vai manipular. Sem elas, não há persistência.
•	Adicionar foreign keys (ALTER TABLE)
👉 Motivo: garante integridade — uma tarefa sempre pertence a uma lista, um comentário sempre pertence a uma tarefa, etc.
________________________________________
2. Models (ORM)
•	Criar models/ToDoList.ts
•	Criar models/ToDoTask.ts
•	Criar models/UrgencyCategory.ts
•	Criar models/ToDoTaskResponse.ts
👉 Motivo: os models são a tradução das tabelas para objetos JS/TS. Eles permitem interagir com o banco sem precisar escrever SQL o tempo todo.
________________________________________
3. Services (lógica de negócio)
Para cada entidade (CRUD):
•	CreateService.ts → criar registros
•	ListService.ts → listar registros
•	ShowService.ts → buscar 1 registro específico
•	UpdateService.ts → atualizar
•	DeleteService.ts → remover
👉 Motivo: os services isolam a lógica de negócio. Assim o controller fica limpo e você pode reutilizar regras em vários lugares.
________________________________________
4. Controllers (entrada HTTP)
•	Criar controllers/ToDoListController.ts
•	Criar controllers/ToDoTaskController.ts
•	Criar controllers/UrgencyCategoryController.ts
•	Criar controllers/ToDoTaskResponseController.ts
👉 Motivo: controllers recebem as requisições (req/res), chamam os services, e devolvem a resposta. Eles são a “ponte” entre API e lógica.
________________________________________
5. Routes (endpoints da API)
•	Criar routes/todoRoutes.ts com CRUD para listas, tarefas, categorias e respostas.
•	Registrar no routes/index.ts → ex: routes.use("/todo", isAuth, todoRoutes);
👉 Motivo: as rotas expõem a API. É o que vai permitir o frontend (ou Postman/Insomnia) conversar com o backend.
________________________________________
6. Validações
•	Validação no service/controller (ex.: não permitir criar tarefa sem title ou urgency_category_id).
👉 Motivo: garante que os dados gravados no banco são válidos e evita bug no app.
________________________________________
7. Testar endpoints
•	Testar POST /todo/lists → criar lista
•	Testar POST /todo/tasks → criar tarefa
•	Testar GET /todo/tasks → listar tarefas
•	Testar POST /todo/responses → adicionar comentário
👉 Motivo: validar se o fluxo funciona de ponta a ponta (do banco até a resposta JSON).
________________________________________
8. Documentar
•	Criar README interno ou checklist atualizado
👉 Motivo: no futuro, você (ou outra pessoa) vai agradecer por saber como o módulo foi estruturado e por onde continuar.
________________________________________
📌 Resumo:
1.	Banco → 2. Models → 3. Services → 4. Controllers → 5. Routes → 6. Validações → 7. Testes → 8. Documentação.


