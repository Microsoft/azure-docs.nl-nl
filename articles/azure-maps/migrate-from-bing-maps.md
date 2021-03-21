---
title: 'Zelfstudie: Migreren vanuit Bing Maps naar Azure Maps | Microsoft Azure Maps'
description: Een zelfstudie over hoe u vanuit Bing Maps migreert naar Microsoft Azure Maps. U wordt begeleid bij het overschakelen naar Azure Maps-API's en SDK's.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388954"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>Zelfstudie: Migrate van Bing Maps naar Azure Maps

In deze handleiding vindt u informatie over het migreren van web-, mobiele en servertoepassingen van Bing Maps naar het Azure Maps-platform. Deze handleiding bevat vergelijkende codevoorbeelden, suggesties voor migratie en best practices voor migratie naar Azure Maps. 

In deze zelfstudie leert u:

> [!div class="checklist"]
> * Vergelijking op hoog niveau voor equivalente Bing Kaarten-functies die beschikbaar zijn in Azure Maps.
> * Met welke licentieverschillen u rekening moet houden.
> * Uw migratie plannen.
> * Waar u technische resources en ondersteuning vindt.

## <a name="prerequisites"></a>Vereisten

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
2. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

## <a name="azure-maps-platform-overview"></a>Overzicht van het Azure Maps-platform

