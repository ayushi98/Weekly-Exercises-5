---
title: 'Weekly Exercises #5'
author: "Ayushi Modi"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---





```r
library(tidyverse)     # for data cleaning and plotting
library(googlesheets4) # for reading googlesheet data
library(lubridate)     # for date manipulation
library(openintro)     # for the abbr2state() function
library(palmerpenguins)# for Palmer penguin data
library(maps)          # for map data
library(ggmap)         # for mapping points on maps
library(gplots)        # for col2hex() function
library(RColorBrewer)  # for color palettes
library(sf)            # for working with spatial data
library(leaflet)       # for highly customizable mapping
library(ggthemes)      # for more themes (including theme_map())
library(plotly)        # for the ggplotly() - basic interactivity
library(gganimate)     # for adding animation layers to ggplots
library(transformr)    # for "tweening" (gganimate)
library(shiny)         # for creating interactive apps
library(gifski)
library(ggimage)
gs4_deauth()           # To not have to authorize each time you knit.
theme_set(theme_minimal())
```


```r
# SNCF Train data
small_trains <- read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-02-26/small_trains.csv") 

# Lisa's garden data
garden_harvest <- read_sheet("https://docs.google.com/spreadsheets/d/1DekSazCzKqPS2jnGhKue7tLxRU3GVL1oxi-4bEM5IWw/edit?usp=sharing") %>% 
  mutate(date = ymd(date))

# Lisa's Mallorca cycling data
mallorca_bike_day7 <- read_csv("https://www.dropbox.com/s/zc6jan4ltmjtvy0/mallorca_bike_day7.csv?dl=1") %>% 
  select(1:4, speed)

# Heather Lendway's Ironman 70.3 Pan Am championships Panama data
panama_swim <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_swim_20160131.csv")

panama_bike <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_bike_20160131.csv")

panama_run <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_run_20160131.csv")

#COVID-19 data from the New York Times
covid19 <- read_csv("https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-states.csv")

bike_link <- "https://raw.githubusercontent.com/llendway/animation_and_interactivity/master/bike.png"
mallorca_bike_day7<-
  mallorca_bike_day7 %>% 
  mutate(bike = bike_link)

census_pop_est_2018 <- read_csv("https://www.dropbox.com/s/6txwv3b4ng7pepe/us_census_2018_state_pop_est.csv?dl=1") %>% #reading in the data
  separate(state, into = c("dot","state"), extra = "merge") %>% #separate the state variable into dot and state. Dot does not have any values.
  select(-dot) %>% #drop the dot variable
  mutate(state = str_to_lower(state))
```

## Put your homework on GitHub!

Go [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md) or to previous homework to remind yourself how to get set up. 

Once your repository is created, you should always open your **project** rather than just opening an .Rmd file. You can do that by either clicking on the .Rproj file in your repository folder on your computer. Or, by going to the upper right hand corner in R Studio and clicking the arrow next to where it says Project: (None). You should see your project come up in that list if you've used it recently. You could also go to File --> Open Project and navigate to your .Rproj file. 

## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* **NEW!!** With animated graphs, add `eval=FALSE` to the code chunk that creates the animation and saves it using `anim_save()`. Add another code chunk to reread the gif back into the file. See the [tutorial](https://animation-and-interactivity-in-r.netlify.app/) for help. 

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.

## Warm-up exercises from tutorial

  1. Choose 2 graphs you have created for ANY assignment in this class and add interactivity using the `ggplotly()` function.


```r
garden_harvest_lettuce<-
  garden_harvest %>% 
  filter(vegetable == "lettuce") %>% 
  ggplot(aes(y = fct_rev(fct_infreq(variety)), text = variety))+
  labs(y ="Variety of Lettuce", title = "Number of times lettuce was harvested")+
  geom_bar()

ggplotly(garden_harvest_lettuce,
         tooltip = c("text", "x"))
```

<!--html_preserve--><div id="htmlwidget-5ef2d30edf4935f29e20" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-5ef2d30edf4935f29e20">{"x":{"data":[{"orientation":"v","width":[1,3,9,27,28],"base":[0.55,1.55,2.55,3.55,4.55],"x":[0.5,1.5,4.5,13.5,14],"y":[0.9,0.9,0.9,0.9,0.9],"text":["count:  1<br />mustard greens","count:  3<br />reseed","count:  9<br />Tatsoi","count: 27<br />Farmer's Market Blend","count: 28<br />Lettuce Mixture"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(89,89,89,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":40.1826484018265,"l":148.310502283105},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"Number of times lettuce was harvested","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-1.4,29.4],"tickmode":"array","ticktext":["0","10","20"],"tickvals":[0,10,20],"categoryorder":"array","categoryarray":["0","10","20"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"count","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.4,5.6],"tickmode":"array","ticktext":["mustard greens","reseed","Tatsoi","Farmer's Market Blend","Lettuce Mixture"],"tickvals":[1,2,3,4,5],"categoryorder":"array","categoryarray":["mustard greens","reseed","Tatsoi","Farmer's Market Blend","Lettuce Mixture"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"Variety of Lettuce","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":false,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"a96c5b080aa0":{"y":{},"text":{},"type":"bar"}},"cur_data":"a96c5b080aa0","visdat":{"a96c5b080aa0":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


