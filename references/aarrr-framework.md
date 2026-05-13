# AARRR Framework — Deep Reference

<document>
<metadata>
  <title>AARRR Framework Deep Reference</title>
  <purpose>Authoritative methodology for AARRR funnel analysis. Loaded by the b2c-growth-analytics SKILL during Step 2.</purpose>
  <sources>
    <source>Dave McClure — original AARRR / Pirate Metrics</source>
    <source>Sean Ellis — GrowthHackers methodology</source>
    <source>Amplitude — Product Analytics Playbook</source>
    <source>Lenny Rachitsky — B2C case studies</source>
  </sources>
</metadata>

<section id="acquisition">
<title>Acquisition</title>
<definition>The process by which users discover and sign up for your product.</definition>

<metrics>
  <metric name="CAC" formula="Total Spend / New Users" note="Always split by channel." />
  <metric name="Blended CAC" formula="Total Marketing Spend / All New Users" note="Includes organic users." />
  <metric name="Payback Period" formula="CAC / (ARPU × Gross Margin)" target="&lt; 12 months for B2C" />
  <metric name="Traffic-to-Signup Rate" formula="Signups / Visitors" note="Signals landing page quality." />
</metrics>

<framework name="channel_quality" source="Lenny Rachitsky">
  <principle>Channels are NOT equal. Always include downstream retention quality, not just acquisition volume.</principle>
  <channel name="Organic / SEO">Typically highest LTV and highest intent.</channel>
  <channel name="Paid Social (Meta, TikTok)">High volume, but often lowest D7 retention.</channel>
  <channel name="Referral / Word-of-Mouth">Highest LTV and retention of all channels.</channel>
  <channel name="Influencer">Highly variable — depends on audience-product fit.</channel>
  <rule>Always cross-tab new user volume by channel with D30 retention. A channel with 5x CAC but 2x retention may still be superior in LTV terms.</rule>
</framework>

<signal name="acquisition_saturation" source="Andrew Chen">
  <indicator>CAC rising YoY on your best channels.</indicator>
  <responses>
    <response>Expand to new channels (content, community, partnerships).</response>
    <response>Improve conversion rate on existing traffic.</response>
    <response>Invest in referral/virality to reduce paid dependency.</response>
  </responses>
</signal>
</section>

<section id="activation">
<title>Activation</title>
<definition>The moment a new user experiences the core value of your product for the first time — the "Aha Moment."</definition>

<methodology name="statistical_aha_moment_finder" source="Sean Ellis">
  <step>Take users who retained at Day-7 or beyond ("successful" users).</step>
  <step>Take users who churned before Day-7 ("failed" users).</step>
  <step>Compare which actions in the first 24-72 hours are overrepresented in the successful group.</step>
  <step>Actions with the highest lift = your Aha Moment candidates.</step>
  <code language="python"><![CDATA[
import pandas as pd
from scipy import stats

retained = df[df['d7_retained'] == True]['first_day_actions'].str.get_dummies(sep=',')
churned = df[df['d7_retained'] == False]['first_day_actions'].str.get_dummies(sep=',')

aha_candidates = {}
for action in retained.columns:
    r_rate = retained[action].mean()
    c_rate = churned[action].mean() if action in churned.columns else 0
    lift = r_rate / (c_rate + 0.001)
    aha_candidates[action] = {
        'retained_rate': r_rate,
        'churned_rate': c_rate,
        'lift': lift
    }

aha_df = pd.DataFrame(aha_candidates).T.sort_values('lift', ascending=False)
  ]]></code>
</methodology>

<framework name="activation_funnel">
  <description>Map onboarding steps between signup and Aha Moment. Every step is a drop-off point.</description>
  <example>
    <step rate="100%">Sign-up</step>
    <step rate="72%">Email Confirm</step>
    <step rate="55%">Profile Setup</step>
    <step rate="40%">First Core Action</step>
    <step rate="28%">Aha Moment</step>
  </example>
  <rule>Focus on the biggest absolute drop first.</rule>
</framework>

<benchmarks name="b2c_activation" source="Amplitude">
  <benchmark vertical="Mobile consumer apps" day1_activation="25-50%" />
  <benchmark vertical="Social/community apps" day1_activation="30-45%" />
  <benchmark vertical="E-commerce (first purchase within 30 days)" day1_activation="20-35%" />
  <benchmark vertical="Content/media" day1_activation="40-60%" />
