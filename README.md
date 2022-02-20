# SwapBTC - Peer-to-Peer and non-custodial Bitcoin <-> Stablecoin exchange

## Scenario
* Alice has stablecoins (USDT, USDC, BSUD, DAI or other) and wants Bitcoins.
* Bob has Bitcoins and wants stablecoins.

## Security model
* Users will be able to trade directly, on their own terms, without a central custodian.
  - Stablecoins will be managed by a smart contract deployed an altcoin blockchain (Ethereum, Binance Smart Chain, etc).
  - Users will hold the private keys for their Bitcoin funds.
* If a trade can't be completed successfully due to some party not fully or only partially fulfilling the terms, the smart contract operator will be able to act to resolve the conflict in a way that each party gets what they rightly own.
  - In order to minimize the trust needed on the operator, the smart contract will only allow the operator to act if parties can't complete the trade after a reasonable time.

## Incentives
* Users of the platform will be rewarded with shares on every exchange they make.
* A 1% fee will be taken on top of the stablecoins exchanged on each trade:
    - 0.25% goes to the smart contractor operator.
    - 0.75% is proportinally distributed as dividends among the shareholders, i.e., if a holder has 50% of the shares, that holder gets 50% of the dividends.

## Protocol
 1. Alice generates a private Bitcoin key and a public Bitcoin address.
 2. Alice deposits X stablecoins in the platform's smart contract, exposing her public Bitcoin address and the price she’s willing to pay per Bitcoin (in stablecoins).
    - The smart contract will make sure that Alice’s public Bitcoin address is unique (never used by anyone else before).
 3. Bob takes Alice’s offer by calling a function in the smart contract, providing how many stablecoins he wants (up to X stablecoins) and a random number N.
    - The smart contract will make sure that Bob’s random number N is unique (never used by anyone else before).
 4. A new Bitcoin address Y will be generated using Alice’s public Bitcoin address  + random number N in a way that funds transferred to that generated address can only be  spent by using Alice’s private key + random number N.
    - That new address Y will be used to unambiguously identify any Bitcoin transaction sent from Bob to Alice, linked to that particular trade.
 5. Bob will have 30 minutes to send the exact amount of Bitcoins — calculated using Alice’s Stablecoin/Bitcoin price — to the generated address Y.
    - During that time, Alice’s stablecoins will be locked in the smart contract, so that she won’t be able to withdraw them or change the terms of the offer.
 6. If Bob doesn’t send the funds before the 30-minute timeout:
    - The trade will be automatically flagged as expired (enforced by smart contract rules).
    - Bob will be instructed not to send the Bitcoins, since the trade will be cancelled.
    - The smart contract operator will be able effectively cancel the trade by invoking a method in the smart contract, returning Alice's stablecoins.
 7. If Bob sends the funds before the timeout, he’ll be instructed to wait for the stablecoins to be released (either by Alice or by the smart contract operator).
 8. Alice can release the stablecoins to Bob at anytime, if she sees that Bob has sent the correct amount of Bitcoins and the transaction has enough confirmations (2+).
    - Alice and Bob will receive S shares (where S = number of stablecoins negotiated), since they both acted correctly.
    - A 1% fee will be taken from the locked stablecoins:
      * 0.25% goes the smart contract operator.
      * 0.75% is distributed proportionally among the shareholders, including Alice and Bob.
    - In addition to dividends, Bob will get the net amount of stablecoins, i.e, 99% (X minus 1% fee).
 9. If Alice doesn't release the funds to Bob after 24 hours and the smart contract operator verifies that Bob has sent the CORRECT amount:
    - Bob will receive S shares, where S = number of stablecoins negotiated, since he acted correctly.
    - Alice will lose S shares (where S = number of stablecoins negotiated), since she didn't act correctly.
    - A 1% fee will be taken from the locked stablecoins:
      * 0.25% goes the smart contract operator.
      * 0.75% is distributed proportionally among the shareholders, including Alice and Bob.
 10. If Alice doesn't release the funds to Bob after 24 hours and the smart contract operator verifies that Bob has sent the INCORRECT amount:
    - Bob will lose S shares, where S = number of stablecoins negotiated, since he didn't act correctly.
    - Alice will receive S shares (where S = number of stablecoins negotiated), since she acted correctly.
    - The smart contract operator will provide to the smart contract the exact amount of Bitcoin sent by Bob, automatically calculating the amount R of stablecoins (limited to X stablecoins, as locked by Alice) that Bob should get, according to Alice's Stablecoin/Bitcoin price.
    - Alice will get a refund on (S - R) stablecoins.
    - A 1% fee will be taken from R stablecoins:
      * 0.25% goes the smart contract operator.
      * 0.75% is distributed proportionally among the shareholders, including Alice and Bob.

 
## Example 1
* Alice has 500 shares, Bob has 0 shares, Charlie has 500 shares in the platform.
* Alice locks 2000 USDT in the platform, offering 1000 USDT per Bitcoin.
* Bob decides to take 500 USDT from Alice's offer.
* The platform generates a new Bitcoin address, and instructs Bob to send 0.5 BTC (500 USDT / 1000 USDT) to that address within 30 minutes.
* Bob sends 0.5 BTC to that address before the timeout.
* Alice identifies the payment and releases the stablecoins to Bob.'
* Alice and Bob get 500 shares each, so now Alice has 1000 shares, Bob has 500 shares and Charlie has 500 shares.
* A fee of 1.25 USDT (0.25% of 500 USDT) is taken and goes to the smart contract operator.
* A fee of 3.75 USDT (0.75% of 500 USDT) is taken and is distributed proportionally among the sharesholders:
  - Alice gets 3.75 x 50% (1000 shares / 2000 total shares) = 1.875 USDT.
  - Bob gets 3.75 x 25% (500 shares / 2000 total shares) = 0.9375 USDT.
  - Alice gets 3.75 x 25% (500 shares / 2000 total shares) = 0.9375 USDT.

