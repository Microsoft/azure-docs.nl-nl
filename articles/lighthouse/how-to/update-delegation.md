---
title: Een delegatie bijwerken
description: Meer informatie over het bijwerken van een delegering voor een klant die eerder is geboardd naar Azure Lighthouse.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f0ed5222cdbac3d0e4d193941c2a6f233d15938c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555769"
---
# <a name="update-a-delegation"></a>Een delegatie bijwerken

Nadat u een abonnement (of resource groep) naar Azure Lighthouse hebt voor bereid, moet u mogelijk wijzigingen aanbrengen. Uw klant wil bijvoorbeeld dat u aanvullende beheer taken moet uitvoeren waarvoor een andere ingebouwde rol van Azure is vereist, of u moet mogelijk de Tenant wijzigen waaraan een klant abonnement is overgedragen.

> [!TIP]
> Hoewel we in dit onderwerp naar service providers en klanten verwijzen, kunnen [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) , hetzelfde proces gebruiken voor het instellen van Azure Lighthouse en het samen voegen van hun beheer ervaring.

Als u [uw klant hebt ingepakt via Azure Resource Manager sjablonen (arm-sjablonen)](onboard-customer.md), moet voor die klant een nieuwe implementatie worden uitgevoerd. Afhankelijk van wat u wilt wijzigen, kunt u de oorspronkelijke aanbieding bijwerken of de oorspronkelijke aanbieding verwijderen en een nieuwe maken.

- **Als u alleen autorisaties wijzigt**: u kunt uw overdracht bijwerken door alleen de sectie **autorisaties** van de arm-sjabloon te wijzigen.
- **Als u de beheer-Tenant wijzigt**: u moet een nieuwe arm-sjabloon maken die gebruikmaakt van een andere **mspOfferName** dan uw vorige aanbieding.

## <a name="update-your-arm-template"></a>Uw ARM-sjabloon bijwerken

Als u de overdracht wilt bijwerken, moet u een ARM-sjabloon implementeren die de wijzigingen bevat die u wilt aanbrengen.

