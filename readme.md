# NetScout data extract utility

### Summary: A quick and portable utility using html forms to extract NetScout data from nGenius, via the NetScout API's available

*Relevance and why this tool is useful*

NetScout provides the ability to extract raw data from its built in tables, this can be done via specific SQL queries, however they also provide an API which works with html get/post queries. This method is easier, more portable and supports extraction of data in xml, csv, html. 

The article below explains how this is done and can be used as a template for multiple NetScout deployments, also an easy to use front end for non-experienced users. Another benefit is the ability to extract data not available via Performance Monitor or nGenius HTML5 GUI, such as extracting all IP flows for a specific interface for the past week, not a 'Top X' but ALL IP's.

## How the API works 

The underlying tool on the NetScout probes is **CDE** [Common Data Export]. This is a command line utility that allows you to extract logged data from the nGenius Performance Manager CDM Flow database or monitored element information from the nGenius Server and move it to a third-party system.

CDE allows you to capture data that could be displayed in the Performance Manager Console from the command line and save it in a CSV or HTML file. You can then use the data to generate formatted output with standard front-end reporting software.

This can be run as a CLI via nGenius server itself or like we use here send the instructions via HTML requests, which is known as *URL-Based Data Access Tool*.

## Usage Guidelines

Before you start using CDE, here are some helpful guidelines to keep in mind:

- Use CDE at off-peak hours, for extremely large queries such as month, 3 months, 6 month and yearly views (The queries can be memory intensive.)

- Avoid long durations (load times) by following these guidelines:

- For 15-minute data, use a duration of 24 hours or less.

- For one-minute data, use a duration of one hour or less.

There is no reason to access more granular data apart from efficiency for loading times and memory usage on the server, however we have tested obtaining weekly views for ALL IP addresses seen on a Internet facing TAP/interface and the only drawback was the time it took to obtain the data. There was no impact on a live production nGenius server / probe.

### Using the URL-Based CDE Data Access Tool

To use the URL-Based Data Access Tool, enter a URL in your browser in the following format:

```
http://<IP address of PM Server>:8080/tools/DataReport.jsp?ME=<monitored elementvalue>&APP=<parameters>&DURATION=<timeduration options>&VIEW_ID=<view ID options>&username=<username>&password=<password>&DT=<display type>
```

- Enter the IP address of your PM server
- The default port it listens on is 8080
- Specify the monitored element (interface)
- Add some parameters (we will go into detail below)
- Enter the time duration you want the data for
- The ViewID (servers, clients, ports)
- Enter type of output (CSV, HTML, XML)

*Details of supported parameters can be found on NetScout support pages, however some commonly used parameters are:*

**APP**

**APPGROUP**

**BH** - Business Hours

**CHART_MODE** - "ss" for snapshot data; "ot" for over time (e.g; "CHART_MODE=ss")

**DST** - Destination conversation address ("DST=10.20.1.1")

**DURATION** - ("DURATION=last_hour")

**HOST**

**ME** - meName (IpAddress:Ifn)

**SRC**

**SRCSITE**

**TOPN** - TopN value

| Time Duration Values	| Resolution Values | Display |
| -----------------------| -----------------:|--------:|
|last_hour					| 1min				  |DT=xml (returns a XML page)
|last_6hours				| 5min				  |DT=csv (returns a CSV file)
|last_12hours				|15min				  |DT=html (returns a HTML page)
|last_24hours				|1hour
|today						|1day
|yesterday					|1week
|last_7days				|1month
|last_31days				|
|last_week
|last_month
|last_3months
|last_6months
|last_12months
