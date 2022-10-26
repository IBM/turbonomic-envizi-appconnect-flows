# TurbonomicAccounts

This flow is designed to run every day to pull the data from Turbonomic APIs and send the data to Envizi's S3 bucket in a CSV file.

The ideal time to execute this flow is at UTC midnight between 00:05 and 00:30

## Overview

![Turbonomic-Envizi Accounts integration](https://media.github.ibm.com/user/375131/files/2a218e80-4edb-11ed-8ec3-aba07922fd4c)

## Turbonomic APIs used:

 - `POST /api/v3/login`
 - `GET /api/v3/search?types=DataCenter`
 - `GET /api/v3/supplychains?environment_type=ONPREM&uuids=<DataCenter UUID>`
 - `POST /api/v3/entities/<DataCenter UUID>/stats`


## Mapping

| CSV Headers   | Turbonomic API Field                                                                                                                | Comments                           |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| ACCOUNT       | `displayName` + ("Active Hosts" or "Energy Consumption" or "Energy Host Intensity" or "Active VMs" or "VM Host Density")              |                                    |
| DATATYPE      | "Active Hosts" or "Energy Consumption" or "Energy Host Intensity" or "Active VMs" or "VM Host Density"                                |                                    |
| LOCATION      | `displayName`                                                                                                                       | Data Center API                    |
| LOCATIONID    | `uuid`                                                                                                                              | Data Center API                    |
| MEASUREUNITID | "number" or "kWh" or "VM/host" or "kWh/host"                                                                                        |                                    |
| METERNAME     | "Active Hosts" or "Energy Consumption" or "Energy Host Intensity" or "Active VMs" or "VM Host Density"                                |                                    |
| QUANTITY      | Calculated using `seMap.PhysicalMachine.stateSummary.ACTIVE`, `seMap.VirtualMachine.stateSummary.ACTIVE`, `statistics.values.total` | Supply Chain and Entity Stats APIs |
| READINGDATE   | Start of day timestamp (UTC)                                                                                                        |                                    |