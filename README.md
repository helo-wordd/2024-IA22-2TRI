# Tutorial - Servidor Utilizando NodeJS

- Escolha o nome que deseja para o seu projeto e crie uma pasta com este nome.
- Abra o VS Code (Visual Studio Code). Escolha como pasta para o projeto a pasta que acabou de criar.
- Instale `nodejs` em seu computador. Se já instalou isto ou estiver em um dos computadores de um laboratório de informática do IFC, ignore este passo.
- Abra o terminal do VS Code.

> *Geralmente, o terminal pode ser aberto pressionando `Ctrl + [tecla abaixo do esc]`. Se isto não funcinar, vá até o menu no canto superior esquerdo, clique em terminal (penúltima opção da esquerda para a direita) e abra um novo terminal.*

- Copie a caixa de texto abaixo utilizando o botão no canto superior direito da caixa. (o botão é similar a 2 quadrados)
- Cole (Ctrl+V ou Ctrl+Shift+V) o texto no terminal que apareceu na parte inferior da janela do VS Code e aperte enter. Isso irá executar os comandos, iniciando o npm, e instalando recursos necessários.

```bash
npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
npx tsc --init
mkdir src
```

> *Espere por alguns segundos. Isto pode demorar um pouco.*

- Após isso, selecione a pasta src, clique com o botão direito do mouse e selecione `New File` ou `Novo Arquivo`, que deve ser a primeira opção.
- Nomeie este arquivo como `app.ts`

## Configurando `tsconfig.json`

- Abra o arquivo tsconfig.json, que os comandos npm devem ter criado.
- Apague o texto neste arquivo (Ctrl + A; Delete)
- Copie a seguinte caixa de texto e cole no arquivo `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2017",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## Configurando `package.json`

- Dentro de `package.json`, embaixo da linha `"scripts"`, coloque uma vírgula no final da linha `"test"` e adicione a seguinte linha à linha abaixo de `"test"`:

```json
  "dev": "npx nodemon src/app.ts"
```

## Criando o Arquivo Inicial do Servidor

- Copie e cole a seguinte caixa de texto dentro de `app.ts`:

```typescript
import express from 'express';
import cors from 'cors';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

## Inicializando e Testando o Servidor

- Execute o seguinte comando no terminal do VS Code:

```bash
npm run dev
```

- Se tudo ocorrer bem, você verá a mensagem `Server running on port 3333` no terminal.
- Abra o navegador que desejar e acesse o link `http://localhost:3333`, você deve ver a mensagem `Hello World` no canto superior esquerdo da tela.

## Configurando o Banco de Dados

- Crie um arquivo chamado `database.ts` dentro da pasta `src`.
- Copie o seguinte código e cole dentro do arquivo `database.ts`:

```typescript
import { open, Database } from 'sqlite';
import sqlite3 from 'sqlite3';

let instance: Database | null = null;

export async function connect() {
  if (instance) return instance;

  const db = await open({
     filename: './src/database.sqlite',
     driver: sqlite3.Database
   });
  
  await db.exec(`
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT,
      email TEXT
    )
  `);

  instance = db;
  return db;
}
```

## Adicionando o Banco de Dados ao Servidor

- Apague tudo dentro do arquivo `app.ts`.
- Copie e cole o seguinte código ao `app.ts`:

```typescript
import express from 'express';
import cors from 'cors';
import { connect } from './database';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());
app.use(express.static(__dirname + '/../public'))

app.post('/users', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;

  const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

  res.json(user);
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

## Editar e Remover Usuários

- Copie e cole o seguinte código ao final do arquivo `app.ts`:

```typescript
app.put('/users/:id', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;
  const { id } = req.params;

  await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [id]);

  res.json(user);
});

app.delete('/users/:id', async (req, res) => {
  const db = await connect();
  const { id } = req.params;

  await db.run('DELETE FROM users WHERE id = ?', [id]);

  res.json({ message: 'User deleted' });
});
```

## Adicionando html estático

- Crie uma pasta chamada `public` no diretório principal (fora de `src`).
- Crie um arquivo dentro da pasta `public` e chame-a de `index.html`.
- Copie e cole o seguinte código dentro do arquivo `index.html`:

```HTML
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <form>
    <input type="text" name="name" placeholder="Nome">
    <input type="email" name="email" placeholder="Email">
    <button type="submit">Cadastrar</button>
  </form>

  <table>
    <thead>
      <tr>
        <th>Id</th>
        <th>Name</th>
        <th>Email</th>
        <th>Ações</th>
      </tr>
    </thead>
    <tbody>
      <!--  -->
    </tbody>
  </table>

  <script>
    // 
    const form = document.querySelector('form')

    form.addEventListener('submit', async (event) => {
      event.preventDefault()

      const name = form.name.value
      const email = form.email.value

      await fetch('/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, email })
      })

      form.reset()
      fetchData()
    })

    // 
    const tbody = document.querySelector('tbody')

    async function fetchData() {
      const resp = await fetch('/users')
      const data = await resp.json()

      tbody.innerHTML = ''

      data.forEach(user => {
        const tr = document.createElement('tr')
        tr.innerHTML = `
          <td>${user.id}</td>
          <td>${user.name}</td>
          <td>${user.email}</td>
          <td>
            <button class="excluir">excluir</button>
            <button class="editar">editar</button>
          </td>
        `

        const btExcluir = tr.querySelector('button.excluir')
        const btEditar = tr.querySelector('button.editar')

        btExcluir.addEventListener('click', async () => {
          await fetch(`/users/${user.id}`, { method: 'DELETE' })
          tr.remove()
        })

        btEditar.addEventListener('click', async () => {
          const name = prompt('Novo nome:', user.name)
          const email = prompt('Novo email:', user.email)

          await fetch(`/users/${user.id}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, email })
          })

          fetchData()
        })

        tbody.appendChild(tr)
      })
    }

    fetchData()
  </script>
</body>

</html>
```

- Rode `npm run dev` em seu terminal e abra em um navegador o link `localhost:3333`.
- Agora você pode adicionar, editar, e remover contas de usuários utilizando seu navegador.

