# 📈 Trade Analyzer

A Python command-line tool that processes raw trade execution data and enriches each record with real-time running metrics across three levels of aggregation: **symbol**, **exchange**, and **portfolio**.

---

## 🔍 Overview

Trade Analyzer was built to mirror how trading desks actually track executions. Rather than simply summarizing data after the fact, it processes each trade sequentially — maintaining live running state for positions, volume, and notional exposure as each fill comes in.

The result is an enriched CSV where every row reflects the **cumulative state of the portfolio at that exact moment in time**.

---

## ⚙️ Features

### 📊 Three Levels of Aggregation (computed simultaneously per row)
| Level | Metrics Tracked |
|-------|----------------|
| **Symbol** | Shares bought, shares sold, current net position, trade notional |
| **Exchange** | Total shares bought and sold per venue |
| **Portfolio** | Total bought/sold shares and notional value across all symbols |

### 🔄 Real-Time Running State
- Tracks **live net position** per symbol as trades are processed in sequence
- Maintains **running notional totals** (shares × fill price) for both buys and sells
- Handles both `b`/`buy` and `t`/`sell` side conventions

### 🛠️ Engineering Highlights
- **Generator-based CSV reader** — streams rows without loading the entire file into memory, making it efficient for large datasets
- **CLI interface via `argparse`** — fully portable, accepts any input/output file path at runtime
- **Modular design** — parsing, processing, and writing are fully separated for easy extension

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Python | Core logic and data processing |
| `csv` (stdlib) | Streaming CSV I/O |
| `argparse` (stdlib) | Command-line interface |

> No external dependencies — runs on Python 3.x out of the box.

---

## 📂 Project Structure

```
trade-analyzer/
│
├── data/
│   └── trades.csv            # Input: raw trade execution data
│
├── output/
│   └── enriched_trades.csv   # Output: enriched with running metrics
│
├── get_info.py         # Main script
└── README.md
```

---

## 🚀 Getting Started

### 1. Clone the repository
```bash
git clone https://github.com/yourusername/trade-analyzer.git
cd trade-analyzer
```

> No pip install needed — uses Python standard library only.

### 2. Prepare your input CSV
Your input file should contain the following columns:

| Column | Description | Example |
|--------|-------------|---------|
| `LocalTime` | Timestamp of the fill | `2024-01-15 09:32:01` |
| `Symbol` | Stock ticker | `AAPL` |
| `EventType` | Trade event type | `fill` |
| `Side` | Buy or sell | `b` / `t` |
| `FillSize` | Number of shares | `100` |
| `FillPrice` | Execution price | `185.20` |
| `FillExchange` | Venue of execution | `NASDAQ` |

### 3. Run the analyzer
```bash
python trade_analyzer.py -i data/trades.csv -o output/enriched_trades.csv
```

---

## 📊 Output Format

Each input row is enriched with **10 additional columns**:

| Column | Description |
|--------|-------------|
| `SymbolBought` | Cumulative shares bought for this symbol |
| `SymbolSold` | Cumulative shares sold for this symbol |
| `SymbolPosition` | Current net position (bought − sold) |
| `SymbolNotional` | Notional value of this specific fill |
| `ExchangeBought` | Total shares bought on this exchange |
| `ExchangeSold` | Total shares sold on this exchange |
| `TotalBought` | Portfolio-wide cumulative shares bought |
| `TotalSold` | Portfolio-wide cumulative shares sold |
| `TotalBoughtNotional` | Portfolio-wide total buy notional ($) |
| `TotalSoldNotional` | Portfolio-wide total sell notional ($) |

---

## 💡 Future Improvements

- [ ] Add P&L calculation per symbol (requires matching buy/sell lots)
- [ ] Support FIFO/LIFO lot matching for realized gain tracking
- [ ] Matplotlib equity curve visualization
- [ ] Streamlit dashboard for interactive exploration
- [ ] Unit tests for edge cases (partial fills, flat positions)

---

## 📄 License

MIT License — free to use and modify.
