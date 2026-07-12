## What Is SIEM?

Crucial within the realm of computer protection, Security Information and Event Management (SIEM) encompasses the utilization of software offerings and solutions that merge the management of security data with the supervision of security events. These instruments facilitate real-time evaluations of alerts related to security, which are produced by network hardware and applications.

SIEM tools possess an extensive range of core functionalities, such as the collection and administration of log events, the capacity to examine log events and supplementary data from various sources, as well as operational features like incident handling, visual summaries, and documentation.

## What Is The Elastic Stack?
The Elastic stack, created by Elastic, is an open-source collection of mainly three applications (Elasticsearch, Logstash, and Kibana) that work in harmony to offer users comprehensive search and visualization capabilities for real-time analysis and exploration of log file sources.
Let's delve into each component of the Elastic stack.

`Elasticsearch` is a distributed and JSON-based search engine, designed with RESTful APIs. As the core component of the Elastic stack, it handles indexing, storing, and querying. Elasticsearch empowers users to conduct sophisticated queries and perform analytics operations on the log file records processed by Logstash.

`Logstash` is responsible for collecting, transforming, and transporting log file records. Its strength lies in its ability to consolidate data from various sources and normalize them.

`Kibana` serves as the visualization tool for Elasticsearch documents. Users can view the data stored in Elasticsearch and execute queries through Kibana. Additionally, Kibana simplifies the comprehension of query results using tables, charts, and custom dashboards.
Note: `Beats` is an additional component of the Elastic stack. These lightweight, single-purpose data shippers are designed to be installed on remote machines to forward logs and metrics to either Logstash or Elasticsearch directly. Beats simplify the process of collecting data from various sources and ensure that the Elastic Stack receives the necessary information for analysis and visualization.
`Beats` -> `Logstash` -> `Elasticsearch` -> `Kibana`
![](Pasted%20image%2020260711153928.png)
`Beats` -> `Elasticsearch` -> `Kibana`
![](Pasted%20image%2020260711153937.png)

## The Elastic Stack As A SIEM Solution
The Elastic stack can be used as a Security Information and Event Management (SIEM) solution to collect, store, analyze, and visualize security-related data from various sources.

To implement the Elastic stack as a SIEM solution, security-related data from various sources such as firewalls, IDS/IPS, and endpoints should be ingested into the Elastic stack using Logstash. Elasticsearch should be configured to store and index the security data, and Kibana should be used to create custom dashboards and visualizations to provide insights into security-related events.
As Security Operations Center (SOC) analysts, we are likely to extensively use Kibana as our primary interface when working with the Elastic stack. Therefore, it is essential to become proficient with its functionalities and features.
![](Pasted%20image%2020260711154337.png)
Kibana Query Language (KQL) is a powerful and user-friendly query language designed specifically for searching and analyzing data in Kibana.Let's explore the technical aspects and key components of the KQL language.

- `Basic Structure`: KQL queries are composed of `field:value` pairs, with the field representing the data's attribute and the value representing the data you're searching for. For example:
The KQL query `event.code:4625` filters data in Kibana to show events that have the [Windows event code 4625](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4625). This Windows event code is associated with failed login attempts in a Windows operating system.
By further refining the query with additional conditions, such as the source IP address, username, or time range, SOC analysts can gain more specific insights and effectively investigate potential security incidents.

- `Free Text Search`: KQL supports free text search, allowing you to search for a specific term across multiple fields without specifying a field name. For instance:
- `Logical Operators`: KQL supports logical operators AND, OR, and NOT for constructing more complex queries. Parentheses can be used to group expressions and control the order of evaluation. For example:
```
event.code:4625 AND winlog.event_data.SubStatus:0xC0000072
```

By using this query, SOC analysts can identify failed login attempts against disabled accounts. Such a behavior requires further investigation, as the disabled account's credentials may have been identified somehow by an attacker.

- `Comparison Operators`: KQL supports various comparison operators such as `:`, `:>`, `:>=`, `:<`, `:<=`, and `:!`. These operators enable you to define precise conditions for matching field values. For instance:
```
event.code:4625 AND winlog.event_data.SubStatus:0xC0000072 AND @timestamp >= "2023-03-03T00:00:00.000Z" AND @timestamp <= "2023-03-06T23:59:59.999Z"
```

`Wildcards and Regular Expressions`: KQL supports wildcards and regular expressions to search for patterns in field values. For example:
```
event.code:4625 AND user.name: admin*
```

## How To Identify The Available Data

**Example**: Identify failed login attempts against disabled accounts that took place between March 3rd 2023 and March 6th 2023 KQL:

```
event.code:4625 AND winlog.event_data.SubStatus:0xC0000072 AND @timestamp >= "2023-03-03T00:00:00.000Z" AND @timestamp <= "2023-03-06T23:59:59.999Z"
```

**Data and field identification approach 1: Leverage KQL's free text search**

Using the [Discover](https://www.elastic.co/guide/en/kibana/current/discover.html) feature, we can effortlessly explore and sift through the available data, as well as gain insights into the architecture of the available fields, before we start constructing KQL queries.
- By using a search engine for the Windows event logs that are associated with failed login attempts, we will come across resources such as [https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4625](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4625)
- Using KQL's free text search we can search for `"4625"`. In the returned records we notice `event.code:4625`, `winlog.event_id:4625`, and `@timestamp`
    - `event.code` is related to the [Elastic Common Schema (ECS)](https://www.elastic.co/guide/en/ecs/current/ecs-event.html#field-event-code)
    - `winlog.event_id` is related to [Winlogbeat](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html)
    - If the organization we work for is using the Elastic stack across all offices and security departments, it is preferred that we use the ECS fields in our queries for reasons that we will cover at the end of this section.
    - `@timestamp` typically contains the time extracted from the original event and it is [different from `event.created`](https://discuss.elastic.co/t/winlogbeat-timestamp-different-with-event-create-time/278160)
![](Pasted%20image%2020260711154717.png)

When it comes to disabled accounts, the aforementioned resource informs us that a SubStatus value of 0xC0000072 inside a 4625 Windows event log indicates that the account is currently disabled. Again using KQL's free text search we can search for `"0xC0000072"`. By expanding the returned record we notice `winlog.event_data.SubStatus` that is related to [Winlogbeat](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html)

![](Pasted%20image%2020260711154731.png)

**Data and field identification approach 2: Leverage Elastic's documentation**

It could be a good idea to first familiarize ourselves with Elastic's comprehensive documentation before delving into the "Discover" feature. The documentation provides a wealth of information on the different types of fields we may encounter. Some good resources to start with are:

- [Elastic Common Schema (ECS)](https://www.elastic.co/guide/en/ecs/current/ecs-reference.html)
- [Elastic Common Schema (ECS) event fields](https://www.elastic.co/guide/en/ecs/current/ecs-event.html)
- [Winlogbeat fields](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html)
- [Winlogbeat ECS fields](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)
- [Winlogbeat security module fields](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-security.html)
- [Filebeat fields](https://www.elastic.co/guide/en/beats/filebeat/current/exported-fields.html)
- [Filebeat ECS fields](https://www.elastic.co/guide/en/beats/filebeat/current/exported-fields-ecs.html)