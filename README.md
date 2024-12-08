# Project Overview

## Common Section

### msgFormat\wsMsgFormat.ts  
This file is referenced by both the client and the server as a communication template to ensure consistent communication formats.

## Server Section

### server.ts  
The server entry file, using Express to create the HTTP server and initialize various modules.

### src\Account.ts  
Manages account and bot data. The `AccData` class handles account loading, saving, and linking to bot data. The `BotData` class manages individual bot states, login, logout, and other functions.

### src\BotControler.ts  
Each online bot is bound to a `BotController` instance, which is responsible for logging in and controlling the bot. The communication between the Express server and the Minecraft server (such as bot login and control) uses existing npm libraries. In earlier versions, there was also functionality for listening to key presses to control the bot, but this has now been removed.

### src\Cmd.ts  
Listens for and executes commands while the server is running.

### src\common.ts  
A collection of utility functions, currently only containing a "generate random key" function.

### src\WsMsgMaster.ts  
Full name: WebSocket Message Manager Master. In this project, communication parties are divided into master and slave. The master sets up all listeners during initialization, while the slave typically uses one-time listeners (For example, when requesting data, immediately start listening after sending the message, and destroy the listener right after receiving and executing the message). The master also manages the socket instances of multiple slaves, corresponding to their real accounts. The server-side logic for processing client messages is mainly concentrated here, with communication formats standardized using `msgFormat\wsMsgFormat.ts` (Previously, there were master-slave communication between main and child threads, where I created a separate thread for each "server-controlled Minecraft bot instance" to ensure thread safety, but later removed this as it was deemed unnecessary).

### src\InvServer\InvServer.ts  
"Inv" refers to inventory, i.e., the Minecraft bot’s inventory page. Users request a key from the web interface, which they can use to query the bot’s inventory status from the `InvServer`. The `InvServer` sends the inventory data to the user via Socket.IO, which is then rendered on the web interface. (Backend data acquisition and frontend rendering use an open-source npm library, which I only ported.)

### src\ViewServer\ViewServer.ts  
Similar to `InvServer`, but this sends data about the bot's surrounding world state, which is then rendered on the web interface. (Backend data acquisition and frontend rendering use an open-source npm library, which I only ported.)

## Web Section

### public\css  
CSS files.

### public\dist  
This folder should contain the files generated by webpack. However, since the generated files are stored in memory during debugging for convenience, there are no files here.

### public\js\client.ts  
The entry file for the web scripts, containing dynamic content generation and communication initialization. It first guides the user to log in before entering the control interface.

### public\js\login\connectWeb3Wallet.ts  
Connects the Web3 wallet (requires a browser extension like MetaMask).

### public\js\login\authorize.ts  
Handles verification and login. The server provides a `challenge` that the user must sign using their Web3 wallet. The server then verifies that the challenge is signed by the correct address before granting access to the next page.

### public\js\Menu.ts  
The main menu page. The left side contains a menu bar where users can select different pages, and the corresponding DOM elements for the selected page are dynamically generated on the right side.

### public\js\dashboard\BotManager.ts  
The entry file for the bot management page. It automatically requests the status of all bots associated with the account from the server when first opened and updates the page. Currently, it allows users to control bot login/logout, dynamically request the bot’s inventory status, and manage the third-person view, all rendered on the web interface. (Backend data acquisition and frontend rendering use an open-source npm library, which I only ported.)

### public\js\dashboard\InvDrawer.ts  
Handles inventory data retrieval and rendering. (Backend data acquisition and frontend rendering use an open-source npm library, which I only ported.)

### public\js\dashboard\ViewDrawer.ts  
Handles third-person view data retrieval and rendering. (Backend data acquisition and frontend rendering use an open-source npm library, which I only ported.)

### public\js\dashboard\BotInfo.ts  
Each bot has a `BotInfo` instance that manages the associated DOM elements, keys, the bot's current status, `InvDrawer`, `ViewDrawer`, etc.

### public\js\statemachine\Editor.ts  
The entry file for the state machine editor (currently under construction), setting up mouse event listeners and basic processing logic. For example, clicking creates a state, clicking two states connects them (representing a state transition), and deleting states. (The state machine editor has an old version, but I don't think it's user-friendly, so I am making a new one)

### public\js\statemachine\State.ts  
State management class, mainly managing the internal logic of states. For example, what the bot should do in this state, and the relationships between states (which state it may enter from, and which state it may exit to).

### public\js\statemachine\DomManager.ts  
DOM element management class for each state. Each state has a `DomManager` instance dedicated to drawing the page.

### public\js\statemachine\Connection.ts  
State transition connections drawn using SVG (under construction).

## Other

### public\method  
Legacy from an old version, currently not in use. Related to the state machine.

### ata\pc\1.20  
Legacy from an old version, currently not in use. Related to the state machine.

### accountsData.json  
Account information. The "acc save" command saves the account data, and "acc load" loads it. See `src\Cmd.ts` for more details.