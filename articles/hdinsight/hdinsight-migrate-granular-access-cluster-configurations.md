---
title: Gedetailleerde, op rollen gebaseerde toegang Azure HDInsight clusterconfiguraties
description: Meer informatie over de wijzigingen die zijn vereist als onderdeel van de migratie naar gedetailleerde, op rollen gebaseerde toegang voor HDInsight-clusterconfiguraties.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: afb30f4648f1649bf6cc6cc6a3bf02f433f49d45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774927"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migreren naar gedetailleerde, op rollen gebaseerde toegang voor clusterconfiguraties

We introduceren een aantal belangrijke wijzigingen ter ondersteuning van meer fijngevoelige toegang op basis van rollen om gevoelige informatie te verkrijgen. Als onderdeel van deze wijzigingen is een bepaalde actie mogelijk vereist voor 3 september **2019** als u een van de betrokken [entiteiten/scenario's gebruikt.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Wat wordt er gewijzigd?

Eerder konden geheimen worden verkregen via de HDInsight-API door clustergebruikers met de Azure-rollen Eigenaar, Inzender of [Lezer,](../role-based-access-control/rbac-and-directory-admin-roles.md)omdat ze beschikbaar waren voor iedereen met de `*/read` machtiging. Geheimen worden gedefinieerd als waarden die kunnen worden gebruikt om meer verhoogde toegang te krijgen dan de rol van een gebruiker zou moeten toestaan. Dit zijn waarden zoals HTTP-referenties voor clustergateways, opslagaccountsleutels en databasereferenties.

Vanaf 3 september 2019 is de machtiging vereist voor toegang tot deze geheimen, wat betekent dat gebruikers met de rol Lezer geen toegang meer `Microsoft.HDInsight/clusters/configurations/action` hebben tot deze geheimen. De rollen met deze machtiging zijn Inzender, Eigenaar en de nieuwe rol HDInsight-clusteroperator (meer informatie hieronder).

We introduceren ook een nieuwe [HDInsight-clusteroperatorrol](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) die geheimen kan ophalen zonder de beheerdersmachtigingen van Inzender of Eigenaar te krijgen. Samenvatting:

| Rol                                  | Eerder                                                                                       | In de toekomst       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Lezer                                | - Leestoegang, inclusief geheimen.                                                                   | - Leestoegang, **met uitzondering van** geheimen | 
| HDInsight-clusteroperator<br>(Nieuwe rol) | N.v.t.                                                                                              | - Lees-/schrijftoegang, inclusief geheimen         | 
| Inzender                           | - Lees-/schrijftoegang, inclusief geheimen.<br>- Maak en beheer alle typen Azure-resources.<br>- Scriptacties uitvoeren.     | Geen wijziging |
| Eigenaar                                 | - Lees-/schrijftoegang, inclusief geheimen.<br>- Volledige toegang tot alle resources<br>- Toegang aan anderen delegeren.<br>- Scriptacties uitvoeren. | Geen wijziging |

Zie de onderstaande sectie De roltoewijzing HDInsight-clusteroperator toevoegen aan een gebruiker voor informatie over het toevoegen van de roltoewijzing HDInsight-clusteroperator aan een gebruiker om hen lees-/schrijftoegang te verlenen tot [clustergeheimen.](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)

## <a name="am-i-affected-by-these-changes"></a>Heb ik last van deze wijzigingen?

De volgende entiteiten en scenario's worden beïnvloed:

- [API:](#api)gebruikers die de `/configurations` `/configurations/{configurationName}` eindpunten of gebruiken.
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versie 1.1.1 of lager.
- [Azure-toolkit voor IntelliJ](#azure-toolkit-for-intellij) versie 3.20.0 of lager.
- [Azure Data Lake en Stream Analytics Tools voor Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) lager dan versie 2.3.9000.1.
- [Azure-toolkit voor Eclipse](#azure-toolkit-for-eclipse) versie 3.15.0 of lager.
- [SDK for .NET](#sdk-for-net) (SDK voor .NET)
    - [versie 1.x of 2.x:](#versions-1x-and-2x)gebruikers die de methoden , , of van de `GetClusterConfigurations` `GetConnectivitySettings` klasse `ConfigureHttpSettings` `EnableHttp` `DisableHttp` ConfigurationsOperationsExtensions gebruiken.
    - [versions 3.x en up:](#versions-3x-and-up)gebruikers die de `Get` methoden , , of van de klasse `Update` `EnableHttp` `DisableHttp` `ConfigurationsOperationsExtensions` gebruiken.
- [SDK voor Python:](#sdk-for-python)gebruikers die de `get` methoden of van de klasse `update` `ConfigurationsOperations` gebruiken.
- [SDK voor Java:](#sdk-for-java)gebruikers die de `update` methoden of van de klasse `get` `ConfigurationsInner` gebruiken.
- [SDK voor Go:](#sdk-for-go)gebruikers die de `Get` methoden of van de `Update` `ConfigurationsClient` struct gebruiken.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) lager dan versie 2.0.0.
Zie de onderstaande secties (of gebruik de bovenstaande koppelingen) om de migratiestappen voor uw scenario te bekijken.

### <a name="api"></a>API

De volgende API's worden gewijzigd of afgeschaft:

- [**GET /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (gevoelige informatie verwijderd)
    - Eerder gebruikt voor het verkrijgen van afzonderlijke configuratietypen (inclusief geheimen).
    - Vanaf 3 september 2019 retourneren met deze API-aanroep nu afzonderlijke configuratietypen met weggelaten geheimen. Als u alle configuraties, inclusief geheimen, wilt verkrijgen, gebruikt u de nieuwe POST/configurations-aanroep. Als u alleen gatewayinstellingen wilt verkrijgen, gebruikt u de nieuwe POST/getGatewaySettings-aanroep.
- [**GET/configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (afgeschaft)
    - Eerder gebruikt voor het verkrijgen van alle configuraties (inclusief geheimen)
    - Vanaf 3 september 2019 wordt deze API-aanroep afgeschaft en wordt deze niet meer ondersteund. Als u alle configuraties in de toekomst wilt verkrijgen, gebruikt u de nieuwe POST/configurations-aanroep. Als u configuraties wilt verkrijgen met gevoelige parameters die worden weggelaten, gebruikt u de aanroep GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (afgeschaft)
    - Eerder gebruikt om gatewayreferenties bij te werken.
    - Vanaf 3 september 2019 wordt deze API-aanroep afgeschaft en niet meer ondersteund. Gebruik in plaats daarvan de nieuwe POST /updateGatewaySettings.

De volgende vervangende API's zijn toegevoegd:</span>

- [**POST/configuraties**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Gebruik deze API om alle configuraties te verkrijgen, inclusief geheimen.
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Gebruik deze API om gatewayinstellingen te verkrijgen.
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Gebruik deze API om gatewayinstellingen (gebruikersnaam en/of wachtwoord) bij te werken.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Als u versie 1.1.1 of lager gebruikt, moet u bijwerken naar de nieuwste versie van [Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) om onderbrekingen te voorkomen.

### <a name="azure-toolkit-for-intellij"></a>Azure-toolkit voor IntelliJ

Als u versie 3.20.0 of lager gebruikt, moet u bijwerken naar de nieuwste versie van de [Azure-toolkit voor IntelliJ-invoegversie](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) om onderbrekingen te voorkomen.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake en Stream Analytics Tools voor Visual Studio

Werk bij naar versie 2.3.9000.1 of hoger van Azure Data Lake en [Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) om onderbrekingen te voorkomen.  Zie onze documentatie Update Data Lake Tools for Visual Studio voor meer [Visual Studio.](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio)

### <a name="azure-toolkit-for-eclipse"></a>Azure-toolkit voor Eclipse

Als u versie 3.15.0 of lager gebruikt, moet u bijwerken naar de nieuwste versie van de [Azure-toolkit voor Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) onderbrekingen te voorkomen.

### <a name="sdk-for-net"></a>SDK voor .NET

#### <a name="versions-1x-and-2x"></a>Versies 1.x en 2.x

Werk bij [naar versie 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) van de HDInsight SDK voor .NET. Er kunnen minimale codewijzigingen vereist zijn als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- `ClusterOperationsExtensions.GetClusterConfigurations`**retourneert geen gevoelige parameters meer,** zoals opslagsleutels (core-site) of HTTP-referenties (gateway).
    - Als u alle configuraties, inclusief gevoelige parameters, wilt ophalen, gebruikt `ClusterOperationsExtensions.ListConfigurations` u in de toekomst.  Houd er rekening mee dat gebruikers met de rol Lezer deze methode niet kunnen gebruiken. Hierdoor is gedetailleerde controle mogelijk over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster.
    - Als u alleen HTTP-gatewayreferenties wilt ophalen, gebruikt `ClusterOperationsExtensions.GetGatewaySettings` u .

- `ClusterOperationsExtensions.GetConnectivitySettings` is nu afgeschaft en is vervangen door `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` is nu afgeschaft en is vervangen door `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` en `DisableHttp` zijn nu afgeschaft. HTTP is nu altijd ingeschakeld, dus deze methoden zijn niet meer nodig.

#### <a name="versions-3x-and-up"></a>Versies 3.x en nieuwe versies

Werk bij [naar versie 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) of hoger van de HDInsight SDK voor .NET. Er kunnen minimale codewijzigingen vereist zijn als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get)**retourneert geen gevoelige parameters meer,** zoals opslagsleutels (core-site) of HTTP-referenties (gateway).
    - Als u alle configuraties, inclusief gevoelige parameters, wilt ophalen, gebruikt [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) u in de toekomst.Houd er rekening mee dat gebruikers met de rol Lezer deze methode niet kunnen gebruiken. Hierdoor is gedetailleerde controle mogelijk over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen HTTP-gatewayreferenties wilt ophalen, gebruikt [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) u . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) is nu afgeschaft en is vervangen door [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) en [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) zijn nu afgeschaft. HTTP is nu altijd ingeschakeld, dus deze methoden zijn niet meer nodig.

### <a name="sdk-for-python"></a>SDK voor Python

Werk bij [naar versie 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) of hoger van de HDInsight SDK voor Python. Er kunnen minimale codewijzigingen vereist zijn als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**retourneert geen gevoelige parameters meer,** zoals opslagsleutels (core-site) of HTTP-referenties (gateway).
    - Als u alle configuraties, inclusief gevoelige parameters, wilt ophalen, gebruikt [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) u in de toekomst.Houd er rekening mee dat gebruikers met de rol Lezer deze methode niet kunnen gebruiken. Hierdoor is gedetailleerde controle mogelijk over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen HTTP-gatewayreferenties wilt ophalen, gebruikt [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) u .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) is nu afgeschaft en is vervangen door [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>SDK voor Java

Werk bij [naar versie 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) of hoger van de HDInsight SDK voor Java. Er kunnen minimale codewijzigingen nodig zijn als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- `ConfigurationsInner.get`**retourneert geen gevoelige parameters meer,** zoals opslagsleutels (core-site) of HTTP-referenties (gateway).
- `ConfigurationsInner.update` is nu afgeschaft.

### <a name="sdk-for-go"></a>SDK voor Go

Werk bij [naar versie 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) of hoger van de HDInsight SDK voor Go. Er kunnen minimale codewijzigingen nodig zijn als u een methode gebruikt die wordt beïnvloed door deze wijzigingen:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get)**retourneert geen gevoelige parameters meer,** zoals opslagsleutels (core-site) of HTTP-referenties (gateway).
    - Als u alle configuraties, inclusief gevoelige parameters, wilt ophalen, gebruikt [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) u in de toekomst.Houd er rekening mee dat gebruikers met de rol Lezer deze methode niet kunnen gebruiken. Dit maakt gedetailleerde controle mogelijk over welke gebruikers toegang hebben tot gevoelige informatie voor een cluster. 
    - Als u alleen HTTP-gatewayreferenties wilt ophalen, gebruikt u [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) is nu afgeschaft en is vervangen door [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Werk bij [naar Az PowerShell versie 2.0.0](https://www.powershellgallery.com/packages/Az) of hoger om onderbrekingen te voorkomen.  Er kunnen minimale codewijzigingen nodig zijn als u een methode gebruikt die wordt beïnvloed door deze wijzigingen.
- `Grant-AzHDInsightHttpServicesAccess` is nu afgeschaft en is vervangen door de nieuwe `Set-AzHDInsightGatewayCredential` cmdlet .
- `Get-AzHDInsightJobOutput` is bijgewerkt om gedetailleerde, op rollen gebaseerde toegang tot de opslagsleutel te ondersteunen.
    - Gebruikers met de HDInsight Cluster-rollen Operator, Bijdrager of Eigenaar worden niet beïnvloed.
    - Gebruikers met alleen de rol Lezer moeten de `DefaultStorageAccountKey` parameter expliciet opgeven.
- `Revoke-AzHDInsightHttpServicesAccess` is nu afgeschaft. HTTP is nu altijd ingeschakeld, dus deze cmdlet is niet meer nodig.
 Zie [de az. HDInsight-migratiehandleiding](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) voor meer informatie.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>De roltoewijzing HDInsight-clusteroperator toevoegen aan een gebruiker

Een gebruiker [](../role-based-access-control/built-in-roles.md#owner) met de rol Eigenaar kan de [rol HDInsight-clusteroperator](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) toewijzen aan gebruikers die u lees-/schrijftoegang wilt hebben tot gevoelige configuratiewaarden voor het HDInsight-cluster (zoals gatewayreferenties voor clusters en opslagaccountsleutels).

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

De eenvoudigste manier om deze roltoewijzing toe te voegen, is met behulp van `az role assignment create` de opdracht in Azure CLI.

> [!NOTE]
> Deze opdracht moet worden uitgevoerd door een gebruiker met de rol Eigenaar, omdat alleen deze machtigingen kunnen worden verleend. De is de naam van de service-principal of het e-mailadres van de gebruiker aan wie `--assignee` u de rol HDInsight-clusteroperator wilt toewijzen. Als er een fout met onvoldoende machtigingen wordt weergegeven, bekijkt u de onderstaande veelgestelde vragen.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Rol verlenen op het niveau van de resource (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Rol verlenen op het niveau van de resourcegroep

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Rol verlenen op abonnementsniveau

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

U kunt ook de roltoewijzing Azure Portal HDInsight-clusteroperator toevoegen aan een gebruiker. Zie de documentatie [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="faq"></a>Veelgestelde vragen

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Waarom krijg ik een 403 (Verboden) reactie na het bijwerken van mijn API-aanvragen en/of hulpprogramma?

Clusterconfiguraties staan nu achter gedetailleerd op rollen gebaseerd toegangsbeheer en vereisen de `Microsoft.HDInsight/clusters/configurations/*` machtiging om toegang te krijgen. Als u deze machtiging wilt verkrijgen, wijst u de rol HDInsight-clusteroperator, -inzender of -eigenaar toe aan de gebruiker of service-principal die toegang probeert te krijgen tot configuraties.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Waarom zie ik 'Onvoldoende bevoegdheden om de bewerking te voltooien' bij het uitvoeren van de Azure CLI-opdracht om de rol HDInsight-clusteroperator toe te wijzen aan een andere gebruiker of service-principal?

Naast de rol Eigenaar moet de gebruiker of service-principal die de opdracht uitvoert, voldoende Azure AD-machtigingen hebben om de object-ID's van de toegewezene op te zoeken. Dit bericht geeft aan dat er onvoldoende Azure AD-machtigingen zijn. Vervang het argument door en geef de object-id van de toegewezene op als de parameter in plaats van de naam (of de principal-id in het geval van `-–assignee` `–assignee-object-id` een beheerde identiteit). Zie de sectie optionele parameters in de [documentatie over az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) voor meer informatie.

Als dit nog steeds niet werkt, neemt u contact op met uw Azure AD-beheerder om de juiste machtigingen te verkrijgen.

### <a name="what-will-happen-if-i-take-no-action"></a>Wat gebeurt er als ik geen actie onderneemt?

Vanaf 3 september 2019 retourneren aanroepen geen informatie meer en retourneren de aanroep geen gevoelige parameters meer, zoals opslagaccountsleutels of het `GET /configurations` `POST /configurations/gateway` `GET /configurations/{configurationName}` clusterwachtwoord. Hetzelfde geldt voor de bijbehorende SDK-methoden en PowerShell-cmdlets.

Als u een oudere versie van een van de hulpprogramma's voor Visual Studio, VSCode, IntelliJ of Eclipse gebruikt die hierboven wordt vermeld, werken ze niet meer totdat u ze bijwerkt.

Zie de bijbehorende sectie van dit document voor uw scenario voor meer informatie.
