# Oracle Cloud Cost by HTTP

Zabbix 7.0 LTS template for monitoring **Oracle Cloud Infrastructure (OCI) costs** through the **OCI Usage API**.

This template is specifically designed for OCI cost monitoring and can be used together with the official **Oracle Cloud by HTTP** template for OCI infrastructure monitoring.

## Zabbix Community Templates

This template has been officially contributed to and merged into the **Zabbix Community Templates** repository.

Official location:

**[Zabbix Community Templates — Oracle Cloud Cost](https://github.com/zabbix/community-templates/tree/main/Cloud/Oracle/template_oracle_cloud_cost/7.0)**

Source repository:

**[oracle-cloud-cost-zabbix-template](https://github.com/luizcarmo1/oracle-cloud-cost-zabbix-template)**

## Requirements

* Zabbix **7.0 LTS**
* Oracle Cloud Infrastructure (OCI)
* OCI user with API Key authentication
* OCI IAM permission to query the Usage API
* HTTPS connectivity between Zabbix and OCI

## OCI IAM Configuration

Create a dedicated OCI user and group for Zabbix monitoring.

The minimum permission required by this template is:

```text
Allow group <group-name> to read usage-report in tenancy
```

Example:

```text
Allow group ZABBIX-OCI-Monitoring to read usage-report in tenancy
```

Additional permissions may be required depending on other OCI templates or monitoring requirements.

For security reasons, use a dedicated monitoring account and follow the principle of least privilege.

## OCI API Key

The template uses OCI API Key authentication.

The following information is required:

* Tenancy OCID
* User OCID
* API Key fingerprint
* RSA private key

Create an API Key for the OCI monitoring user through the OCI Console.

The private key is used to sign OCI API requests using **RSA-SHA256**.

The private key must be stored securely in Zabbix and must never be committed to Git.

## Template Macros

Configure the following macros on the Zabbix host:

| Macro                          | Description                 |
| ------------------------------ | --------------------------- |
| `{$OCI.API.TENANCY}`           | OCI tenancy OCID            |
| `{$OCI.API.USER}`              | OCI user OCID               |
| `{$OCI.API.FINGERPRINT}`       | OCI API Key fingerprint     |
| `{$OCI.API.PRIVATE.KEY}`       | OCI RSA private key         |
| `{$OCI.API.USAGE.HOST}`        | OCI Usage API hostname      |
| `{$OCI.API.HTTP.PROXY}`        | HTTP proxy, if required     |
| `{$OCI.HTTP.RESPONSE.CODE.OK}` | Expected HTTP response code |
| `{$OCI.COST.DAILY.LIMIT}`      | Daily cost threshold in BRL |

### Example

```text
{$OCI.API.TENANCY}

ocid1.tenancy.oc1...

{$OCI.API.USER}

ocid1.user.oc1...

{$OCI.API.FINGERPRINT}

aa:bb:cc:dd:...

{$OCI.API.PRIVATE.KEY}

-----BEGIN PRIVATE KEY-----
...
-----END PRIVATE KEY-----

{$OCI.API.USAGE.HOST}

usageapi.sa-saopaulo-1.oci.oraclecloud.com

{$OCI.API.HTTP.PROXY}

{$OCI.HTTP.RESPONSE.CODE.OK}

200

{$OCI.COST.DAILY.LIMIT}

800
```

The following macro should be configured as **Secret text**:

```text
{$OCI.API.PRIVATE.KEY}
```

The daily cost limit should be adjusted according to the organization's expected OCI spending.

## OCI Usage API

The template communicates with the OCI Usage API using HTTPS.

API endpoint:

```text
https://usageapi.<region>.oci.oraclecloud.com/20200107/usage
```

Example for the São Paulo region:

**https://usageapi.sa-saopaulo-1.oci.oraclecloud.com/20200107/usage**

The API request is authenticated using OCI's API signing mechanism.

The request flow is:

```text
Zabbix
   |
   | HTTPS/TLS
   | RSA-SHA256 signed request
   v
OCI Usage API
   |
   v
Usage / Cost information
```

## HTTP Proxy

The template supports HTTP proxies.

Configure:

```text
{$OCI.API.HTTP.PROXY}
```

Example:

```text
http://proxy.example.local:8080
```

If a proxy is not required, leave the macro empty.

## Items

The template provides four cost monitoring items.

| Item                    | Key                      | Unit |
| ----------------------- | ------------------------ | ---- |
| OCI Cost: Current Month | `oci.cost.current_month` | BRL  |
| OCI Cost: Today         | `oci.cost.today`         | BRL  |
| OCI Cost: Yesterday     | `oci.cost.yesterday`     | BRL  |
| OCI Cost: 2 Days Ago    | `oci.cost.2days_ago`     | BRL  |

### Collection Schedule

The items run once per hour with a 10-minute offset:

|     Offset | Item          |
| ---------: | ------------- |
| 00 minutes | Current Month |
| 10 minutes | Today         |
| 20 minutes | Yesterday     |
| 30 minutes | 2 Days Ago    |

The same sequence repeats every hour.

### Current Month

The **Current Month** item retrieves the accumulated OCI cost for the current month.

The API query uses monthly usage data and aggregates the returned values in BRL.

### Today

The **Today** item retrieves the cost for the current UTC day.

If OCI has not yet published cost data for the current day, the item returns:

```text
0 BRL
```

The previous day's cost is not used as a substitute.

The returned usage date is validated before the value is processed.

### Yesterday

The **Yesterday** item retrieves the OCI cost for the previous UTC day.

This allows the daily cost threshold to be evaluated using a complete day of usage data.

### 2 Days Ago

The **2 Days Ago** item retrieves the cost from two days before the current UTC day.

This item is provided for historical visibility and does not have a daily threshold trigger.

## Triggers

The template provides two **Warning** triggers.

### Today's Cost

The trigger expression is:

```text
last(/Oracle Cloud Cost by HTTP/oci.cost.today)>={$OCI.COST.DAILY.LIMIT}
```

### Yesterday's Cost

The trigger expression is:

```text
last(/Oracle Cloud Cost by HTTP/oci.cost.yesterday)>={$OCI.COST.DAILY.LIMIT}
```

Both triggers use:

```text
{$OCI.COST.DAILY.LIMIT}
```

as the configurable threshold.

The operational data displays the latest cost value.

## Dashboard and Graphs

The template includes a dashboard and graphs for visualizing OCI cost data.

The dashboard is designed for a Full HD resolution of:

```text
1920 × 1080
```

The visualization provides an overview of current and historical OCI costs directly in Zabbix.

## Security Considerations

Never commit real credentials to GitHub.

Do not publish:

* OCI private keys
* Passwords
* API tokens
* Credential files
* PEM files containing private keys
* PFX files containing private keys

The private key should only be configured in Zabbix as a **Secret text** macro.

### Recommended Security Practices

* Use a dedicated OCI monitoring user.
* Use a dedicated IAM group.
* Apply the principle of least privilege.
* Restrict access to the Zabbix configuration.
* Never store production credentials in the template YAML.
* Rotate API Keys according to organizational security policies.

## Compatibility

| Component      | Version / Requirement       |
| -------------- | --------------------------- |
| Zabbix         | 7.0 LTS                     |
| OCI            | Oracle Cloud Infrastructure |
| API            | OCI Usage API               |
| Authentication | OCI API Key                 |
| Signature      | RSA-SHA256                  |
| Transport      | HTTPS/TLS                   |
| Currency       | BRL                         |
| Proxy          | Optional                    |

## Using with Oracle Cloud by HTTP

This template focuses on cost monitoring.

For OCI infrastructure monitoring, use the official Zabbix template:

**[Oracle Cloud by HTTP](https://www.zabbix.com/integrations/oracle)**

The two templates can be linked to the same Zabbix host.

```text
Oracle Cloud by HTTP
        |
        +--> OCI resource monitoring

Oracle Cloud Cost by HTTP
        |
        +--> OCI cost monitoring
```

This approach separates infrastructure monitoring from financial and cost monitoring.

## Troubleshooting

### HTTP 401 — Unauthorized

Check:

* Tenancy OCID
* User OCID
* API Key fingerprint
* Private key
* API Key configuration in OCI
* Request signature

### HTTP 403 — Forbidden

Check the OCI IAM policy.

The monitoring user must have permission to read the Usage API data.

Example:

```text
Allow group ZABBIX-OCI-Monitoring to read usage-report in tenancy
```

### HTTP 404 — Not Found

Check:

* OCI Usage API hostname
* OCI region
* `{$OCI.API.USAGE.HOST}` macro
* API endpoint

Example:

```text
usageapi.sa-saopaulo-1.oci.oraclecloud.com
```

### Today's Cost Is 0

This can be expected.

OCI may not have published cost data for the current UTC day yet.

The template intentionally returns `0 BRL` instead of using the previous day's value.

### SSL/TLS Connection Errors

Verify:

* DNS resolution
* Internet connectivity
* Firewall rules
* Proxy configuration
* TLS inspection policies
* OCI endpoint availability

## License

MIT License.

See the [LICENSE](../../../../LICENSE) file for details.

## Author

**luizcarmo1**

Project:

**[oracle-cloud-cost-zabbix-template](https://github.com/luizcarmo1/oracle-cloud-cost-zabbix-template)**

## References

* [Oracle Cloud Infrastructure Documentation](https://docs.oracle.com/en-us/iaas/Content/home.htm)
* [OCI Usage API](https://docs.oracle.com/en-us/iaas/api/#/en/usage/20200107/)
* [OCI API Signing](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/signingrequests.htm)
* [OCI IAM Policies](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/policygetstarted.htm)
* [Zabbix](https://www.zabbix.com/)
* [Zabbix Community Templates](https://github.com/zabbix/community-templates)