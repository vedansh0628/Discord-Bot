
# ALBot 2.0 Alpha

Alpha version of ALBot 2.0, the spiritual successor
to ALBot. ALBot 2.0 uses Discord.js to interface with
the Discord API, supplanting ALBot's dependency on the
deprecated Discord.py library.

To get started working on the bot, first clone the repository!
Then proceed to the `Dependencies` and `Building and Testing`
sections of this README.

## Dependencies

The only non-npm managed dependency is Node.js.
[Install Node.js here.](https://nodejs.org/en/)
This will also install `npm`.
The necessary versions for `node` and `npm` are:

+ `node`: v16.13.2
+ `npm`: 8.1.2

After installing `node` and `npm`, run the following command:

```bash
npm install
```

### Linting

Running this command will install the node package dependencies.
One of these is ESLint, a linter for Javascript.
If desired, you can install an ESLint extension for your 
development environment to provide code highlighting based on 
the project's linting rules.

+ [Install ESLint Support with VS Code](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
+ [Install ESLint Support with Atom](https://atom.io/packages/linter-eslint)
+ [Install ESLint Support with Sublime Text](https://packagecontrol.io/packages/ESLint)

## Building and Testing

To run and test the bot locally, you will need
to set up a a test bot and a bot testing server in Discord. Only you
and the test bot need to be in this server.

Creating your test bot is a quick process, and just requires
changing some settings in Discord. [Follow the guide linked here to
create your bot](https://discordjs.guide/preparations/setting-up-a-bot-application.html#creating-your-bot). Using its token will be discussed shortly.

Once your bot is created, create a server for bot testing
under your Discord account. Then, [follow this guide to add your bot
to the testing server](https://discordjs.guide/preparations/adding-your-bot-to-servers.html#bot-invite-links).
You will need to give the bot the following permissions:
`bot` and `applications.commands`.

Good work! Now your test bot application is created and waiting
in your server. The last thing you need to do for setup
is create a `config.json` file. 

This file is loaded by the application
at runtime and stores confidential tokens and IDs specific to your bot.
**NEVER COMMIT YOUR config.json!**
If you do, promptly go to Discord developer portal and request new tokens.

To create the `config.json`, first take a look at `config.json.example`.
This file provides the basic schema for your `config.json`. To create
your `config.json`, copy the contents of `config.json.example` into
yours but substitute the following values for each of the keys
in the JSON object as follows:

+ `clientId`: Substitute in the client id provided for your bot application found on 
the [Discord Developer Portal](https://discord.com/developers/applications).
You can find it under the OAuth2 section.
+ `guildId`: Substituted in the guild id for your bot testing server. You can find it
in Discord by right-clicking your server in Discord and then selecting the
"Copy ID" option at the bottom. You will have to enable Developer Mode to 
see this option.
+ `token`: Substitute in the token for your bot application found on the 
[Discord Developer Portal](https://discord.com/developers/applications).

Once your config.json is setup, it's time to run the application!

First, run the following to deploy the commands to the bot:

```bash
npm run deploy-commands
```

Next, launch the bot!

```bash
npm run start
```

If you see a "Ready! Logged in as. . ." message, you've succeeded!

## Architecture

ALBot 2.0's functionality is distributed across several files.
The `src/index.ts` contains the 'main' of the program, and should
be executed using node to launch the bot (`npm run start` or `ts-node src/index.ts`).
The `client` object in `index.ts`
is the bot - that is, it contains all the functionality as properties.

The commands ALBot can perform are stored in the `src/commands/` directory.
Each command is stored in its own `.ts` file with the name of the command
being the file name (e.g. command `/foo` is in `foo.ts`).
All commands implement the `ICommand` inteface defined in `src/icommand.ts`.

Each command object implements the `ICommand` inteface.
`ICommand` describes an inteface with the following attributes:
+ `name`: the name of the command
+ `description`: a description of its purpose
+ `execute(interaction: Interaction)`: a function which
contains the commands functionality.

Dispatching interactions to the proper commands is handled
by the `interactionCreate` event (see `events/interactionCreate.js`).
The commands are compiled into a key-value store at runtime
when the bot is started, with names as keys and the object
as value (see `src/commands.ts`).
Import `commands.ts` to access this store.

The bots interactivity is implemented using an event handling
architecture wherein event handlers are specified by modules
in the `src/events/` directory. Each `.ts` file in `events/` exports
a distinct event handler. The naming scheme mimics that of the 
command files: an event `foo` is handled in `foo.js`.

Each event handler implements the `IEvent` interface type,
which includes the following attributes:
+ `name`: name of the event being handled
+ `once` (optional): true if the command only executes once,
false if it should be run on every ocurrence.
+ `execute(any[]): void | Promise<void>`: the function executed to handle the event

These events are loaded into the `client` object at runtime when
the bot is started.

## Acknowledgement

This project was built with the help of the Discord.js guides.

+ [discord.js](https://discord.js.org/#/)
+ [discord.js guide](https://discordjs.guide/#before-you-begin)