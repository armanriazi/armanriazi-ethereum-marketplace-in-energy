## A guide to demoing the energy contract

### Prerequisites
This documentation has been intended for readers with a basic understanding on the Solidity smart contract programming language and on basic web developing tools. In order to run,
the demo requires the following software to be installed. For verified functionality, the specified versions are recommended:

> Ubuntu 16.04.2 LTS

> TestRPC, version 3.0.4

> Truffle, version 3.2.1

> Node.js, version 7.9.0

Running an Ethereum client

### Install dependencies

Before the first run, dependencies need to be installed for the test scripts and the status viewer.

```shell
cd scripts/
npm install
```

```shell
cd scripts/status
bower install
```

### Run a deterministic TestRPC session

For demoing purposes, TestRPC is a good choice for a client, for a number of reasons. Firstly, TestRPC creates a new blockchain instance and transactions can be paid with tokens of the
said blockchain. The creator of the TestRPC session gains access to the tokens for free and
therefore transactions can be made without a cost. Secondly, by default, TestRPC is configured
in such a way that there is no block time—instead, blocks are created on demand, whenever
transactions occur. This type of a configuration is well suited for quick testing and demoing.
Finally, TestRPC can be run in deterministic mode. This means that a smart contract’s address,
for example, can be known already before deploying it in the blockchain. This makes it possible to reference the address in scripts made for testing or demoing purposes.

```shell
testrpc -d
```

### Deploy the contract

The smart contracts written in Solidity need to be compiled and deployed to the blockchain.
This can be achieved by using a development environment for Ethereum called Truffle. A simple migration script needs to be created for Truffle, after which the contracts can be deployed
using the following command:

```shell
truffle migrate
```

### Run the issuer script that issues smart meter ownerships

For the demo, a seller and a buyer of electricity are needed. Furthermore, the ownership of a
smart meter needs to be assigned to both of these parties. In our demo, we utilize an approach
where a master key holder has the power to establish ownerships to the system participants.
We establish a master key holder of smart meters for the electricity marketplace and have the
key holder assign ownership to the smart meters.

```shell
cd scripts/
node issuer.js
```

### Open the status view in browser

Without a graphical user interface, none of the process steps can be visually observed in any
way. Therefore, a simple web-browser-based status viewer has been added to the demo appli
cation. It shows the changes in the status of the different entities as a crude HTML table. The
status viewer can be accessed by opening the web page index.html in any web browser.

Open `scripts/status/index.html` in browser.

### Create sell offers

For the purposes of the demo, the market should be populated with sell offers of energy. The
script offer.js is used for this purpose. The script’s optional command line parameter index can
be used to create different pre-populated sell offers. Change the optional index argument to create different pre-populated offers. The default behavior is to choose the offer at index 0 so the index must be within range 0 to 9.
When omitting the index parameter, the script defaults to using index 0.

```shell
node offer [<index>]
```

### Accept offers as a buyer

Any sell offers created earlier can be accepted by the buyer. The script acceptoffer.js can be
used to do so. The optional index parameter of the script works similarly as the parameter earlier specified for the offer.js script and can be used to accept offers. For example, if an offer was
created using the command node offer 4, then running the command node acceptoffer 4 will
make the buyer accept it.

```shell
node acceptoffer [<index>]
```

### Send reports from smart meters (or wait until the report deadline)

Once an offer has been created and accepted, and the scheduled transfer of electricity is due,
both the seller’s and the buyer’s smart meters are expected to report on the successfulness of
the transfer. The Ethereum transactions submitting these reports can be created using the
scripts sellerreport.js and buyerreport.js. The optional index parameter can be used to refer to
different sell offers exactly the same way as in the scripts described earlier. Both scripts, sellerreport.js and buyerreport.js need to be run in order to move an instance of electricity transfer
to its next state in the smart contract.
The reports have a deadline before which they need to be submitted. According to the deadline, the reports must be submitted and written into the blockchain no later than 30 minutes
after the transfer is completed. In the case a transacting party fails to report within the allocated time frame, the smart contract will assume the worst possible economic outcome for the
abstinent party.

```shell
node sellerreport [<index>]
node buyerreport [<index>]
```

### Withdraw money

Once both smart meters have submitted their reports (or the deadline has expired), the assets
stored in the contract can be withdrawn. The withdrawal can be initiated by anyone, in which
case the assets are sent to their rightful owner, as determined by the logic of the smart contract. The script withdraw.js can be used to execute the withdrawal. The optional index parameter can be used to refer to different instances of electricity transfer.

```shell
node withdraw [<index>]
```

* A part of this project was got from an article 

### Smart Contracts

The logic of the electricity market smart contract is defined in the Solidity file ElectricityMarket.sol. The contract defines the public methods for creating sell offers, accepting them, sending smart meter reports and withdrawing assets.

### GUI
The status viewer is a web page which is useful for observing changes in the blockchain while
running the demo. It shows the status of all the created sell offers and the account balances of
the buyer, the seller and the electricity market smart contract. The status viewer can be run by
opening the file index.html in any web browser.
