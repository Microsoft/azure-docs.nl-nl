---
title: Besparen op SAP HANA Large Instances met een Azure-reservering
description: Lees hier meer informatie over de dingen die u moet weten voordat u een reservering van een HANA Large Instance koopt en hoe u de aankoop kunt doen.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/19/2021
ms.author: banders
ms.openlocfilehash: 579c7b5f66f52551e2504101406e1f7b2bb69e92
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775524"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Besparen op SAP HANA Large Instances met een Azure-reservering

U kunt besparen op de kosten van uw SAP HANA Large Instances (HLI) door voor een periode van één of drie jaar vooraf Azure-reserveringen te kopen. De reserveringskorting wordt toegepast op de ingerichte HLI-SKU die overeenkomt met de aangeschafte gereserveerde instantie. In dit artikel vindt u informatie over de dingen die u moet weten voordat u een reservering koopt en hoe u de aankoop kunt doen.

Door een reservering te kopen, verplicht u zich de HLI één of drie jaar te gebruiken. De aankoop van de gereserveerde HLI-capaciteit geldt voor de compute en NFS-opslag die zijn gebundeld met de SKU. De reservering omvat niet de licentiekosten voor software zoals het besturingssysteem, SAP of extra opslagkosten. De reserveringskorting wordt automatisch toegepast op de ingerichte SAP-HLI. Wanneer de reserveringsperiode afloopt, gelden de tarieven voor betalen naar gebruik voor de ingerichte resource.

## <a name="purchase-considerations"></a>Aankoopoverwegingen

U moet een HLI-SKU inrichten voordat u de gereserveerde capaciteit gaat kopen. U betaalt vooraf voor de reservering of per maand. De volgende beperkingen gelden voor gereserveerde HLI-capaciteit:

- Reserveringskortingen gelden alleen voor abonnementen die vallen onder een Enterprise Agreement of Microsoft-klantovereenkomst. Andere abonnementen worden niet ondersteund.
- Bij gereserveerde HLI-capaciteit is het niet mogelijk om de grootte van de instantie aan te passen. Een reservering geldt alleen voor de SKU en de regio waarvoor u de reservering koopt.
- Self-service annulering en uitwisseling worden niet ondersteund.
- Het gereserveerde capaciteitsbereik is een enkel bereik en geldt dus voor één abonnement en één resourcegroep. De aangeschafte capaciteit kan niet worden bijgewerkt voor gebruik door een ander abonnement.
- U kunt geen gedeeld reserveringsbereik gebruiken met gereserveerde HANA-capaciteit. U kunt een reserveringsbereik niet splitsen, samenvoegen of bijwerken.
- U kunt één HLI tegelijk kopen met behulp van de API-aanroepen voor gereserveerde capaciteit. Voer extra API-aanroepen uit om extra hoeveelheden te kopen.

U kunt gereserveerde capaciteit kopen in de Azure-portal of door gebruik te maken van de [REST-API](/rest/api/reserved-vm-instances/reservationorder/purchase).

## <a name="buy-a-hana-large-instance-reservation"></a>Een reservering voor HANA Large Instance kopen

Gebruik de volgende informatie om een HLI-reservering te kopen met de [REST-API's ReservationOrder](/rest/api/reserved-vm-instances/reservationorder/purchase).

### <a name="get-the-reservation-order-and-price"></a>De reserveringsorder en prijs opvragen

Haal eerst de reserveringsorder en prijs op voor de ingerichte SKU van HANA Large Instance. Dit kan met behulp van de API [calculatePrice](/rest/api/reserved-vm-instances/reservationorder/calculate).

In het volgende voorbeeld wordt [armclient](https://github.com/projectkudu/ARMClient) gebruikt om REST API-aanroepen te maken met PowerShell. Hieronder ziet u hoe de reserveringsorder, de aanroep van de API calculatePrice en de tekst van de aanvraag eruit moeten zien:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'billingplan': 'Monthly'
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Zie [HLI-reserveringsvelden](#hli-reservation-fields) voor meer informatie over gegevensvelden en de bijbehorende beschrijvingen.

Het volgende voorbeeldantwoord lijkt op wat u te zien krijgt. Noteer de waarde die is geretourneerd voor `quoteId`.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>De aankoop uitvoeren

Maak uw aankoop met behulp van de geretourneerde `quoteId` en de `reservationOrderId` die u hebt opgevraagd in de voorgaande sectie [De reserveringsorder en prijs opvragen](#get-the-reservation-order-and-price).

Hier ziet u een voorbeeld van een aanvraag:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
       'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
               'billingplan': 'Monthly'

        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Hier ziet u een voorbeeld van een antwoord. Als de order is geplaatst, moet `provisioningState` de waarde `creating` hebben.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Controleren of de aankoop is gelukt

Voer de opdracht GET uit op de reserveringsorder om de status van de inkooporder te bekijken. `provisioningState` moet zijn ingesteld op `Succeeded`.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

Het antwoord moet lijken op het volgende voorbeeld.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>HLI-reserveringsvelden

Hier worden de verschillende reserveringsvelden besproken.

  **sku**: de naam van de HLI-SKU. Deze ziet er uit als `SAP_HANA_On_Azure_<SKUname>`.

  **location**: de beschikbare HLI-regio's. Zie [Beschikbare SKU's voor SAP HANA in Azure (Large Instances)](../../virtual-machines/workloads/sap/hana-available-skus.md) voor de beschikbare regio's. Als u de notatie van de tekenreeks voor de locatie wilt opvragen, gebruikt u [GET met de API locations](/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **reservedResourceType**: `SapHana`

  **subscription**: het abonnement dat wordt gebruikt voor het betalen van de reservering. Via de betalingswijze voor het abonnement worden de kosten voor de reservering in rekening gebracht. Het abonnementstype moet een Enterprise-overeenkomst zijn (nummers van aanbieding: MS-AZR-0017P or MS-AZR-0148P) of Microsoft-klantovereenkomst. De kosten worden in mindering gebracht op het saldo van Azure-vooruitbetaling (voorheen financiële toezegging), indien beschikbaar, of in rekening gebracht als overschrijding.

  **scope**: het bereik van de reservering moet een enkel bereik zijn.

  **term**: één jaar of drie jaar. Dit wordt aangegeven als `P1Y` of `P3Y`.

  **quantity**: het aantal exemplaren dat wordt gekocht voor de reservering. De te kopen hoeveelheid is één HLI per keer. Voor extra reserveringen herhaalt u de API-aanroep met de bijbehorende velden.

## <a name="troubleshoot-errors"></a>Problemen oplossen

Er wordt mogelijk een fout zoals in het volgende voorbeeld weergegeven wanneer u een reserveringsaankoop doet. De mogelijke oorzaak is dat de HLI nog niet is ingericht voor de aankoop. Als dit het geval is, neemt u contact op met uw Microsoft-accountteam om een HLI in te richten voordat u een reserveringsaankoop gaat doen.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [aanroepen van Azure REST-API's met Postman en cURL](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Zie [Beschikbare SKU's voor SAP HANA in Azure (Large Instances)](../../virtual-machines/workloads/sap/hana-available-skus.md) voor een overzicht van de beschikbare SKU's en regio's.