# fintech-calculator
investment calculator
# Combined Finance Calculator

A modular, menu-driven C application engineered to simulate critical financial market investments. Built entirely using standard C libraries (`stdio.h`, `math.h`) to prioritize structural execution efficiency and safe, verified data validation pipelines.

## Project Track
* **Hackathon Track:** Hack Quest (AMS Codeathon 2026)
* **Developer:** Ganesh Sreevatsan T (First-Year CSE, AMSCE | IIT Madras BS Learner)

## Core Calculation Engines
1. **Lump-Sum / Compound Interest Engine:** Simulates long-term geometric compounding values mapped into sequential annual data outputs.
2. **SIP (Systematic Investment Plan) Future Target Value Engine:** Evaluates monthly recurring deposits computed via standard future annuity formulas.
3. **Multi-Frequency Fixed Deposit Module:** Supports toggling calculation logic across yearly, quarterly, and monthly compounding intervals.
4. **Mutual Fund Volatility Risk Analyzer:** Dynamically buckets structural asset risk profiles (<8% Low, 8-18% Moderate, >=18% High Risk) alongside worst-case market capital constraints.

## Technical Highlights
* **Defensive Pipeline Validation:** Integrated strict user console input buffer validation loops to intercept parsing exceptions, symbols, and negative variables, ensuring complete immunity to common terminal loop runtime lockups.
* **Domain Alignment:** Algorithmic calculation parameters are directly informed by standard regulatory guidelines from the National Stock Exchange (NSE) and SEBI consumer frameworks.

#include <stdio.h>
#include <math.h>

/* ---------- Helper: Safe Double Input with Buffer Validation ---------- */
double getPositiveDouble(const char *prompt) {
    double value;
    while (1) {
        printf("%s", prompt);
        if (scanf("%lf", &value) == 1 && value > 0) {
            return value;
        }
        printf("   Invalid input. Please enter a positive number.\n");
        while (getchar() != '\n'); /* Clear bad input from buffer */
    }
}

/* ---------- Helper: Safe Integer Input with Buffer Validation ---------- */
int getPositiveInt(const char *prompt) {
    int value;
    while (1) {
        printf("%s", prompt);
        if (scanf("%d", &value) == 1 && value > 0) {
            return value;
        }
        printf("   Invalid input. Please enter a positive whole number.\n");
        while (getchar() != '\n'); /* Clear bad input from buffer */
    }
}

/* ---------- 1. Lump-Sum / Compound Interest Engine ---------- */
void compoundInterestCalculator() {
    printf("\n===== Lump-Sum / Compound Interest Calculator =====\n");
    double principal = getPositiveDouble("Enter initial investment: Rs ");
    double rate = getPositiveDouble("Enter annual interest rate (in %): ");
    int years = getPositiveInt("Enter number of years: ");

    double balance = principal;
    printf("\nYear-by-year growth:\n");
    printf("%-6s %-15s\n", "Year", "Balance (Rs)");
    
    for (int y = 1; y <= years; y++) {
        balance = balance * (1 + rate / 100.0);
        printf("%-6d %-15.2f\n", y, balance);
    }
    
    printf("\nAfter %d years, your investment grows from Rs %.2f to Rs %.2f\n",
           years, principal, balance);
    printf("Total gain: Rs %.2f\n", balance - principal);
}

/* ---------- 2. SIP Calculator ---------- */
void sipCalculator() {
    printf("\n===== SIP (Systematic Investment Plan) Calculator =====\n");
    double monthlyInvestment = getPositiveDouble("Enter monthly SIP amount: Rs ");
    double annualRate = getPositiveDouble("Enter expected annual return (in %): ");
    int years = getPositiveInt("Enter investment duration (years): ");

    int months = years * 12;
    double monthlyRate = annualRate / 12.0 / 100.0;
    
    // Future Value of an Annuity Due Formula
    double futureValue = monthlyInvestment *
        (((pow(1 + monthlyRate, months) - 1) / monthlyRate) * (1 + monthlyRate));

    double totalInvested = monthlyInvestment * months;
    double totalGain = futureValue - totalInvested;

    printf("\nTotal invested over %d years: Rs %.2f\n", years, totalInvested);
    printf("Estimated maturity value: Rs %.2f\n", futureValue);
    printf("Estimated wealth gained: Rs %.2f\n", totalGain);
}

