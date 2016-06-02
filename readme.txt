
Summary: A quick and portable utility using html forms to extract NetScout data from nGenius, via the NetScout API's available

*Relevance and why this tool is useful*

NetScout provides the ability to extract raw data from its built in tables, this can be done via specific SQL queries, however they also provide an API which works with html get/post queries. This method is easier, more portable and supports extraction of data in xml, csv, html. 

The article below explains how this is done and can be used as a template for multiple NetScout deployments, also an easy to use front end for non-experienced users. Another benefit is the ability to extract data not available via Performance Monitor or nGenius HTML5 GUI, such as extracting all IP flows for a specific interface for the past week, not a 'Top X' but ALL IP's.

### How the API works 

The underlying tool on the NetScout probes is **CDE** [Common Data Export]. This is a command line utility that allows you to extract logged data from the nGenius Performance Manager CDM Flow database or monitored element information from the nGenius Server and move it to a third-party system.

CDE allows you to capture data that could be displayed in the Performance Manager Console from the command line and save it in a CSV or HTML file. You can then use the data to generate formatted output with standard front-end reporting software.

This can be run as a CLI via nGenius server itself or like we use here send the instructions via HTML requests, which is known as *URL-Based Data Access Tool*.

### Usage Guidelines

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

#### Sample Code and URL request
The below can be used as a template to create a HTML form which take sin user data, and utilises radio buttons with pre-defined values for interfaces, times, and resolutions.

This will then send the request and obtain the relevant data.

##### CSS code to make the form presentable
```css
.flat-table {
  display: block;
  font-family: sans-serif;
  -webkit-font-smoothing: antialiased;
  font-size: 115%;
  overflow: auto;
  width: auto;
  }
.th {
    background-color: rgb(112, 196, 105);
    color: white;
    font-weight: normal;
    padding: 20px 30px;
    text-align: center;
  }
.td {
    background-color: rgb(238, 238, 238);
    color: rgb(111, 111, 111);
    padding: 20px 30px;
  }
```

##### HTML code which should have the inputs changed