Als u alleen autorisaties bijwerkt (zoals het toevoegen van een nieuwe gebruikers groep met een rol die u eerder hebt opgenomen, of als u de rol voor een bestaande gebruiker hebt gewijzigd), kunt u dezelfde **mspOfferName** gebruiken als in de [arm-sjabloon](onboard-customer.md#create-an-azure-resource-manager-template) die u voor de vorige overdracht hebt gebruikt. U kunt uw vorige sjabloon als uitgangs punt gebruiken. Breng vervolgens de gewenste wijzigingen aan, zoals het vervangen van een ingebouwde rol van Azure met een andere, of het toevoegen van een volledig nieuwe autorisatie aan de sjabloon.

Als u de **mspOfferName** wijzigt, wordt dit beschouwd als een nieuwe, afzonderlijke aanbieding. Dit is vereist als u de beheer-Tenant wijzigt.

Het is niet nodig om de **mspOfferName** te wijzigen als de beheer Tenant hetzelfde blijft. In de meeste gevallen raden we u aan om slechts één **mspOfferName** te gebruiken door dezelfde klant en Tenant te beheren. Als u ervoor kiest om het toch te wijzigen, moet u ervoor zorgen dat de vorige delegering van de klant wordt verwijderd voordat u de nieuwe implementeert.

## <a name="remove-the-previous-delegation"></a>De vorige overdracht verwijderen

Voordat u een nieuwe implementatie uitvoert, wilt u mogelijk [de toegang tot de vorige overdracht verwijderen](remove-delegation.md). Dit zorgt ervoor dat alle vorige machtigingen worden verwijderd, zodat u kunt beginnen met de exacte gebruikers/groepen en rollen die doorlopend moeten worden toegepast.

> [!IMPORTANT]
> Als u een nieuwe **mspOfferName** gebruikt en dezelfde **principalId** -waarden behoudt, moet u de toegang tot de vorige overdracht verwijderen voordat u de nieuwe aanbieding implementeert. Als u de aanbieding eerst niet verwijdert, is het mogelijk dat gebruikers die eerder toestemming hebben gekregen, de toegang niet volledig kunnen verliezen door conflicterende toewijzingen.

Als u de beheer Tenant wijzigt, kunt u de vorige aanbieding op de plaats houden als u wilt dat beide tenants toch toegang hebben. Als u alleen de nieuwe Tenant voor het beheren van toegang wilt hebben, moet de eerdere aanbieding worden verwijderd. Dit kan vóór of na de onboarding van de nieuwe aanbieding plaatsvinden.

Als u de aanbieding bijwerkt om autorisaties alleen aan te passen en dezelfde **mspOfferName** behouden, hoeft u de vorige overdracht niet te verwijderen. De nieuwe implementatie vervangt de vorige overdracht en alleen de autorisaties in de nieuwste sjabloon worden toegepast.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagram waarin wordt weer gegeven wanneer mspOfferName moet worden gewijzigd en een eerdere delegering wordt verwijderd.":::

Het verwijderen van de toegang tot de overdracht kan worden uitgevoerd door een gebruiker in de Tenant beheren die de rol voor het [verwijderen van beheerde services voor registratie toewijzing](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) in de oorspronkelijke overdracht heeft gekregen. Als er geen gebruiker in uw Tenant voor beheer deze rol heeft, kunt u de klant vragen om de [toegang tot de aanbieding te verwijderen in de Azure Portal](view-manage-service-providers.md#add-or-remove-service-provider-offers).

> [!TIP]
> Als u de vorige delegatie hebt verwijderd volgens de bovenstaande stappen en nog steeds niet de nieuwe ARM-sjabloon kunt implementeren, moet u [de registratie definitie mogelijk volledig verwijderen](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). Dit kan worden gedaan door elke gebruiker met een rol die de `Microsoft.Authorization/roleAssignments/write` machtiging heeft, zoals de [eigenaar](../../role-based-access-control/built-in-roles.md#owner), in de Tenant van de klant.  

## <a name="deploy-the-arm-template"></a>De ARM-sjabloon implementeren

De klant kan [de bijgewerkte sjabloon](onboard-customer.md#deploy-the-azure-resource-manager-templates) op dezelfde manier implementeren als voorheen: in het Azure Portal, met behulp van Power shell of met behulp van Azure cli.

Nadat de implementatie is voltooid, [controleert u of deze is geslaagd](onboard-customer.md#confirm-successful-onboarding). De bijgewerkte autorisaties worden vervolgens van kracht voor het abonnement of de resource groep (en) die de klant heeft gedelegeerd.

## <a name="updating-managed-service-offers"></a>De aanbiedingen van beheerde services worden bijgewerkt

Als u uw klant hebt ingecheckt via een beheerde service aanbieding die is gepubliceerd op Azure Marketplace en u de autorisatie wilt bijwerken, kunt u de overdracht bijwerken door [een nieuwe versie van uw aanbieding te publiceren](../../marketplace/partner-center-portal/update-existing-offer.md) met de [autorisaties](../../marketplace/plan-managed-service-offer.md) die u wilt gebruiken in het plan voor die klant. De klant kan vervolgens bijwerken naar de nieuwste versie in de Azure Portal.

Als u de beheer Tenant wilt wijzigen, moet u [een nieuwe beheerde service aanbieding maken en publiceren](../../marketplace/plan-managed-service-offer.md) zodat de klant deze kan accepteren.

> [!TIP]
> Zoals eerder vermeld, raden we u aan geen meerdere verschillende aanbiedingen te gebruiken tussen dezelfde klant en de Tenant beheren. Als u een nieuwe aanbieding voor dezelfde klant publiceert die dezelfde beheer Tenant gebruikt, moet u ervoor zorgen dat de eerdere aanbieding wordt verwijderd voordat de klant de nieuwe aanbieding aanvaardt.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk en beheer klanten](view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.
- Meer informatie over het [verwijderen van de toegang tot een overdracht](remove-delegation.md) die eerder is uitgevoerd.
