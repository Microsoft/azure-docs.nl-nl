---
title: Azure Cost Management-bereiken begrijpen en ermee werken
description: Dit artikel helpt u inzicht te krijgen in de bereiken voor facturering- en resourcebeheer die beschikbaar zijn in Azure en hoe u de bereiken gebruikt in Cost Management en API's.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 693045d094ddbf5ad6df81eca8cd16d9cac62fe4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308605"
---
# <a name="understand-and-work-with-scopes"></a>Bereiken begrijpen en gebruiken

Dit artikel helpt u inzicht te krijgen in de bereiken voor facturering- en resourcebeheer die beschikbaar zijn in Azure en hoe u de bereiken gebruikt in Cost Management en API's.

## <a name="scopes"></a>Bereiken

Een _Bereik_ is een knooppunt in de Azure-resourcehiërarchie waar Azure Active Directory-gebruikers services kunnen openen en beheren. De meeste Azure-resources worden gemaakt en geïmplementeerd in resourcegroepen, die deel uitmaken van abonnementen. Microsoft biedt ook twee hiërarchieën boven Azure-abonnementen met gespecialiseerde rollen voor het beheren van factureringsgegevens:
- Factureringsgegevens, zoals betalingen en facturen
- Cloud Services, zoals kosten en beleidsbeheer

Bereiken zijn de plekken waar u factureringsgegevens beheert, over rollen beschikt die specifiek zijn voor betalingen, facturen beheert en algemeen accountbeheer uitvoert. Facturerings- en accountrollen worden afzonderlijk beheerd met rollen die worden gebruikt voor resourcebeheer, die gebruikmaken van [Azure RBAC](../../role-based-access-control/overview.md). Om de intentie van de afzonderlijke bereiken duidelijk te onderscheiden, met inbegrip van de verschillen in toegangsbeheer, worden ze respectievelijk _factureringsbereiken_ en _Azure RBAC-bereiken_ genoemd.

