---
title: Op werkruimte gebaseerd resourceschema van Azure Monitor Application Insights
description: Meer informatie over de nieuwe tabel structuur en het schema voor Azure Monitor resources op basis van Application Insights werk ruimte.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: ef9d22cd2b45679928ee54778b2a521ea9ecab03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575603"
---
# <a name="workspace-based-resource-changes"></a>Wijzigingen van resources op basis van werk ruimten

Vóór de introductie van [Application Insights resources op basis van een werk ruimte](create-workspace-resource.md), zijn Application Insights gegevens gescheiden van andere logboek gegevens opgeslagen in azure monitor. Beide zijn gebaseerd op Azure Data Explorer en gebruiken dezelfde Kusto query language (KQL). Dit wordt beschreven in de [Logboeken van Azure monitor](../logs/data-platform-logs.md).

Met Application Insights resources-gegevens op basis van een werk ruimte worden opgeslagen in een Log Analytics-werk ruimte met andere bewakings gegevens en toepassings gegevens. Dit vereenvoudigt uw configuratie, zodat u gemakkelijker gegevens kunt analyseren over meerdere oplossingen en de mogelijkheden van werk ruimten kunt benutten.

## <a name="table-structure"></a>Tabelstructuur

| Verouderde tabel naam | Nieuwe tabelnaam | Beschrijving |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Samenvattings gegevens van beschikbaarheids testen.|
| browserTimings | AppBrowserTimings | Gegevens over client prestaties, zoals de tijd die nodig is om de binnenkomende gegevens te verwerken.|
| elkaar | AppDependencies | Aanroepen van de toepassing naar andere onderdelen (waaronder externe onderdelen) die zijn vastgelegd via TrackDependency (), bijvoorbeeld aanroepen naar REST API, data base of een bestands systeem.  |
| customEvents | AppEvents | Aangepaste gebeurtenissen die door uw toepassing zijn gemaakt. |
| customMetrics | AppMetrics | Aangepaste metrische gegevens die door uw toepassing zijn gemaakt. |
| Page views | AppPageViews| Gegevens over elke website weergave met browser informatie. |
| Performance Counters | AppPerformanceCounters | Prestatie metingen van de reken bronnen die de toepassing ondersteunen, bijvoorbeeld Windows-prestatie meter items. |
| requests | AppRequests | Aanvragen die door uw toepassing zijn ontvangen. Een afzonderlijke aanvraag record wordt bijvoorbeeld vastgelegd voor elke HTTP-aanvraag die uw web-app ontvangt.  |
| uitzonderingen | AppSystemEvents | Uitzonde ringen die worden veroorzaakt door de runtime van de toepassing, legt zowel server-als client-side-uitzonde ringen vast. |
| traceringen | AppTraces | Gedetailleerde logboeken (traceringen) die worden verzonden via toepassings code/logboek registratie raamwerken vastgelegd via TrackTrace (). |

## <a name="table-schemas"></a>Tabel schema's

In de volgende secties ziet u de toewijzing tussen de namen van de klassieke eigenschappen en de nieuwe Application Insights eigenschappen van de werk ruimte.  Gebruik deze informatie om query's te converteren met behulp van verouderde tabellen.

De meeste kolommen hebben dezelfde naam met een ander hoofdletter gebruik. Aangezien KQL hoofdletter gevoelig is, moet u elke kolom naam wijzigen in combi natie met de tabel namen in bestaande query's. Kolommen met wijzigingen naast het hoofdletter gebruik worden gemarkeerd. U kunt nog steeds uw klassieke Application Insights query's gebruiken in het deel venster **Logboeken** van uw Application Insights resource, zelfs als het een op een werk ruimte gebaseerde resource is. De nieuwe eigenschapnamen zijn vereist voor het uitvoeren van query's in de context van de Log Analytics werkruimte-ervaring.

### <a name="appavailabilityresults"></a>AppAvailabilityResults

