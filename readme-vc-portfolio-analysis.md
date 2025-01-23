# 🚀 VC Portfolio Analysis: SQL Portfolio Project

## The TL;DR
Hey there! Welcome to my SQL portfolio project where I dive deep into venture capital data analysis. This project showcases advanced SQL capabilities through a real-world VC portfolio management system. From complex financial calculations to sophisticated data modeling, this project demonstrates how SQL can transform raw investment data into actionable insights.

## Project Overview 🎯
This project simulates a production-grade VC portfolio management system using PostgreSQL. It demonstrates:

### Technical Skills
- Advanced SQL query optimization
- Complex database schema design
- Window functions and CTEs
- Custom function creation
- Materialized views for performance
- Transaction management
- Data integrity constraints
- Financial calculations in SQL

### Business Context
- Portfolio company tracking
- Investment performance analysis
- LP reporting automation
- Risk assessment metrics
- Exit analysis
- Investment pacing
- Portfolio diversification analytics

## Technical Deep Dive 🔍

### Database Schema Design

#### Companies Table
```sql
CREATE TABLE companies (
    company_id INTEGER PRIMARY KEY,
    company_name VARCHAR(100),
    sector VARCHAR(50),
    investment_stage VARCHAR(50),
    initial_investment_date DATE,
    exit_date DATE NULL,
    exit_type VARCHAR(50) NULL,
    status VARCHAR(20) CHECK (status IN ('Active', 'Exited', 'Written Off'))
);
```
- Implements status constraints
- Handles null exit dates/types for active companies
- Enables efficient indexing on commonly queried fields

#### Investment Rounds
```sql
CREATE TABLE investment_rounds (
    round_id INTEGER PRIMARY KEY,
    company_id INTEGER,
    round_date DATE,
    round_type VARCHAR(50),
    pre_money_valuation DECIMAL(15,2),
    amount_invested DECIMAL(15,2),
    ownership_percentage DECIMAL(5,2),
    FOREIGN KEY (company_id) REFERENCES companies(company_id)
);
```
- Maintains referential integrity
- Handles precise decimal calculations
- Tracks ownership dilution

#### Cash Flows
```sql
CREATE TABLE cash_flows (
    cash_flow_id INTEGER PRIMARY KEY,
    company_id INTEGER,
    flow_date DATE,
    amount DECIMAL(15,2),
    flow_type VARCHAR(20) CHECK (flow_type IN ('Investment', 'Distribution')),
    description VARCHAR(200),
    FOREIGN KEY (company_id) REFERENCES companies(company_id)
);
```
- Implements flow type constraints
- Enables complex financial calculations
- Supports detailed transaction tracking

### Advanced Queries Showcase 💫

#### 1. Vintage Year Analysis
```sql
WITH vintage_investments AS (
    SELECT 
        EXTRACT(YEAR FROM initial_investment_date) as vintage_year,
        company_id,
        SUM(CASE 
            WHEN cf.flow_type = 'Investment' 
            THEN ABS(cf.amount) 
            ELSE 0 
        END) as total_invested
    FROM companies c
    LEFT JOIN cash_flows cf ON c.company_id = cf.company_id
    GROUP BY EXTRACT(YEAR FROM initial_investment_date), company_id
)
-- Complete query in codebase
```
Demonstrates:
- Common Table Expressions (CTEs)
- Date manipulation
- Conditional aggregation
- Complex grouping

#### 2. Portfolio IRR Calculation
```sql
CREATE OR REPLACE FUNCTION calculate_irr(company_id_param INTEGER)
RETURNS DECIMAL(10,2) AS $$
-- Function implementation in codebase
$$ LANGUAGE plpgsql;
```
Showcases:
- Custom function creation
- Financial mathematics in SQL
- Error handling
- Performance optimization

## Key Features & Implementation Details 🛠️

### 1. Investment Performance Tracking

#### Implemented Metrics:
- TVPI (Total Value to Paid-In)
- DPI (Distributions to Paid-In)
- RVPI (Residual Value to Paid-In)
- IRR (Internal Rate of Return)
- Cash-on-Cash multiple

#### Technical Highlights:
```sql
-- Example of complex performance calculation
WITH investment_metrics AS (
    SELECT 
        company_id,
        SUM(CASE 
            WHEN flow_type = 'Investment' 
            THEN ABS(amount) 
            ELSE 0 
        END) as total_invested,
        SUM(CASE 
            WHEN flow_type = 'Distribution' 
            THEN amount 
            ELSE 0 
        END) as total_distributed
    FROM cash_flows
    GROUP BY company_id
)
-- Complete implementation in codebase
```