Azure Maps biedt ontwikkelaars uit alle branches krachtige georuimtelijke mogelijkheden, gecombineerd met de meest recente kaartgegevens die beschikbaar zijn, om mobiele en webapplicaties te voorzien van geografische context. Azure Maps is een Azure One API-conforme set REST API's voor kaarten, zoekfunctionaliteit, routebeschrijvingen, verkeer, tijdzones, geofencing, kaartgegevens,weersgegevens en nog veel meer services, aangevuld met web- en Android-SDK's om het ontwikkelproces eenvoudig, flexibel en platformoverstijgend te maken. [Azure Maps is ook beschikbaar in Power BI](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Algemene platformvergelijking

De volgende tabel bevat een lijst op hoofdlijnen van de functies van Bing Maps en de relatieve ondersteuning voor die functies in Azure Maps. Deze lijst bevat geen aanvullende Azure Maps-functies, zoals toegankelijkheid, API's voor geofencing, verkeersdiensten, ruimtelijke bewerkingen, directe toegang tot kaarttegels en batchservices.

| Bing Maps-functie                     | Azure Maps-ondersteuning |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | Gepland            |
| UWP SDK                               | N.v.t.                 |
| WPF SDK                               | N.v.t.                 |
| REST Service-API's                     | ✓                  |
| Automatische suggestie                           | ✓                  |
| Routebeschrijving (inclusief voor vrachtwagens)          | ✓                  |
| Afstandsmatrix                       | ✓                  |
| Hoogtetoenames                            | ✓ (Preview)        |
| Afbeeldingen - Statische kaart                  | ✓                  |
| Metagegevens van afbeeldingen                      | ✓                  |
| Isochronen                            | ✓                  |
| Local Insights                        | ✓                  |
| Lokale zoekopdracht                          | ✓                  |
| Locatieherkenning                  | ✓                  |
| Locaties (forward/reverse geocodering) | ✓                  |
| Geoptimaliseerde reisroutes            | Gepland            |
| Uitlijnen op weg                         | ✓                  |
| Services voor ruimtelijke gegevens (SDS)           | Gedeeltelijk            |
| Tijdzone                             | ✓                  |
| Verkeersincidenten                     | ✓                  |
| Door configuratie gestuurde kaarten             | N.v.t.                |

Bing Maps biedt basisverificatie op basis van een sleutel. Azure Maps biedt zowel basisverificatie op basis van sleutels als uiterst veilige Azure Active Directory-verificatie.

## <a name="licensing-considerations"></a>Licentieoverwegingen

Wanneer u van Bing Kaarten naar Azure Maps migreert, moet u rekening houden met de volgende informatie met betrekking tot licenties.

* Azure Maps brengt kosten in rekening voor het gebruik van interactieve kaarten op basis van het aantal geladen kaarttegels; Bing brengt kosten in rekening voor het laden van kaartbesturingselementen (sessies). In Azure Maps worden kaarttegels automatisch opgeslagen in de cache om de kosten voor ontwikkelaars te verlagen. Er wordt één Azure Maps-transactie gegenereerd voor elke 15 kaarttegels die worden geladen. De interactieve SDK's van Azure Maps gebruiken tegels van 512 pixels en er wordt gemiddeld een of minder transacties per paginaweergave gegenereerd.

* Met Azure Maps kunnen gegevens van het platform worden opgeslagen in Azure. Ook kunnen ze overeenkomstig de [gebruiksvoorwaarden](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) maximaal zes maanden ergens anders in een cachegeheugen worden opgeslagen.

Hier volgen enkele resources met betrekking tot licenties voor Azure Maps:

-   [Pagina met Azure Maps-prijzen](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps-gebruiksvoorwaarden](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (opgenomen in de Voorwaarden voor Online Diensten van Microsoft)
-   [De juiste prijscategorie kiezen in Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Voorgesteld migratieplan

Hier volgt een voorbeeld van een migratieplan op hoog niveau.

1.  Inventariseer de Bing Maps-SDK’s en -services die uw toepassing gebruikt en verifieer of Azure Maps alternatieve SDK’s en services biedt waarnaar u kunt migreren.
2.  Maak een Azure-abonnement via <https://azure.com> als u nog geen abonnement hebt.
3.  Maak een Azure Maps-account ([documentatie](./how-to-manage-account-keys.md)) en de verificatiesleutel of Azure Active Directory ([documentatie](./how-to-manage-authentication.md)).
4.  Migreer uw toepassingscode.
5.  Test uw gemigreerde toepassing.
6.  Implementeer uw gemigreerde toepassing naar productie.

## <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken

Ga als volgt te werk om een Azure Maps-account te maken en toegang te krijgen tot het Azure Maps-platform:

1. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
2. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
3. Maak een [Azure Maps-account](./how-to-manage-account-keys.md).
4. [Download uw Azure Maps-abonnementssleutel](./how-to-manage-authentication.md#view-authentication-details) of configureer Azure Active Directory-verificatie voor verbeterde beveiliging.

## <a name="azure-maps-technical-resources"></a>Technische informatiebronnen voor Azure Maps

Hier volgt een lijst met nuttige technische informatiebronnen voor Azure Maps.

* Overzicht: <https://azure.com/maps>
* Documentatie: <https://aka.ms/AzureMapsDocs>
* Voorbeelden van Web-SDK-code: <https://aka.ms/AzureMapsSamples>
* Ontwikkelaarsforums: <https://aka.ms/AzureMapsForums>
* Video's: <https://aka.ms/AzureMapsVideos>
* Blog: <https://aka.ms/AzureMapsBlog>
* Azure Maps-feedback (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Ondersteuning voor migratie

Ontwikkelaars kunnen migratieondersteuning zoeken via de [forums](/answers/topics/azure-maps.html) of via een van de vele opties voor ondersteuning voor Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Nieuwe terminologie

De volgende lijst bevat algemene voorwaarden voor Bing Kaarten en de bijbehorende voorwaarden voor Azure Maps.

| Bing Maps-term                    | Azure Maps-term                                                |
|-----------------------------------|----------------------------------------------------------------|
| Satellietbeeld                            | Satelliet of lucht                                            |
| Aanwijzingen                        | Kan ook Routering worden genoemd                             |
| Entiteiten                          | Geometrieën of functies                                         |
| `EntityCollection`                | Gegevensbron of -laag                                           |
| `Geopoint`                        | Positie                                                       |
| `GeoXML`                          | XML-bestanden in de ruimtelijke IO-module                             |
| Overlay voor terrein                    | Afbeeldingslaag                                                    |
| Hybride (in verwijzing naar type kaart) | Satelliet met wegen                                           |
| Infobox                           | Popup                                                          |
| Locatie                          | Positie                                                       |
| `LocationRect`                    | Begrenzingsvak                                                   |
| Type toewijzing                          | Kaartstijl                                                      |
| Navigatiebalk                    | Kaartstijlkiezer, zoombesturingselement, besturingselement voor pitch, kompasbesturingselement |
| Markeringspunt                           | Tekenlaag, symboollaag of HTML-markering                      |

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen vindt u meer informatie over het migreren van uw Bing Maps-toepassing:

> [!div class="nextstepaction"]
> [Een web-app migreren](migrate-from-bing-maps-web-app.md)
