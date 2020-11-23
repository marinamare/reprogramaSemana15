# **Reprograma + Porto Digital - Mulheres em Inovação, Negócios e  Artes (MINAs)** 

## Reprograma Semana 15: Autenticação

Nessas semana eu não consegui desenvolver toda a atividade, mas - ao reassistir a aula - complementei os direcionamentos da atividade com as coisas faladas e feitas em salas de aula. Ao longo dessa semana irei finalizar o que foi pedido. 

### Criar rota autenticada

1. Instalar "jsonwebtoken" via npm install
`$ npm install jsonwebtoken`
Plugin que faz a criptografia do token, assim faz com que consigamos criar rotas autenticadas.

2. Gerar chave pelo https://travistidwell.com/jsencrypt/demo/ e guardar a chave privada
Chave que usa criptografia RSA.

3. Instalar dotenv-safe
`$ npm install dotenv-safe`
Faz controle de variável de ambiente, assim, as pessoas que baixarem o projeto não precisarão configurar a URL do banco ou a chave secreta para conseguir fazer a aplicação rodar. Dessa forma também é possível que, ao subir no servidor, sejam utilizadas as chaves geradas pelo servidor.

4. Criar arquivo .env.example e .env, ambos com chave chamada SECRET
`$ SECRET=chave_rsa_aqui_sem_aspas`
.env.example é um template, no fala quais variáveis de ambiente são obrigatórias.
O conteúdo da variável "SECRET" é setada dentro do .env (que é o arquivo que será efetivamente lido localmente) foi a chave que usa criptografia RSA, criada no passo 2.
As variáveis de ambiente setadas no .env são acessadas pelo comando process.env

5. Carregar as variáveis de ambiente no projeto, no arquivo tarefasRoute.js
`$ require('dotenv-safe').config();`
É uma boa prática colocar esse acesso no arquivo de app, que inicializa tudo e está na raiz do projeto. Assim, não é preciso replicar em todos os outros lugares. 


6. Criar variável contendo a SECRET no arquivo de controller, que contém as funções a serem autenticadas;
`$ const secret = process.env.SECRET`
`process.` acessa uma variável global situada no arquivo `env.` `SECRET.` acesso a um objeto que tem uma chave chamada SECRET. 


8. Criar método de autenticação em `getAll`

no VSCode, no método ao qual queremos conferir autenticação:
`$ const authHeader = req.get(authorization)`

no postman
Ainda sem termos gerado o TOKEN, podemos gerar um header que faça com que os dados de determinada URL precisem ser autorizados antes de serem acessados. Por isso, no headers de autorização colocamos o termo genérico ABCD_TOKEN.
Por padrão, quando estamos falando de criação de autenticação, tem um Header que se chama Authorization. 

9. Pegar o header de autorização e enviar uma mensagem de erro 401 quando vir vazio
No postman, acessar a aba de Headers. 

*Content-Type: application/json*  header que seta o tipo de conteúdo que está sendo passado pela API.

Variável que pega o token do header Authorization setado no postman:
`$ const authHeader = request.get('authorization');`


10. Passar bearer token no header de autenticação via Postman
Anna procurou bastante mas não encontrou respostas muito concretas sobre o que é o Bearer, parece ser um padrão para passar tolkens na web. É uma palabra aleatória para dar um pouco mais de segurança ao token contra ataques que utilizam táticas hackers de brute force.
`$ Bearer TOKEN_JWT_AQUI`

11. Pegar, no controller, o token do Postman ignorando o "Bearer" padrão da web mas que não faz parte do token
Utilizar o método split(' ') que separa a String "Bearer TOKEN" a partir do espaço dado entre eles. Assim, transformando a String em uma array com dois objetos. A fim de acessar o TOKEN em si, utilizamos o número que corresponde ao seu índice dentro da array: [1]
`const token = authHeader.split(' ')[1]`

12. Verificar token JWT e enviar uma mensagem de erro 403 caso seja inválido
Existem dois códigos http para erros de autenticação
401 significa Unauthorized, utilizado comumente quando nenhum header de autorização é enviado

&
403 Forbidden, significa proibido. é mais comumente utilizado quando é enviado um header que não é válido.

depois da constante authHeader e antes da const token, criar o código que vai conferir se existe um header Authorization setado e, caso não haja, vai enviar uma mensagem de erro:
`$ if (!authHeader){ return res.status(401).send('Kd os headers, parça?')}`

antes de prosseguir, criar uma constante que acesse a biblioteca jwt depois da constante SECRET: 
`const jwt = require('jsonwebtoken')`

depois do token criar o método da biblioteca jwt que confere se o token é válido, chamado verify, que recebe como parâmetro o token, a chave e uma função callback. 
precisa saber o token que ele vai verificar
pede a chave secreta para decodificar token
se ele não conseguir significa que o token não foi gerado com o nosso segredo, de forma que podemos recusá-lo com um early return.
`$ jwt.verify(token, SECRET, (error) => { if(erro){ return res.status(403).send('Nope')} return res.status(200).send(o que a função retorna)});`

### Criar rota para criar colaboradoras

1. Criar rota para criar usuária em colaboradorasRoute.js
`$ router.post('/', controller.create);`

2. Criar model de colaboradoras com id, nome, email e senha

3. Criar método no controller para criar colaboradora

4. Criar uma colaborada de teste via Postman

### Criptografar senha das colaboradoras

1. Instalar bcrypt
`$ npm install bcrypt`

2. Fazer require do bcrypt no `colaboradorasController.js`
`$ const bcrypt = require('bcrypt');`

3. Gerar hash com senha recebida no body da request
`$ bcrypt.hashSync(request.body.senha, 10);`

4. Criar nova colabora no banco com a senha hasherizada e o login (email) recebido no body da request

### Criar rota de login

1. Criar rota de login em `colaboradorasRoute.js`
A rota precisa ser um post porque iremos enviar o email e a senha para o login pelo body
`$ router.post('/login', controller.login);`

2. Buscar colaboradora a partir do email recebido na request, e mostrar um erro 404 caso não encontre
`$ Colaboradoras.findOne({ email: req.body.email }, function(error, colaboradora) {...}`

3. Comparar senha de colaboradora encontra com a senha recebida via request, e mostrar um erro 401 caso seja diferente
`$ bcrypt.compareSync(request.body.senha, colaboradoraEncontrada.senha);`

4. Fazer require do plugin JWT
`$ const jwt = require('jsonwebtoken');`

5. Importar SECRET e gerar token JWT a partir do nome e secret e devolver na request
`$ jwt.sign({ name: colaboradora.name }, SECRET);`

6. Bater na rota `getAll` via Postman com o token gerado
