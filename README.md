# Oracle Cloud Cost — Zabbix Template

[![Zabbix](https://img.shields.io/badge/Zabbix-7.0%20LTS-red)](https://www.zabbix.com/)
[![Oracle Cloud](https://img.shields.io/badge/Oracle%20Cloud-OCI-F80000)](https://www.oracle.com/cloud/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

**Zabbix template for monitoring Oracle Cloud Infrastructure (OCI) costs through the OCI Usage API.**

This project provides a Zabbix 7.0 LTS template designed to monitor Oracle Cloud Infrastructure costs directly from Zabbix.

It allows infrastructure and cloud administrators to monitor OCI spending, track daily and monthly costs, and generate alerts when a configured daily cost threshold is reached.

## Features

The template provides monitoring for:

- **Current month cost**
- **Today's cost**
- **Yesterday's cost**
- **Cost from two days ago**
- **Daily cost threshold alerts**
- **OCI Usage API integration**
- **OCI API Key authentication**
- **RSA-SHA256 request signing**
- **HTTPS/TLS communication**
- **Optional HTTP proxy support**
- **Zabbix graphs and dashboard**
- Cost values in **BRL**

## Zabbix Community Templates

This template has been officially contributed to and merged into the **Zabbix Community Templates** repository.

Official community template:

**[Zabbix Community Templates — Oracle Cloud Cost](https://github.com/zabbix/community-templates/tree/main/Cloud/Oracle/template_oracle_cloud_cost/7.0)**

The template is available under the **Cloud / Oracle** category of the Zabbix Community Templates repository.

## Template

**Template name:**

`Oracle Cloud Cost by HTTP`

**Zabbix version:**

`7.0 LTS`

**Template file:**

[`template_oracle_cloud_cost.yaml`](Cloud/Oracle/template_oracle_cloud_cost/7.0/template_oracle_cloud_cost.yaml)

**Template documentation:**

[`Cloud/Oracle/template_oracle_cloud_cost/7.0/README.md`](Cloud/Oracle/template_oracle_cloud_cost/7.0/README.md)

## How It Works

The template uses the **Oracle Cloud Infrastructure Usage API** to retrieve cost information.

The general flow is:

```text
Zabbix
   |
   | HTTPS + OCI API Signature
   v
OCI Usage API
   |
   v
Usage and Cost Data
   |
   v
Zabbix Items
   |
   +--> Graphs
   |
   +--> Dashboard
   |
   +--> Cost Alerts

The template uses OCI API Key authentication and signs requests using RSA-SHA256.

Monitoring

The template provides four main cost monitoring items:

Metric	Zabbix item key
Current month cost	oci.cost.current_month
Today's cost	oci.cost.today
Yesterday's cost	oci.cost.yesterday
Cost from two days ago	oci.cost.2days_ago

Two Warning triggers can be configured using:

{$OCI.COST.DAILY.LIMIT}

This allows the Zabbix administrator to define the daily cost threshold appropriate for the environment.

Requirements
Zabbix 7.0 LTS
Oracle Cloud Infrastructure tenancy
OCI user with API Key authentication
OCI IAM permission to access the Usage API
Network connectivity from Zabbix to the OCI Usage API

An HTTP proxy can optionally be configured for environments where Zabbix does not have direct Internet access.

Documentation

For complete installation and configuration instructions, including:

OCI IAM configuration
API Key setup
Required permissions
Zabbix macros
Authentication
HTTP proxy configuration
Items
Triggers
Dashboard
Graphs
Troubleshooting

see the version-specific documentation:

Oracle Cloud Cost by HTTP — Zabbix 7.0

Official Zabbix Template

This template is specifically focused on OCI cost monitoring.

For monitoring OCI infrastructure and resources, it can be used together with the official Zabbix template:

Oracle Cloud by HTTP

The two templates have complementary purposes:

Oracle Cloud by HTTP
        |
        +--> OCI infrastructure
        +--> Compute
        +--> Networking
        +--> Storage
        +--> Databases
        +--> Other OCI resources

Oracle Cloud Cost by HTTP
        |
        +--> Monthly cost
        +--> Daily cost
        +--> Cost thresholds
        +--> Cost alerts
Repository

GitHub:

oracle-cloud-cost-zabbix-template

Author

Developed and maintained by luizcarmo1.

License

This project is licensed under the MIT License.

See LICENSE for details: https://github.com/luizcarmo1/oracle-cloud-cost-zabbix-template/blob/fix/template-directory/LICENSE.