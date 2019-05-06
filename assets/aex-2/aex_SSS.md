# AEX 5: Strategic Semantic Syntax: Contract Interaction Wrapper 
<style>
.markdown-body { max-width: 80% !important; }
</style>
```
AEX: 5
Title: Strategic Semantic Syntax Part 1: Contract Interaction Wrapper
Author: Nikita Fuchs GitHub: @nikita-fuchs
License: BSD-3-Clause
Discussions-To: TBA
Status: Draft
Type: Standards Track
Created: 2019-05-04
References : AEX-2, AEX-4
```

*"If a 14 year old can't use it, your product is not completed."*

## Simple Summary

With regard to three main scenarios of developing with smart contracts, we can have **three levels** of abstraction for our SDKs and frameworks. The simplified syntax would utilize the ACI (Aeternity Contract Interface) generator to automate repetitive tasks and also streamline the development, testing and integration process. Breaking changes would not occur for existing æepps, as they are put in place under the hood of the wrapper syntax, which follows a semantic design strategy.


## Abstract

Currently, the SDK is not aware of any characteristics of any smart contract or any of its functions. Any information is provided through hard coding of names, type info and explicit decoding instructions. Additionally, a lack of a formalized strategic concept in the development of the SDK syntax makes æepp development tedious and hindered by seemingly arbitrary breaking changes through adjustments and every new feature.


## Motivation

The team did an amazing job so far implementing SDK functionalities in an evolutionary way. People from different backgrounds did the best imaginable job to add functionalities to the SDK subsequently and in a way that made the best sense to them. We can be very thankful to have these many talented JS devs in our team! Now, as the project matures, we face at least two challenges: 
- The learnings we make and additions we come up with will amost always lead to breaking changes

- So will implementing further AEX, too, providing advanced functionalities like device-specific behaviour, deep-linking, etc.

Therefor I would like to propose a Strategic Semantic Syntax, that is supposed to wrap everything neccessary to interact with contracts (and later state channels, oracles, and events) in function calls that take only the bare minimum of *semantic* information and leaves the heavy-lifting to the SDK and Framework.

`"What is the bare minimum of information I need for calling a function?" Its name and the parameters!` 

For the semantic part or this AEX, our syntax shouldn't take more than that.

For the strategic part:

All interactions with smart contracts from building till roll-out fall under one of the following three categories:
- Occasional function call
    The contract you are interacting with plays no major role in your application, you just need to read/write something from/to the contract at this one spot real quick. You know the contract's address, the function's name, take care about arity, type info and decoding yourself.
    
- Implementing an existing contract
    Your application is utilizing the smart contract in quite a few places and you don't want to do the heavy lifting all yourself. You know the contracts address as well as either its full source or the functions relevant to you. You do not want to do the rest of the heavy lifting, but just want to call the function and get the result in **one line of code** and prevent errors before they occur.
    
- Building your own dæpp
    You are building an application that heavily utilizes a smart contract you are *currently developing*. Through the iterations, you do not want to refactor your code too much every time something about the contract changes. You also do not want to do the rest of the heavy lifting (decoding calls..), just want to call the function and get the result in **one line of code** and prevent errors from happening. During your workflow, you deploy the contract many times, also on many different networks and you want to make interacting with / switching between **all of them** as seemless as possible. 
    
The thoughts and ideas of this AEX are inspired from the years of suffering from disadvantages of working with Ethereum's web3 SDK and Truffle framework while paying attention to the needs of a young and fast evolving protocol. This is how web3 and truffle, in my humble opinion, performed in the above mentioned scenarios:


|   | Occasional function call | Implementing an existing contract | Building your own dæpp  |
| -------- | -------- | -------- | -- |
| web3     | 3/10 - need to instantiate contract and provide ABI (ACI equivalent)     | 6/10 same as "occasional function call" but with a little type checking, semantic syntax     | 0/10 - need to manually change hardcoded address on every new deployment or build own deployment management solution|
| truffle     | 0/10 - fully impossible     | 1/10 need contract code and compiler information to recreate artifact .json file + manually fake the deployment information (network, address, etc.)     | 9/10 - dæpp automatically aware of all information about deployments, functions and contract events through artifact .json file|


To sum it up: There was a trade-of between a non-smart but agile web3 SDK that and a really context-aware but rigid truffle framework.

Having a semantic syntax that takes these three abovementioned scenarios in regard **without forcing developers into a trade-off between flexibility and automation** will make building applications on top of aeternity a smooth process from testing till roll-out.


## Definition



The following three terms are to be defined for the various layers of SDK and framework syntax (open for naming suggestions) : 

**Low Level:** This level is represented by the current SDK syntax. Every piece of information required to interact with a contract is to be provided manually, interacting with a contract is still broken down in steps.

