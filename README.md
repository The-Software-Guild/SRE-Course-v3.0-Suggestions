# SLO Error Budget

- Now that we have one SLO, we could create an Error Budget. However, you should consider making further SLOs and assigning them to the single graph. We will demonstrate this in another practice.

### Scenario
- Error budgets are the top and first most important level to an SRE dashboard. Let's create an Error budget for Latency, based on our SLO created in the previous activity.

## Step-by-step
1. In your Grafana dashboard, click the add new panel icon. 


![err](https://the-software-guild.s3.amazonaws.com/sre/2207/images/GrafanaAddNewPanelIcon.png). 

2. Click the Add a new row button


3. Hover over the Row title and click the gear icon


4. Change Row title to Error Budget and click Update


5. Click the add new panel icon

![err](https://the-software-guild.s3.amazonaws.com/sre/2207/images/GrafanaAddNewPanelIcon.png).

6. Click the Add a new panel button

7. Change Time series to Stat

8. Set Data source to Loki

9. In the Log browser text box type:

```
(5-((sum(count_over_time({app="ingress-nginx"}[15m] |= "orderbookdev.computerlab.online" | json | __error__ !=
"JSONParserError" | request_uri =~ "/buy" , request_method="POST" , request_time > 0.03)) / 
sum(count_over_time({app="ingress-nginx"}[15m] |="orderbookdev.computerlab.online" | json | __error__ != "JSONParserError" | request_uri = "/buy" , request_method="POST")) ) * 100))*20
```
 

10. Set the Panel Title to ERROR BUDGET. 
11. Scroll down to Standard options and change:  
  - Unit to Misc -> Percent (0-100). 
  - Min to 0. 
  - Max to 100. 
  - Decimals to 3. 
12. Scroll down to Thresholds and change:  
  - Base = RED. 
  - 80 = RED change to 20 = GREEN. 
13. Under Value mappings, click Add value mappings. 
14. Delete the current condition by clicking the trashcan. 
15. Click the + Add a new mapping button. 
16. Select Special. 
17. Click on the Choose pull-down and select Null. 
18. In the Optional display text, type 100%. 
19. Click Set color, and select Green. 
20. Click the Update button. 
21. Click Apply. 
22. Move the panel into the Error Budget row. 
23. Save the dashboard. 
24. Copy the JSON Model from the dashboard settings to your local copy of the file.  
