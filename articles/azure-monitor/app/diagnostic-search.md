---
title: Zoek functie gebruiken in Azure-toepassing Insights | Microsoft Docs
description: Zoek en filter ruwe telemetriegegevens die zijn verzonden door uw web-app.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: f7c2229a2e497fb052db79d61fcfeff140897798
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579543"
---
# <a name="using-search-in-application-insights"></a>Zoeken in Application Insights gebruiken

Search is een functie van [Application Insights](./app-insights-overview.md) waarmee u afzonderlijke telemetriegegevens, zoals pagina weergaven, uitzonde ringen of webaanvragen, kunt zoeken en verkennen. En u kunt logboek traceringen en gebeurtenissen weer geven die u hebt gecodeerd.

(Voor complexere query's over uw gegevens gebruikt u [Analytics](../logs/log-analytics-tutorial.md).)

## <a name="where-do-you-see-search"></a>Waar ziet u zoeken?

### <a name="in-the-azure-portal"></a>In Azure Portal

U kunt Diagnostische Zoek opdrachten openen vanaf het tabblad Application Insights overzicht van uw toepassing (in de bovenste balk) of onder onderzoek aan de linkerkant.

![Tabblad Zoeken](./media/diagnostic-search/view-custom-events.png)

Ga naar de vervolg keuzelijst gebeurtenis typen om een lijst met telemetriegegevens te bekijken: server aanvragen, pagina weergaven, aangepaste gebeurtenissen die u hebt gecodeerd, enzovoort. Boven aan de lijst met resultaten ziet u een overzichts grafiek met aantallen gebeurtenissen gedurende een bepaalde periode.

Klik op het vervolg keuzemenu of op vernieuwen om nieuwe gebeurtenissen op te halen.

### <a name="in-visual-studio"></a>In Visual Studio

In Visual Studio is er ook een Application Insights Zoek venster. Het is vooral nuttig voor het weer geven van telemetrie-gebeurtenissen die zijn gegenereerd door de toepassing die u wilt opsporen. Maar het kan ook de gebeurtenissen weer geven die zijn verzameld uit uw gepubliceerde app op het Azure Portal.

Open het venster zoeken in Visual Studio:

![Visual Studio Open Application Insights zoeken](./media/diagnostic-search/32.png)

Het venster zoeken bevat functies die vergelijkbaar zijn met de webportal:

![Zoek venster van Visual Studio Application Insights](./media/diagnostic-search/34.png)

Het tabblad spoor bewerking is beschikbaar wanneer u een aanvraag of een pagina weergave opent. Een ' Operation ' is een reeks gebeurtenissen die aan een enkele aanvraag of pagina weergave is gekoppeld. Afhankelijkheids aanroepen, uitzonde ringen, traceer logboeken en aangepaste gebeurtenissen kunnen bijvoorbeeld deel uitmaken van één bewerking. Op het tabblad spoor bewerking worden de timing en duur van deze gebeurtenissen grafisch weer gegeven ten opzichte van de aanvraag of pagina weergave.

## <a name="inspect-individual-items"></a>Afzonderlijke items controleren

Selecteer een telemetrie-item om sleutel velden en gerelateerde items weer te geven.

![Scherm afbeelding van een afzonderlijke afhankelijkheids aanvraag](./media/diagnostic-search/telemetry-item.png)

Hiermee wordt de weergave met end-to-end-transactiedetails geopend.

## <a name="filter-event-types"></a>Gebeurtenis typen filteren

Open de vervolg keuzelijst gebeurtenis typen en kies de gebeurtenis typen die u wilt weer geven. (Als u de filters later wilt herstellen, klikt u op opnieuw instellen.)

De gebeurtenis typen zijn:

* **Traceren**  -  [Diagnostische logboeken](./asp-net-trace-logs.md) , waaronder TrackTrace-, log4Net-, NLog-en System. Diagnostics. trace-aanroepen.
* **Aanvraag** -HTTP-aanvragen die door uw server toepassing worden ontvangen, waaronder pagina's, scripts, afbeeldingen, stijl bestanden en gegevens. Deze gebeurtenissen worden gebruikt voor het maken van de aanvraag-en antwoord overzichts diagrammen.
* **Pagina weergave**  -  [Telemetrie verzonden door de webclient](./javascript.md), die wordt gebruikt om pagina weergave rapporten te maken.
* **Aangepaste gebeurtenis** : als u aanroepen naar track Event () hebt ingevoegd om het [gebruik te controleren](./api-custom-events-metrics.md), kunt u deze hier zoeken.
* **Uitzonde ring** -niet-onderschepte [uitzonde ringen in de server](./asp-net-exceptions.md)en die u registreert met behulp van TrackException ().
* **Afhankelijkheid**  -  [Aanroepen van uw server toepassing](./asp-net-dependencies.md) naar andere services, zoals rest api's of data bases en Ajax-aanroepen vanuit uw [client code](./javascript.md).
* **Beschik baarheid** -resultaten van [beschikbaarheids testen](./monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filteren op eigenschaps waarden

U kunt gebeurtenissen filteren op de waarden van hun eigenschappen. De beschik bare eigenschappen zijn afhankelijk van de gebeurtenis typen die u hebt geselecteerd. Klik op het filter pictogram ![Filter pictogram](./media/diagnostic-search/filter-icon.png) om te starten.

Het kiezen van geen waarden van een bepaalde eigenschap heeft hetzelfde effect als het kiezen van alle waarden. Hiermee schakelt u het filteren van de eigenschap uit.

U ziet dat in het aantal rechts van de filter waarden wordt weer gegeven hoeveel exemplaren er in de huidige gefilterde set aanwezig zijn.

## <a name="find-events-with-the-same-property"></a>Gebeurtenissen met dezelfde eigenschap zoeken

Als u alle items met dezelfde eigenschaps waarde wilt zoeken, typt u deze in de zoek balk of klikt u op het selectie vakje bij eigenschappen op het tabblad filter.

![Klik op het selectie vakje van een eigenschap op het tabblad Filter](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>De gegevens zoeken

> [!NOTE]
> Als u complexere query's wilt schrijven, opent u [**Logboeken (Analytics)**](../logs/log-analytics-tutorial.md) aan de bovenkant van de Blade zoeken.
>

U kunt zoeken naar termen in een van de eigenschaps waarden. Dit is handig als u [aangepaste gebeurtenissen](./api-custom-events-metrics.md) met eigenschaps waarden hebt geschreven.

Mogelijk wilt u een tijds bereik instellen, omdat Zoek opdrachten in een kortere reeks sneller zijn.

![Diagnostische zoek opdracht openen](./media/diagnostic-search/search-property.png)

Zoeken naar volledige woorden, geen subtekenreeksen. Gebruik aanhalings tekens voor het insluiten van speciale tekens.

| Tekenreeks | *Niet* gevonden | Gevonden |
| --- | --- | --- |
| HomeController. about |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Verenigde Staten|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Dit zijn de zoek expressies die u kunt gebruiken:

| Voorbeeldquery | Effect |
| --- | --- |
| `apple` |Alle gebeurtenissen zoeken in het tijds bereik waarvan de velden het woord ' Apple ' bevatten |
| `apple AND banana` <br/>`apple banana` |Gebeurtenissen zoeken die beide woorden bevatten. Gebruik ' en ', niet ' en '. <br/>Verkorte vorm. |
| `apple OR banana` |Gebeurtenissen zoeken die een van beide woorden bevatten. Gebruik ' of ', niet ' of '. |
| `apple NOT banana` |Gebeurtenissen zoeken die één woord bevatten, maar niet de andere. |

## <a name="sampling"></a>Steekproeven

Als uw app een grote hoeveelheid telemetrie genereert (en u de ASP.NET SDK-versie 2.0.0-beta3 of hoger gebruikt), beperkt de adaptieve steekproef module automatisch het volume dat naar de portal wordt verzonden door alleen een representatieve Fractie van gebeurtenissen te verzenden. Gebeurtenissen die betrekking hebben op dezelfde aanvraag, worden echter geselecteerd of als groep opgeheven, zodat u kunt navigeren tussen gerelateerde gebeurtenissen.

[Meer informatie over steekproeven](./sampling.md).

## <a name="create-work-item"></a>Werk item maken

U kunt een bug in GitHub of Azure DevOps maken met de details van een telemetrie-item.

Ga naar de weer gave end-to-end trans actie-Details door te klikken op een telemetrie-item en vervolgens **werk item maken** te selecteren.

![Klik op nieuw werk item, bewerk de velden en klik vervolgens op OK.](./media/diagnostic-search/work-item.png)

De eerste keer dat u dit doet, wordt u gevraagd een koppeling naar uw Azure DevOps-organisatie en-project te configureren.

(U kunt ook de koppeling op het tabblad werk items configureren.)

## <a name="send-more-telemetry-to-application-insights"></a>Meer telemetrie naar Application Insights verzenden

Naast de out-of-the-box-telemetrie die wordt verzonden door Application Insights SDK, kunt u het volgende doen:

* Logboek traceringen vastleggen vanuit uw favoriete Framework voor logboek registratie in [.net](./asp-net-trace-logs.md) of [Java](./java-trace-logs.md). Dit betekent dat u in uw logboek traceringen kunt zoeken en deze aan de hand van pagina weergaven, uitzonde ringen en andere gebeurtenissen moet correleren.
* [Schrijf code](./api-custom-events-metrics.md) voor het verzenden van aangepaste gebeurtenissen, pagina weergaven en uitzonde ringen.

[Meer informatie over het verzenden van Logboeken en aangepaste telemetrie naar Application Insights](./asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>Q & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Hoeveel gegevens worden er bewaard?

Zie de [limieten samen vatting](./pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hoe kan ik POST gegevens in mijn server aanvragen zien?

De POST gegevens worden niet automatisch geregistreerd, maar u kunt wel [TrackTrace of logboek aanroepen](./asp-net-trace-logs.md)gebruiken. Plaats de POST-gegevens in de para meter Message. U kunt geen filter op het bericht uitvoeren op dezelfde manier als u op Eigenschappen filtert, maar de maximale grootte is langer.

## <a name="next-steps"></a><a name="add"></a>Volgende stappen

* [Complexe query's schrijven in analyse](../logs/log-analytics-tutorial.md)
* [Logboeken en aangepaste telemetrie naar Application Insights verzenden](./asp-net-trace-logs.md)
* [Testen van Beschik baarheid en reactie snelheid instellen](./monitor-web-app-availability.md)
* [Problemen oplossen](../faq.md)