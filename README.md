# SwapBTC - Peer-to-Peer and non-custodial Bitcoin <-> Stablecoin exchange

## Scenario
* Alice has stablecoins (USDT, USDC, BSUD, DAI or other) and wants to buy Bitcoins.
* Bob has Bitcoins and wants to sell them for stablecoins.

## Incentives
* After a successful trade, both Alice and Bob will receive shares in the platform.
* After any successful trade, a portion of the trading fees (paid in stablecoins) will be distributed as dividends among the shareholders, proportional to the amount of shares they hold.

## Protocol
 1. Alice generates a private Bitcoin key and a public Bitcoin address.
 2. Alice deposits X stablecoins in the platform's smart contract, exposing her public Bitcoin address and the price she’s willing to pay per Bitcoin (in stablecoins).
    - The smart contract will make sure that Alice’s public Bitcoin address is unique (never used by anyone else).
 3. Bob takes Alice’s offer by calling a function in the smart contract, providing how many stablecoins he wants (up to X stablecoins) and a random number N.
    - The smart contract will make sure that Bob’s random number N is unique (never used by anyone else).
 4. A new Bitcoin address Y will be generated using Alice’s public Bitcoin address  + random number N in a way that funds transferred to that generated address can only be  spent by using Alice’s private key + random number N.
    - That new address Y will be used to unambiguously identify any Bitcoin transaction sent from Bob to Alice, linked to that particular trade.
 5. Bob will have 30 minutes to send the exact amount of Bitcoins — calculated using Alice’s Bitcoin price — to the generated address Y.
    - During that time, Alice’s stablecoins will be locked in the smart contract, so that she won’t be able to withdraw them or change the terms of the offer.
 6. If Bob doesn’t send the funds before the 30-minute timeout:
    - The trade will be automatically flagged as expired (enforced by smart contract rules).
    - Bob will be instructed not to send the Bitcoins, since the trade will be cancelled.
    - The smart contract operator will be able effectively cancel the trade by invoking a method in the smart contract, returning Alice's stablecoins.
 7. If Bob sends the funds before the timeout, he’ll be instructed to wait for the stablecoins to be released (either by Alice or by the smart contract operator).
 8. Alice can release the stablecoins to Bob at anytime, if she sses that if Bob has sent the correct amount of Bitcoins and the transaction has enough confirmations (2+).
    - Alice and Bob will receive S shares, where S = number of stablecoins negotiated.
    - A 1% fee will be taken from Alice's stablecoins, where 0.25% will go the smart contract operator and 0.75% will be distributed proportionally among the shareholders, including Alice and Bob.
 9. If Alice doesn't release the funds to Bob after 24 hours, the smart contract operator will be able to act by verifying the amount of Bitcoins sent from Bob:
    - If Bob sends the correct amount to Alice but she refuses to release the stablecoins, the smart contract operator will release the stablecoins to Bob and only provide extra shares to Bob.
    - If Bob sends the incorrect amount to Alice, the smart contract operator will provide to the smart contract the the exact amount of Bitcoins sent by Bob, releasing the proportional amount of stablecoins. In that case, only Alice will receive extra shares.
 

