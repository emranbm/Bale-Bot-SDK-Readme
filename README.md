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
const FileMessage = Platform.FileMessage;
const File = Platform.File;
const BotStatus = Platform.BotStatus;
const User = Platform.User;
const Conversation = Platform.Conversation;
```
### Instantiate your bot
```javascript
let bot = new NasimBot("Your Token");
```
Now your index.js should look something like this:
```javascript
const Platform = require("nasim-bot");

const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;
const FileMessage = Platform.FileMessage;
const File = Platform.File;
const BotStatus = Platform.BotStatus;
const User = Platform.User;
const Conversation = Platform.Conversation;

let bot = new NasimBot("Your Token");
```
### Next
Now your code environment is ready. You can ```hear``` for an individual message. Make ```conversation```s and talk to your users, and so on and so forth...  
Discover more and more from [samples](#samples).
## Samples
### Hears sample
You can hear for a message with a particular content and respond to it.  
The ```hears``` method takes two arguments:
* A string array of expected texts in receiving messages to match. (Or alternatively a [Sensitive](#sensitive) object)
* A callback function in type of ```(message, responder) => {}``` that takes two arguments:
    * **message**: The matched received [Message](#message) object.
    * **responder**: See [Responder](#responder).
```js
"use strict";

const Platform = require("nasim-bot");
const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;

let bot = new NasimBot("Your Token");

bot.hears(['whats your name', 'name', 'name?'], (message, responder) => {
    responder.reply("My name is samplebot!");
});
```
### Conversation sample
Not all activities are limited to just ```hear```ing for a message and respond to it; As sometimes there is a need to make a __statefull__ conversation. Using this platform, you can make conversations with users as follows:
   1. Create a [Conversation](#conversation) object.  
   1. Determine what makes the conversation to trigger and get started.  
   1. Determine what states the conversation would take, and the [behavior of each state](#behavior-of-each-state).  
   1. Set the created conversation to your ```bot```.
```js
"use strict";

const Platform = require("nasim-bot");
const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;
const BotStatus = Platform.BotStatus;
const User = Platform.User;
const Conversation = Platform.Conversation;

let bot = new NasimBot("Your Token");

let conv = new Conversation(); // Step 1

let tracer = conv.startsWith(["lets talk"]); // Step 2
tracer.then((message, session, responder) => {
    //STATE 0
    //The first state definitely matches with the "starts with" sensitive. In this case: "lets talk"
    responder.reply("OK. Whats your name?");
    //Go to the next state
    session.next();
}).then((message, session, responder) => {
    //STATE 1
    if (message.text.length < 7) {
        responder.reply("Nice name. I like " + message.text);
        //Ok. go to the next state...
        session.next();
    } else {
        responder.reply("What a long name! Give me a shorter name! :|");
        // Don't call session.next() to remain in the current state.
    }
}); // Step 3

bot.setConversation(conv); // Step 4
```
#####Behavior of each state:
In each state, the user sent ```message```, the ```session``` object, and the ```responder``` object are given as the input.  
* **message**: The message that the user has sent in such state.
* **session**: The object to handle state-based operations like:
   * next(): Go to the next state. (==> next message will be catched by the next state)
   * reset(): Reset the state of the conversation. (==> like once the conversation is not **triggered** yet)
   * putToSessionMemory: Put a custom key-value pair to the session memory to use in next states.
   * getFromSessionMemomry: Get the value by its key.
* **responder**: See [Responder](#responder)

### Send sample
You can send a message to a user initially. See the code example below:
```js
"use strict";

const Platform = require("nasim-bot");
const NasimBot = Platform.NasimBot;
const TextMessage = Platform.TextMessage;
const User = Platform.User;
const Conversation = Platform.Conversation;

let bot = new NasimBot("Your Token");
let msg = new TextMessage("Hi, I'm connected :)", new User(123 /*user id*/, "321" /*user access hash*/));
bot.send(msg);
```
In this case, you need to have the user (or his ```id``` and ```accessHash```) to contact him. See the [User](#user) section for more details.  
There are also other message types beside the TextMessage; See the [Message](#message) section for more details.

### Default reaction
There are certainly some situations that the received message to your bot, does not match any of the registered ```conversation```s, ```hears```s, etc... . For these situaions, you can set a **default callback** that gets called when such a message arrives. See the example below:
```js
"use strict";

const Platform = require("../../../index");

const NasimBot = Platform.NasimBot;

let bot = new NasimBot("Bot Token");

bot.hears(["hello", "Hi"], (message,responder) => {
    //The user said something like hello!
});

bot.setDefaultCallback((message,responder) => {
    //The user said something unexpected!
});
```
## API classes and models
### Sensitive
In [Hears sample](#hears-sample), we learned how to respond to a specific message. (For example saying your bot name in response of the user asking "what's your name")  
So, how can we ```hear``` for a message that is not distinguishable by text. For example we want to ```hear``` for a photo, and in response of that, say "thank you for sharing pics with me!"?
```Sensitive```s are objects that are sensitive to an individual sort of message. The ```Sensitive``` intself is an __interface__ with one method called ```match``` that accepts [Message](#message) as the argument and returns a boolean.  
In the above example, we can use a sensitive like this:
```js
class PhotoMessageSensitive implements Sensitive {

    match(message: Message): boolean {
        if (message instanceof PhotoMessage)
            return true;
        else
            return false;
    }

}
```
And now we can hear for photo messages like this:
```js
bot.hears(new PhotoMessageSensitive(), (message,responder) => {
   responder.reply("thank you for sharing pics with me!");
});
```
### Message
There are variaty of message types that you can send/receive, like:
* TextMessage
* FileMessage
   * PhotoMessage
   * AudioMessage
   * VideoMessage

Note that:
* ```FileMessage``` class is not abstract. So feel free to instantiate and send ```FileMessage```s.
* Unfortunately it's not possible to **upload** a local file and send it as a message. The only thing your bot can do with ```FileMessage```s (and its subclasses), is to receive a ```FileMessage``` from a user, and send it to someone else. You can also [save received messages](#saving-received-messages) to a database or text file or etc., and send it in the future.

#### Saving received messages
All [Message](#message)s have a method named ```getJsonObject``` that translates the message object to a light-weight object with the required attributes. Save the object wherever you want.  
On the other hand, each ```Message``` object has a method named ```manipulateFromJsonObject``` that accepts such an object you saved before, and manipulates the message object with that. So you can send it to a user.  
Why do we talk, when we can sense the code!  
Assume a bot that shows the last received photo from users.
```js
bot.hears(new PhotoMessageSensitive(), (message, responder) => {
   // I have received a PhotoMessage
   // Save it to my collection of images.
   mySampleDatabase.save(message.name, message.getJsonObject());
});

bot.hears(["last photo"], (message, responder) => {
   let savedObj = mySampleDatabase.selectLast();
   let photoMsg = new PhotoMessage();
   photoMsg.manipulateFromJsonObject(savedObj);
   responder.reply("I've received this pic from someone recently:");
   responder.reply(photoMsg);
});
```
#### Usage
### Responder
### Conversation
### User
