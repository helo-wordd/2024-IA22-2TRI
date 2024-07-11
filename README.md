# Iniciando um projeto Node.js com TypeScript

- Abra o seu navegador de preferência, crie uma conta no https://github.com.
- Crie um repositório utilizando o botão verde na parte esquerda da tela. Deixe o repositório público, e deixe a caixa "Add a README file" (adicionar um arquivo README) habilitada.
- Agora, em seu computador, crie uma pasta para o projeto.
- Abra o VS Code (Visual Studio Code) e pressione o botão "Clone Git Repository" (Clonar Repositório Git). Se o seu VS Code não possuir esta opção, atualize-o.
- Vá até o seu navegador, copie (Ctrl+C) o endereço da página onde criou o repositório e cole (Ctrl+V) na caixa de texto na parte de cima da tela do VS Code.
- Escolha a pasta que você criou como a pasta a ser utilizada para o projeto. Após isso, clique em Open para abrí-la.
- Após ter aberto o projeto pelo VS Code, abre o terminal do VS Code, pressionando Ctrl e a tecla embaixo do esc (pode ser aspas ou outro botão, dependendo do teclado).
- Copie a caixa de texto abaixo utilizando o botão que parece dois quadrados, no canto superior direito da caixa.
- Cole o text no terminal que apareceu na parte debaixo do VS Code e aperte enter. Isso irá executar os comandos, iniciando o npm, e instalando recursos necessários.

```bash
npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
npx tsc --init
mkdir src
```

- Após isso, selecione a pasta src, clique com o botão direito do mouse e selecione "New File", que deve ser a primeira opção.
- Nomeie este arquivo como "app.ts"

## Configuranado o `tsconfig.json`

- Abra o arquivo tsconfig.json, que os comandos npm devem ter criado.
- Selecione o texto do arquivo e pressione Ctrl+F. Isso irá abrir uma caixa de texto no canto superior direito da tela e você deve escrever "outDir". Selecione a linha inteira clicando nela com o botão esquerdo do mouse três vezes e apague-a.
- Copie a caixa de texto seguinte e cole no lugar da linha apagada.

```json
   "outDir": "./dist",
   "rootDir": "./src",
```

- Você pode apagar as linhas comentadas (as que possuem // no começo) mas isso não é necessário. Se você não as apagou, ignore-as.
- Seu arquivo tsconfig.json deve estar assim (Ignore a diferença do "target". Ele muda dependendo da versão de javascript instalada):

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

## Configurando o `package.json`

Adicione o seguinte script ao seu `package.json`

```json
"scripts": {
  "dev": "npx nodemon src/app.ts"
}
```

## Criando arquivo inicial do servidor

Adicione o seguinte código ao arquivo `src/app.ts`

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

## Inicializando o servidor

```bash
npm run dev
```

Se tudo ocorrer bem, você verá a mensagem `Server running on port 3333` no terminal.

## Testando o servidor

Abra o navegador e acesse `http://localhost:3333`, você verá a mensagem `Hello World`.
