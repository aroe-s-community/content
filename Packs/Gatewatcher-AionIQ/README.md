Gatewatcher is a European leader in advanced Threats detection, protecting critical networks of large Entreprises and Governement organisations since 2015. We release a new detection and response platform (NDR) called Aioniq that enables to identify with certainty malicious actions and suspicious behaviors based on a mapping of all assets present on the information system.

**The Aioniq platform consists of two appliances:**

- A central appliance, called **GCenter**, which receives the information sent by the probes to detect and analyze threats,
- One or more probes, called **GCap**, which listen to the traffic on which they are placed.

> For more information, check out our documentation https://docs.gatewatcher.com/en/gcenter/2.5.3/101/index.html.

## What does this pack do?

**This pack includes 1 integration:**

- GCenter integration:
  - List Elasticsearch (NoSQL database) alerts.
  - Get an Elasticsearch (NoSQL database) alert by uid.
  - Add a malcore (Malwares analysis) whitelist/blacklist entry.
  - Delete a malcore (Malwares analysis) whitelist/blacklist entry.
  - Add a DGA (Domain generation algorithms) whitelist/blacklist entry.
  - Delete a DGA (Domain generation algorithms)  whitelist/blacklist entry.
  - Get results of an Elasticsearch (NoSQL database) query.
  - Add an ignore list asset name.
  - Add an ignore list Kerberos IP.
  - Add an ignore list Kerberos username.
  - Add an ignore list MAC address.
  - Delete an ignore list asset name.
  - Delete an ignore list Kerberos IP.
  - Delete an ignore list Kerberos username.
  - Delete an ignore list MAC address.
  - Send file to the GScan malcore analysis (Malwares analysis).
  - Send file to the GScan shellcode analysis.
  - Send file to the GScan powershell analysis.

## IOCs requests examples

This section will give somme examples on how you can retreive some IOCs (Indicator of compromise) from the GCenter appliance in version 2.5.3.102.

**The GCenter uses 4 engines to detect threats:**

- **Malcore:** Detection of malware through a static and heuristic multi-engine analysis of files in real time.
- **Sigflow:** Generates alerts, metadata, and content based on rules.
- **Codebreaker:** Detection of shellcode and powershell exploitation.
- **Dga:** Detection of domain names generated by DGAs (Domain Generation Algorithm).

**For the first three engines, we will be able to extract theses IOCs (Non exhaustive):**

- **Malcore:**
  - IP address of source and/or destination.
  - Sha256 of files.
- **Sigflow:**
  - IP address of source and/or destination.
  - HTTP hostanmes and urls.
  - DNS domain names queried.
  - TLS SNI (Service Name Identifier).
- **Codebreaker:**
  - IP address of source and/or destination.
  - Sha256 of files.

> For more information on the documents produced by the malcore engine, check out our documentation https://docs.gatewatcher.com/en/gcenter/2.5.3/101/malcore.html#generated-events.

> For more information on the documents produced by the sigflow engine, check out our documentation https://docs.gatewatcher.com/en/gcenter/2.5.3/101/sigflow.html#generated-events.

> For more information on the documents produced by the codebreaker engine, check out our documentation https://docs.gatewatcher.com/en/gcenter/2.5.3/101/codebreaker.html#generated-events.

> All the use cases below use the gw-es-query command of the GCenterv102 integration.

### Malcore engine

#### Use case 1: List of source or destination IP addresses by number of occurrences

- **IOC:**
  - The top 100 source or destination IP addresses.
  - Source IP is used in the example below.
- **Filters:**
  - malware event type.
  - Infected file only.
- **Period:**
  - Last 1h.

**Arguments to be passed to the gw-es-query command:**

- **index:** malware
- **query:**

```json
{
    "size": 0,
    "query": {
        "bool": {
            "filter": [
                {
                    "range": {
                        "@timestamp": {
                            "gte": "now-1h"
                        }
                    }
                },
                {
                    "term": {
                        "state": "Infected"
                    }
                },
                {
                    "term": {
                        "event_type": "malware"
                    }
                }
            ]
        }
    },
    "aggs": {
        "src_ip": {
            "terms": {
                "field": "src_ip",
                "size": 100
            }
        }
    }
}
```

#### Use case 2: List of SHA256 by number of occurrences

- **IOC:**
  - The top 100 SHA256 files.
- **Filters:**
  - malware event type.
  - Infected file only.
