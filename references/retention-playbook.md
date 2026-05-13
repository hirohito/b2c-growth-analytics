# Retention Playbook — Deep Reference

<document>
<metadata>
  <title>Retention Playbook</title>
  <purpose>Authoritative methodology for B2C retention analysis. Loaded by the b2c-growth-analytics SKILL during Step 2c.</purpose>
  <sources>
    <source>Andrew Chen — andrewchen.com, "The Cold Start Problem"</source>
    <source>Reforge — Retention Programs (Casey Winters, Brian Balfour)</source>
    <source>Amplitude — "Mastering Retention" series</source>
    <source>Lenny Rachitsky — retention deep-dives</source>
  </sources>
</metadata>

<thesis>
<quote source="Brian Balfour (Reforge)">Retention is the single most important thing for growth.</quote>
<quote source="Andrew Chen">If you don't fix retention first, every dollar you spend on acquisition makes the problem worse.</quote>
<implication>Aggregated MAU/DAU graphs can grow even while every individual cohort is hemorrhaging users. Cohort retention is the only honest measurement.</implication>
</thesis>

<section id="building_cohort_retention">
<title>Building a Cohort Retention Table</title>

<code language="python"><![CDATA[
import pandas as pd
import numpy as np

def build_cohort_retention(df, user_col='user_id', date_col='event_date', signup_col='signup_date'):
    """
    Builds a cohort retention table from a long-format dataframe of user events.
    Returns a pivot table: rows = cohort month, columns = months since signup, values = retention %.
    """
    df['cohort'] = pd.to_datetime(df[signup_col]).dt.to_period('M')
    df['period'] = pd.to_datetime(df[date_col]).dt.to_period('M')
    df['period_number'] = (df['period'] - df['cohort']).apply(lambda x: x.n)
    
    cohort_data = df.groupby(['cohort', 'period_number'])[user_col].nunique().reset_index()
    cohort_pivot = cohort_data.pivot(index='cohort', columns='period_number', values=user_col)
    
    cohort_size = cohort_pivot.iloc[:, 0]
    retention = cohort_pivot.divide(cohort_size, axis=0) * 100
    return retention.round(1)
]]></code>

<variants>
  <variant name="n_day_retention">User active on exactly day N.</variant>
  <variant name="rolling_retention">User active on day N OR any day after — more forgiving.</variant>
  <variant name="bracket_retention">User active during days N to N+M — good for weekly/monthly products.</variant>
</variants>

<selection_rule>
  <usage cadence="Daily-use apps (social, news, games)">Use N-day or rolling daily.</usage>
  <usage cadence="Weekly-use apps (fitness, productivity)">Use weekly bracket.</usage>
  <usage cadence="Monthly-use apps (travel, finance)">Use monthly bracket.</usage>
</selection_rule>
</section>

<section id="retention_curves">
<title>Reading Retention Curves</title>
<source>Andrew Chen framework</source>

<curve_shape id="A" name="Smiling Curve">
  <pattern>Initial decline that flattens at a stable retained base (e.g., ~30%).</pattern>
  <interpretation>Product-market fit. The flattening indicates a core retained user base.</interpretation>
  <action>Scale acquisition. Focus on widening the top of the funnel.</action>
</curve_shape>

<curve_shape id="B" name="Declining to Zero">
  <pattern>Continuous decline toward 0% retention.</pattern>
  <interpretation>No PMF. Users churn out completely.</interpretation>
  <action>STOP scaling. Investigate why no users find lasting value. Fix product before acquisition.</action>
</curve_shape>

<curve_shape id="C" name="Slow Decline (Never Flattens)">
  <pattern>Gradual decline that never stabilizes, even at D90+.</pattern>
  <interpretation>Partial PMF. Some users love it, but the base is leaking.</interpretation>
  <action>Segment users — find which subgroup has flat retention. Build for them.</action>
</curve_shape>
</section>

<section id="three_retention_phases">
<title>The Three Retention Phases</title>
<source>Reforge / Casey Winters</source>

<phase id="1" name="Initial Retention" range="Day 0-7">
  <goal>Get users to the Aha Moment fast.</goal>
  <levers>Onboarding, empty state design, day-1 push notifications, email drip.</levers>
  <diagnostic>D1 retention. If &lt; 25%, onboarding is broken OR acquisition is bringing bad-fit users.</diagnostic>
</phase>

<phase id="2" name="Mid-term Retention" range="Day 7-30">
  <goal>Build a habit. User must derive value repeatedly.</goal>
  <levers>Habit loops, notifications, content freshness, social hooks.</levers>
  <diagnostic>D7 → D30 slope. Steep drop = no habit formation.</diagnostic>
</phase>

<phase id="3" name="Long-term Retention" range="Day 30+">
  <goal>Lock in by accumulated value (data, network, content, status).</goal>
  <levers>Network effects, data lock-in, premium tiers, community.</levers>
  <diagnostic>D90+ retention. Plateau height = your true PMF ceiling.</diagnostic>
</phase>
</section>

<section id="l28_model">
<title>The L28 Model (Power Users)</title>
<source>Andrew Chen + Meta growth team</source>

<definition>L28 = number of the last 28 days a user was active. Distribution matters more than the mean.</definition>

