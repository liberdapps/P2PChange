# SwapBTC - Peer-to-Peer and non-custodial Bitcoin <-> Stablecoin exchange

## Scenario
* Alice has stablecoins (USDT, USDC, BSUD, DAI or other) and wants to buy Bitcoins.
* Bob has Bitcoins and wants to sell them for stablecoins.

## Incentives
* On a successful trade, both Alice and Bob will receive shares in the platform.
* After any successful trade, a portion of the trading fees (paid in stablecoins) will be distributed as dividends among the shareholders of the platform, proportional to the amount of shares they hold.

## Protocol
 1. Alice generates a private Bitcoin key and public Bitcoin address.
 2. Alice deposits X stablecoins in the platform's smart contract, exposing her public Bitcoin address and the price she’s willing to pay per Bitcoin (in stablecoins).
    - The smart contract will make sure that Alice’s public BTC address is unique (never used by anyone else in the smart contract).
 3. Bob takes Alice’s offer by calling a function in the smart contract, providing how many stablecoins he wants (limited up to X stablecoins) and a random number N.
    - The smart contract will make sure that Bob’s random number N  is unique (never used by anyone else in the smart contract).
 4. A new BTC address Y will be generated using Alice’s public BTC address  + random number N in a way that funds transferred to that generated address can only be  spent by using Alice’s private key + random number N.
    - That new address Y will be used to unambiguously identify any BTC transaction sent from Bob to Alice, linked to that particular trade.
 5. Bob will have 30 minutes to deposit the exact BTC amount — calculated using Alice’s BTC price and Bob’s desired amount — to the generated address Y.
    - During that time, Alice’s stablecoins will be locked in the smart contract, so that she won’t be able to withdraw them or change the terms.
 6. If Bob doesn’t send the funds before the 30-minute timeout:
    - The trade will be automatically flagged as cancelable (by the smart contract code).
    - Bob will be instructed not to send the funds, since the trade will be canceled.
    - The smart contract operator will be able effectively cancel the trade by invoking a method in the smart contract, returning Alice's stablecoins.
 7. If Bob sends the funds before the timeout, he’ll be instructed to wait for funds (stablecoins) to be release (either by Alice or by the smart contract operator).
 8. Alice can release the stablecoins to Bob at anytime if Bob sends the correct amount of BTC and the transaction has enough confirmations (2+).
    - In that case, Alice and Bob will receive X shares each, also receiving a portion of the trading fees together with all other shareholders.
 10. If Alice doesn't release the funds after 24 hours, the smart contract operator will be able to act by verifying the amount of BTC sent from Bob to Alice, and providing to the smart contract the exact amount of BTC sent by Bob.
    - The smart contract will release a proportional amount stablecoins to Bob.
 

