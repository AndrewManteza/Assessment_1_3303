I initially used Logisitc Regression and Random Forest,


I was able to keep most outlier values due to the delivery times being legitimately high due to delays.

I had to normalize the weight though since the range of the values were too far apart.

Winsorizing the train data by capping outliers where I can keep the rows so I do not lose information. I want to keep the sign that the number is an outlier but be able to run it with Logistic Regression.

I found the values to be personally not as satisfactory with the following values:

| Model               | Precision | Recall   | F1       |
| ------------------- | --------- | -------- | -------- |
| Logistic Regression | 0.431235  | 0.748988 | 0.547337 |
| Random Forest       | 0.880000  | 0.089069 | 0.161765 |

Logistic Regression:

The baseline model achieved a recall of 75% for late shipments, meaning it identified approximately 75% of shipments that were actually late. However, its precision was only 43%, indicating a high number of false positive alerts.

I will be choosing the Logistic Regression since we can have a threshold of 70%, for this as we can afford around 43% of false alarms. Although as the business scales, the model needs to be retrained since scaling this as more deliveries are happening in the thousands will cause alert fatigue and might drive the monitoring down.

If its possible, the threshold might benefit from different service tiers where the thresehold is much tighter on more expensive tiers.




Context


MapleFreight Logistics moves about 8,000 shipments a week across Ontario, Quebec and the Prairies. Its contracts carry on-time guarantees, so every late delivery costs service credits and re-delivery charges.

On-time performance has dropped from 94% to 88% this year, and we only learn a shipment is late when the customer calls.

AI Use:
Claude was used to help organize the columns and weigh decisions on which features to keep. 

Problem statement

You are a Data Scientist at MapleFreight. Delays are currently found only after the delivery window has passed, which means paid-out service credits, expedited re-deliveries and complaints that reach the account manager before dispatch knows anything is wrong.

Build an end-to-end system that predicts which in-transit shipments will arrive late and alerts dispatch in Slack while there is still time to re-route or warn the customer.

Objectives

Analyze shipment, route and carrier data to identify the main drivers of late delivery.

Build a predictive model that classifies whether a shipment will be delivered late.

Integrate Slack alerts so dispatch is notified about high-risk shipments automatically.

Provide actionable recommendations that improve on-time performance.

Dataset

maplefreight_delivery_delay_dataset.csv — 6,035 shipments, 23 columns. Target: delivered_late (1 = missed the promised window). About 21% are late, so predicting "on time" for everything scores 79% accuracy and is useless.

The export is raw. Expect missing values, duplicates, inconsistent spellings and unit errors.

Reference ML workflow

Context

Problem statement

Objective

Data understanding

Exploratory data analysis (EDA)

Data preprocessing

Feature engineering

Data preparation

Data modelling

Model evaluation

Model explainability

Slack alert integration

Recommendations

A typical end-to-end flow looks like the steps below. Treat it as a reference, not a checklist: adapt, merge, or reorder the steps to suit your approach, as long as the work is organized with Markdown headings and each part includes code, output, and a short written finding.

Slack integration

Your notebook must send a real Slack message when high-risk shipments are detected.

Setup

Create a free Slack workspace (or use one you already have) and a channel named #dispatch-alerts.

Create a Slack app, enable Incoming Webhooks, and add a webhook to that channel.

Store the webhook URL in a .env file and load it with python-dotenv or os.environ.

Post with requests.post(webhook_url, json={"text": message}).

The webhook URL is a credential: put it in .env, add .env to .gitignore, and commit a .env.example. A webhook visible in your repo or notebook output costs marks.

The message should be useful on a phone: how many shipments are at risk, then ID, destination, carrier and risk score for the top few. Commit a screenshot of it.

Modelling, alerts and AI use

Start with a baseline model and compare anything else against it.

21% of shipments are late: handle the imbalance and report precision, recall, F1 and a confusion matrix.

AI assistants are encouraged. In your README, note which you used and one case where the output was wrong.

Submission (IMPORTANT)

Submit the link to a public GitHub repository. Work individually.

Repository contents

MapleFreight_Delivery_Delay_Prediction_<your_id>.ipynb — the complete notebook, run top to bottom with outputs visible

README.md — problem, dataset, how to run, key findings, your chosen threshold and why, and the AI usage section

screenshots/slack_alert.png — the Slack notification as it appeared

requirements.txt

.gitignore (must include .env) and .env.example

The dataset, or a link to it

Submission checklist

Notebook runs from a clean kernel and is organised with clear headings and findings

Baseline reported alongside the final model, with precision, recall, F1 and a confusion matrix

Alert threshold chosen and justified; Slack screenshot committed

No webhook URL or .env anywhere in the repository

Appendix: data dictionary

Column

Type

Description

shipment_id

text

Unique shipment reference

origin_city

category

Pickup city and province

destination_city

category

Delivery city and province

carrier

category

Carrier handling the shipment, including owner-operators

service_level

category

Express, Standard or Economy

goods_type

category

General freight, refrigerated, fragile, hazardous or bulk

customer_priority_tier

category

Gold, Silver or Bronze account

shipment_month

integer

Month of pickup, 1 to 12

distance_km

numeric

Route distance in kilometres

weight_kg

numeric

Shipment weight

num_stops

integer

Intermediate stops on the route

is_cross_border

binary

1 if the shipment crosses into the United States

scheduled_transit_hours

numeric

Transit time promised to the customer

pickup_delay_minutes

numeric

Minutes late at pickup; negative means early

driver_experience_years

numeric

Years the assigned driver has been driving

vehicle_age_years

numeric

Age of the assigned vehicle

weather_condition

category

Forecast at dispatch: clear, rain, fog, snow or storm

traffic_index

numeric

Route traffic congestion at dispatch, 0 to 100

route_congestion_score

numeric

Historical congestion for the route, 0 to 1

prior_late_deliveries_30d

integer

Late deliveries by this carrier in the past 30 days

fuel_cost_cad

numeric

Estimated fuel cost for the trip

actual_transit_hours

numeric

Actual transit time, recorded on delivery — not available at prediction time

delivered_late

binary

Target. 1 = delivered after the promised window
