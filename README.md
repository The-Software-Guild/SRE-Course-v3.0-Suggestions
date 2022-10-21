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
 - replace orderbookdev with cXXXteamXXdev, where cXXX is cohort number and teamXX is team number. User r for reskill instead of c

```
100-(sum(count_over_time({app="ingress-nginx"}[15m] |= "orderbookdev.computerlab.online" | json | __error__ !=
"JSONParserError" | request_uri =~ "/buy" , request_method="POST" , request_time <= 0.03)) / 
sum(count_over_time({app="ingress-nginx"}[15m] |="orderbookdev.computerlab.online" | json | __error__ != "JSONParserError" | request_uri = "/buy" , request_method="POST")) ) * 100
```
 
 ### Lets Break step 9 down
  - 9.1 **In another tab got to the explore page and select Loki Data Source and run each step**. 
  - 9.2 Set time to 1 hour. 
  - 9.3 run `{app="ingress-nginx"}`. 
    - Query all logs from our load balancer. You will notice all web request logs to Kubernetes are here
    - Click on any log. You will see labels, such as **request_uri, request_method, request_time. We will search on these labels in step 9.6**
  - 9.4 `count_over_time({app="ingress-nginx"}[15m])`. 
    - Count the number of each log stream within a 15 minute time frame
    - **Notice the [15m]**, this converts the vector [log1, log2, log3,...] into a range vector [[log1], [log2, log3, log4], [log5,..]] grouped by 15 minutes. Each log stream (different type of log) will get its own vector. That is why we have more than one line plotted.
  - 9.5 `count_over_time({app="ingress-nginx"} |= "orderbookdev.computerlab.online" [15m])`. 
    - Use line search to search for the logs containing the string of the URI (you can find URI in the ingress.yaml under host)
    - |= is piping each log to do a search and only retain logs that have that string.
    - Other options: |~ is regex, != is line does not contain, !~ line does not contain pattern.
  - 9.6 `count_over_time({app="ingress-nginx"}[15m] |= "orderbookdev.computerlab.online" | json | __error__ !=
"JSONParserError" | request_uri =~ "/buy" , request_method="POST" , request_time <= 0.03)`. 
    - Notice the pipe to **json**. This allows you to search on the recognized labels (from step 9.3)
    - We remove any json parser errors to remove errors.
    - Then we search for any uri matching the patter "/buy", "POST" request methods, and request_times faster or equal to 0.03 seconds
  - 9.7 `sum(count_over_time({app="ingress-nginx"}[15m] |= "orderbookdev.computerlab.online" | json | __error__ !=
"JSONParserError" | request_uri =~ "/buy" , request_method="POST" , request_time <= 0.03))`. 
    - When we aggregate it with sum, we count each log stream for each given time period into one result.
  - 9.8 Run the final solution from above. 
    - Suppose we have 10 total request, with 9 less than or equal to 0.03 seconds
    - The 9 will be the numerator, the 10 is the denominator. This will give us 0.90
    - We multiply it by 100 to get 90 (90%), **which would be the percentage of request faster than or equal to 0.03**
  9.9 Close this tab and go back to dashboard to finish setting up the panel!  
  

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