- **Period:**
  - Last 1h.

**Arguments to be passed to the gw-es-query command:**

- **index:** malware
- **query:**

```json
{
    "size": 0,
    "query": {
        "bool": {
            "filter": [
                {
                    "range": {
                        "@timestamp": {
                            "gte": "now-1h"
                        }
                    }
                },
                {
                    "term": {
                        "state": "Infected"
                    }
                },
                {
                    "term": {
                        "event_type": "malware"
                    }
                }
            ]
        }
    },
    "aggs": {
        "sha256": {
            "terms": {
                "field": "SHA256",
                "size": 100
            }
        }
    }
}
```


### Sigflow engine

#### Use case 1: List of source or destination IP addresses by number of occurrences

- **IOC:**
  - The top 100 source or destination IP addresses.
  - Source IP is used in the example below.
- **Filters:**
  - alert event type.
  - alert severity (1 the highest and 4 the lowest). A severity of 1 is used in the example below.
- **Period:**
  - Last 1h.

**Arguments to be passed to the gw-es-query command:**

- **index:** suricata
- **query:**

```json
{
    "size": 0,
    "query": {
        "bool": {
            "filter": [
                {
                    "range": {
                        "@timestamp": {
                            "gte": "now-1h"
                        }
                    }
                },
                {
                    "term": {
                        "alert.severity": "1"
                    }
                },
                {
                    "term": {
                        "event_type": "alert"
                    }
                }
            ]
        }
    },
    "aggs": {
        "src_ip": {
            "terms": {
                "field": "src_ip",
                "size": 100
            }
        }
    }
}
```

#### Use case 2: List of HTTP hostnames by number of occurrences

- **IOC:**
  - The top 100 HTTP hostnames.
- **Filters:**
  - alert event type.
  - alert severity (1 the highest and 4 the lowest). A severity of 1 is used in the example below.
  - HTTP application protocol.
- **Period:**
  - Last 1h.

**Arguments to be passed to the gw-es-query command:**

- **index:** suricata
- **query:**

```json
{
    "size": 0,
    "query": {
        "bool": {
            "filter": [
                {
                    "range": {
                        "@timestamp": {
                            "gte": "now-1h"
                        }
                    }
                },
                {
                    "term": {
                        "alert.severity": "1"
                    }
                },
                {
                    "term": {
                        "event_type": "alert"
                    }
                },
                {
                    "term": {
                        "app_proto": "http"
                    }
                }
            ]
        }
    },
    "aggs": {
        "hostname": {
            "terms": {
                "field": "http.hostname",
                "size": 100
            }
        }
    }
}
```

### Codebreaker engine

#### Use case 1: List of source or destination IP addresses by number of occurrences

- **IOC:**
  - The top 100 source or destination IP addresses.
  - Source IP is used in the example below.
- **Filters:**
  - powershell or shellcode event type. powershell is used in the example below.
  - Infected file only.
- **Period:**
  - Last 1h.

**Arguments to be passed to the gw-es-query command:**

- **index:** codebreaker
- **query:**

```json
{
    "size": 0,
    "query": {
        "bool": {
            "filter": [
                {
                    "range": {
                        "@timestamp": {
                            "gte": "now-1h"
                        }
                    }
                },
                {
                    "term": {
                        "state": "Exploit"
                    }
                },
                {
                    "term": {
                        "event_type": "powershell"
                    }
                }
            ]
        }
    },
    "aggs": {
        "src_ip": {
            "terms": {
                "field": "src_ip",
                "size": 100
            }
        }
    }
}
```

#### Use case 2: List of SHA256 by number of occurrences

- **IOC:**
  - The top 100 SHA256 files.
- **Filters:**
  - powershell or shellcode event type. powershell is used in the example below.
  - Infected file only.
- **Period:**
  - Last 1h.

**Arguments to be passed to the gw-es-query command:**

- **index:** codebreaker
- **query:**

```json
{
    "size": 0,
    "query": {
        "bool": {
            "filter": [
                {
                    "range": {
                        "@timestamp": {
                            "gte": "now-1h"
                        }
                    }
                },
                {
                    "term": {
                        "state": "Exploit"
                    }
                },
                {
                    "term": {
                        "event_type": "powershell"
                    }
                }
            ]
        }
    },
    "aggs": {
        "sha256": {
            "terms": {
                "field": "SHA256",
                "size": 100
            }
        }
    }
}
```