</benchmarks>
</section>

<section id="retention">
<title>Retention</title>
<definition>Whether users come back. The single most important metric for sustainable growth.</definition>
<note>For deep retention methodology, see retention-playbook.md.</note>

<metrics>
  <metric name="D1 Retention" definition="% returning next day" b2c_mobile_benchmark="25-40%" />
  <metric name="D7 Retention" definition="% returning on day 7" b2c_mobile_benchmark="15-25%" />
  <metric name="D30 Retention" definition="% returning on day 30" b2c_mobile_benchmark="10-20%" />
  <metric name="L28 (Monthly)" definition="Days active in last 28 days" engaged_threshold="&gt; 4 days" />
  <metric name="Churn Rate" definition="% who stop using / period" subscription_target="&lt; 5% / month" />
</metrics>

<framework name="retention_curve_test">
  <shape name="smiling_curve" outcome="Product-market fit exists">Curve flattens above 0%. Scale acquisition now.</shape>
  <shape name="declining_to_zero" outcome="No PMF">Stop scaling. Fix product before paid acquisition.</shape>
  <shape name="slow_decline" outcome="Partial PMF">Some users love it but base is leaking. Segment to find retained subgroup.</shape>
</framework>
</section>

<section id="referral">
<title>Referral</title>
<definition>Existing users bringing in new users.</definition>

<metric name="K_factor" formula="Invitations per user × Conversion rate of invitations">
  <interpretation k="&gt; 1.0">Exponential viral growth — every user creates more than one new user.</interpretation>
  <interpretation k="0.5">Each user brings 0.5 new users — supplement with paid.</interpretation>
  <interpretation k="&lt; 0.1">Effectively no viral loop.</interpretation>
</metric>

<framework name="virality_types" source="Andrew Chen">
  <type name="word_of_mouth_virality">Users tell friends organically (NPS-driven).</type>
  <type name="incentivized_referral">Dropbox model — reward both sides.</type>
  <type name="inherent_product_virality">Product only works with others (WhatsApp, Slack).</type>
  <type name="showcase_virality">Users share outputs (Spotify Wrapped, Canva designs).</type>
  <type name="communication_virality">Notifications/invites to non-users (calendar invites).</type>
</framework>

<health_metrics>
  <metric name="NPS" strong="&gt; 40" excellent="&gt; 60" />
  <metric name="Referral rate" healthy="&gt; 5% of users refer ≥1 person/month" />
  <metric name="Referral conversion rate" definition="% of referred people who sign up and activate" />
</health_metrics>
</section>

<section id="revenue">
<title>Revenue</title>
<definition>Monetizing users, not just retaining them.</definition>

<formula name="LTV_subscription">LTV = ARPU / Churn Rate</formula>
<formula name="LTV_ecommerce">LTV = AOV × Purchase Frequency × Avg Customer Lifespan</formula>

<framework name="ltv_cac_ratio">
  <ratio value="&lt; 1x" interpretation="Burning money" action="Stop scaling; fix fundamentals." />
  <ratio value="1-2x" interpretation="Marginally viable" action="Optimize conversion and retention." />
  <ratio value="3x" interpretation="Healthy" action="Standard scaling threshold." />
  <ratio value="&gt; 5x" interpretation="Excellent — may be underinvesting" action="Increase acquisition spend." />
</framework>

<expansion_levers>
  <lever id="1">Conversion rate (free → paid).</lever>
  <lever id="2">Average order value (upsell, bundles, premium tiers).</lever>
  <lever id="3">Purchase frequency (re-engagement, subscriptions, habits).</lever>
  <lever id="4">Win-back (recovering churned paying users).</lever>
</expansion_levers>
</section>

<funnel_audit_checklist>
<purpose>Use this checklist when first receiving data to ensure comprehensive analysis.</purpose>
<item>Can every data column be mapped to an AARRR stage?</item>
<item>Is cohort-level data available (not just aggregate)?</item>
<item>Is channel-level acquisition data available?</item>
<item>Is retention measured at D1/D7/D30 minimum?</item>
<item>Is BOTH new user volume AND downstream quality available per channel?</item>
<item>Is revenue per user (ARPU/LTV estimate) present?</item>
<item>Is referral/invite tracking data captured?</item>
<item>Are timestamps available for time-series analysis?</item>
</funnel_audit_checklist>

</document>
