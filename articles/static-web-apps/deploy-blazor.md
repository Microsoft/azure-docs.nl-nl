---
title: 'Zelfstudie: Een statische web-app bouwen met Blazor in Azure Static Web Apps'
description: Meer informatie over het bouwen van een Azure Static Web Apps-website met Blazor.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 0086f7f68fd05d6925d19c7ab457fbc125e36be4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350225"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Zelfstudie: Een statische web-app bouwen met Blazor in Azure Static Web Apps

Met Azure Static Web Apps wordt een website gepubliceerd in een productieomgeving door apps te bouwen vanuit een GitHub-opslagplaats. In deze snelsartgids implementeert u een webtoepassing in Azure Static Web Apps met behulp van de Azure Portal.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Vereisten

- Een [GitHub](https://github.com)-account
- [Azure](https://portal.azure.com)-account

## <a name="application-overview"></a>Overzicht van toepassing

Met Azure Static Web Apps kunt u statische webtoepassingen maken die worden ondersteund door een serverloze back-end. De volgende zelfstudie laat zien hoe u een C# Blazor-webtoepassing implementeert waarmee weergegevens worden geretourneerd.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Volledige Blazor-app":::

De app die in deze zelfstudie wordt aanbevolen, bestaat uit drie verschillende Visual Studio-projecten:

- **Api**: De C# Azure Functions-toepassing die het API-eindpunt implementeert dat weerinformatie levert aan de statische app. De **WeatherForecastFunction** retourneert een matrix van `WeatherForecast` objects.

- **Client**: Het front-end Blazor-web-assembly-webproject. Er wordt een [terugvalroute](#fallback-route) geïmplementeerd om ervoor te zorgen dat aan alle routes het _index. html_-bestand worden geleverd.

- **Gedeeld**: Bevat algemene klassen waarnaar wordt verwezen door de API- en client-projecten, waarmee gegevens kunnen stromen van API-eindpunt naar de front-end-web-app. De [-`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs)-klasse wordt gedeeld tussen beide apps.

Deze projecten vormen samen de vereiste onderdelen om een Blazor-Web-assembly-toepassing te maken, die wordt uitgevoerd in de browser die wordt ondersteund door een API-back-end.

## <a name="fallback-route"></a>Alternatieve route

De toepassing geeft URL's weer zoals _/counter_ en _/fetchdata_ die aan specifieke routes van de toepassing zijn toegewezen. Omdat deze app is geïmplementeerd als een toepassing met één pagina, krijgt elke route het bestand _index.html_. Om ervoor te zorgen dat voor een aanvraag voor een willekeurig pad _index.html-_ een [terugvalroute](./routes.md#fallback-routes) wordt geïmplementeerd in het _routes.json_-bestand dat is te vinden in de map _wwwroot_ van het client-project.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

De bovenstaande configuratie zorgt ervoor dat aanvragen voor elke route in de app de pagina _index.html_ retourneert.

## <a name="create-a-repository"></a>Een opslagplaats maken

In dit artikel wordt gebruikgemaakt van een GitHub-opslagplaatssjabloon, zodat u eenvoudig aan de slag kunt gaan. De sjabloon biedt een starters-app die wordt geïmplementeerd naar Azure Static Web Apps.

1. Zorg ervoor dat u bent aangemeld bij GitHub en ga naar de volgende locatie om een nieuwe opslagplaats te maken:
    - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Geef uw opslagplaats de naam **mijn-eerste-statische-blazor-app**

## <a name="create-a-static-web-app"></a>Statische web-app maken

Nu de opslagplaats is gemaakt, kunt u een statische web-app maken in de Azure Portal.

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Selecteer **Een resource maken**
1. Zoek naar **Static Web Apps**
1. Selecteer **Static Web Apps (preview)**
1. Selecteer **Maken**

Op het tabblad _Basisbeginselen_ kunt u de nieuwe app configureren en aan een GitHub-opslagplaats koppelen.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Tabblad Basisbeginselen":::

1. Selecteer uw _Azure-abonnement_
1. Een nieuwe _Resourcegroep_ selecteren of maken
1. Geef de app de naam **mijn-eerste-statische-blazor-app**
    - Geldige tekens zijn `a-z` (hoofdlettergevoelig), `0-9` en `-`.
1. Selecteer de _Regio_ het dichtst bij u in de buurt
1. Selecteer de **gratis** _SKU_
1. Selecteer de knop **Aanmelden bij GitHub** en verifieer u bij GitHub

Nadat u zich hebt aangemeld bij GitHub, voert u de gegevens van de opslagplaats in.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Gegevens van opslagplaats":::

1. Kies de gewenste _organisatie_
1. Selecteer **mijn-eerste-statische-blazor-app** in de vervolgkeuzelijst _Opslagplaats_
1. Selecteer **Hoofd** in de vervolgkeuzelijst _Vertakking_

    Als u geen opslagplaatsen ziet, moet u mogelijk Azure Static Web Apps autoriseren in GitHub. Ga naar de GitHub-opslagplaats en ga naar **Instellingen > Toepassingen > Geautoriseerde OAuth-apps**, selecteer **Azure Static Web Apps** en selecteer **Verlenen**. Voor organisatie-opslagplaatsen moet u een eigenaar van de organisatie zijn om de machtigingen te verlenen.

1. Voeg in de sectie _Details van de build_ Blazor-details van de configuratie toe.

    - Selecteer **Blazor** in de vervolgkeuzelijst _Build-presets_ en behoud alle standaardwaarden.

1. Selecteer **Controleren + maken**.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="Knop Beoordelen en maken":::

1. Selecteer **Maken**.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="De knop Maken":::

1. Selecteer **Ga naar resource**.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="De knop Naar de resource gaan":::

## <a name="view-the-website"></a>De website weergeven

Het implementeren van een statische app heeft twee aspecten. Het eerste aspect is het inrichten van de onderliggende Azure-resources waaruit de app bestaat. Het tweede aspect is een werkstroom voor GitHub-acties waarmee de toepassing wordt gebouwd en gepubliceerd.

Voordat u naar de nieuwe statische site kunt navigeren, moet de uitvoering van de implementatiebuild eerst zijn voltooid.

In het overzichtsvenster van Static Web Apps wordt een reeks koppelingen weergegeven die u helpen te werken met uw web-app.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Overzichtsvenster":::

1. Als u op de banner klikt waarin staat _Klik hier om de status van de uitvoering van uw GitHub-acties te controleren_ wordt u naar de GitHub-acties geleid die worden uitgevoerd in uw opslagplaats. Zodra u hebt gecontroleerd of de implementatietaak is voltooid, kunt u naar de website navigeren via de gegenereerde URL.

2. Zodra de werkstroom voor GitHub Actions is voltooid, kunt u op de _URL_-koppeling klikken om de website te openen in een nieuw tabblad.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, kunt u het Azure Static Web Apps-exemplaar verwijderen door de volgende stappen te volgen:

1. Open de [Azure-portal](https://portal.azure.com)
1. Zoek in de bovenste zoekbalk naar **mijn-eerste-statische-blazor-app**
1. Selecteer de naam van de app
1. Selecteer de knop **Verwijderen**
1. Selecteer **Ja** om de verwijdering te bevestigen

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verificatie en autorisatie](./authentication-authorization.md)