Verouderde tabel: Beschik baarheid

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|Dynamisch|
|customMeasurements|dynamisch|Metingen|Dynamisch|
|duur|werkelijk|DurationMs|werkelijk|
|`id`|tekenreeks|`Id`|tekenreeks|
|iKey|tekenreeks|IKey|tekenreeks|
|itemCount|int|ItemCount|int|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|Tekenreeks|
|location|tekenreeks|Locatie|tekenreeks|
|message|tekenreeks|Bericht|tekenreeks|
|naam|tekenreeks|Name|tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|Requests|tekenreeks|Requests|tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|grootte|werkelijk|Grootte|werkelijk|
|voltooid|tekenreeks|Geslaagd|Booleaanse waarde|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Verouderde tabel: browserTimings

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|Dynamisch|
|customMeasurements|dynamisch|Metingen|Dynamisch|
|iKey|tekenreeks|IKey|tekenreeks|
|itemCount|int|ItemCount|int|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|tekenreeks|
|naam|tekenreeks|Name|datum/tijd|
|networkDuration|werkelijk|NetworkDurationMs|werkelijk|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|Requests|tekenreeks|Requests|tekenreeks|
|processingDuration|werkelijk|ProcessingDurationMs|werkelijk|
|receiveDuration|werkelijk|ReceiveDurationMs|werkelijk|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|sendDuration|werkelijk|SendDurationMs|werkelijk|
|session_Id|tekenreeks|SessionId|tekenreeks|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|totalDuration|werkelijk|TotalDurationMs|werkelijk|
|url|tekenreeks|URL|tekenreeks|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|

### <a name="appdependencies"></a>AppDependencies

Verouderde tabel: afhankelijkheden

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|Dynamisch|
|customMeasurements|dynamisch|Metingen|Dynamisch|
|gegevens|tekenreeks|Gegevens|tekenreeks|
|duur|werkelijk|DurationMs|werkelijk|
|`id`|tekenreeks|`Id`|tekenreeks|
|iKey|tekenreeks|IKey|tekenreeks|
|itemCount|int|ItemCount|int|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|Tekenreeks|
|naam|tekenreeks|Name|tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|Requests|tekenreeks|Requests|tekenreeks|
|resultCode|tekenreeks|ResultCode|tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|voltooid|tekenreeks|Geslaagd|Booleaanse waarde|
|stemming|tekenreeks|Doel|tekenreeks|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|type|tekenreeks|DependencyType|tekenreeks|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|

### <a name="appevents"></a>AppEvents

Verouderde tabel: customEvents

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|Dynamisch|
|customMeasurements|dynamisch|Metingen|Dynamisch|
|iKey|tekenreeks|IKey|tekenreeks|
|itemCount|int|ItemCount|int|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|tekenreeks|
|naam|tekenreeks|Name|tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|

### <a name="appmetrics"></a>AppMetrics

Verouderde tabel: customMetrics

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|Dynamisch|
|iKey|tekenreeks|IKey|tekenreeks|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|tekenreeks|
|naam|tekenreeks|Name|tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|
|waarde|werkelijk|Voer||
|valueCount|int|ValueCount|int|
|valueMax|werkelijk|ValueMax|werkelijk|
|valueMin|werkelijk|ValueMin|werkelijk|
|valueStdDev|werkelijk|ValueStdDev|werkelijk|
|valueSum|werkelijk|ValueSum|werkelijk|

### <a name="apppageviews"></a>AppPageViews

Verouderde tabel: page views

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|Dynamisch|
|customMeasurements|dynamisch|Metingen|Dynamisch|
|duur|werkelijk|DurationMs|werkelijk|
|`id`|tekenreeks|`Id`|tekenreeks|
|iKey|tekenreeks|IKey|tekenreeks|
|itemCount|int|ItemCount|int|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|Tekenreeks|
|naam|tekenreeks|Name|tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|Requests|tekenreeks|Requests|tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|url|tekenreeks|URL|tekenreeks|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Verouderde tabel: Performance Counters

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|category|tekenreeks|Categorie|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|counter|tekenreeks|Voer||
|customDimensions|dynamisch|Eigenschappen|Dynamisch|
|iKey|tekenreeks|IKey|tekenreeks|
|exemplaar|tekenreeks|Exemplaar|tekenreeks|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|tekenreeks|
|naam|tekenreeks|Name|tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|
|waarde|werkelijk|Waarde|werkelijk|

### <a name="apprequests"></a>AppRequests

