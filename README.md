# P2PChange - Peer-to-Peer, non-KYC, and non-custodial crypto exchange with a reward system

## Scenario
* Alice has stablecoins (USDT, USDC, BSUD, DAI or other) and wants bitcoins.
* Bob has bitcoins and wants stablecoins.
* Alice and Bob don't like KYC and don't want to delegate the custody of their funds.
 
## Security model
* Alice and Bob will be able to trade directly, on their own terms, without a central custodian.
  * Stablecoins will be managed by a smart contract deployed to the relevant blockchain (Ethereum, Binance Smart Chain, etc).
  * Alice and Bob will control the private keys for their bitcoin funds.
* If a trade can't be completed successfully due to some party not fulfilling the terms, a mediator (specified in the smart contract) will be able to step in and resolve the conflict in a way that each party gets what they rightly own.
  * In order to minimize trust, the smart contract will only allow the mediator to act if parties can't complete the trade on their own after a reasonable time.
  * All actions taken by the mediator will be recorded on public blockchains, so anyone can audit them and verify that the mediator is acting honestly.

## Incentives
* Users of the platform will be rewarded with shares on every successful trade they make.
* The smart contract will collect a 1% fee on top of the stablecoins traded in the platform:
    * 0.25% goes to the smart contract mediator, to support the mediation service.
    * 0.75% is proportionally distributed as dividends among the shareholders, i.e., if a user has 50% of the shares, that user gets 50% of the dividends.
* If a user misbehaves, e.g., initiating but never completing trades, sending incorrect amounts or willingly delaying trades, that user will lose shares in the platform, both reducing that user's ability to receive dividends while also increasing the amount of dividends paid to honest shareholders. 

## Protocol
 1. Alice generates a private bitcoin key and a public bitcoin address.
 2. Alice deposits X stablecoins in the platform's smart contract, providing her public bitcoin address and the price she’s willing to pay per bitcoin (example: 1000 stablecoins per bitcoin).
    * The smart contract will make sure that Alice’s public bitcoin address is unique (never used by anyone else before).
 3. Bob takes Alice’s offer by calling a function in the smart contract, providing how many stablecoins he wants (up to X stablecoins) and a random number N.
    * The smart contract will make sure that Bob’s random number N is unique (never used by anyone else before).
    * The smart contract will automatically calculate the final amount of stablecoins that Bob will receive, after deducting the 1% fee. For example, if Bob requests 100 stablecoins, he'll actually get 99 stablecoins.
 4. A new bitcoin address Y will be generated using Alice’s public bitcoin address + random number N in a way that funds transferred to that generated address can only be  spent by using Alice’s private key + random number N.
    * That new address Y will be used to unambiguously identify any bitcoin transaction sent from Bob to Alice, linked to that particular trade.
 5. Bob will have 30 minutes to send the exact amount of bitcoins — calculated using Alice’s stablecoin/bitcoin price — to the generated address Y.
    * During that time, Alice’s stablecoins will be locked in the smart contract, so that she won’t be able to withdraw them or change the terms of the offer.
 6. If Bob doesn’t send the funds before the 30-minute timeout:
    * The trade will be automatically flagged as expired (enforced by smart contract rules).
    * Bob will be instructed not to send the bitcoins, since the trade will be cancelled.
    * The smart contract mediator will be able effectively cancel the trade by invoking a method in the smart contract, unlocking Alice's stablecoins.
    * Bob will lose S shares in the platform (where S = number of stablecoins locked in the trade), reducing his participation in future dividends, since he didn't follow the terms.
 7. If Bob sends the funds before the timeout, he’ll be instructed to wait for the stablecoins to be released (either by Alice or by the smart contract mediator).
 8. Alice can release the stablecoins to Bob at anytime, if she sees that Bob has sent the correct amount of bitcoins and that the transaction has enough confirmations (2+). In that case:
    * Alice and Bob will receive S shares each (where S = number of stablecoins locked in the trade), since they both acted correctly.
    * The smart contract will collect and distribute the 1% fee on top of the stablecoins.
    * Bob will get the final amount of stablecoins (after the 1% fee).
 9. If Alice doesn't release the funds to Bob after 24 hours and the smart contract mediator verifies that Bob has sent the **CORRECT** amount:
    * If the transaction has <= 4 confirmations, the mediator will consider it the normal case:
      * Alice and Bob will receive S shares each (where S = number of stablecoins locked in the trade).
      * The smart contract will collect and distribute the 1% fee on top of the stablecoins.
      * Bob will get the final amount of stablecoins (after the 1% fee).
    * If the transaction has > 4 confirmations, the mediator will consider that Alice willingly delayed the trade:
      * Alice will lose S shares (where S = number of stablecoins locked in the trade), since she didn't act correctly.
      * Bob will receive S shares (where S = number of stablecoins locked in the trade), since he acted correctly.
      * The smart contract will collect and distribute the 1% fee on top of the stablecoins.
      * Bob will get the final amount of stablecoins (after the 1% fee).
10. If Alice doesn't release the funds to Bob after 24 hours and the smart contract mediator verifies that Bob has sent the **INCORRECT** amount: 
    * It'll be clear that Bob didn't follow the terms, so the mediator will call a method in the smart contract providing the exact amount of bitcoins that Bob has actually sent to Alice.
    * The smart contract will automatically recalculate how many stablecoins the amount of bitcoins corresponds to — using Alice’s stablecoin/bitcoin price — and refund Alice the extra stablecoins locked. For example, if Bob asks for 500 stablecoins but only send bitcoins worth of 200 stablecoins, 300 stablecoins go back to Alice.
    * Bob will lose R shares (where R = number of stablecoins returned to Alice) since he didn't send the correct amount.
    * Alice will receive S shares (where S = number of stablecoins actually traded), since she acted correctly.
    * The smart contract will collect and distribute the 1% fee on top of the stablecoins.
    * Bob will get the final recalculated amount of stablecoins (after the 1% fee).


