---
title: API's voor automatisering van Azure-reserveringen | Microsoft Docs
description: Meer informatie over de Azure-API's die u kunt gebruiken voor het programmatisch ophalen van reserveringsgegevens.
author: yashesvi
ms.reviewer: yashesvi
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 773334787ec7b2706c16e517281d6a60215ad482
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353014"
---
# <a name="apis-for-azure-reservation-automation"></a>API's voor automatisering van Azure-reserveringen

Gebruik Azure-API's om programmatisch informatie voor uw organisatie te verkrijgen over de Azure-service of softwarereserveringen.

## <a name="find-reservation-plans-to-buy"></a>Reserveringsplannen zoeken om te kopen

Gebruik de reserveringsaanbeveling-API om aanbevelingen te krijgen voor welk reserveringsplan u moet kopen gebaseerd op het gebruik van uw organisatie. Zie [Reserveringsaanbevelingen krijgen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) voor meer informatie.

U kunt ook uw resourcegebruik analyseren met behulp van de gebruiksdetails van de verbruiks-API. Zie [Gebruiksdetails - Lijst voor factureringsperiode per factureringsaccount](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy) voor meer informatie. De Azure-resources die u consistent gebruikt, zijn doorgaans de beste kandidaten voor een reservering.

## <a name="buy-a-reservation"></a>Een reservering kopen

U kunt Azure-reserveringen en softwareabonnementen programmatisch aanschaffen met behulp van REST API's. Zie [Reserveringsorder - Aankoop-API](/rest/api/reserved-vm-instances/reservationorder/purchase) voor meer informatie.

Hier ziet u een voorbeeld van een aankoopaanvraag met behulp van de REST API:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Aanvraagtekst:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

U kunt ook een reservering aanschaffen in Azure Portal. Raadpleeg voor meer informatie de volgende artikelen:

Service-abonnementen:
- [Virtuele machine](../../virtual-machines/prepay-reserved-vm-instances.md?toc=%2fazure%2fbilling%2fTOC.json)
-  [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md?toc=/azure/billing/TOC.json)

Softwareabonnementen:
- [SUSE Linux-software](../../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Reserveringen ophalen

Als u een Azure-klant met een Enterprise Agreement (EA-klant) bent, kunt u de reserveringen die uw organisatie heeft gekocht, ophalen met behulp van de [API Transactiekosten voor gereserveerde instanties ophalen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Voor andere abonnementen kunt u de lijst met reserveringen die u hebt aangeschaft en weergavemachtigingen ophalen met behulp van de API [Reserveringsorder - Lijst](/rest/api/reserved-vm-instances/reservationorder/list). De accounteigenaar van de persoon die de reservering heeft aangeschaft, heeft standaard machtigingen voor het weergeven van de reservering.

## <a name="see-reservation-usage"></a>Zie reserveringsgebruik

Als u een EA-klant bent, kunt u programmatisch zien hoe de reserveringen in uw organisatie worden gebruikt. Zie [Gebruik van gereserveerde instanties ophalen voor zakelijke klanten](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) voor meer informatie. Gebruik de API [Samenvattingen van reserveringen - Lijst op reserveringsorder en reservering](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation) voor andere abonnementen.

Als u merkt dat de reserveringen van uw organisatie niet voldoende worden gebruikt:

- Zorg ervoor dat de virtuele machines die in uw organisatie worden gemaakt, overeenkomen met de VM-grootte die voor de reservering is bedoeld.
- Zorg ervoor dat Flexibiliteit van de instantiegrootte is ingeschakeld. Zie [Reserveringen beheren - De optimalisatie-instelling wijzigen voor gereserveerde VM-instanties](manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances) voor meer informatie.
- Wijzig het bereik van de reservering in Gedeeld voor een breder toepassingsgebied. Zie [Reserveringen beheren - Het bereik voor een reservering beheren](manage-reserved-vm-instance.md#change-the-reservation-scope) voor meer informatie.
- Ruil de niet-gebruikte hoeveelheid. Zie [Reserveringen beheren](manage-reserved-vm-instance.md) voor meer informatie.

## <a name="give-access-to-reservations"></a>Toegang geven tot reserveringen

Haal de lijst op met alle reserveringen waartoe een gebruiker toegang heeft met behulp van [Reservering - Bewerking - Lijst-API](/rest/api/reserved-vm-instances/reservationorder/list). Raadpleeg een van de volgende artikelen om programmatisch toegang tot een reservering te geven:

- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de REST API](../../role-based-access-control/role-assignments-rest.md)
- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure CLI](../../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Reserveringen splitsen of samenvoegen

Nadat u meer dan één resource-instantie in een reservering hebt aangeschaft, kunt u ervoor kiezen instanties in die reservering aan verschillende abonnementen toe te wijzen. U kunt het bereik van de reservering wijzigen zodat het van toepassing is op alle abonnementen binnen dezelfde factureringscontext. Maar voor kostenbeheer of budgettering is het mogelijk om het bereik als Enkel abonnement aan te blijven duiden en reserveringsinstanties aan een specifiek abonnement toe te wijzen.

Gebruik de API [Reservering - Splitsen](/rest/api/reserved-vm-instances/reservation/split) om een reservering te splitsen. U kunt ook een reservering splitsen met behulp van PowerShell. Zie [Reserveringen beheren - Reserveringen in twee reserveringen splitsen](manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations) voor meer informatie.

Gebruik de API [Reservering - Samenvoegen](/rest/api/reserved-vm-instances/reservation/merge) als u twee reserveringen in één reservering wilt samenvoegen.

## <a name="change-scope-for-a-reservation"></a>Het bereik van een reservering wijzigen

Het bereik van een reservering kan een enkel abonnement, een enkele resourcegroep of alle abonnementen in uw factureringscontext zijn. Als u het bereik instelt op Enkel abonnement of Enkele resourcegroep, wordt de reservering vergeleken met uitgevoerde resources in het geselecteerde abonnement. Als u het abonnement of de resourcegroep verwijdert of verplaatst, wordt de reservering niet gebruikt.  Als u het bereik instelt op Gedeeld, vergelijkt Azure de reservering met resources die worden uitgevoerd in alle abonnementen binnen de factureringscontext. De factureringscontext is afhankelijk van het abonnement dat u gebruikt om de reservering aan te schaffen. U kunt het bereik tijdens de aankoop selecteren of deze op elk gewenst moment ná de aankoop wijzigen. Zie [Reserveringen beheren - Het bereik wijzigen](manage-reserved-vm-instance.md#change-the-reservation-scope) voor meer informatie.

Gebruik de API [Reservering - Bijwerken](/rest/api/reserved-vm-instances/reservation/update) om het bereik programmatisch te wijzigen.

## <a name="learn-more"></a>Meer informatie

- [Wat zijn reserveringen voor Azure](save-compute-costs-reservations.md)
- [Inzicht in de manier waarop de korting voor VM-reserveringen wordt toegepast](../manage/understand-vm-reservation-charges.md)
- [Inzicht in de manier waarop de korting voor het SUSE Linux Enterprise-softwareabonnement wordt toegepast](understand-suse-reservation-charges.md)
- [Inzicht in de manier waarop andere reserveringskortingen worden toegepast](understand-reservation-charges.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
- [Kosten van Windows-software zijn niet inbegrepen bij reserveringen](reserved-instance-windows-software-costs.md)
- [Azure-reserveringen in CSP-programma (Cloud Solution Provider) van partnercentrum](/partner-center/azure-reservations)