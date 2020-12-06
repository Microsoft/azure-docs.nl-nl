---
title: Integratie service omgevingen (ISEs) met Logic Apps REST API maken
description: Maak een integratie service omgeving (ISE) met behulp van de Logic Apps REST API zodat u Azure Virtual Networks (VNETs) kunt openen vanuit Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 12/05/2020
ms.openlocfilehash: 783431c4888a68e24cf3d2603c541c4797ea65d8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741096"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Een integratieserviceomgeving (ISE) maken met behulp van de Logic Apps REST API

Voor scenario's waarbij uw Logic apps en integratie accounts toegang nodig hebben tot een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md), kunt u een [ *integratie service omgeving* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken met behulp van de Logic apps rest API. Zie [toegang tot Azure Virtual Network-resources vanuit Azure Logic apps](connect-virtual-network-vnet-isolated-environment-overview.md)voor meer informatie over ISEs.

In dit artikel wordt beschreven hoe u een ISE maakt met behulp van de Logic Apps REST API in het algemeen. U kunt eventueel ook een door het [systeem toegewezen of door de gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) inschakelen op uw ISE, maar alleen met behulp van de Logic apps rest API op dit moment. Met deze identiteit kan uw ISE toegang tot beveiligde bronnen verifiëren, zoals virtuele machines en andere systemen of services, die in of verbonden zijn met een virtueel Azure-netwerk. Op die manier hoeft u zich niet aan te melden met uw referenties.

Raadpleeg de volgende artikelen voor meer informatie over andere manieren om een ISE te maken:

* [Een ISE maken met behulp van de Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Een ISE maken met behulp van de voor beeld-Azure Resource Manager Quick Start-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Een ISE maken die ondersteuning biedt voor door de klant beheerde sleutels voor het versleutelen van gegevens in rust](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Vereisten

* Dezelfde vereisten [prerequisites](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) en [toegangs eisen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) als bij het maken van een ISE in de Azure Portal

* Alle extra resources die u met uw ISE wilt gebruiken, zodat u de informatie in de ISE-definitie kunt toevoegen, bijvoorbeeld: 

  * Als u ondersteuning voor zelfondertekende certificaten wilt inschakelen, moet u in de ISE-definitie informatie over dat certificaat toevoegen.

  * Als u de door de gebruiker toegewezen beheerde identiteit wilt inschakelen, moet u die identiteit vooraf maken en de `objectId` `principalId` Eigenschappen, en de `clientId` bijbehorende waarden in de ISE-definitie toevoegen. Zie [een door de gebruiker toegewezen beheerde identiteit maken in de Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)voor meer informatie.

* Een hulp programma dat u kunt gebruiken om uw ISE te maken door de Logic Apps REST API aan te roepen met een HTTPS-aanvraag. U kunt bijvoorbeeld [postman](https://www.getpostman.com/downloads/)gebruiken of u kunt een logische app maken die deze taak uitvoert.

## <a name="create-the-ise"></a>De ISE maken

Als u uw ISE wilt maken door de Logic Apps REST API aan te roepen, maakt u deze HTTPS-aanvraag:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Voor de versie Logic Apps REST API 2019-05-01 moet u uw eigen HTTP PUT-aanvraag voor ISE-connectors maken.

Het volt ooien van de implementatie duurt gewoonlijk binnen twee uur. De implementatie kan af en toe Maxi maal vier uur duren. Als u de implementatie status wilt controleren, selecteert u in de [Azure Portal](https://portal.azure.com)op de Azure-werk balk het meldingen pictogram, waarmee het deel venster meldingen wordt geopend.

> [!NOTE]
> Als de implementatie mislukt of als u uw ISE verwijdert, kan het tot een uur duren voordat de subnetten worden vrijgegeven. Deze vertraging betekent dat u mogelijk moet wachten voordat u deze subnetten opnieuw gebruikt in een andere ISE.
>
> Als u het virtuele netwerk verwijdert, duurt het over het algemeen Maxi maal twee uur voordat de subnetten worden vrijgegeven, maar deze bewerking kan langer duren. 
> Wanneer u virtuele netwerken verwijdert, moet u ervoor zorgen dat er nog geen resources zijn verbonden. 
> Zie [virtueel netwerk verwijderen](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Aanvraagheader

Neem de volgende eigenschappen op in de aanvraag header:

* `Content-type`: Stel de waarde van deze eigenschap in op `application/json` .

* `Authorization`: Stel deze eigenschaps waarde in op het Bearer-token voor de klant die toegang heeft tot het Azure-abonnement of de resource groep die u wilt gebruiken.

<a name="request-body"></a>

## <a name="request-body"></a>Aanvraagbody

Geef in de hoofd tekst van de aanvraag de resource definitie op die moet worden gebruikt voor het maken van uw ISE, met inbegrip van informatie over aanvullende mogelijkheden die u wilt inschakelen voor uw ISE, bijvoorbeeld:

* Als u een ISE wilt maken die het gebruik van een zelfondertekend certificaat dat is geïnstalleerd op de `TrustedRoot` locatie, neemt u het `certificates` object op in de `properties` sectie ISE-definitie, zoals verderop in dit artikel wordt beschreven.

  Als u deze mogelijkheid wilt inschakelen op een bestaande ISE, kunt u een PATCH aanvraag alleen voor het `certificates` object verzenden. Zie voor meer informatie over het gebruik van zelfondertekende certificaten [beveiligde toegang en gegevens toegang voor uitgaande oproepen naar andere services en systemen](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

* Als u een ISE wilt maken die gebruikmaakt van een door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteit, neemt u het `identity` object op met het beheerde identiteits type en andere vereiste informatie in de ISE-definitie, zoals verderop in dit artikel wordt beschreven.

* Als u een ISE wilt maken die gebruikmaakt van door de klant beheerde sleutels en Azure Key Vault voor het versleutelen van gegevens in rust, neemt u de informatie op die door de [klant beheerde sleutel ondersteuning mogelijk maakt](customer-managed-keys-integration-service-environment.md). U kunt alleen door de klant beheerde sleutels instellen wanneer deze worden *gemaakt*.

### <a name="request-body-syntax"></a>Syntaxis van aanvraag tekst

Hier volgt de syntaxis van de hoofd tekst van de aanvraag, waarin de eigenschappen worden beschreven die moeten worden gebruikt bij het maken van uw ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Voor beeld van aanvraag tekst

In dit voor beeld van de aanvraag tekst worden de voorbeeld waarden weer gegeven:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integratieserviceomgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
