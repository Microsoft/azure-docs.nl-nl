---
title: 'Sjabloon functies: bronnen'
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon (ARM-sjabloon) om waarden over resources op te halen.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: efd7c554e6da8b60d4834d1a1290407a6b9e94d4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544108"
---
# <a name="resource-functions-for-arm-templates"></a>Resource functies voor ARM-sjablonen

Resource Manager biedt de volgende functies voor het ophalen van resource waarden in uw Azure Resource Manager-sjabloon (ARM-sjabloon):

* [extensionResourceId](#extensionresourceid)
* [orderverzamellijst](#list)
* [pickZones](#pickzones)
* [hardwareproviders](#providers)
* [referentielaag](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [abonnees](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Zie [implementatie waarde Functions](template-functions-deployment.md)om waarden op te halen uit para meters, variabelen of de huidige implementatie.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Retourneert de resource-ID voor een [extensie resource](../management/extension-resource-types.md), een resource type dat wordt toegepast op een andere resource om aan de mogelijkheden ervan toe te voegen.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceId |Yes |tekenreeks |De resource-ID voor de resource waarop de uitbreidings resource wordt toegepast. |
| resourceType |Yes |tekenreeks |Het type resource, inclusief de naam ruimte van de resource provider. |
| resourceName1 |Yes |tekenreeks |De naam van de resource. |
| resourceName2 |No |tekenreeks |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De basis indeling van de resource-ID die wordt geretourneerd door deze functie is:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Het bereik segment varieert van de resource die wordt uitgebreid.

Wanneer de extensie resource wordt toegepast op een **resource**, wordt de resource-id geretourneerd met de volgende indeling:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **resource groep**, is de notatie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **abonnement**, is de indeling:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **beheer groep**, is de indeling:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId-voor beeld

In het volgende voor beeld wordt de resource-ID voor een resource groeps vergrendeling geretourneerd.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "lockName": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "lockResourceId": {
      "type": "string",
      "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param lockName string

output lockResourceId string = extensionResourceId(resourceGroup().Id, 'Microsoft.Authorization/locks', lockName)
```

---

Een aangepaste beleids definitie die is geïmplementeerd in een beheer groep, wordt geïmplementeerd als een uitbreidings resource. Als u een beleid wilt maken en toewijzen, implementeert u de volgende sjabloon in een beheer groep.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param targetMG string
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

var mgScope = tenantResourceId('Microsoft.Management/managementGroups', targetMG)
var policyDefinition = 'LocationRestriction'

resource myDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: policyDefinition
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      'if': {
        'not': {
          'field': 'location'
          'in': allowedLocations
        }
      }
      'then': {
        'effect': 'deny'
      }
    }
  }
}

resource myAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'location-lock'
  properties: {
    scope: mgScope
    policyDefinitionId: extensionResourceId(mgScope, 'Microsoft.Authorization/policyDefinitions', policyDefinition)
  }
}
```

---

Ingebouwde beleids definities zijn resources op Tenant niveau. Zie [tenantResourceId](#tenantresourceid)voor een voor beeld van de implementatie van een ingebouwde beleids definitie.

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>orderverzamellijst

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

De syntaxis voor deze functie is afhankelijk van de naam van de lijst bewerkingen. Elke implementatie retourneert waarden voor het resource type dat een lijst bewerking ondersteunt. De naam van de bewerking moet beginnen met `list` en kan een achtervoegsel hebben. Enkele veelvoorkomende gebruiks handelingen zijn `list` , `listKeys` , en `listKeyValue` `listSecrets` .

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| ResourceName of resourceIdentifier |Yes |tekenreeks |De unieke id voor de resource. |
| apiVersion |Yes |tekenreeks |API-versie van de runtime status van de resource. Doorgaans in de notatie **jjjj-mm-dd**. |
| functionValues |No |object | Een object met waarden voor de functie. Geef dit object alleen op voor functies die ondersteuning bieden voor het ontvangen van een object met parameter waarden, zoals **listAccountSas** op een opslag account. In dit artikel wordt een voor beeld gegeven van het door geven van functie waarden. |

### <a name="valid-uses"></a>Geldige toepassingen

De lijst functies kunnen worden gebruikt in de eigenschappen van een resource definitie. Gebruik geen lijst functie die gevoelige informatie beschikbaar maakt in het gedeelte outputs van een sjabloon. Uitvoer waarden worden opgeslagen in de implementatie geschiedenis en kunnen worden opgehaald door een kwaadwillende gebruiker.

In combi natie met [eigenschaps herhaling](copy-properties.md)kunt u de functies lijst gebruiken voor `input` omdat de expressie wordt toegewezen aan de eigenschap resource. U kunt deze niet gebruiken `count` omdat het aantal moet worden bepaald voordat de lijst functie wordt opgelost.

### <a name="implementations"></a>Implementaties

Het mogelijke gebruik van lijst * wordt weer gegeven in de volgende tabel.

| Resourcetype | Functienaam |
| ------------- | ------------- |
| Micro soft. Addons/supportProviders | listsupportplaninfo |
| Micro soft. AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Micro soft. ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Micro soft. ApiManagement/service/gateways | [Listkeys ophalen](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Micro soft. ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Micro soft. ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Micro soft. ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Micro soft. ApiManagement/service/abonnementen | [listSecrets](/rest/api/apimanagement/2019-12-01/subscription/listsecrets) |
| Micro soft. AppConfiguration/configurationStores | [Listkeys ophalen](/rest/api/appconfiguration/configurationstores/listkeys) |
| Micro soft. AppPlatform/lente | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Micro soft. Automation/automationAccounts | [Listkeys ophalen](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Bat-CH/batchAccounts | [listkeys ophalen](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/werk ruimten/experimenten/taken | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Micro soft. Block Chain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Micro soft. BotService/botServices/channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Micro soft. cache/redis | [Listkeys ophalen](/rest/api/redis/redis/listkeys) |
| Micro soft. CognitiveServices/accounts | [Listkeys ophalen](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Micro soft. ContainerRegistry/registers | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Micro soft. ContainerRegistry/registers | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Micro soft. ContainerRegistry/registers | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Micro soft. ContainerRegistry/registers/agentpools | listQueueStatus |
| Micro soft. ContainerRegistry/registers/buildTasks | listSourceRepositoryProperties |
| Micro soft. ContainerRegistry/registers/buildTasks/Steps | listBuildArguments |
| Micro soft. ContainerRegistry/registers/taskruns | listDetails |
| Micro soft. ContainerRegistry/registers/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Micro soft. ContainerRegistry/registers/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Micro soft. ContainerRegistry/registers/taken | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Micro soft. container service/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Micro soft. container service/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Micro soft. container service/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Micro soft. container service/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Micro soft. DataBox/Jobs | listCredentials |
| Micro soft. DataFactory/datafactories/gateways | listauthkeys |
| Micro soft. DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Micro soft. DataLakeAnalytics/accounts/Storage accounts/containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Micro soft. DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Micro soft. DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Micro soft. DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Micro soft. devices/iotHubs | [listkeys ophalen](/rest/api/iothub/iothubresource/listkeys) |
| Micro soft. devices/iotHubs/iotHubKeys | [listkeys ophalen](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Micro soft. apparaten/provisioningServices/sleutels | [listkeys ophalen](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Micro soft. devices/provisioningServices | [listkeys ophalen](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Micro soft. DevTestLab/Labs/planningen | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Micro soft. DevTestLab/Labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Micro soft. DevTestLab/Labs/informatie | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [Listkeys ophalen](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/notebookworkspaces/listconnectioninfo) |
| Micro soft. DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Micro soft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Micro soft. EventGrid/domeinen | [Listkeys ophalen](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Micro soft. EventGrid/topics | [Listkeys ophalen](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Micro soft. EventHub/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/eventhub) |
| Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/authorizationRules | [listkeys ophalen](/rest/api/eventhub) |
| Micro soft. EventHub/naam ruimten/Event hubs/authorizationRules | [listkeys ophalen](/rest/api/eventhub) |
| Micro soft. ImportExport/Jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Micro soft. Kusto/clusters/data bases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Micro soft. LabServices/gebruikers | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Micro soft. LabServices/gebruikers | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Micro soft. Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Micro soft. Logic/integrationAccounts/assembly's | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Micro soft. Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Micro soft. Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Micro soft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Micro soft. Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Micro soft. Logic/integrationAccounts/-schema's | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Micro soft. Logic/werk stromen/uitvoeringen/acties | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Micro soft. Logic/werk stromen/uitvoeringen/acties/herhalingen | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Micro soft. Logic/werk stromen/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Micro soft. Logic/werk stromen/versies/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Micro soft. MachineLearning/webservices | [listkeys ophalen](/rest/api/machinelearning/webservices/listkeys) |
| Micro soft. MachineLearning/werk ruimten | listworkspacekeys |
| Micro soft. MachineLearningServices/werk ruimten/reken kracht | [Listkeys ophalen](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Micro soft. MachineLearningServices/werk ruimten/reken kracht | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Micro soft. MachineLearningServices/werk ruimten | [Listkeys ophalen](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Micro soft. Maps/accounts | [Listkeys ophalen](/rest/api/maps-management/accounts/listkeys) |
| Micro soft. Media/Media Services/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Micro soft. Media/Media Services/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Micro soft. Media/Media Services/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Micro soft. Media/Media Services/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Micro soft. Network/applicationSecurityGroups | listIpConfigurations |
| Micro soft. notification hubs/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/notificationhubs/namespaces/listkeys) |
| Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules | [listkeys ophalen](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | Listkeys ophalen |
| Micro soft. PolicyInsights/herstel bewerkingen | [listDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Micro soft. RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Micro soft. relay/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/relay/namespaces/listkeys) |
| Micro soft. relay/naam ruimten/disasterRecoveryConfigs/authorizationRules | listkeys ophalen |
| Micro soft. relay/naam ruimten/HybridConnections/authorizationRules | [listkeys ophalen](/rest/api/relay/hybridconnections/listkeys) |
| Micro soft. relay/naam ruimten/WcfRelays/authorizationRules | [listkeys ophalen](/rest/api/relay/wcfrelays/listkeys) |
| Micro soft. Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Micro soft. Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Micro soft. ServiceBus/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/authorizationRules | [listkeys ophalen](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Micro soft. ServiceBus/naam ruimten/wacht rijen/authorizationRules | [listkeys ophalen](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Micro soft. ServiceBus/naam ruimten/onderwerpen/authorizationRules | [listkeys ophalen](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys ophalen](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys ophalen](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Micro soft. StorSimple/managers/apparaten | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Micro soft. StorSimple/managers/apparaten | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Micro soft. StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Micro soft. StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Micro soft. Synapse/werk ruimten/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Micro soft. Web/connectionGateways | ListStatus |
| micro soft. Web/verbindingen | listconsentlinks |
| Micro soft. Web/customApis | listWsdlInterfaces |
| micro soft. Web/locaties | listwsdlinterfaces |
| micro soft. Web/apimanagementaccounts/api's/Connections | listconnectionkeys |
| micro soft. Web/apimanagementaccounts/api's/Connections | listsecrets |
| micro soft. web/sites/back-ups | [list](/rest/api/appservice/webapps/listbackups) |
| Micro soft. web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| micro soft. web/sites/functies | [listkeys ophalen](/rest/api/appservice/webapps/listfunctionkeys)
| micro soft. web/sites/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| micro soft. web/sites/hybridconnectionnamespaces/relays | [listkeys ophalen](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| micro soft. web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| micro soft. web/sites/sleuven/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| micro soft. web/sites/sleuven/back-ups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Micro soft. web/sites/sleuven/configuratie | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| micro soft. web/sites/sleuven/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Als u wilt bepalen welke resource typen een lijst bewerking hebben, hebt u de volgende opties:

* Bekijk de [rest API bewerkingen](/rest/api/) voor een resource provider en zoek naar lijst bewerkingen. Opslag accounts hebben bijvoorbeeld de Listkeys ophalen- [bewerking](/rest/api/storagerp/storageaccounts).
* Gebruik de Power shell [-cmdlet Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . In het volgende voor beeld worden alle lijst bewerkingen voor opslag accounts opgehaald:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* Gebruik de volgende Azure CLI-opdracht om alleen de bewerkingen van de lijst te filteren:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object is afhankelijk van de functie van de lijst die u gebruikt. De Listkeys ophalen voor een opslag account retourneert bijvoorbeeld de volgende indeling:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Andere lijst functies hebben verschillende retour indelingen. Als u de indeling van een functie wilt zien, neemt u deze op in de sectie outputs, zoals wordt weer gegeven in de voorbeeld sjabloon.

### <a name="remarks"></a>Opmerkingen

Geef de resource op met behulp van de resource naam of de [functie resourceId](#resourceid). Wanneer u een lijst functie gebruikt in dezelfde sjabloon die de resource van de verwijzing implementeert, gebruikt u de resource naam.

Als u een **lijst** functie gebruikt in een resource die voorwaardelijk wordt geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd. Er wordt een fout bericht weer **geven** als de functie List verwijst naar een resource die niet bestaat. Gebruik de functie **als** om te controleren of de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en-lijst met een voorwaardelijke geïmplementeerde resource.

### <a name="list-example"></a>Voor beeld van lijst

In het volgende voor beeld wordt Listkeys ophalen gebruikt bij het instellen van een waarde voor [implementatie scripts](deployment-script-template.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"storageAccountSettings": {
  "storageAccountName": "[variables('storageAccountName')]",
  "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
storageAccountSettings: {
  storageAccountName: storageAccountName
  storageAccountKey: listKeys(resourceId('Microsoft.Storage/storageAccounts', storageAccountName), '2019-06-01').keys[0].value
}

```

---

In het volgende voor beeld ziet u een lijst functie waarvoor een para meter wordt gebruikt. In dit geval is de functie **listAccountSas**. Geef een object door voor de verloop tijd. De verloop tijd moet in de toekomst liggen.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "accountSasProperties": {
    "type": "object",
    "defaultValue": {
      "signedServices": "b",
      "signedPermission": "r",
      "signedExpiry": "2020-08-20T11:00:00Z",
      "signedResourceTypes": "s"
    }
  }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: listAccountSas(storagename, '2018-02-01', accountSasProperties).accountSasToken
```

---

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Hiermee wordt bepaald of een resource type zones voor een regio ondersteunt.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace | Yes | tekenreeks | De naam ruimte van de resource provider voor het bron type om te controleren op zone-ondersteuning. |
| resourceType | Yes | tekenreeks | Het bron type om te controleren op zone-ondersteuning. |
| location | Yes | tekenreeks | De regio die moet worden gecontroleerd op zone ondersteuning. |
| numberOfZones | No | geheel getal | Het aantal logische zones dat moet worden geretourneerd. De standaardwaarde is 1. Het getal moet een positief geheel getal tussen 1 en 3 zijn.  Gebruik 1 voor bronnen met één zone. Voor resources met meerdere zones moet de waarde kleiner zijn dan of gelijk zijn aan het aantal ondersteunde zones. |
| offset | No | geheel getal | De offset van de eerste logische zone. De functie retourneert een fout als offset plus numberOfZones het aantal ondersteunde zones overschrijdt. |

### <a name="return-value"></a>Retourwaarde

Een matrix met de ondersteunde zones. Wanneer u de standaard waarden voor offset en numberOfZones gebruikt, retourneert een resource type en regio die zones ondersteunt de volgende matrix:

```json
[
    "1"
]
```

Wanneer de `numberOfZones` para meter is ingesteld op 3, wordt het volgende geretourneerd:

```json
[
    "1",
    "2",
    "3"
]
```

Wanneer het resource type of de regio geen ondersteuning biedt voor zones, wordt een lege matrix geretourneerd.

```json
[
]
```

### <a name="pickzones-example"></a>pickZones-voor beeld

De volgende sjabloon toont drie resultaten voor het gebruik van de functie pickZones.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "supported": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')]"
    },
    "notSupportedRegion": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')]"
    },
    "notSupportedType": {
      "type": "array",
      "value": "[pickZones('Microsoft.Cdn', 'profiles', 'westus2')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

---

De uitvoer van de voor gaande voor beelden retourneert drie matrices.

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| Ondersteund | matrix | ["1"] |
| notSupportedRegion | matrix | [] |
| notSupportedType | matrix | [] |

U kunt de reactie van pickZones gebruiken om te bepalen of u null moet opgeven voor zones of virtuele machines aan verschillende zones wilt toewijzen. In het volgende voor beeld wordt een waarde ingesteld voor de zone op basis van de beschik baarheid van zones.

# <a name="json"></a>[JSON](#tab/json)

```json
"zones": {
  "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Lussen en functie copyindex () zijn nog niet geïmplementeerd.  Zie [lussen](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

Retourneert informatie over een resource provider en de ondersteunde resource typen. Als u geen resource type opgeeft, retourneert de functie alle ondersteunde typen voor de resource provider.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace |Yes |tekenreeks |Naam ruimte van de provider |
| resourceType |No |tekenreeks |Het type resource binnen de opgegeven naam ruimte. |

### <a name="return-value"></a>Retourwaarde

Elk ondersteund type wordt in de volgende indeling geretourneerd:

```json
{
  "resourceType": "{name of resource type}",
  "locations": [ all supported locations ],
  "apiVersions": [ all supported API versions ]
}
```

Matrix volgorde van de geretourneerde waarden is niet gegarandeerd.

### <a name="providers-example"></a>Voor beeld van providers

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) ziet u hoe u de functie provider gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "providerNamespace": {
      "type": "string"
    },
    "resourceType": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "providerOutput": {
      "type": "object",
      "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param providerNamespace string
param resourceType string

output providerOutput array = providers(providerNamespace, resourceType)
```

---

Voor het resource type **micro soft. Web** resource provider en **sites** retourneert het voor gaande voor beeld een object in de volgende indeling:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>referentielaag

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Retourneert een-object dat de runtime status van een resource aangeeft.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| ResourceName of resourceIdentifier |Yes |tekenreeks |De naam of de unieke id van een resource. Als u naar een resource in de huidige sjabloon verwijst, geeft u alleen de resource naam op als para meter. Als er wordt verwezen naar een eerder geïmplementeerde resource of als de naam van de bron ambigu is, geeft u de resource-ID op. |
| apiVersion |No |tekenreeks |API-versie van de opgegeven resource. **Deze para meter is vereist als de resource niet binnen dezelfde sjabloon is ingericht.** Doorgaans in de notatie **jjjj-mm-dd**. Zie voor een geldige API-versie voor uw resource [sjabloon verwijzing](/azure/templates/). |
| Waard |No |tekenreeks |Waarde die aangeeft of het volledige Resource-object moet worden geretourneerd. Als u niet opgeeft `'Full'` , wordt alleen het eigenschappen object van de resource geretourneerd. Het volledige object bevat waarden zoals de resource-ID en de locatie. |

### <a name="return-value"></a>Retourwaarde

Elk resource type retourneert verschillende eigenschappen voor de verwijzings functie. De functie retourneert geen enkele vooraf gedefinieerde notatie. De geretourneerde waarde verschilt ook op basis van de waarde van het `'Full'` argument. Als u de eigenschappen voor een resource type wilt zien, retourneert u het object in de sectie outputs, zoals wordt weer gegeven in het voor beeld.

### <a name="remarks"></a>Opmerkingen

De functie Reference haalt de runtime status op van een eerder geïmplementeerde resource of een resource die in de huidige sjabloon is geïmplementeerd. In dit artikel vindt u voor beelden van beide scenario's.

Normaal gesp roken gebruikt u de functie **Reference** om een bepaalde waarde van een object te retour neren, zoals de URI van het BLOB-eind punt of Fully Qualified Domain name.

# <a name="json"></a>[JSON](#tab/json)

```json
"outputs": {
  "BlobUri": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]"
  },
  "FQDN": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output BlobUri string = reference(resourceId('Microsoft.Storage/storageAccounts', storageAccountName)).primaryEndpoints.blob
output FQDN string = reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName)).dnsSettings.fqdn
```

---

Gebruiken `'Full'` Wanneer u resource waarden nodig hebt die geen deel uitmaken van het eigenschappen schema. Als u bijvoorbeeld een sleutel kluis toegangs beleid wilt instellen, haalt u de identiteits eigenschappen voor een virtuele machine op.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "vaultName",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource myVault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'vaultName'
  properties: {
    tenantId: subscription().tenantId
    accessPolicies: [
      {
        'tenantId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.tenantId
        'objectId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.principalId
        'permissions': {
          'keys': [
            'all'
          ]
          'secrets': [
            'all'
          ]
        }
      }
    ]
  }
}
```

---

### <a name="valid-uses"></a>Geldige toepassingen

De functie Reference kan alleen worden gebruikt in de eigenschappen van een resource definitie en de sectie outputs van een sjabloon of implementatie. In combi natie met [eigenschaps herhaling](copy-properties.md)kunt u de functie Reference gebruiken voor `input` omdat de expressie wordt toegewezen aan de resource-eigenschap.

U kunt de functie Reference niet gebruiken om de waarde van de `count` eigenschap in een copy-lus in te stellen. U kunt gebruiken om andere eigenschappen in de lus in te stellen. De verwijzing is geblokkeerd voor de eigenschap Count omdat die eigenschap moet worden bepaald voordat de verwijzings functie wordt opgelost.

Als u de functie Reference of een lijst * wilt gebruiken in het gedeelte outputs van een geneste sjabloon, moet u instellen dat de evaluatie van de  ```expressionEvaluationOptions``` [binnenste Scope](linked-templates.md#expression-evaluation-scope-in-nested-templates) wordt gebruikt of dat u een gekoppeld gebruik maakt in plaats van een geneste sjabloon.

Als u de functie **Reference** gebruikt in een resource die voorwaardelijk wordt geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd.  Er wordt een fout bericht weer geven als de **verwijzings** functie verwijst naar een resource die niet bestaat. Gebruik de functie **als** om te controleren of de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en verwijst naar een voorwaardelijk geïmplementeerde resource.

### <a name="implicit-dependency"></a>Impliciete afhankelijkheid

Als u de functie Reference gebruikt, declareert u impliciet dat de ene resource afhankelijk is van een andere resource als de resource waarnaar wordt verwezen in dezelfde sjabloon is ingericht en u naar de resource verwijst met de naam (niet Resource-ID). U hoeft niet ook de eigenschap dependsOn te gebruiken. De functie wordt niet geëvalueerd tot de resource waarnaar wordt verwezen, de implementatie heeft voltooid.

### <a name="resource-name-or-identifier"></a>Resource naam of-id

Wanneer u verwijst naar een resource die in dezelfde sjabloon is geïmplementeerd, geeft u de naam van de resource op.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(parameters('storageAccountName'))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(storageAccountName)
```

---

Wanneer u verwijst naar een resource die niet is geïmplementeerd in dezelfde sjabloon, geeft u de resource-ID en op `apiVersion` .

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId(storageResourceGroup, 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')]"
```

---

U kunt een volledig gekwalificeerde resource-id opgeven om te voor komen dat u dubbel zinnigheid weet over welke resource u verwijst.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName))
```

---

Bij het samen stellen van een volledig gekwalificeerde verwijzing naar een resource is de volg orde voor het combi neren van segmenten van het type en naam niet gewoon een samen voeging van de twee. Gebruik in plaats daarvan een reeks van *type-en naam* paren van minst specifieke voor de meest specifieke kenmerken:

**{resource-Provider-namespace}/{Parent-resource-type}/{Parent-resource-name} [/{Child-resource-type}/{Child-resource-name}]**

Bijvoorbeeld:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` is correct `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` is niet juist

Gebruik de `resourceId()` functies die in dit document worden beschreven in plaats van de functie om het maken van een resource-id te vereenvoudigen `concat()` .

### <a name="get-managed-identity"></a>Beheerde identiteit ophalen

[Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) zijn [uitbreidings bron typen](../management/extension-resource-types.md) die impliciet voor sommige resources worden gemaakt. Omdat de beheerde identiteit niet expliciet is gedefinieerd in de sjabloon, moet u verwijzen naar de resource waarmee de identiteit wordt toegepast. Gebruik `Full` om alle eigenschappen op te halen, met inbegrip van de impliciet gemaakte identiteit.

Het patroon is:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

Als u bijvoorbeeld de principal-ID wilt ophalen voor een beheerde identiteit die wordt toegepast op een virtuele machine, gebruikt u:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachines', vmName),'2019-12-01', 'Full').identity.principalId
```

---

Als u de Tenant-ID wilt ophalen voor een beheerde identiteit die wordt toegepast op een schaalset voor virtuele machines, gebruikt u:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  vmNodeType0Name), 2019-12-01, 'Full').Identity.tenantId
```

---

### <a name="reference-example"></a>Referentie voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) implementeert een resource en verwijst naar die resource.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string

resource myStorage 'Microsoft.Storage/storageAccounts@2016-12-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {}
  properties: {}
}

output referenceOutput object = reference(storageAccountName)
output fullReferenceOutput object = reference(storageAccountName, '2016-12-01', 'Full')
```

---

In het voor gaande voor beeld worden de twee objecten geretourneerd. Het eigenschappen object heeft de volgende indeling:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Het volledige object heeft de volgende indeling:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) verwijst naar een opslag account dat niet in deze sjabloon is geïmplementeerd. Het opslag account bestaat al in hetzelfde abonnement.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageResourceGroup": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "ExistingStorage": {
      "type": "object",
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageResourceGroup string
param storageAccountName string

output ExistingStorage object = reference(resourceId(storageAccountName), 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')
```

---

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Retourneert een object dat de huidige resource groep vertegenwoordigt.

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object heeft de volgende indeling:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

De eigenschap **managedBy** wordt alleen geretourneerd voor resource groepen die resources bevatten die worden beheerd door een andere service. Voor beheerde toepassingen, Databricks en AKS is de waarde van de eigenschap de resource-ID van de beheer bron.

### <a name="remarks"></a>Opmerkingen

De `resourceGroup()` functie kan niet worden gebruikt in een sjabloon die is [geïmplementeerd op het abonnements niveau](deploy-to-subscription.md). Deze kan alleen worden gebruikt in sjablonen die zijn geïmplementeerd in een resource groep. U kunt de `resourceGroup()` functie gebruiken in een [gekoppelde of geneste sjabloon (met binnenste bereik)](linked-templates.md) die gericht is op een resource groep, zelfs wanneer de bovenliggende sjabloon wordt geïmplementeerd in het abonnement. In dat scenario wordt de gekoppelde of geneste sjabloon geïmplementeerd op het niveau van de resource groep. Zie [Azure-resources implementeren voor meer dan één abonnement of resource groep](./deploy-to-resource-group.md)voor meer informatie over het richten op een resource groep in een implementatie op abonnements niveau.

Een veelvoorkomend gebruik van de functie resourceGroup is het maken van resources op dezelfde locatie als de resource groep. In het volgende voor beeld wordt de locatie van de resource groep gebruikt voor een standaard parameter waarde.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

U kunt ook de functie resourceGroup gebruiken om labels van de resource groep toe te passen op een resource. Zie [labels Toep assen op resource groep](../management/tag-resources.md#apply-tags-from-resource-group)voor meer informatie.

Wanneer geneste sjablonen worden gebruikt voor het implementeren van meerdere resource groepen, kunt u het bereik opgeven voor de evaluatie van de functie resourceGroup. Zie [Azure-resources implementeren voor meer dan één abonnement of resource groep](./deploy-to-resource-group.md)voor meer informatie.

### <a name="resource-group-example"></a>Voor beeld van resource groep

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) retourneert de eigenschappen van de resource groep.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "resourceGroupOutput": {
      "type" : "object",
      "value": "[resourceGroup()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output resourceGroupOutput object = resourceGroup()
```

---

In het voor gaande voor beeld wordt een object in de volgende indeling geretourneerd:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Retourneert de unieke id van een resource. U gebruikt deze functie als de resource naam niet eenduidig of niet binnen dezelfde sjabloon is ingericht. De notatie van de geretourneerde id varieert, afhankelijk van of de implementatie plaatsvindt bij het bereik van een resource groep, een abonnement, een beheer groep of een Tenant.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |teken reeks (in GUID-indeling) |De standaard waarde is het huidige abonnement. Geef deze waarde op als u een resource in een ander abonnement wilt ophalen. Geef deze waarde alleen op wanneer u implementeert in het bereik van een resource groep of een abonnement. |
| resourceGroupName |No |tekenreeks |De standaard waarde is de huidige resource groep. Geef deze waarde op wanneer u een resource in een andere resource groep wilt ophalen. Geef deze waarde alleen op wanneer u implementeert voor het bereik van een resource groep. |
| resourceType |Yes |tekenreeks |Het type resource, inclusief de naam ruimte van de resource provider. |
| resourceName1 |Yes |tekenreeks |De naam van de resource. |
| resourceName2 |No |tekenreeks |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

Wanneer de sjabloon wordt geïmplementeerd in het bereik van een resource groep, wordt de resource-ID geretourneerd met de volgende indeling:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

U kunt de functie resourceId gebruiken voor andere implementatie bereiken, maar de indeling van de ID wordt gewijzigd.

Als u resourceId gebruikt tijdens het implementeren van een abonnement, wordt de resource-ID in de volgende indeling geretourneerd:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Als u resourceId gebruikt tijdens het implementeren van een beheer groep of Tenant, wordt de resource-ID in de volgende indeling geretourneerd:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Om Verwar ring te voor komen, wordt u aangeraden geen resourceId te gebruiken bij het werken met resources die zijn geïmplementeerd in het abonnement, de beheer groep of de Tenant. Gebruik in plaats daarvan de functie ID die is ontworpen voor het bereik.

Gebruik de functie [subscriptionResourceId](#subscriptionresourceid) voor [resources op abonnements niveau](deploy-to-subscription.md).

Gebruik voor [resources op beheer groeps niveau](deploy-to-management-group.md)de functie [extensionResourceId](#extensionresourceid) om te verwijzen naar een resource die is geïmplementeerd als een uitbrei ding van een beheer groep. Aangepaste beleids definities die worden geïmplementeerd in een beheer groep zijn bijvoorbeeld uitbrei dingen van de beheer groep. Gebruik de functie [tenantResourceId](#tenantresourceid) om te verwijzen naar resources die zijn geïmplementeerd naar de Tenant, maar die beschikbaar zijn in uw beheer groep. Ingebouwde beleids definities worden bijvoorbeeld geïmplementeerd als resources op Tenant niveau.

Gebruik de functie [tenantResourceId](#tenantresourceid) voor [resources op Tenant niveau](deploy-to-tenant.md). Gebruik tenantResourceId voor ingebouwde beleids definities omdat deze zijn geïmplementeerd op Tenant niveau.

### <a name="remarks"></a>Opmerkingen

Het aantal para meters dat u opgeeft, varieert op basis van het feit of de resource een bovenliggende of onderliggende resource is en of de resource zich in hetzelfde abonnement of dezelfde resource groep bevindt.

Als u de resource-ID voor een bovenliggende resource in hetzelfde abonnement en dezelfde resource groep wilt ophalen, geeft u het type en de naam van de resource op.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')
```

---

Als u de resource-ID voor een onderliggende resource wilt ophalen, moet u rekening best Eden aan het aantal segmenten in het resource type. Geef een resource naam op voor elk segment van het resource type. De naam van het segment komt overeen met de resource die bestaat voor dat deel van de hiërarchie.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')
```

---

Als u de resource-ID voor een resource in hetzelfde abonnement maar een andere resource groep wilt ophalen, geeft u de naam van de resource groep op.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')
```

---

Als u de resource-ID voor een resource in een ander abonnement en een andere resource groep wilt ophalen, geeft u de abonnements-ID en de naam van de resource groep op.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
```

---

Vaak moet u deze functie gebruiken wanneer u een opslag account of een virtueel netwerk in een andere resource groep gebruikt. In het volgende voor beeld ziet u hoe een resource uit een externe resource groep eenvoudig kan worden gebruikt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "virtualNetworkName": {
      "type": "string"
    },
    "virtualNetworkResourceGroup": {
      "type": "string"
    },
    "subnet1Name": {
      "type": "string"
    },
    "nicName": {
      "type": "string"
    }
  },
  "variables": {
    "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnet1Ref')]"
              }
            }
          }
        ]
      }
    }
  ]
}
```
# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string
param virtualNetworkName string
param virtualNetworkResourceGroup string
param subnet1Name string
param nicName string

var subnet1Ref = resourceId('virtualNetworkResourceGroup', 'Microsoft.Network/virtualNetworks/subnets', 'virtualNetworkName', 'subnet1Name')

resource myInterface 'Microsoft.Network/networkInterfaces@2015-05-01-preview' = {
  name: nicName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          subnet: {
            id: subnet1Ref
          }
        }
      }
    ]
  }
}
```

---


### <a name="resource-id-example"></a>Resource-ID-voor beeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) retourneert de resource-id voor een opslag account in de resource groep:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "sameRGOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentRGOutput": {
      "type": "string",
      "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentSubOutput": {
      "type": "string",
      "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "nestedResourceOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output sameRGOutput string = resourceId('Microsoft.Storage/storageAccounts','examplestorage')
output differentRGOutput string = resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output differentSubOutput string = resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output nestedResourceOutput string = resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')
```

---

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| sameRGOutput | Tekenreeks | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Tekenreeks | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Tekenreeks | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Tekenreeks | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>abonnement

`subscription()`

Retourneert Details over het abonnement voor de huidige implementatie.

### <a name="return-value"></a>Retourwaarde

De functie retourneert de volgende indeling:

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Opmerkingen

Wanneer geneste sjablonen worden gebruikt voor het implementeren van meerdere abonnementen, kunt u het bereik opgeven voor de evaluatie van de abonnements functie. Zie [Azure-resources implementeren voor meer dan één abonnement of resource groep](./deploy-to-resource-group.md)voor meer informatie.

### <a name="subscription-example"></a>Voor beeld van abonnement

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) toont de abonnements functie die is aangeroepen in de sectie outputs.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "subscriptionOutput": {
      "value": "[subscription()]",
      "type" : "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output subscriptionOutput object = subscription()
```

---

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Retourneert de unieke id voor een resource die is geïmplementeerd op het abonnements niveau.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |teken reeks (in GUID-indeling) |De standaard waarde is het huidige abonnement. Geef deze waarde op als u een resource in een ander abonnement wilt ophalen. |
| resourceType |Yes |tekenreeks |Het type resource, inclusief de naam ruimte van de resource provider. |
| resourceName1 |Yes |tekenreeks |De naam van de resource. |
| resourceName2 |No |tekenreeks |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De id wordt geretourneerd in de volgende indeling:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Opmerkingen

U gebruikt deze functie om de resource-ID op te halen voor resources die zijn [geïmplementeerd in het abonnement](deploy-to-subscription.md) in plaats van een resource groep. De geretourneerde ID wijkt af van de waarde die wordt geretourneerd door de functie [resourceId](#resourceid) door de waarde van een resource groep niet op te nemen.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID-voor beeld

De volgende sjabloon wijst een ingebouwde rol toe. U kunt de app implementeren voor een resource groep of abonnement. De functie subscriptionResourceId wordt gebruikt om de resource-ID voor ingebouwde rollen op te halen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}
param roleNameGuid string {
  default: newGuid()
  metadata: {
    'description': 'A new GUID used to identify the role assignment'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

---

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Retourneert de unieke id voor een resource die is geïmplementeerd op Tenant niveau.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceType |Yes |tekenreeks |Het type resource, inclusief de naam ruimte van de resource provider. |
| resourceName1 |Yes |tekenreeks |De naam van de resource. |
| resourceName2 |No |tekenreeks |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De id wordt geretourneerd in de volgende indeling:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Opmerkingen

U gebruikt deze functie om de resource-ID op te halen voor een resource die wordt geïmplementeerd op de Tenant. De geretourneerde ID wijkt af van de waarden die worden geretourneerd door andere resource-ID-functies, met inbegrip van resource groep-of abonnements waarden.

### <a name="tenantresourceid-example"></a>tenantResourceId-voor beeld

Ingebouwde beleids definities zijn resources op Tenant niveau. Gebruik de functie tenantResourceId om een beleids toewijzing te implementeren die verwijst naar een ingebouwde beleids definitie.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string",
      "defaultValue": "0a914e76-4921-4c19-b460-a2d36003525a",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    },
    "policyAssignmentName": {
      "type": "string",
      "defaultValue": "[guid(parameters('policyDefinitionID'), resourceGroup().name)]",
      "metadata": {
        "description": "Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "scope": "[subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)]",
        "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

---

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor een beschrijving van de secties in een arm-sjabloon.
* Zie voor het samen voegen [van meerdere sjablonen gekoppelde en geneste sjablonen gebruiken bij het implementeren van Azure-resources](linked-templates.md).
* Als u een bepaald aantal keer wilt herhalen bij het maken van een resource type, raadpleegt u [resource-iteratie in arm-sjablonen](copy-resources.md).
* Zie [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md)voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt.
