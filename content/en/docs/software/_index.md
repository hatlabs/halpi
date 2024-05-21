---
title: Software
weight: 500
---

## Introduction

HALPI is a general-purpose computer that comes preinstalled with OpenPlotter 4, a custom distribution of Raspberry Pi OS with a number of pre-installed applications and services.
Documentation for each software component is provided by their respective projects:

- Raspberry Pi OS: [Raspberry Pi OS Documentation](https://www.raspberrypi.org/documentation/)
- OpenPlotter: [OpenPlotter Documentation](https://openplotter.readthedocs.io/)
- Signal K: [Signal K Documentation](https://signalk.org/)
- AvNav: [AvNav Documentation](https://wellenvogel.net/software/avnav/docs/beschreibung.html?lang=en)
- OpenCPN: [OpenCPN Documentation](https://opencpn.org/)

Hat Labs provides some additional documentation relevant to common use cases of HALPI computers.

## Data Logging and Visualization with InfluxDB and Grafana

A particularly powerful use case for HALPI computers is data logging and visualization.
Signal K, together with the InfluxDB time-series database, can be used to log data from the NMEA 2000 network and other sources.
Grafana, a popular analytics and visualization platform, can then be used to create dashboards and reports based on the logged data.

The SSD storage capacity of HALPI computers is sufficient for storing years of data.
However, since we prefer to minimize unintended disk use, we do not enable data logging by default.
Follow the instructions below to enable data logging and visualization.

### InfluxDB Installation

InfluxDB v2 can be installed using the OpenPlotter desktop tools.

If you don't have a display connected, use VNC Viewer to connect to the HALPI computer and open the OpenPlotter desktop.

Go to the top-left Raspberry menu -> OpenPlotter -> Dashboards:

{{< imgrel "op-dashboard-menu.jpg" "60%" >}}
Open the Dashboards app.
{{< /imgrel >}}

Install InfluxDB OSS 2.x and Grafana.

{{< imgrel "op-dashboards-app.jpg" "60%" >}}
Install InfluxDB and Grafana.
{{< /imgrel >}}


### Configure InfluxDB

Open InfluxDB with a browser (doesn't have to be on the VNC): http://openplotter.local:8086/

- Click Get Started
- Create a user and fill in the following information:
    - Username: admin
    - Password: (select one)
    - org: signalk
    - initial bucket: signalk

{{< imgrel "influxdb-initial-user.jpg" "60%" >}}
Setup the initial user as shown in the image.
{{< /imgrel >}}

Click Continue, and then copy the generated operator API token. You will need this later.

### Configure the Signal K InfluxDB plugin

Now that we have InfluxDB set up and running, we want to configure Signal K to send data there.

- Go to Signal K web UI: http://openplotter.local:3000/
  (Create an admin user if this is your first time here.)

Add the "Signal K to InfluxDb 2" plugin:

- Go to Appstore.
- Search for "influx".
- Click Install on "Signal K to InfluxDb 2".

{{< imgrel "sk-install-influxdb-plugin.jpg" "60%" >}}
Install the signalk-to-influxdb2 plugin.
{{< /imgrel >}}

Restart Signal K to apply the changes.

Next, configure the plugin:

- Go to Server -> Plugin Config.
- Scroll down to "Signal K to InfluxDb 2". Expand it.

The plugin can connect to multiple InfluxDB instances. We will only use one:

- Add a new Influx instance
- Url: http://127.0.0.1:8086
- Set the token to the one you copied earlier
- Organization: signalk
- Bucket: signalk

If you're mostly interested in your own boat data, select "Store only self data".
Leaving that unchecked may result in a lot of data being stored, especially if you are in a busy area.

{{< imgrel "sk-plugin-config.jpg" "60%" >}}
Configure the InfluxDB plugin.
{{< /imgrel >}}

You can skip the ignored path and sources at this point.
Come back later to play with the settings once you have some data.

Select "Use timestamps from SK data." We want to store the data time as it was generated, not when it was saved to InfluxDB (even though the difference is usually insignificant -- but we need to have some principles!).

"Resolution" is an important setting. It defines how often data is stored.
Fastest NMEA 2000 updates happen every 100 ms, but that results in a lot of data.
Usually something like 1000 ms or even slower is enough.

If you intend to view Grafana graphs in real-time, set "Flush interval" to 1000 ms.
Otherwise, you can set it to a higher value like 5000 ms or 10&nbsp;000 ms to improve disk write efficiency.

The remaining settings get pretty technical and can be safely skipped.
Be sure to click "Submit" to save your settings!

At this point, Signal K should start sending data to InfluxDB. How exciting!

### Configure Grafana

To view the data, we need to connect Grafana to InfluxDB and create a dashboard.

Login to Grafana: http://openplotter.local:3001/

At the first login, you might have to create a new admin user.
When you're in, you can create a new data source to make Grafana access the InfluxDB data:

- Click on "Add your first data source"
  - Data source type: Influxdb
  - Name: influxdb
  - Query language: InfluxQL
  - HTTP URL: http://localhost:8086

To authenticate, you need to add a custom HTTP header:

- Header: Authorization
- Value: Token (token from above)

{{< imgrel "grafana-auth-header.jpg" "60%" >}}
Add the Authorization header.
{{< /imgrel >}}

Use the following settings:

- Database: signalk
- HTTP Method: GET
- Min time interval: 1s

Then click "Save & test".
After a short while, you should get a positive acknowledgment.

### Create a Dashboard

Dashboards are collections of graphs and other visualizations.
That's where you can see your data in a more human-friendly way.
We'll just create a simple graph for now:

- Go back to Grafana Home menu
- Hamburger menu -> Dashboards -> Create Dashboard
- Add visualization
- Data source: influxdb

{{< imgrel "grafana-edit-panel.jpg" "60%" >}}
Panel editing view.
{{< /imgrel >}}

{{% alert title="Wait, what?" color="info" %}}
Before we continue, let's have a quick explanation of how the data is stored.
Under the surface, Signal K (and mostly NMEA 2000 as well) stores all of its data in SI units.
That means that the boat speed is stored in m/s, wind angle in radians, and so on.

This may sound weird because everyone knows that boat speed is in knots, wind angle in degrees, water depth in fathoms and anchor weight in stones.
However, always sticking to SI units makes the stored data unambiguous.
If we know the measurement type (e.g. temperature), we know the unit (Kelvin).
It is then the responsibility of the display layer (a dashboard, plotter app, Grafana graph) to convert the data to the desired units.

(This approach is not designed to annoy just those using imperial units. Using Kelvins and radians is equally annoying to everyone. But it is still the right way. Trust us. Principles, you know.)
{{% /alert %}}

In the "A" query, click "select measurement" and select a measurement (e.g. environment.wind.angleApparent)

The value is in radians. Let's convert it to degrees. Click the plus sign on the SELECT row. Select "math".

Edit the math expression: `/ (2 * 3.14159265) * 360`. This converts radians to degrees.

In the right hand side column, scroll down to "Standard options".
Select Unit and pick Degrees. This tells Grafana that the data, after conversion, is in degrees.

You should now have a minimal graph showing wind angle in degrees! Congratulations!
