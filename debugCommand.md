I have found it a lot easier when debugging discord bots to have a debug command to help debug my code than to have to restart the bot over and over again just to place a console.log()
so here is a command to help with that
if you have a command handler great it helps a lot but if you dont you still need the client passed, message, and args
The way I get my args and command is like this
```js
const Mention = new RegExp(`^<@!?${client.user.id}> `);
        prefix = message.content.match(Mention) ? message.content.match(Mention)[0] : prefix;

        if (message.content.indexOf(prefix) !== 0) return;

        const args = message.content.slice(prefix.length).trim().split(/ +/g);
        const command = args.shift().toLowerCase();
```

Now if you have the client object, message object, and args array we can continue with the code for the command

```js
try {
                let depth = 1
                if (args[0].startsWith('d') && args[0].length >= 2) {
                    depth = args[0].slice(1).trim();
                    args.shift();
                }
                const code = args.join(' ');
                if (!args[0]) return message.reply('Please specify the object or function');
                let result = await eval(code);

                if (typeof result === 'object' && result !== null) {
                    result = util.inspect(result, {
                        depth: depth,
                        compact: false
                    });
                }

                await message.channel.send(`${/*Date.now() - message.createdTimestamp*/ client.ws.ping}ms` + `\n` + `${'```js' + '\n' + result + '\n' + '```'}`);
            } catch (err) {
                const ErrorEmbed = new EmbedBuilder()
                    .setAuthor({
                        name: 'An Error Occurred'
                    })
                    .setDescription(`${'```ml' + '\n' + `Error: "` + err + '",' + '\n' + 'Status: "Connected..."' + '\n' + '```'}`)
                    .setColor('Red');
                message.reply({
                    embeds: [ErrorEmbed]
                });
                console.log(err)
            }
```
This uses the eval function to execute the code provided as the arguments
I also say if the first argument startswith d then to let depth = to the number provided
This helps with discord api character limit
I also say that if the result is typeof object than use the util.inspect which lets us help with the formatting of the JavaScript Object
The depth saves room from hierarchies of objects eg if we don't specify a depth then this is what is returned
```js
{
  cookie: {
    foo: [object Object]
  }
}
```
But if we say a depth of 2 or !export d2 \<code\>
```js
{
  cookie: {
    foo: [object Object]
  }
}
```