Verouderde tabel: aanvragen

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|Dynamisch|
|customMeasurements|dynamisch|Metingen|Dynamisch|
|duur|werkelijk|DurationMs|Realistische|
|`id`|tekenreeks|`Id`|Tekenreeks|
|iKey|tekenreeks|IKey|tekenreeks|
|itemCount|int|ItemCount|int|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|Tekenreeks|
|naam|tekenreeks|Name|Tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|Requests|tekenreeks|Requests|Tekenreeks|
|resultCode|tekenreeks|ResultCode|Tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|source|tekenreeks|Bron|Tekenreeks|
|voltooid|tekenreeks|Geslaagd|Booleaanse waarde|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|url|tekenreeks|URL|Tekenreeks|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|

### <a name="appsystemevents"></a>AppSystemEvents

Verouderde tabel: uitzonde ringen

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|assemblage|tekenreeks|Assembly|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|dynamisch|
|customMeasurements|dynamisch|Metingen|dynamisch|
|nadere|dynamisch|Details|dynamisch|
|handledAt|tekenreeks|HandledAt|tekenreeks|
|iKey|tekenreeks|IKey|tekenreeks|
|innermostAssembly|tekenreeks|InnermostAssembly|tekenreeks|
|innermostMessage|tekenreeks|InnermostMessage|tekenreeks|
|innermostMethod|tekenreeks|InnermostMethod|tekenreeks|
|innermostType|tekenreeks|InnermostType|tekenreeks|
|itemCount|int|ItemCount|int|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|tekenreeks|
|message|tekenreeks|Bericht|tekenreeks|
|method|tekenreeks|Methode|tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|outerAssembly|tekenreeks|OuterAssembly|tekenreeks|
|outerMessage|tekenreeks|OuterMessage|tekenreeks|
|outerMethod|tekenreeks|OuterMethod|tekenreeks|
|outerType|tekenreeks|OuterType|tekenreeks|
|Probleem|tekenreeks|Probleem|tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|severityLevel|int|SeverityLevel|int|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|type|tekenreeks|ExceptionType|tekenreeks|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|

### <a name="apptraces"></a>AppTraces

Verouderde tabel: traceringen

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|tekenreeks|\_GUID|tekenreeks|
|application_Version|tekenreeks|AppVersion|tekenreeks|
|Toepassings|tekenreeks|\_ResourceId|tekenreeks|
|client_Browser|tekenreeks|ClientBrowser|tekenreeks|
|client_City|tekenreeks|ClientCity|tekenreeks|
|client_CountryOrRegion|tekenreeks|ClientCountryOrRegion|tekenreeks|
|client_IP|tekenreeks|ClientIP|tekenreeks|
|client_Model|tekenreeks|ClientModel|tekenreeks|
|client_OS|tekenreeks|ClientOS|tekenreeks|
|client_StateOrProvince|tekenreeks|ClientStateOrProvince|tekenreeks|
|client_Type|tekenreeks|ClientType|tekenreeks|
|cloud_RoleInstance|tekenreeks|AppRoleInstance|tekenreeks|
|cloud_RoleName|tekenreeks|AppRoleName|tekenreeks|
|customDimensions|dynamisch|Eigenschappen|dynamisch|
|customMeasurements|dynamisch|Metingen|dynamisch|
|iKey|tekenreeks|IKey|tekenreeks|
|itemCount|int|ItemCount|int|
|itemId|tekenreeks|\_ItemId|tekenreeks|
|Item type|tekenreeks|Type|tekenreeks|
|message|tekenreeks|Bericht|tekenreeks|
|operation_Id|tekenreeks|OperationId|tekenreeks|
|operation_Name|tekenreeks|OperationName|tekenreeks|
|operation_ParentId|tekenreeks|OperationParentId|tekenreeks|
|operation_SyntheticSource|tekenreeks|OperationSyntheticSource|tekenreeks|
|sdkVersion|tekenreeks|SdkVersion|tekenreeks|
|session_Id|tekenreeks|SessionId|tekenreeks|
|severityLevel|int|SeverityLevel|int|
|tijdstempel|datum/tijd|TimeGenerated|datum/tijd|
|user_AccountId|tekenreeks|UserAccountId|tekenreeks|
|user_AuthenticatedId|tekenreeks|UserAuthenticatedId|tekenreeks|
|user_Id|tekenreeks|UserId|tekenreeks|

## <a name="next-steps"></a>Volgende stappen

* [Metrische gegevens verkennen](../essentials/metrics-charts.md)
* [Analytics-query's schrijven](../logs/log-query-overview.md)

