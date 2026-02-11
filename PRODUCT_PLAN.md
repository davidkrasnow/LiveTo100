# LiveTo100.ai Product Plan

## Vision
LiveTo100.ai helps members increase their probability of living to age 100 by translating medical data and daily behaviors into a simple score, clear next actions, and social accountability.

## Core Product Experience
1. **Account setup**
   - Email/password or SSO sign-up.
   - Identity profile: age, sex at birth, gender identity, country, timezone.
   - Consent screens for health data and community sharing.
2. **Baseline intake**
   - Health questionnaire (sleep, exercise, nutrition, stress, alcohol, tobacco, family history, medications, known conditions).
   - Biomarker entry (manual + lab upload/API): blood pressure, resting HR, A1C, fasting glucose, ApoB/LDL/HDL/TG, hsCRP, BMI/body composition, VO2max estimate, etc.
   - Optional wearable integrations (Apple Health, Oura, Whoop, Garmin).
3. **Score and insights**
   - Personal **LiveTo100 Score** = estimated odds of reaching 100 (displayed as percentage and percentile vs peers by age/sex).
   - Subscores by domain: cardio-metabolic, fitness, recovery, lifestyle, prevention.
   - Explainability panel: “what is helping your score” and “what is hurting your score.”
4. **Recommendations**
   - Personal action plan with 30/60/90-day priorities.
   - “Bring to doctor” brief with suggested labs, guideline references, and discussion prompts.
   - Habit tracking with expected score impact.
5. **Group accountability (YPO forum mode)**
   - Private invite-only groups.
   - Leaderboard (overall score, monthly delta, consistency streak).
   - Weekly check-ins, comments, and nudges.
   - Admin controls for visibility/privacy in group.

## MVP Scope (First 12 Weeks)
### In scope
- Authentication and profile onboarding.
- Questionnaire + manual biomarker entry.
- V1 scoring model (rules + weighted risk factors).
- Recommendations engine (rule-based) + doctor brief PDF.
- Private group with leaderboard and progress trends.

### Out of scope (phase 2+)
- Clinical diagnosis claims.
- Full EHR integration.
- AI coach chat with unrestricted medical advice.
- Insurance/employer analytics.

## Suggested Scoring Framework (V1)
- Use a transparent weighted model grounded in published mortality/longevity risk factors.
- Separate **non-modifiable** (age, sex, family history) from **modifiable** factors.
- Map each factor to a normalized 0–100 contribution.
- Convert total score to “estimated likelihood of reaching 100” with calibration bands.
- Recalculate score whenever new biomarker or behavior data is entered.

### Example top weighted modifiable inputs
- Blood pressure control
- ApoB / LDL-C and triglycerides
- Glycemic control (A1C / fasting glucose)
- Body composition / visceral adiposity proxy
- Cardiorespiratory fitness
- Sleep duration/regularity
- Tobacco and alcohol risk
- Activity minutes + strength frequency

## Compliance & Trust Guardrails
- HIPAA-aligned architecture if handling PHI in U.S.
- Clear disclaimer: educational insights, not diagnosis/treatment.
- Recommendation citations to trusted guidelines.
- Data controls: private by default, explicit opt-in for group sharing.
- Audit trails for score changes and recommendation updates.

## Data Model (High-Level)
- `users`
- `profiles`
- `questionnaire_responses`
- `biomarker_measurements`
- `scores` (overall + subscores + model version)
- `recommendations`
- `doctor_briefs`
- `groups`
- `group_memberships`
- `check_ins`
- `leaderboard_snapshots`

## KPI Dashboard
- Activation: % users completing onboarding + baseline biomarkers.
- Engagement: weekly active users, check-in completion.
- Outcome: average score change at 30/90/180 days.
- Social: group retention, accountability interactions.
- Clinical usefulness: doctor-brief download rate and follow-up lab completion.

## Brand Positioning Ideas
- **Primary:** LiveTo100.ai
- **Tagline options:**
  - “Know your odds. Improve your future.”
  - “Your path to 100, backed by data.”
  - “Score your longevity. Raise it together.”

## Immediate Next Steps
1. Validate willingness-to-pay with 10–15 YPO peers.
2. Define V1 biomarker list and scoring weights with a medical advisor.
3. Build clickable prototype (onboarding → score → recommendations → group board).
4. Run a 6-week pilot with one forum cohort.
5. Use pilot outcomes to refine model and messaging before public launch.