### 2. Portfolio Analytics Engine

#### Key Components:
- Sector diversification analysis
- Stage distribution metrics
- Ownership concentration tracking
- Vintage year performance
- Exit multiples analysis

#### Implementation Example:
```sql
-- Sector concentration analysis with risk metrics
SELECT 
    sector,
    COUNT(*) as num_companies,
    SUM(amount_invested) as total_invested,
    ROUND(
        SUM(amount_invested) * 100.0 / 
        SUM(SUM(amount_invested)) OVER (), 
        2
    ) as portfolio_percentage
FROM companies c
JOIN investment_rounds ir ON c.company_id = ir.company_id
GROUP BY sector
ORDER BY total_invested DESC;
```

### 3. LP Reporting System

#### Features:
- Automated quarterly reports
- Performance attribution
- Risk metrics
- Portfolio company updates
- Cash flow statements

#### Technical Implementation:
```sql
-- Example of LP report generation
CREATE VIEW lp_quarterly_report AS
WITH metrics AS (
    -- Complex metrics calculation
),
performance AS (
    -- Performance aggregation
),
risk_analysis AS (
    -- Risk metrics computation
)
-- Complete implementation in codebase
```

## Data Analysis Examples 📊

### Portfolio Composition
```sql
SELECT 
    sector,
    COUNT(*) as num_companies,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 2) as sector_percentage,
    SUM(ABS(cf.amount)) as total_invested
FROM companies c
LEFT JOIN cash_flows cf 
    ON c.company_id = cf.company_id
    AND cf.flow_type = 'Investment'
GROUP BY sector
ORDER BY total_invested DESC;
```

### Investment Pace Analysis
```sql
SELECT 
    DATE_TRUNC('quarter', flow_date) as quarter,
    COUNT(DISTINCT company_id) as num_investments,
    SUM(ABS(amount)) as total_invested,
    SUM(SUM(ABS(amount))) OVER (
        ORDER BY DATE_TRUNC('quarter', flow_date)
    ) as cumulative_invested
FROM cash_flows
WHERE flow_type = 'Investment'
GROUP BY DATE_TRUNC('quarter', flow_date)
ORDER BY quarter;
```

## Performance Optimization 🚄

### Implemented Optimizations:
1. Strategic indexing
2. Materialized views for heavy calculations
3. Partitioning for large tables
4. Query optimization techniques
5. Efficient join strategies

### Example Index Creation:
```sql
CREATE INDEX idx_company_sector ON companies(sector);
CREATE INDEX idx_cashflows_date ON cash_flows(flow_date);
CREATE INDEX idx_rounds_company ON investment_rounds(company_id, round_date);
```

## Installation & Setup 🔧

### Prerequisites
- PostgreSQL 12+
- psql command-line tool
- Basic understanding of SQL

### Quick Start
```bash
# Clone the repository
git clone https://github.com/yourusername/vc-portfolio-analysis.git

# Navigate to the project directory
cd vc-portfolio-analysis

# Run the setup script
psql -U your_username -d your_database -f setup.sql

# Load sample data
psql -U your_username -d your_database -f sample_data.sql
```

## Coming Soon™️
1. Reserve analysis (window functions galore)
2. Deal flow tracking (event-based data modeling)
3. Portfolio company KPIs (time-series analysis)
4. Benchmarking (advanced comparative analytics)
5. Operational metrics (complex aggregations)

## Contributing 🤝
Found a bug? Have an idea? Contributions are welcome! Here's how:
1. Fork the repo
2. Create a branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Technical Challenges & Solutions 🧩
1. IRR Calculation Optimization
   - Challenge: Complex financial math in SQL
   - Solution: Custom PL/pgSQL function with numerical approximation

2. Data Consistency
   - Challenge: Maintaining accurate ownership calculations
   - Solution: Trigger-based validation system

3. Performance at Scale
   - Challenge: Handling large portfolios
   - Solution: Materialized views and strategic indexing

## Shoutouts 🙏
Big thanks to:
- Coffee (the real MVP)
- PostgreSQL documentation (my trusted companion)
- Stack Overflow community (for the inspiration)
- My laptop (for not crashing during large queries)

## Need Help? 💁‍♀️
If you're exploring this project and have questions about the SQL implementation or want to discuss potential improvements, feel free to open an issue. Always happy to connect with fellow data enthusiasts!

---
Made with 💖 and probably too much coffee in San Francisco

*P.S. No spreadsheets were harmed in the making of this project*
