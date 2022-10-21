# TurbonomicLocations

This flow pulls the list of Cloud Regions and On-prem Data Centers from Turbonomic APIs and sends the data to Envizi's S3 bucket in a CSV file

This flow can be set to execute at the start of the month or manually executed (Start and Stop after first execution) when there is a new Region or Data Center.

## Overview

![Turbonomic-Envizi Locations integration](https://media.github.ibm.com/user/375131/files/589f6980-4edb-11ed-8fdf-24c278afd4ea)


## Turbonomic APIs used:

- `POST /api/v3/login`
- `GET /api/v3/search?detail_type=aspects&types=Region`
- `GET /api/v3/search?types=DataCenter`

## Mapping

| CSV Headers | Turbonomic API Field                                                 | Comments                                       |
|-------------|----------------------------------------------------------------------|------------------------------------------------|
| LOCATION    | `displayName`                                                        |                                                |
| LOCATIONID  | `uuid`                                                               |                                                |
| DESCRIPTION | `environmentType + "-" + className`                                  | "CLOUD-Region" or "ONPREM-DataCenter"          |
| LATITUDEY   | Cloud: `aspects.regionAspect.latitude` On-prem: `tags.Latitude[0]`   | Tags from vCenter need to be added for On-prem |
| LONGITUDEX  | Cloud: `aspects.regionAspect.longitude` On-prem: `tags.Longitude[0]` | Tags from vCenter need to be added for On-prem |
| COUNTRY     | `tags.Country[0]`                                                    | Only available for On-prem                     |
