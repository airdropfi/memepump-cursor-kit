---
name: check-token-safety
description: Check whether a Solana token looks risky with MemePump before the user buys it. Use when the user asks if a Solana token is a rug pull, scam or honeypot, whether its liquidity is locked, whether mint or freeze authority is active, or who holds it.
license: MIT
compatibility: Needs the MemePump MCP server connected. Read-only; no sign-in needed.
---

# Check Solana token safety

This skill reads data only. It cannot trade or move funds.

## Steps

1. Get the token's mint address from the user (a base58 address of 32 to 44 characters). Ask for it if they gave only a name or ticker.
2. Call `check_token_safety` with `mint`.
3. If concentration or the warnings call for a closer look, or the user asks who holds it, call `get_token_holders` (use `limit` up to 25) and report the top holders, their share of supply and any wallet clusters. Exchange, pool and protocol wallets can sit near the top without being a rug risk.
4. If the user asks what a field means, call `explain_risk_signals` and answer from it.
5. Report the result as returned, with its source and `checked_at`, and end with the disclaimer: informational, not financial advice, and a low risk score is not a guarantee.
6. If the tools return an error, say so; do not invent a verdict. Missing data means unknown, not safe.

## Rules that always apply

1. **Read-only.** MemePump tools only read data. They cannot trade, sign or move funds, and no tool takes a wallet address, seed phrase or private key. If the user offers a seed phrase or private key, tell them not to share it.
2. **Valid mint.** `mint` is a base58 Solana token address, 32 to 44 characters. If the user gives a name or ticker instead, ask for the mint address; tickers are copied by fake tokens.
3. **Report as returned.** Give the risk level and score (0 is lowest risk, 100 is a scam), the honeypot signal, liquidity lock (null means unknown), whether the mint and freeze authority are active, holder concentration and the warnings. Name the source as shown in the result (RugCheck analysis of on-chain data) and say when it was checked (`checked_at`; `cached` means a stored result up to 4 hours old).
4. **Not advice.** Say the result is informational and not financial advice, and that a low risk score is not a guarantee. Never tell the user to buy or sell.
5. **Unknown is not safe.** If the tool returns an error or data is unavailable, say so and offer to retry later. Do not guess a verdict.
6. **Limits.** Calls without an API key are limited per IP (HTTP 429 carries `Retry-After`: wait that long once). A 401 means the API key is wrong or inactive: stop and tell the user.
7. **Keys stay out of chat.** An API key is optional. Never ask the user to paste one into the conversation.
