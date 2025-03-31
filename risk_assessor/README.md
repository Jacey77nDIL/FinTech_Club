## Risk Model Factors

Here's a breakdown of the risk factors for your model, including their respective weights and justifications.

### 1. Customer Type (Weight: 0.3)
- **Individual vs. Business**
- **Rationale:** Businesses typically handle larger transaction volumes and have more structured financial records, making them lower risk compared to individuals.
- **Implementation:** Binary categorical variable (Individual = 1, Business = 0).

### 2. Customer Signup Location and Transaction Location (Weight: 0.4)
- **Geographic location of signup vs. transaction**
- **Rationale:** A significant deviation in location can indicate a compromised account or fraudulent activity.
- **Implementation:**
  - Map cities to predefined distances from the signup city (base reference: Ogun State).
  - Calculate the absolute distance difference.
  - Normalize the distance using the maximum observed distance (Jigawa = 1020 km).
  - Scale the normalized risk using a weight of 0.4.

### 3. Transaction History (Weight: 0.3)
- **Analysis of past transaction patterns**
- **Rationale:** Customers with fewer past transactions pose higher risks because sudden high-value transactions might indicate fraud.
- **Implementation:**
  - Normalize transaction history values.
  - Assign higher risk to customers with fewer transactions by inverting the normalization (1 - normalized value).

### 4. Device Information (Weight: 0.5)
- **Device and browser fingerprinting**
- **Rationale:** Using a new or unrecognized device significantly increases risk as fraudsters often use new devices to bypass security measures.
- **Implementation:**
  - If the transaction is made from a previously used device, risk = 0.
  - If from a new device, risk = 1 (maximum risk).
  - Apply a weight of 0.5 due to the strong correlation between device change and fraud.

### 5. Transaction Amount (Weight: 0.1 - 0.4)
- **Magnitude of each transaction**
- **Rationale:** Large amounts can be risky, but businesses typically process higher amounts.
- **Implementation:**
  - Normalize transaction amounts.
  - Apply different weights:
    - **Business accounts:** Risk = normalized amount * 0.1 (low weight because high transactions are expected).
    - **Individual accounts:** Risk = normalized amount * 0.4 (higher weight because large transactions are unusual).

### 6. Transaction Frequency (Weight: 0.3)
- **Number of transactions in a short time frame**
- **Rationale:** Frequent transactions in a short period may indicate automated fraud or money laundering.
- **Implementation:**
  - Normalize transaction frequency.
  - Assign a weight of 0.3 to increase risk for frequent small transactions.

### 7. Transaction Time (Weight: 0.2)
- **Time of day and preferred transaction hours**
- **Rationale:** Transactions outside typical business hours are riskier due to the potential for fraudulent activities.
- **Implementation:**
  - Define preferred transaction windows: **8-11 AM, 1-2 PM, 6-10 PM**.
  - If transaction occurs in these ranges, risk = 0.
  - Otherwise, assign a risk score of **0.2**.

### Total Risk Calculation
The total transaction risk is computed by summing the individual risk components:

```python
transactions['total_risk'] = (
    transactions['amount_risk'] + 
    transactions['history_risk'] + 
    transactions['time_risk'] + 
    transactions['location_risk'] +
    transactions['account_type_risk'] +
    transactions['device_type_risk'] +
    transactions['transaction_frequency_risk']
)
```
Each component is weighted accordingly to balance its contribution to the overall risk score.
