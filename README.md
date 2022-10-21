# SLO Error Budget

- Now that we have one SLO, we could create an Error Budget. However, you should consider making further SLOs and assigning them to the single graph. We will demonstrate this in another practice.

### Scenario
- Error budgets are the top and first most important level to an SRE dashboard. Let's create an Error budget for Latency, based on our SLO created in the previous activity.

## Step-by-step
1. In your Grafana dashboard, click the add new panel icon
(https://the-software-guild.s3.amazonaws.com/sre/2207/images/GrafanaAddNewPanelIcon.png)[]
2. Click the Add a new row button
3. Hover over the Row title and click the gear icon
4. Change Row title to Error Budget and click Update
5. Click the add new panel icon
6.Click the Add a new panel button
Change Time series to Stat
Set Data source to Loki
In the Log browser text box type:

(5-((sum(count_over_time({app="ingress-nginx"}[15m] |= "orderbookdev.computerlab.online" | json | __error__ != "JSONParserError" | request_uri =~ "/buy" , request_method="POST" , request_time > 0.03)) / sum(count_over_time({app="ingress-nginx"}[15m] |="orderbookdev.computerlab.online" | json | __error__ != "JSONParserError" | request_uri = "/buy" , request_method="POST")) ) * 100))*20
  
NOTE: We have added the following changes to our SLO so that we capture only the 5%:
5- has been added to the beginning of the query because we are only interested in the 5% that is in error
The request_time < 0.3 now becomes request_time > 0.3 because we are interested in what is above the 300ms
We multiply the entire sum by 20 because we want to represent the budget as 100% and reducing to 0%. This is because we are only interested in 5 out of the 100 and therefore 5 x 20 = 100. 

Set the Panel Title to ERROR BUDGET
Scroll down to Standard options and change:
Unit to Misc -> Percent (0-100)
Min to 0
Max to 100
Decimals to 3
Scroll down to Thresholds and change:
Base = RED
80 = RED change to 20 = GREEN
Under Value mappings, click Add value mappings
Delete the current condition by clicking the trashcan
Click the + Add a new mapping button
Select Special
Click on the Choose pull-down and select Null
In the Optional display text, type 100%
Click Set color, and select Green
Click the Update button
Click Apply
Move the panel into the Error Budget row
Save the dashboard
Copy the JSON Model from the dashboard settings to your local copy of the file.
