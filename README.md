#Bale Bot SDK
By this SDK, you can develop [Bale](https://bale.ai/) bots easily with Node.js.  
##Requirements
You need to have installed [node](https://nodejs.org) and [npm](https://www.npmjs.com) on your system.  
##Create a bot like a boss!
###Talk to BotFather
Get a bot token from Bale BotFather. You can find Bale BotFather by this id: <b>@bot-father</b>  
###Prepare your environment
```bash
$ mkdir mybot
$ cd mybot
$ npm init
```
###Install the SDK
```bash
$ npm install balebot
```
###Require it
Create an index.js file and inside it:
```javascript
const SDK = require("balebot");
```
**Good news:** Bale bot SDK is Object-Oriented! There some useful classes provided in the sdk. You are free to take a look at [API classes' overview](#overview).  
But for now, it's enough to know that: **all provided classes can be accessed from the above SDK class statically. For example in the next section you need a class named** ```BaleBot``` **to instantiate. So you may access it in such a way:**  
```js
const BaleBot = SDK.BaleBot;
```
###Instantiate your bot  
As said before, one of the provided classes is ```BaleBot```. This is... your bot!
```javascript
let bot = new BaleBot("Your Token");
```
Now your index.js may look something like this:
```javascript
const SDK = require("balebot");

const BaleBot = SDK.BaleBot;
const TextMessage = SDK.TextMessage;
const FileMessage = SDK.FileMessage;
const User = SDK.User;
const Conversation = SDK.Conversation;
// ... and other usefull SDK classes imported ...

let bot = new BaleBot("Your Token");
```
###Next
Now your code environment is ready. You can ```hear``` for an individual message. Make ```conversation```s and talk to your users, and so on and so forth...  
Discover more and more from [samples](#samples).  
##Samples
###Hears sample
You can hear for a message with a particular content and respond to it.  
The ```hears``` method takes two arguments:
* A string array of expected texts in receiving messages to match. (Or alternatively a [Sensitive](#sensitive) object)
* A callback function in type of ```(message, responder) => {}``` that takes two arguments:
    * **message**: The matched received [Message](#message) object.
    * **responder**: See [Responder](#responder).
```js
"use strict";

const SDK = require("balebot");
const BaleBot = SDK.BaleBot;
const TextMessage = SDK.TextMessage;

let bot = new BaleBot("Your Token");

bot.hears(['whats your name', 'name', 'name?'], (message, responder) => {
    responder.reply("My name is samplebot!");
});
```
###Conversation sample
Not all activities are limited to just ```hear```ing for a message and responding to it; As sometimes there is a need to make a __statefull__ conversation. Using this SDK, you can make conversations with users as follows:
   1. Create a [Conversation](#conversation) object.  
   1. Determine what makes the conversation to trigger and get started.  
   1. Determine what states the conversation would take, and the [behavior of each state](#behavior-of-each-state).  
   1. Set the created conversation to your ```bot```.
```js
"use strict";

const SDK = require("balebot");
const BaleBot = SDK.BaleBot;
const TextMessage = SDK.TextMessage;
const BotStatus = SDK.BotStatus;
const User = SDK.User;
const Conversation = SDK.Conversation;

let bot = new BaleBot("Your Token");

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
In each state, the users sent ```message```, the ```session``` object, and the ```responder``` object are given as the input.  
* **message**: The message that the user has sent in such state.
* **session**: The object to handle state-based operations like:
   * next(): Go to the next state. (==> next message will be catched by the next state)
   * reset(): Reset the state of the conversation. (==> like once the conversation is not **triggered** yet)
   * putToSessionMemory: Put a custom key-value pair to the session memory to use in next states.
   * getFromSessionMemomry: Get the value by its key.
* **responder**: See [Responder](#responder)

###Send sample
You can send a message to a user initially. See the code example below:
```js
"use strict";

const SDK = require("balebot");
const BaleBot = SDK.BaleBot;
const TextMessage = SDK.TextMessage;
const User = SDK.User;
const Conversation = SDK.Conversation;

let bot = new BaleBot("Your Token");
let msg = new TextMessage("Hi, I'm connected :)");
let receiver = new User(123 /*user id*/, "321" /*user access hash*/);
bot.send(msg, receiver);
```
In this case, you need to have the user (or his ```id``` and ```accessHash```) to contact him. See the [Peer](#peer-models) section for more details.  
There are also other message types beside the TextMessage; See the [Message](#message) section for more details.

###Default reaction
There are certainly some situations that the received message to your bot, does not match any of the registered ```conversation```s, ```hears```s, etc... . For these situations, you can set a **default callback** that gets called when such a message arrives. See the example below:
```js
"use strict";

const SDK = require("balebot");

const BaleBot = SDK.BaleBot;

let bot = new BaleBot("Bot Token");

bot.hears(["hello", "Hi"], (message,responder) => {
    //The user said something like hello!
});

bot.setDefaultCallback((message,responder) => {
    //The user said something unexpected!
});
```
##API classes and models
###Overview
There is a list of SDK classes. You can use them in the shape of ```require('balebot').<ClassName>```.  
* **Messages**   
   * TextMessage
   * FileMessage
   * PhotoMessage
   * AudioMessage
   * VideoMessage
   * TemplateMessage (Coming soon...)
* **Requests (Coming soon...)**
   * FileUploadRequest
   * FileDownloadRequest
* **Sensitives**
   * PhotoMessageSensitive
   * FileMessageSensitive
   * TextMessageSensitive
* **Peer models**
   * User
   * Group
* **Other**
   * Conversation
   * Logger  

###Message
There are variaty of message types that you can send/receive, like:
* TextMessage
* FileMessage
   * PhotoMessage
   * AudioMessage
   * VideoMessage

Note that:
* ```FileMessage``` class is not abstract. So feel free to instantiate and send ```FileMessage```s.  

####Saving received messages
All [Message](#message)s have a method named ```getJsonObject``` that translates the message object to a light-weight object with the required attributes. Save the object wherever you want.  
On the other hand, to load the saved object as a ```FileMessage```, FileMessages (and the subclasses) receive a light-weight object in their constructor. So you can instantiate a ```FileMessage``` and send it to a user.  
Why do we talk, when we can sense the code!  
Assume a bot that shows the last received photo from users:
```js
bot.hears(new PhotoMessageSensitive(), (message, responder) => {
    // I have received a PhotoMessage
    // Save it to my collection of images.
    mySampleDatabase.save(message.name, message.getJsonObject());
});

bot.hears(["last photo"], (message, responder) => {
    let savedObj = mySampleDatabase.selectLast();
    let photoMsg = new PhotoMessage(savedObj);
    responder.reply("I've received this pic from someone recently:");
    responder.reply(photoMsg);
});
```

###Requests (Coming soon...)
####File Upload

```js
const FileUploadRequest = SDK.FileUploadRequest;

bot.sendRequest(new FileUploadRequest('/path/to/local file')).then(response => {
    // you can save the location of uploadedfile
    let fileId = response.fileId
    let fileAccessHash = response.accessHash
    let fileٰVersion = response.version

    bot.send(new FileMessage(fileId, fileAccessHash, fileName, fileSize, mimeType, captionText), new User(userId, userAccessHash))
})

```

####File Download

```js
const FileDownloadRequest = SDK.FileDownloadRequest;

bot.sendRequest(new FileDownloadRequest(fileId, fileAccessHash)).then(response => {

    mySampleDatabase.save('file name', response.fileBytes);
})

```

###Sensitive
In [Hears sample](#hears-sample), we learned how to respond to a specific message. (For example saying your bot name in response of the user asking "what's your name")  
So, how can we ```hear``` for a message that is not distinguishable by text. For example we want to ```hear``` for a photo, and in response of that, say "thank you for sharing pics with me!"?
```Sensitive```s are objects that are sensitive to an individual sort of message. The ```Sensitive``` itself is an __interface*__ with one method called ```match``` that accepts [Message](#message) as the argument and returns a boolean determining whether the received message is matched or not.  
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

***Note:** In javascript there are no __Interfaces__ actually. So, by __Interface__ we just mean an object that has the required function implemented. (in this case a method named ```match```) 
###Responder
```responder``` (in receiving message callbacks) is an object to make responding to a message simpler. There are always cases you want to send back a text message to the current peer (user) when you receive a message from him. With ```responder.reply('some text')``` you can send back a text message to the corresponding user.  
Note that you are not forced to use ```responder``` as you can send message in the standard way:
```js
bot.hears(["hello", "Hi"], (message,responder) => {
    // Short version
    responder.reply("Hello! What's goin on?");

    // Standard version
    let msg = new TextMessage("Hello! What's goin on?", responder.peer);
    bot.send(msg);
});
```
###Peer models
Q: What do we mean by peer?  
A: The client that is sending\receiving message to\from the bot.  
####Peer types
There are two types of peer:  
  * User
  * Group (Channels are also assumed as Groups)  

Each peer is identified by its ```id``` and ```acessHash```.
