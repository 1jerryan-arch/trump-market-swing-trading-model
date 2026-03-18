# Trump Market Swing Trading Model
### Live Integration with Grok API | Technical Trend Quality Score + Regime-Conditional Multiplier

> **Document Version:** March 2026 — Live Deployment via xAI Grok 4 Fast Reasoning API  
> **Status:** Production-Ready | Paper Trading Demo Included

---

## Overview

This repository contains a **live swing trading model** that combines:

- **Real-time tweet sentiment analysis** via xAI Grok 4 Fast Reasoning API (monitoring Trump administration statements on X/Twitter)
- **5-component Trend Quality Score** (0–1 continuous) derived from weekly technical indicators
- **Regime-conditional event multiplier** (0.70–1.90) with contrarian override logic
- **Full portfolio management pipeline** including position sizing, stop-loss management, and performance tracking

The model extends a backtested framework (2017–2021: **19.7% annual return, Sharpe 1.58, 14.3% max drawdown, 60% win rate**) into a live production environment with cost-optimised API integration.

---

## Architecture: 9-Component Pipeline

```
LIVE TWEET STREAM (X/Twitter)
         │
    [Stage 1] Keyword Pre-Filter (local regex, FREE) — drops ~95% of tweets
         │
    [Stage 2] Grok API Analyzer → TweetSignal JSON ($0.00037/call)
         │
    [Stage 3] Weekly Aggregation (sector momentum, sustained themes)
         │
    [Stage 4] Multiplier Calculation → Final Multiplier (0.70–1.90)
         │
    Weekly OHLCV + Technical Indicators
         │
    [Component 2] Trend Quality Score (0–1)
         │
    [Component 4] Multi-Timeframe Technical Analysis → Base Signal (0–100)
         │
    Combined Signal = TQ Score × Base Signal × Final Multiplier
         │
    [Component 5] Position Sizing + Contrarian Override
         │
    [Component 6] Portfolio Manager / Execution
         │
    [Component 8] Cost Analytics Dashboard
         │
    [Component 9] Performance Tracking & Reporting
```

---

## Component Map

| Component | Description | Mode |
|-----------|-------------|------|
| **1** | Data Handler & Market Screener (ATR + volume filters) | Base model |
| **2** | Weekly Trend Quality Scoring (5 sub-components, 0–1) | Base model |
| **3** | Live Tweet Event Handler — 4-Stage Grok API Pipeline | **NEW (this repo)** |
| **4** | Multi-Timeframe Technical Analysis (daily signals) | Base model |
| **5** | Portfolio Risk Manager (position sizing, contrarian factor) | Base model |
| **6** | Execution Simulator & Portfolio Tracker | Base model |
| **7** | Event Queue & Backtest Controller | Base model |
| **8** | Grok API Cost Analytics Dashboard | **NEW (this repo)** |
| **9** | Performance Tracking & Reporting | Base model |

---

## Component 2: Trend Quality Score

Generates a continuous 0–1 metric from five weekly technical sub-components:

| Sub-Component | Weight | Indicators |
|--------------|--------|------------|
| A — EMA Alignment | 0–0.20 | 10w/20w/50w EMA alignment + acceleration |
| B — MACD Momentum | 0–0.20 | MACD signal crossover, histogram expansion, divergence check |
| C — ADX Directional | 0–0.20 | ADX > 25, +DI > -DI, +DI > 25 |
| D — Volume Confirmation | 0–0.20 | Volume vs. 12w/26w avg, Volume MACD |
| E — Stochastic Positioning | 0–0.20 | Stochastic 40–80 zone, oversold crossover, overbought penalty |

**Gate:** TQ Score ≥ 0.60 required for new long entries.

---

## Component 3: Live Tweet Event Handler (4-Stage Pipeline)

### Stage 1 — Keyword Pre-Filter (FREE)
Local regex matching against 11 sector buckets. ~95% of tweets dropped before any API call. ~1ms per tweet.

**Sector Buckets:** TradeTariffs | China | CanadaMexico | MacroFed | Energy | Defence | Technology | Financials | Cryptocurrency | DollarFX | TaxFiscal | Healthcare

### Stage 2 — Grok API Analysis ($0.00037/call typical)
- Model: `grok-4-fast-reasoning` via xAI API (OpenAI-compatible SDK)
- Returns `TweetSignal` JSON: sector, sentiment (-1.0 to 1.0), market-moving probability, signal type, summary
- Prompt caching: 75% input token discount on cached system prompt

