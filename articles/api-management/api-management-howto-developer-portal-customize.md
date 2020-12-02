---
title: 'Zelfstudie: De ontwikkelaarsportal openen en aanpassen - Azure API Management | Microsoft Docs'
description: Volg deze zelfstudie voor informatie over het aanpassen van de API Management-ontwikkelaarsportal, een automatisch gegenereerde, volledig aanpasbare website met de documentatie van uw API's.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012971"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Zelfstudie: De ontwikkelaarsportal openen en aanpassen

De *ontwikkelaarsportal* is een automatisch gegenereerde, volledig aanpasbare website met de documentatie van uw API's. Hier kunnen API-gebruikers uw API's ontdekken, leren hoe ze deze kunnen gebruiken, en toegang aanvragen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toegang krijgen tot de beheerde versie van de ontwikkelaarsportal
> * Navigeren door de bijbehorende beheerinterface
> * De inhoud aanpassen
> * De wijzigingen publiceren
> * De gepubliceerde portal bekijken

Meer informatie over de ontwikkelaarsportal vindt u in het [Overzicht van de API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management-ontwikkelaarsportal - beheerdersmodus" border="false":::

## <a name="prerequisites"></a>Vereisten

- Voltooi de volgende quickstart: [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
- Importeer en publiceer een Azure API Management-exemplaar. Zie [Importeren en publiceren](import-and-publish.md) voor meer informatie

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Toegang krijgen tot de portal als beheerder

Volg de onderstaande stappen om toegang te krijgen tot de beheerde versie van de portal.

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer de knop **Ontwikkelaarsportal** in de bovenste navigatiebalk. Er wordt een nieuw browsertabblad geopend met een beheerversie van de portal.

## <a name="understand-the-portals-administrative-interface"></a>De beheerinterface van de portal begrijpen

### <a name="default-content"></a>Standaardinhoud 

Als u de portal de eerste keer opent, wordt de standaardinhoud automatisch op de achtergrond ingericht. Standaardinhoud is ontworpen om de mogelijkheden van de portal te presenteren, en om de aanpassingen te minimaliseren die nodig zijn om de portal persoonlijker te maken. In het [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md) vindt u meer informatie over wat is opgenomen in de portalinhoud.

### <a name="visual-editor"></a>Visuele editor

U kunt de inhoud van de portal aanpassen met de visuele editor. 
* Met de menusecties aan de linkerkant kunt u pagina's, media, indelingen, menu's, stijlen of website-instellingen maken of wijzigen. 
* Met de menu-items onderaan kunt u schakelen tussen viewports (bijvoorbeeld mobiel of desktop), de elementen van de portal weergeven die zichtbaar zijn voor geverifieerde of anonieme gebruikers, of opslaan of acties ongedaan maken.
* Voeg rijen toe aan een pagina door te klikken op een blauw pictogram met een plusteken. 
* Widgets (bijvoorbeeld tekst, afbeeldingen of een API-lijst) kunnen worden toegevoegd door te drukken op een grijs pictogram met een plusteken.
* Rangschik items opnieuw op een pagina met de interactie slepen-en-neerzetten. 

### <a name="layouts-and-pages"></a>Indelingen en pagina's

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Pagina's en indelingen" border="false":::

Indelingen bepalen hoe pagina's worden weergegeven. De standaardinhoud heeft bijvoorbeeld twee indelingen: de ene is van toepassing op de startpagina en de andere op alle resterende pagina's.

U kunt een indeling toepassen op een pagina door de bijbehorende URL-sjabloon te laten overeenkomen met de URL van de pagina. Een indeling met een URL-sjabloon `/wiki/*` wordt bijvoorbeeld toegepast op elke pagina die het segment `/wiki/` bevat in de URL: `/wiki/getting-started`, `/wiki/styles`, enzovoort.

In de voorgaande afbeelding is inhoud die hoort bij de indeling, gemarkeerd in blauw, terwijl de pagina is gemarkeerd in rood. De menusecties zijn respectievelijk gemarkeerd.

### <a name="styling-guide"></a>Stijlgids

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Stijlgids" border="false":::

De stijlgids is een paneel dat is gemaakt voor ontwerpers. Het biedt een overzicht van alle visuele elementen in de portal en biedt de mogelijkheid om deze vorm te geven. Vormgeving is hiërarchisch: veel elementen nemen de eigenschappen over van andere elementen. Knopelementen gebruiken bijvoorbeeld kleuren voor tekst en achtergrond. Als u de kleur van een knop wilt wijzigen, moet u de oorspronkelijke kleurvariant wijzigen.

Als u een variant wilt bewerken, selecteert u deze en selecteert u het potloodpictogram dat er overheen wordt weergegeven. Sluit het pop-upvenster nadat u de wijzigingen hebt aangebracht.

### <a name="save-button"></a>De knop Opslaan

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="De knop Opslaan" border="false":::

Wanneer u een wijziging aanbrengt in de portal, moet u deze handmatig opslaan door in het menu onderaan de knop **Opslaan** te selecteren, of op [Ctrl]+[S] te drukken. Wanneer u de wijzigingen opslaat, wordt de gewijzigde inhoud automatisch geüpload naar de API Management-service.

## <a name="customize-the-portals-content"></a>Inhoud van de portal aanpassen

Voordat u de portal beschikbaar maakt voor bezoekers, moet u de automatisch gegenereerde inhoud personaliseren. Aanbevolen wijzigingen zijn onder andere de indelingen, stijlen en de inhoud van de startpagina.

> [!NOTE]
> Vanwege integratieoverwegingen kunnen de volgende pagina's niet worden verwijderd of verplaatst onder een andere URL: `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso`, `/signup`.

### <a name="home-page"></a>Startpagina

De standaardversie van de **Startpagina** staat vol met tijdelijke aanduidingen voor inhoud. U kunt hele secties met deze inhoud verwijderen, of de structuur handhaven en de elementen één voor één aanpassen. Vervang de gegenereerde tekst en afbeeldingen door uw eigen inhoud, en zorg ervoor dat de koppelingen naar de gewenste locaties verwijzen.

### <a name="layouts"></a>Indelingen

Vervang het automatisch gegenereerde logo in de navigatiebalk door uw eigen afbeelding.

### <a name="styling"></a>Vormgeving

Hoewel u geen stijlen hoeft aan te passen, kunt u overwegen bepaalde elementen aan te passen. U kunt bijvoorbeeld de primaire kleur aanpassen aan de kleur van uw merk.

### <a name="customization-example"></a>Voorbeeld van aanpassing

In de volgende video ziet u hoe u de inhoud van de portal bewerkt, het uiterlijk van de website aanpast, en de wijzigingen publiceert.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> De portal publiceren

Als u de portal en de meest recente wijzigingen in deze portal beschikbaar wilt maken voor bezoekers, moet u de portal *publiceren*. U kunt de portal publiceren in de beheerinterface van de portal, of vanuit Azure Portal.

### <a name="publish-from-the-administrative-interface"></a>Publiceren vanuit de beheerinterface

1. Zorg ervoor dat u de wijzigingen hebt opgeslagen door het pictogram **Opslaan** te selecteren.
1. Selecteer in de sectie **Bewerkingen** van het menu de optie **Website publiceren**. Deze bewerking kan enkele minuten duren.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Portal publiceren" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publiceren vanuit Azure Portal

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar.
1. Selecteer in het linkermenu onder **Ontwikkelaarsportal** de optie **Overzicht van de portal**.
1. Selecteer in het **Overzicht van de portal** de optie **Publiceren**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Portal publiceren vanuit Azure Portal":::

> [!NOTE]
> Na wijzigingen in de configuratie van de API Management-service moet de portal opnieuw worden gepubliceerd. U kunt de portal bijvoorbeeld opnieuw publiceren na het toewijzen van een aangepast domein, het bijwerken van de id-providers, het instellen van delegering, of het opgeven van voorwaarden voor aanmelding en producten.


## <a name="visit-the-published-portal"></a>De gepubliceerde portal bezoeken

Nadat u de portal hebt gepubliceerd, kunt u deze bereiken via dezelfde URL als het beheerpaneel, bijvoorbeeld `https://contoso-api.developer.azure-api.net`. Bekijk de portal in een afzonderlijke browsersessie (met behulp van de modi Incognito of Private Browsing) als een externe bezoeker.

## <a name="apply-the-cors-policy-on-apis"></a>Het CORS-beleid toepassen op API's

Schakel CORS (Cross-Origin Resource Sharing) in voor uw API's om de bezoekers van de portal de API's te laten testen via de ingebouwde interactieve console. Raadpleeg het [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md#cors) voor de details.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)
- [Migreer naar de nieuwe ontwikkelaarsportal](developer-portal-deprecated-migration.md) van de afgeschafte verouderde portal.