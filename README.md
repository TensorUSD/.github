<div align="center">

# τensorUSD
A reserve-backed stablecoin designed to support 1:1 redeemability for US Dollar within the Bittensor ecosystem.

<a href="https://tensorusd.com/discord"><img alt="Discord" src="https://img.shields.io/discord/1451203260982497406?style=flat-square&label=discord" /></a>

<img src="./assets/banner.png" alt="banner" width="100%">

</div>



## Architecture

```mermaid
sequenceDiagram 

    Miner->>Platform: 1. Connect WebSocket (Signed Auth)
    Platform-->>Miner: 2. Ack Subscription

    loop Alpha Signal Processing
        Platform-->>Miner: 3. alpha_signal (Buy/Sell, Metadata)
        Miner->>Platform: 4. Signal Confirmation (Signed Ack)
        
        Miner->>Blockchain: 5. Execute Transaction (Buy/Sell)
        Blockchain-->>Miner: 6. Return tx_hash
        
        Miner->>Platform: 7. Submit Result (tx_hash, event_id)
        Platform->>Blockchain: 8. Verify Transaction Status
        Blockchain-->>Platform: 9. Verification Result (Success/Fail)
    end

      Validator->>Platform: 10. GET /miner-orders/last-30-mins
      Platform-->>Validator: 11. Return Miner Activity (Trades, tx_hashes)

      Validator->>Blockchain: 12. Set Weight (miner_id, weight)
      Blockchain-->>Validator: 13. Block Confirmation

```

**Miner flow**
1. Connect to the subnet gateway (WebSocket).
2. Receive an order assignment.
3. Validate balance and per-order limits (`MAX_BUY_LIMIT`, `MAX_SELL_LIMIT`).
4. Sign and send an ACK back to the gateway.
5. Execute the on-chain swap and submit completion data.

**Validator flow**
1. Fetch miner performances from the gateway/API.
2. Analyze completions.
3. Compute miner scores (EMA).
4. Set weights.