### Stage 3 — Weekly Aggregation (FREE)
Buffers signals, computes avg sentiment, dominant sectors, sustained themes (≥3 signals/5 days), sector momentum.

### Stage 4 — Multiplier Calculation (Existing Logic)

| Raw Multiplier | Regime | Technical Check | Final Multiplier |
|---------------|--------|----------------|----------------|
| 0.7–1.0 | Neutral | N/A | 0.7–1.0 (keep) |
| 1.1–1.9 | Moderate | N/A | 1.1–1.9 (keep) |
| 2.0–3.0 | Extreme | Confirmed (Cond A) | Capped at 1.9 |
| 2.0–2.4 | Extreme | Weak/Diverging (Cond B) | Reduced to 0.85 |
| 2.5–3.0 | Extreme | Weak/Diverging (Cond B) | Reduced to 0.70 |

**Contrarian Override** activates when: Raw Multiplier ≥ 2.0 AND technical trend is weak (TQ < 0.70 OR Stochastic > 75 OR MACD divergence OR low volume).

---

## Cost Analysis

| Scenario | Tweets/Day | API Calls/Day | Daily Cost |
|----------|-----------|--------------|-----------|
| Low | 1,000 | 50 | $0.019 |
| Typical | 4,000 | 200 | $0.074 |
| High (capped) | 10,000 | 500 | $0.185 |

**Monthly cost at typical volume: ~$2.22 | API cost as % of $100K portfolio: 0.003%**

### Cost Guardrails
- MD5 deduplication with 6-hour TTL cache
- Hard cap: 500 calls/day
- Circuit breaker: disables API if daily spend > $0.30
- 15-second request timeout

---

## Performance (Backtest 2017–2021)

| Model Variant | Annual Return | Sharpe | Max Drawdown | Win Rate |
|--------------|--------------|--------|-------------|---------|
| Base (no tweets) | 12.8% | 1.05 | -18.2% | 54% |
| + Tweet Handler (directional only) | 18.4% | 1.42 | -16.1% | 58% |
| + Contrarian Override (full model) | **19.7%** | **1.58** | **-14.3%** | **60%** |

**Tweet Alpha:** 6.9% annually from sentiment integration.

---

## Quick Start

### 1. Set Up Environment

```bash
export XAI_API_KEY=your-grok-api-key-here
pip install -r requirements.txt
```

### 2. Configure API Key

Copy `.env.example` to `.env` and fill in your xAI API key:

```bash
cp .env.example .env
```

### 3. Run in Google Colab

Open `sentiment_model_live_github.py` in Google Colab:
- **Cell 1:** Setup & dependency installation
- **Cell 2:** Stage 1 Keyword Pre-Filter (self-test)
- **Cell 3:** Stage 2 Grok API Analyzer
- **Cell 4:** Stage 3 & 4 — Live Event Handler + Weekly Multiplier
- **Cell 5:** Component 8 Cost Analytics Dashboard
- **Cell 6:** Full Paper Trading Demo (end-to-end pipeline)

```python
# Wire into your X/Twitter stream
signal = live_handler.ingest_tweet(tweet_text, tweet_id, timestamp)

# At weekly boundary (every 5 trading days)
result = live_handler.get_weekly_multiplier_live(
    trend_quality=tq_score,
    adx_score=adx,
    volume_score=vol,
    stochastic_val=stoch,
    macd_divergence=macd_div
)
```

### 4. Monitor Costs

```python
costs = grok_analyzer.cost_summary()
print(f"API Calls Today: {costs['total_api_calls']}")
print(f"Estimated Spend: ${costs['estimated_cost_usd']:.4f}")
render_cost_dashboard()  # HTML dashboard
```

---

## Notebook Cell Structure

| Cell | Component | Purpose | Cost |
|------|-----------|---------|------|
| 1 | Setup | Install deps, configure Grok API key | Free |
| 2 | Component 3 Stage 1 | Keyword Pre-Filter (local regex) | $0.00 |
| 3 | Component 3 Stage 2 | Grok API Analyzer — TweetSignal generation | $0.00037/call |
| 4 | Component 3 Stages 3 & 4 | Live Event Handler + Weekly Aggregation + Multiplier | Free |
| 5 | Component 8 | Cost Signal Analytics Dashboard | Free |
| 6 | Components 3, 5 | Paper Trading Demo — full pipeline end-to-end | ~$0.002 total |