## Example 1
* Alice has 500 shares and Bob has 500 shares in the platform.
* Alice locks 2000 USDT in the platform, offering 1000 USDT per bitcoin.
* Bob decides to take 500 USDT from Alice's offer.
* The platform generates a new bitcoin address, and instructs Bob to send exactly 0.5 BTC (500 USDT divided by 1000 USDT/BTC) to that address within 30 minutes.
* Bob does not send the bitcoins before the 30-minute timeout.
* The platform warns Bob that the trade has expired and that it will be cancelled, so Bob should no longer send the bitcoins.
* The smart contract mediator calls a method that will cancel the expired trade, unlocking Alice's 500 USDT.
* Bob will lose 500 shares in the platform for not following the terms, so now Alice stays with her 500 shares and Bob has now 0 shares.

## Example 2
* Alice has 500 shares, Bob has 0 shares, Charlie has 500 shares in the platform.
* Alice locks 2000 USDT in the platform, offering 1000 USDT per bitcoin.
* Bob decides to take 500 USDT from Alice's offer.
* The platform generates a new bitcoin address, and instructs Bob to send exactly 0.5 BTC  (500 USDT divided by 1000 USDT/BTC) to that address within 30 minutes.
* Bob sends 0.5 BTC to that address before the timeout.
* The platform informs Bob that payment has been sent and ask him to wait for the USDT to be released.
* Alice identifies the payment and releases the stablecoins to Bob.
* Alice and Bob get 500 shares each, so now Alice has 1000 shares, Bob has 500 shares and Charlie has 500 shares.
* A fee of 1.25 USDT (0.25% of 500 USDT) is taken and goes to the smart contract mediator.
* A fee of 3.75 USDT (0.75% of 500 USDT) is taken and is distributed proportionally among the sharesholders:
  * Alice gets 3.75 USDT x 50% (1000 shares / 2000 total shares) = 1.875 USDT.
  * Bob gets 3.75 USDT x 25% (500 shares / 2000 total shares) = 0.9375 USDT.
  * Charlie gets 3.75 USDT x 25% (500 shares / 2000 total shares) = 0.9375 USDT.
* Bob also gets 495 USDT (500 USDT minus 1% fee) from the trade.  

  
## Example 3
* Alice has 500 shares, Bob has 0 shares, Charlie has 500 shares in the platform.
* Alice locks 2000 USDT in the platform, offering 1000 USDT per bitcoin.
* Bob decides to take 500 USDT from Alice's offer.
* The platform generates a new bitcoin address, and instructs Bob to send exactly 0.5 BTC (500 USDT / 1000 USDT) to that address within 30 minutes.
* Bob sends 0.5 BTC to that address before the timeout.
* The platform informs Bob that payment has been sent and ask him to wait for the USDT to be released.
* Alice doesn't release the stablecoins after 24 hours.
* After 24 hours, the smart contract mediator verifies the transaction and notices that it has 4+ confirmations, so it'll be stated that Alice didn't follow the terms:
   * Alice loses 500 shares and Bob gets 500 shares, so Alice will have 0 shares, Bob will have 500 shares and Charlie will have 500 shares.
   * A fee of 1.25 USDT (0.25% of 500 USDT) is taken and goes to the smart contract mediator.  
   * A fee of 3.75 USDT (0.75% of 500 USDT) is taken and is distributed proportionally among the sharesholders:
     * Alice gets no dividends, since she has no shares.
     * Bob gets 3.75 USDT x 50% (500 shares / 1000 total shares) = 1.875 USDT.
     * Charlie gets 3.75 USDT x 50% (50 shares / 1000 total shares) = 1.875 USDT.
   * Bob also gets 495 USDT (500 USDT minus 1% fee) from the trade.  
  
## Example 4
* Alice has 500 shares and Bob has 500 shares in the platform.
* Alice locks 2000 USDT in the platform, offering 1000 USDT per bitcoin.
* Bob decides to take 500 USDT from Alice's offer.
* The platform generates a new bitcoin address, and instructs Bob to send exactly 0.5 BTC (500 USDT / 1000 USDT) to that address within 30 minutes.
* Bob sends 0.4 BTC to that address before the timeout.
* The platform informs Bob that a partial payment has been sent and ask him to wait 24 hours before the mediator can act and do a partial release.
* After 24 hours and after the transaction has at least 2 confirmations:
  * The smart contract mediator calls a method in the smart contract stating that Bob has actually sent 0.4 BTC in the trade.
  * The smart contract recalculates the trade amount from 500 USDT to 400 USDT (0.4 BTC * 1000 USDT/BTC), returning 100 USDT to Alice.
  * Bob loses 100 shares, since he has sen't the incorrect amount and 100 USDT had to be returned to Alice. Bob now has 400 shares.
  * Alice gets 400 shares, since 400 USDT is the new amount of stablecoins being traded. Alice now has 900 shares.
  * A fee of 1.00 USDT (0.25% of 400 USDT) is taken and goes to the smart contract mediator.  
  * A fee of 3.00 USDT (0.75% of 400 USDT) is taken and is distributed proportionally among the sharesholders:
     * Bob gets 3.00 USDT x 31% (400 shares / 1300 total shares) = 0.93 USDT.
     * Charlie gets 3.00 USDT x 69% (900 shares / 1300 total shares) = 2.07 USDT.
   * Bob also gets 396 USDT (400 USDT minus 1% fee) from the trade.  
