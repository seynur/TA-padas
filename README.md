## TA for Padas;

### Overview
This add-on provides integration with [PADAS](www.padas.io) generated alerts and includes predefined inputs and field mappings to normalize the data to the Common Information Model.

### Setup Instructions

#### 1. Create an index (optional):
While it's possible to store everything in default (main) index, it is recommended to create a separate index to store Padas Alerts.  An example configuration file `indexes.conf.example` is provided with this distribution (i.e. `padas` index).

**indexes.conf**
```properties
[padas]
homePath   = $SPLUNK_DB/padas/db
coldPath   = $SPLUNK_DB/padas/colddb
thawedPath = $SPLUNK_DB/padas/thaweddb
```

#### 2. Define HEC Input:
In order to index Padas Alerts, you'll first need to define a HEC input.  An example configuration file `inputs.conf.example` is provided with this distribution.

**inputs.conf**
```properties
[http]
disabled = 0

# Following is a sample HEC input for getting PADAS Alerts from Confluent Kafka Connect
# https://www.confluent.io/hub/splunk/kafka-connect-splunk
[http://padasinput]
disabled = 0
token = e8de5f0e-97b1-4485-b416-9391cbf89392
useACK = 0
```

#### 3. Enrich with Padas Rules (optional):
This add-on has seamless integration with [MITRE ATT&amp;CK App for Splunk](https://splunkbase.splunk.com/app/4617/) where you can simply populate MITRE ATT&amp;CK related dashboards with alerts triggered via PADAS.  In order to enrich `padas:alert` event with corresponding rule, you will need to perform the following:
- Download Padas Rules: From Padas Manager interface you'll need to select *Rules* from the menu and click *Download* in order to download all applicable rules as a json file.  For details please refer to https://padas.io/docs/user-guide.html#rules
- Upload `padasRules.json` to Splunk: This add-on comes with a KV Store lookup definition for Padas Rules.  You'll need to run the following command:
  
  **Command**
  ```sh
  curl -k -u <username>:<password> https://<SPLUNK SEARCH HEAD>:8089/servicesNS/nobody/TA-padas/storage/collections/data/padas_rules_collection/batch_save -H "Content-Type: application/json" -d @<DOWNLOADED JSON FILE>
  ```

  **Example:**
  ```sh
  curl -k -u admin:changeme https://localhost:8089/servicesNS/nobody/TA-padas/storage/collections/data/padas_rules_collection/batch_save -H "Content-Type: application/json" -d @padasRules.json
  ```

#### 4. Send Data:
You will need to configure Confluent Kafka with Splunk Sink Connector to send data.  Please refer to [PADAS documentation](https://padas.io/docs/admin-guide.html#integrate-to-external-systems) on how to setup Splunk Sink Connector and its configuration to send data to Splunk HEC input.


#### 5. Enable Scheduled Searches (optional):
This add-on comes with 2 scheduled searches in order to integrate `padas:alert` events to either `notable` index or `alerts` index to be used by [Splunk Enterprise Security](https://splunkbase.splunk.com/app/263/) or [Alert Manager](https://splunkbase.splunk.com/app/2665/).

Saved Searches (disabled by default):
```
Padas Detect Engine - Notable Event Gen
Padas Detect Engine - Alert Manager Event Gen
```

Please enable the one that is applicable to your platform.

<br/>

---

<br/>

### Release Notes:
Version 1.0.0
- Date: 15 Jan 2022
- Initial version

### Support
Contact information for reporting an issue: development@seynur.com
