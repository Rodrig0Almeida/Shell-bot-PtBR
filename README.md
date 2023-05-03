# shell-bot

Este é um bot [Telegram][Telegram bot] totalmente funcional. Você informa um comando, ele o executa e posta a saída ao vivo. Você pode enviar entrada para o comando respondendo às mensagens de saída.

É um exemplo bastante complexo, porque ele realmente aparece para o comando como um terminal, interpreta sequências de escape e **atualizará as mensagens se suas linhas forem tocadas**. Isso significa que programas interativos como o wget devem funcionar naturalmente, você deve ver a barra de status ser atualizada.

O bot também permite o upload ou download de arquivos e possui um editor de texto simples disponível para conveniência.

Aqui está um exemplo do bot executando `git` para clonar um repositório:

![Basic tasks](http://i.imgur.com/Xxtoe4G.png)

Aqui está um exemplo do bot executando alsamixer:

![Alsamixer with keypad](http://i.imgur.com/j8aXFLd.png)

Este bot demonstra uma grande parte da API do [Botgram][].

**Nota:** Devido à integração estreita, executar este bot no Windows atualmente *não* é suportado.

## Instalação

Primeiro, instale as dependências [node-pty](https://github.com/Microsoft/node-pty#dependencies). Por exemplo, se você estiver no Ubuntu/Debian:

~~~
sudo apt install -y make python build-essential
~~~

Se você estiver usando o fedora em vez disso:
```
sudo dnf install -y python
sudo dnf group install -y "C Development Tools and Libraries" 
```

Antes de usar isso, você deve ter obtido um token de autenticação para seu bot e saber o ID numérico do usuário pessoal. Se você não sabe o que isso significa, confira a [postagem do blog][blog post] para obter um guia completo passo a passo.

~~~
git clone https://github.com/botgram/shell-bot.git && cd shell-bot
npm install && npm install axios
~~~

Para iniciar o bot:

~~~
node server
~~~

Na primeira vez que você executá-lo, ele fará algumas perguntas e criará o arquivo de configuração automaticamente: `config.json`. Você também pode escrevê-lo manualmente, consulte `config.example.json`.

Quando iniciado, ele imprimirá uma mensagem `Bot ready.` quando estiver ativo e em execução.
Para conveniência, você pode conversar com o BotFather e definir a lista de comandos para o conteúdo de `commands.txt`.

## Autorização

Quando iniciado pela primeira vez, o bot aceitará apenas mensagens vindas do seu usuário. Isso é por motivos de segurança: você não deseja que pessoas arbitrariamente enviem comandos para o seu computador!

Se você deseja permitir que outro usuário use o bot, use `/token` e dê a esse usuário o link resultante. Se você deseja usar este bot em um grupo, `/token` lhe dará uma mensagem para encaminhar para o grupo.

## Servidor Proxy

O shell-bot obedece à variável de ambiente `https_proxy` ou `all_proxy` para usar um servidor proxy e suporta proxies HTTP/HTTPS/SOCKS4/SOCKS4A/SOCKS5. Exemplos:

~~~ bash
export https_proxy="http://168.63.76.32:3128"
node server

export https_proxy="socks://127.0.0.1:9050"
node server
~~~

**Aviso:** Para SOCKS proxies, você precisa usar um endereço IP (não um nome de host DNS).

## Iniciando junto com o sistema

Para iniciar automaticamente o shell-bot durante a inicialização do sistema, você pode criar um script de inicialização e configurar o sistema para executá-lo durante a inicialização. Aqui está um exemplo de como criar um arquivo de serviço para o shell-bot e configurar o sistema para executá-lo na inicialização usando Systemd no Ubuntu/Debian:

1. Abra um terminal e navegue até o diretório do shell-bot:

   ```
   cd shell-bot
   ```

2. Crie um arquivo de serviço para o shell-bot no Systemd:

   ```
   sudo nano /etc/systemd/system/shell-bot.service
   ```

3. Copie e cole o seguinte conteúdo no arquivo de serviço:

   ```
   [Unit]
   Description=Bot de Shell
   
   [Service]
   ExecStart=/usr/bin/node /path/to/shell-bot/server.js
   WorkingDirectory=/path/to/shell-bot
   Restart=on-failure
   RestartSec=10s
   User=seunome
   
   [Install]
   WantedBy=multi-user.target
   ```

   Substitua "/path/to/shell-bot" e "/path/to/shell-bot/server.js" pelo caminho completo para o diretório onde você clonou o repositório do shell-bot. Substitua "seunome" pelo nome de usuário que você usa para executar o bot.

4. Salve o arquivo e saia do editor de texto.

5. Recarregue o daemon Systemd para ler o novo arquivo de serviço:

   ```
   sudo systemctl daemon-reload
   ```

6. Habilite o serviço para iniciar o bot durante a inicialização do sistema:

   ```
   sudo systemctl enable shell-bot.service
   ```

7. Inicie o serviço para testar se o bot inicia corretamente:

   ```
   sudo systemctl start shell-bot.service
   ```

8. Verifique o status do serviço para garantir que o bot esteja em execução:

   ```
   sudo systemctl status shell-bot.service
   ```

   Se tudo estiver funcionando corretamente, o status deve indicar que o serviço está ativo (em execução) e sem erros.

A partir de agora, o shell-bot iniciará automaticamente durante a inicialização do sistema. 

##
[Telegram bot]: https://core.telegram.org/bots
[Botgram]: https://botgram.js.org
[blog post]: https://alba.sh/blog/telegram-shell-bot/
