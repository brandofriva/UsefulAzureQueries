# UsefulAzureQueries
Useful Azure Queries

Newly created or deleted resources in Azure:
(Resource Graph query)
```
resourcechanges
| extend changeTime = todatetime(properties.changeAttributes.timestamp), targetResourceId = tostring(properties.targetResourceId),
changeType = tostring(properties.changeType), correlationId = properties.changeAttributes.correlationId, 
changedProperties = properties.changes, changeCount = properties.changeAttributes.changesCount, targetResourceType = properties.targetResourceType
//| where changeTime > ago(1d)
| where (changeType == "Delete" or changeType == "Create")
| order by changeTime desc
| join kind=leftouter (resourcecontainers | where type=='microsoft.resources/subscriptions' | project SubscriptionName=name, subscriptionId) on subscriptionId
| join kind=leftouter (resources | extend targetResourceId=id | project resourcename=name, resourcesku=sku, resourcekind=kind, targetResourceId=id, resourcesku2=properties.sku.name) on targetResourceId
| project changeTime, subscriptionId, SubscriptionName, resourceGroup, changeType, targetResourceId, resourcename, resourcesku, resourcesku2, resourcekind
```
