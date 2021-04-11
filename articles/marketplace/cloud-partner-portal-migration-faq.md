---
title: 'Veelgestelde vragen over overgang van de Cloud Partner-portal naar partner Center: micro soft Commercial Marketplace'
description: Antwoorden op veelgestelde vragen over overgangs aanbiedingen van Cloud Partner-portal naar het partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e606e21fe2b80683737970b92d57ce87b9103144
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106867"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Veelgestelde vragen over overgang van de Cloud Partner-portal naar het partner centrum

De Cloud Partner-portal is overgegaan naar het partner centrum. Partner centrum biedt een gestroomlijnde, geïntegreerde ervaring voor het publiceren van nieuwe aanbiedingen op [Microsoft AppSource](https://appsource.microsoft.com/) of [Azure Marketplace](https://azuremarketplace.microsoft.com/) en voor het beheren van bestaande aanbiedingen die vanuit de Cloud Partner-Portal zijn gemigreerd. Alle functies van de Cloud Partner-portal zijn beschikbaar in partner centrum. In dit artikel komen veelgestelde vragen aan de gang.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Wat betekent de overgang naar het partner centrum voor mij?

U kunt nu door gaan met zakendoen in Partner Center:

| Gebied | Wijzigingen |
| --- | --- |
| Account | U hoeft geen nieuw partner centrum-account te maken. u kunt uw bestaande Cloud Partner-portal referenties gebruiken om u aan te melden bij het partner centrum, waar u nu uw account, gebruikers, machtigingen en facturering beheert. De gegevens voor de publicatie overeenkomst en het bedrijfs profiel worden gemigreerd naar uw nieuwe partner centrum-account, samen met alle informatie over het profiel voor uitbetaling, gebruikers accounts en machtigingen en actieve aanbiedingen. Meer informatie over [het beheren van uw commerciële Marketplace-account in Partner Center](manage-account.md). |
| Aanbieding voor publicatie en aanbod beheer | Uw aanbiedings gegevens zijn verplaatst van de Cloud Partner-portal naar het partner centrum. U hebt nu toegang tot uw aanbiedingen in partner centrum. Dit biedt een verbeterde gebruikers ervaring en intuïtieve interface. Meer informatie over [het bijwerken van een bestaande aanbieding in de commerciële Marketplace](partner-center-portal/update-existing-offer.md). |
| Beschik baarheid van uw aanbiedingen in de commerciële Marketplace | Er zijn geen wijzigingen. Als uw aanbieding Live is in de commerciële Marketplace, blijft deze live. |
| Nieuwe aankopen en implementaties | Er zijn geen wijzigingen. Uw klanten kunnen uw aanbiedingen blijven kopen en implementeren zonder onderbrekingen. |
| Uitbetalingen | Alle aankopen en implementaties blijven als normaal worden betaald. Meer informatie over hoe u [in de commerciële Marketplace betaalt](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context). |
| API-integraties met bestaande [Cloud Partner-Portal-api's](cloud-partner-portal-api-overview.md) | Bestaande Cloud Partner-portal Api's worden nog steeds ondersteund en uw bestaande integraties werken nog steeds. Meer informatie over [worden de Cloud Partner-Portal rest api's ondersteund?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analyse | U kunt door gaan met het bewaken van de verkoop, het evalueren van prestaties en het optimaliseren van uw aanbiedingen in de commerciële Marketplace door analyses in het partner centrum te bekijken. Er zijn verschillen tussen hoe analyse rapporten worden weer gegeven in CPP en partner Center. Bijvoorbeeld: de **verkoop inzichten** in cpp hebben het tabblad **Orders & gebruik** waarin gegevens worden weer gegeven voor aanbiedingen op basis van gebruik en niet-gebruiks basis. in het partner centrum heeft de pagina **Orders** een afzonderlijk tabblad voor SaaS-aanbiedingen. Meer informatie over [Access analytic-rapporten voor de commerciële Marketplace in Partner Center](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Moet ik een nieuw account maken voor het beheren van mijn aanbiedingen in Partner Center?

Nee, uw account wordt bewaard. Dit betekent dat als u een bestaande partner bent, u uw bestaande Cloud Partner-portal-account referenties kunt gebruiken om u aan te melden bij het partner centrum. Maak geen nieuw account of aanbiedingen die zijn gemaakt in Cloud Partner-portal en worden verplaatst naar het partner centrum.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Welke pagina's in het partner centrum komen overeen met de pagina's die ik heb gebruikt in de Cloud Partner-portal?

Hieronder vindt u partner Center-koppelingen voor pagina's die vaak worden gebruikt in de Cloud Partner-portal. Als u de Cloud Partner-portal koppelingen als blad wijzers hebt opgeslagen, moet u deze bijwerken.

| Cloud Partner-portal pagina | Koppeling naar Cloud Partner-portal pagina | Koppeling naar de pagina partner centrum |
| --- | --- | --- |
| Pagina voor Alle aanbiedingen | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Pagina voor Alle uitgevers | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Publisher-profiel | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Pagina voor Gebruikers | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Pagina geschiedenis | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | De geschiedenis functie wordt nog niet ondersteund in partner centrum. |
| Insights-dash board | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Uitbetalings rapport | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Verschillen in uitbetalings rapport

Dit zijn de verschillen in het uitbetalings rapport tussen de buiten gebruik gestelde Cloud Partner-portal en het huidige partner centrum:

| Cloud Partner-portal | Partnercentrum |
| --- | --- |
| **Koppeling**: https://cloudpartner.azure.com/ | **Koppeling**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory en https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigatie**: uitbetalings rapportage die is opgenomen in Insights-uitbetaling | **Navigatie**: uitbetalings rapportage die is opgenomen in het partner centrum-pictogram voor uitbetaling |
| **Bereik**:<ul><li>Trans actie per regel item is zichtbaar voor de verzameling die wordt uitgevoerd, verzameld en betaald.</li><li>Rapportage: geeft alle regel items weer zodra de aankoop order is gemaakt, inclusief de verzameling die wordt uitgevoerd en de facturering wordt uitgevoerd, en de verzamelings status en regel items die nog niet kunnen worden betaald.</li></ul> | **Bereik**:<ul><li>De regel items worden weer gegeven nadat deze als in aanmerking komende winst worden beschouwd.</li><li>Klanten betalen eerst aan micro soft en vervolgens kunnen Isv's het rapport voor uitbetaling starten.</li><li>De verzameling wordt niet weer gegeven in het uitbetalings rapport en de facturering wordt momenteel uitgevoerd.</li></ul> |
| **Trans actie niet gereed voor uitbetaling**: facturering wordt uitgevoerd | **Trans actie niet gereed voor uitbetaling**: volgende geschatte betaling: de status van de uitbetaling is in de niet-verwerkte staat. |
| **Status van uitbetaling**: n.v.t. | **Uitbetalings status**:<ul><li>Niet verwerkt: het verdienen komt in aanmerking voor betaling.</li><li>Binnenkort: het verdienen wordt in de volgende maandelijkse uitbetaling naar de uitgever verzonden.</li><li>Verzonden: de betaling is naar uw bank verzonden.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Hoe over aanbiedingen die ik in het Cloud Partner-portal gepubliceerd?

De aanbiedingen zijn verplaatst naar het partner centrum en zijn toegankelijk voor u nadat u zich hebt aangemeld bij het partner centrum, met uitzonde ring van de door Dynamics NAV beheerde service en Cortana Intelligence aanbiedingen. Als uw aanbieding in de commerciële Marketplace Live was, blijft deze live en blijven uw klanten deze zonder onderbrekingen kunnen kopen en implementeren. Zie de volgende vraag, **welke aanbiedingen zijn verplaatst naar het partner centrum?**, voor meer informatie.

## <a name="what-offers-were-moved-to-partner-center"></a>Welke aanbiedingen zijn verplaatst naar het partner centrum?

Alle aanbiedings typen die eerder worden ondersteund in de Cloud Partner-portal worden ondersteund in het partner centrum, met uitzonde ring van de door Dynamics NAV beheerde service en Cortana Intelligence aanbiedingen.

Voor de aanbiedings typen die in Partner Center worden ondersteund, zijn alle aanbiedingen verplaatst, ongeacht hun status. de aanbiedingen concept, genoteerd en alleen voor beeld zijn ook verplaatst.

| Type aanbieding | Verplaatst naar het partner centrum? | Volgende stappen |
| --- | --- | --- |
| SaaS | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het plannen van een SaaS-aanbieding voor de commerciële Marketplace](plan-saas-offer.md). |
| Virtuele machine | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het plannen van een aanbieding voor een virtuele machine](marketplace-virtual-machines.md). |
| Azure-toepassing | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het maken van een Azure-toepassings aanbieding](create-new-azure-apps-offer.md). |
| Dynamics 365 Business Central | Ja | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het maken van een Dynamics 365 Business Central-aanbieding](partner-center-portal/create-new-business-central-offer.md). |
| Dynamics 365 voor klant betrokkenheid & PowerApps | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het maken van een Dynamics 365 voor klant Engagement & PowerApps-aanbieding](partner-center-portal/create-new-customer-engagement-offer.md). |
| Dynamics 365 for Operations | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het maken van een Dynamics 365 voor de aanbieding](partner-center-portal/create-new-operations-offer.md). |
| Power BI-app | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie vindt u in [een Power bi-app maken voor AppSource](partner-center-portal/create-power-bi-app-offer.md). |
| Module IoT Edge | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het maken, configureren en publiceren van een IOT Edge module aanbod in azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Container | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het maken van een Azure-container aanbieding](./create-azure-container-offer.md). |
| Adviesservice | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het maken van een advies service-aanbieding](./create-consulting-service-offer.md). |
| Beheerde service | Yes | Meld u aan bij Partner Center om nieuwe aanbiedingen te maken en aanbiedingen te beheren die zijn gemaakt in Cloud Partner-portal. Meer informatie over [het maken van een beheerde service aanbieding](./plan-managed-service-offer.md). |
| Door Dynamics NAV beheerde service | No | Micro soft heeft Dynamics NAV managed service ontwikkeld in [dynamics 365 Business Central](/dynamics365/business-central/), zodat we de aanbiedingen van Dynamics NAV Managed Service Live kunnen ontvangen van AppSource. Deze aanbiedingen kunnen niet meer worden gedetecteerd door klanten en zijn niet verplaatst naar het partner centrum. Als u uw aanbiedingen beschikbaar wilt maken in AppSource, past u deze toe aan Dynamics 365 Business Central-aanbiedingen en verzendt u deze in [Partner Center](https://partner.microsoft.com/). Meer informatie over [het maken van een Dynamics 365 Business Central-aanbieding](partner-center-portal/create-new-business-central-offer.md). |
| Cortana Intelligence | No | Micro soft heeft het overzicht van de product handleiding voor Cortana Intelligence ontwikkeld, zodat we Cortana Intelligence Live-aanbiedingen van AppSource niet meer vermelden. Deze aanbiedingen kunnen niet meer worden gedetecteerd door klanten en zijn niet verplaatst naar het partner centrum. Als u uw aanbiedingen beschikbaar wilt stellen in de commerciële Marketplace, kunt u uw aanbiedingen aanpassen aan SaaS-aanbiedingen (Software as a Service) en deze verzenden in het [partner centrum](https://partner.microsoft.com/). Meer informatie over de [controle lijst voor het maken van SaaS-aanbiedingen in partner centrum](./plan-saas-offer.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Ik kan mijn Cloud Partner-portal aanbiedingen niet vinden in het partner centrum

Wat u in het partner centrum ziet, is afhankelijk van de Program ma's die u hebt Inge schreven, de accounts waartoe u behoort en de gebruikers rollen en-machtigingen die u hebt toegewezen. Er zijn veel partner centrum-Program ma's beschikbaar en u bent mogelijk Inge schreven in meerdere Program ma's. Mogelijk hebt u ook toegang tot meerdere accounts met dezelfde gebruikers referenties.

De aanbiedingen die u in Cloud Partner-portal hebt gemaakt, zijn beschikbaar in partner centrum onder het **Commercial Marketplace** -programma en onder het account dat is gebruikt om de aanbiedingen te maken. Volg de onderstaande stappen om ervoor te zorgen dat u het juiste programma en de juiste account bekijkt. Zie [uw partner Center-account beheren](/partner-center/partner-center-account-setup)voor andere tips voor probleem oplossing.

### <a name="access-the-right-program-in-partner-center"></a>Toegang tot het juiste programma in Partner Center

1. Meld u aan bij het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) met dezelfde referenties waarmee u zich aanmeldt bij de Cloud Partner-Portal. In het navigatie deel venster aan de linkerkant worden opties weer gegeven die zijn gekoppeld aan de Program ma's die u hebt Inge schreven.

    Voor beeld: Stel dat u toegang hebt tot drie Program ma's: het MPN-programma, het programma referrals en het Commercial Marketplace-programma. Wanneer u zich aanmeldt bij het partner centrum, worden deze drie Program ma's weer geven in het navigatie deel venster.

2. Selecteer overzicht van **commerciële Marketplace**  >   voor toegang tot uw aanbiedingen.

    Als u het Commercial Marketplace-programma niet ziet in het navigatie deel venster aan de linkerkant, is het mogelijk dat u zich in het verkeerde account bevinden. Volg de stappen in de volgende sectie om toegang te krijgen tot het juiste account.

    [![Scherm afbeelding van het overzichts menu van het partner centrum](media/cpp-pc-faq/overview-menu.png "Toont het overzichts menu van het partner centrum")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Toegang krijgen tot het juiste account in het partner centrum

Als u deel uitmaakt van meerdere accounts, ziet u in Partner Center een knop voor account kiezer, gemarkeerd met twee pijlen in het navigatie menu aan de linkerkant. Selecteer de knop account kiezer om een lijst weer te geven van alle accounts waarvan u deel uitmaakt. Selecteer een account in de lijst om ernaar over te scha kelen en alle Program ma's en informatie met betrekking tot dat account te bekijken. Als u in het navigatie menu geen knop voor account kiezer ziet, bent u lid van één account.

[![Scherm afbeelding toont de knop voor het kiezen van een partner centrum-account.](media/cpp-pc-faq/picker-button.png "Hiermee wordt de knop Partner Center-account kiezer weer gegeven")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Hoe kan ik nieuwe aanbiedingen maken?

Open het Commercial Marketplace-programma in het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) om nieuwe aanbiedingen te maken. Selecteer op de pagina overzicht **+ nieuwe aanbieding**.

[![Scherm afbeelding toont het overzichts menu van het partner centrum.](media/cpp-pc-faq/new-offer.png "Toont het overzichts menu van het partner centrum")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Ik kan me niet aanmelden en moet een ondersteunings ticket openen

Als u zich niet kunt aanmelden bij uw account, kunt u hier een [ondersteunings ticket](https://partner.microsoft.com/support/v2/?stage=1) openen.

## <a name="where-are-instructions-for-using-partner-center"></a>Waar worden de instructies voor het gebruik van partner Center?

Ga naar de [documentatie voor commerciële Marketplace](index.yml)en vouw vervolgens de **Portal voor commerciële Marketplace uit in het partner centrum**. Als u Help-artikelen voor het maken van aanbiedingen in partner centrum wilt bekijken, vouwt u **een nieuwe aanbieding maken** uit.

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Wat zijn de verschillen in de publicaties en aanbiedings beheer?

Hier volgen enkele verschillen tussen de Cloud Partner-portal en het partner centrum.

### <a name="modular-publishing-capabilities"></a>Modulaire publicatie mogelijkheden

Het partner centrum biedt een modulaire publicatie optie waarmee u de wijzigingen kunt selecteren die u wilt publiceren in plaats van alle updates altijd tegelijk te publiceren. In het volgende scherm ziet u bijvoorbeeld dat de gegevens die zijn geselecteerd om te worden gepubliceerd, de wijzigingen in **Eigenschappen** en  **aanbiedings vermelding** zijn. De wijzigingen die u in de voorbeeld pagina aanbrengt, worden niet gepubliceerd.

[![Scherm afbeelding toont de pagina bekijken en publiceren van het partner centrum.](media/cpp-pc-faq/review-page.png "Toont de pagina voor het bekijken en publiceren van het partner centrum")](media/cpp-pc-faq/review-page.png#lightbox)

De updates die u niet publiceert, worden opgeslagen als concept. Ga door met de preview-versie van uw aanbieding om uw aanbieding te verifiëren voordat u deze beschikbaar maakt voor het publiek.

### <a name="enhanced-preview-options"></a>Verbeterde preview-opties

Het partner centrum bevat een [vergelijkings functie](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) met verbeterde filter opties. Dit biedt u de mogelijkheid om te vergelijken met de preview-versie en de live versies van de aanbieding.

[![Scherm afbeelding toont de functie vergelijken met partner centrum.](media/cpp-pc-faq/compare.png "Hiermee wordt de functie voor het vergelijken van partner Centers weer gegeven")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Huismerk en navigatie wijzigingen

U ziet enkele huismerk wijzigingen. *Sku's* worden bijvoorbeeld aangemerkt als *plannen* in Partner Center:

[![Scherm afbeelding toont de pagina partner centrum plannen.](media/cpp-pc-faq/plans.png "Hiermee wordt de pagina partner centrum plannen weer gegeven")](media/cpp-pc-faq/plans.png#lightbox)

Daarnaast worden de informatie die u eerder hebt verstrekt in de pagina's **Marketplace** of Details van de **winkel**  (Consulting Service, Power bi app) in de Cloud partner-portal nu verzameld op de **aanbiedings** pagina in Partner Center:

[! [Scherm afbeelding toont de pagina aanbieding van het partner centrum.] (Media/cpp-PC-FAQ/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

De informatie die u eerder hebt verstrekt voor Sku's op één pagina in de Cloud Partner-portal, kan nu worden verzameld op verschillende pagina's in partner centrum:

- Pagina installatie plannen
- Aanbiedings pagina plannen
- Pagina Beschik baarheid plannen
- De pagina technische configuratie plannen, zoals hier wordt weer gegeven:

[![Toont de pagina technische configuratie van het partner centrum.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Uw aanbiedings-ID wordt nu weer gegeven in de navigatie balk van de aanbieding:

![Toont de locatie van het Partner Center-aanbod-ID](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Stoppen met verkopen van een aanbieding

U kunt aanvragen om het [verkopen van een aanbieding](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) op Marketplace rechtstreeks vanuit de portal van partner centrum te stoppen. De optie is beschikbaar op de **overzichts** pagina van de aanbieding voor uw aanbieding.

[![Scherm afbeelding toont de Partner Center-pagina om te stoppen met het verkopen van een aanbieding.](media/cpp-pc-faq/stop-sell.png "Toont de Partner Center-pagina om te stoppen met het verkopen van een aanbieding")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Worden de Cloud Partner-portal REST-Api's nog steeds ondersteund?

De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken. De overgang naar het partner centrum introduceert kleine wijzigingen. Raadpleeg de onderstaande tabel om te controleren of uw code blijft werken in het partner centrum.

| API | Beschrijving wijziging | Impact |
| --- | --- | --- |
| POST publiceren, GoLive, annuleren | Voor gemigreerde aanbiedingen heeft de reactie header een andere indeling, maar blijft op dezelfde manier werken, waarbij een relatief pad wordt opgegeven om de bewerkings status op te halen. | Bij het verzenden van een van de bijbehorende POST-aanvragen voor een aanbieding, heeft de locatie header een van de twee indelingen, afhankelijk van de migratie status van de aanbieding: <ul><li>Niet-gemigreerde aanbiedingen: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Gemigreerde aanbiedingen: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET-bewerking | Voor aanbiedingen waarvoor eerder een veld met de meldings-e in het antwoord wordt ondersteund, wordt dit veld afgeschaft en wordt het niet langer geretourneerd voor gemigreerde aanbiedingen. | Voor gemigreerde aanbiedingen worden er geen meldingen meer verzonden naar de lijst met e-mail berichten die in de aanvragen zijn opgegeven. In plaats daarvan wordt de API-service uitgelijnd met het e-mail proces voor meldingen in het partner centrum om e-mail berichten te verzenden. In het bijzonder worden meldingen van de voortgang van de bewerking verzonden naar het e-mail adres dat is ingesteld in het gedeelte contact gegevens van de verkoper van uw account instellingen in partner centrum.<br><br>Zorg ervoor dat het e-mail adres dat is ingesteld in het gedeelte contact gegevens van de verkoper in de [account instellingen](https://partner.microsoft.com/dashboard/account/management) in Partner Center juist is voor het ontvangen van meldingen. |
|||