```html

<!DOCTYPE html>
<html>
<head>

<link rel="stylesheet" type="text/css" href="query.css">

</head>

<body>

<form action="https://x.x.x.x/tools/DataReport.jsp?" method="get">
<table class="flat-table">
  <tbody>
    <tr>
      <th class="th">Interface</th>
      <th class="th">Duration</th>
	  <th class="th">Resolution</th>
	  <th class="th">Table ID</th>
    </tr>
    <tr>
      <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Interface 12</td>
	  <td class="td"><input type="radio" name="DURATION" value="last_hour">Last Hour</td>
	  <td class="td"><input type="radio" name="RES" value="1min">1 Minute</td>
	  <td class="td"><select name="VIEW_ID" size="5" id="VIEW_ID" size="20" multiple="multiple" >
		<option value="AlarmViewId">AlarmViewId</option>
		<option value="AlConvViewId">AlConvViewId</option>
		<option value="AlHostViewId">AlHostViewId</option>
		<option value="AllAlConvViewId">AllAlConvViewId</option>
		<option value="AllAlHostViewId">AllAlHostViewId</option>
		<option value="AllAppServersRespViewId">AllAppServersRespViewId</option>
		<option value="AllAppsRespViewId">AllAppsRespViewId</option>
		<option value="AllAppsViewId">AllAppsViewId</option>
		<option value="AllClientsRespViewId">AllClientsRespViewId</option>
		<option value="AllDshViewId">AllDshViewId</option>
		<option value="AllFlowsRespViewId">AllFlowsRespViewId</option>
		<option value="AllLinkViewId">AllLinkViewId</option>
		<option value="AllLocationsRespViewId">AllLocationsRespViewId</option>
		<option value="AllNlConvViewId">AllNlConvViewId</option>
		<option value="AllNlHostViewId">AllNlHostViewId</option>
		<option value="AllNlProtocolsViewId">AllNlProtocolsViewId</option>
		<option value="AllServersRespViewId">AllServersRespViewId</option>
		<option value="AppBurstViewId">AppBurstViewId</option>
		<option value="AppRespViewId">AppRespViewId</option>
		<option value="AppServerRespViewId">AppServerRespViewId</option>
		<option value="AppViewId">AppViewId</option>
		<option value="CallsViewId">CallsViewId</option>
		<option value="CapacityPlanningViewId">CapacityPlanningViewId</option>
		<option value="ClientRespViewId">ClientRespViewId</option>
		<option value="CSAllAppServersRespViewId">CSAllAppServersRespViewId</option>
		<option value="CSAllAppsRespViewId">CSAllAppsRespViewId</option>
		<option value="CSAllAppsViewId">CSAllAppsViewId</option>
		<option value="CSAllClientsRespViewId">CSAllClientsRespViewId</option>
		<option value="CSAllConvsViewId">CSAllConvsViewId</option>
		<option value="CSAllFlowsRespViewId">CSAllFlowsRespViewId</option>
		<option value="CSAllHostsViewId">CSAllHostsViewId</option>
		<option value="CSAllServersRespViewId">CSAllServersRespViewId</option>
		<option value="CSAppBurstViewId">CSAppBurstViewId</option>
		<option value="CSAppRespViewId">CSAppRespViewId</option>
		<option value="CSAppServerRespViewId">CSAppServerRespViewId</option>
		<option value="CSAppViewId">CSAppViewId</option>
		<option value="CSBurstViewId">CSBurstViewId</option>
		<option value="CSClientRespViewId">CSClientRespViewId</option>
		<option value="CSConvViewId">CSConvViewId</option>
		<option value="CSFlightTimeViewId">CSFlightTimeViewId</option>
		<option value="CSFlowRespViewId">CSFlowRespViewId</option>
		<option value="CSHostViewId">CSHostViewId</option>
		<option value="CSKpiErrorDistViewId">CSKpiErrorDistViewId</option>
		<option value="CSKpiViewId">CSKpiViewId</option>
		<option value="CSMAServerByClientsViewId">CSMAServerByClientsViewId</option>
		<option value="CSMAServerByRespViewId">CSMAServerByRespViewId</option>
		<option value="CSMAServersByClientsViewId">CSMAServersByClientsViewId</option>
		<option value="CSMAServersByRespViewId">CSMAServersByRespViewId</option>
		<option value="CSNumClientsViewId">CSNumClientsViewId</option>
		<option value="CSNumRespViewId">CSNumRespViewId</option>
		<option value="CSNumRetransViewId">CSNumRetransViewId</option>
		<option value="CSRespTimeDistViewId">CSRespTimeDistViewId</option>
		<option value="CSServerRespViewId">CSServerRespViewId</option>
		<option value="CSSuccessFailViewId">CSSuccessFailViewId</option>
		<option value="CSSummaryViewId">CSSummaryViewId</option>
	</select>
	</td>
    </tr>
    <tr>
      <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Interface 13</td>
	  <td class="td"><input type="radio" name="DURATION" value="last_6hours">Last 6 Hours</td>
	  <td class="td"><input type="radio" name="RES" value="5min">5 Minute</td>
    </tr>
    <tr>
      <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Interface 3</td>
	  <td class="td"><input type="radio" name="DURATION" value="last_12hours">Last 12 Hours</td>
	  <td class="td"><input type="radio" name="RES" value="15min">15 Minutes</td>
     <tr>
       <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Burwood 4</td>
	   <td class="td"><input type="radio" name="DURATION" value="last_24hours">Last 24 Hours</td>
	   <td class="td"><input type="radio" name="RES" value="1hour">1 Hour</td>
    <tr>
      <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Burwood 5</td>
	  <td class="td"><input type="radio" name="DURATION" value="today">Today</td>
	  <td class="td"><input type="radio" name="RES" value="1day">1 Day</td>
      </td>
       <tr>
      <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Interface 16</td>
	  <td class="td"><input type="radio" name="DURATION" value="yesterday">Yesterday</td>
	  <td class="td"><input type="radio" name="RES" value="1week">1 Week</td>
    </tr>
    <tr>
      <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Interface 17</td>
	  <td class="td"><input type="radio" name="DURATION" value="last_7days">Last 7 Days</td>
	  <td class="td"><input type="radio" name="RES" value="1month">1 Month</td>
    </tr>
    <tr>
      <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Norwest 13</td>
	  <td class="td"><input type="radio" name="DURATION" value="last_31days">Last 31 Days</td>
	  <th class="th">Data Format</th>
     <tr>
       <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Norwest 14</td>
	   <td class="td"><input type="radio" name="DURATION" value="last_week">Last Week</td>
	   <td class="td"><input type="radio" name="DT" value="HTML"> HTML Data</td>
    <tr>
      <td class="td"><input type="radio" name="ME" value="x.x.x.x:a"> Norwest 15</td>
	  <td class="td"><input type="radio" name="DURATION" value="last_month">Last Month</td>
	  <td class="td"><input type="radio" name="DT" value="CSV"> CSV File</td>
      </td>
	 <tr>
	   <td class="td">
	   <td class="td"><input type="radio" name="DURATION" value="last_3months">Last 3 Months</td>
	   <td class="td"><input type="radio" name="DT" value="XML"> XML File</td>
	  </tr>
	 <tr>
	   <td class="td">
	   <td class="td"><input type="radio" name="DURATION" value="last_6months">Last 6 Months</td>
  </tbody>
	</table>
	<input type="submit" value="submit">
</form>
	
</body>
</html>
```





