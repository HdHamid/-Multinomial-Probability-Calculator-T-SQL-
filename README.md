# 🎲 Multinomial Probability in T-SQL

This project implements a clean and accurate way to calculate **multinomial probability** in **pure T-SQL**, something SQL Server doesn't support out of the box.

It includes:
- A recursive, set-based **factorial function**.
- A **stored procedure** to compute multinomial probabilities using table-valued parameters (TVPs).

---

## 📐 Why This Matters

By default, SQL Server lacks:
- Native `FACTORIAL()` support.
- Statistical combinatorics functions.
- Efficient set-based alternatives for such calculations.

🔴 **Without this approach, most attempts in T-SQL suffer from:**
- Procedural loops and slow scalar UDFs.
- Risk of overflow or floating-point underflow.
- Poor readability and maintainability.
- Lack of input validation (e.g., when `@n ≠ SUM(OccurCount)`).

---

## 🧮 Components

### 🔹 `[Analysis].[Factorial]` — Function

Calculates factorial of a given integer using a recursive CTE:

```sql
CREATE FUNCTION [Analysis].[Factorial](@Val INT) RETURNS BIGINT
```

✅ Behavior:
Input: @Val = 5

Output: 120

🔹 [Analysis].[MultinomialProbability] — Stored Procedure
Calculates the multinomial probability using the following formula:

![image](https://github.com/user-attachments/assets/2f7050db-b24c-4af3-afd4-07eaec0e3448)
​
 
📥 Parameters:
```SQL
@n INT,
@ProbTable Analysis.MultinomialProbTable READONLY,
@ReqTable Analysis.MultinomialRequestTable READONLY
```

@n: Total number of trials.

@ProbTable: Table of (Id, Probability) values.

@ReqTable: Table of (Id, OccurCount) values.

🔎 Validation:
Throws an error if @n is not equal to the sum of all OccurCount values.

📤 Output:
Returns a single row with:

```SQL
Probability (DECIMAL(38,5))
```
🧪 Example
Make sure these table types are created first:

```SQL
CREATE TYPE Analysis.MultinomialProbTable AS TABLE (
    Id INT,
    Probability DECIMAL(10,5)
)

CREATE TYPE Analysis.MultinomialRequestTable AS TABLE (
    Id INT,
    OccurCount INT
)
```

Then you can call the procedure like this:

```SQL
DECLARE @P Analysis.MultinomialProbTable
INSERT INTO @P VALUES (1, 0.5), (2, 0.3), (3, 0.2)

DECLARE @R Analysis.MultinomialRequestTable
INSERT INTO @R VALUES (1, 2), (2, 1), (3, 1)

EXEC [Analysis].[MultinomialProbability]
    @n = 4,
    @ProbTable = @P,
    @ReqTable = @R
```

✅ Advantages of This Approach
Fully set-based (no WHILE loops or cursors).

Works with TVPs, enabling flexible input formats.

Performance is efficient even with larger n.

📄 License
MIT — use it freely in academic, commercial, or experimental projects.
