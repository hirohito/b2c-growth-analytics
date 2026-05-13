# Retention Playbook — Deep Reference

*Synthesized from: Andrew Chen ("The Cold Start Problem", andrewchen.com), Reforge Retention Programs (Casey Winters, Brian Balfour), Amplitude's "Mastering Retention" series, Lenny Rachitsky's retention deep-dives*

---

## Why Retention Is the #1 Metric

> "Retention is the single most important thing for growth." — Brian Balfour (Reforge)

> "If you don't fix retention first, every dollar you spend on acquisition makes the problem worse." — Andrew Chen

Aggregated MAU/DAU graphs can grow even while every individual cohort is hemorrhaging users. **Cohort retention is the only honest measurement.**

---

## Table of Contents
1. [Building a Cohort Retention Table](#building-a-cohort-retention-table)
2. [Reading Retention Curves](#reading-retention-curves)
3. [The Three Retention Phases](#the-three-retention-phases)
4. [The L28 Model (Power Users)](#the-l28-model-power-users)
5. [Diagnosing Retention Problems](#diagnosing-retention-problems)
6. [Retention Levers](#retention-levers)

---

## Building a Cohort Retention Table

```python
import pandas as pd
import numpy as np

def build_cohort_retention(df, user_col='user_id', date_col='event_date', signup_col='signup_date'):
    df['cohort'] = pd.to_datetime(df[signup_col]).dt.to_period('M')
    df['period'] = pd.to_datetime(df[date_col]).dt.to_period('M')
    df['period_number'] = (df['period'] - df['cohort']).apply(lambda x: x.n)
    
    cohort_data = df.groupby(['cohort', 'period_number'])[user_col].nunique().reset_index()
    cohort_pivot = cohort_data.pivot(index='cohort', columns='period_number', values=user_col)
    
    # Convert to retention percentages
    cohort_size = cohort_pivot.iloc[:, 0]
    retention = cohort_pivot.divide(cohort_size, axis=0) * 100
    return retention.round(1)
```

### Variants
- **N-day retention**: User active on exactly day N
- **Rolling retention**: User active on day N OR any day after (more forgiving)
- **Bracket retention**: User active during days N to N+M (good for weekly/monthly products)

Pick the variant that matches your product's natural usage cadence:
- Daily-use apps (social, news, games) → N-day or rolling daily
- Weekly-use apps (fitness, productivity) → weekly bracket
- Monthly-use apps (travel, finance) → monthly bracket

---

## Reading Retention Curves

Andrew Chen's classic framework distinguishes three curve shapes:

### Curve A: "Smiling" Curve (Flat or Rising After Decline)
```
100% ─╮
      ╰─╮___________
            ╰────────  ← Stabilizes at ~30%
            
Day: 0   7   14  30   60   90
```
**Interpretation:** You have product-market fit. The curve flattening means you've found a core retained user base.  
**Action:** Scale acquisition. Focus on widening the top of the funnel.

### Curve B: Declining to Zero
```
100% ╮
      ╲
       ╲___
           ╲___
                ╲___ → 0%
```
**Interpretation:** No PMF. Users churn out completely.  
**Action:** STOP scaling. Investigate why no users find lasting value. Fix product before acquisition.

### Curve C: Slow Decline (Never Flattens)
```
100% ╮
      ╰╮
        ╰─╮
            ╰─╮
               ╰─╮  ← still declining at D90
```
**Interpretation:** Partial PMF. Some users love it, but the base is leaking.  
**Action:** Segment users — find which subgroup has flat retention. Build for them.

---

## The Three Retention Phases (Reforge / Casey Winters)

### Phase 1: Initial Retention (Day 0–7)
- **Goal:** Get users to the Aha Moment fast
- **Levers:** Onboarding, empty state design, day-1 push notifications, email drip
- **Diagnostic:** D1 retention. If < 25%, your onboarding is broken or your acquisition is bringing bad-fit users.

### Phase 2: Mid-term Retention (Day 7–30)
- **Goal:** Build a habit. User must derive value repeatedly.
- **Levers:** Habit loops, notifications, content freshness, social hooks
- **Diagnostic:** D7 → D30 slope. Steep drop = no habit formation.

### Phase 3: Long-term Retention (Day 30+)
- **Goal:** Lock in by accumulated value (data, network, content, status)
- **Levers:** Network effects, data lock-in, premium tiers, community
- **Diagnostic:** D90+ retention. The plateau height = your true PMF ceiling.

---

## The L28 Model (Power Users)

Per Andrew Chen and the Facebook/Meta team's framework:

For products with high engagement requirements (social, communication, games):
- **L28 = how many of the last 28 days the user was active**
- Distribution matters more than the mean

```python
# Compute L28 distribution
df['active_date'] = pd.to_datetime(df['active_date'])
last_28 = df[df['active_date'] >= (today - pd.Timedelta(days=28))]
l28 = last_28.groupby('user_id')['active_date'].nunique().reset_index(name='days_active')

# Bucket users
def bucket(d):
    if d >= 21: return 'Power (L21+)'
    elif d >= 7: return 'Core (L7-20)'
    elif d >= 2: return 'Casual (L2-6)'
    else: return 'Dormant (L1)'
l28['segment'] = l28['days_active'].apply(bucket)
```

**Healthy distributions** have a "U-shape" or right-skew with a meaningful Power tier:
- Power users (21+ days/month): >20% of MAU → strong
- Core users (7–20 days): 30–40% of MAU → healthy
- Casual + Dormant: leftover

If 80%+ of MAU is in "Casual + Dormant," you have a low-engagement product masquerading as high-MAU.

---

## Diagnosing Retention Problems

### Step 1: Segment-First Analysis
Aggregate retention is misleading. Always segment by:
- **Acquisition channel** (organic users typically retain 2–3x paid users)
- **Geography** (US vs. emerging markets often differ dramatically)
- **Device/platform** (iOS vs. Android, web vs. mobile)
- **Persona/cohort attribute** (age, signup intent, referrer)
- **Feature usage** (did they hit Aha Moment? did they use feature X?)

### Step 2: Find the "Magic Number" (Sean Ellis / Facebook method)
```
What action, in what timeframe, predicts long-term retention?

Examples:
- Facebook: 7 friends in 10 days
- Twitter: Follow 30 people on Day 1
- Slack: Send 2,000 messages as a team
- Dropbox: Put a file in your folder
```

```python
# Find correlation between Day-7 actions and Day-30 retention
from sklearn.tree import DecisionTreeClassifier
features = df[['logins_w1', 'invites_sent_w1', 'content_created_w1', 'connections_w1']]
target = df['retained_d30']

tree = DecisionTreeClassifier(max_depth=3)
tree.fit(features, target)
# Examine splits to find magic number thresholds
```

### Step 3: Identify Churn Triggers
For churned users, what happened RIGHT BEFORE they churned?
- Bad experience? (errors, slow load, bug)
- Notification fatigue? (too many pushes)
- Competitor switch? (NPS survey, exit survey)
- Life event? (seasonal, situational)

---

## Retention Levers (Ranked by Impact)

### Tier 1: Product-Level Levers (Highest Impact)
1. **Improve the Aha Moment** — make core value clearer/faster
2. **Build habit loops** — trigger → action → variable reward → investment (Nir Eyal)
3. **Network effects** — make the product more valuable as more friends join
4. **Personalization** — adapt to user behavior

### Tier 2: Re-engagement Levers (Medium Impact)
5. **Push notifications** — relevant, timed, not noisy (target <3/week)
6. **Email lifecycle campaigns** — welcome series, milestone, win-back
7. **In-app messaging** — onboarding nudges, feature discovery
8. **Content freshness** — algorithm tuning, recommendation quality

### Tier 3: Recovery Levers (Lower Impact)
9. **Win-back campaigns** — discount/incentive for churned users
10. **Reactivation push** — "You haven't been here in a while"
11. **Referral incentives for churned users** — bring back through social

### Notification Anti-Patterns (Andrew Chen)
- DON'T send daily notifications to all users — segment by L28 first
- DON'T re-engage with generic messages — personalize on user data
- DON'T optimize for short-term opens at cost of unsubscribes
- DO send notifications tied to social events (friends, mentions)

---

## Retention Targets by Vertical (Amplitude data)

| Product Type | D1 | D7 | D30 |
|---|---|---|---|
| Social/Communication | 50% | 30% | 22% |
| Media/Entertainment | 35% | 18% | 12% |
| Finance | 38% | 22% | 15% |
| Gaming | 32% | 12% | 6% |
| E-commerce | 25% | 12% | 8% |
| Health/Fitness | 30% | 15% | 8% |
| Travel | 20% | 8% | 4% |

Top quartile products beat these by ~50%. Top-decile (Instagram, TikTok, WhatsApp) can hit 70%+ D1 / 50%+ D30.
