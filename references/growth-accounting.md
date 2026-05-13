# Growth Accounting — Deep Reference

*Synthesized from: Reforge (Brian Balfour, Jonathan Hsu's "Diligence at Social Capital"), Andrew Chen's network effects writing, Amplitude's growth accounting framework*

---

## Why Growth Accounting Matters

A flat or growing MAU number can hide a dying product. **Growth Accounting decomposes MAU change into its drivers**, exposing whether growth is healthy compounding or a treadmill of churn-and-replace.

> "It is mathematically impossible to grow if you cannot retain users." — Jonathan Hsu (Social Capital, ex-Facebook)

---

## The MAU Equation

```
MAU(this month) = MAU(last month) + New + Resurrected − Churned

Where:
- New        = first-ever active this month
- Retained   = active last month AND this month
- Resurrected= active this month, NOT last month, but was active before
- Churned    = active last month, NOT this month
```

This is the **fundamental identity**. Every MAU number can be decomposed this way.

---

## Computing Growth Accounting

```python
import pandas as pd

def compute_growth_accounting(df, user_col='user_id', date_col='active_date'):
    """
    Input: dataframe with user_id and active_date columns (one row per user-day active)
    Output: monthly growth accounting decomposition
    """
    df[date_col] = pd.to_datetime(df[date_col])
    df['month'] = df[date_col].dt.to_period('M')
    
    # Set of active users per month
    monthly_users = df.groupby('month')[user_col].apply(set)
    
    # Track who has ever been active before each month
    ever_active = set()
    results = []
    
    for i, (month, users) in enumerate(monthly_users.items()):
        if i == 0:
            new_users = users
            retained = set()
            resurrected = set()
            churned = set()
        else:
            prev_users = monthly_users.iloc[i-1]
            new_users = users - ever_active
            retained = users & prev_users
            resurrected = (users & ever_active) - prev_users
            churned = prev_users - users
        
        results.append({
            'month': month,
            'mau': len(users),
            'new': len(new_users),
            'retained': len(retained),
            'resurrected': len(resurrected),
            'churned': len(churned),
            'net_change': len(new_users) + len(resurrected) - len(churned)
        })
        ever_active |= users
    
    return pd.DataFrame(results)
```

---

## Quick Ratio (Social Capital / Jonathan Hsu)

The fundamental health metric of growth:

```
Quick Ratio = (New + Resurrected) / Churned
```

| Quick Ratio | Interpretation |
|---|---|
| > 4 | Excellent — growing fast and healthily |
| 2–4 | Good — sustainable growth |
| 1–2 | Marginal — barely growing |
| < 1 | Shrinking — losing users faster than acquiring |

**Critical insight:** A product can have growing MAU with a Quick Ratio just above 1 — it's running on a treadmill. As soon as acquisition slows, it collapses.

---

## Retention-Churn Spectrum

Plot churned users as % of last month's MAU:
```
Monthly Churn Rate = Churned / MAU(t-1)
```

| Monthly Churn | Annual Implications |
|---|---|
| 1% | 88% retained after 1 year |
| 3% | 69% retained |
| 5% | 54% retained |
| 7% | 42% retained |
| 10% | 28% retained |
| 15% | 14% retained |

For most B2C apps, monthly churn above 7–8% means you're building on sand.

---

## Resurrected Users: The Hidden Story

**Resurrected users are often misunderstood.** A high resurrection rate can be a sign of:
- **Good:** A product with episodic use cases (travel, tax, fitness seasonal)
- **Bad:** Re-engagement campaigns that briefly wake users who churn again immediately

**Always check the "Resurrected-then-Churned-again" rate.** If users who resurrect don't stay for 2+ months, your resurrection efforts are vanity metrics.

```python
# Compute "sticky resurrection" - users who resurrected and stayed
resurrected_users = set(...)  # users resurrected in month M
next_month_users = set(...)  # users active in month M+1
sticky_resurrection_rate = len(resurrected_users & next_month_users) / len(resurrected_users)
```

Target: >50% sticky resurrection. Below 30% means resurrection is just noise.

---

## The DAU/MAU Ratio (Stickiness)

```
Stickiness = DAU / MAU
```

This tells you what % of monthly users use the product on any given day:

| DAU/MAU | Interpretation |
|---|---|
| > 50% | Daily-habit product (Instagram, WhatsApp) |
| 20–50% | Strong frequent-use (Spotify, Netflix) |
| 10–20% | Weekly-use product (banking apps, food delivery) |
| < 10% | Occasional/episodic use (travel, real estate) |

Whether your DAU/MAU is "good" depends on the natural use case. A travel app with 10% DAU/MAU may be world-class; a social app with 10% is dying.

---

## The "Power User Curve" (L7 Histogram)

Plot a histogram of days active in last 28 days, x-axis = days, y-axis = # users:

```
Number of users
   │ ▓
   │ ▓
   │ ▓ ▓                              ▓
   │ ▓ ▓ ▓                       ▓ ▓ ▓
   │ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓ ▓
   └──────────────────────────────────────
    0 1 2 3 4 5 6 7 8 ... 28
```

**Healthy products have a U-shape or right-skew with a meaningful right bulge** (power users on right side).
**Unhealthy products are heavily left-skewed** (most users barely engaged) with no right bulge.

If you see no right bulge, your "MAU" is mostly drive-by users. Even high MAU is hollow.

---

## Combined Growth Accounting Waterfall (Reporting Template)

For every report, show this:
```
              Month 1   Month 2   Month 3   Month 4
Starting MAU    -       50,000    62,000    71,000
+ New        50,000     18,000    15,000    14,000
+ Resurrected  -         3,000     4,000     5,000
− Churned      -        -9,000   -10,000   -11,000
─────────────────────────────────────────────────────
Ending MAU   50,000     62,000    71,000    79,000

Quick Ratio    ∞         2.33      1.90      1.73   ← declining!
```

Even though MAU is growing, the **declining Quick Ratio** signals trouble.

---

## Activation, Retention, Resurrection: Lifecycle View

The three "growth accounting drivers" map to product strategy:

| Driver | Owned by | Key levers |
|---|---|---|
| New users (Activation rate ↑) | Acquisition + Onboarding | Channels, landing pages, onboarding flow |
| Retained users (Churn rate ↓) | Product + Engagement | Habits, notifications, core value |
| Resurrected users (Resurrection rate ↑) | Lifecycle Marketing | Win-back emails, push, comeback offers |

When diagnosing a problem, ask: **which of these three is broken?** That tells you which team owns the fix.
