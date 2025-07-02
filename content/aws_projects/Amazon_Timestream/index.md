---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Telegraf with Minecraft Input Plugin & Amazon Timestream Output Plugin"

summary: "Amazon Timestream is a timeseries fully-managed database service. In this project we incroporate an opensourced agent Telegraf that streams data from minecraft server to Amazon Timestream for us."
authors: [Anderson Wu]
tags: [Database]
categories: [Amazon Timestream, Database]
date: 2021-08-10T05:47:23+08:00

# Optional external URL for project (replaces project detail page).
external_link: ""

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: ""
---
{{< toc >}}


## Purpose
The purpose of this proof of concept aims at delivering a general view of how Amazon Timestream, a fully managed time-series database, can easily cooperate with some open source data collectors and visualization tools. 
Task:
We will build a data streaming workflow incorporating open source data collector Telegraf as a bridge between our Minecraft Server and our Amazon Timestream database. 

&nbsp;

## Implementation
#### 1. Minecraft Server Configuration

**Step1**

We first launch an Ubuntu ec2 instance as a host for our Minecraft Server. Luanching a Minecraft Server is not as difficult as it might sound. Simply follow this guide: How to install Minecraft Server on Ubuntu (https://www.fosslinux.com/18063/how-to-install-minecraft-server-on-ubuntu.htm)

**Step2** 

After finished setting up our Minecraft Server Java Edition, we look to build stable RCON connections by configuring server.properties file in our Minecraft Server directory. RCON protocol allows server owners to apply in-game console even if they do not have access to the server. 


```
enable-rcon=true
rcon.password=12345678
rcon.port=25575
```

**Step3**

Now, we want to index what objective statistic we are interested the most in Minecraft. For instance, we would like to know how many times players in my Minecraft server “jumps”. There’s a Minecraft built-in application we want to make use of called “Scoreboard”. With the help of Scoreboard, we can index “number of jumps” as our target objective through this command. Check out more objectives to add on this web page (https://minecraft.gamepedia.com/Scoreboard)


```
/scoreboard objectives add jumps stat.jump
```
&nbsp;

#### 2. Telegraf Agent Configuration

**Step1**

We launch another Ubuntu ec2 instance as a host for Telegraf. Do remember to configure security group to allow inbound connection from our Minecraft server. 

**Step2**

Telegraf release notes (https://docs.influxdata.com/telegraf/v1.16/about_the_project/release-notes-changelog/#outputs) point out the release of Amazon Timestream output plugin on 2020/10/21. With the help of Telegraf community, we can now bridge Amazon Timestream with all the Telegraf input plugins. 
Telegraf configuration is easy. All we have to do is to make a little adjustments from Amazon Timestream Developer Guide (https://docs.aws.amazon.com/timestream/latest/developerguide/Telegraf.running-output-plugin.title.html).

1) Generating configuration file using TelegrafThis command takes minecraft plugin as input filter and timestream plugin as output filter. You then create a minecraft.config file in your current directory.
telegraf —section-filter agent:inputs:outputs --input-filter minecraft —output-filter timestream config > minecraft.config

2) Create a database in Timestream through management console



3) Configure minecraft.config
minecraft.config can be split into two parts, one for input plugins and the other for output plugins.


For output plugins, we configure access_key and secret_key in order to let Telegraf ingest data to Timestream database.


For input plugins, we configure our Minecraft server IP address, RCON port and RCON password. 


Finally we start Telegraf service through this command
telegraf —config minecraft.config

&nbsp;

#### 3. Query data for Amazon Timestream
Amazon Timestream supports SQL query. For more detailed instructions on efficient query command, please check out Timestream Query Language Reference (https://docs.aws.amazon.com/timestream/latest/developerguide/reference.html)


&nbsp;


#### 4. Visualization with Amazon QuickSight
Amazon QuickSight shines when it comes to data visualization. To incorporate QuickSight in our workflow, we need to go to Security & permissions setting and add Amazon Timestream.

&nbsp;



## Results

#### Part 1
Demonstrates data written from minecraft server to Amazon Timestream through Telegraf.
{{< video src="Telegraf_Timestream_1.mp4" controls="yes" >}}

#### Part 2
Demonstrates SQL command run on Amazon Timestream to retrieve "numbder of jumps" over time.
{{< video src="Telegraf_Timestream_2.mp4" controls="yes" >}}

#### Part 3
Demonstrates data visualization with Amazon Quicksight.
{{< video src="Telegraf_Timestream_3.mp4" controls="yes" >}}