**Contextual:** This syntax is provided by the SDK, it wraps around the Low Level API syntax and utilizes the ACI to provide functionalities according to the table further below. 


**Artifactual:** This syntax is provided by the **framework** (forgAE), it is syntactically equal with the SDK's but *extends* its functionalities with features available through the use of forgAE and the *artifacts it generates* (e.g. JSON file with specific information about the contract, see Implementation/Example section). I have not figured out  many functionalities yet which it is to provide additionally to the Contextual syntax, but I found *some* so far. Please contribute any ideas of any benefits any information could bring, which can be collected during the development workflow and/or point of deployment to some network.





## Rationale


 One important rationale is also a product design philosophy I would like to establish in the aeternity project, for the sake of the developer experience and thus our sustainable success: Let's make our product our problem, not the users' (read: æepp developers) ones. 
 
 So far, we had an easy job simply introducing changes and forcing people to adapt. I would love to see us make up our mind over a wrapper syntax, that 
 
 - semantically covers everything necessary and provides all the updates *under* the hood
 - thus will be backwards compatible for years
 - can be easily extended with new semantics (read: features)

See [this forum post](https://forum.aeternity.com/t/forgae-sdk-and-the-unpleasant-experience-of-an-unexperienced-developer/3323), it is a proper example for pretty much everyones experience trying to build something on aeternity. Let's leave that in the past.


## Implementation (Examples)


Here is an example for the wrapper syntax that partly intersects with what is already there. It is explicitly to be understood as **a first glimpse to inspire other devs with far more senior JS experience than me** to contribute ideas on the *type* level, following the outlined philiosophy of this AEX on the *meta* level.

Import SDK/framework:
```
const ae = require("aepp-sdk");
const aeFramework = require("forgae-module") *extend SDK with functions: if ae.someFunction == undefined..*
```

Create a reference to a node. Optionally check for the network it is connected to (abort if condition not met) and provide CORS header settings if node requires for some reason (this shall be elaborated later):

```
const myFirstMainnetNode = ae.nodeProvider(“http://ip:port/”, “ae_mainnet”, “cors-params”)
```
If we find a suitable way for communication between a (desktop?) browser and a mobile signing tool a.k.a. identity provider, now a request for permission from browser plugin (on desktop) and/or mobile app should pop up, requesting to expose the account to the context of the website. (if no website <-> mobile communication: trigger only the browser plugin or whatever we use on desktop for account exposure) - this should be combined with the auth process described in AEX-2:
```
var accounts;
var myAccount;

ae.accounts().then(function(accounts, error) {
	if (error) {
    // no accounts were injected by browser plugin, or, in whatever way, through mobile. Seems like we are in a back-end or some environment where we let users enter their private keys manually, like Playground.
    **create some accounts from private keys**
} else {
    accounts = ae.accounts; // ae.accounts should return only the    active account per default, for privacy reasons!
    myAccount = accounts[0]; // we define one account as our main account 
    }

})
```

<table class=MsoTableGrid border=1 cellspacing=0 cellpadding=0 align=left
 style='border-collapse:collapse;border:none;margin-left:4.8pt;margin-right:
 4.8pt'>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><b>Intent</b></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><b>Low Level Syntax (SDK)</b></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><b>Contextual Syntax (SDK)</b></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><b>Artifactual Syntax (Framework)</b></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><b>Rationale</b></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Deploy a contract and create a reference  </span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>(as is)</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>MyContract = ae.contractFromSource(*source
  code*) </span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>var NewDeployedContract = await MyContract.new(*constructor
  args*, {from:myAccount, *furtherTxParams*}  )</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Deployed using contract deployment feature</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Contextual Syntax uses ACI to verify
  constructor args to prevent the transaction from failing, verifying arg count
  and types.</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Create a reference to a deployed contract</span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>(as is)</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>MyContract = ae.contractFromSource(*source
  code*) </span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>(or)</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>MyContract = ae.contractFromAci(*ACI*) </span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>To gather all information about the
  functions, then</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>MyContract.locatedAt(“ak_asdf”); // set
  the address</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>MyContract = ae.fromArtifact( require(&quot;./MyTokenArtifact.json&quot;))</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>thereby, check whether the contract is really
  deployed to the network currently connected to. This is best to be done by
  comparing the contract’s bytecode in artifacts with the contract’s bytecode on
  chain.</span></p>
  <p class=MsoNormal><span lang=EN-US><br>
  If multiple deployments exist in artifacts, chose the contract address corresponding
  to the network currently connected to.</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>Also, allow for named deployments to a
  network, so one can optionally do:</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>MyContract.locatedAt(“newFeatureTesting”);
  // set the address by name, looked up in the artifacts</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>contractFromSource() should simply wrap  contractFromAci(). 
  For framework, MyTokenArtifact.json is created by framework during
  compilation and extended e.g. with address information during contract
  deployment. The contract object has information about all of the smart
  contracts functions, their state mutability, the amount of parameters and
  their types, as well as amount and types of returns. This information is to e.g.
  prevent users calling functions in invalid ways and to automatically decode
  return values.</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Tell a contract which node provider to
  use for interacting with chain (reading and writing) </span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>(?)</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>MyContract.options.setNodeProvider(myFirstMainnetNode);</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>Or</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>ae.setNodeProvider(MyContract, myFirstMainnetNode);</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>same as contextual SDK, but with check
  that the node is actually connected to the network specified </span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Contracts and nodes shall be able to have
  an n to n relationship, one contract could also be instantiated multiple
  times, to use one node only for reading, and the other contract instance and
  its nodeprovider for sending the transactions (this saved a big project in
  the past)</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>First we see how to interact with a
  contract in <i>explicit ways. </i>This means, we explicitly say in which way
  we want to interact with a function In regard of either changing the state,
  reading it, or dry-running a state change (e.g. testing stuff) </span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>Here we also introduce the term integrity
  check</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Read data from  contract</span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>(as is)</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>var someData = await MyContract.methods.someGetterFunction.get(*params*
  );</span></p>
  <p class=MsoNormal><span lang=EN-US>console.log(someData);</span></p>
  <p class=MsoNormal><span lang=EN-US>“Yeah, that’s all!”</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>1.Check if the expected contract is
  actually the one deployed at the address written in artifacts, multiple ways
  to do so <b>(integrity check)</b></span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>then</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>2. (same as in contextual SDK)</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Using the ACI, input data verification
  (type, count) and the decoding are done automatically through member
  functions of the MyContract object. </span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Read data from a different instance of
  the contract / a contract that implements the exact same function</span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Requires re-initialization of reference  afaik,
  then use dry-run API)</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>var someData = await MyContract.at(“ak_asdf”).methods.someGetterFunction.get(*params*
   );</span></p>
  <p class=MsoNormal><span lang=EN-US>console.log(someData);</span></p>
  <p class=MsoNormal><span lang=EN-US>“Yeah, that’s something else!”</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>skip integrity check<br>
  <br>
  then </span></p>
  <p class=MsoNormal><span lang=EN-US><br>
  (same as in contextual SDK)</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Sometimes you want to just quickly read
  data from another instance or another contract that is similar to yours or
  implements the same interface (e.g. token contracts that have the same
  functions most of the times)</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Read past state data from a contract (“at
  height” and eventually impersonate as some stranger’s account) </span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>(using the dry-run API)</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>var someData = await MyContract.methods.someGetterFunction.get(*params*,
  {from: *some account*, atHeight: *blocknumber*} );</span></p>
  <p class=MsoNormal><span lang=EN-US>console.log(someData);</span></p>
  <p class=MsoNormal><span lang=EN-US>“Yeah, that’s some older information!”</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>Non-statechanging functions (getters)
  shall only be defined for those contract functions, that are marked as such
  in the ACI.</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>(same as in contextual SDK)</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Second parameter (object) is optional, as
  “reading” data is performed with local dry-runs in the node under the hood,
  which allow to take any identity and (very important) provide the
  blockheight, at which this data should be read.</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Execute a contract function through
  transaction</span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>(as is)</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>var MyTransaction = await MyContract.methods.someFunction.send(*params*,
  {from: myAccount, *further tx params*}</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>console.log(MyTransaction.return);<br>
  “Contrary to Ethereum, our transactions can have return values!”<br>
  <br>
  console.log(MyTransaction.details);<br>
  {<br>
   *the transaction call object with details like sender, costs, tx id..*</span></p>
  <p class=MsoNormal><span lang=EN-US>}</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Perform integrity check</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>then</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>(same is in contextual SDK)</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>With .send and .get we make it easier to
  differentiate, which function will induce a transaction and change state, and
  which is just there for reading.</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>The function execution is aborted, if </span></p>
  <p class=MsoListParagraphCxSpFirst style='text-indent:-18.0pt'><span
  lang=EN-US>-<span style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  </span></span><span lang=EN-US>Param types and count don’t match</span></p>
  <p class=MsoListParagraphCxSpMiddle style='text-indent:-18.0pt'><span
  lang=EN-US>-<span style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  </span></span><span lang=EN-US>Trying to execute a non-stateful function</span></p>
  <p class=MsoListParagraphCxSpMiddle style='text-indent:-18.0pt'><span
  lang=EN-US>-<span style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  </span></span><span lang=EN-US>Trying to send funds to a non-payable function
  (payable modifier to arrive later)</span></p>
  <p class=MsoListParagraphCxSpLast style='text-indent:-18.0pt'><span
  lang=EN-US>-<span style='font:7.0pt "Times New Roman"'>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  </span></span><span lang=EN-US>Later also: Do a full dry-run execution first</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Test the possible success of a
  state-changing function call by dry-running it</span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Var MyTestRun = await</span></p>
  <p class=MsoNormal><span lang=EN-US>MyContract.methods.someFunction.dryrun(*params*,
  {from: *some account*, atHeight: *blocknumber*} );</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>console.log(MyTransaction.return);</span></p>
  <p class=MsoNormal><span lang=EN-US>*information on the success of the
  dryrun*</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>(same is in contextual SDK)</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>The atHeight parameter allows for using
  the feature to dry-run at certain block height (e.g. if something would have
  been successful at some time or not) </span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>To make life easier and allow for a
  faster workflow, we can (in a second step?) implement an <i>implicit</i>
  contract interacting syntax</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
 </tr>
 <tr>
  <td width=110 valign=top style='width:82.15pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Interact with an arbitrary function </span></p>
  </td>
  <td width=97 valign=top style='width:72.7pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  </td>
  <td width=476 valign=top style='width:357.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>var WhateverItIs = await MyContract.methods.anyFunction(*params*
  ….)</span></p>
  </td>
  <td width=311 valign=top style='width:233.1pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>(same is in contextual SDK)</span></p>
  </td>
  <td width=241 valign=top style='width:180.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal><span lang=EN-US>Depending on information in the ACI about
  this function if:</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>It is state-changing: run the same
  requirements check for function parameters and necessary transaction
  parameters like with .send()</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>It is non.statechanging:  run the same
  requirements check for function parameters like with .get()</span></p>
  <p class=MsoNormal><span lang=EN-US>&nbsp;</span></p>
  <p class=MsoNormal><span lang=EN-US>We know enough about the contract through
  the ACI to take care of everything for the user as long as he provides the
  bare necessary minimum.</span></p>
  </td>
 </tr>