Bekijk de video [Hiërarchieën instellen met Cost Management](https://www.youtube.com/watch?v=n3TLRaYJ1NY) voor meer informatie over bereiken. Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Hoe Cost Management bereiken gebruikt

Cost Management werkt in alle bereiken boven de resources om organisaties in staat te stellen kosten te beheren op het niveau waarop ze toegang hebben, ongeacht of dat nu de hele factureringsrekening of één resourcegroep is. Hoewel de factureringsbereiken verschillen op basis van uw Microsoft-overeenkomst (type abonnement), geldt dit niet voor de Azure RBAC-bereiken.

## <a name="azure-rbac-scopes"></a>Azure RBAC-bereiken

Azure ondersteunt drie bereiken voor resourcebeheer. Elk bereik biedt ondersteuning voor het beheer van toegang en governance, inclusief, maar niet beperkt tot, kostenbeheer.

- [**Beheergroepen**](../../governance/management-groups/overview.md) - Hiërarchische containers, maximaal acht niveaus, om Azure-abonnementen te organiseren.

    Resourcetype: [Microsoft.Management/managementGroups](/rest/api/managementgroups/)

- **Abonnementen** - Primaire containers voor Azure-resources.

    Resourcetype: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Resource groepen**](../../azure-resource-manager/management/overview.md#resource-groups) - Logische groeperingen van gerelateerde resources voor een Azure-oplossing die dezelfde levenscyclus delen. Bijvoorbeeld resources die samen worden geïmplementeerd en verwijderd.

    Resourcetype: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Met beheergroepen kunt u abonnementen indelen in een hiërarchie. U kunt bijvoorbeeld een logische organisatie hiërarchie maken met behulp van beheergroepen. Geef vervolgens teams abonnementen op voor productie- en dev/test-workloads. En maak vervolgens resourcegroepen in de abonnementen om elk subsysteem of onderdeel te beheren.

Door een organisatiehiërarchie te maken kunt u de kosten en beleidsnaleving per organisatie samenvoegen. Elke leidinggevende kan vervolgens hun huidige kosten bekijken en analyseren. En vervolgens kunnen ze budgetten maken om onjuiste bestedingspatronen te beperken en de kosten te optimaliseren met Advisor-aanbevelingen op het laagste niveau.

Het verlenen van toegang voor het weergeven van kosten en optioneel beheer van kostenconfiguratie, zoals budgetten en export bewerkingen, gebeurt op beheerbereiken met behulp van Azure RBAC. U gebruikt Azure RBAC om Azure Active Directory-gebruikers en -groepen toegang te verlenen tot het uitvoeren van een vooraf gedefinieerde set acties die zijn gedefinieerd in een rol voor een specifiek bereik en hieronder. Zo verleent een rol die is toegewezen aan een beheergroepsbereik ook dezelfde machtigingen voor geneste abonnementen en resourcegroepen.

Cost Management ondersteunt de volgende ingebouwde rollen voor elk bereik:

- [**Eigenaar**](../../role-based-access-control/built-in-roles.md#owner) – Kan kosten bekijken en alles beheren, met inbegrip van de kostenconfiguratie.
- [**Inzender**](../../role-based-access-control/built-in-roles.md#contributor) – Kan kosten bekijken en alles beheren, inclusief kostenconfiguratie, maar exclusief toegangsbeheer.
- [**Lezer**](../../role-based-access-control/built-in-roles.md#reader) – Kan alles bekijken, inclusief kostengegevens en -configuratie, maar kan geen wijzigingen aanbrengen.
- [**Cost Management Inzender**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – Kan kosten bekijken, kostenconfiguratie beheren en aanbevelingen weergeven.
- [**Cost Management Lezer**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – Kan kostengegevens bekijken, kostenconfiguratie beheren en aanbevelingen weergeven.

Cost Management Inzender is de aanbevolen rol met minimale bevoegdheden. De rol staat gebruikers toe om budgetten en exports te maken en beheren, om de kosten effectiever te kunnen controleren en hierover rapport uit te brengen. Cost Management Inzenders kunnen ook extra rollen vereisen om complexe scenario's voor kostenbeheer te ondersteunen. Neem de volgende scenario's:

- **Rapportage over resourcegebruik** – Azure Cost Management geeft kosten weer in de Azure-portal. Dit omvat gebruik omdat het betrekking heeft op kosten in de volledige gebruikspatronen. In dit rapport worden API- en downloadkosten weergegeven, maar u kunt ook inzoomen op gedetailleerdere metrische gebruiksgegevens in Azure Monitor om meer inzicht te krijgen. Overweeg [Lezer voor bewaking](../../role-based-access-control/built-in-roles.md#monitoring-reader) toe te kennen aan elk bereik waar u ook moet rapporteren over gedetailleerde metrische gebruiksgegevens.
- **Actie ondernemen wanneer budgetten worden overschreden** – Cost Management Inzenders hebben ook toegang nodig tot het maken en beheren van actiegroepen om automatisch te reageren op overschrijdingen. Overweeg [Bewakingsbijdrage](../../role-based-access-control/built-in-roles.md#monitoring-contributor) toe te kennen aan een resourcegroep die de actiegroep bevat, die moet worden gebruikt wanneer de budgetdrempels worden overschreden. Voor het automatiseren van specifieke acties zijn aanvullende rollen vereist voor de specifieke services die worden gebruikt, zoals automatisering en Azure Functions.
- **Kosten plannen voor gegevensexport** – Cost Management Inzenders moeten ook toegang hebben tot het beheren van opslagaccounts om een export te plannen om gegevens naar een opslagaccount te kopiëren. Overweeg [Inzender voor Storage-account](../../role-based-access-control/built-in-roles.md#storage-account-contributor) toe te kennen aan een resourcegroep die het opslagaccount bevat waarin kostengegevens worden geëxporteerd.
- **Het weergeven van aanbevelingen voor het besparen van kosten** – Cost Management Lezers en Cost Management Inzenders hebben standaard toegang tot het *bekijken* van kostenaanbevelingen. Toegang tot de kostenaanbevelingen vereist echter toegang tot afzonderlijke resources. Overweeg om een [servicespecifieke rol toe te kennen](../../role-based-access-control/built-in-roles.md#all) als u op een op kosten gebaseerde aanbeveling wilt handelen.

Beheergroepen worden alleen ondersteund als ze EA-abonnementen (Enterprise Agreement), PAYG-abonnementen (betalen per gebruik) of interne Microsoft-abonnementen bevatten. Beheergroepen met andere typen abonnementen, zoals Microsoft Customer Agreement- of Azure Active Directory-abonnementen, kunnen geen kosten weergeven. Als u een combinatie van abonnementen hebt, verplaatst u de niet-ondersteunde abonnementen naar een afzonderlijke tak van de beheergroephiërarchie om Cost Management in te schakelen voor de ondersteunde abonnementen. Voorbeeld: maak twee beheergroepen onder de hoofdbeheergroep: **Azure AD** en **Mijn organisatie**. Verplaats uw Azure AD-abonnement naar de beheergroep **Azure AD**, en bekijk en beheer kosten vervolgens met behulp van de beheergroep **Mijn organisatie**.

## <a name="enterprise-agreement-scopes"></a>Enterprise Agreement-bereiken

Enterprise Agreement (EA)-factureringsrekeningen, ook wel inschrijvingen genoemd, hebben de volgende bereiken:

- [**Factureringsrekening**](../manage/view-all-accounts.md)- Vertegenwoordigt een EA-inschrijving. Facturen worden in dit bereik gegenereerd. Aankopen die niet op basis van gebruik zijn, zoals Marketplace en reserveringen, zijn alleen beschikbaar in dit bereik. Ze worden niet weergegeven in afdelingen of inschrijvingsaccounts. Het gebruik van reserveringen, samen met alle andere gebruik, wordt toegepast op afzonderlijke resources. Gebruik wordt doorgevoerd op abonnementen binnen het factureringsaccount. Als u de reserveringskosten uitgesplitst wilt weergeven voor elke resource, schakelt u over naar de weergave **Afgeschreven kosten** in de kostenanalyse.

    Resourcetype: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Afdeling** - Optionele groepering van inschrijvingsaccounts.

    Resourcetype: `Billing/billingAccounts/departments`

- **Inschrijvingsaccount** - Verwijst naar een enkele accounteigenaar. Biedt geen ondersteuning voor het verlenen van toegang aan meerdere personen.

    Resourcetype: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Hoewel governance-bereiken zijn gebonden aan één map, zijn EA-factureringsbereiken dit niet. Een EA-factureringsrekening kan abonnementen hebben op elk willekeurig aantal Azure Active Directory-directory's.

EA-factureringsbereiken ondersteunen de volgende rollen:

- **Enterprise admin** - Kan instellingen en toegang van de factureringsrekening beheren, kan alle kosten bekijken en de kostenconfiguratie beheren. Bijvoorbeeld budgetten en exports. In functie is het EA-factureringsbereik hetzelfde als de [Azure-rol Cost Management-inzender](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Enterprise alleen-lezen gebruiker** – Kan instellingen voor de factureringsrekening, kostengegevens en kostenconfiguratie bekijken. Bijvoorbeeld budgetten en exports. In functie is het EA-factureringsbereik hetzelfde als de [Azure-rol Cost Management-lezer](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Afdelingsbeheerder** – Kan afdelingsinstellingen, zoals kostencentrum, beheren en alle kosten bekijken en de kostenconfiguratie beheren. Bijvoorbeeld budgetten en exports.  De instellingen van de **DA kosten bekijken**-factureringsrekening moeten zijn ingeschakeld voor afdelingsbeheerders en alleen-lezen gebruikers om de kosten te bekijken. Als de optie **DA kosten bekijken** is uitgeschakeld, kunnen de afdelingsgebruikers geen kosten op elk niveau zien, zelfs niet als ze een account of abonnementseigenaar zijn.
- **Enterprise alleen-lezen gebruiker** – Kan afdelingsinstellingen, kostengegevens en kostenconfiguratie bekijken. Bijvoorbeeld budgetten en exports. Als de optie **DA kosten bekijken** is uitgeschakeld, kunnen de afdelingsgebruikers geen kosten op elk niveau zien, zelfs niet als ze een account of abonnementseigenaar zijn.
- **Accounteigenaar** – Kan instellingen voor het inschrijvingsaccount beheren (zoals het kostencentrum), alle kosten bekijken en de kostenconfiguratie (zoals budgetten en exporten) voor het inschrijvingsaccount beheren. De instellingen van de **DA kosten bekijken**-factureringsrekening moeten zijn ingeschakeld voor afdelingsbeheerders en Azure RBAC-gebruikers om de kosten te bekijken.

EA-factureringsrekening-gebruikers hebben geen directe toegang tot facturen. Facturen zijn verkrijgbaar via een extern volume-licentiesysteem.

Azure-abonnementen zijn genest onder inschrijvingsaccounts. Factureringsgebruikers hebben toegang tot kostengegevens voor de abonnementen en resourcegroepen die zich onder hun respectieve bereik bevinden. Ze hebben geen toegang tot het weergeven of beheren van resources in de Azure-portal. Gebruikers kunnen kosten weergeven door te navigeren naar **Cost Management + facturering** in de Azure-portal-lijst met services. Vervolgens kunnen ze de kosten filteren voor de specifieke abonnementen en resourcegroepen die ze nodig hebben om te rapporteren.

Factureringsgebruikers hebben geen toegang tot beheergroepen omdat ze niet expliciet onder een specifieke factureringsrekening vallen. Toegang moet expliciet aan beheergroepen worden verleend. Beheergroepen omvatten de totale kosten van beheergroepen van alle geneste abonnementen. Ze omvatten echter alleen aankopen op basis van gebruik. Ze omvatten geen aankopen zoals reserveringen en Marketplace-aanbiedingen van derden. Gebruik de EA-factureringsrekening om deze kosten weer te geven.

## <a name="individual-agreement-scopes"></a>Bereiken voor individuele overeenkomsten

Azure-abonnementen die zijn gemaakt op basis van afzonderlijke aanbiedingen, zoals betalen per gebruik en gerelateerde typen, zoals gratis proefversie en ontwikkel- en testaanbiedingen, hebben geen expliciet bereik voor factureringsrekeningen. In plaats daarvan heeft elk abonnement een accounteigenaar of accountbeheerder, zoals de eigenaar van het EA-account.

- [**Factureringsrekening**](../manage/view-all-accounts.md) - Verwijst naar een enkele eigenaar (accountbeheerder) voor één of meer Azure-abonnementen. Het biedt momenteel geen ondersteuning voor het verlenen van toegang aan meerdere personen of toegang tot samengevoegde kostenweergaven.

    Resourcetype: Niet van toepassing

Afzonderlijke accounts beheerders van een Azure-abonnement kunnen facturerings gegevens, zoals facturen en betalingen, bekijken en beheren via de [Azure Portal](https://portal.azure.com)  >  **abonnementen** > een abonnement selecteren.

In tegenstelling tot EA kunnen accountbeheerders van afzonderlijke Azure-abonnementen hun facturen zien in de Azure-portal. Houd er wel voor dat Cost Management Lezer- en Cost Management Inzender-rollen geen toegang tot facturen bieden. Zie [Het verlenen van toegang tot facturen](../manage/manage-billing-access.md#give-read-only-access-to-billing) voor meer informatie.

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft-klantovereenkomst-bereiken

Factureringsrekeningen voor Microsoft-klantovereenkomsten hebben de volgende bereiken:

- **Factureringsrekening** - Verwijst naar een klantenovereenkomst voor meerdere Microsoft-producten en -services. Factureringsrekeningen van klantovereenkomsten zijn niet functioneel hetzelfde als EA-inschrijvingen. EA-inschrijvingen zijn nauwkeuriger afgestemd op factureringsprofielen.

    Resourcetype: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Factureringsprofiel** - Hier worden de abonnementen gedefinieerd die zijn opgenomen in een factuur. Factureringsprofielen zijn het functionele equivalent van een EA-inschrijving, omdat dat de omvang is waarop facturen worden gegenereerd. Zo zijn ook aankopen die niet op basis van gebruik zijn (zoals Marketplace en reserveringen), alleen beschikbaar in dit bereik. Ze zijn niet opgenomen in de secties van de factuur.

    Resourcetype: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Factuursectie** - Vertegenwoordigt een groep abonnementen in een factuur of factureringsprofiel. Factuursecties zijn vergelijkbaar met afdelingen: meerdere personen kunnen toegang hebben tot een factuursectie.

    Resourcetype: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Klant** - Vertegenwoordigt een groep abonnementen die zijn gekoppeld aan een specifieke klant die wordt voorbereid op een Microsoft-klantovereenkomst per partner. Dit bereik is specifiek voor CSP (Cloud Solution Providers).

In tegenstelling tot EA-factureringsbereiken, _zijn_ factureringsrekeningen voor klantovereenkomsten gebonden aan één map en kunnen er geen abonnementen zijn voor meerdere Azure Active Directory-mappen.

Factureringsbereiken van klantovereenkomsten gelden niet voor partners. Partnerrollen en -machtigingen worden beschreven op [Gebruikersrollen en machtigingen toewijzen](/partner-center/permissions-overview).

Factureringsbereiken van klantovereenkomsten ondersteunen de volgende rollen:

- **Eigenaar** – Kan factureringsinstellingen en toegang beheren, alle kosten bekijken en de kostenconfiguratie beheren. Bijvoorbeeld budgetten en exports. In functie is dit Customer Agreement-factureringsbereik hetzelfde als de [Azure-rol Cost Management-inzender](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Inzender** – Kan factureringsinstellingen en toegang beheren, alle kosten bekijken en de kostenconfiguratie beheren. Bijvoorbeeld budgetten en exports. In functie is dit Customer Agreement-factureringsbereik hetzelfde als de [Azure-rol Cost Management-inzender](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Lezer** – Kan factureringsinstellingen, kostengegevens en kostenconfiguratie bekijken. Bijvoorbeeld budgetten en exports. In functie is dit Customer Agreement-factureringsbereik hetzelfde als de [Azure-rol Cost Management-lezer](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Factuurbeheerder** – Kan facturen bekijken en betalen en kan kosten gegevens en-configuratie bekijken. Bijvoorbeeld budgetten en exports. In functie is dit Customer Agreement-factureringsbereik hetzelfde als de [Azure-rol Cost Management-lezer](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Maker van Azure-abonnement** – Kan Azure-abonnementen maken, kosten bekijken en de kostenconfiguratie beheren. Bijvoorbeeld budgetten en exports. In functie is dit factuurbereik voor klantenovereenkomsten hetzelfde als de rol van de accounteigenaar van de EA-inschrijving.

Azure-abonnementen zijn genest onder factuursecties, zoals hoe ze onder EA-inschrijvingsaccounts vallen. Factureringsgebruikers hebben toegang tot kostengegevens voor de abonnementen en resourcegroepen die zich onder hun respectieve bereik bevinden. Ze hebben echter geen toegang tot het weergeven of beheren van resources in de Azure-portal. Factureringsgebruikers kunnen kosten weergeven door te navigeren naar **Cost Management + facturering** in de Azure-portal-lijst met services. Vervolgens kunnen ze de kosten filteren voor de specifieke abonnementen en resourcegroepen die ze nodig hebben om te rapporteren.

Factureringsgebruikers hebben geen toegang tot beheergroepen omdat ze niet expliciet onder de factureringsrekening vallen. Als er echter beheergroepen zijn ingeschakeld voor de organisatie, worden alle abonnementskosten naar de factureringsrekening en de hoofdbeheergroep gedistribueerd, omdat deze beide zijn beperkt tot één map. Beheergroepen bevatten alleen aankopen die gebaseerd zijn op gebruik. Aankopen als reserveringen en Marketplace-aanbiedingen van derden zijn niet opgenomen in beheergroepen. De factureringsrekening en de hoofdbeheergroep kunnen dus verschillende totalen rapporteren. Gebruik de factureringsrekening of het respectieve factureringsprofiel om deze kosten weer te geven.

## <a name="aws-scopes"></a>AWS-bereiken

Nadat de AWS-integratie is voltooid, raadpleegt u de [Setup en configuratie van AWS-integratie](aws-integration-set-up-configure.md). De volgende bereiken zijn beschikbaar:

- **Externe factureringsrekening** - Vertegenwoordigt een klantovereenkomst met een externe leverancier. Het is vergelijkbaar met de EA-factureringsrekening.

    Resourcetype: `Microsoft.CostManagement/externalBillingAccounts`

- **Extern abonnement** - Vertegenwoordigt een operationeel account van een klant met een externe leverancier. Het is vergelijkbaar met een Azure-abonnement.

    Resourcetype: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Cloud Solution Provider (CSP)-bereiken

De volgende bereiken worden ondersteund voor CSP's met klanten in een Microsoft-klantovereenkomst:

- **Factureringsrekening** - Verwijst naar een klantenovereenkomst voor meerdere Microsoft-producten en -services. Factureringsrekeningen van klantovereenkomsten zijn niet functioneel hetzelfde als EA-inschrijvingen. EA-inschrijvingen zijn nauwkeuriger afgestemd op factureringsprofielen.

    Resourcetype: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Factureringsprofiel** - Hier worden de abonnementen gedefinieerd die zijn opgenomen in een factuur. Factureringsprofielen zijn het functionele equivalent van een EA-inschrijving, omdat dat de omvang is waarop facturen worden gegenereerd. Zo zijn ook aankopen die niet op basis van gebruik zijn (zoals Marketplace en reserveringen), alleen beschikbaar in dit bereik.

    Resourcetype: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Klant** - Vertegenwoordigt een groep abonnementen die zijn gekoppeld aan een specifieke klant die wordt voorbereid op een Microsoft-klantovereenkomst door een partner.

Alleen gebruikers met de rollen *Globale beheerder* en *Beheerdersagent* kunnen de kosten voor factureringsrekeningen, factureringsprofielen en klanten rechtstreeks in de Azure-tenant van de partner beheren en weergeven. Zie [Gebruikersrollen en -machtigingen toewijzen](/partner-center/permissions-overview) voor meer informatie over de rollen van het partnercentrum.

Azure Cost Management ondersteunt alleen CSP-partnerklanten als de klanten een Microsoft-klantovereenkomst hebben. Zie [Partnercentrum](/azure/cloud-solution-provider/overview/partner-center-overview) voor met CSP'S ondersteunde klanten die nog geen Microsoft-klantovereenkomst hebben.

Beheergroepen in CSP-bereiken worden niet ondersteund in Cost Management. Als u een CSP-abonnement hebt en u het bereik instelt op een beheergroep in kostenanalyse, ziet u een fout die vergelijkbaar is met het volgende:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Schakelen tussen bereiken in Cost Management

Alle Cost Management-weergaven in de Azure-portal bevatten een **Bereik**-selectiepil in de linkerbovenhoek van de weergave. Gebruik deze om het bereik snel te wijzigen. Selecteer de **Bereik**-pil om de bereik-kiezer te openen. Hierin worden factureringsrekeningen, de hoofdbeheergroep en abonnementen weergegeven die niet zijn genest onder de hoofdbeheergroep. Als u een bereik wilt selecteren, selecteert u de achtergrond om deze te markeren, en selecteert u vervolgens **Selecteren** onderaan. Als u wilt inzoomen op geneste bereiken, zoals resourcegroepen in een abonnement, selecteert u de koppeling Bereiknaam. Als u het bovenliggende bereik op een genest niveau wilt selecteren, selecteert u **Selecteer dit &lt;bereik&gt;** boven aan de bereik-kiezer.

## <a name="identify-the-resource-id-for-a-scope"></a>De resource-ID voor een bereik identificeren

Wanneer u werkt met Cost Management-API's, is het kritiek om het bereik te kennen. Gebruik de volgende informatie om de juiste Scope-URI te maken voor Cost Management-API's.

### <a name="billing-accounts"></a>Factureringsrekeningen

1. Open de Azure-portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met services.
2. Selecteer **Eigenschappen** in het menu van de factureringsrekening.
3. Selecteer de id van de factureringsrekening.
4. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Factureringsprofielen

1. Open de Azure-portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met services.
2. Selecteer **Factureringsprofielen** in het menu van de factureringsrekening.
3. Selecteer de naam van het factureringsprofiel.
4. Selecteer **Eigenschappen** in het menu van het factureringsprofiel.
5. Kopieer de factureringsrekening en de facturerings profiel-id's.
6. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Factuursecties

1. Open de Azure-portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met services.
2. Selecteer **Factuursecties** in het menu Factureringsrekening.
3. Selecteer de naam van de factuursectie.
4. Selecteer **Eigenschappen** in het menu Factuursectie.
5. Kopieer de id van de factureringsrekening en de factuursectie.
6. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA-afdelingen

1. Open de Azure-portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met services.
2. Selecteer **Afdelingen** in het menu van de factureringsrekening.
3. Selecteer de naam van de afdeling.
4. Selecteer **Eigenschappen** in het afdeling-menu.
5. Kopieer de factureringsrekening en de afdelings-id's.
6. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA-Inschrijvingsaccount

1. Open de Azure-portal en navigeer vervolgens naar **Cost Management en facturering** in de lijst met services.
2. Selecteer **Inschrijvingsaccounts** in het menu van de factureringsrekening.
3. Selecteer de naam van het inschrijvingsaccount.
4. Selecteer **Eigenschappen** in het menu van het inschrijvingsaccount.
5. Kopieer de id’s van de factureringsrekening en het inschrijvingsaccount.
6. Uw bereik is: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Beheergroep

1. Open de Azure-portal en navigeer vervolgens naar **Beheergroepen** in de lijst met services.
2. Ga naar de beheergroep.
3. Kopieer de ID van de beheergroep uit de tabel.
4. Uw bereik is: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Abonnement

1. Open de Azure-portal en navigeer vervolgens naar **Abonnementen** in de lijst met services.
2. Kopieer de abonnements-id uit de tabel.
3. Uw bereik is: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Resourcegroepen

1. Open de Azure-portal en navigeer vervolgens naar **Resourcegroepen** in de lijst met services.
2. Selecteer de naam van de resourcegroep.
3. Selecteer **Eigenschappen** in het menu van de resourcegroep.
4. Kopieer de waarde van het veld Resource-id.
5. Uw bereik is: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management wordt momenteel ondersteund in [Azure Global](https://management.azure.com) en [Azure Government](https://management.usgovcloudapi.net). Zie [Azure Global en Government API-eindpunten](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping)voor meer informatie over Azure Government.

## <a name="next-steps"></a>Volgende stappen

- Als u de eerste quickstart voor Cost Management nog niet hebt voltooid, leest u die op [Kosten analyseren](quick-acm-cost-analysis.md).
