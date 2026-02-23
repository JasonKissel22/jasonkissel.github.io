---
title: "NFL_historic_odds"
date: 2026-02-23
draft: false
---

This project explores historic NFL odds and outcomes.  

I used a combination of web scraping, data cleaning, and analysis to identify trends and patterns across seasons.  

The goal is to document the full process from data acquisition to visualization, highlighting the decision-making at each step.  

Future additions will include interactive Tableau dashboards and visualizations to illustrate key findings.

![Chart of historic odds](/images/3lander.png)

## Tableau Dashboard Example

{{< tableau >}}
<div class='tableauPlaceholder' id='viz1771870847196' style='position: relative'><noscript><a href='#'><img alt=' ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ag&#47;AgendaMedellnODS&#47;Portada&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='AgendaMedellnODS&#47;Portada' /><param name='tabs' value='yes' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ag&#47;AgendaMedellnODS&#47;Portada&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>                <script type='text/javascript'>                    var divElement = document.getElementById('viz1771870847196');                    var vizElement = divElement.getElementsByTagName('object')[0];                    vizElement.style.width='1300px';vizElement.style.height='850px';                    var scriptElement = document.createElement('script');                    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    vizElement.parentNode.insertBefore(scriptElement, vizElement);                </script>
{{< /tableau >}}

##  
## Looker Studio Example
##   

{{< tableau >}}
<iframe width="1300px" height="400" src="https://lookerstudio.google.com/embed/reporting/6292eaeb-9869-46e9-bc6c-94cdb6359560/page/1M" frameborder="0" style="border:0" allowfullscreen></iframe>
{{< /tableau >}}

## Code Example

```python
# Placeholder for data cleaning
import pandas as pd
df = pd.DataFrame({'team': ['A', 'B'], 'odds': [1.5, 2.0]})
print(df.head())
```

TEST HERE


