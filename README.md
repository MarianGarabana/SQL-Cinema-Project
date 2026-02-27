# Cinema Chain Database

**SQL II · Group 3 · IE University · 2026**

End-to-end relational database for a multi-location cinema chain operating across Madrid, Barcelona, and Sevilla. Covers full schema design (DDL), sample data population (DML), and 5 business queries with results.

---

## Overview

| | |
|---|---|
| **Database** | MySQL |
| **Tables** | 16 |
| **Normalisation** | Third Normal Form (3NF) |
| **Locations** | Madrid, Barcelona, Sevilla |
| **Records** | 255 movie tickets · 104 concession orders |

---

## Repository Structure

```
cinema-chain-database/
├── cinema_chain_ddl.sql       # Schema — all CREATE TABLE statements
├── cinema_chain_dml.sql       # Sample data — all INSERT statements
├── cinema_chain_report.pdf    # Full project report with ERD & business queries
└── README.md
```

---

## Database Structure

The 16 tables are organised into five functional sections:

| Section | Tables | Purpose |
|---|---|---|
| **Location & Facilities** | `location`, `auditorium`, `auditorium_type` | Physical infrastructure |
| **Films & Scheduling** | `film`, `film_genre`, `genre`, `showtime`, `showtime_group` | Movie catalogue & screening schedule |
| **Movie Ticket Sales** | `movie_ticket`, `seat`, `ticket_status` | Online ticket transactions |
| **Concessions** | `shop_ticket`, `shop_item`, `product`, `product_category` | In-store food & beverage sales |
| **Shared Entities** | `customer`, `payment_method` | Used by both sales systems |

---

## Key Design Decisions

**Auditorium-based pricing** — Ticket prices are set at the auditorium type level via a `Price_Multiplier` column, not per seat. Reflects how cinema investment works in practice.

```
Final Price = Base Ticket Price × Auditorium Type Multiplier
```

**Separate ticketing systems** — `movie_ticket` requires customer registration and card payment (web-only). `shop_ticket` supports anonymous walk-ins with any payment method. These are independent transaction structures by design.

**Explicit `Location_ID` on `movie_ticket`** — Although derivable via `showtime → auditorium → location`, storing it directly simplifies revenue-per-location queries significantly.

**Showtime Group time ranges** — `showtime_group` uses `Start_Range` / `End_Range` columns, allowing both movie tickets (direct FK) and shop purchases (time matching via `CAST(Purchase_DateTime AS TIME)`) to be grouped by time of day.

**Film–Genre junction table** — Many-to-many relationship handled via `film_genre`, avoiding comma-separated fields and preserving query integrity.

---

## Business Queries & Results

### Q1 — Most Profitable Film Title

> Which title generates the most profit after licensing costs, and when is it shown?

**Answer: Dune: Part Two — €45.17 net profit — Evening showings**

| Title | Revenue (€) | Licensing Cost (€) | Net Profit (€) | Showtime |
|---|---|---|---|---|
| Dune: Part Two | 1,545.17 | 1,500.00 | **45.17** | Evening |
| Deadpool & Wolverine | 1,150.71 | 1,200.00 | -49.29 | Evening |
| Inside Out 2 | 660.66 | 1,000.00 | -339.34 | Afternoon |
| Gladiator II | 753.83 | 1,100.00 | -346.17 | Evening |
| Wicked | 347.27 | 900.00 | -552.73 | Evening |

---

### Q2 — Popcorn & Coke Combo Purchase Rate

> What percentage of concession tickets include both popcorn and Coca-Cola?

**Answer: 68.27% (71 out of 104 orders)**

| Total Tickets | Tickets With Both | Percentage |
|---|---|---|
| 104 | 71 | 68.27% |

---

### Q3 — Peak Sales Showtime Group

> At which time of day do combined sales (movie + shop) peak?

**Answer: Evening — 242 total transactions**

| Showtime Group | Total Sales |
|---|---|
| Evening | 242 |
| Afternoon | 89 |
| Morning | 28 |

---

### Q4 — Monthly Revenue per Location (12 Months)

> Show revenue per cinema location across the full year.

| Month | Centro (€) | Diagonal (€) | Gran Via (€) |
|---|---|---|---|
| January | 72.00 | 128.26 | 343.06 |
| February | 72.60 | 110.72 | 284.97 |
| March | 84.10 | 150.04 | 675.22 |
| April | 63.53 | 91.96 | 189.98 |
| May | 72.00 | 152.48 | 0.00 |
| June | 63.53 | 88.94 | 193.01 |
| July | 78.05 | 113.74 | 357.58 |
| August | 91.96 | 303.12 | 0.00 |
| September | 78.05 | 130.68 | 288.00 |
| October | 78.65 | 110.72 | 289.81 |
| November | 72.00 | 137.94 | 445.91 |
| December | 72.60 | 206.32 | 0.00 |

*Output exported to CSV and visualised as a line chart in Excel.*

---

### Q5 — Revenue by Auditorium Type (Custom Query)

> Which auditorium type generates the highest average revenue per ticket?

**Answer: VIP leads on average (€30.53/ticket) · IMAX leads on total volume (€1,571.79)**

| Auditorium Type | Tickets Sold | Avg Rev / Ticket (€) | Total Revenue (€) |
|---|---|---|---|
| VIP | 13 | 30.53 | 396.88 |
| IMAX | 69 | 22.78 | 1,571.79 |
| 4DX | 36 | 19.93 | 717.53 |
| 3D | 24 | 15.43 | 370.26 |
| Regular | 113 | 12.40 | 1,401.18 |

*For expansion decisions, IMAX offers the best balance of premium pricing and demand volume.*

---

## How to Run

```sql
-- 1. Run the DDL to create the schema
SOURCE cinema_chain_ddl.sql;

-- 2. Populate with sample data
SOURCE cinema_chain_dml.sql;
```

Requires MySQL 8.0+.

---

## Report

The full project report (ERD, design decisions, normalisation analysis, and all SQL queries with results) is available as a PDF in this repository.
