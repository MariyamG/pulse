![Travis Build Tag](https://travis-ci.org/phdata/pulse.svg?branch=master)

<p align="center">
  <img src="logo.png" alt="logo.png" length="200" width="200" />


  <h3 align="center"></h3>

  <p align="center">
    Hadoop log aggregation, alerting and management
    <br>

  </p>
</p>

<br>

# Pulse
Hadoop log aggregation, alerting, and lifecycle management

## Pulse Features

### Log Aggregation and Search
- Add log aggregation, search, and alerts to your Java, Scala, Bash, or Python programs and scripts

### Alerting
- Flexible alerting using the Pulse Alert Engine
- Write alerts using the full solr query syntax. Example alerts:
	- There was an appliction Error in the last 5 minutes 
	- My application has stopped logging messages
- Email and web hook integrations
	
### Log Lifecycle Management
- Built in rotation of logs. 
  - Keep logs for as long as you need them
  - Automatically rotate new log indexes after a configured time period

### Dashboarding
- Integration with existing dashboards for rich UIs. 

Works with 
- Banana
- Hue Search
- Arcadia Data

### Hadoop Native
- Pulse uses Solr for document indexing and search and can be deployed on your Hadoop cluster
- Use existing Sentry roles to control access to log data

### Cloudera Manager Integration
- Simple deployment using CSDs and Parcels
- Manage and monitor Pulse from within Cloudera Manager

## Adding an application to Pulse
If a new application wants to use Pulse, a block should be created in the Collection Roller config.yml 
file and the Alert Engine config.yml file. See the [Alert Engine readme](./alert-engine/README.md)
and [Collection Roller readme](./collection-roller/README.md) for more details.

## Components

### Collection Roller
The collection roller is responsible for creating solr indexes for new applications in Pulse and 
deleting old or expired log collections. 

### Alert Engine
The Alert Engine is a daemon process with a set of `alertRules`. Alert rules can be configured
to notify developers and application users when an event happens.

See the [Alerting Engine README](./alert-engine/README.md) for more details and configuration
options.

### Log Collector
The Log Collector is an HTTP server that receives log messages as JSON and puts them into SOLR
 collections.

See the [Log Collector README](./log-collector/README.md) for more details and configuration
 options.
### Log Appender
An HTTP log appender for log4j 1.x that will post json messages to the log collector.

See the [Log Appender README](./log-appender/README.md) for more details.

## Installation
Pulse can be installed as a Cloudera CSD (Custom Service Descriptor). See [CSD](./cloudera-integration/csd/README.md) 
and [PARCEL](./cloudera-integration/parcel/README.md) READMEs for details.

***Installing the CSD***

1. Download the latest CSD jar []()
2. Place the jar in your Cloudera Manager CSD directory, usually `/opt/cloudera/csd`
3. Modify the ownership of the jar `chown cloudera-scm: /opt/cloudera/csd/pulse-<version>.jar`
4. Restart Cloudera Manager to install the jar

***Installing the Parcel***

1. Download the parcel and parcel.sha from []()
2. Place the parcel in the parcel-repo directory, usuall `/opt/cloudera/parcel-repo`
3. In Cloudera Manager, navigate to the Parcels page and click 'Check for new Parcels' button
4. Find the Pulse parcel, deploy and activate it

***Installing the service***

Pulse can be installed through the "Add New Service" button for your cluster.

The wizard will ask you for:
- `smpt user`: This will be the 'from' address for alerts
- `smtp password`: This is only necessary if your smtp server uses authentication
- `smtp address`: The hostname of your smpt server
- `smtp port`: The port of your smtp server

***Deploying config***
Pulse has two configuration files, `collection-roller.yml` and `alert-engine.yml`

Both of these configs are deployed via safety valves. [Alert Engine Example](./example-configs/collection-roller/collection-roller.yml) and [Collection Roller Example](./example-configs/alert-engine/alert-engine.yml)

Place `collection-roller.yml` in `
Collection Roller Advanced Configuration Snippet (Safety Valve) for collection-roller.yml`

Place the `alert-engine.yml` in `
Alert Engine Advanced Configuration Snippet (Safety Valve) for alert-engine.yml`

## Running the application manually on a cluster

Remove the `.template` suffix from these two files under the local directory.
- `env.sh.template`
- `jaas.conf.template`

Change the kerberos keyTab and principal in `jaas.conf` to your own (you need to create one if you don't have it already)

Run individual components:
```bash
$ local/run-collection-roller
$ local/run-log-collector
$ local/run-alert-engine
```

to run all components. Note `j3` is necessary to run all components, each running on their own thread. If `j3` is
left out, then only one component will run.

## Running tests
Run all tests from the project root:

```bash
$ make test
```

## Contributing
Sbt is used for building scala, and Make is added to simplify building tasks.

Makefile targets are run with `make <target-name>`, some target examples are like this:

- `dist`: create a distribution (parcel and csd)
- `test`: run all tests
- `package`: create jars from source
- `install`: install parcel and CSD. This is only valid on a node running Cloudera Manager and will install
the csd/parcel to /opt/cloudera/csd and /opt/cloudera/parcel-repo. It will not distribute/activate the parcel
or refresh/install the CSD in Cloudera Manager
- `run`:  run all components locally. This will run log collector, collection roller, and alert engine.

## Submitting a pull request
Before submitting a pull request, please make sure `make test` and `make dist` both pass successfully.

## Downloads

### CSD
https://s3.amazonaws.com/phdata-pulse/csd/PULSE-1.0.0-cdh5.jar

### Parcels
https://s3.amazonaws.com/phdata-pulse/parcels/PULSE-1.0.0-cdh5-el7.parcel
https://s3.amazonaws.com/phdata-pulse/parcels/PULSE-1.0.0-cdh5-el7.parcel.sha

https://s3.amazonaws.com/phdata-pulse/parcels/PULSE-1.0.0-cdh5-el6.parcel
https://s3.amazonaws.com/phdata-pulse/parcels/PULSE-1.0.0-cdh5-el6.parcel.sha
