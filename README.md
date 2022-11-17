# SLO Error Budget

In the Log browser text box type:
 - replace orderbookdev with cXXXteamXXdev
   - where cXXX is cohort number and teamXX is team number. *Use r for reskill instead of c*

```
(sum(count_over_time({app="ingress-nginx"}[15m] |= "orderbookdev.computerlab.online" | json | __error__ !=
"JSONParserError" | request_uri =~ "/buy" , request_method="POST" , request_time > 0.03)) / 
sum(count_over_time({app="ingress-nginx"}[15m] |="orderbookdev.computerlab.online" | json | __error__ != "JSONParserError" | request_uri = "/buy" , request_method="POST")) ) * 100
```
 
 ### Lets Break this down
  - 9.1 **In another tab got to the explore page and select Loki Data Source and run each step**. 
  - 9.2 Set time to 1 hour. 
  - 9.3 run `{app="ingress-nginx"}`. 
    - This querries all web request logs. You will notice all web request logs to Kubernetes are here.
    - Click on any log. You will see labels, such as **request_uri, request_method, request_time. We will search on these labels in step 9.6**
  - 9.4 `count_over_time({app="ingress-nginx"}[15m])`. 
    - Count the number of each log stream within a 15 minute time frame
    - **Notice the [15m]**, this converts the vector [log1, log2, log3,...] into a range vector [[log1], [log2, log3, log4], [log5,..]] grouped by 15 minutes. Each log stream (different type of log) will get its own vector. **That is why we have more than one line plotted.**
  - 9.5 `count_over_time({app="ingress-nginx"} |= "orderbookdev.computerlab.online" [15m])`. 
    - Use line search to search for the logs containing the string of the URI (you can find URI in the ingress.yaml under host)
    - |= is piping each log to do a search and only retain logs that have that string.
    - Other options: |~ is regex, != is line does not contain, !~ line does not contain pattern.
  - 9.6 `count_over_time({app="ingress-nginx"}[15m] |= "orderbookdev.computerlab.online" | json | __error__ !=
"JSONParserError" | request_uri =~ "/buy" , request_method="POST" , request_time > 0.03)`. 
    - Notice the pipe to **json**. This allows you to search on the recognized labels (from step 9.3)
    - We remove any json parser errors to remove errors.
    - Then we search for any uri matching the patter "/buy", "POST" request methods, and request_times greater than 0.03 seconds
  - 9.7 `sum(count_over_time({app="ingress-nginx"}[15m] |= "orderbookdev.computerlab.online" | json | __error__ !=
"JSONParserError" | request_uri =~ "/buy" , request_method="POST" , request_time <= 0.03))`. 
    - When we aggregate it with sum, we count each log stream for each given time period into one result.
  - 9.8 Run the final solution from above. 
    - Suppose we have 10 total request, with 1 greater than 0.03 seconds
    - The 1 will be the numerator, the 10 is the denominator. This will give us 0.10
    - We multiply it by 100 to get 10 (10%), **which would be the percentage of request slower than to 0.03**
  - 9.9 Close this tab and go back to dashboard to finish setting up the panel!  
 
