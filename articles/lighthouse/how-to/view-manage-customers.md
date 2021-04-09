---
title: Klanten en gedelegeerde resources in het Azure Portal weer geven en beheren
description: Als service provider of onderneming die Azure Lighthouse gebruikt, kunt u al uw gedelegeerde resources en abonnementen weer geven door naar mijn klanten te gaan in de Azure Portal.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419326"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Klanten en gedelegeerde resources in het Azure Portal weer geven en beheren

Service providers die [Azure Lighthouse](../overview.md) gebruiken, kunnen de pagina **mijn klanten** in het [Azure Portal](https://portal.azure.com) gebruiken om gedelegeerde klant resources en abonnementen weer te geven.

> [!TIP]
> Hoewel we hier naar service providers en klanten verwijzen, kunnen [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) , hetzelfde proces gebruiken om hun beheer ervaring te consolideren.

Als u toegang wilt krijgen tot de pagina **mijn klanten** in de Azure Portal, selecteert u **alle services**, zoekt u naar **mijn klanten** en selecteert u deze. U kunt deze ook vinden door ' mijn klanten ' in te voeren in het zoekvak boven aan de Azure Portal.

Houd er rekening mee dat de meeste **klanten** sectie van de pagina **mijn klanten** alleen informatie weer geven over klanten die zijn gedelegeerde abonnementen of resource groepen aan uw Azure Active Directory-Tenant (Azure AD) via Azure Lighthouse. Als u met andere klanten werkt (bijvoorbeeld door het [CSP-programma (Cloud Solution Provider](/partner-center/csp-overview))), ziet u geen informatie over deze klanten in het **gedeelte klanten** , tenzij u de resources hebt ingewisseld [naar Azure Lighthouse](onboard-customer.md) (hoewel er mogelijk details van bepaalde CSP-klanten in de sectie voor de [Cloud Solution Provider (preview)](#cloud-solution-provider-preview) lager op de pagina worden weer gegeven).

> [!NOTE]
> Uw klanten kunnen informatie over service providers weer geven door te navigeren naar **service providers** in de Azure Portal. Zie [service providers weer geven en beheren](view-manage-service-providers.md)voor meer informatie.

## <a name="view-and-manage-customer-details"></a>Klant gegevens weer geven en beheren

Selecteer **klanten** aan de linkerkant van de pagina **mijn klanten** om klant gegevens weer te geven.

> [!IMPORTANT]
> Als u deze informatie wilt weer geven, moeten gebruikers beschikken over de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) (of een andere ingebouwde rol die lezers toegang bevat) in het voorbereidings proces.

Voor elke klant ziet u de naam van de klant, de klant-ID (Tenant-ID) en de **aanbiedings-id** en **aanbiedings versie** die aan de afspraak is gekoppeld. In de kolom **delegaties** ziet u het aantal gedelegeerde abonnementen en/of het aantal gedelegeerde resource groepen.

Met de opties boven aan de pagina kunt u uw klant gegevens sorteren, filteren en groeperen op specifieke klanten, aanbiedingen of tref woorden.

U kunt de volgende informatie op deze pagina bekijken:

- Als u alle abonnementen, aanbiedingen en delegaties wilt zien die zijn gekoppeld aan een klant, selecteert u de naam van de klant.
- Als u meer informatie over een aanbieding en de delegaties wilt zien, selecteert u de naam van het aanbod.
- Als u meer informatie over roltoewijzingen voor gedelegeerde abonnementen of resource groepen wilt weer geven, selecteert u de vermelding in de kolom **delegaties** .

## <a name="view-and-manage-delegations"></a>Delegaties weer geven en beheren

Delegaties tonen het abonnement of de resource groep die is gedelegeerd, samen met de gebruikers en machtigingen die er toegang toe hebben. Als u deze informatie wilt weer geven, selecteert u **delegaties** aan de linkerkant van de pagina **mijn klanten** .

Met de opties boven aan de pagina kunt u deze informatie sorteren, filteren en groeperen op specifieke klanten, aanbiedingen of tref woorden.

### <a name="view-role-assignments"></a>Roltoewijzingen weergeven

De gebruikers en machtigingen die aan elke delegatie zijn gekoppeld, worden weer gegeven in **de kolom roltoewijzingen** . U kunt elk item selecteren om de volledige lijst met gebruikers, groepen en service-principals weer te geven waaraan toegang is verleend tot het abonnement of de resource groep. Hier kunt u een bepaalde gebruiker, groep of Service Principal Name selecteren om meer informatie weer te geven.

### <a name="remove-delegations"></a>Delegaties verwijderen

Als u gebruikers met de functie voor het verwijderen van de [registratie toewijzing van beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) bij het onboarden van een klant hebt opgenomen in azure Lighthouse, kunnen deze gebruikers een overdracht verwijderen door het pictogram Prullenbak te selecteren dat wordt weer gegeven in de rij voor die delegering. Als dit het geval is, kunnen gebruikers in de Tenant van de service provider geen toegang krijgen tot de resources die eerder zijn gedelegeerd.

Zie [toegang tot een overdracht verwijderen](remove-delegation.md)voor meer informatie.

## <a name="view-delegation-change-activity"></a>Wijzigings activiteit voor overdracht weer geven

De sectie **activiteiten logboek** van de pagina **mijn klanten** houdt elke keer bij dat klant abonnementen of resource groepen worden gedelegeerd aan uw Tenant en elke keer dat eerder gedelegeerde resources worden verwijderd. Deze informatie kan alleen worden weer gegeven door gebruikers aan wie [de rol bewakings lezer is toegewezen in het hoofd bereik](monitor-delegation-changes.md).

Zie [delegatie wijzigingen weer geven in de Azure Portal](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal)voor meer informatie.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Werken in de context van een gedelegeerd abonnement

U kunt rechtstreeks in de context van een gedelegeerd abonnement in de Azure Portal werken zonder de directory waarin u bent aangemeld te scha kelen. Hiervoor doet u het volgende:

1. Selecteer het pictogram voor het adres van de **map en het abonnement** boven aan de Azure Portal.
2. Zorg ervoor dat in het **standaard abonnements filter** alleen het vak voor dat gedelegeerd abonnement is geselecteerd. U kunt de vervolg keuzelijst **huidige en gedelegeerde directory's** gebruiken om alleen abonnementen in een specifieke map weer te geven. (Gebruik niet de optie **Directory overschakelen** , want Hiermee wijzigt u de map waarin u bent aangemeld.)

Als u vervolgens een service opent die ondersteuning biedt voor [Cross-Tenant beheer](../concepts/cross-tenant-management-experience.md), wordt de service standaard ingesteld op de context van het overgedragen abonnement dat u hebt geselecteerd. U kunt dit wijzigen door de bovenstaande stappen te volgen en de **selectie** vakjes in te scha kelen (of een of meer abonnementen kiezen om in plaats daarvan te werken).

> [!NOTE]
> Als u toegang hebt gekregen tot een of meer resource groepen, in plaats van toegang tot een volledig abonnement, selecteert u het abonnement waarvan de resource groep deel uitmaakt. U werkt vervolgens in de context van dat abonnement, maar heeft alleen toegang tot de aangewezen resource groepen.

U hebt ook toegang tot functionaliteit die betrekking heeft op gedelegeerde abonnementen of resource groepen vanuit Services die ondersteuning bieden voor cross-Tenant beheer door het abonnement of de resource groep te selecteren in de service.

## <a name="cloud-solution-provider-preview"></a>Cloud Solution Provider (preview-versie)

Een afzonderlijke **Cloud Solution Provider (preview)** -sectie van de pagina **mijn klanten** bevat factuur gegevens en resources voor uw CSP-klanten die [de micro soft-klant overeenkomst (MCA) hebben ondertekend](/partner-center/confirm-customer-agreement) en [onder het Azure-abonnement](/partner-center/azure-plan-get-started)vallen. Zie [aan de slag met uw micro soft-partner overeenkomst](../../cost-management-billing/understand/mpa-overview.md)voor meer informatie.

Dergelijke CSP-klanten worden in deze sectie weer gegeven, ongeacht of u ze ook hebt opvolgd voor Azure Lighthouse. Op dezelfde manier hoeft een CSP-klant niet te worden weer gegeven in de sectie **Cloud Solution Provider (preview)** van **mijn klanten** , zodat u deze kunt opheffen in azure Lighthouse.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerervaring in meerdere tenants](../concepts/cross-tenant-management-experience.md).
- Meer informatie over hoe uw klanten [service providers kunnen weer geven en beheren](view-manage-service-providers.md) door naar **service providers** te gaan in de Azure Portal.
