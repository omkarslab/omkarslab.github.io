---
id: 611
title: Solidity contract essentials
date: 2017-06-27T21:23:54+00:00
author: Omkar
layout: post
guid: http://omkarslab.com/?p=611
permalink: /2017/06/27/solidity-contract-essentials/
sfw_pwd:
  - BNvCGkgmAVPm
categories:
  - General
---
Understanding that everything on Ethereum is public and permanent, caution needs to be maintained while deploying code in form of Smart Contracts. There is of course an exception to this permanence, which comes at the cost of a community initiated hard-fork. In the history of Ethereum, this has [happened once](https://www.cryptocompare.com/coins/guides/the-dao-the-hack-the-soft-fork-and-the-hard-fork/) with ~$70 million at stake due to a buggy contract. Considering what is at stake, here are a few considerations before deploying your smart contract.

**Routing contracts**

Excluding some very exceptional cases, your contracts will most likely need an upgrade. While you cannot update code for an already deployed contract, you can very well upload a newer version of a contract. If it is not just your Dapp that interacts with the contracts, you need a way to ensure that your contract is accessible at the same address to avoid confusion. This is where Routing Contracts help. You can design contracts like the one below to simply act a pointer to your final contract. You can utilize _delegatecall_ to delegate the operation to another contract and allow state modifications. While upgrading the contract code, you can then simply update the routing to point to the new address.

    
    contract Router {
        address public owner;
        address public greeter;
        
        modifier isOwner () {
            if (msg.sender != owner) throw;
            _;
        }
    
        function SetOwner (address _owner) isOwner() {
            owner = _owner;
        }
    
        function UpdateGreeter (address _greeter) isOwner() {
            greeter = _greeter;
        }
    
        function () {
            if(greeter.delegatecall(msg.data)) throw;
        }
    }
    

**Overrides** 

It is ideal to set override flags that are checked before executing high stake transactions. In case a vulnerability is discovered post deployment, these override flags can be set to block any transactions from being executed.

    
    contract Vote {
        address public owner;
        bool private override;
    
        ...
    
        modifier isOwner () {
            if (msg.sender != owner) throw;
        }
    
        modifier isOverride () {
            if (override) throw;
            _;
        }
    
        // Override can be set only by owner
        function setOverride (bool flag) isOwner() returns (bool) {
            override = flag;
            return true;
        }
    
        // Block voting is override is set
        function castVote () isOverride() {
            ...
        }
    }
    

**Block lock**
  
A single contract is bound to receive concurrent transactions, and as a result frequent state changes. Certain states like ownership or override flags if changed frequently can lead to unintended transactions by users. In such cases block locks can be implemented to block changes to these states for a preset block height.

    
    contract Donation {
        address public owner;
        uint public blocklock;
    
        // Minimim block height to block state change
        uint constant BLOCKLOCK_HEIGHT = 10;
    
        // Check if modification is older than set block lock height
        modifier checkBlocklock() {
            if (blocklock + BLOCKLOCK_HEIGHT > block.number) {
                throw;
            }
            _;
        }
    
        // Set blocknumber when modification was carried out
        modifier setBlocklock () {
            blocklock = block.number;
            _;
        }
    
        // Change owner, and start a block lock for set block height
        function changeOwner (address _owner) checkBlocklock() setBlocklock() returns (bool) {
            owner = _owner;
        }
    }
    

Users can then ensure that the state they are referring to is at least X block height old, to be sure that no concurrent transactions will drop in leading to unintended transactions.

**Suicide**
  
For contracts that are intended to be temporary, Ethereum provides an OPCODE that acts as a kill switch (suicide) to destroy the contract and free up space on the blockchain. All Ether owned by the contract is transferred to the address passed to this OPCODE. Solidity example below.

    
    function KillContract() IsOwner() returns (uint) {
        suicide(owner);
    }
    
    

Note that suicide take negative gas, as an incentive for freeing up space on the blockchain, so any cleanup operations carried out in a transaction that calls suicide can be discounted. Suicide is also helpful when running a high stake contract that runs into an unintended state, and you wish prevent further damage that any incorporated overrides don’t.

These are some less discussed operational considerations on the top of my list, you can find an exhaustive list of security considerations here to follow here: <a href="https://github.com/ConsenSys/smart-contract-best-practices" target="_blank">https://github.com/ConsenSys/smart-contract-best-practices</a>. Think I&#8217;m missing something on my list? Feel free to add your comments below 🙂

&nbsp;