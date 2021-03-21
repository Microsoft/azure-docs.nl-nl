---
title: Fout opsporing in Visual Studio met Azure-toepassing Insights
description: Analyse van web-app-prestaties en diagnostische gegevens tijdens foutopsporing en algemeen gebruik.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: 2507dbf7bb8294c949f434d5fa96ccc0af9a7eb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563535"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Fouten opsporen in uw toepassingen met Azure-toepassing Insights in Visual Studio
In Visual Studio (2015 en hoger) kunt u de prestaties analyseren en problemen in uw ASP.NET web-app identificeren tijdens de foutopsporing en algemeen gebruik. Dit gebeurt aan de hand van telemetrie uit [Azure Application Insights](./app-insights-overview.md).

Als u uw ASP.NET-web-app hebt gemaakt met Visual Studio 2017 of hoger, beschikt deze al over de Application Insights-SDK. Anders [voegt u Application Insights aan uw app toe](./asp-net.md) als u dit nog niet hebt gedaan.

Als uw app wilt bewaken wanneer deze in live productie is, bekijkt u normaal gesproken de Application Insights-telemetrie in de [Azure-portal](https://portal.azure.com), waar u waarschuwingen kunt instellen en krachtige bewakingsprogramma's kunt toepassen. Voor foutopsporing kunt u echter ook de telemetrie in Visual Studio zoeken en analyseren. U kunt Visual Studio gebruiken om telemetrie te analyseren van uw productie site en van fout opsporing wordt uitgevoerd op uw ontwikkel computer. In het laatste geval kunt u foutopsporingsruns al analyseren nog voordat u de SDK hebt geconfigureerd om telemetrie naar de Azure-portal te verzenden. 

## <a name="debug-your-project"></a><a name="run"></a> Fouten opsporen in uw project
Voer uw web-app in de lokale foutopsporingsmodus door op F5 te drukken. Open verschillende pagina's om telemetrie te genereren.

In Visual Studio ziet u het aantal gebeurtenissen dat is vastgelegd door de module Application Insights in uw project.

![Tijdens het opsporen van fouten wordt in Visual Studio de knop Application Insights weergegeven.](./media/visual-studio/appinsights-09eventcount.png)

Klik op deze knop om uw telemetrie te doorzoeken. 

## <a name="application-insights-search"></a>Application Insights-zoekopdracht
In het zoekvenster van Application Insights worden geregistreerde gebeurtenissen weergegeven. (Als u zich bij het instellen van Application Insights hebt aangemeld bij Azure, kunt u dezelfde gebeurtenissen in de Azure Portal zoeken.)

![Klik met de rechtermuisknop op het project en kies Application Insights > Zoeken.](./media/visual-studio/34.png)

> [!NOTE] 
> Nadat u filters hebt in- of uitgeschakeld, klikt u op de knop Zoeken aan het einde van het veld Tekst zoeken.
>

U kunt zoeken met vrije tekst gebruiken voor alle velden in de gebeurtenissen. Zoek bijvoorbeeld naar een deel van de URl van een pagina, naar de waarde van een eigenschap, zoals de clientlocatie, of naar specifieke woorden in een traceringslogboek.

Klik op een gebeurtenis om de gedetailleerde eigenschappen ervan weer te geven.

Voor aanvragen voor uw web-app, kunt u verder klikken naar de code.

![Klik onder Aanvraagdetails door naar de code](./media/visual-studio/31.png)

U kunt ook de Verwante items openen om mislukte aanvragen of uitzonderingen te diagnosticeren.

![Blader onder Aanvraagdetails naar beneden, naar gerelateerde items](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Uitzonde ringen en mislukte aanvragen weer geven
Uitzonderingsrapporten worden weergegeven in het venster Zoeken. (In enkele oudere typen ASP.NET-toepassingen moet u [uitzonderingencontrole instellen](./asp-net-exceptions.md) om te zien welke uitzonderingen door het framework worden afgehandeld.)

Klik op een uitzondering voor een stack-trace. Als de code van de app in Visual Studio is geopend, kunt u via de stack-trace doorklikken naar de relevante coderegel.

![Scherm afbeelding toont het object about in een stack-trace.](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Samen vattingen van aanvragen en uitzonde ringen in de code weer geven
In de lens regel van de code boven elke handler-methode ziet u het aantal aanvragen en uitzonde ringen dat door Application Insights in de afgelopen 24 uur is geregistreerd.

![Scherm afbeelding toont een uitzonde ring in het dialoog venster context.](./media/visual-studio/21.png)

> [!NOTE] 
> Code Lens toont alleen Application Insights-gegevens als u [uw app hebt geconfigureerd om telemetrie te verzenden naar de Application Insights-portal](./asp-net.md).
>

[Meer informatie over Application Insights in Code Lens](./visual-studio-codelens.md)

## <a name="local-monitoring"></a>Lokale bewaking
(Uit Visual Studio 2015 update 2) Als u de SDK niet hebt geconfigureerd voor het verzenden van telemetrie naar de Application Insights Portal (zodat er geen instrumentatie sleutel in ApplicationInsights.config is), wordt in het venster diagnostische gegevens telemetrie van uw nieuwste foutopsporingssessie weer gegeven. 

Dit is handig als u al een eerdere versie van uw app hebt gepubliceerd. Zo voorkomt u dat de telemetrie van uw foutopsporingssessies in de Application Insights-portal wordt verward met de telemetrie over de gepubliceerde app.

Ook als u beschikt over [aangepaste telemetrie](./api-custom-events-metrics.md) waarin u fouten wilt opsporen voordat u deze naar de portal verzendt, komt deze functie van pas.

* *In eerste instantie heb ik Application Insights voor het verzenden van telemetrie naar de portal volledig geconfigureerd. Maar nu wil ik de telemetrie alleen bekijken in Visual Studio.*
  
  * De instellingen van het venster Zoeken bevat een optie om lokale diagnostische gegevens te doorzoeken, zelfs als uw app telemetrie verzendt naar de portal.
  * Als u wilt stoppen met het verzenden van telemetrie naar de portal, kunt u de regel `<instrumentationkey>...` van ApplicationInsights.config afmelden. Als u klaar bent om telemetrie naar de portal te verzenden, moet u de opmerking opheffen.


## <a name="next-steps"></a>Volgende stappen

 * **[Werken met de Application Insights Portal](./overview-dashboard.md)**. Bekijk Dash boards, krachtige hulpprogram ma's voor diagnose en analyse, waarschuwingen, een live afhankelijkheids kaart van uw toepassing en geëxporteerde telemetriegegevens. 

