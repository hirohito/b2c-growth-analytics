# B2C Growth Benchmarks — Industry Reference

<document>
<metadata>
  <title>B2C Growth Benchmarks</title>
  <purpose>Industry reference benchmarks for B2C products. Loaded by the b2c-growth-analytics SKILL when comparing user metrics against industry standards.</purpose>
  <sources>
    <source>Amplitude — Product Benchmarks Report (annual)</source>
    <source>Lenny's Newsletter — benchmark posts</source>
    <source>Reforge — internal data</source>
    <source>Andreessen Horowitz — consumer benchmarks</source>
    <source>RevenueCat — State of Subscription Apps</source>
    <source>Mixpanel — product benchmarks</source>
    <source>AppsFlyer — Performance Index</source>
  </sources>
  <warning>Use benchmarks as a directional sanity check, NOT a target. Absolute numbers depend on vertical, geo mix, and channel mix. The most useful comparison is YOUR product over time.</warning>
</metadata>

<section id="retention_benchmarks">
<title>Retention Benchmarks by Vertical</title>
<source>Amplitude</source>

<benchmark vertical="Social Networking" d1="41%" d7="23%" d30="16%" top_quartile_d30="28%" />
<benchmark vertical="Communication/Messaging" d1="50%" d7="32%" d30="23%" top_quartile_d30="38%" />
<benchmark vertical="Finance" d1="38%" d7="22%" d30="15%" top_quartile_d30="24%" />
<benchmark vertical="Media/Entertainment" d1="35%" d7="18%" d30="12%" top_quartile_d30="22%" />
<benchmark vertical="Sports" d1="32%" d7="16%" d30="10%" top_quartile_d30="18%" />
<benchmark vertical="Gaming (casual)" d1="32%" d7="12%" d30="6%" top_quartile_d30="12%" />
<benchmark vertical="Gaming (hardcore)" d1="38%" d7="18%" d30="11%" top_quartile_d30="20%" />
<benchmark vertical="Health/Fitness" d1="30%" d7="15%" d30="8%" top_quartile_d30="16%" />
<benchmark vertical="E-commerce" d1="25%" d7="12%" d30="8%" top_quartile_d30="14%" />
<benchmark vertical="Travel" d1="20%" d7="8%" d30="4%" top_quartile_d30="10%" />
<benchmark vertical="Food &amp; Drink" d1="22%" d7="10%" d30="6%" top_quartile_d30="12%" />
<benchmark vertical="Education" d1="30%" d7="14%" d30="8%" top_quartile_d30="18%" />
<benchmark vertical="Productivity" d1="33%" d7="17%" d30="11%" top_quartile_d30="20%" />

<subsection title="Monthly Churn (Subscription B2C)" source="RevenueCat">
  <benchmark vertical="Streaming (video)" median_churn="4-6%" top_decile="&lt; 3%" />
  <benchmark vertical="Streaming (music)" median_churn="3-5%" top_decile="&lt; 2%" />
  <benchmark vertical="Dating" median_churn="8-15%" top_decile="&lt; 6%" />
  <benchmark vertical="Fitness" median_churn="6-10%" top_decile="&lt; 4%" />
  <benchmark vertical="Meditation/Wellness" median_churn="5-9%" top_decile="&lt; 3%" />
  <benchmark vertical="News" median_churn="4-8%" top_decile="&lt; 3%" />
  <benchmark vertical="Photo/Video editing" median_churn="7-12%" top_decile="&lt; 5%" />
  <benchmark vertical="Language learning" median_churn="8-14%" top_decile="&lt; 6%" />
</subsection>
</section>

<section id="activation_benchmarks">
<title>Activation Benchmarks</title>
<source>Lenny's Newsletter survey</source>

<benchmark product_type="Free-to-use consumer app" median_activation="25-35%" top_quartile="50%+" />
<benchmark product_type="Freemium SaaS-for-consumers" median_activation="15-25%" top_quartile="35%+" />
<benchmark product_type="Subscription trial → paid" median_activation="35-55%" top_quartile="70%+" />
<benchmark product_type="E-commerce: signup → first purchase" median_activation="5-15%" top_quartile="25%+" />
<benchmark product_type="Marketplace: signup → first transaction" median_activation="8-18%" top_quartile="30%+" />
<benchmark product_type="Social: signup → first post/follow" median_activation="30-50%" top_quartile="70%+" />