<code language="python"><![CDATA[
df['active_date'] = pd.to_datetime(df['active_date'])
last_28 = df[df['active_date'] >= (today - pd.Timedelta(days=28))]
l28 = last_28.groupby('user_id')['active_date'].nunique().reset_index(name='days_active')

def bucket(d):
    if d >= 21: return 'Power (L21+)'
    elif d >= 7: return 'Core (L7-20)'
    elif d >= 2: return 'Casual (L2-6)'
    else: return 'Dormant (L1)'
l28['segment'] = l28['days_active'].apply(bucket)
]]></code>

<healthy_distribution>
  <segment name="Power (21+ days/month)" target="&gt; 20% of MAU" />
  <segment name="Core (7-20 days)" target="30-40% of MAU" />
  <segment name="Casual + Dormant" target="Leftover" />
</healthy_distribution>

<warning>If 80%+ of MAU is in "Casual + Dormant," you have a low-engagement product masquerading as high-MAU.</warning>
</section>

<section id="diagnosing_retention_problems">
<title>Diagnosing Retention Problems</title>

<procedure id="1" name="segment_first_analysis">
  <rule>Aggregate retention is misleading. Always segment by:</rule>
  <dimension>Acquisition channel (organic users typically retain 2-3x paid users).</dimension>
  <dimension>Geography (US vs. emerging markets often differ dramatically).</dimension>
  <dimension>Device/platform (iOS vs. Android, web vs. mobile).</dimension>
  <dimension>Persona/cohort attribute (age, signup intent, referrer).</dimension>
  <dimension>Feature usage (Aha Moment hit? Feature X used?).</dimension>
</procedure>

<procedure id="2" name="find_magic_number" source="Sean Ellis / Facebook">
  <question>What action, in what timeframe, predicts long-term retention?</question>
  <examples>
    <example product="Facebook">7 friends in 10 days</example>
    <example product="Twitter">Follow 30 people on Day 1</example>
    <example product="Slack">Send 2,000 messages as a team</example>
    <example product="Dropbox">Put a file in your folder</example>
  </examples>
  <code language="python"><![CDATA[
from sklearn.tree import DecisionTreeClassifier
features = df[['logins_w1', 'invites_sent_w1', 'content_created_w1', 'connections_w1']]
target = df['retained_d30']
tree = DecisionTreeClassifier(max_depth=3)
tree.fit(features, target)
# Examine splits to find magic number thresholds
  ]]></code>
</procedure>

<procedure id="3" name="identify_churn_triggers">
  <question>For churned users, what happened RIGHT BEFORE they churned?</question>
  <trigger>Bad experience (errors, slow load, bug).</trigger>
  <trigger>Notification fatigue (too many pushes).</trigger>
  <trigger>Competitor switch (NPS survey, exit survey).</trigger>
  <trigger>Life event (seasonal, situational).</trigger>
</procedure>
</section>

<section id="retention_levers">
<title>Retention Levers (Ranked by Impact)</title>

<tier id="1" name="Product-Level Levers" impact="highest">
  <lever rank="1">Improve the Aha Moment — make core value clearer/faster.</lever>
  <lever rank="2">Build habit loops — trigger → action → variable reward → investment (Nir Eyal).</lever>
  <lever rank="3">Network effects — make the product more valuable as more friends join.</lever>
  <lever rank="4">Personalization — adapt to user behavior.</lever>
</tier>

<tier id="2" name="Re-engagement Levers" impact="medium">
  <lever rank="5">Push notifications — relevant, timed, not noisy (target &lt; 3/week).</lever>
  <lever rank="6">Email lifecycle campaigns — welcome series, milestone, win-back.</lever>
  <lever rank="7">In-app messaging — onboarding nudges, feature discovery.</lever>
  <lever rank="8">Content freshness — algorithm tuning, recommendation quality.</lever>
</tier>

<tier id="3" name="Recovery Levers" impact="lower">
  <lever rank="9">Win-back campaigns — discount/incentive for churned users.</lever>
  <lever rank="10">Reactivation push — "You haven't been here in a while."</lever>
  <lever rank="11">Referral incentives for churned users — bring back through social.</lever>
</tier>

<anti_patterns source="Andrew Chen">
  <anti_pattern>DON'T send daily notifications to all users — segment by L28 first.</anti_pattern>
  <anti_pattern>DON'T re-engage with generic messages — personalize on user data.</anti_pattern>
  <anti_pattern>DON'T optimize for short-term opens at cost of unsubscribes.</anti_pattern>
  <best_practice>DO send notifications tied to social events (friends, mentions).</best_practice>
</anti_patterns>
</section>

<section id="retention_benchmarks">
<title>Retention Targets by Vertical</title>
<source>Amplitude data</source>

<benchmark vertical="Social/Communication" d1="50%" d7="30%" d30="22%" />
<benchmark vertical="Media/Entertainment" d1="35%" d7="18%" d30="12%" />
<benchmark vertical="Finance" d1="38%" d7="22%" d30="15%" />
<benchmark vertical="Gaming" d1="32%" d7="12%" d30="6%" />
<benchmark vertical="E-commerce" d1="25%" d7="12%" d30="8%" />
<benchmark vertical="Health/Fitness" d1="30%" d7="15%" d30="8%" />
<benchmark vertical="Travel" d1="20%" d7="8%" d30="4%" />

<note>Top quartile beat these by ~50%. Top decile (Instagram, TikTok, WhatsApp) can hit 70%+ D1 / 50%+ D30.</note>
</section>

</document>