</table>

A contract Object would therefore - roughly outlined - be structured like this:

```
MyContract = {
address: // the current address
subscribeEvent: function (eventName) {} // live-listening to events, to be discussed in Part 2 of this AEX series (separate AEX)
methods: {...}, // the contract functions
at: function(address) {..}, // perform the contract call with a different address once and don't do a vaidity check
aci: {...},// the contract's ACI used for function call checking and decoding
decode: function(returnData, functionName) {..}, // take return data and look up in ACI for information on how to decode
new: function(args, deploymentTxParameters) {..},
options: { ... }, // containing setters and getters, also additional info maybe
encodeSignAndSendAndDecode: function(functionName, inputData, txParams) {...} // contains the heavy lifting needed to be done to send a transaction and, if it has a return value, decode its return data. Put existing Low Level syntax here, the ACI tells you everything you need to know. Also optionally, do device type checking here (or at some point beforehead and set global var) and execute a deep link like seen in AEX-4

...

}
```

This is how a simple Token æepp could look like, demonstrating the bare minimum of neccessary features, assuming the contract was deployed with the framework already. (`await` used in non-`async` function context for sake of simplicity):

```
const ae = require("aepp-sdk");

// connect to a node
const myMainnetNode = ae.nodeProvider(“http://publicNode.aeternity.com/”, “ae_mainnet”, “cors-params”) 

// create contract reference
TokenContract = ae.fromArtifact(require(./MyToken.json)); // automatically taking latest deployment address for current net

// define which node to use
TokenContract.options.setNodeProvider(myMainnetNode);

//do a transaction
var oneTransaction = await TokenContract.methods.transfer("ak_xyz", 1337, {...tx params...})

//read data from latest contract state
var myTokenBalance = await TokenContract.methods.checkBalance("ak_xyz")

```


## Backwards Compatibility

As you can see, every contract is an object. It can have any property/method, I suggest at least the ones mentioned in the syntax example. Every proposed feature can and should be realized with the current and future Low Level SDK Syntax. Whenever breaking changes occur, they do so only *inside* the inner workings of the instantiated contract objects and its methods. An æepp sticking to the semantic syntax of e.g.

`MyTokenContract.methods.transfer("ak_xyz", 120 {..})  `

can **always** rely on his code to do the intended work, because we do ours under the hood. Need new features? Just upgrade the SDK/Framework, we take care of the rest.



## Important Note:

This proposed wrapper syntax needs to be easily adaptable to smart contracts in state channels. The relationship between them is `n`to `m`, a bigger application may have to handle several different contracts with one or more instances across many state channels. The third part of this AEX, which is to be published after review, discussion and polishing of this one, shall take care of ammending this syntax to the requirements of state channel use cases just stated.




## Copyright

Just some ideas of Nikita Fuchs
