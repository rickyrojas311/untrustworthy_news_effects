# `master_data_2020.csv` — Data Documentation

## Overview

- **Shape**: 1,151 rows × 235 columns. One row per survey respondent.
- **Source**: Survey-plus-behavioral panel underlying *Moore et al., "Exposure to untrustworthy websites in the 2020 US election"* (Nature Human Behaviour, 2023, [doi:10.1038/s41562-023-01564-2](https://doi.org/10.1038/s41562-023-01564-2)). The replication paper and supplementary materials are bundled with this repo.
- **Two data sources are merged together**:
  1. **Survey responses** from a YouGov-style two-wave panel: a *pre-election* wave (Sept–early Oct 2020) and a *post-election* wave (Nov–early Dec 2020). Post-wave items carry the `_post` suffix.
  2. **Passive web-browsing trace data** from YouGov Pulse, covering **Oct 2 – Nov 9, 2020**. These appear at the end of the file (`untrustworthy_n`, `total_n`, `hard_news_n`, …) and are derived counts of URL visits classified against curated website lists.
- **All ID/sample**: `caseid` (1,151 unique). All respondents satisfied `consent == "I consent"` and `votereg == "Yes"` (so these two columns carry no information — they're filter screens).

> ⚠️ **Marked-uncertain items** (no codebook supplied) are flagged inline with **[uncertain]**. The supplementary PDF gives the analysis-ready operationalizations of the key constructs but does not name every survey item; questions referenced only by item code (e.g. `conf02`, `election_behaviors_3`, `tf_adv`) are documented from response-value inspection rather than verbatim wording.

---

## 1. Identifier & sample-frame

| Column | Type | Description |
|---|---|---|
| `caseid` | int | YouGov panelist ID. Unique per row. |
| `consent` | str | Consent screen. Always `"I consent"` (filter — those who didn't consent were dropped). |
| `votereg` | str | Voter-registration screen. Always `"Yes"`. |
| `weight` | float | Survey weight for the **pre-election** wave (YouGov sampling/post-stratification weight). |
| `weight_post` | float | Survey weight for the **post-election** wave. NA for respondents who didn't complete the post-wave (n = 162). |
| `starttime` / `endtime` | str (ISO 8601) | Start/end timestamps of the pre-election survey response. |
| `starttime_post` / `endtime_post` | str | Start/end timestamps of the post-election survey response. |

---

## 2. Fake-news perception battery — pre-election

This block measures *self-reported perception of fake news*. The full battery is asked once pre-election and again post-election (the post-election copies append `_post` to the column name; identical semantics).

| Column | Description |
|---|---|
| `encountered_fakenews1` | "How much fake news have you encountered?" 5-point ordinal: `"I have not encountered any fake news"` → `"A great deal"` → `"Nearly all news I see is fake"`. |
| `fakenews_pctg1` | Self-estimated percentage (0–100) of the news the respondent sees that is fake. |
| `fnews_story1` … `fnews_story9`, `fnews_story0` | Open-text responses: respondents describe up to **10 specific fake-news stories** they recall encountering. (`fnews_story0` is the 10th slot — note the digit-wrap from `1..9, 0`.) Slot N is more often filled than slot N+1, so missingness grows down the list. |
| `respondent_stories_1` … `_10` | Marker columns — value is `"selected"` if that story-slot was filled, NA otherwise. Parallel to `fnews_story{1..0}`. Useful for `count of fake-news stories supplied`. |
| `certainty_nofakenews1` | Only shown to respondents who said they encountered no fake news; measures how *certain* they are. 4-point Likert. Mostly NA (only ~9 respondents). |
| `fnews_story{i}_source` | Open text — where the respondent encountered story *i*. |
| `fnews_story{i}_recognize` | Open text — *how* the respondent recognized story *i* as fake. |
| `biggest_source1` | Single-select: "Biggest source of fake news you encounter." 6 categories — Social media, Television, Talk Radio, News websites, In-person discussion, Other. |
| `biggest_source_other1` | Open-text follow-up for "Other" responses. Mostly `__NA__`. |
| `detecting_fakenews1` | "How good are you at detecting fake news?" 5-point ordinal: Never able → Always able. |
| `fakenews_trend1` | "Is there more fake news today than in the past?" 5-point ordinal: much less → much more. |
| `socmed_use` | Self-reported daily social-media use (6 buckets: `Less than 10 minutes per day` … `More than 3 hours per day`). |

**Post-wave duplicates** of the above appear with the `_post` suffix and identical meaning, e.g. `encountered_fakenews1_post`, `fnews_story1_post`, `biggest_source1_post`, `socmed_use_post`.

---

## 3. Digital-literacy battery (`tf_*`)

Six items measuring self-rated familiarity with technology terms/concepts. Each is recorded on a 5-point self-rated certainty scale (`1  None`, `2  Little`, `3  Some`, `4  Good`, `5  Full`).

| Column | Concept (inferred from variable name) |
|---|---|
| `tf_adv` | Advertising/sponsored content [uncertain — likely "advanced search" or "ad-targeting"]. |
| `tf_pdf` | PDF files. |
| `tf_spy` | Spyware. |
| `tf_wiki` | Wikipedia / wikis. |
| `tf_cache` | Browser cache / cached pages. |
| `tf_phishing` | Phishing. |

> The exact prompt wording is not in the supplementary PDF. These look like a standard "Internet skills" battery (Hargittai-style), where respondents rate familiarity with each term. **[Item wording uncertain.]**

---

## 4. Political-knowledge battery

Four civic-knowledge multiple-choice items. The supplementary materials note that the `knowledge` score (see §10) is the count out of 4 of correct answers on **"Pew Research Center's civic knowledge questionnaire"**.

| Column | Question | Likely correct answer (from response frequencies) |
|---|---|---|
| `tie_senate` | Who breaks tied Senate votes? | "The vice president" |
| `filibuster` | How many Senate votes to end a filibuster? | 60 |
| `presidential_terms` | What limits a president to 2 terms? | "The 22nd Amendment of the Constitution" |
| `electoral_college` | What is the Electoral College? | "Is an assembly that formally elects the president" |

(There is a `"blank"` response category in `tie_senate`, `presidential_terms`, and `electoral_college` for respondents who skipped.)

---

## 5. Vote intention & vote choice

| Column | Description |
|---|---|
| `presvote2020` | Pre-election: who do you plan to vote for? `Donald Trump` / `Joe Biden` / `Another candidate` / `Undecided` / `Do not plan on voting`. |
| `presvote20post` | Post-election: who did you vote for? Adds `Jo Jorgensen`, `Howie Hawkins`, `Did not vote`, `Other`. NA for non-respondents (162). |
| `presvote20post_t` | Always NA in observed data — likely an open-text "Other (specify)" field. |
| `presvote20combined` | Harmonized vote variable combining pre/post (no NAs). Used for analysis. |
| `presvote16post` | Vote choice in 2016 (Clinton / Trump / Johnson / Stein / McMullin / Other / Did not vote). |
| `presvote16post_t` | Open-text "Other" — empty in observed data. |
| `turnout16` | Did you turn out to vote in 2016? `Yes` / `No`. |
| `won_election` | Post-election item: who won the 2020 election? `Joe Biden` / `Donald Trump` / `"I'm still not sure"`. |

---

## 6. Demographics

| Column | Description |
|---|---|
| `gender4` | Self-reported gender: `Male` / `Female` / `Other` / `Prefer not to say`. |
| `gender4_other` | Open follow-up. Mostly `__NA__`. |
| `birthyr` | Year of birth (4-digit). |
| `age` | Age in years at survey time. |
| `age4` | Age category: `Under 30`, `30-44`, `45-64`, `65+`. (Column displays the slash-form labels but the meaning is these four bins.) |
| `race` | 7-category race: `White`, `Black`, `Hispanic`, `Asian`, `Native American`, `Two or more races`, `Other`. |
| `race_other` | Open follow-up. All NA in observed data. |
| `hispanic` | `Yes` / `No`: Hispanic origin. |
| `hispanic_origin_1..4` | Multi-select sub-origin (Mexican / Puerto Rican / Cuban / Other Hispanic). Each is `"selected"` or NA. **[Specific labels per index uncertain.]** |
| `race4` | Collapsed 4-category race used by analyses: `White`, `Black`, `Hispanic`, `Other`. |
| `educ` | 6-category education: `No HS` → `Post-grad`. |
| `educ4` | Collapsed 4-category: `HS or less`, `Some college`, `College grad`, `Postgrad`. |
| `inputstate` | U.S. state name (50 unique). |
| `region` | Census region: `Northeast`, `Midwest`, `South`, `West`. |
| `zipcode` | Post-wave ZIP code (float because of NA encoding). NA for the 162 non-post-responders. |
| `occupation` | Open-text occupation (post-wave). |

---

## 7. News-consumption attitudes & politics (pre-wave only)

| Column | Description |
|---|---|
| `newsint` | "How often do you follow news?" 5-point: `Hardly at all` → `Most of the time`. (Pew/ANES standard `newsint` item.) |
| `sourcenews_new` | "Where do you get most of your news?" 14 categories (Television, A newspaper's website, Social network, Podcasts, …). |
| `pid3` | Party ID, 3-point: `Democrat` / `Independent` / `Republican` / `Other` / `Not sure`. |
| `pid3_other` | Open follow-up to `pid3`. |
| `pid7` | Party ID, 7-point branched: Strong Dem → Lean Dem → Independent → Lean Rep → Strong Rep (+ Not sure). |
| `ideo5` | Ideology 5-point: Very liberal → Liberal → Moderate → Conservative → Very conservative (+ Not sure). |
| `intuse` | Internet-use frequency: `Less often` → `Several times a day`. |

### Confidence-in-institutions battery (`conf02`–`conf12`)

Seven items asked on the standard 5-point confidence scale (`None at all` → `Very little` → `Some` → `Quite a lot` → `A great deal`). This is the GSS-style "confidence in institutions" battery. **The specific institution for each index is not documented in the supplementary PDF.** A typical GSS/CCES rotation in this slot covers items like: the press, the military, organized religion, the Supreme Court, banks/financial institutions, the executive branch, Congress, public schools, the scientific community. Without the codebook, treat each as an institution-specific confidence rating and confirm against the survey instrument before reporting any single item.

| Column | Question | Verified? |
|---|---|---|
| `conf02` | Confidence in [institution] | **[uncertain — institution label]** |
| `conf05` | Confidence in [institution] | **[uncertain]** |
| `conf07` | Confidence in [institution] — note `"None at all"` is the modal response, suggesting a highly distrusted institution (Congress? the press?) | **[uncertain]** |
| `conf08` | Confidence in [institution] | **[uncertain]** |
| `conf10` | Confidence in [institution] | **[uncertain]** |
| `conf11` | Confidence in [institution] | **[uncertain]** |
| `conf12` | Confidence in [institution] | **[uncertain]** |

---

## 8. Post-wave behavioral & COVID items

| Column | Description |
|---|---|
| `election_behaviors_1` … `_17` | 17-item checklist of political/election behaviors during the post-wave period. Each value is `"selected"` or `"not selected"`. **The specific behavior for each numeric index is not labeled in the supplementary PDF.** Likely candidates (typical CCES 2020 batteries): registered voters, donated to a candidate, attended a rally, displayed a sign, talked to others about politics, posted on social media about the election, watched the debates, wore a campaign button, volunteered, etc. **[Verify exact item wording in the original questionnaire before reporting.]** |
| `election_behaviors_oth` | Open text — "Other (please describe)" follow-up. |
| `covid_positive` | "Have you tested positive for COVID-19?" `Yes` / `No`. |
| `covid_month` | Month of positive test (string month name). |
| `covid_date` | Specific date of positive test (ISO 8601). |
| `self_isolation` | Self-isolation behavior, 4-point ordinal: `None of the time` → `All of the time`. |

---

## 9. Behavioral / web-trace measures (the **heart of the paper**)

These columns are **derived from YouGov Pulse browsing data** collected Oct 2 – Nov 9, 2020 and then classified against curated lists of websites. The classifications follow the paper's Methods section.

### Visit-count columns (`*_n`)

Each `*_n` column is the count of URL visits from that respondent during the data-collection window that match the named list.

| Column | List | Notes |
|---|---|---|
| `total_n` | All visited URLs | Total browsing volume for the respondent (any URL). Used as the denominator for "share of info diet" calculations. |
| `untrustworthy_n` | The paper's own curated list of **1,796 untrustworthy websites** (compiled per the Methods, extending Allcott & Gentzkow, Grinberg et al., and Guess et al.). | The headline treatment variable. |
| `untrustworthy_ng_n` | **[uncertain]** A more restrictive version of the untrustworthy list. The `_ng` suffix is undocumented in the supplementary PDF. Reasonable candidates: "No Google" (excludes visits referred from Google search), "No Guess" (untrustworthy sites that don't overlap with Guess et al.'s list), or "Newsguard"-derived. **Confirm against the replication R/Python code before relying on this column.** |
| `grinberg_n` | Grinberg et al. (2019) Science paper's untrustworthy-site list (a much smaller list — Guess et al. (2019) used a 42-site version of this). | Used for cross-study comparison. |
| `untrustworthy_conservative_n` | Untrustworthy sites coded as ideologically conservative. | Slant inferred from Bakshy et al. (2015) audience-ideology scores. |
| `untrustworthy_liberal_n` | Untrustworthy sites coded as ideologically liberal. | Far fewer respondents have nonzero visits here than to conservative untrustworthy (20 vs 58 unique nonzero counts). |
| `hard_news_n` | Visits to "hard news" sites — operationalized as the **5,471 sites contained in Bakshy et al. (2015) plus all NewsGuard-rated sites, excluding any flagged as repeatedly publishing false content**. | |
| `hard_news_conservative_n` | Hard-news visits classified as conservative-slanted. | |
| `hard_news_liberal_n` | Hard-news visits classified as liberal-slanted. | |
| `fact_check_n` | Visits to fact-checking sites: **PolitiFact, the Washington Post Fact Checker, Factcheck.org, Snopes** (per Guess et al. 2020). | |

### Flag columns (`*_flag`) — binary exposure indicators

Each `*_flag` is `1` if the corresponding `*_n` is ≥ 1 (i.e., the respondent was *exposed to at least one site* on that list), else `0`. This binary exposure variable is the primary outcome in the paper's headline analyses.

`untrustworthy_flag`, `untrustworthy_ng_flag`, `grinberg_flag`, `untrustworthy_conservative_flag`, `untrustworthy_liberal_flag`, `hard_news_flag`, `hard_news_conservative_flag`, `hard_news_liberal_flag`, `fact_check_flag`.

### Share-of-diet columns (`percentage_*`, `*_percentage_*`)

| Column | Numerator / denominator |
|---|---|
| `percentage_of_total` | `untrustworthy_n / total_n` — share of *all* web visits that went to untrustworthy sites. |
| `percentage_of_total_ng` | `untrustworthy_ng_n / total_n`. **[uncertain — see note on `_ng` above.]** |
| `grinberg_percentage_of_total` | `grinberg_n / total_n`. |
| `untrustworthy_conservative_percentage_info_diet` | `untrustworthy_conservative_n` as share of the respondent's hard-news ("information") diet rather than total browsing. NA when the respondent has zero hard-news visits (90 cases). |
| `untrustworthy_liberal_percentage_info_diet` | Same construction for liberal-slanted untrustworthy. |
| `untrustworthy_all_percentage_info_diet` | Sum of the two above. |

### Ideological-slant summary

| Column | Description |
|---|---|
| `mean_ideology` | Weighted-mean ideological slant of the respondent's hard-news diet, computed from Bakshy et al. (2015)–style audience-alignment scores per visited domain. Negative values = more liberal-leaning diet; positive = more conservative-leaning. NA when the respondent has too few hard-news visits to estimate (141 cases). |
| `ideology_bin` | Decile (1–10) of `mean_ideology` across the sample. 1 = most liberal media diet, 10 = most conservative. NA when `mean_ideology` is NA. |
| `total_n_bin_all` | Tertile (1, 2, 3) of `total_n` (total browsing volume) across the full sample — low, medium, high web activity. |
| `hard_news_bin` | Tertile of `hard_news_n` among those with at least one hard-news visit. **[uncertain — `hard_news_bin` and `hard_news_bin_all` both have 3 unique values; the distinction may be the conditional vs. unconditional sample.]** |
| `hard_news_bin_all` | Tertile of `hard_news_n` across the **full sample**. |

---

## 10. Analyst-ready (re-encoded) versions of survey variables

These columns are derived recodes used in the paper's regression tables (per Supplementary Tables 1, 6, 8). They are numeric and ready to drop into a model.

### Demographic dummies (one-hot from `race4`, `gender4`, `ideo5`, `age4`)

`race4_white`, `race4_black`, `race4_hispanic`, `race4_other`,
`gender4_male`, `gender4_female`, `gender4_other_2`, `gender4_prefer_not_to_say`,
`ideo5_very_liberal`, `ideo5_liberal`, `ideo5_moderate`, `ideo5_conservative`, `ideo5_very_conservative`, `ideo5_not_sure`,
`age4_under_30`, `age4_30_44`, `age4_45_64`, `age4_65`.

All are `{0, 1}` indicator columns.

### Continuous / collapsed analysis variables

| Column | Encoding (verified against supplementary Table 1 / 6 / 8 captions) |
|---|---|
| `education` | 1–4 ordinal recoding of `educ4`: 1 = HS or less, 2 = Some college, 3 = College grad, 4 = Postgrad. |
| `educ_factor` | Same values as `education` (kept as factor in R). |
| `college` | 1 if college graduate (i.e., `educ4 ∈ {College grad, Postgrad}`), else 0. |
| `female` | 1 if `gender4 == "Female"`, else 0. |
| `non_white` | 1 if `race4 != "White"`, else 0. |
| `trump_support` | **Defined per the paper as: 1 if intending to vote for Trump in 2020 (pre-wave), 0 otherwise.** Not a vote-cast indicator — it's intent. |
| `biden_support` | 1 if intending to vote for Biden in 2020, 0 otherwise. (Mirror of `trump_support`.) |
| `knowledge` | Count (0–4) of `tie_senate`, `filibuster`, `presidential_terms`, `electoral_college` answered correctly. The paper calls this "political knowledge". |
| `interest` | 1–4 recoding of `newsint`: 1 = `Hardly at all`, 4 = `Most of the time`. The paper calls this "political interest". |

---

## Suggested usage notes / gotchas

1. **Two waves, two weights.** Don't average `weight` and `weight_post`. Pick the one matching the wave you're analyzing. Post-wave items have 162 NA respondents (attrition).
2. **`_post` suffix is wave indicator, not "treatment".** It refers to time (Sept vs Nov/Dec survey) — not pre/post treatment in any causal sense. The behavioral *exposure* itself is measured across the campaign window via web traces, not by the survey wave.
3. **`fnews_story0` is the 10th slot.** Column ordering wraps `1,2,…,9,0`. Make sure list-comprehension / loops include `0`.
4. **`votereg` and `consent` are constants** — both screens. Drop before modeling.
5. **`untrustworthy_flag == 1` ≠ "treated".** The paper measures *exposure*; respondents self-select into exposure. Treat as endogenous in causal models.
6. **NA encoding is inconsistent**: text columns use both `NA` and `__NA__` and Python's `pd.read_csv` will interpret `NA` as missing but not `__NA__`. Normalize early in your pipeline.
7. **`mean_ideology` is NA for 141 respondents** with too few hard-news visits to estimate slant. Their `ideology_bin` is also NA. They are still in the sample for binary-exposure regressions.
8. **The `_ng` suffix is undocumented in the supplementary materials** — before using `untrustworthy_ng_*` or `percentage_of_total_ng`, check the bundled replication code to confirm what list/filter it represents.

---

## Open questions / items to verify against the replication code

The following are points where this documentation is inferring from data inspection or the supplementary PDF and would benefit from direct confirmation in the original `.R` / `.Rmd` / `.py` replication script:

- **`_ng` suffix**: what list or filter does it represent?
- **`conf02`–`conf12`**: what institution does each index correspond to?
- **`election_behaviors_1`–`_17`**: what behavior does each index correspond to?
- **`tf_adv`**: the term `adv` is ambiguous (advertising? advanced search? adversarial content?).
- **`hispanic_origin_1`–`_4`**: which Hispanic-origin category corresponds to each index?
- **`hard_news_bin` vs `hard_news_bin_all`**: is the difference conditional-on-nonzero vs. unconditional, or some other distinction?
