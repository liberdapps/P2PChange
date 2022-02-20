# P2PChange - Peer-to-Peer, non-KYC, and non-custodial crypto exchange with a reward system

## Scenario
* Alice has stablecoins (USDT, USDC, BSUD, DAI or other) and wants bitcoins.
* Bob has bitcoins and wants stablecoins.
* There will be a mediator, specified in a smart contract, who will be able to resolve conflicts between Alice and Bob (if necessary).

## Security model
* Alice and Bob will be able to trade directly, on their own terms, without a central custodian.
  - Stablecoins will be managed by a smart contract deployed to the relevant blockchain (Ethereum, Binance Smart Chain, etc).
  - Alice and Bob will control the private keys for their bitcoin funds.
* If a trade can't be completed successfully due to some party not fulfilling or only partially fulfilling the terms, the smart contract mediator will be able to act and resolve the conflict in a way that each party gets what they rightly own.
  - In order to minimize trust, the smart contract will only allow the mediator to act if parties can't complete the trade on their own after a reasonable time.
  - All actions taken by the mediator will be recorded on public blockchains, so anyone can audit them and verify that the mediator is acting honestly.

## Incentives
* Users of the platform will be rewarded with shares on every successful trade they make.
* A 1% fee will be taken on top of the stablecoins traded in the platform:
    - 0.25% goes to the smart contract mediator, to support the mediation service.
    - 0.75% is proportionally distributed as dividends among the shareholders, i.e., if a holder has 50% of the shares, that holder gets 50% of the dividends.
* If a user misbehaves, e.g., initiating but never completing trades, sending incorrect amounts or willingly delaying trades, that user will lose shares in the platform, both reducing that user's ability to receive dividends while also automatically  increasing the amount of dividends paid to honest shareholders. 

## Protocol
 1. Alice generates a private Bitcoin key and a public Bitcoin address.
 2. Alice deposits X stablecoins in the platform's smart contract, providing her public Bitcoin address and the price she’s willing to pay per Bitcoin (in stablecoins).
    - The smart contract will make sure that Alice’s public Bitcoin address is unique (never used by anyone else before).
 3. Bob takes Alice’s offer by calling a function in the smart contract, providing how many stablecoins he wants (up to X stablecoins) and a random number N.
    - The smart contract will make sure that Bob’s random number N is unique (never used by anyone else before).
    - The smart contract will automatically calculate the net amount of stablecoins that Bob will receive, after deducting the 1% fee. For example, if Bob requests 100 stablecoins, he'll actually get 99 stablecoins.
 4. A new Bitcoin address Y will be generated using Alice’s public Bitcoin address + random number N in a way that funds transferred to that generated address can only be  spent by using Alice’s private key + random number N.
    - That new address Y will be used to unambiguously identify any Bitcoin transaction sent from Bob to Alice, linked to that particular trade.
 5. Bob will have 30 minutes to send the exact amount of Bitcoins — calculated using Alice’s Stablecoin/Bitcoin price — to the generated address Y.
    - During that time, Alice’s stablecoins will be locked in the smart contract, so that she won’t be able to withdraw them or change the terms of the offer.
 6. If Bob doesn’t send the funds before the 30-minute timeout:
    - The trade will be automatically flagged as expired (enforced by smart contract rules).
    - Bob will be instructed not to send the Bitcoins, since the trade will be cancelled.
    - The smart contract operator will be able effectively cancel the trade by invoking a method in the smart contract, unlocking Alice's stablecoins.
    - Bob will lose S shares in the platform (where S = number of stablecoins locked in the trade), reducing his participation in the dividends, since he didn't follow the terms.
 7. If Bob sends the funds before the timeout, he’ll be instructed to wait for the stablecoins to be released (either by Alice or by the smart contract operator).
 8. Alice can release the stablecoins to Bob at anytime, if she sees that Bob has sent the correct amount of Bitcoins and the transaction has enough confirmations (2+). In that case:
    - Alice will be able to withdraw her Bitcoins at anytime, using her private Bitcon key + random number N.
    - Alice and Bob will receive S shares (where S = number of stablecoins negotiated), since they both acted correctly.
    - A 1% fee will be taken from the locked stablecoins:
      * 0.25% goes the smart contract operator.
      * 0.75% is distributed proportionally among the shareholders, including Alice and Bob.
    - In addition to dividends, Bob will get the net amount of stablecoins, i.e, 99% (X minus 1% fee).
 9. If Alice doesn't release the funds to Bob after 24 hours and the smart contract operator verifies that Bob has sent the **CORRECT** amount:
    - Bob will receive S shares, where S = number of stablecoins negotiated, since he acted correctly.
    - Alice will lose S shares (where S = number of stablecoins negotiated), since she didn't act correctly.
    - A 1% fee will be taken from the locked stablecoins:
      * 0.25% goes the smart contract operator.
      * 0.75% is distributed proportionally among the shareholders, including Alice and Bob.