<subsection title="Time-to-Aha-Moment">
  <benchmark category="Best-in-class consumer apps" target="&lt; 60 seconds" />
  <benchmark category="Average" target="3-5 minutes" />
  <benchmark category="Redesign threshold" target="&gt; 10 minutes" />
</subsection>
</section>

<section id="acquisition_benchmarks">
<title>Acquisition Benchmarks</title>

<subsection title="Cost-Per-Install (Mobile, 2024-2025, US)">
  <benchmark channel="Meta (Facebook/Instagram)" ios="$3.50-$8" android="$1.50-$4" />
  <benchmark channel="TikTok" ios="$2-$6" android="$1-$3" />
  <benchmark channel="Google App Campaigns" ios="$2.50-$6" android="$1-$3" />
  <benchmark channel="Apple Search Ads" ios="$1.50-$5" android="n/a" />
  <benchmark channel="Snapchat" ios="$2.50-$5" android="$1.50-$3" />
</subsection>

<subsection title="CAC (Subscription B2C, blended)">
  <benchmark vertical="Streaming" median_cac="$25-$80" top_decile="&lt; $20" />
  <benchmark vertical="Dating" median_cac="$15-$60" top_decile="&lt; $15" />
  <benchmark vertical="Fitness/Wellness" median_cac="$30-$100" top_decile="&lt; $25" />
  <benchmark vertical="Edtech" median_cac="$40-$120" top_decile="&lt; $30" />
  <benchmark vertical="Finance/Fintech" median_cac="$50-$300" top_decile="&lt; $40" />
</subsection>

<subsection title="LTV/CAC Ratios">
  <benchmark category="Healthy" target="&gt; 3x" />
  <benchmark category="Top-decile" target="&gt; 5x" />
  <benchmark category="Unsustainable" warning="&lt; 1x" />
  <benchmark category="Razor-thin" warning="&lt; 2x" />
</subsection>

<subsection title="Payback Period">
  <benchmark category="Strong consumer subscription" target="&lt; 6 months" />
  <benchmark category="Acceptable" target="6-12 months" />
  <benchmark category="Concerning" warning="12-18 months" />
  <benchmark category="Likely unsustainable" warning="&gt; 18 months (unless very high LTV)" />
</subsection>
</section>

<section id="monetization_benchmarks">
<title>Monetization Benchmarks</title>

<subsection title="Free → Paid Conversion (Subscription B2C)">
  <benchmark model="Free trial → Paid" median="40-60%" top_quartile="70%+" />
  <benchmark model="Freemium (no trial)" median="2-5%" top_quartile="8%+" />
  <benchmark model="Hard paywall" median="3-8%" top_quartile="12%+" />
  <benchmark model="Soft paywall (metered)" median="1-3%" top_quartile="5%+" />
</subsection>

<subsection title="Annual ARPU (Subscription B2C)">
  <benchmark vertical="Streaming (video)" arpu="$80-$160" />
  <benchmark vertical="Streaming (music)" arpu="$50-$110" />
  <benchmark vertical="Dating" arpu="$60-$200" />
  <benchmark vertical="Fitness" arpu="$60-$150" />
  <benchmark vertical="Meditation" arpu="$60-$80" />
  <benchmark vertical="News" arpu="$50-$120" />
  <benchmark vertical="Language learning" arpu="$60-$120" />
</subsection>

<subsection title="Trial-to-Paid Drop-off (after trial ends)">
  <benchmark category="Strong" target="&lt; 30% cancel" />
  <benchmark category="Average" target="30-50% cancel" />
  <benchmark category="Weak" warning="&gt; 50% cancel within 7 days post-trial" />
</subsection>
</section>

<section id="engagement_benchmarks">
<title>Engagement Benchmarks</title>

