# Morpheus {php}IPAM Plugin

This plugin provides IP address management integration between [{php}IPAM](https://phpipam.net/) and [Morpheus](https://morpheusdata.com). It enables network pool sync, IPv4 and IPv6 pool types, optional existing IP inventory, and host record allocation from within the Morpheus platform.

## Requirements

| Component | Minimum Version |
|-----------|----------------|
| Morpheus | 7.0.2 |

## Installation

1. Download the latest `.jar` from the [Releases](https://github.com/gomorpheus/morpheus-phpipam-plugin/releases) page, or [build it yourself](#building).
2. In Morpheus, navigate to **Administration → Integrations → Plugins**.
3. Click **Browse** and upload the `.jar` file.
4. The **phpIPAM** IPAM integration will appear after the plugin loads.

## Configuration

When adding a phpIPAM IPAM integration in Morpheus (**Infrastructure → Network → IPAM Integrations**), provide the following:

| Field | Description |
|-------|-------------|
| **API Url** | phpIPAM API endpoint URL. |
| **App ID** | phpIPAM API application identifier. |
| **Credentials** | Select whether to use local username/password values or a stored Morpheus username/password credential. |
| **Username** | phpIPAM API username when using local credentials. |
| **Password** | phpIPAM API password when using local credentials. |
| **Throttle Rate** | Optional request throttle rate for phpIPAM API calls. |
| **Disable SSL SNI Verification** | Disable SSL SNI verification for the phpIPAM endpoint. |
| **Inventory Existing** | Sync existing phpIPAM address records into Morpheus network pool IP inventory. |
| **Network Filter** | Optional filter applied when syncing phpIPAM networks. |

## Features

### IPAM Integration

The plugin registers an `IPAMProvider` for phpIPAM. The following resources are discovered and kept in sync from phpIPAM:

- **Network Pools** — phpIPAM subnets are synchronized into Morpheus network pools.
- **IPv4 and IPv6 Pool Types** — separate phpIPAM and phpIPAM IPv6 pool types are registered.
- **Pool Ranges** — CIDR and range information is mapped onto Morpheus network pool ranges.
- **Existing IP Inventory** — when enabled, existing phpIPAM address records are synchronized into Morpheus pool IP records.

### Host Record Management

Morpheus can allocate and release addresses through phpIPAM during provisioning workflows. Supported operations include:

- Create a requested IP address in a phpIPAM subnet.
- Allocate the next free IP address from a phpIPAM subnet.
- Update hostnames on phpIPAM address records.
- Delete phpIPAM address records when releasing addresses.

## Building

```bash
./gradlew shadowJar
```

The plugin JAR will be written to `build/libs/`.

## License

Copyright 2022 Morpheus Data, LLC. Licensed under the [Apache License, Version 2.0](LICENSE).