/* ---------- 3. Fixed Deposit Calculator ---------- */
void fixedDepositCalculator() {
    printf("\n===== Fixed Deposit (FD) Calculator =====\n");
    double principal = getPositiveDouble("Enter deposit amount: Rs ");
    double rate = getPositiveDouble("Enter annual interest rate (in %): ");
    int years = getPositiveInt("Enter tenure (years): ");
    int compoundingPerYear = getPositiveInt("Compounding frequency per year (1=yearly, 4=quarterly, 12=monthly): ");

    double n = (double)compoundingPerYear;
    double maturity = principal * pow(1 + (rate / 100.0) / n, n * (double)years);

    printf("\nMaturity amount after %d years: Rs %.2f\n", years, maturity);
    printf("Interest earned: Rs %.2f\n", maturity - principal);
}

/* ---------- 4. Mutual Fund Risk & Growth Analyzer ---------- */
void mutualFundAnalyzer() {
    printf("\n===== Mutual Fund Risk & Growth Analyzer =====\n");
    double investment = getPositiveDouble("Enter investment amount: Rs ");
    double expectedReturn = getPositiveDouble("Enter expected annual return (in %): ");
    double volatility = getPositiveDouble("Enter expected annual volatility / std deviation (in %): ");
    int years = getPositiveInt("Enter investment horizon (years): ");

    /* Risk classification based on volatility thresholds */
    const char *riskCategory;
    if (volatility < 8.0) {
        riskCategory = "LOW RISK (Debt-oriented / Conservative fund)";
    } else if (volatility < 18.0) {
        riskCategory = "MODERATE RISK (Balanced / Hybrid fund)";
    } else {
        riskCategory = "HIGH RISK (Equity-oriented / Aggressive fund)";
    }

    double expectedValue = investment * pow(1 + expectedReturn / 100.0, years);
    double bestCase = investment * pow(1 + (expectedReturn + volatility) / 100.0, years);
    double worstCase = investment * pow(1 + (expectedReturn - volatility) / 100.0, years);
    
    if (worstCase < 0) worstCase = 0;

    printf("\nRisk Category: %s\n", riskCategory);
    printf("Expected value after %d years: Rs %.2f\n", years, expectedValue);
    printf("Best-case estimate (return + volatility): Rs %.2f\n", bestCase);
    printf("Worst-case estimate (return - volatility): Rs %.2f\n", worstCase);
}

/* ---------- Main Execution Menu Loop ---------- */
int main() {
    int choice;

    do {
        printf("\n======================================\n");
        printf("      COMBINED FINANCE CALCULATOR\n");
        printf("======================================\n");
        printf("1. Lump-Sum / Compound Interest Calculator\n");
        printf("2. SIP Calculator\n");
        printf("3. Fixed Deposit (FD) Calculator\n");
        printf("4. Mutual Fund Risk & Growth Analyzer\n");
        printf("5. Exit\n");
        printf("Choose an option (1-5): ");

        if (scanf("%d", &choice) != 1) {
            printf("   Invalid input. Please enter a valid menu number.\n");
            while (getchar() != '\n'); /* Flush input stream buffer */
            choice = 0; /* Reset choice to force re-loop execution */
            continue;
        }

        switch (choice) {
            case 1: compoundInterestCalculator(); break;
            case 2: sipCalculator(); break;
            case 3: fixedDepositCalculator(); break;
            case 4: mutualFundAnalyzer(); break;
            case 5: printf("\nThank you for using the Finance Calculator!\n"); break;
            default: printf("   Invalid choice. Please select an option between 1 and 5.\n");
        }

    } while (choice != 5);

    return 0;
}
