---
title: Hero-voorbeeld van groepsgesprek
titleSuffix: An Azure Communication Services sample overview
description: Overzicht van hero-voorbeeld voor aanroepen waarin gebruik wordt gemaakt van Azure Communication Services om ontwikkelaars in staat te stellen meer te weten te komen over de interne mechanismen van het voorbeeld.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d73024c6c227a0ef0b05a871b455411e93cb70ba
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761779"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Aan de slag met het hero-voorbeeld voor groepsgesprekken

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

> [!IMPORTANT]
> [Dit voorbeeld is beschikbaar op GitHub.](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Het **hero-voorbeeld van groepsgesprekken** laat zien hoe de webclientbibliotheek van Communication Services-aanroepen kan worden gebruikt voor het bouwen van een groepsgesprekservaring.

In deze quickstart over hero-voorbeelden komt u te weten hoe het voorbeeld werkt voordat u het op uw lokale computer gaat uitvoeren. Vervolgens gaat u het voorbeeld in Azure implementeren met behulp van uw eigen Azure Communication Services-resources.

## <a name="overview"></a>Overzicht

Het voorbeeld heeft zowel een toepassing aan de clientzijde als een toepassing aan de serverzijde. De **toepassing aan de clientzijde** is een React/Redux-webtoepassing die gebruikmaakt van het Fluent UI-framework van Microsoft. Met deze toepassing worden aanvragen verzonden naar een ASP.NET Core-**toepassing aan de serverzijde** waarmee de toepassing aan de clientzijde kan worden verbonden met Azure. 

Het voorbeeld ziet er als volgt uit:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Schermopname van de landingspagina van de voorbeeldtoepassing.":::

Wanneer de knop Een gesprek starten kiest, wordt door de webtoepassing een toegangstoken voor gebruikers opgehaald bij de toepassing op de server. Vervolgens wordt dit token gebruikt om de client-app met Azure Communication Services te verbinden. Zodra het token is opgehaald, wordt u gevraagd om de camera en de microfoon die u wilt gebruiken, op te geven. Met behulp van de wisselknoppen kunt u uw apparaten in-/uitschakelen:

:::image type="content" source="./media/calling/pre-call.png" alt-text="Schermopname van het scherm voorafgaand aan het gesprek van de voorbeeldtoepassing.":::

Zodra u uw weergavenaam en apparaten hebt geconfigureerd, kunt u deelnemen aan de gesprekssessie. Nu wordt het hoofdcanvas van het gesprek weergegeven. Dit is de locatie van de basisgesprekservaring.

:::image type="content" source="./media/calling/main-app.png" alt-text="Schermopname van het hoofdscherm van de voorbeeldtoepassing.":::

Onderdelen van het hoofdgespreksscherm:

1. **Mediagalerie**: Het hoofdgebied waarin de deelnemers worden weergegeven. Als deelnemers hun camera hebben ingeschakeld, wordt hier hun videofeed weergegeven. Elke deelnemer beschikt over een afzonderlijke tegel waarop hun weergavenaam en videostream (indien beschikbaar) worden weergegeven
2. **Header**: Dit is het deel met de primaire besturingselementen voor het gesprek waarmee u tussen de instellingen en de zijbalk met deelnemers kunt wisselen, beeld en geluid kunt in- en uitschakelen, het scherm kunt delen en het gesprek kunt verlaten.
3. **Zijbalk**: Hier worden informatie over de deelnemers en de instellingen weergegeven wanneer u tussen de besturingselementen in de header wisselt. U kunt het onderdeel verwijderen door op X te klikken in de rechterbovenhoek. In de zijbalk met deelnemers ziet u een lijst met alle deelnemers en een koppeling om meer gebruikers voor het gesprek uit te nodigen. In de zijbalk met instellingen kunt u de microfoon- en camera-instellingen configureren.

Hieronder vindt u meer informatie over de vereisten en stappen voor het instellen van het voorbeeld.

## <a name="prerequisites"></a>Vereisten

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie
- [Node.js (12.18.4 en hoger)](https://nodejs.org/en/download/)
- [Visual Studio (2019 en hoger)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (zorg ervoor dat u de versie installeert die overeenkomt met uw Visual Studio-exemplaar, 32-bits of 64-bits)
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../quickstarts/create-communication-resource.md) voor meer informatie. Voor deze quickstart moet u de **verbindingsreeks** van uw resource vastleggen.

## <a name="locally-deploy-the-service--client-applications"></a>De service en clienttoepassingen lokaal implementeren

Het voorbeeld voor groepsgesprekken bestaat in feite uit twee toepassingen: ClientApp en de Service.NET-app.

Als u het voorbeeld lokaal wilt implementeren, moeten beide toepassingen worden gestart. Wanneer u de server-app opent vanuit de browser, wordt de lokaal geïmplementeerde ClientApp gebruikt voor de gebruikerservaring.

U kunt het voorbeeld lokaal testen door meerdere browsersessies te openen met de URL van uw gesprek om een gesprek met meerdere gebruikers te simuleren.

### <a name="before-running-the-sample-for-the-first-time"></a>Voordat u het voorbeeld voor de eerste keer uitvoert

1. Open een instantie van PowerShell, Windows Terminal, de opdrachtprompt of een vergelijkbare service en ga naar de map waarnaar u het voorbeeld wilt klonen.
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Haal de `Connection String` op uit Azure Portal. Zie [Een Azure Communications-resource maken](../quickstarts/create-communication-resource.md) voor meer informatie over verbindingsreeksen
4. Zodra u de `Connection String` hebt opgehaald, voegt u de verbindingsreeks toe aan het bestand **Calling/appsetting.json**. U vindt dit bestand in de Service .NET-map. Voer in de variabele uw verbindingsreeks in: `ResourceConnectionString`.

### <a name="local-run"></a>Lokaal uitvoeren

1. Ga naar de map Calling en open de oplossing `Calling.csproj` in Visual Studio
2. Voer project `Calling` uit. De browser wordt geopend op localhost:5001

#### <a name="troubleshooting"></a>Problemen oplossen

- De oplossing wordt niet gebouwd; er worden fouten weergegeven tijdens de installatie/bouw van NPM.

   Probeer de projecten te wissen/opnieuw te bouwen.

## <a name="publish-the-sample-to-azure"></a>Het voorbeeld naar Azure publiceren

1. Klik met de rechtermuisknop op het `Calling`-project en selecteer Publiceren.
2. Maak een nieuw publicatieprofiel en selecteer uw Azure-abonnement.
3. Voeg vóór publicatie uw verbindingsreeks toe met `Edit App Service Settings`, vul `ResourceConnectionString` in als de sleutel en geef uw verbindingsreeks (gekopieerd uit appsettings.json) als de waarde op.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

>[!div class="nextstepaction"] 
>[Het voorbeeld downloaden uit GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Raadpleeg voor meer informatie de volgende artikelen:

- Vertrouwd raken met [het gebruik van de clientbibliotheek voor aanroepen](../quickstarts/voice-video-calling/calling-client-samples.md)
- Meer informatie over [de werking van aanroepen](../concepts/voice-video-calling/about-call-types.md)
- Het voor beeld van [Contoso med-app](https://github.com/Azure-Samples/communication-services-contoso-med-app) bekijken

## <a name="additional-reading"></a>Meer artikelen

- [Azure Communication GitHub](https://github.com/Azure/communication): u vindt meer voorbeelden en informatie op de officiële GitHub-pagina
- [Redux](https://redux.js.org/): statusbeheer op de client
- [FluentUI](https://aka.ms/fluent-ui): door Microsoft ondersteunde bibliotheek voor de gebruikersinterface
- [React](https://reactjs.org/): bibliotheek voor het ontwikkelen van gebruikersinterfaces
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1): een framework voor het bouwen van webtoepassingen
