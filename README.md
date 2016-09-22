#Nasim Bot Platform
Are you a JS Developer? By this platform, you can develop [Nasim](https://nasim.elenoon.ir/) bots easily.
##Requirements
You should have installed [node](https://nodejs.org) and [npm](https://www.npmjs.com) on your system.
##Create a bot like a boss!
###Get a bot token from Nasim BotFather.
You can find Nasim BotFather by this id: <b>@bot-father</b>
###Prepare your environment
```bash
$ mkdir mybot
$ cd mybot
$ npm init
```
### Install the platform
```bash
$ npm install nasim-bot
```
### Require it
Create an index.js file and inside it:
```javascript
const Platform = require("nasim-bot");
```
### Nasim bot platform is Object-Oriented!
Enjoy the provided classes. Here you are:
```javascript
const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;
const BotStatus = Platform.BotStatus;
const User = Platform.User;
const Conversation = Platform.Conversation;
```
### Instantiate your bot
```javascript
let bot = new NasimBot("Your Token");
```
Now your index.js should look like this:
```javascript
const Platform = require("nasim-bot");

const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;
const BotStatus = Platform.BotStatus;
const User = Platform.User;
const Conversation = Platform.Conversation;

let bot = new NasimBot("Your Token");
```
### Next
Use your ```bot``` methods to ```hear``` for a message, ```conversation``` or ...  
## Samples
### Hears sample
```js
"use strict";

const Platform = require("nasim-bot");
const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;

let bot = new NasimBot("Your Token");

bot.hears(['ok', 'you connected'], (responser, message) => {
    responser.reply("yes I said I'm connected!");
});
```
### Conversation sample
```js
"use strict";

const Platform = require("nasim-bot");
const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;
const BotStatus = Platform.BotStatus;
const User = Platform.User;
const Conversation = Platform.Conversation;

let bot = new NasimBot("Your Token");

let conv = new Conversation();

let tracer = conv.startsWith("Lets talk");
tracer.then((message, session, responser) => {
    //STATE 0
    //The first state definitely matches with the "starts with" sensitive. In this case: "Let's talk"
    responser.reply("OK. Whats your name?");
    //Go to the next state
    session.next();
}).then((message, session, responser) => {
    //STATE 1
    if (message.text.length < 7) {
        responser.reply("Nice name. I like " + message.text);
        //Ok. go to the next state...
        session.next();
    } else {
        responser.reply("What a long name! Give me a shorter name! :|");
        // Don't call session.next() to remain in the current state.
    }
});

bot.setConversation(conv);
```
### Send sample
```js
"use strict";

const Platform = require("nasim-bot");
const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;
const BotStatus = Platform.BotStatus;
const User = Platform.User;
const Conversation = Platform.Conversation;

let bot = new NasimBot("Your Token");

BotStatus.getOnConnect().then(() => {
    console.log("connected to the server.");
    let msg = new TextMessage("Hi, I'm connected :)", new User(123, "321"));
    bot.send(msg);
});
```
