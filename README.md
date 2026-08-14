# Morpheus phpIPAM Plugin

The Morpheus phpIPAM Plugin integrates Morpheus with phpIPAM to provide IP address management (IPAM) within Morpheus. The plugin communicates with the phpIPAM REST API to allocate and release IP addresses from phpIPAM-managed subnets and synchronise network pools.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Repository structure](#repository-structure)
- [Building the plugin](#building-the-plugin)
- [License](#license)
- [Installing](#installing)
- [Detailed Usage Steps](#detailed-usage-steps)
- [API Endpoints](#api-endpoints)

---

## Features

### IP Address Management

Allocate and release IP addresses from phpIPAM subnets within Morpheus. Supports automatic next-available IP selection from subnets, manual IP entry, existing inventory import, and configurable network filtering.

### Cloud Sync

Morpheus synchronises the following phpIPAM resources for inventory:

- Subnets as Morpheus network pools
- IP address allocations within each subnet

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Morpheus | 7.0.2 or later |
| Java | 11 or later |
| Gradle | Use the included Gradle wrapper (`./gradlew`) |

Additional prerequisites:

- A running phpIPAM instance with the REST API enabled and accessible from the Morpheus appliance
- A phpIPAM API application configured with an **App ID** and read/write permissions
- A phpIPAM user account with access to the subnets managed by Morpheus
- Network access from the Morpheus appliance to the phpIPAM host on the configured port

---

## Repository structure

```
src/main/groovy/com/morpheusdata/phpipam/
├── PhpIpamPlugin.groovy    - Plugin entry point; registers PhpIpamProvider
└── PhpIpamProvider.groovy  - IPAMProvider implementation; IPAM operations, sync, OptionTypes, API client
build.gradle, gradle.properties - Build configuration and plugin metadata
```

---

## Building the plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew clean build
```

The packaged jar will be written to `build/libs/`.

To execute tests, use the following command:

```bash
./gradlew test
```

---

## License

This project is licensed under the Apache License 2.0.

See the [LICENSE](LICENSE) file for details.

---

## Installing

1. Build the plugin (see [Building the plugin](#building-the-plugin)) or download a released jar.
2. In Morpheus, navigate to **Administration > Integrations > Plugins**.
3. Click **Add** and upload the `morpheus-phpipam-plugin-<version>.jar` from `build/libs/`.
4. Navigate to **Infrastructure > Networks > IP Pools > Add** and select **phpIPAM** to configure the integration.

---

## Detailed Usage Steps

### Adding a phpIPAM IPAM Integration

1. Go to **Infrastructure > Networks > IP Pools > Add**.
2. Select **phpIPAM** as the pool server type.
3. Enter:
   - **API Url** — phpIPAM base URL (e.g. `https://phpipam.example.com/`)
   - **App ID** — the phpIPAM API application ID configured in phpIPAM
   - **Username** and **Password** (or a stored credential)
4. Optionally configure **Throttle Rate**, **Disable SSL SNI Verification**, **Inventory Existing**, and **Network Filter**.
5. Save. Morpheus authenticates against phpIPAM and syncs available subnets as network pools.

### Allocating an IP Address

When provisioning an instance on a network backed by a phpIPAM subnet, Morpheus calls the phpIPAM API to allocate the next available IP from the subnet. The IP is registered in phpIPAM with the instance details.

### Releasing an IP Address

When an instance is decommissioned, Morpheus calls the phpIPAM API to delete the IP address record from the subnet.

---

## API Endpoints

This plugin communicates with the **phpIPAM REST API** at `{serviceUrl}/api/{appId}/`. Authentication uses a session token obtained by posting credentials to the user controller. All calls include `?app_id={appId}` as a query parameter.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/{appId}/user/` | POST | Authenticate and obtain a session token |
| `/api/{appId}/subnets/` | GET | List subnets |
| `/api/{appId}/subnets/{id}/addresses/` | GET | List IP addresses in a subnet |
| `/api/{appId}/subnets/{id}/first_free/` | GET | Get next available IP in a subnet |
| `/api/{appId}/addresses/` | POST | Create (allocate) an IP address record |
| `/api/{appId}/addresses/{id}/` | PUT | Update an IP address record |
| `/api/{appId}/addresses/{id}/` | DELETE | Delete (release) an IP address record |
