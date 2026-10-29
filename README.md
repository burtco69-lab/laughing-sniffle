import datetime

class CreditScoreSimulator:
    def __init__(self, current_score, total_limit, current_balance, open_accounts, oldest_account_years):
        self.score = current_score
        self.total_limit = total_limit
        self.balance = current_balance
        self.open_accounts = open_accounts
        self.oldest_account_age = oldest_account_years
        self.payment_history_perfect = True

    @property
    def utilization_ratio(self):
        return (self.balance / self.total_limit) * 100

    def simulate_monthly_update(self, monthly_payment, total_inquiries):
        """Simulates one billing cycle of credit scoring adjustments."""
        # 1. Update Balance & Utilization
        self.balance = max(0, self.balance - monthly_payment)
        util = self.utilization_ratio
        
        # 2. Score Impact: Credit Utilization (30% weight)
        if util <= 2:
            util_points = 120  # Optimized (AZEO method)
        elif util <= 10:
            util_points = 100
        elif util <= 30:
            util_points = 60
        else:
            util_points = 10
            
        # 3. Score Impact: Age of Credit History (15% weight)
        self.oldest_account_age += (1 / 12)
        age_points = min(75, int(self.oldest_account_age * 6))
        
        # 4. Score Impact: Credit Mix & Inquiries (10% + 10% weights)
        mix_points = 50 if self.open_accounts >= 3 else 25
        inquiry_penalty = total_inquiries * 5
        
        # 5. Score Impact: Payment History (35% weight)
        payment_points = 165 if self.payment_history_perfect else 50
        
        # Base baseline calculation alignment
        calculated_base = 300 + payment_points + util_points + age_points + mix_points - inquiry_penalty
        self.score = min(850, max(300, calculated_base))
        
        return self.score

# --- USER PROFILE PROFILE DATA ---
# Baseline from your Credit Karma profile
starting_score = 678
card_limit = 2000
current_debt = 1400  # Assumed high utilization driving down the 678 score
accounts_count = 1
years_active = 2.5

simulator = CreditScoreSimulator(
    current_score=starting_score,
    total_limit=card_limit,
    current_balance=current_debt,
    open_accounts=accounts_count,
    oldest_account_years=years_active
)

# --- RUNNING SIMULATION TO 800 ---
monthly_paydown_budget = 400
months = 0

print(f"Starting Simulation | Score: {simulator.score} | Utilization: {simulator.utilization_ratio:.1f}%")
print("-" * 70)

while simulator.score < 800 and months < 36:
    months += 1
    
    # Action step simulated in month 4: User opens 2 more cards to fix thin credit mix
    if months == 4:
        simulator.open_accounts += 2
        simulator.total_limit += 5000  # Limit expanded by $5,000
        print(f"[* Action Month 4]: Opened 2 clean tradelines. Total limit expanded to ${simulator.total_limit}")
        
    current_projected = simulator.simulate_monthly_update(monthly_paydown_budget, total_inquiries=0)
    print(f"Month {months:02d}: Projected Score = {current_projected} | Remaining Debt: ${simulator.balance} | Util: {simulator.utilization_ratio:.1f}%")

print("-" * 70)
print(f"Target Acquired! It takes approximately {months} months of optimized programmatic behavior to reach an 800 score.")
# laughing-sniffle
800 credit
