---
title: Serviceproviders weergeven en beheren
description: Klanten kunnen de pagina service providers in het Azure Portal gebruiken om informatie over service providers, aanbiedingen van providers en gedelegeerde resources weer te geven.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f6ee5fb154d75ff715acf99c5184cd1652ccdb80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555577"
---
# <a name="view-and-manage-service-providers"></a>Serviceproviders weergeven en beheren

De pagina **service providers** in de [Azure Portal](https://portal.azure.com) biedt klanten controle en zicht baarheid voor hun service providers die [Azure Lighthouse](../overview.md)gebruiken. Klanten kunnen Details over service providers weer geven, specifieke resources delegeren, zoeken naar nieuwe aanbiedingen, toegang tot service providers verwijderen, enzovoort.

Als u de pagina **service providers** wilt weer geven in de Azure Portal, selecteert u **alle services**, zoekt u naar **service providers** en selecteert u deze. U kunt deze pagina ook vinden door ' service providers ' of ' Azure Lighthouse ' in te voeren in het zoekvak boven aan de Azure Portal.

> [!NOTE]
> Als u de pagina **service providers** wilt weer geven, moet een gebruiker in de Tenant van de klant beschikken over de [ingebouwde rol](../../role-based-access-control/built-in-roles.md#reader) van de lezer (of een andere ingebouwde rol die lees toegang heeft).
>
> Om aanbiedingen toe te voegen of bij te werken, resources te delegeren en Voorst Ellen te verwijderen, moet de gebruiker een rol hebben met de `Microsoft.Authorization/roleAssignments/write` machtiging, zoals de [eigenaar](../../role-based-access-control/built-in-roles.md#owner).

Houd er wel op dat op de pagina **service providers** alleen informatie wordt weer gegeven over de service providers die toegang hebben tot de abonnementen of resource groepen van de klant via Azure Lighthouse. Als een klant werkt met aanvullende service providers die geen Azure Lighthouse gebruiken, ziet u hier geen informatie over die service providers.

## <a name="view-service-provider-details"></a>Details van service provider weer geven

Als u details wilt weer geven over de huidige service providers die Azure Lighthouse gebruiken om te werken aan de Tenant van de klant, selecteert u aanbiedingen voor de **service provider** aan de linkerkant van de pagina **service providers** .

Voor elke aanbieding ziet u de naam van de service provider en de aanbieding die eraan is gekoppeld. U kunt een aanbieding selecteren om een beschrijving en andere details te bekijken, met inbegrip van de roltoewijzingen die de service provider heeft verleend.

In de kolom **delegaties** kunt u zien hoeveel abonnementen en/of resource groepen zijn overgedragen aan de service provider voor die aanbieding. De service provider kan deze abonnementen en/of resource groepen openen en beheren op basis van de toegangs niveaus die zijn opgegeven in de aanbieding.

## <a name="add-or-remove-service-provider-offers"></a>Aanbiedingen voor service providers toevoegen of verwijderen

Als u een nieuwe aanbieding voor een service provider wilt toevoegen vanaf de pagina aanbiedingen van de **service provider** , selecteert u **aanbieding toevoegen**. Selecteer **privé aanbiedingen** om aanbiedingen te bekijken die een service provider heeft gepubliceerd voor deze klant. U kunt vervolgens die aanbieding selecteren in het scherm **persoonlijke aanbiedingen** en vervolgens **instellen en abonneren** selecteren.

U kunt een aanbieding van een service provider op elk gewenst moment verwijderen door het prullenbak pictogram te selecteren in de rij voor die aanbieding. Nadat de verwijdering is bevestigd, heeft die service provider geen toegang meer tot de resources die voorheen zijn gedelegeerd voor die aanbieding.

## <a name="delegate-resources"></a>Resources delegeren

Voordat een service provider de resources van een klant kan openen en beheren, moeten een of meer specifieke abonnementen en/of resource groepen worden gedelegeerd. Als een klant een aanbieding heeft geaccepteerd, maar nog geen resources heeft overgedragen, ziet u boven aan de sectie aanbiedingen van de **service provider** een opmerking. Zo kan de klant weten dat hij actie moet ondernemen voordat de service provider toegang kan krijgen tot de resources van de klant.

Abonnementen of resource groepen delegeren:

1. Schakel het selectie vakje in voor de rij met de service provider, de aanbieding en de naam. Selecteer vervolgens **resources delegeren** boven aan het scherm.
1. Bekijk de details over de service provider en aanbieding in het gedeelte Details van de **aanbieding** van de pagina **gemachtigde resources** . Als u roltoewijzingen voor de aanbieding wilt controleren, selecteert u **hier Klik hier om de details van de geselecteerde aanbieding te bekijken**.
1. Selecteer in de sectie **gemachtigde** de optie **abonnementen overdragen** of **resource groepen delegeren**.
1. Kies de abonnementen en/of resource groepen die u wilt delegeren voor deze aanbieding en selecteer vervolgens **toevoegen**.
1. Schakel het selectie vakje onder aan de pagina in om te bevestigen dat u deze service provider toegang wilt geven tot de resources die u hebt geselecteerd, en selecteer vervolgens **delegeren**.

## <a name="update-service-provider-offers"></a>Aanbiedingen van service providers bijwerken

Nadat een klant een aanbieding heeft toegevoegd, kan een service provider een bijgewerkte versie van dezelfde aanbieding publiceren op Azure Marketplace. Het is bijvoorbeeld mogelijk dat een nieuwe roldefinitie moet worden toegevoegd. Als een nieuwe versie van de aanbieding is gepubliceerd, wordt op de pagina aanbiedingen van de **service provider** het pictogram ' update ' weer gegeven in de rij voor die aanbieding. Selecteer dit pictogram voor een overzicht van de verschillen tussen de huidige versie van de aanbieding en de nieuwe.

 ![Pictogram aanbieding bijwerken](../media/update-offer.jpg)

Nadat u de wijzigingen hebt bekeken, kan de klant ervoor kiezen om bij te werken naar de nieuwe versie. De autorisaties en andere instellingen die zijn opgegeven in de nieuwe versie, worden toegepast op alle abonnementen en/of resource groepen die zijn gedelegeerd voor die aanbieding.

## <a name="view-delegations"></a>Delegaties weer geven

Delegaties vertegenwoordigen de roltoewijzingen die machtigingen verlenen aan de service provider voor de resources die een klant heeft gedelegeerd. Als u deze informatie wilt weer geven, selecteert u **delegaties** aan de linkerkant van de pagina **service providers** .

Met filters boven aan de pagina kunt u uw delegatie gegevens sorteren en groeperen. U kunt ook filteren op specifieke klanten, aanbiedingen of tref woorden.

> [!NOTE]
> Klanten kunnen deze roltoewijzingen of gebruikers van de Tenant van de service provider die deze rollen hebben verleend, niet zien bij het [weer geven van roltoewijzingen voor het gedelegeerde bereik in de Azure Portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) of via api's.

## <a name="audit-delegations-in-your-environment"></a>Audit overdrachten in uw omgeving

Klanten willen mogelijk inzicht krijgen in de abonnementen en/of resource groepen die zijn overgedragen aan Azure Lighthouse. Dit is met name handig voor klanten met een groot aantal abonnementen of met veel gebruikers die beheer taken uitvoeren.

We bieden een [Azure Policy ingebouwde beleids definitie](../../governance/policy/samples/built-in-policies.md#lighthouse) voor het [controleren van de overdracht van scopes naar een beheer Tenant](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). U kunt dit beleid toewijzen aan een beheer groep die alle abonnementen bevat die u wilt controleren. Wanneer u controleert op naleving van dit beleid, worden alle gedelegeerde abonnementen en/of resource groepen (binnen de beheer groep waaraan het beleid is toegewezen) weer gegeven in een niet-compatibele status. U kunt vervolgens de resultaten bekijken en controleren of er geen onverwachte delegaties zijn.

Met een andere [ingebouwde beleids definitie](../../governance/policy/samples/built-in-policies.md#lighthouse) kunt u [delegaties beperken tot specifieke beheer tenants](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). Dit beleid kan op dezelfde manier worden toegepast op een beheer groep die alle abonnementen bevat waarvoor u de delegaties wilt beperken. Nadat het beleid is geïmplementeerd, worden pogingen om een abonnement te delegeren naar een Tenant die u opgeeft, geweigerd.

Zie [Quick Start: een beleids toewijzing maken](../../governance/policy/assign-policy-portal.md)voor meer informatie over het toewijzen van een beleid en het weer geven van de resultaten van de nalevings status.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Lighthouse](../overview.md).
- Meer informatie over het controleren van de activiteit van de [service provider](view-service-provider-activity.md).
- Meer informatie over hoe service providers [klanten kunnen bekijken en beheren](view-manage-customers.md) op de pagina **mijn klanten** in de Azure Portal.
- Meer informatie over hoe [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) , Azure Lighthouse kunnen gebruiken om hun beheer ervaring te consolideren.

