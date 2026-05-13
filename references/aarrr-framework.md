# AARRR Framework — Deep Reference

*Synthesized from: Dave McClure (original AARRR), Sean Ellis (GrowthHackers), Amplitude Product Analytics Playbook, Lenny Rachitsky's B2C case studies*

---

## Table of Contents
1. [Acquisition](#acquisition)
2. [Activation](#activation)
3. [Retention](#retention)
4. [Referral](#referral)
5. [Revenue](#revenue)
6. [Funnel Audit Checklist](#funnel-audit-checklist)

---

## Acquisition

**Definition:** The process by which users discover and sign up for your product.

### Key Metrics
| Metric | Formula | Notes |
|---|---|---|
| CAC | Total Spend / New Users | Split by channel |
| Blended CAC | Total Marketing Spend / All New Users | Includes organic |
| Payback Period | CAC / (ARPU × Gross Margin) | Target: <12 months B2C |
| Traffic-to-Signup Rate | Sign-ups / Visitors | Signals landing page quality |

### Channel Quality Framework (Lenny's Newsletter)
Channels are NOT equal. A channel analysis must include **downstream retention quality**, not just volume:
- Organic/SEO users: typically highest LTV, highest intent
- Paid social (Meta/TikTok): high volume, but often lowest D7 retention
- Referral/WOM: highest LTV and retention of all channels
- Influencer: variable — heavily dependent on audience-product fit

**Always cross-tab new user volume by channel with D30 retention.** A channel with 5x CAC but 2x retention may still be superior in LTV terms.

### Acquisition Saturation Signal
Per Andrew Chen: when you see your CAC rising YoY on your best channels, you're hitting saturation. This means:
1. Expand to new channels (content, community, partnerships)
2. Improve conversion rate on existing traffic
3. Invest in referral/virality to reduce paid dependency

---

## Activation

**Definition:** The moment a new user experiences the core value of your product for the first time — the "Aha Moment."

### Finding the Aha Moment Statistically (Sean Ellis Method)
1. Take users who retained at D7+ (your "successful" users)
2. Take users who churned before D7 (your "failed" users)
3. Compare which actions in the first 24–72 hours are overrepresented in the successful group
4. The action(s) with the highest lift = your Aha Moment candidates

```python
# Statistical Aha Moment finder
import pandas as pd
from scipy import stats

retained = df[df['d7_retained'] == True]['first_day_actions'].str.get_dummies(sep=',')
churned = df[df['d7_retained'] == False]['first_day_actions'].str.get_dummies(sep=',')

aha_candidates = {}
for action in retained.columns:
    r_rate = retained[action].mean()
    c_rate = churned[action].mean() if action in churned.columns else 0
    lift = r_rate / (c_rate + 0.001)
    aha_candidates[action] = {'retained_rate': r_rate, 'churned_rate': c_rate, 'lift': lift}

aha_df = pd.DataFrame(aha_candidates).T.sort_values('lift', ascending=False)
```

### Activation Funnel
Map the onboarding steps between sign-up and Aha Moment. Every step is a drop-off point.

```
Sign-up → Email Confirm → Profile Setup → First Core Action → Aha Moment
  100%  →      72%      →      55%      →        40%        →     28%
```
Each drop-off represents an optimization opportunity. Focus on the biggest absolute drop first.

### Activation Benchmarks (B2C, Amplitude)
- Mobile consumer apps: 25–50% Day-1 activation is typical
- Social/community apps: 30–45%
- E-commerce: 20–35% (first purchase within 30 days)
- Content/media: 40–60% (content consumption is low-friction)

---

## Retention

**Definition:** Whether users come back. The single most important metric for sustainable growth.

*For deep retention methodology, see `retention-playbook.md`*

### Core Retention Metrics
| Metric | Definition | B2C Benchmark |
|---|---|---|
| D1 Retention | % returning next day | 25–40% (mobile) |
| D7 Retention | % returning on day 7 | 15–25% |
| D30 Retention | % returning on day 30 | 10–20% |
| L28 (Monthly) | Days active in last 28 days | >4 days = engaged |
| Churn Rate | % who stop using / period | <5%/month (subscription) |

### The Retention Curve Test
Plot cohort retention curves. Three shapes:
1. **Smiling curve** (flattens above 0%) → Product-market fit exists. Scale now.
2. **Declining to 0** → No PMF yet. Do NOT scale paid acquisition.
3. **Declining slowly** → Partial PMF. Fix retention before scaling.

---

## Referral

**Definition:** Existing users bringing in new users.

### K-Factor (Viral Coefficient)
```
K = Invitations sent per user × Conversion rate of those invitations

K > 1.0 = Exponential viral growth (every user creates more than 1 new user)
K = 0.5 = Each user brings in 0.5 new users on average (supplement with paid)
K < 0.1 = Effectively no viral loop
```

### Types of Virality (Andrew Chen)
1. **Word-of-mouth virality**: Users tell friends organically (NPS-driven)
2. **Incentivized referral**: Dropbox model — reward both sides
3. **Inherent product virality**: The product only works with others (WhatsApp, Slack)
4. **Showcase virality**: Users share outputs (Spotify Wrapped, Canva designs)
5. **Communication virality**: Notifications/invites to non-users (calendar invites)

### Referral Health Metrics
- **NPS (Net Promoter Score)**: >40 is strong for consumer; >60 is excellent
- **Referral rate**: % of users who refer ≥1 person per month; >5% is healthy
- **Referral conversion rate**: % of referred people who sign up and activate

---

## Revenue

**Definition:** Monetizing users, not just retaining them.

### LTV Calculation (B2C)
```
LTV = ARPU × Average Customer Lifetime
    = ARPU / Churn Rate (for subscription)
    = Average Order Value × Purchase Frequency × Avg Customer Lifespan (for e-commerce)
```

### LTV/CAC Framework
| Ratio | Interpretation | Action |
|---|---|---|
| < 1x | Burning money | Stop scaling, fix fundamentals |
| 1–2x | Marginally viable | Optimize conversion + retention |
| 3x | Healthy | Standard scaling threshold |
| > 5x | Excellent — may be underinvesting | Increase acquisition spend |

### Revenue Expansion Levers
1. **Conversion rate**: % of free → paid users
2. **Average order value**: Upsell, bundles, premium tiers
3. **Purchase frequency**: Re-engagement, subscriptions, habits
4. **Win-back**: Recovering churned paying users

---

## Funnel Audit Checklist

Use this when first receiving data to ensure comprehensive analysis:

- [ ] Can I map every data column to an AARRR stage?
- [ ] Do I have cohort-level data (not just aggregate)?
- [ ] Do I have channel-level acquisition data?
- [ ] Is retention measured at D1/D7/D30 minimum?
- [ ] Do I have both new user volume AND downstream quality per channel?
- [ ] Do I have revenue per user (ARPU/LTV estimate)?
- [ ] Is there referral/invite tracking data?
- [ ] Do I have timestamps to do time-series analysis?
