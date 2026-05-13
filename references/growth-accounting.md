# Growth Accounting — Deep Reference

<document>
<metadata>
  <title>Growth Accounting Methodology</title>
  <purpose>Authoritative methodology for MAU decomposition and Quick Ratio analysis. Loaded by the b2c-growth-analytics SKILL during Step 2d.</purpose>
  <sources>
    <source>Reforge — Brian Balfour</source>
    <source>Jonathan Hsu — "Diligence at Social Capital" (ex-Facebook growth)</source>
    <source>Andrew Chen — network effects writing</source>
    <source>Amplitude — growth accounting framework</source>
  </sources>
</metadata>

<thesis>
<quote source="Jonathan Hsu (Social Capital)">It is mathematically impossible to grow if you cannot retain users.</quote>
<problem>A flat or growing MAU number can hide a dying product. Growth Accounting decomposes MAU change into its drivers, exposing whether growth is healthy compounding or a treadmill of churn-and-replace.</problem>
</thesis>

<section id="mau_equation">
<title>The MAU Equation</title>

<identity>
MAU(this month) = MAU(last month) + New + Resurrected − Churned
</identity>

<components>
  <component name="New" definition="First-ever active this month." />
  <component name="Retained" definition="Active last month AND this month." />
  <component name="Resurrected" definition="Active this month, NOT last month, but was active before." />
  <component name="Churned" definition="Active last month, NOT this month." />
</components>

<principle>This is the fundamental identity. Every MAU number can be decomposed this way.</principle>
</section>

<section id="computing_growth_accounting">
<title>Computing Growth Accounting</title>

<code language="python"><![CDATA[
import pandas as pd

def compute_growth_accounting(df, user_col='user_id', date_col='active_date'):
    """
    Input: dataframe with user_id and active_date columns (one row per user-day active).
    Output: monthly growth accounting decomposition.
    """
    df[date_col] = pd.to_datetime(df[date_col])
    df['month'] = df[date_col].dt.to_period('M')
    
    monthly_users = df.groupby('month')[user_col].apply(set)
    
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
]]></code>
</section>

<section id="quick_ratio">
<title>Quick Ratio</title>
<source>Social Capital / Jonathan Hsu</source>
<significance>The fundamental health metric of growth.</significance>

<formula>Quick Ratio = (New + Resurrected) / Churned</formula>

<interpretation_table>
  <ratio value="&gt; 4" label="Excellent" meaning="Growing fast and healthily." />
  <ratio value="2-4" label="Good" meaning="Sustainable growth." />
  <ratio value="1-2" label="Marginal" meaning="Barely growing." />
  <ratio value="&lt; 1" label="Shrinking" meaning="Losing users faster than acquiring." />
</interpretation_table>

<critical_insight>A product can have growing MAU with a Quick Ratio just above 1 — it's running on a treadmill. As soon as acquisition slows, it collapses.</critical_insight>
</section>

<section id="retention_churn_spectrum">
<title>Retention-Churn Spectrum</title>

<formula>Monthly Churn Rate = Churned / MAU(t-1)</formula>

<annual_implications>
  <row monthly_churn="1%" annual_retained="88%" />
  <row monthly_churn="3%" annual_retained="69%" />
  <row monthly_churn="5%" annual_retained="54%" />
  <row monthly_churn="7%" annual_retained="42%" />
  <row monthly_churn="10%" annual_retained="28%" />
  <row monthly_churn="15%" annual_retained="14%" />
</annual_implications>

<rule>For most B2C apps, monthly churn above 7-8% means you're building on sand.</rule>
</section>

<section id="resurrected_users">
<title>Resurrected Users: The Hidden Story</title>

<insight>Resurrected users are often misunderstood. A high resurrection rate can be a sign of:</insight>

<signal type="good">A product with episodic use cases (travel, tax, fitness seasonal).</signal>
<signal type="bad">Re-engagement campaigns that briefly wake users who churn again immediately.</signal>

<diagnostic name="sticky_resurrection_rate">
  <description>Check the "Resurrected-then-Churned-again" rate. If users who resurrect don't stay for 2+ months, your resurrection efforts are vanity metrics.</description>
  <code language="python"><![CDATA[
resurrected_users = set(...)  # users resurrected in month M
next_month_users = set(...)   # users active in month M+1
sticky_resurrection_rate = len(resurrected_users & next_month_users) / len(resurrected_users)
  ]]></code>
  <target healthy="&gt; 50%" warning="&lt; 30% means resurrection is just noise" />
</diagnostic>
</section>

<section id="dau_mau_ratio">
<title>The DAU/MAU Ratio (Stickiness)</title>

<formula>Stickiness = DAU / MAU</formula>

<interpretation_table>
  <ratio value="&gt; 50%" category="Daily-habit product" examples="Instagram, WhatsApp" />
  <ratio value="20-50%" category="Strong frequent-use" examples="Spotify, Netflix" />
  <ratio value="10-20%" category="Weekly-use product" examples="Banking apps, food delivery" />
  <ratio value="&lt; 10%" category="Occasional/episodic use" examples="Travel, real estate" />
</interpretation_table>

<rule>"Good" DAU/MAU depends on the natural use case. A travel app with 10% DAU/MAU may be world-class; a social app with 10% is dying.</rule>
</section>

<section id="power_user_curve">
<title>The Power User Curve (L7 Histogram)</title>

<description>Plot a histogram of days active in last 28 days. x-axis = days active, y-axis = number of users.</description>

<healthy_shape>U-shape or right-skew with a meaningful right bulge (power users on the right).</healthy_shape>
<unhealthy_shape>Heavy left-skew (most users barely engaged) with no right bulge.</unhealthy_shape>

<diagnostic>If you see no right bulge, your "MAU" is mostly drive-by users. Even high MAU is hollow.</diagnostic>
</section>

<section id="reporting_template">
<title>Growth Accounting Waterfall (Reporting Template)</title>

<rule>For every report, show this decomposition.</rule>

<example_table>
  <header>Month 1 | Month 2 | Month 3 | Month 4</header>
  <row label="Starting MAU">— | 50,000 | 62,000 | 71,000</row>
  <row label="+ New">50,000 | 18,000 | 15,000 | 14,000</row>
  <row label="+ Resurrected">— | 3,000 | 4,000 | 5,000</row>
  <row label="− Churned">— | −9,000 | −10,000 | −11,000</row>
  <row label="Ending MAU">50,000 | 62,000 | 71,000 | 79,000</row>
  <row label="Quick Ratio">∞ | 2.33 | 1.90 | 1.73</row>
</example_table>

<insight>Even though MAU is growing in this example, the declining Quick Ratio signals trouble. The product is increasingly dependent on new acquisition to offset rising churn.</insight>
</section>

<section id="lifecycle_view">
<title>Activation, Retention, Resurrection: Lifecycle View</title>

<purpose>The three "growth accounting drivers" map to product strategy.</purpose>

<driver name="new_users_acquisition" owned_by="Acquisition + Onboarding">
  <key_levers>Channels, landing pages, onboarding flow.</key_levers>
</driver>

<driver name="retained_users_churn_reduction" owned_by="Product + Engagement">
  <key_levers>Habits, notifications, core value.</key_levers>
</driver>

<driver name="resurrected_users_winback" owned_by="Lifecycle Marketing">
  <key_levers>Win-back emails, push, comeback offers.</key_levers>
</driver>

<diagnostic_question>When diagnosing a problem, ask: which of these three is broken? That tells you which team owns the fix.</diagnostic_question>
</section>

</document>