10. If Alice doesn't release the funds to Bob after 24 hours and the smart contract operator verifies that Bob has sent the **INCORRECT** amount:
    - The smart contract operator will call a method in the smart contract providing the exact amount that Bob has actually sent to Alice.
    - The smart contract will automatically recalculate how many stablecoins the sent BTC amount corresponds to — calculated using Alice’s Stablecoin/Bitcoin price —, release those to Bob and refund the remaining to Alice. For example, if BOb asks for 500 stablecoins but only send bitcoins wotth of 200 stablecoins, Bob gets 200 stablecoins and 300 stablecoins goes back to Alice.
    - Bob will lose S shares (where S = number of stablecoins negotiated) since he didn't send the correct amount and the operator had to step in.
    - Alice will receive S shares (where S = number of stablecoins negotiated), since she acted correctly.
    - A 1% fee will be taken from the locked stablecoins:
      * 0.25% goes the smart contract operator.
      * 0.75% is distributed proportionally among the shareholders, including Alice and Bob.


## Example 1
* Alice has 500 shares and Bob has 500 shares in the platform.
* Alice locks 2000 USDT in the platform, offering 1000 USDT per Bitcoin.
* Bob decides to take 500 USDT from Alice's offer.
* The platform generates a new Bitcoin address, and instructs Bob to send 0.5 BTC (500 USDT / 1000 USDT) to that address within 30 minutes.
* Bob does not send the Bitcoins before the 30-minute timeout.
* The platform warns Bob that the trade has expired and that it will be cancelled, so Bob should no longer send the Bitcoins.
* The smart contract operator calls a method that will cancel the expired trade, unlocking Alice's stablecoins.
* Bob will lose 500 shares in the platform for not following the terms, so now Alice stays with her 500 shares and Bob has now 0 shares.

## Example 2
* Alice has 500 shares, Bob has 0 shares, Charlie has 500 shares in the platform.
* Alice locks 2000 USDT in the platform, offering 1000 USDT per Bitcoin.
* Bob decides to take 500 USDT from Alice's offer.
* The platform generates a new Bitcoin address, and instructs Bob to send 0.5 BTC (500 USDT / 1000 USDT) to that address within 30 minutes.
* Bob sends 0.5 BTC to that address before the timeout.
* Alice identifies the payment and releases the stablecoins to Bob.
* Alice and Bob get 500 shares each, so now Alice has 1000 shares, Bob has 500 shares and Charlie has 500 shares.
* A fee of 1.25 USDT (0.25% of 500 USDT) is taken and goes to the smart contract operator.
* A fee of 3.75 USDT (0.75% of 500 USDT) is taken and is distributed proportionally among the sharesholders:
  - Alice gets 3.75 x 50% (1000 shares / 2000 total shares) = 1.875 USDT.
  - Bob gets 3.75 x 25% (500 shares / 2000 total shares) = 0.9375 USDT.
  - Alice gets 3.75 x 25% (500 shares / 2000 total shares) = 0.9375 USDT.
  
## Example 3
* Alice has 500 shares, Bob has 0 shares, Charlie has 500 shares in the platform.
* Alice locks 2000 USDT in the platform, offering 1000 USDT per Bitcoin.
* Bob decides to take 500 USDT from Alice's offer.
* The platform generates a new Bitcoin address, and instructs Bob to send 0.5 BTC (500 USDT / 1000 USDT) to that address within 30 minutes.
* Bob sends 0.5 BTC to that address before the timeout.
* Alice doesn't release the stablecoins after 24 hours.
* The smart contract operator will verify the trade status:
  - If Bob's transaction has 6+ confirmations by the time the operator checks it, it'll be stated that Alice didn't follow the terms, so:
    * Alice will lose 500 shares and Bob gets 500 shares, so now Alice has 0 shares, Bob has 500 shares and Charlie has 500 shares.
    * A fee of 1.25 USDT (0.25% of 500 USDT) is taken and goes to the smart contract operator.  
    * A fee of 3.75 USDT (0.75% of 500 USDT) is taken and is distributed proportionally among the sharesholders:
  -  Alice gets 3.75 x 50% (1000 shares / 2000 total shares) = 1.875 USDT.
  - Bob gets 3.75 x 25% (500 shares / 2000 total shares) = 0.9375 USDT.
  - Alice gets 3.75 x 25% (500 shares / 2000 total shares) = 0.9375 USDT.
  

