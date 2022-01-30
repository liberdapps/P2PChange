# SwapBTC

## Scenario
* Alice has X stablecoin (USDT, USDC, BSUD, DAI or other) and wants to buy bitcoin.
* Bob has Y Bitcoins and wants to sell them for a stablecoin.

## Protocol
 1. Alice generates a one-time private/public Bitcoin key pair.
 2. Alice deposits X stablecoin in a smart contract (Ethereum, Binance Smart Chain, etc), also exposing her public bitcoin address and the price she’s willing to pay per Bitcoin.
 3. Bob sees the offer in the smart contract and decides to take it by locking a security deposit (in ETH, BNB, etc) in the contract, showing good faith in continuing with the trade.
 4. The smart contract will automatically lock Alice’s coins during a time T1, so that Alice won’t be able to withdraw her coins or make changes to the price until T1 runs out.
 5. Bob will have T1 time to transfer the exact amount of Bitcoins to Alice's one-time public Bitcoin address, and signal to the smart contract that he has made the payment.
    - If Bob never sends the Bitcoins or never marks the trade as paid, after T1, Alice will be able to cancel the trade, unlocking their stablecoin and also getting Bob’s security deposit as a punishment to Bob not following the terms.
 6. Once Bob marks the trade as paid, Alice will have T2 time to verify that the payment has been made with the correct amount and either confirm the trade or reject it.
    - If Alice doesn't act before T2 expires, the contract will automatically allow Bob to withdraw Alice's stablecoin, so Alice has the incentive to act on time.
 7. If things go well, Alice can confirm the trade, unlocking X stablecoin to Bob and also refunding his security deposit. Alice can withdraw her Bitcoins at anytime using her private key.
 8. If Bob marks the trade as paid but doesn’t send the exact amount required or doesn’t send the bitcoins at all, Alice can reject the trade:
    - Bob will no longer be able to withdraw Alice’s coins, even after T2 runs out.
    - The smart contract will allow Bob to withdraw his security deposit.
 9. After canceling a trade, Alice will have T3 time to reset her offer, meaning that she’ll need to release the private key of the Bitcoin address to Bob (allowing Bob to withdraw any BTC sent to that address), and generating a new one-time private/public key pair for future trades.
    - If Alice doesn’t act before T3 runs out, Bob will be able to withdraw Alice’s stablecoin, so Alice has an incentive to act on time.
