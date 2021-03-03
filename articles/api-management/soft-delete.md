---
title: Azure API Management voorlopig verwijderen (preview) | Microsoft Docs
description: Met zacht verwijderen kunt u verwijderde API Management exemplaren herstellen.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: e2842f3e428abb4f0eb628dbb8e446f2714d5d89
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652382"
---
# <a name="api-management-soft-delete-preview"></a>API Management zacht verwijderen (preview-versie)

Met API Management zacht verwijderen (preview) kunt u recent verwijderde API Management-exemplaren (APIM) herstellen en herstellen.

> [!IMPORTANT]
> Alleen API Management exemplaren die worden verwijderd met `2020-06-01-preview` en latere versies van de API, worden zacht verwijderd en kunnen worden hersteld aan de hand van de stappen die in dit artikel worden beschreven. APIM-exemplaren die zijn verwijderd met eerdere API-versies, blijven permanent worden verwijderd. Azure PowerShell en Azure CLI maken momenteel geen gebruik `2020-06-01-preview` van de versie en resulteren ook in het gedrag van het permanent verwijderen.

## <a name="supporting-interfaces"></a>Ondersteunende interfaces

De functie voor voorlopig verwijderen is beschikbaar via [rest API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore).

> [!TIP]
> Raadpleeg de [Naslag informatie voor azure rest API](/rest/api/azure/) voor tips en hulpprogram ma's voor het aanroepen van Azure rest api's.

| Bewerking | Beschrijving | API Management naam ruimte | Minimale API-versie |
|--|--|--|--|
| [Maken of bijwerken](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Hiermee wordt een API Management service gemaakt of bijgewerkt.  | API Management-service | Alle |
| [Maken of bijwerken](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) met `restore` eigenschap ingesteld op **waar** | Hiermee wordt de verwijdering van API Management-service ongedaan gemaakt als deze eerder zacht werd verwijderd. Als `restore` is opgegeven en ingesteld op `true` alle andere eigenschappen, wordt genegeerd.  | API Management-service |  2020-06-01-preview |
| [Verwijderen](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Hiermee verwijdert u een bestaande API Management-service. | API Management-service | 2020-06-01-preview|
| [Ophalen op naam](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | De voorlopig verwijderde API Management-service op naam ophalen. | Verwijderde Services | 2020-06-01-preview |
| [Lijst op abonnement](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Een lijst met alle voorlopig verwijderde services die beschikbaar zijn voor verwijdering ongedaan maken voor het opgegeven abonnement. | Verwijderde Services | 2020-06-01-preview
| [Opschonen](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Hiermee wordt API Management service gewist (deze wordt verwijderd zonder optie om de verwijdering ongedaan te maken). | Verwijderde Services | 2020-06-01-preview

## <a name="soft-delete-behavior"></a>Gedrag bij voorlopig verwijderen

U kunt een wille keurige API-versie gebruiken om uw API Management-exemplaar te maken, maar u moet `2020-06-01-preview` of hogere versies gebruiken om uw APIM-exemplaar te verwijderen (en de optie om het te herstellen).

Wanneer u een API Management exemplaar verwijdert, bestaat de service in een verwijderde staat, waardoor deze niet toegankelijk is voor APIM bewerkingen. In deze status kan het APIM-exemplaar alleen worden vermeld, hersteld of verwijderd (permanent verwijderd).

Tegelijkertijd plant Azure het verwijderen van de onderliggende gegevens die overeenkomen met het APIM-exemplaar voor uitvoering na de Bewaar periode van het vooraf vastgestelde (48 uur). De DNS-record die overeenkomt met het exemplaar, wordt ook bewaard voor de duur van de Bewaar periode. U kunt de naam van een API Management exemplaar dat zacht is verwijderd, pas opnieuw gebruiken nadat de Bewaar periode is verstreken.

Als uw APIM-exemplaar niet binnen 48 uur wordt hersteld, is het permanent verwijderd (onherstelbaar). U kunt er ook voor kiezen om uw APIM-exemplaar [leeg](#purge-a-soft-deleted-apim-instance) te maken (permanent verwijderen), voor voor gaande Bewaar periode voor voorlopig verwijderen.

## <a name="list-deleted-apim-instances"></a>Verwijderde APIM-instanties weer geven

U kunt controleren of een voorlopig verwijderd APIM-exemplaar beschikbaar is voor herstel (Undelete) met behulp van de verwijderde Services [ophalen op basis van de naam](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) of [lijst op basis van een abonnement](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) .

### <a name="get-a-soft-deleted-instance-by-name"></a>Een voorlopig verwijderd exemplaar op naam ophalen

Gebruik de API Management bewerking [Get by name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) , substitutie `{subscriptionId}` , `{location}` , en `{serviceName}` met uw Azure-abonnement, resource locatie en API Management exemplaar naam:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Als deze beschikbaar is voor ongedaan maken, wordt er door Azure een record geretourneerd van het APIM-exemplaar met de `deletionDate` en `scheduledPurgeDate` , bijvoorbeeld:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Alle voorlopig verwijderde exemplaren voor een bepaald abonnement weer geven

Gebruik de API Management [lijst per abonnements](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) bewerking, `{subscriptionId}` waarbij u de abonnements-id vervangt:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Hiermee wordt een lijst geretourneerd met alle voorlopig verwijderde services die beschikbaar zijn voor verwijdering ongedaan maken onder het gegeven abonnement, waarin de `deletionDate` en voor elke worden weer gegeven `scheduledPurgeDate` .

## <a name="recover-a-deleted-apim-instance"></a>Een verwijderd APIM-exemplaar herstellen

Gebruik de API Management [maken of bijwerken](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) , vervangen `{subscriptionId}` , `{resourceGroup}` en `{apimServiceName}` met uw Azure-abonnement, naam van de resource groep en API Management naam:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . en stel de `restore` eigenschap `true` in op de hoofd tekst van de aanvraag. (Als deze vlag is opgegeven en is ingesteld op *True*, worden alle andere eigenschappen genegeerd.) Bijvoorbeeld:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Een voorlopig verwijderd APIM-exemplaar verwijderen

Gebruik de API Management [opschoon](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) bewerking, het vervangen `{subscriptionId}` `{location}` van, en `{serviceName}` met uw Azure-abonnement, resource locatie en API Management naam:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Hiermee wordt uw API Management-exemplaar permanent verwijderd uit Azure.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de back-up-en herstel opties voor lange termijn API Management:

- [Noodherstel implementeren met back-up en herstellen van services in Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)