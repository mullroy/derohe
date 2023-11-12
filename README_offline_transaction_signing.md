## Sign a transaction using an offline wallet
This README matches the behaviour of a modified release 140

## Introduction
The problem with traditional wallets and how Dero addresses these
shortfalls:
1. How traditional wallets work<br>
   A wallet file (wallet.db) stores your addresses. Each address consists of a public
   and secret part. The public part is what you share with people, so they can
   make payments to you. The secret key is required to unlock and spend the funds
   associated with that address.
   
2. Risk associated with traditional wallets<br>
   If somebody obtains your secret key they can spend your funds. The network will
   process the transaction if the cryptographic signature is correct. The network
   doesn't validate who sent the transaction or from where.
   An attacker will aim to obtain your wallet file in order to steal your secret
   keys, and per extension, your funds. Malware hidden in applications is an easy
   way for hackers to scout your hard drive for wallet files, which they send to
   themselves over your internet link.<br>
   
   Apart from the risk of loosing your funds to an attacker it's also much more likely
   you'll loose your wallet file due to neglect. There are countless horror stories
   of people who have lost billions (yes, with a 'B') worth of Bitcoin due to
   hard drive crashes, hard drives that were formatted to make room for a new
   O/S or game installation or simply old machines that were thrown away.<br>
   
   If you were diligent and made a backup of the wallet file, you have to remember
   which addresses it contains. Each time you create a new addresses it is not 
   automatically added to the backup copy. 
   Upon restoring of an old backup you might discover that it doesn't contain all 
   the new addresses created since the backup. Access to those funds are lost forever.<br>
   
3. BIP39: Mnemonic phrase improvement<br>
   The problem of loosing addresses and their private keys were mitigated with the
   implementation of BIP39 mnemonic seed phrases. A single master key is created.
   All the addresses in that wallet are derived from the master key. To the user
   the master key is presented as a random 24 word (natural language) phrase, 
   called a mnemonic. 
   
   When the wallet is created this phrase must be written down and stored in a secure
   manner. The storage medium must protect against fire and moisture damage.
   
   Do not use your phone to take a picture of the phrase. Synchronisation software 
   could upload your images to the 'cloud' where it can be intercepted in transit
   or on the remote storage. Do not print it out either. In rare cases malware in
   printer firmware has been found to recognise these seed phrases and send it of
   to the hackers.
   
   Paranoia regarding protection of your seed phrase is not unwarrented.
   
   When it's time to restore your addresses in a new wallet all you have to do is
   to type in the mnemonic. The application will convert it to the master key.
   No software backup of the wallet file is required. By initialising the wallet
   with a valid seed phrase all the addresses derived from it can be recreated.
   
   If you have funds in a traditional wallet thats not based on BIP39, you can
   setup a second wallet that uses the BIP39 technology. You can pay the funds
   from the old wallet addresses to the new wallet, thereby transferring the 
   funds to the new wallet.
   
4. Encrypted wallets
   As seen above, BIP39 guards against loosing access to your individual addresses.
   It however does not provide additional protection of loosing the actual wallet
   file to an attacker. 
   On Dero the wallet file can be encrypted with a password. If an attacker obtains
   the wallet file, they will not be able to open it. Your security is based on
   choosing a long, secure password which can withstand a brute force attack. 
      
5. Offline cold storage
   Two computers are required for this setup. One is connected to the internet
   while the other has no network connectivity. 

   The machine without network access is called the offline machine. On it you
   setup an encrypted wallet with mnemonic seed phrase. The machine doesn't have
   a copy of the blockchain.

   The machine with internet access is called the online machine. This machine
   is synchronised with the blockchain.
   
   This process adds two levels of complexity:
   *  How do you view the transactions & balance of an address in the offline wallet?
   *  How do you spend the funds of an address in the offline wallet?
   
   The Dero command line (CLI) wallet overcomes these obstacles as follow:

   Viewing the transaction history
   -------------------------------
   The Dero blockchain is encrypted. None of the transaction data is visible on
   the blockchain. You can't import an address into the online wallet and view
   the activity that occurred on that address. The secret key is required to 
   decrypt the data that contains your transactions.

   Dero requires that your register your address on the network. This allows an
   initial filter of the data so that your wallet client only receives data that
   is related to its address.

   As the online wallet receives the data it will create a separate data file, which
   needs to be copied to the offline wallet for decryption. The decrypted data must
   be copied back to the online wallet where it is imported. The decrypted data
   enables the wallet to extract the transaction data and to compile your account balance.
   
   If the online wallet is somehow stolen it's (almost) no deal - information
   is leaked regarding the balance & transaction history of your address. This is
   clearly not a desireable thing, but at least the attacker will not be able to spend
   the funds, like they would have if the wallet contained the secret (private)
   key as well.
   
   Spend the funds 
   ---------------
   The online wallet can construct a transaction for its address. All the required 
   inputs are contained in this transaction. The transaction is stored in a data file.
   The file must be copied to the offline machine. There the secret key authorises (signs) the transaction. The authorised transaction is copied back to the online wallet and submitted to the network for processing.
   
