---
title: Veelgestelde vragen over het overstappen van de Cloud Partner-portal naar Partner Center - commerciële marketplace van Microsoft
description: Antwoorden op veelgestelde vragen over het overstappen van aanbiedingen van Cloud Partner-portal naar Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0a45e7c0479cf490ad8688230897ae89ce4d1020
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819120"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Veelgestelde vragen over het overstappen van de Cloud Partner-portal naar Partner Center

De Cloud Partner-portal is overgestappen naar Partner Center. Partner Center biedt een gestroomlijnde, geïntegreerde ervaring voor het publiceren van nieuwe aanbiedingen op [Microsoft AppSource](https://appsource.microsoft.com/) of [Azure Marketplace](https://azuremarketplace.microsoft.com/) en voor het beheren van bestaande aanbiedingen die zijn gemigreerd van de Cloud Partner-portal. Alle functies van de Cloud Partner-portal zijn beschikbaar in Partner Center. In dit artikel worden veelgestelde vragen over dit artikel beantwoord.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Wat betekent de overgang naar Partner Center voor mij?

U kunt op de volgende Partner Center:

| Gebied | Wijzigingen |
| --- | --- |
| Account | U hoeft geen nieuw Partner Center maken; U kunt uw bestaande Cloud Partner-portal gebruiken om u aan te melden bij Partner Center waar u nu uw account, gebruikers, machtigingen en facturering gaat beheren. De publicatieovereenkomst en bedrijfsprofielgegevens worden gemigreerd naar uw nieuwe Partner Center-account, samen met eventuele uitbetalingsprofielgegevens, gebruikersaccounts, machtigingen en actieve aanbiedingen. Meer informatie kunt [u vinden in Uw commerciële marketplace-account beheren in Partner Center.](manage-account.md) |
| Publicatie- en aanbiedingsbeheerervaring voor aanbieding | We hebben uw aanbiedingsgegevens verplaatst van de Cloud Partner-portal naar Partner Center. U hebt nu toegang tot uw aanbiedingen in Partner Center, die een verbeterde gebruikerservaring en intuïtieve interface biedt. Meer informatie over [het bijwerken van een bestaande aanbieding in de commerciële marketplace.](partner-center-portal/update-existing-offer.md) |
| Beschikbaarheid van uw aanbiedingen in de commerciële marketplace | Er zijn geen wijzigingen. Als uw aanbieding live is in de commerciële marketplace, blijft deze live. |
| Nieuwe aankopen en implementaties | Er zijn geen wijzigingen. Uw klanten kunnen uw aanbiedingen zonder onderbrekingen blijven kopen en implementeren. |
| Uitbetalingen | Alle aankopen en implementaties worden op de gebruikelijke manier aan u betaald. Meer informatie over [Betaald krijgen in de commerciële marketplace.](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context) |
| API-integraties met Cloud Partner-portal [API's](cloud-partner-portal-api-overview.md) | Bestaande Cloud Partner-portal API's worden nog steeds ondersteund en uw bestaande integraties werken nog steeds. Meer informatie vindt u [in Wordt Cloud Partner-portal REST API's ondersteund?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analyse | U kunt de verkoop blijven bewaken, prestaties evalueren en uw aanbiedingen optimaliseren in de commerciële marketplace door analyses in de Partner Center. Er zijn verschillen tussen de weergave van analyserapporten in CPP en Partner Center. Een voorbeeld: **Verkopersinzichten** in CPP heeft een tabblad **Orders & Usage** met gegevens voor aanbiedingen op basis van gebruik en aanbiedingen die niet op gebruik zijn gebaseerd, terwijl in Partner Center de pagina **Orders** een afzonderlijk tabblad heeft voor SaaS-aanbiedingen. Meer informatie kunt [u vinden in Analyserapporten openen voor de commerciële marketplace in Partner Center.](partner-center-portal/analytics.md) |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Moet ik een nieuw account maken voor het beheren van mijn aanbiedingen in Partner Center?

Nee, uw account blijft behouden. Dit betekent dat als u een bestaande partner bent, u uw bestaande accountreferenties voor Cloud Partner-portal kunt gebruiken om u aan te melden bij Partner Center. Maak geen nieuw account of aanbiedingen die zijn gemaakt in Cloud Partner-portal en verplaatst naar Partner Center worden er niet aan gekoppeld.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Welke pagina's in Partner Center komen overeen met pagina's die ik in de Cloud Partner-portal?

Hieronder volgen Partner Center koppelingen voor pagina's die vaak worden gebruikt in de Cloud Partner-portal. Als u de Cloud Partner-portal als bladwijzers hebt opgeslagen, moet u deze bijwerken.

| Cloud Partner-portal pagina | Cloud Partner-portal-paginakoppeling | Partner Center-paginakoppeling |
| --- | --- | --- |
| Pagina voor Alle aanbiedingen | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Pagina voor Alle uitgevers | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Uitgeversprofiel | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Pagina voor Gebruikers | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Pagina Geschiedenis | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | De functie Geschiedenis wordt nog niet ondersteund in Partner Center. |
| Insights-dashboard | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Uitbetalingsrapport | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Verschillen in uitbetalingsrapport

Dit zijn de verschillen in het uitbetalingsrapport tussen de Cloud Partner-portal en de huidige Partner Center:

| Cloud Partner-portal | Partnercentrum |
| --- | --- |
| **Koppeling:**https://cloudpartner.azure.com/ | **Koppeling:** https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory en https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigatie:** Uitbetalingsrapportage in Insights Uitbetaling | **Navigatie:** Uitbetalingsrapportage in Partner Center - Uitbetalingspictogram |
| **Bereik**:<ul><li>Transactie per regelitem is zichtbaar voor verzameling wordt uitgevoerd, verzameld en betaald.</li><li>Rapportage: toont alle regelitems zodra de inkooporder is gemaakt, inclusief de verzameling die wordt uitgevoerd en de facturering wordt uitgevoerd, en de verzamelingsstatus en regelitems die nog niet in aanmerking komen om te worden betaald.</li></ul> | **Bereik**:<ul><li>Toont de regelitems nadat ze worden beschouwd als in aanmerking komende inkomsten.</li><li>De klanten betalen eerst aan Microsoft en vervolgens kunnen ISV's zien dat het uitbetalingsrapport begint.</li><li>Het uitbetalingsrapport bevat geen verzameling die wordt uitgevoerd en facturering wordt uitgevoerd.</li></ul> |
| **Transactie is niet gereed voor uitbetaling:** Facturering wordt uitgevoerd | **Transactie niet gereed voor uitbetaling:** Volgende geschatte betaling: De uitbetalingsstatus heeft de status Niet-verwerkt. |
| **Uitbetalingsstatus:** n.t.t. | **Uitbetalingsstatus:**<ul><li>Niet verwerkt: De inkomsten komen in aanmerking voor betaling.</li><li>Binnenkort: De inkomsten worden in de volgende maandelijkse uitbetaling naar de uitgever verzonden.</li><li>Verzonden: De betaling is verzonden naar uw bank.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Hoe zit het met aanbiedingen die ik in de Cloud Partner-portal?

De aanbiedingen zijn verplaatst naar Partner Center en zijn voor u toegankelijk nadat u zich hebt aanmelden bij Partner Center, met uitzondering van dynamics Nav Managed Service- en Cortana Intelligence-aanbiedingen. Als uw aanbieding live was op de commerciële marketplace, blijft deze live en kunnen uw klanten deze zonder onderbrekingen kopen en implementeren. Zie de volgende vraag: Welke aanbiedingen zijn verplaatst naar **Partner Center?**, voor meer informatie.

## <a name="what-offers-were-moved-to-partner-center"></a>Welke aanbiedingen zijn verplaatst naar Partner Center?

Alle aanbiedingstypen die eerder werden ondersteund in de Cloud Partner-portal worden ondersteund in Partner Center, met uitzondering van aanbiedingen voor de beheerde Dynamics Nav-service en Cortana Intelligence.

Voor de typen aanbiedingen die worden ondersteund in Partner Center, zijn alle aanbiedingen verplaatst, ongeacht hun status; draft-, de-listed- en preview-only-aanbiedingen zijn ook verplaatst.

| Type aanbieding | Verplaatst naar Partner Center? | Volgende stappen |
| --- | --- | --- |
| SaaS | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie kunt [u vinden in Plan a SaaS offer for the commercial marketplace (Een SaaS-aanbieding plannen voor de commerciële marketplace).](plan-saas-offer.md) |
| Virtuele machine | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie kunt u [zien in Aanbieding voor virtuele machines plannen.](marketplace-virtual-machines.md) |
| Azure-toepassing | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Een Azure-toepassingsaanbieding maken.](create-new-azure-apps-offer.md) |
| Dynamics 365 Business Central | Ja | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Een Dynamics 365 Business Central-aanbieding maken.](partner-center-portal/create-new-business-central-offer.md) |
| Dynamics 365 for Customer Engagement & PowerApps | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Create a Dynamics 365 for Customer Engagement (Een Dynamics 365 for Customer Engagement & PowerApps-aanbieding).](dynamics-365-customer-engage-offer-setup.md) |
| Dynamics 365 for Operations | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Create a Dynamics 365 for Operations offer (Een Dynamics 365 for Operations-aanbieding maken).](partner-center-portal/create-new-operations-offer.md) |
| Power BI-app | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Een app Power BI app maken voor AppSource.](partner-center-portal/create-power-bi-app-offer.md) |
| IoT Edge module | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Een aanbieding voor een module maken, configureren IoT Edge publiceren in Azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Container | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Een Azure-containeraanbieding maken.](./create-azure-container-offer.md) |
| Adviesservice | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Een adviesserviceaanbieding maken.](./create-consulting-service-offer.md) |
| Beheerde service | Yes | Meld u aan Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie in [Create a Managed Service offer (Een beheerde-serviceaanbieding maken).](./plan-managed-service-offer.md) |
| Beheerde Dynamics Nav-service | No | Microsoft heeft dynamics NAV Managed Service ontwikkeld tot [Dynamics 365 Business Central,](/dynamics365/business-central/)dus hebben we de lijst met live aanbiedingen van Dynamics NAV Managed Service van AppSource verwijderd. Deze aanbiedingen kunnen niet meer worden ontdekt door klanten en zijn niet verplaatst naar Partner Center. Als u uw aanbiedingen beschikbaar wilt maken in AppSource, past u deze aan aan dynamics 365 Business Central-aanbiedingen en verstuurt u deze in [Partner Center](https://partner.microsoft.com/). Meer informatie in [Een Dynamics 365 Business Central-aanbieding maken.](partner-center-portal/create-new-business-central-offer.md) |
| Cortana Intelligence | No | Microsoft heeft de productroutekaart voor Cortana Intelligence ontwikkeld, dus hebben we de lijst met live Cortana Intelligence-aanbiedingen van AppSource verwijderd. Deze aanbiedingen kunnen niet meer worden ontdekt door klanten en zijn niet verplaatst naar Partner Center. Als u uw aanbiedingen beschikbaar wilt maken in de commerciële marketplace, past u uw aanbiedingen aan op SaaS-aanbiedingen (Software as a Service) en verstuurt u deze in [Partner Center](https://partner.microsoft.com/). Meer informatie over de [controlelijst voor het maken van SaaS-Partner Center.](./plan-saas-offer.md) |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Ik kan mijn Cloud Partner-portal niet vinden in Partner Center

Wat u in Partner Center is afhankelijk van de programma's die u hebt ingeschreven, de accounts waar u bij hoort en de gebruikersrollen en machtigingen die u hebt toegewezen. Er zijn veel Partner Center programma's beschikbaar en u kunt zijn ingeschreven bij meerdere programma's. Mogelijk hebt u ook toegang tot meerdere accounts met dezelfde gebruikersreferenties.

De aanbiedingen die u hebt gemaakt in Cloud Partner-portal zijn beschikbaar in Partner Center onder het **commerciële marketplace-programma** en onder het account dat wordt gebruikt om de aanbiedingen te maken. Volg de onderstaande stappen om ervoor te zorgen dat u het juiste programma en het juiste account bekijkt. Zie Manage your Partner Center account (Uw Partner Center beheren) [voor andere tips voor het oplossen van problemen.](/partner-center/partner-center-account-setup)

### <a name="access-the-right-program-in-partner-center"></a>Toegang tot het juiste programma in Partner Center

1. Meld u aan [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) met dezelfde referenties die zijn gebruikt om u aan te melden bij de Cloud Partner-portal. In het navigatiedeelvenster aan de linkerkant worden opties weergegeven die zijn gekoppeld aan de programma's waarin u bent ingeschreven.

    Voorbeeld: stel dat u toegang hebt tot drie programma's: het MPN-programma, het programma Verwijzingen en het commerciële Marketplace-programma. Wanneer u zich bij Partner Center, ziet u deze drie programma's in het navigatiedeelvenster.

2. Selecteer **Overzicht van commerciële marketplace**  >  **om** toegang te krijgen tot uw aanbiedingen.

    Als u het programma Commerciële marketplace niet ziet in het navigatiedeelvenster aan de linkerkant, hebt u mogelijk het verkeerde account. Volg de stappen in de volgende sectie om toegang te krijgen tot het juiste account.

    [![Schermopname van het menu Partner Center overzicht](media/cpp-pc-faq/overview-menu.png "Geeft het menu Partner Center overzicht weer")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Toegang tot het juiste account in Partner Center

Als u deel uitmaakt van meerdere accounts, ziet u in het Partnercentrum een knop voor het kiezen van accounts die is gemarkeerd met twee pijlen in het navigatiemenu aan de linkerkant. Selecteer de knop Account picker om een lijst met alle accounts te bekijken waar u bij hoort. Selecteer een account in de lijst om er naar over te schakelen en alle programma's en informatie met betrekking tot dat account weer te geven. Als u geen knop voor het kiezen van een account in het navigatiemenu ziet, bent u lid van één account.

[![Schermopname van de Partner Center knop account picker.](media/cpp-pc-faq/picker-button.png "Toont de knop Partner Center account kiezen")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Hoe kan ik nieuwe aanbiedingen maken?

Toegang tot het commerciële [marketplace-programma](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) in Partner Center om nieuwe aanbiedingen te maken. Selecteer op de pagina Overzicht **+ Nieuwe aanbieding.**

[![Schermopname van het Partner Center menu Overzicht.](media/cpp-pc-faq/new-offer.png "Geeft het menu Partner Center overzicht weer")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Ik kan me niet aanmelden en ik moet een ondersteuningsticket openen

Als u zich niet kunt aanmelden bij uw account, kunt u hier een [ondersteuningsticket](https://partner.microsoft.com/support/v2/?stage=1) openen.

## <a name="where-are-instructions-for-using-partner-center"></a>Waar zijn instructies voor het gebruik van Partner Center?

Ga naar de [documentatie voor de commerciële marketplace.](index.yml)vouw vervolgens Commerciële **marketplace-portal uit in Partner Center**. Als u Help-artikelen wilt bekijken voor het maken van aanbiedingen in Partner Center vouwt u **Een nieuwe aanbieding maken uit.**

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Wat zijn de verschillen in publicatie- en aanbiedingsbeheer?

Hier zijn enkele verschillen tussen de Cloud Partner-portal en Partner Center.

### <a name="modular-publishing-capabilities"></a>Modulaire publicatiemogelijkheden

Partner Center biedt een modulaire publicatieoptie waarmee u de wijzigingen kunt selecteren die u wilt publiceren in plaats van alle updates altijd in één keer te publiceren. In het volgende scherm ziet u bijvoorbeeld dat de enige wijzigingen die zijn geselecteerd om te worden gepubliceerd, de wijzigingen zijn **in Eigenschappen** en **Aanbiedingsvermelding.** De wijzigingen die u op de pagina Preview aan te brengen, worden niet gepubliceerd.

[![Schermopname van de Partner Center pagina Controleren en publiceren.](media/cpp-pc-faq/review-page.png "Toont de Partner Center pagina Controleren en publiceren")](media/cpp-pc-faq/review-page.png#lightbox)

De updates die u niet publiceert, worden opgeslagen als concept. Ga door met het gebruik van de preview-versie van uw aanbieding om uw aanbieding te controleren voordat u deze live beschikbaar maakt voor het publiek.

### <a name="enhanced-preview-options"></a>Verbeterde preview-opties

Partner Center bevat een [vergelijkingsfunctie met](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) verbeterde filteropties. Dit biedt u de mogelijkheid om te vergelijken met de preview- en liveversies van de aanbieding.

[![Schermopname van de functie Partner Center vergelijken.](media/cpp-pc-faq/compare.png "Toont de functie Partner Center vergelijken")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Wijzigingen in huisstijl en navigatie

U ziet enkele wijzigingen in de huisstijl. SKU's *worden bijvoorbeeld* in de *volgende* Partner Center:

[![Schermopname van de Partner Center Plans.](media/cpp-pc-faq/plans.png "Toont de pagina Partner Center-abonnementen")](media/cpp-pc-faq/plans.png#lightbox)

De informatie die u eerder hebt opgegeven op de pagina **Marketplace-** of **Storefront Details** (adviesservice,  Power BI-app) in de Cloud Partner-portal, wordt nu ook verzameld op de pagina Aanbiedingsvermelding in Partner Center:

[! [Schermopname toont de Partner Center aanbiedingspagina.] (media/cpp-pc-faq/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

De informatie die u eerder hebt opgegeven voor SKU's op één pagina in de Cloud Partner-portal kunnen nu worden verzameld op verschillende pagina's in Partner Center:

- Installatiepagina plannen
- Pagina met abonnementsvermelding
- Beschikbaarheidspagina plannen
- Pagina voor technische configuratie plannen, zoals hier wordt weergegeven:

[![Geeft de pagina Partner Center technische configuratie weer.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Uw aanbiedings-id wordt nu weergegeven in de linkernavigatiebalk van de aanbieding:

![Toont de Partner Center van de aanbiedings-id](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Stoppen met het verkopen van een aanbieding

U kunt aanvragen om [te stoppen met het verkopen van een aanbieding](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) op de marketplace rechtstreeks vanuit Partner Center portal. De optie is beschikbaar op de **overzichtspagina van** de aanbieding voor uw aanbieding.

[![Schermopname van de Partner Center om te stoppen met het verkopen van een aanbieding.](media/cpp-pc-faq/stop-sell.png "Toont de Partner Center om te stoppen met het verkopen van een aanbieding")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Worden de Cloud Partner-portal REST API's nog steeds ondersteund?

De Cloud Partner-portal API's zijn geïntegreerd met Partner Center en blijven werken. De overgang naar Partner Center brengt kleine wijzigingen met zich mee. Bekijk de onderstaande tabel om te controleren of uw code blijft werken in Partner Center.

| API | Beschrijving wijziging | Impact |
| --- | --- | --- |
| POST Publiceren, GoLive, Annuleren | Voor gemigreerde aanbiedingen heeft de antwoordheader een andere indeling, maar blijft deze op dezelfde manier werken, met een relatief pad om de bewerkingsstatus op te halen. | Bij het verzenden van een van de bijbehorende POST-aanvragen voor een aanbieding, heeft de Location-header een van de twee indelingen, afhankelijk van de migratiestatus van de aanbieding: <ul><li>Niet-gemigreerde aanbiedingen: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Gemigreerde aanbiedingen: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET-bewerking | Voor aanbiedingen die eerder een veld 'notification-email' in het antwoord ondersteunden, wordt dit veld afgeschaft en niet meer geretourneerd voor gemigreerde aanbiedingen. | Voor gemigreerde aanbiedingen verzenden we geen meldingen meer naar de lijst met e-mailberichten die zijn opgegeven in de aanvragen. In plaats daarvan wordt de API-service afgestemd op het e-mailproces voor meldingen in Partner Center e-mailberichten te verzenden. Meldingen over de voortgang van de bewerking worden verzonden naar het e-mailadres dat is ingesteld in de sectie Contactgegevens van verkoper van uw accountinstellingen in Partner Center.<br><br>Zorg ervoor dat het e-mailadres dat is ingesteld in de sectie Contactgegevens van verkoper in de [sectie Accountinstellingen](https://partner.microsoft.com/dashboard/account/management) in Partner Center juist is om meldingen te ontvangen. |
|||