<subsection title="DAU/MAU Ratio">
  <benchmark category="Social / Messaging" healthy="50%+" />
  <benchmark category="Music streaming" healthy="35-50%" />
  <benchmark category="Video streaming" healthy="30-45%" />
  <benchmark category="Gaming (live ops)" healthy="25-40%" />
  <benchmark category="News" healthy="20-35%" />
  <benchmark category="Banking" healthy="15-25%" />
  <benchmark category="Fitness" healthy="10-25%" />
  <benchmark category="E-commerce" healthy="5-15%" />
  <benchmark category="Travel" healthy="3-10%" />
</subsection>

<subsection title="Sessions per User per Week (Mobile B2C)">
  <benchmark vertical="Social" median="30+" />
  <benchmark vertical="Messaging" median="100+" />
  <benchmark vertical="News" median="14" />
  <benchmark vertical="Music" median="20" />
  <benchmark vertical="Fitness" median="5" />
  <benchmark vertical="Banking" median="4" />
  <benchmark vertical="E-commerce" median="3" />
  <benchmark vertical="Travel" median="1-2" />
</subsection>
</section>

<section id="referral_benchmarks">
<title>Referral &amp; Virality Benchmarks</title>

<subsection title="K-Factor">
  <benchmark category="Viral product (world-class, rare)" k="&gt; 1.0" />
  <benchmark category="Strong supplementary virality" k="0.3-0.7" />
  <benchmark category="Weak virality" k="0.1-0.3" />
  <benchmark category="Effectively no virality" k="&lt; 0.1" />
</subsection>

<subsection title="NPS (Net Promoter Score) — Consumer Apps">
  <benchmark score="&gt; 70" interpretation="World-class (Apple, Netflix territory)" />
  <benchmark score="50-70" interpretation="Strong" />
  <benchmark score="30-50" interpretation="Good" />
  <benchmark score="10-30" interpretation="Average — meaningful work needed" />
  <benchmark score="0-10" interpretation="Weak" />
  <benchmark score="&lt; 0" interpretation="Problem — more detractors than promoters" />
</subsection>

<subsection title="Referral Rate (% of MAU who refer ≥1 person/month)">
  <benchmark category="Strong" target="&gt; 8%" />
  <benchmark category="Average" target="3-7%" />
  <benchmark category="Weak" warning="&lt; 2%" />
</subsection>
</section>

<section id="diagnostic_rules" priority="critical">
<title>Quick Diagnostic Rules of Thumb</title>
<usage>When reviewing a B2C product's metrics, flag these against these red lines.</usage>

<status level="critical" color="red" label="Stop &amp; Fix Before Scaling">
  <rule>D30 retention &lt; 5% (any vertical)</rule>
  <rule>Quick Ratio &lt; 1.0 (shrinking)</rule>
  <rule>LTV/CAC &lt; 1.0 (burning money)</rule>
  <rule>Activation rate &lt; 15% (broken onboarding)</rule>
  <rule>Monthly churn &gt; 15% (subscription)</rule>
</status>

<status level="caution" color="yellow" label="Needs Optimization">
  <rule>D30 retention in bottom quartile of vertical</rule>
  <rule>Quick Ratio between 1.0-1.5</rule>
  <rule>LTV/CAC between 1.0-2.5</rule>
  <rule>Payback period &gt; 18 months</rule>
  <rule>DAU/MAU well below vertical median</rule>
</status>

<status level="healthy" color="green" label="Scale With Confidence">
  <rule>D30 retention in top quartile of vertical</rule>
  <rule>Quick Ratio &gt; 3</rule>
  <rule>LTV/CAC &gt; 3</rule>
  <rule>Payback period &lt; 6 months</rule>
  <rule>Power user tier (L21+) &gt; 15% of MAU</rule>
</status>
</section>

<section id="further_reading">
<title>Sources &amp; Further Reading</title>
<purpose>For the most current benchmarks, cross-reference these sources.</purpose>

<source name="Amplitude Product Benchmarks Report" frequency="annual" cost="free" />
<source name="RevenueCat State of Subscription Apps" frequency="annual" cost="free" focus="mobile" />
<source name="AppsFlyer Performance Index" focus="acquisition cost data" />
<source name="Lenny's Newsletter benchmark posts" url="lennysnewsletter.com" />
<source name="Andreessen Horowitz consumer benchmarks" url="a16z.com" />
<source name="First Round Review" focus="case studies" />
</section>

</document>