6. Summary
   Pirate's Treasure Chest wallet gives you access to these features today:
   * BIP39 master seed 
   * Split addresses between two wallets. The offline contains the private keys
     and the online the viewing and spending keys.
   * Encrypted storage of the wallet on disk and encrypted communication
   * All running on an encrypted blockchain

## Software setup
1. You require two PCs, preferrably Intel i5 or faster. About 200mb of hard drive space
   is required if you plan to connect to a remote node. The internet link speed isn't 
   very important if you connect to a remote node. 1mbps or faster is sufficient.
   The machines can run Linux, Mac OS X or MS Windows. For this tutorial Linux is used.
   
2. You can obtain the software from the official Dero website:
   https://dero.io/download.html or a source copy from GitHUB at
   https://github.com/deroproject/derohe
   
   At the moment only the cli (command line) wallet supports offline transaction signing.

   If you've downloaded the Linux CLI (command line interface) install archive, extract
   it as follow:
   $ tar -xvzf dero_linux_amd64.tar.gz
   $ cd dero_linux_amd64
   $ ls
   derod-linux-amd64  dero-miner-linux-amd64  dero-wallet-cli-linux-amd64  explorer-linux-amd64  simulator-linux-amd64  Start.md
   
   We'll use the dero-wallet-cli-* application
   
   To build from source, you'll need to Go language (golang) compiler on your machine
   to compile the software. On a Debian based Linux installation, you can install the
   package as follow:
   # apt-get update
   # apt-get install golang:amd64
   
   If you want to check out a copy of the github source code:
   $ git clone https://github.com/deroproject/derohe
   $ cd derohe/cmd/dero-wallet-cli
   $ go build
  
   The new application is called: dero-wallet-cli
   
3 Offline machine
3.1 First run
  From a terminal console, launch the application: ./dero-wallet-cli --help
  We will use the following command line options:
  --offline - Specify that this wallet is an offline (signing) wallet
  
  --wallet-file - The name of your wallet, i.e. offline.db
  --password - The password with which to encypt the wallet. It needs to 
               be a strong password, which can withstand a password attack,
               but note, you'll have to enter this password regularly, so it
               still needs to be something practical to work with.
               
  --generate-new-wallet - Let the wallet create a new mnemonic seed phrase and address
  or
  --restore-deterministic-wallet - You'll provide the mnemonic seed phrase
  --electrum-seed - Here you'll provide the mnemonic phrase

  An example will be:
  $ ./dero-wallet-cli --offline --wallet-file=offline.db --password=someexamplepw --restore-deterministic-wallet --electrum-seed="your 25 seedphrase words here"
  
  After the wallet starts up the menu will provide you with a couple of options.
  At the top of the menu is a greeting to show you that it is running in offline mode:
    Offline (signing) wallet:
    1. Setup the online wallet with the exported public key
    2. Generate a registration transaction for the online wallet
    3. Sign spend transactions for the online wallet
  
  Select '0' to exit the wallet.
  Check to see if the wallet was saved to disk
  $ ls
  The output must contain your wallet file: offline.db
  
