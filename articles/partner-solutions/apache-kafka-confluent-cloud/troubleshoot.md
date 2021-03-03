---
title: Problemen oplossen Apache Kafka voor confluente Cloud-Azure-partner oplossingen
description: Dit artikel bevat informatie over het oplossen van problemen en veelgestelde vragen voor confluente Cloud op Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/18/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: b1e4b06fcbecf11d7d5f58a583fe3bd6643d99ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709390"
---
# <a name="troubleshooting-apache-kafka-for-confluent-cloud-solutions"></a>Problemen met Apache Kafka voor confluente cloud oplossingen oplossen

Dit document bevat informatie over het oplossen van problemen met de oplossingen die Apache Kafka voor confluente Cloud gebruiken.

Als u geen antwoord vindt of een probleem niet kunt oplossen, [maakt u een aanvraag via de Azure Portal](manage.md#get-support) of neemt u contact op met de [ondersteuning](https://support.confluent.io).

## <a name="cant-find-offer-in-the-marketplace"></a>Kan geen aanbieding in Marketplace vinden

Gebruik de volgende stappen om de aanbieding te vinden in de Azure Marketplace:

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een resource maken**.
1. Zoeken naar _Apache Kafka in confluente Cloud_.
1. Selecteer de tegel toepassing.

Als de aanbieding niet wordt weer gegeven, neemt u contact op met de [ondersteuning voor Confluent](https://support.confluent.io). De Tenant-ID van uw Azure Active Directory moet op de lijst met toegestane tenants staan. Zie [uw Azure Active Directory Tenant-id vinden](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)voor meer informatie over het vinden van uw Tenant-id.

## <a name="purchase-errors"></a>Inkoop fouten

* De aankoop mislukt omdat een geldige credit card niet is verbonden met het Azure-abonnement of omdat er geen betalings methode aan het abonnement is gekoppeld.

  Gebruik een ander Azure-abonnement. Of Voeg de credit card of betalings methode voor het abonnement toe of werk deze bij. Zie [de credit-en betalings methode bijwerken](../../cost-management-billing/manage/change-credit-card.md)voor meer informatie.

* Het EA-abonnement staat geen Marketplace-aankopen toe.

  Gebruik een ander abonnement. U kunt ook controleren of uw EA-abonnement is ingeschakeld voor Marketplace-aankopen. Zie [Marketplace-aankopen inschakelen](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)voor meer informatie. Als het probleem hiermee niet is opgelost, neemt u contact op met de [ondersteuning](https://support.confluent.io).

## <a name="conflict-error"></a>Conflict fout

Als u zich eerder hebt geregistreerd voor confluente Cloud, moet u een nieuw e-mail adres gebruiken om een andere resource in de Cloud organisatie te maken. Wanneer u een eerder geregistreerd e-mail adres gebruikt, ontvangt u een **conflict** fout. Meld u opnieuw aan met een nieuw e-mail adres.

## <a name="deploymentfailed-error"></a>Heeft-fout

Als u een **heeft** -fout krijgt, controleert u de status van uw Azure-abonnement. Zorg ervoor dat deze niet is onderbroken en geen problemen heeft met de facturering.

## <a name="resource-isnt-displayed"></a>De resource wordt niet weer gegeven

Als het **overzicht** of **verwijderen** van Blades voor confluente Cloud niet wordt weer gegeven in de portal, kunt u proberen de pagina te vernieuwen. Deze fout kan worden veroorzaakt door een regel matig probleem met de portal. Als dat niet werkt, neemt u contact op met de [ondersteuning voor Confluent](https://support.confluent.io).

Als de confluente Cloud resource niet wordt gevonden in de lijst **alle resources** van Azure, neemt u contact op met de [ondersteuning voor confluentie](https://support.confluent.io).

## <a name="resource-creation-takes-long-time"></a>Het maken van resources neemt lange tijd in beslag

Als het implementatie proces meer dan drie uur duurt, neemt u contact op met de ondersteuning.

Als de implementatie mislukt en de confluente Cloud resource de status heeft `Failed` , verwijdert u de resource. Probeer na het verwijderen opnieuw of u de resource hebt gemaakt.

## <a name="offer-plan-doesnt-load"></a>Het aanbiedings plan wordt niet geladen

Deze fout kan een onregelmatige oorzaak zijn van de Azure Portal. Probeer de aanbieding opnieuw te implementeren.

## <a name="unable-to-delete"></a>Kan

Als u geen confluente resources kunt verwijderen, controleert u of u gemachtigd bent om de resource te verwijderen. U moet micro soft. confluente/*/Delete acties mogen uitvoeren. Zie Azure-roltoewijzingen weer geven [met behulp van de Azure Portal](../../role-based-access-control/role-assignments-list-portal.md)voor meer informatie over het bekijken van machtigingen.

Als u de juiste machtigingen hebt, maar de resource nog steeds niet kunt verwijderen, neemt u contact op met de [ondersteuning](https://support.confluent.io). Deze voor waarde kan zijn gerelateerd aan het retentie beleid voor confluente. Met confluente ondersteuning kunt u de organisatie en het e-mail adres voor u verwijderen.

## <a name="unable-to-use-single-sign-on"></a>Kan eenmalige aanmelding niet gebruiken

Als SSO niet werkt voor de confluente Cloud SaaS-Portal, controleert u of u de juiste Azure Active Directory-e-mail gebruikt. U moet ook toestemming hebben om toegang toe te staan voor de SaaS-Portal (cloud software as a Service). Zie de [richt lijnen voor eenmalige aanmelding](manage.md#single-sign-on)voor meer informatie.

Als het probleem zich blijft voordoen, neemt u contact op met de [ondersteuning voor Confluent](https://support.confluent.io).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van uw exemplaar](manage.md) van Apache Kafka voor confluente Cloud.
