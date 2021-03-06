*Read this in other languages: [日本語](README-ja.md).*

# Visualize and analyze San Francisco crime incidents using a Jupyter Notebook, PixieDust, and PixieApps

> Data Science Experience is now Watson Studio. Although some images in this code pattern may show the service as Data Science Experience, the steps and processes will still work.

In this Code Pattern we will use PixieDust running on IBM Watson Studio to analyze traffic calming and crime data from the City of San Francisco. Watson Studio is an interactive, collaborative, cloud-based environment where data scientists, developers, and others interested in data science can use tools (e.g., RStudio, Jupyter Notebooks, Spark, etc.) to collaborate, share, and gather insight from their data.

When the reader has completed this Code Pattern, they will understand how to:

* Use [Jupyter Notebooks](https://jupyter.org/) to load, visualize, and analyze data
* Run Notebooks in [IBM Watson Studio](https://dataplatform.cloud.ibm.com/)
* Leverage [PixieDust](https://github.com/pixiedust/pixiedust) as a python notebook helper
* Build a dashboard using [PixieApps](https://pixiedust.github.io/pixiedust/pixieapps.html)
* Fetch data from [City of San Francisco Open Data](https://datasf.org/opendata/)
* Create an interactive map with [Mapbox GL](https://www.mapbox.com/mapbox-gl-js/api/)

The intended audience for this Code Pattern is application developers and other stakeholders who wish to utilize the power of Data Science quickly and effectively.

![](doc/source/images/architecture.png)

# Flow

1. Load the provided notebook into the Watson Studio platform.
2. [DataSF Open Data](https://datasf.org/opendata/) crime info is loaded into the Jupyter Notebook.
3. The notebook analyzes the crime info.
4. You can interactively change charts and graphs.
5. A PixieApp dashboard is created and can be interacted with.

# Included Components

* [IBM Watson Studio](https://www.ibm.com/cloud/watson-studio): Analyze data using RStudio, Jupyter, and Python in a configured, collaborative environment that includes IBM value-adds, such as managed Spark.

## Featured technologies

* [Jupyter Notebooks](https://jupyter.org/): An open-source web application that allows you to create and share documents that contain live code, equations, visualizations and explanatory text.

* [PixieDust](https://github.com/pixiedust/pixiedust) Python helper library for python notebooks

* [PixieApps](https://pixiedust.github.io/pixiedust/pixieapps.html): Python library used to write UI elements for analytics, and run them directly in a Jupyter notebook.

* [Mapbox GL](https://www.mapbox.com/mapbox-gl-js/api/): JavaScript library that uses WebGL to render interactive maps.

# Watch the Video

[![](https://img.youtube.com/vi/cYUdXFEmxP4/0.jpg)](https://www.youtube.com/watch?v=cYUdXFEmxP4)

# Prerequisites

* Get a [Mapbox Token](https://www.mapbox.com/) for use in the notebook

# Steps

Follow these steps to setup and run this Code Pattern. The steps are
described in detail below.

1. [Sign up for the Watson Studio](#1-sign-up-for-watson-studio)
2. [Create the Spark Service](#2-create-the-spark-service)
3. [Create the notebook](#3-create-the-notebook)
4. [Run the notebook](#4-run-the-notebook)
5. [Analyze the results](#5-analyze-the-results)
6. [Save and Share](#6-save-and-share)

## 1. Sign up for Watson Studio

Sign up for IBM's [Watson Studio](https://dataplatform.cloud.ibm.com/). By creating a project in Watson Studio a free tier ``Object Storage`` service will be created in your IBM Cloud account. Take note of your service names as you will need to select them in the following steps.

> Note: When creating your Object Storage service, select the ``Free`` storage type in order to avoid having to pay an upgrade fee.

## 2. Create the Spark service

* In your project go to the `Settings` tab, scroll down to `Associated Services` and choose `+ Add service` -> `Spark`

![](doc/source/images/createSparkService.png)

* Either choose and `Existing` Spark service, or create a `New` one

<img width="500" src="doc/source/images/chooseExistingSpark.png">

## 3. Create the notebook

* In [Watson Studio](https://dataplatform.cloud.ibm.com/), click on `Create notebook` to create a notebook.
* Create a project if necessary, provisioning an object storage service if required.
* In the `Assets` tab, select the `Create notebook` option.
* Select the `From URL` tab.
* Enter a name for the notebook.
* Optionally, enter a description for the notebook.
* Enter this Notebook URL: https://raw.githubusercontent.com/IBM/pixiedust-traffic-analysis/master/notebooks/pixiedust-traffic-analysis.ipynb
* Select the Spark runtime you've associated with this project:

<img width="500" src="doc/source/images/chooseSparkRuntime.png">

* Click the `Create` button.

![](doc/source/images/create_notebook.png)

## 4. Run the notebook

> NOTE: See the points in the notebook where you will have to enter your [Mapbox Token](https://www.mapbox.com) to render the map.

When a notebook is executed, what is actually happening is that each code cell in
the notebook is executed, in order, from top to bottom.

Each code cell is selectable and is preceded by a tag in the left margin. The tag
format is `In [x]:`. Depending on the state of the notebook, the `x` can be:

* A blank, this indicates that the cell has never been executed.
* A number, this number represents the relative order this code step was executed.
* A `*`, this indicates that the cell is currently executing.

There are several ways to execute the code cells in your notebook:

* One cell at a time.
  * Select the cell, and then press the `Play` button in the toolbar.
* Batch mode, in sequential order.
  * From the `Cell` menu bar, there are several options available. For example, you
    can `Run All` cells in your notebook, or you can `Run All Below`, that will
    start executing from the first cell under the currently selected cell, and then
    continue executing all cells that follow.
* At a scheduled time.
  * Press the `Schedule` button located in the top right section of your notebook
    panel. Here you can schedule your notebook to be executed once at some future
    time, or repeatedly at your specified interval.

## 5. Analyze the Results

After running each cell of the notebook, the results will display. When we use PixieDust ``display()`` to create an interactive dataset, we are able to change the visualization using tables, graphs, and charts.

### Options for PixieDust Charts

After running cell #3 `display(accidents)`, we can see by clicking the `Options` button that we are able to manipulate the keys and values for the fields used in the chart:

![](doc/source/images/pixieChartOptions.png)

Following the instructions, we use DaysOfWeek and IncidntNum, but the user can change the keys and value to see how the chart will look with different inputs.

### Use Spark SQL to query data

We use Spark SQL to isolate data to the Taraval district:

```
accidents.registerTempTable("accidents")
taraval = sqlContext.sql("SELECT * FROM accidents WHERE PdDistrict='TARAVAL'")
```

We then get an interactive map of the Taraval district:

![](doc/source/images/taravalSQLresults.png)

### Create a PixieApp Dashboard

With PixieApps, we can create a dashboard with map layers that can be used to visualize various datasets (i.e. Traffic Calming, Police Districts, and Crimes):

![](doc/source/images/pixieAppsLayers.png)

#### Create the skeleton

```
from pixiedust.display.app import *

@PixieApp
class SFDashboard():
    def mainScreen(self):
        return """
<div class="well">
    <center><span style="font-size:x-large">Analyzing San Francisco Public Safety data with PixieDust</span></center>
    <center><span style="font-size:large"><a href="https://datasf.org/opendata" target="new">https://datasf.org/opendata</a></span></center>
</div>
<div class="row">
    <div class="form-group col-sm-2" style="padding-right:10px;">
        <div><strong>Layers</strong></div>
        {% for layer in this.layers %}
        <div class="rendererOpt checkbox checkbox-primary">
            <input type="checkbox" pd_refresh="map{{prefix}}" pd_script="self.toggleLayer({{loop.index0}})">
            <label>{{layer["name"]}}</label>
        </div>
        {%endfor%}
    </div>
    <div class="form-group col-sm-10">
        <div id="map{{prefix}}" pd_entity pd_options="{{this.formatOptions(this.mapJSONOptions)}}"/>
    </div>
</div>
"""
```

#### Create the Map of Incidents

```
<div id="map{{prefix}}" pd_entity pd_options="{{this.formatOptions(this.mapJSONOptions)}}"/>
```

``pd_entity``: Tell PixieDust which dataset to work on.

``pd_options``: Contains the PixieDust options for the map.

#### Generate the pd_options

The best way to generate the ``pd_options`` for a PixieDust visualization is to:
1.  Call ``display()`` on a new cell
2.  Graphically select the options for your chart
3.  Select ``View/Cell Toobar/Edit metadata`` menu
4.  Click on the ``Edit Metadata`` button and copy the PixieDust metadata

![](doc/source/images/pixieEditMetadata.png)

To conform to the ``pd_options`` notation, we need to transform the PixieDust JSON metadata into an attribute string with the following format: ```“key1=value1;key2=value2;…”```

To make it easier, we use a simple Python transform function:
```
def formatOptions(self, options):
    return ';'.join(["{}={}".format(k,v) for (k, v) in iteritems(options)])
```

The ``formatOptions`` is then invoked using JinJa2 notation from within the html:

```
pd_options = “{{this.formatOptions(this.mapJSONOptions)}}”
```

#### Initialize the pd_options

> Note: setup is a special method that will be called automatically when the PixieApp is initialized.

> A Mapbox token is included in the notebook, for now. To get your own visit [Mapbox](https://www.mapbox.com/)

```
def setup(self):
    self.mapJSONOptions = {
      	"mapboxtoken": "pk.eyJ1IjoicmFqcnNpbmdoIiwiYSI6ImNqM2s4ZDg4djAwcGYyd3BwaGxwaDV3bWoifQ.d5Rklkdu5MeGAnXu1GMNYw",
      	"chartsize": "90",
	"aggregation": "SUM",
	"rowCount": "500",
	"handlerId": "mapView",
	"rendererId": "mapbox",
	"valueFields": "IncidntNum",
	"keyFields": "X,Y",
	"basemap": "light-v9"
    }
```

#### Create the GeoJSON Custom Layers

```
from pixiedust.display.app import *
from pixiedust.apps.mapboxBase import MapboxBase

@PixieApp
class SFDashboard(MapboxBase):
    def setup(self):

    ...<snip>...

    self.setLayers([
        {
            "name": "Traffic calming",
            "url": "https://data.sfgov.org/api/geospatial/ddye-rism?method=export&format=GeoJSON",
            "type": "symbol",
            "layout": {
                "icon-image": "police-15",
                "icon-size": 1.5
            }
        },

    ...<snip>...
```

#### Create the Checkboxes from the Layers

```
    ...<snip>...

    {% for layer in this.layers %}
    <div class="rendererOpt checkbox checkbox-primary">
        <input type="checkbox" pd_refresh="map{{prefix}}" pd_script="self.toggleLayer({{loop.index0}})">
        <label>{{layer["name"]}}</label>
    </div>
    {%endfor%}

    ...<snip>...
```
The user can now select layers and the map will dynamically add or remove them.


## 6. Save and Share


### How to save your work:

Under the `File` menu, there are several ways to save your notebook:

* `Save` will simply save the current state of your notebook, without any version
  information.
* `Save Version` will save your current state of your notebook with a version tag
  that contains a date and time stamp. Up to 10 versions of your notebook can be
  saved, each one retrievable by selecting the `Revert To Version` menu item.

### How to share your work:

You can share your notebook by selecting the “Share” button located in the top
right section of your notebook panel. The end result of this action will be a URL
link that will display a “read-only” version of your notebook. You have several
options to specify exactly what you want shared from your notebook:

* `Only text and output`: will remove all code cells from the notebook view.
* `All content excluding sensitive code cells`:  will remove any code cells
  that contain a *sensitive* tag. For example, `# @hidden_cell` is used to protect
  your dashDB credentials from being shared.
* `All content, including code`: displays the notebook as is.
* A variety of `download as` options are also available in the menu.

# Sample Output

There is a sample of the output in [data/examples/pixiedust-traffic-analysis.html](data/examples/pixiedust-traffic-analysis.html), it is best viewed via [rawgit](https://cdn.rawgit.com/IBM/pixiedust-traffic-analysis/783542ab87a71db93e7d9b95f732697c4219cf51/data/examples/pixiedust-traffic-analysis.html).

> Note: Some interactive map functionality, like ```Options``` and ```Layers``` will not work. To see these, you must run the notebook itself.

# Links
* [Demo on youtube](https://www.youtube.com/watch?v=cYUdXFEmxP4)
* [PixieDust](https://github.com/pixiedust/pixiedust)
* [PixieApps](https://pixiedust.github.io/pixiedust/pixieapps.html)
* [City of San Francisco Open Data](https://datasf.org/opendata/)

# Learn more

* **Artificial Intelligence Code Patterns**: Enjoyed this Code Pattern? Check out our other [AI Code Patterns](https://developer.ibm.com/technologies/artificial-intelligence/).
* **Data Analytics Code Patterns**: Enjoyed this Code Pattern? Check out our other [Data Analytics Code Patterns](https://developer.ibm.com/technologies/data-science/)
* **AI and Data Code Pattern Playlist**: Bookmark our [playlist](https://www.youtube.com/playlist?list=PLzUbsvIyrNfknNewObx5N7uGZ5FKH0Fde) with all of our Code Pattern videos
* **With Watson**: Want to take your Watson app to the next level? Looking to utilize Watson Brand assets? [Join the With Watson program](https://www.ibm.com/watson/with-watson/) to leverage exclusive brand, marketing, and tech resources to amplify and accelerate your Watson embedded commercial solution.
* **Watson Studio**: Master the art of data science with IBM's [Watson Studio](https://dataplatform.cloud.ibm.com/)

# License

This code pattern is licensed under the Apache Software License, Version 2.  Separate third party code objects invoked within this code pattern are licensed by their respective providers pursuant to their own separate licenses. Contributions are subject to the [Developer Certificate of Origin, Version 1.1 (DCO)](https://developercertificate.org/) and the [Apache Software License, Version 2](https://www.apache.org/licenses/LICENSE-2.0.txt).

[Apache Software License (ASL) FAQ](https://www.apache.org/foundation/license-faq.html#WhatDoesItMEAN)