3.2 Second run 
  Now that the wallet is already created, you don't provide the restore & seed CLI options anymore:
  $ ./dero-wallet-cli --offline --wallet-file=offline.db --password=someexamplepw

  Menu options:
  1 Display account Address -- Shows your account address. Share this with people so they can pay you:
    Wallet address : dero1abcdef12345678907j0n6ft4yzlm300fxzz2sg84t28g2cp897f5yqghyx4z3
  2 Display seed -- This prints your mnemonic recovery seed. If somebody obtains this seed phrase, they can restore a wallet and spend all your funds.
  3 Display Keys -- This normally contains the public and secret keys. While you're running in offline mode, an additional entry is display: The 'view only' key. This key is used to set up the online (view only) wallet.
  secret key: 1234567890abcdecbd05d9e1a7f52796da7bc2d931bd0034ee6facef8f46e9be
  public key: 1234567890abcde07af49f3d257520bfb8bde93084a820f55a8e8560272f934201
  View only key - Import the complete text into the online (view only) wallet to set it up:
  viewkey,dero1abcdef12345678907j0n6ft4yzlm300fxzz2sg84t28g2cp897f5yqghyx4z3,1234567890abcde07af49f3d257520bfb8bde93084a820f55a8e8560272f934201,1f9004d20e823de07af49f3d257520bfb8bde93084a820f55a8e8560272f9342290626bd4f1422affe88578e5fbdb825f1d4d6d60bd458402dc18c1614a2395b;20010
  
  4 Generate registration transaction -- In order to use a remote node, i.e. running in 'light mode', where you do not download the full blockchain yourself, the node requires you to register your address. 
  
  Example output:
  Generating registration transaction for wallet address : dero1abcdef12345678907j0n6ft4yzlm300fxzz2sg84t28g2cp897f5yqghyx4z3
  Searched 100000 hashes
  ...
  ...
  Searched 24600000 hashes
  Found transaction:
  Found the registration transaction. Import the complete text into the online (view only) wallet:
  registration,dero1qy0eqpxjp6prmcr67j0n6ft4yzlm300fxzz2sg84t28g2cp897f5yqghyx4z3,010000011f9004d20e823de07af49f3d257520bfb8bde93084a820f55a8e8560272f934201001822f65724584421db9e860e740250450b8192b3036a351c343274253636e21ad69f267b3a23e0f1d271ab17c143be2019710e682d4671258bb5dacd07e958,00000057d2f1c0fa1f00849f863fe296e4933c9ed18666587f7fe0f497c03993;24578
  

4 Online machine
4.1 First run
  From a terminal console, launch the application: ./dero-wallet-cli --help
  We will use the following command line options:
  --remote - Connect to a remote node. This is often called 'light weight mode', since you do 
             not maintain a full copy of the blockchain.
  --wallet-file - The name of your wallet, i.e. viewonly.db
  --password - The password with which to encypt the wallet. It needs to 
               be a strong password, which can withstand a password attack,
               but note, you'll have to enter this password regularly, so it
               still needs to be something practical to work with.
               
  --restore-viewonly-wallet - Set up the wallet with the viewing key obtained from the offline wallet

  An example will be:
  $ ./dero-wallet-cli --remote --wallet-file=viewonly.db --password=someexamplepw --restore-viewonly-wallet
  
  The software will have these 2 prompts:
  Enter wallet filename (default viewonly.db): Just press enter to accept the default
  Enter the view only key (obtained from the offline (signing) wallet): Paste the viewing key here, i.e.:
  viewkey,dero1abcdef12345678907j0n6ft4yzlm300fxzz2sg84t28g2cp897f5yqghyx4z3,1234567890abcde07af49f3d257520bfb8bde93084a820f55a8e8560272f934201,1f9004d20e823de07af49f3d257520bfb8bde93084a820f55a8e8560272f9342290626bd4f1422affe88578e5fbdb825f1d4d6d60bd458402dc18c1614a2395b;20010
  
  If the key is accepted, you'll get this greeting: INFO wallet Successfully restored an online (view only) wallet
  
  After the wallet starts up the menu will provide you with a couple of options.
  At the top of the menu is a greeting to show you that it is running in view only mode:
    Online (view only) wallet:
    1. Register you account, using registration transaction from the offline (signing) wallet.
    2. View your account balance & transaction history
    3. Generate transactions for the offline wallet to sign.
    4. Submit the signed transactions to the network.
  
  Select '0' to exit the wallet.
  Check to see if the wallet was saved to disk
  $ ls
  The output must contain your wallet file: viewonly.db
  
