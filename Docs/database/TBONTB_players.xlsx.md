# 🏏 TBONTB Player Stats System — Progress Summary

## 📁 Workbook: `TBONTB_players.xlsx`

A centralized, dynamic player database for Two Bats on Not To Bat (TBONTB), combining batting and bowling stats, player IDs, and calculated metrics. Built using Power Query and Excel formulas for modularity and scalability.

---

## ✅ Structure & Workflow

### 1. **Player ID Registry**
- Created `id_registry` using Power Query:
  - Filtered `all_batting` for TBONTB
  - Sorted by earliest match
  - Removed duplicates by `player_name`
  - Assigned sequential `player_id` based on first appearance
  - Merged debut bowling figures for richer context

---

### 2. **Batting Summary (`tbontb_batting_summary`)**
- Grouped by `player_name` using Power Query
- Aggregated:
  - `bat_innings`, `runs`, `balls_faced`, `4s`, `6s`, `50s`, `high_score`
  - `dismissals`, `not_outs`, `ducks`
- Added `high_score_flag` (`*`) for not out scores using a merge with filtered top innings

---

### 3. **Bowling Summary (`tbontb_bowling_summary`)**
- Converted overs to base-5 format (Hundred rules)
- Added helper columns:
  - `bowl_innings`, `3wickets_flag`
- Grouped by `player_name` to aggregate:
  - `overs_bowled`, `runs_conceded`, `wickets`, `maidens`, `3wickets`, `bbi`
- Built `bbi_display` as `wickets/runs_conceded`, selecting best innings by wickets and least runs
- Converted overs bowled to the correct base 5 (5 balls per over) to be able to have economy and bowling avarage correctly.

---

### 4. **Match Count**
- Extracted `player_name` + `match_id` from both batting and bowling tables
- Removed duplicates
- Appended both tables
- Grouped by `player_name` to count unique match appearances

---

### 5. **Player Stats Table (`tbontb_player_stats`)**
- Combined all summaries using `XLOOKUP()` formulas
- Calculated derived metrics:
  - `strike_rate`, `bat_avg`, `economy`, `bowl_avg`
- Used base-5 logic for economy rate
- Added `bbi_display` with optional `*`
- Structured for dashboard-readiness and future automation

---

## 🧠 Design Principles

- Modular: Power Query handles aggregation, Excel handles display
- Refreshable: All summaries update dynamically from master data
- Cricket-aware: Handles Hundred-format overs and proper BBI logic
- Dashboard-ready: Clean formatting, calculated fields, and extensibility

---