---

## Signal Flow (Combined Signal Formula)

```
Combined Signal = Trend Quality Score × Base Signal × Final Event Multiplier
```

**Conviction Thresholds:**
- ≥ 85: High Conviction (1.25× position size)
- 70–84: Standard (1.0× position size)
- 55–69: Low Conviction (0.75× position size)
- < 55: No trade

**Contrarian Override Behavior:**
- Suppresses ALL new long entries
- Existing positions: tighten stops by 20%, reduce size by 25% if held ≥ 3 days
- Contrarian entries: base signal capped at 60, multiplier 0.70–0.85

---

## Risk Management

### Stop-Loss Strategy
- Day 1–2: Keep initial stop fixed
- Day 3–5: Move to breakeven if +5% or more
- Day 5+: Trail via 20-day EMA or 2× Daily ATR (whichever is wider)
- Trump negative tweet: tighten stop by 3%
- Contrarian override activates mid-trade: tighten stop by 20%

### Take-Profit Targets
- Target 1: 50% position at 8% gain OR 3× Weekly ATR
- Target 2: 30% position at 15% gain OR weekly resistance
- Target 3: 20% position trailing via weekly 20-EMA
- Time exit: Close after 20 trading days if targets missed

---

## Limitations & Risk Considerations

> **⚠️ This model is for educational and research purposes only. It does not constitute financial advice.**

1. **Overfitting Risk** — Contrarian decision tree adds degrees of freedom. Mitigate with walk-forward validation.
2. **Threshold Sensitivity** — Performance is sensitive to contrarian trigger boundary (1.8–2.2 range recommended).
3. **False Fade Risk** — Override may suppress valid breakout trades. Strict Condition A gate preserves strong trend entries.
4. **Regime Change Risk** — Criteria optimised for Trump-era market dynamics; may degrade in other regimes.
5. **Grok API Availability** — Fallback to neutral multiplier (1.0) if API unavailable for > 2 hours. Redis cache stores last 7 days.
6. **Cost Overrun** — Hard cap at 500 calls/day. Alert at 400 calls/day (80% threshold).
7. **Prompt Injection** — JSON schema validation + confidence < 0.3 discard + sentiment clamping to [-1.0, 1.0].
8. **Paper Trade First** — Recommended 30-day paper trading period before live deployment.

---

## Universe Coverage

**Large-cap US Equities** (Trump policy-sensitive): AAPL, MSFT, GOOGL, AMZN, META, TSLA, NVDA, JPM, BAC, GS, XOM, CVX, CAT, DE, BA, LMT, RTX, and more.

**Sector ETFs:** XLF, XLE, XLI, XLK, XLB, XLC, XLRE, XLU, XLV, XLP, XLY

**Macro / Trump-Sensitive:** GLD, SLV, USO, TLT, IEF, UUP, FXI, EEM

**Cryptocurrency (Top 25):** BTC-USD, ETH-USD, XRP-USD, SOL-USD, DOGE-USD, and 20 more.

---

## File Structure

```
trump-market-swing-trading-model/
├── sentiment_model_live_github.py   # Main model — all 6 cells
├── requirements.txt                 # Python dependencies
├── .env.example                     # API key template
├── .gitignore                       # Git ignore rules
└── README.md                        # This file
```

---

## Dependencies

```
openai          # xAI Grok API (OpenAI-compatible SDK)
pandas
numpy
yfinance        # Market data download
vaderSentiment  # Fallback sentiment (Stage 1 assist)
matplotlib      # Performance dashboard charts
```

---

## References

1. xAI. (2026). *Grok 4 Fast Reasoning Pricing Specifications.* https://docs.x.ai/models/grok-4-fast-reasoning
2. Artificial Analysis. (2026). *Grok 4 Fast Reasoning — Intelligence, Performance & Price Analysis.* https://artificialanalysis.ai/models/grok-4-fast-reasoning
3. OpenAI SDK Documentation. (2024). *Python Client Library for xAI API.* https://github.com/openai/openai-python

---

*Model Version: March 2026 | Backtest Period: 2017–2021 | Live Deployment: Grok 4 Fast Reasoning API*
