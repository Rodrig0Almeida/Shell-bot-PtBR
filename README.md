# shell-bot

This is a fully functional shellrunner [Telegram bot][]. You tell it a
command, it executes it and posts the live output. You can send input to the
command by replying to the output messages.

It's a fairly complex example, because it actually appears to the
command as a terminal, interprets escape sequences and **it will
update messages if their lines get touched**. This means interactive
programs such as wget should work naturally, you should see the
status bar update.

The bot also allows files to be uploaded or downloaded, and also
has a simple text editor available for convenience.

Here's an example of the bot running `git` to clone a repository:

![Basic tasks](http://i.imgur.com/Xxtoe4G.png)

Here's an example of the bot running alsamixer:

![Alsamixer with keypad](http://i.imgur.com/j8aXFLd.png)

This bot demonstrates a great part of [Botgram][]'s API.

**Note:** Due to the tight integration, running this bot on Windows is
currently *not* supported.

## Install

First install [node-pty dependencies](https://github.com/Microsoft/node-pty#dependencies). For example, if you're in Ubuntu/Debian:

~~~
sudo apt install -y make python build-essential
~~~

If you're using fedora instead:
```
sudo dnf install -y python
sudo dnf group install -y "C Development Tools and Libraries" 
```

Before using this, you should have obtained an auth token for your bot,
and know your personal user's numeric ID. If you don't know what this
means, check out the [blog post][] for a full step-by-step guide.

~~~
git clone https://github.com/botgram/shell-bot.git && cd shell-bot
npm install && npm install axios
~~~

To start the bot:

~~~
node server
~~~

The first time you run it, it will ask you some questions and create
the configuration file automatically: `config.json`. You can also
write it manually, see `config.example.json`.

When started it will print a `Bot ready.` message when it's up and running.
For convenience, you might want to talk to the BotFather and set the
command list to the contents of `commands.txt`.

## Authorization

When first started, the bot will just accept messages coming from your user.
This is for security reasons: you don't want arbitrary people to issue
commands to your computer!

If you want to allow another user to use the bot, use `/token` and give
that user the resulting link. If you want to use this bot on a group,
`/token` will give you a message to forward into the group.

## Proxy server

shell-bot obeys the `https_proxy` or `all_proxy` environment variable
to use a proxy, and supports HTTP/HTTPS/SOCKS4/SOCKS4A/SOCKS5 proxies.
Examples:

~~~ bash
export https_proxy="http://168.63.76.32:3128"
node server

export https_proxy="socks://127.0.0.1:9050"
node server
~~~

**Warning:** For SOCKS proxies, you need to use an IP address (not a DNS hostname).

## Start together with your machine

To automatically start the shell-bot on system boot, you can create a startup script and configure the system to execute it during boot. Here's an example of how to create a startup script for the shell-bot and configure the system to execute it on boot using Systemd on Ubuntu/Debian:

1. Open a terminal and navigate to the shell-bot directory:

   ```
   cd shell-bot
   ```

2. Create a service file for the shell-bot in Systemd:

   ```
   sudo nano /etc/systemd/system/shell-bot.service
   ```

3. Copy and paste the following content into the service file:

   ```
   [Unit]
   Description=Shell Bot
   
   [Service]
   ExecStart=/usr/bin/node /path/to/shell-bot/server.js
   WorkingDirectory=/path/to/shell-bot
   Restart=on-failure
   RestartSec=10s
   User=yourusername
   
   [Install]
   WantedBy=multi-user.target
   ```

   Replace "/path/to/shell-bot" and "/path/to/shell-bot/server.js" with the full path to the directory where you cloned the shell-bot repository. Replace "yourusername" with the username you use to run the bot.

4. Save the file and exit the text editor.

5. Reload the Systemd daemon to read the new service file:

   ```
   sudo systemctl daemon-reload
   ```

6. Enable the service to start the bot on system boot:

   ```
   sudo systemctl enable shell-bot.service
   ```

7. Start the service to test if the bot starts correctly:

   ```
   sudo systemctl start shell-bot.service
   ```

8. Check the status of the service to ensure the bot is running:

   ```
   sudo systemctl status shell-bot.service
   ```

   If everything is working correctly, the status should indicate that the service is active (running) and without errors.

From now on, the shell-bot will start automatically on system boot.

##
[Telegram bot]: https://core.telegram.org/bots
[Botgram]: https://botgram.js.org
[blog post]: https://alba.sh/blog/telegram-shell-bot/
