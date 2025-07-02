---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Analyzing Big Data with Amazon EMR"

summary: "Building Haddop clusters from scratch for large-scale data is no easy task. Amazon EMR is a managed Analytics service born to make big data analytics simple and efficient. Amazon EMR also supports relevant known tools such as Presto, Hive, Pig and many more."
authors: [Anderson Wu]
tags: [Analytics]
categories: [Amazon EMR, Analytics, HiveQL]
date: 2021-05-13T05:47:23+08:00

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
In this project, we look to deploy a Haddop cluster with **Amazon EMR** for analyzing log data stored in S3. A SQL analagous query language **HiveQL** is incorporated for data processing and analytics.

## Objectives
- Get familiar with Amazon EMR
- Get familiar with analyzing data using HiveQL

&nbsp;

## Implementation
### 1. Prepare Storage for Amazon EMR

- Create a S3 bucket.
![image info](S3.png)



&nbsp;

### 2. Luanching of an EMR Cluster

 Launch an EMR cluster
![image info](Create_cluster.png)

#### Set up EMR node types, instance types and instance counts
![image info](node_type.png)
In this case, we have 2 nodes in this specific cluster: a master node and a core node
#### Set up Networking
![image info](network.png)

#### Cluster general settings
set up cluster name and choose the S3 bucket we've just created. "Termination protection" is to prevent the cluster from accidental termination. This needs to be turn off if we were to shut down the cluster in the end.
![image info](cluster_name.png)
#### Set up security options
![image info](Security_options.png)
#### Set up EC2 security groups
![image info](security_group.png)
<!-- - Record EMS cluster public DNS to access EMR cluster later on.
ec2-34-220-55-173.us-west-2.compute.amazonaws.com -->

#### Finally! Luanch the cluster
![image info](start_cluster.png)
Wait patiently while status "starting" shifts to "waiting".
&nbsp;

### 3. Processing Data with Hive Script

A step in Amazon EMR is a unit of work that includeds one or more Hadoop jobs. 
![image info](step.png)

&nbsp;

### 4. View the results 

Results of the Hive Script are stored in S3 bucket. Download and view the text files of .


&nbsp;

### 5. Performing HiveQL for Data Querying
Other than performing Hive script through steps defined in EMR, we can also ssh connect to EMR cluster and conduct HiveQL through terminal.

Use EMR.pem to ssh connect to the public DNS of the cluster.
![image info](login_EMR.png)

Hive application is already installed for us in the cluster. Start Hive application and input HiveQL to query log data.
![image info](query_results_terminal.png)


&nbsp;



