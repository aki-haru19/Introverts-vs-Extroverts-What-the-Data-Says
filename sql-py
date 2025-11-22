--H1. Behavioral Comparison - introvert vs extravert across metrics
SELECT
	pd."Personality",
	AVG(pd."Time_spent_Alone") AS avg_time_alone,
	AVG(pd."Social_event_attendance") AS avg_social_event_attendence,
	AVG(pd."Going_outside") AS avg_going_outside,
	AVG(pd."Friends_circle_size") AS avg_friend_circle,
	AVG(pd."Post_frequency") AS avg_post_frequency,
	COUNT( CASE WHEN pd."Drained_after_socializing" = 'Yes' THEN 1 END ) AS cnt_drain_after_social,
	COUNT( CASE WHEN pd."Stage_fear" = 'Yes' THEN 1 END ) AS cnt_stage_fear
FROM
	personality_datasert pd
GROUP BY
	pd."Personality"

--H2. Correlation Analysis
SELECT
	CORR(pd."Time_spent_Alone", pd."Social_event_attendance" )
FROM
	personality_datasert pd

--H3. Sociality Score
WITH sociality_score AS (
SELECT
	pd."Personality",
	(pd."Social_event_attendance" + pd."Going_outside" +
	pd."Post_frequency" + pd."Friends_circle_size") AS sociality_score
FROM
	personality_datasert pd ),
ranked AS (
SELECT
	sociality_score,
	sc."Personality" ,
	ROW_NUMBER() OVER (PARTITION BY "Personality" ORDER BY sociality_score DESC ) AS rn
FROM
	sociality_score sc)
SELECT
	*
FROM
	ranked
WHERE
	rn<11

--H4. Post Frequency Variation 
SELECT
	pd."Personality",
	AVG(pd."Post_frequency") AS avg_post,
	MIN(pd."Post_frequency") AS min_post,
	MAX(pd."Post_frequency") AS max_post,
	STDDEV(pd."Post_frequency") AS sigma
FROM
	personality_datasert pd
GROUP BY
	pd."Personality"

--H5. Outlier Detection 
WITH sociality_score AS (
SELECT
	pd."Personality" ,
	pd."Social_event_attendance" + pd."Going_outside" + pd."Post_frequency" +
	pd."Friends_circle_size" AS sociality_score
FROM
	personality_datasert pd),
calc AS (
SELECT
	"Personality",
	AVG(sociality_score) AS mu,
	STDDEV(sociality_score) AS sigma
FROM
	sociality_score
GROUP BY
	"personality"),
z_score AS (
SELECT
	sc."Personality",
	sc.sociality_score,
	c.mu,
	c.sigma,
	(sc.sociality_score - c.mu )/ NULLIF(c.sigma, 0) AS z_score
FROM
	calc c
INNER JOIN sociality_score sc ON
	sc."Personality" = c."Personality" 
)
SELECT
	*
FROM
	z_score
WHERE
	z_score >2
	OR z_score <-2

--H6.  Statistical Testing 
ELECT 
    "Personality",
    AVG("Going_outside") as avg_going_outside,
    STDDEV("Going_outside") as std
FROM personality_datasert
GROUP BY "Personality"

-------------------------
  
import pandas as pd
from scipy import stats

intro = df[df['Personality'] == 'Introvert']['Going_outside']
extra = df[df['Personality'] == 'Extrovert']['Going_outside']

print(f"Intro: mean={intro.mean()}, std={intro.std()}")
print(f"Extra: mean={extra.mean()}, std={extra.std()}")

t_stat, p_value = stats.ttest_ind(intro, extra)
print(f"t-statistic: {t_stat}, p-value: {p_value}")