```r
garden_harvest_tomatoes<-
  garden_harvest %>% 
  filter(vegetable == "tomatoes") %>% 
  mutate(variety2 = fct_reorder(variety,date, min)) %>% 
  group_by(variety2) %>% 
  summarize(wt_lbs_tomatoes = (weight*0.00220462)) %>% 
  ggplot(aes(y = variety2, x = wt_lbs_tomatoes, text = variety2))+
  geom_col()+
  labs(y = "Tomatoe varieties",
       x = "Weight in lbs",
       title = "Tomatoes reorded by Harvest Date")

ggplotly(garden_harvest_tomatoes,
         tooltip = c("text", "x"))
```

<!--html_preserve--><div id="htmlwidget-d99cd753905d0c6c3a78" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-d99cd753905d0c6c3a78">{"x":{"data":[{"orientation":"v","width":[0.05291088,0.18959732,0.06834322,0.23368972,0.28880522,0.0881848,0.20062042,0.220462,0.37037616,0.22487124,0.26014516,0.47840254,0.64374904,0.17857422,0.14109568,0.66579524,0.928145020000001,0.277782119999999,1.05160374,0.96121432,0.29982832,0.994283619999999,1.08687766,0.584224300000001,0.96121432,0.1653465,1.11553772,1.80558378,0.837755599999999,1.83644846,2.49342522,0.97444204,1.3558413,1.12215158,0.568791959999999,2.33248796,1.80558378,0.30203294,0.44753786,0.49163026,0.67681834,0.35714844,0.91050806,0.58642892,0.87523414,0.58201968,0.75838928,1.85629004,2.18918766,1.09569614,2.27737246,2.7888443,3.63541838,0.670204479999999,1.45284458,0.74736618,0.3086468,0.32628376,0.33730686,0.9590097,0.34392072,0.85318794,1.24120106,0.79145858,1.24340568,0.39462698,0.67681834,0.73193384,1.56748482,1.19710866,0.80248168,0.59745202,1.39331984,0.727524600000001,0.593042779999999,1.38670598,0.341716100000003,1.5652802,2.31926024,1.57409868,1.05160374,0.4850164,0.68784144,0.97444204,0.6393398,0.46517482,0.67902296,2.3038279,2.42949124,0.78043548,1.60275874,0.52469956,1.68432968,1.3558413,0.507062600000001,0.68122758,0.866415659999999,6.39119338,3.06883104,1.4770954,2.73152418,0.54454114,0.5291088,0.67681834,1.36465978,0.66799986,0.67902296,0.47619792,1.76810524,1.92242864,3.52959662,1.30733966,1.32056738,0.44312862,1.02073906,0.46076558,0.43431014,0.21384814,1.12215158,0.84216484,0.3858085,1.11553772,1.41536604,1.1574255,1.2345872,1.29631656,2.19800614,2.2266662,0.943577360000001,3.39952404,3.086468,4.15791332,6.46835508,3.38850094,4.7619792,2.90348454,1.52559703999999,0.502653360000004,6.03624956,1.76590062,0.74736618,1.72180822,0.80248168,1.34040896,1.55205248,2.26194012,2.41846814,1.29852118,2.64995324,0.562178099999997,0.696659920000002,4.42246772,0.6944553,1.23899644,0.40565008,1.30293042,0.53131342,0.39903622,0.7936632,1.27427036,1.65787424,1.70417126,2.59704236,1.66228348,1.34702282,1.39552446,0.74075232,0.51367646,0.52469956,1.32056738,0.2314851,0.535722659999999,1.76590062,0.253531300000001,1.46827692,1.89817782,1.7747191,1.48591388,3.59573522,4.01902226,0.96121432,1.88935934,0.86641566,0.79145858,0.64154442,0.69886454,1.27647498,3.39070556,3.46786726,0.476197920000001,0.520290319999999,0.7054784,0.50926722,0.6393398,0.78484472,0.48060716,0.6724091,0.7385477,1.06483146,0.98326052,1.92022402,2.29721404,1.0141252,0.392422359999999,0.9479866,1.57409868,0.16093726,0.14770954,0.11904948,0.3527392,1.0802638,0.72311536,0.67461372,0.32628376,0.67461372,0.73413846,1.23899644,1.07585456,0.575405819999999,1.62480494,1.24781492,1.81219764,4.30562286,1.3448182,2.72050108,5.24038174,0.910508060000002,1.5983495,0.467379439999998,6.46835508,0.209438900000002,4.36073836],"base":[0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,0.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,1.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,2.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,3.55,4.55,4.55,4.55,4.55,4.55,4.55,4.55,4.55,4.55,4.55,4.55,4.55,4.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,5.55,6.55,6.55,6.55,6.55,6.55,6.55,6.55,6.55,6.55,6.55,6.55,6.55,6.55,7.55,7.55,7.55,7.55,7.55,7.55,7.55,7.55,7.55,7.55,7.55,7.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,8.55,9.55,9.55,9.55,9.55,9.55,9.55,9.55,9.55,9.55,9.55,9.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,10.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55,11.55],"x":[0.02645544,0.14770954,0.27667981,0.42769628,0.68894375,0.87743876,1.02184137,1.23238258,1.52780166,1.82542536,2.06793356,2.43720741,2.9982832,3.40944483,3.56927978,3.97272524,4.76969537,5.37265894,6.03735187,7.0437609,7.67428222,8.32133819,9.36191883,10.19746981,10.97018912,11.53346953,12.17391164,13.63447239,14.95614208,16.29324411,18.45818095,20.19211458,21.35725625,22.59625269,23.44172446,24.89236442,26.96140029,0.15101647,0.52580187,0.99538593,1.57961023,2.09659362,2.73042187,3.47889036,4.20972189,4.9383488,5.60855328,6.91589294,8.93863179,10.58107369,12.26760799,14.80071637,18.01284771,20.16565914,21.22718367,0.37368309,0.90168958,1.21915486,1.55095017,2.19910845,2.85057366,3.44912799,4.49632249,5.51265231,6.53008444,7.34910077,7.88482343,8.58919952,9.73890885,11.12120559,12.12100076,12.82096761,13.81635354,14.87677576,15.53705945,16.52693383,17.39114487,18.34464302,20.28691324,22.2335927,23.54644391,0.2425082,0.82893712,1.66007886,2.46696978,3.01922709,3.59132598,5.08275141,7.44941098,9.05437434,10.24597145,11.3097006,12.41421522,13.93430071,14.86575266,15.45989775,16.23371937,19.86252389,24.5925361,26.86549932,28.96980911,0.27227057,0.80909554,1.41205911,2.43279817,3.44912799,4.1226394,4.70024984,5.82240142,7.66766836,10.39368099,12.81214913,14.12610265,15.00795065,0.51036953,1.25112185,1.69865971,2.02273885,2.69073871,3.67289692,4.28688359,5.0375567,6.30300858,7.58940435,8.7854107,10.05086258,11.79802393,14.0103601,15.59548188,17.76703258,21.0100286,24.63221926,29.94535346,34.87378147,38.94902154,42.78175341,44.9962942,46.0104194,49.27987086,0.88295031,2.13958371,3.37417091,4.63631586,5.70776118,7.1539919,9.0609882,11.40119233,13.25968699,15.2339242,16.83998987,17.46940888,20.0289727,0.34722765,1.31395352,2.13627678,2.99056703,3.90768895,4.37286377,4.96921348,6.00318026,7.46925256,9.15027531,11.30088212,13.43054504,0.67351141,2.04478505,3.11292344,3.74013783,4.25932584,5.18195931,5.95798555,6.34158943,7.49240107,8.50211703,9.36302114,11.04624851,12.88269697,14.51301346,17.05383801,20.86121675,0.48060716,1.90589399,3.28378149,4.11271861,4.82922011,5.49942459,6.48709435,8.82068462,12.24997103,14.22200362,14.72024774,0.3527392,0.96011201,1.53441552,2.24650778,2.87923372,3.45574185,4.16122025,5.06290983,6.08695582,7.53869809,9.64741712,11.30308674,12.00636052,12.676565,13.93760764,0.08046863,0.23479203,0.36817154,0.60406588,1.32056738,2.22225696,2.9211215,3.42157024,3.92201898,4.62639507,5.61296252,6.77038802,7.59601821,8.69612359,10.13243352,11.6624398,14.72135005,17.54657058,19.57923022,23.55967163,26.63511653,27.88954531,28.92240978,32.39027704,35.72917403,38.01426266],"y":[0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.9,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999,0.899999999999999],"text":["wt_lbs_tomatoes: 0.05291088<br />grape","wt_lbs_tomatoes: 0.18959732<br />grape","wt_lbs_tomatoes: 0.06834322<br />grape","wt_lbs_tomatoes: 0.23368972<br />grape","wt_lbs_tomatoes: 0.28880522<br />grape","wt_lbs_tomatoes: 0.08818480<br />grape","wt_lbs_tomatoes: 0.20062042<br />grape","wt_lbs_tomatoes: 0.22046200<br />grape","wt_lbs_tomatoes: 0.37037616<br />grape","wt_lbs_tomatoes: 0.22487124<br />grape","wt_lbs_tomatoes: 0.26014516<br />grape","wt_lbs_tomatoes: 0.47840254<br />grape","wt_lbs_tomatoes: 0.64374904<br />grape","wt_lbs_tomatoes: 0.17857422<br />grape","wt_lbs_tomatoes: 0.14109568<br />grape","wt_lbs_tomatoes: 0.66579524<br />grape","wt_lbs_tomatoes: 0.92814502<br />grape","wt_lbs_tomatoes: 0.27778212<br />grape","wt_lbs_tomatoes: 1.05160374<br />grape","wt_lbs_tomatoes: 0.96121432<br />grape","wt_lbs_tomatoes: 0.29982832<br />grape","wt_lbs_tomatoes: 0.99428362<br />grape","wt_lbs_tomatoes: 1.08687766<br />grape","wt_lbs_tomatoes: 0.58422430<br />grape","wt_lbs_tomatoes: 0.96121432<br />grape","wt_lbs_tomatoes: 0.16534650<br />grape","wt_lbs_tomatoes: 1.11553772<br />grape","wt_lbs_tomatoes: 1.80558378<br />grape","wt_lbs_tomatoes: 0.83775560<br />grape","wt_lbs_tomatoes: 1.83644846<br />grape","wt_lbs_tomatoes: 2.49342522<br />grape","wt_lbs_tomatoes: 0.97444204<br />grape","wt_lbs_tomatoes: 1.35584130<br />grape","wt_lbs_tomatoes: 1.12215158<br />grape","wt_lbs_tomatoes: 0.56879196<br />grape","wt_lbs_tomatoes: 2.33248796<br />grape","wt_lbs_tomatoes: 1.80558378<br />grape","wt_lbs_tomatoes: 0.30203294<br />Big Beef","wt_lbs_tomatoes: 0.44753786<br />Big Beef","wt_lbs_tomatoes: 0.49163026<br />Big Beef","wt_lbs_tomatoes: 0.67681834<br />Big Beef","wt_lbs_tomatoes: 0.35714844<br />Big Beef","wt_lbs_tomatoes: 0.91050806<br />Big Beef","wt_lbs_tomatoes: 0.58642892<br />Big Beef","wt_lbs_tomatoes: 0.87523414<br />Big Beef","wt_lbs_tomatoes: 0.58201968<br />Big Beef","wt_lbs_tomatoes: 0.75838928<br />Big Beef","wt_lbs_tomatoes: 1.85629004<br />Big Beef","wt_lbs_tomatoes: 2.18918766<br />Big Beef","wt_lbs_tomatoes: 1.09569614<br />Big Beef","wt_lbs_tomatoes: 2.27737246<br />Big Beef","wt_lbs_tomatoes: 2.78884430<br />Big Beef","wt_lbs_tomatoes: 3.63541838<br />Big Beef","wt_lbs_tomatoes: 0.67020448<br />Big Beef","wt_lbs_tomatoes: 1.45284458<br />Big Beef","wt_lbs_tomatoes: 0.74736618<br />Bonny Best","wt_lbs_tomatoes: 0.30864680<br />Bonny Best","wt_lbs_tomatoes: 0.32628376<br />Bonny Best","wt_lbs_tomatoes: 0.33730686<br />Bonny Best","wt_lbs_tomatoes: 0.95900970<br />Bonny Best","wt_lbs_tomatoes: 0.34392072<br />Bonny Best","wt_lbs_tomatoes: 0.85318794<br />Bonny Best","wt_lbs_tomatoes: 1.24120106<br />Bonny Best","wt_lbs_tomatoes: 0.79145858<br />Bonny Best","wt_lbs_tomatoes: 1.24340568<br />Bonny Best","wt_lbs_tomatoes: 0.39462698<br />Bonny Best","wt_lbs_tomatoes: 0.67681834<br />Bonny Best","wt_lbs_tomatoes: 0.73193384<br />Bonny Best","wt_lbs_tomatoes: 1.56748482<br />Bonny Best","wt_lbs_tomatoes: 1.19710866<br />Bonny Best","wt_lbs_tomatoes: 0.80248168<br />Bonny Best","wt_lbs_tomatoes: 0.59745202<br />Bonny Best","wt_lbs_tomatoes: 1.39331984<br />Bonny Best","wt_lbs_tomatoes: 0.72752460<br />Bonny Best","wt_lbs_tomatoes: 0.59304278<br />Bonny Best","wt_lbs_tomatoes: 1.38670598<br />Bonny Best","wt_lbs_tomatoes: 0.34171610<br />Bonny Best","wt_lbs_tomatoes: 1.56528020<br />Bonny Best","wt_lbs_tomatoes: 2.31926024<br />Bonny Best","wt_lbs_tomatoes: 1.57409868<br />Bonny Best","wt_lbs_tomatoes: 1.05160374<br />Bonny Best","wt_lbs_tomatoes: 0.48501640<br />Better Boy","wt_lbs_tomatoes: 0.68784144<br />Better Boy","wt_lbs_tomatoes: 0.97444204<br />Better Boy","wt_lbs_tomatoes: 0.63933980<br />Better Boy","wt_lbs_tomatoes: 0.46517482<br />Better Boy","wt_lbs_tomatoes: 0.67902296<br />Better Boy","wt_lbs_tomatoes: 2.30382790<br />Better Boy","wt_lbs_tomatoes: 2.42949124<br />Better Boy","wt_lbs_tomatoes: 0.78043548<br />Better Boy","wt_lbs_tomatoes: 1.60275874<br />Better Boy","wt_lbs_tomatoes: 0.52469956<br />Better Boy","wt_lbs_tomatoes: 1.68432968<br />Better Boy","wt_lbs_tomatoes: 1.35584130<br />Better Boy","wt_lbs_tomatoes: 0.50706260<br />Better Boy","wt_lbs_tomatoes: 0.68122758<br />Better Boy","wt_lbs_tomatoes: 0.86641566<br />Better Boy","wt_lbs_tomatoes: 6.39119338<br />Better Boy","wt_lbs_tomatoes: 3.06883104<br />Better Boy","wt_lbs_tomatoes: 1.47709540<br />Better Boy","wt_lbs_tomatoes: 2.73152418<br />Better Boy","wt_lbs_tomatoes: 0.54454114<br />Cherokee Purple","wt_lbs_tomatoes: 0.52910880<br />Cherokee Purple","wt_lbs_tomatoes: 0.67681834<br />Cherokee Purple","wt_lbs_tomatoes: 1.36465978<br />Cherokee Purple","wt_lbs_tomatoes: 0.66799986<br />Cherokee Purple","wt_lbs_tomatoes: 0.67902296<br />Cherokee Purple","wt_lbs_tomatoes: 0.47619792<br />Cherokee Purple","wt_lbs_tomatoes: 1.76810524<br />Cherokee Purple","wt_lbs_tomatoes: 1.92242864<br />Cherokee Purple","wt_lbs_tomatoes: 3.52959662<br />Cherokee Purple","wt_lbs_tomatoes: 1.30733966<br />Cherokee Purple","wt_lbs_tomatoes: 1.32056738<br />Cherokee Purple","wt_lbs_tomatoes: 0.44312862<br />Cherokee Purple","wt_lbs_tomatoes: 1.02073906<br />Amish Paste","wt_lbs_tomatoes: 0.46076558<br />Amish Paste","wt_lbs_tomatoes: 0.43431014<br />Amish Paste","wt_lbs_tomatoes: 0.21384814<br />Amish Paste","wt_lbs_tomatoes: 1.12215158<br />Amish Paste","wt_lbs_tomatoes: 0.84216484<br />Amish Paste","wt_lbs_tomatoes: 0.38580850<br />Amish Paste","wt_lbs_tomatoes: 1.11553772<br />Amish Paste","wt_lbs_tomatoes: 1.41536604<br />Amish Paste","wt_lbs_tomatoes: 1.15742550<br />Amish Paste","wt_lbs_tomatoes: 1.23458720<br />Amish Paste","wt_lbs_tomatoes: 1.29631656<br />Amish Paste","wt_lbs_tomatoes: 2.19800614<br />Amish Paste","wt_lbs_tomatoes: 2.22666620<br />Amish Paste","wt_lbs_tomatoes: 0.94357736<br />Amish Paste","wt_lbs_tomatoes: 3.39952404<br />Amish Paste","wt_lbs_tomatoes: 3.08646800<br />Amish Paste","wt_lbs_tomatoes: 4.15791332<br />Amish Paste","wt_lbs_tomatoes: 6.46835508<br />Amish Paste","wt_lbs_tomatoes: 3.38850094<br />Amish Paste","wt_lbs_tomatoes: 4.76197920<br />Amish Paste","wt_lbs_tomatoes: 2.90348454<br />Amish Paste","wt_lbs_tomatoes: 1.52559704<br />Amish Paste","wt_lbs_tomatoes: 0.50265336<br />Amish Paste","wt_lbs_tomatoes: 6.03624956<br />Amish Paste","wt_lbs_tomatoes: 1.76590062<br />Mortgage Lifter","wt_lbs_tomatoes: 0.74736618<br />Mortgage Lifter","wt_lbs_tomatoes: 1.72180822<br />Mortgage Lifter","wt_lbs_tomatoes: 0.80248168<br />Mortgage Lifter","wt_lbs_tomatoes: 1.34040896<br />Mortgage Lifter","wt_lbs_tomatoes: 1.55205248<br />Mortgage Lifter","wt_lbs_tomatoes: 2.26194012<br />Mortgage Lifter","wt_lbs_tomatoes: 2.41846814<br />Mortgage Lifter","wt_lbs_tomatoes: 1.29852118<br />Mortgage Lifter","wt_lbs_tomatoes: 2.64995324<br />Mortgage Lifter","wt_lbs_tomatoes: 0.56217810<br />Mortgage Lifter","wt_lbs_tomatoes: 0.69665992<br />Mortgage Lifter","wt_lbs_tomatoes: 4.42246772<br />Mortgage Lifter","wt_lbs_tomatoes: 0.69445530<br />Jet Star","wt_lbs_tomatoes: 1.23899644<br />Jet Star","wt_lbs_tomatoes: 0.40565008<br />Jet Star","wt_lbs_tomatoes: 1.30293042<br />Jet Star","wt_lbs_tomatoes: 0.53131342<br />Jet Star","wt_lbs_tomatoes: 0.39903622<br />Jet Star","wt_lbs_tomatoes: 0.79366320<br />Jet Star","wt_lbs_tomatoes: 1.27427036<br />Jet Star","wt_lbs_tomatoes: 1.65787424<br />Jet Star","wt_lbs_tomatoes: 1.70417126<br />Jet Star","wt_lbs_tomatoes: 2.59704236<br />Jet Star","wt_lbs_tomatoes: 1.66228348<br />Jet Star","wt_lbs_tomatoes: 1.34702282<br />Old German","wt_lbs_tomatoes: 1.39552446<br />Old German","wt_lbs_tomatoes: 0.74075232<br />Old German","wt_lbs_tomatoes: 0.51367646<br />Old German","wt_lbs_tomatoes: 0.52469956<br />Old German","wt_lbs_tomatoes: 1.32056738<br />Old German","wt_lbs_tomatoes: 0.23148510<br />Old German","wt_lbs_tomatoes: 0.53572266<br />Old German","wt_lbs_tomatoes: 1.76590062<br />Old German","wt_lbs_tomatoes: 0.25353130<br />Old German","wt_lbs_tomatoes: 1.46827692<br />Old German","wt_lbs_tomatoes: 1.89817782<br />Old German","wt_lbs_tomatoes: 1.77471910<br />Old German","wt_lbs_tomatoes: 1.48591388<br />Old German","wt_lbs_tomatoes: 3.59573522<br />Old German","wt_lbs_tomatoes: 4.01902226<br />Old German","wt_lbs_tomatoes: 0.96121432<br />Black Krim","wt_lbs_tomatoes: 1.88935934<br />Black Krim","wt_lbs_tomatoes: 0.86641566<br />Black Krim","wt_lbs_tomatoes: 0.79145858<br />Black Krim","wt_lbs_tomatoes: 0.64154442<br />Black Krim","wt_lbs_tomatoes: 0.69886454<br />Black Krim","wt_lbs_tomatoes: 1.27647498<br />Black Krim","wt_lbs_tomatoes: 3.39070556<br />Black Krim","wt_lbs_tomatoes: 3.46786726<br />Black Krim","wt_lbs_tomatoes: 0.47619792<br />Black Krim","wt_lbs_tomatoes: 0.52029032<br />Black Krim","wt_lbs_tomatoes: 0.70547840<br />Brandywine","wt_lbs_tomatoes: 0.50926722<br />Brandywine","wt_lbs_tomatoes: 0.63933980<br />Brandywine","wt_lbs_tomatoes: 0.78484472<br />Brandywine","wt_lbs_tomatoes: 0.48060716<br />Brandywine","wt_lbs_tomatoes: 0.67240910<br />Brandywine","wt_lbs_tomatoes: 0.73854770<br />Brandywine","wt_lbs_tomatoes: 1.06483146<br />Brandywine","wt_lbs_tomatoes: 0.98326052<br />Brandywine","wt_lbs_tomatoes: 1.92022402<br />Brandywine","wt_lbs_tomatoes: 2.29721404<br />Brandywine","wt_lbs_tomatoes: 1.01412520<br />Brandywine","wt_lbs_tomatoes: 0.39242236<br />Brandywine","wt_lbs_tomatoes: 0.94798660<br />Brandywine","wt_lbs_tomatoes: 1.57409868<br />Brandywine","wt_lbs_tomatoes: 0.16093726<br />volunteers","wt_lbs_tomatoes: 0.14770954<br />volunteers","wt_lbs_tomatoes: 0.11904948<br />volunteers","wt_lbs_tomatoes: 0.35273920<br />volunteers","wt_lbs_tomatoes: 1.08026380<br />volunteers","wt_lbs_tomatoes: 0.72311536<br />volunteers","wt_lbs_tomatoes: 0.67461372<br />volunteers","wt_lbs_tomatoes: 0.32628376<br />volunteers","wt_lbs_tomatoes: 0.67461372<br />volunteers","wt_lbs_tomatoes: 0.73413846<br />volunteers","wt_lbs_tomatoes: 1.23899644<br />volunteers","wt_lbs_tomatoes: 1.07585456<br />volunteers","wt_lbs_tomatoes: 0.57540582<br />volunteers","wt_lbs_tomatoes: 1.62480494<br />volunteers","wt_lbs_tomatoes: 1.24781492<br />volunteers","wt_lbs_tomatoes: 1.81219764<br />volunteers","wt_lbs_tomatoes: 4.30562286<br />volunteers","wt_lbs_tomatoes: 1.34481820<br />volunteers","wt_lbs_tomatoes: 2.72050108<br />volunteers","wt_lbs_tomatoes: 5.24038174<br />volunteers","wt_lbs_tomatoes: 0.91050806<br />volunteers","wt_lbs_tomatoes: 1.59834950<br />volunteers","wt_lbs_tomatoes: 0.46737944<br />volunteers","wt_lbs_tomatoes: 6.46835508<br />volunteers","wt_lbs_tomatoes: 0.20943890<br />volunteers","wt_lbs_tomatoes: 4.36073836<br />volunteers"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(89,89,89,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.7625570776256,"r":7.30593607305936,"b":40.1826484018265,"l":113.24200913242},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"Tomatoes reorded by Harvest Date","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-2.614899782,54.912895422],"tickmode":"array","ticktext":["0","10","20","30","40","50"],"tickvals":[0,10,20,30,40,50],"categoryorder":"array","categoryarray":["0","10","20","30","40","50"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"Weight in lbs","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.4,12.6],"tickmode":"array","ticktext":["grape","Big Beef","Bonny Best","Better Boy","Cherokee Purple","Amish Paste","Mortgage Lifter","Jet Star","Old German","Black Krim","Brandywine","volunteers"],"tickvals":[1,2,3,4,5,6,7,8,9,10,11,12],"categoryorder":"array","categoryarray":["grape","Big Beef","Bonny Best","Better Boy","Cherokee Purple","Amish Paste","Mortgage Lifter","Jet Star","Old German","Black Krim","Brandywine","volunteers"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.65296803652968,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"Tomatoe varieties","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":false,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"a96c3cb1a516":{"x":{},"y":{},"text":{},"type":"bar"}},"cur_data":"a96c3cb1a516","visdat":{"a96c3cb1a516":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
  
  2. Use animation to tell an interesting story with the `small_trains` dataset that contains data from the SNCF (National Society of French Railways). These are Tidy Tuesday data! Read more about it [here](https://github.com/rfordatascience/tidytuesday/tree/master/data/2019/2019-02-26).


```r
small_trains_animate <-
  small_trains %>% 
  filter(avg_delay_all_departing >1,
         year!="2018") %>%
  ggplot(aes(x = delayed_number)) +
  geom_histogram(aes(fill = service)) +
  labs(title = "Percentage of delayed departures by service line", 
       subtitle = "Date: {closest_state}",
       x = "Percentage of delayed departues",
       y = "",
       color = "service") +
  transition_states(year)

anim_save("small_trains_animate.gif", small_trains_animate)
```

![](small_trains_animate.gif)<!-- -->

From this graph we see that in 2015 there were fewer departures than in 2016 or 2017. We also see that the percentage of National line delays are much lesser in 2017 than 2015. 

## Garden data

  3. In this exercise, you will create a stacked area plot that reveals itself over time (see the `geom_area()` examples [here](https://ggplot2.tidyverse.org/reference/position_stack.html)). You will look at cumulative harvest of tomato varieties over time. You should do the following:
  * From the `garden_harvest` data, filter the data to the tomatoes and find the *daily* harvest in pounds for each variety.  
  * Then, for each variety, find the cumulative harvest in pounds.  
  * Use the data you just made to create a static cumulative harvest area plot, with the areas filled with different colors for each vegetable and arranged (HINT: `fct_reorder()`) from most to least harvested (most on the bottom).  
  * Add animation to reveal the plot over date. 
  

```r
garden_harvest_animate <-
  garden_harvest %>% 
  filter(vegetable == "tomatoes") %>%
  complete(variety, date = seq.Date(min(date),max(date), by = "day")) %>% 
  select(-c(vegetable, units)) %>% 
  mutate(weight = replace_na(weight, 0)) %>% 
  group_by(variety, date) %>% 
  summarize(daily_weight_g = sum(weight)) %>% 
  mutate(daily_weight_lb = daily_weight_g*0.00220462,
         cum_weight_lb = cumsum(daily_weight_lb)) %>%
  select(-daily_weight_lb) %>% 
  ggplot(aes(x = date, y = cum_weight_lb), position = position_stack())+
  geom_area(aes(fill = variety)) +
  labs(title = "Cumulative harvest of tomatoes over time",
       subtitle = "Date:{frame_along}",
       x = "Date",
       y = "Wight in lbs")+
  scale_fill_viridis_d()+
  transition_reveal(date)

anim_save("garden_harvest_animate.gif", garden_harvest_animate)
```


![](garden_harvest_animate.gif)<!-- -->

## Maps, animation, and movement!

  4. Map my `mallorca_bike_day7` bike ride using animation! 
  Requirements:
  * Plot on a map using `ggmap`.  
  * Show "current" location with a red point. 
  * Show path up until the current point.  
  * Color the path according to elevation.  
  * Show the time in the subtitle.  
  * CHALLENGE: use the `ggimage` package and `geom_image` to add a bike image instead of a red point. You can use [this](https://raw.githubusercontent.com/llendway/animation_and_interactivity/master/bike.png) image. See [here](https://goodekat.github.io/presentations/2019-isugg-gganimate-spooky/slides.html#35) for an example. 
  * Add something of your own! And comment on if you prefer this to the static map and why or why not.
  

```r
mallorca_map <- get_stamenmap(bbox = c(left = 2.01,
                                        bottom = 39.26, 
                                        right = 3.43,
                                        top = 40.01),
                               maptype = "terrain",
                               zoom = 11)

bike_animate<- 
  ggmap(mallorca_map) +
  geom_image(data = mallorca_bike_day7,
             aes(x = lon, y = lat, image = bike_link),
             size = .06)+
  geom_path(data = mallorca_bike_day7,
            aes(x = lon, y = lat, color = ele),
            size = .5)+
  labs(title = "Mallorca Bike Ride", 
       subtitle = "Date: {frame_along}",
       x = "",
       y = "")+
  scale_color_viridis_c(option = "magma")+
  theme_map()+
  theme(legend.background = element_blank())+
  transition_reveal(time)
  
anim_save("bike_animate.gif", bike_animate)
```
  
![](bike_animate.gif)<!-- -->
  
I do prefer this to a static map, because it gives you exactly the route that was taken. While the static map provides a clear view of the elevation and route, the animation actually shows us how the trail was done and is more interactive.   
  
  5. In this exercise, you get to meet my sister, Heather! She is a proud Mac grad, currently works as a Data Scientist at 3M where she uses R everyday, and for a few years (while still holding a full-time job) she was a pro triathlete. You are going to map one of her races. The data from each discipline of the Ironman 70.3 Pan Am championships, Panama is in a separate file - `panama_swim`, `panama_bike`, and `panama_run`. Create a similar map to the one you created with my cycling data. You will need to make some small changes: 1. combine the files (HINT: `bind_rows()`, 2. make the leading dot a different color depending on the event (for an extra challenge, make it a different image using `geom_image()!), 3. CHALLENGE (optional): color by speed, which you will need to compute on your own from the data. You can read Heather's race report [here](https://heatherlendway.com/2016/02/10/ironman-70-3-pan-american-championships-panama-race-report/). She is also in the Macalester Athletics [Hall of Fame](https://athletics.macalester.edu/honors/hall-of-fame/heather-lendway/184) and still has records at the pool. 
  

```r
panama_all<-
  panama_swim %>% 
  bind_rows(panama_bike) %>% 
  bind_rows(panama_run) 
  

panama_map <- get_stamenmap(bbox = c(left = -79.56,
                                        bottom = 8.88, 
                                        right = -79.41,
                                        top = 9.001),
                               maptype = "terrain",
                               zoom = 13)

panama_animate<- 
  ggmap(panama_map)+
  geom_path(data = panama_all,
            aes(x = lon, y = lat, color = event),
            size = .5)+
  geom_point(data = panama_all,
             aes(x = lon, y = lat, shape = event, color = event),
             size = 2)+
  labs(title = "Panama trail",
       x = "",
       y = "")+
  scale_color_viridis_d(option = "plasma")+
  theme_map()+
  theme(legend.background = element_blank())+
  transition_reveal(time)
  
anim_save("panama_animation.gif", panama_animate)
```

![](panama_animation.gif)<!-- -->
  
## COVID-19 data

  6. In this exercise, you are going to replicate many of the features in [this](https://aatishb.com/covidtrends/?region=US) visualization by Aitish Bhatia but include all US states. Requirements:
 * Create a new variable that computes the number of new cases in the past week (HINT: use the `lag()` function you've used in a previous set of exercises). Replace missing values with 0's using `replace_na()`.  
  * Filter the data to omit rows where the cumulative case counts are less than 20.  
  * Create a static plot with cumulative cases on the x-axis and new cases in the past 7 days on the x-axis. Connect the points for each state over time. HINTS: use `geom_path()` and add a `group` aesthetic.  Put the x and y axis on the log scale and make the tick labels look nice - `scales::comma` is one option. This plot will look pretty ugly as is.
  * Animate the plot to reveal the pattern by date. Display the date as the subtitle. Add a leading point to each state's line (`geom_point()`) and add the state name as a label (`geom_text()` - you should look at the `check_overlap` argument).  
  * Use the `animate()` function to have 200 frames in your animation and make it 30 seconds long. 
  * Comment on what you observe.

```r
covid2<-
  covid19 %>% 
  group_by(state) %>% 
  mutate(lag7 = lag(cases, 7, order_by = date)) %>%
  replace_na(list(lag7 = 0)) %>% 
  mutate(new_cases = cases-lag7) %>% 
  filter(cases>20) %>% 
  ggplot(aes(x = cases, y = new_cases, group = state))+
  geom_path(color = "grey") +
  geom_point(color = "yellow") +
  geom_text(aes(label = state), check_overlap = TRUE) +
  scale_x_log10(labels = scales::comma)+
  scale_y_log10(labels = scales::comma)+
  labs(title = "COVID Cases per 10,000 persons by State",
       subtitle = "Date: {frame_along}",
       x = "Cumulative Cases",
       y = "New cases in the past 7 days")+
  theme(legend.position = "none") +
  transition_reveal(date)
  
animate(covid2, duration = 30, nframes = 300)
anim_save("cov_cases.gif", covid2)
```

![](cov_cases.gif)<!-- -->
  
From this graph, we see that new covid cases increasing in May, and then eventually falling for some states, but states like Florida and California have increasing number of new cases even after there is a decrease in August/September. New York increases the most in May and then drops its new case count by June.   
  
  7. In this exercise you will animate a map of the US, showing how cumulative COVID-19 cases per 10,000 residents has changed over time. This is similar to exercises 11 & 12 from the previous exercises, with the added animation! So, in the end, you should have something like the static map you made there, but animated over all the days. Put date in the subtitle. Comment on what you see.


```r
states_map <- map_data("state")

covid19_population <-
  covid19 %>% 
  mutate(state = str_to_lower(state)) %>%
  left_join(census_pop_est_2018,
            by = "state") %>% 
  group_by(state, est_pop_2018, date) %>%
  summarize(cumulative_cases = max(cases)) %>%
  mutate(cases_per_10000 = (cumulative_cases/est_pop_2018)*10000)
         
covid_map <- covid19_population %>% 
  mutate(state = str_to_lower(state), weekday = wday(date, label=TRUE)) %>%
  filter(weekday == "Fri") %>%
  ggplot() +
  geom_map(map = states_map,
           aes(map_id = state, fill = cases_per_10000, group = date)) +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  labs(title = "Cumulative COVID-19 cases per 10,000 people in the United States") +
  theme(legend.background = element_blank()) + 
  theme_map() +
  scale_fill_viridis_c(option = "magma") +
  transition_states(date, transition_length = 0) +
  labs(subtitle = "Moving to {next_state}")
animate(covid_map, duration = 30) 
```

![](05_exercises_files/figure-html/unnamed-chunk-13-1.gif)<!-- -->

```r
anim_save("cov_cases1.gif", covid_map)
```

![](cov_cases1.gif)<!-- -->

Through this graph we see that there is a gradual increase in the COVID cases across the USA. Initially there is a drastic increase in New York in May, and then it spreads to the entire US.

## GitHub link

  9. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 05_exercises.Rmd, provide a link to the 05_exercises.md file, which is the one that will be most readable on GitHub. If that file isn't very readable, then provide a link to your main GitHub page.

The link is attached [here](https://github.com/ayushi98/Weekly-Exercises-5.git)

**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
