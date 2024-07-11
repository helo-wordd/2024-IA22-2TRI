# Iniciando um projeto Node.js com TypeScript

- Abra o firefox ou o seu navegador de preferência, crie uma conta no https://github.com.
- Crie um repositório utilizando o botão verde na parte esquerda da tela. Deixe o repositório público, e deixe a caixa "Add a README file" (adicionar um arquivo README) habilitada.
- Agora, em seu computador, crie uma pasta para o projeto.
- Abra o VS Code (Visual Studio Code) e pressione o botão "Clone Git Repository" (Clonar Repositório Git). Se o seu VS Code não possuir esta opção, atualize-o.
- Vá até o seu navegador, copie (Ctrl+C) o endereço da página onde criou o repositório e cole (Ctrl+V) na caixa de texto na parte de cima da tela do VS Code.
- Escolha a pasta que você criou como a pasta a ser utilizada para o projeto. Após isso, clique em Open ou Abrir para abrir 
Crie um diretório para o projeto e acesse-o pelo vscode, abra o terminal e siga os passos abaixo.

```bash
npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