4.2 Second run
  Now that the wallet is already created, you don't provide the restore & seed CLI options anymore:
  $ ./dero-wallet-cli --remote --wallet-file=viewonly.db --password=someexamplepw

  Menu options:
  1 Display account Address -- Shows your account address. Share this with people so they can pay you.
    Note: The address must be the same as that of the offline wallet
    Wallet address : dero1abcdef12345678907j0n6ft4yzlm300fxzz2sg84t28g2cp897f5yqghyx4z3    
  2 Display seed -- This option is not available in the view only wallet.
  3 Display Keys -- Only the public key is displayed. This must match the public key in the offline wallet
  4 Account registration to blockchain -- In order to use a remote node, i.e. running in 'light mode', where you do not download the full blockchain yourself, the node requires you to register your address.
  
    Enter the registration transaction (obtained from the offline (signing) wallet): registration,dero1...,...,...;24578
    Registration TXID 00000057d2f1c0fa1f00849f863fe296e4933c9ed18666587f7fe0f497c03993
    registration tx dispatched successfully

  Note: After the account was registered, the wallet needs to synchronise your account balance.
        In order to accomplish this, interaction with the offline wallet is required.
    
## Using the Online / Offline wallet configuration
  For the demonstration to work effectively, fund your newly created address with some 
  Dero by sending some cents (0.xx) to it, either from an online exchange or from one 
  of your existing wallets with a balance.
  
1. Balance enquiry and transaction history
  The blockchain data is received by the online (view only) wallet. The data needs
  to be decrypted before the information can be processed. The secret key, located 
  in the offline (signing) wallet, is required to accomplish this.
  
  Each time the online wallet receives a block which contains transaction information
  for your address, a part of the information needs to be send to the offline wallet 
  for decryption.
  
  The online wallet will automatically create a file called 'offline_request' in the 
  current working directory, where the wallet is running. 
  
  For testing purposes, if you run the online & offline copies in the same directory,
  on the same machine, then the file created by the online wallet (offline_request)
  will immediately be detected by the offline (signing) wallet and automatically
  processed. If you run a production setup where the two wallets are completely 
  separate, then you'll have to copy the file (offline_request) from the online machine
  to the offline machine yourself.
  
  The prompts are as follow:
  Online wallet:
  The blockchain interaction occurs in the background. After a block is detected this
  text will appear:
    Interaction with offline wallet required. Saved request to: ./offline_request
    Searching for 60 seconds for the response at: ./offline_response
  The 'offline_request' file must be copied to the offline wallet.
    
  Offline wallet:
  After the 'offline_request' file is copied to its home directory, the wallet 
  will detect the presense of the file automatically and process it automatically:
    Found ./offline_request -- new decryption request
    Saved result in ./offline_response
  The 'offline_response' file must be copied back to the online wallet
    
  Online wallet:
    Found a valid response
  
  This exchange happens for each transaction that you receive or spend on your
  address. Your balance will be shown as part of the command prompt. You can 
  view the transaction history under menu entry 13: Show transaction history
    
2. Spend transaction
  In order to spend your hard earned Dero you first need to fund your address, 
  as suggested at the top of this chapter. The online (view only) wallet will 
  pick up the transaction from the remote node. The transaction history and 
  account balance will be updated, after you've decrypted the data files as 
  described above in 5.1
  
  The prompts are as follow:
  Online wallet: 
    Select option '5' to prepare the transaction.
    Enter the destination address, amount, destination port and comment
  After the transaction is confirmed, the wallet prepares all the data and
  saves it to 'transaction' in the current working directory where the 
  wallet is executing.
  
  This file must be copied to the offline wallet and placed in its working 
  directory.
  
  Offline wallet:
    Select optio '5' to tell the wallet to sign the transaction. The output 
    of the wallet is:
      Read 4680 bytes from ./transaction
      Saved signed transaction to ./signed. Return it to the online (view only)
      wallet to complete the transaction.
  
  Online wallet:
    As per instruction, return ./signed to the online wallet. When the wallet 
    detect the file it will print a message:
      Read 6465 bytes from ./signed. The checksum passed. Continue to submit the transaction
      INFO wallet Dispatched tx {"txid": "c3cf9c408b43d7a7a9d2bcd603acdb6e70d89418014546e1bdbba755ac1ce427"}
  
Congratulations, you've successfully created and send a transaction using an offline wallet