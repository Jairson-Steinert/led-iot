# Controle de LED Arduino com Node.js, WebSocket e ngrok

Este projeto permite controlar um LED conectado a um Arduino por meio de uma interface web. O servidor Node.js utiliza o Express para servir arquivos estáticos, o WebSocket para comunicação em tempo real e realiza requisições HTTP para o Arduino, que liga ou desliga o LED.

## Pré-requisitos

- [Node.js](https://nodejs.org/) instalado.
- Arduino com conexão Ethernet (ou shield Ethernet) configurado na rede local (por exemplo, IP `192.168.1.7`).
- ngrok instalado (opcional, para expor o servidor local à internet).
- Dependências do Node.js (Express, ws e outras listadas no `package.json`).

## Estrutura do Projeto

```bash
│ LED-IoT
│   ├──node_modules
├── public
│   ├── index.html
├── package-lock.json
├── package.json
├── server.js
├── arduino.txt
```

> **Observação:** O código do Arduino(arquivo arduino.txt) também precisa estar configurado para responder às requisições HTTP (exemplo: `/led/on` e `/led/off`).

## Configuração do Projeto

1. **Clone ou baixe o projeto:**
   ```bash
   git clone <URL-do-repositório>
   cd seu-projeto
   ```
    - Em server.js configura o hostname com o seu endereço de IP local

```javascript
function sendCommandToArduino(command) {
  const options = {
    hostname: 'xxx.xxx.x.x', // IP do Arduino
    port: 80,
    path: `/led/${command}`,  // Ex.: /led/on ou /led/off
    method: 'GET'
  };
}
```

2. **Instale as dependências do Node.js:**
   ```bash
   npm install
   ```

3. **Verifique a conexão com o Arduino:**
- * Certifique-se de que o Arduino está na mesma rede local e configurado para responder às requisições HTTP conforme o código fornecido.

## Executando o Projeto Localmente

1. **Inicie o servidor Node.js:**
   ```bash
   node server.js
   ```
   O servidor rodará na porta `8080`.

2. **Acesse a aplicação:**
   - Abra o navegador e acesse `http://localhost:8080`.

## Expondo o Servidor com ngrok (para acesso remoto)

Para acessar o projeto de fora da sua rede local sem expor diretamente seu IP, siga os passos abaixo:

### 1. Configurar o ngrok

- **Instale o ngrok** (caso ainda não tenha instalado):
  - Por exemplo, via Chocolatey:
    ```bash
    choco install ngrok
    ```
- **Adicione seu authtoken:**
  - No site [ngrok.com](https://ngrok.com) crie sua conta, copie seu authtoken e execute:
    ```bash
    ngrok config add-authtoken <seu_authtoken>
    ```

### 2. Inicie o Túnel

- Com o servidor Node.js rodando na porta 8080, abra um terminal e execute:
  ```bash
  ngrok http 8080
  ```
- O ngrok gerará uma URL pública, por exemplo, `https://xxxxxxxx.ngrok.io`.

### 3. Atualize a URL do WebSocket no Cliente

- No arquivo `public/index.html`, localize a linha onde o WebSocket é instanciado:
  ```javascript
  const ws = new WebSocket(`ws://${window.location.hostname}:8080`);
  ```
- Substitua-a pela URL pública do ngrok:
  ```javascript
  const ws = new WebSocket('wss://xxxxxxxx.ngrok.io');
  ```

### 4. Acesse a Aplicação Remotamente

- Abra a URL pública fornecida pelo ngrok no seu navegador:
  ```
  https://xxxxxxxx.ngrok.io
  ```

## Testando a Comunicação

- **No servidor Node.js:** Observe os logs para confirmar que as mensagens via WebSocket estão sendo recebidas.
- **No Arduino:** Utilize o monitor serial para verificar se os comandos (como "LED ligado" ou "LED desligado") estão sendo executados conforme esperado.

## Desinstalando o ngrok (opcional)

Se desejar remover o ngrok do seu sistema, execute:
```bash
choco uninstall ngrok
```
Além disso, você pode remover o arquivo de configuração localizado em:
```
C:\Users\<seu_usuario>\AppData\Local\ngrok\ngrok.yml
```

## Considerações Finais

- **Segurança:** Ao expor seu servidor via ngrok, lembre-se de adotar medidas de segurança, como o uso de conexões seguras (WSS) e autenticação.
- **Ambiente de Produção:** Para uma solução mais robusta, considere hospedar seu servidor em nuvem ou usar VPN.
