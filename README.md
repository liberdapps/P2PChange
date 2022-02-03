# SwapBTC - Peer-to-Peer and non-custodial Bitcoin <-> Stablecoin exchange

## Scenario
* Alice has stablecoins (USDT, USDC, BSUD, DAI or other) and wants to buy Bitcoins.
* Bob has Bitcoins and wants to sell them for stablecoins.

## Protocol
 1. Alice generates a one-time Bitcoin key pair (private/public).
 2. Alice deposits X stablecoins in a smart contract (Ethereum, Binance Smart Chain, etc), exposing her public Bitcoin address and the price she’s willing to pay per Bitcoin.
 3. Bob sees the offer and decides to take it by calling a method in the smart contract.
 4. The smart contract will automatically lock Alice’s stablecoins during a time T1, so that Alice won’t be able to withdraw her stablecoins or make changes to the terms until T1 runs out.
 5. Bob will have T1 time to transfer the exact amount of Bitcoins to Alice's one-time public Bitcoin address, and signal to the smart contract that he has made the payment.
    - If Bob never sends the Bitcoins or never marks the trade as paid, after T1, Alice will be able to cancel the trade, unlocking their stablecoins and also getting Bob’s security deposit as a punishment to Bob not following the terms.
 6. Once Bob marks the trade as paid, Alice will have a time T2 to verify that the payment has been correctly made and either confirm or reject the trade.
    - If Alice doesn't act before T2 expires, the contract will automatically allow Bob to withdraw Alice's stablecoins, so Alice has the incentive to act on time.
 7. If things go well, Alice can confirm the trade, transferring the stablecoins to Bob and also refunding his security deposit. Alice will be able withdraw her Bitcoins at anytime using her private key.
 8. If Bob marks the trade as paid but doesn’t send the correct amount or doesn’t send the Bitcoins at all, Alice can reject the trade:
    - The time T2 is invalidated, so Bob will no longer be able to withdraw Alice’s stablecoins after T2 runs out.
    - The smart contract will allow Bob to withdraw his security deposit.
    - Alice will have a time T3 to reset her offer, releasing the private key of the one-time Bitcoin address to Bob (allowing Bob to withdraw any BTC that he might have sent to that address) and generating a new one-time Bitcoin key pair for future trades.
      * If Alice doesn’t act before T3 runs out, Bob will be able to withdraw Alice’s stablecoins, so Alice has an incentive to reset her offer after canceling the trade.
