---
title: 'Zelfstudie: Met de server weergegeven Nuxt.js-websites implementeren in Azure Static Web Apps'
description: Genereer en implementeer dynamische Nuxt.js-sites met Azure Static Web Apps.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563463"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>Met de server weergegeven Nuxt.js-websites implementeren in Azure Static Web Apps (preview)

In deze zelfstudie leert u hoe u een met [Nuxt.js](https://nuxtjs.org) gegenereerde statische website implementeert in [Azure Static Web Apps](overview.md). Allereerst leer u hoe u een Nuxt.js-app instelt, configureert en implementeert. Tijdens dit proces leert u ook hoe u omgaat met veelvoorkomende uitdagingen die kunnen optreden bij het genereren van statische pagina’s met Nuxt.js

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)
- Een GitHub-account. [Gratis een account maken](https://github.com/join)
- [Node.js](https://nodejs.org) geïnstalleerd.

## <a name="set-up-a-nuxtjs-app"></a>Een Nuxt.js-app instellen

U kunt een nieuw Nuxt.js-project instellen met behulp van `create-nuxt-app`. In plaats van een nieuw project begint u in deze zelfstudie met het klonen van een bestaande opslagplaats. Deze opslagplaats wordt ingesteld om te laten zien hoe u een dynamische Nuxt.js-app kunt implementeren als een statische site.

1. Maak een nieuwe opslagplaats onder uw GitHub-account met behulp van een opslagplaatssjabloon.
1. Ga naar [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. Geef de opslagplaats de naam **nuxtjs-starter**
1. Kloon de nieuwe opslagplaats vervolgens naar de computer. Zorg ervoor dat u < UW_GITHUB_ACCOUNTNAAM> vervangt door de naam van uw account.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Ga naar de zojuist gekloonde Nuxt.js-app:

   ```bash
   cd nuxtjs-starter
   ```

1. Afhankelijkheden installeren:

    ```bash
    npm install
    ```

1. Start de Nuxt.js-app in de ontwikkelomgeving:

    ```bash
    npm run dev
    ```

Ga naar `http://localhost:3000` om de app te openen. Hier ziet u de volgende website geopend in uw voorkeursbrowser:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Nuxt.js-app starten":::

Wanneer u op een framework/bibliotheek klikt, ziet u een pagina met details over het geselecteerde item:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Pagina Details":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Een statische website genereren vanuit een Nuxt.js-build

Wanneer u een Nuxt.js-site bouwt met behulp van `npm run build`, wordt de app gebouwd als een traditionele web-app, niet als een statische site. Als u een statische site wilt genereren, gebruikt u de volgende toepassingsconfiguratie.

1. Werk het buildscript van _package.json_ bij om alleen een statische site te genereren met behulp van de opdracht `nuxt generate`:

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    Nu deze opdracht is uitgevoerd wordt in Static Web Apps het script `build` uitgevoerd telkens wanneer u een doorvoering pusht.

1. Een statische site genereren:

    ```bash
    npm run build
    ```

    Met Nuxt.js wordt de statische site gegenereerd en gekopieerd naar een map _dist_ in de hoofdmap van uw werkmap.

    > [!NOTE]
    > Deze map wordt vermeld in het _.gitignore_-bestand, omdat het moet worden gegenereerd met CI/CD wanneer u implementeert.

## <a name="push-your-static-website-to-github"></a>Uw statische website naar GitHub pushen

Met Azure Static Web Apps wordt de app geïmplementeerd vanuit een GitHub-opslagplaats. Dit gebeurt bij elke gepushte doorvoering naar een toegewezen vertakking. Gebruik de volgende opdrachten om uw wijzigingen te synchroniseren in GitHub.

1. Alle gewijzigde bestanden faseren:

    ```bash
    git add .
    ```

1. Alle wijzigingen doorvoeren

    ```bash
    git commit -m "Update build config"
    ```

1. Push uw wijzigingen naar GitHub.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Uw statische website implementeren

De volgende stappen laten zien hoe u de app die u zojuist naar GitHub hebt gepusht, koppelt aan Azure Static Web Apps. Eenmaal in Azure kunt u de toepassing implementeren in een productieomgeving.

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Een Azure Static Web Apps Preview-resource maken

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Klik op **Een resource maken**
1. Ga naar **Static Web Apps**
1. Klik op **Static Web Apps (preview)**
1. Klik op **Maken**.

1. Selecteer een abonnement in de vervolgkeuzelijst *Abonnement*, of gebruik de standaardwaarde.
1. Klik op de koppeling **Nieuw** in de vervolgkeuzelijst *Resourcegroep*. Voer bij *Naam van nieuwe resourcegroep* in: **mystaticsite**. Klik vervolgens op **OK**
1. Geef een globaal unieke naam op voor de app in het tekstvak **Naam**. Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `-`. Deze waarde wordt gebruikt als het URL-voorvoegsel voor de statische app in de indeling `https://<APP_NAME>.azurestaticapps.net`.
1. Kies in de vervolgkeuzelijst *Regio* een regio die het dichtst bij u ligt.
1. Selecteer **Gratis** in de vervolgkeuzelijst SKU.

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="Statische web-app maken":::

### <a name="add-a-github-repository"></a>GitHub-opslagplaats toevoegen

Het nieuwe Static Web Apps-account heeft toegang nodig tot de opslagplaats met de Nuxt.js-app, zodat doorvoeringen automatisch kunnen worden geïmplementeerd.

1. Klik op de knop **Aanmelden bij GitHub**
1. Selecteer de **Organisatie** waaronder u de opslagplaats voor het Next.js-project hebt gemaakt, wat uw gebruikersnaam van GitHub kan zijn.
1. Zoek en selecteer de naam van de opslagplaats die u eerder hebt gemaakt.
1. Kies **hoofd** als de vertakking in de vervolgkeuzelijst *Vertakking*.

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="Verbinding maken met GitHub":::

### <a name="configure-the-build-process"></a>Het buildproces configureren

Azure Static Web Apps is ontworpen om automatisch algemene taken uit te voeren, zoals het installeren van NPM-modules en het uitvoeren van `npm run build` tijdens elke implementatie. Er zijn echter enkele instellingen, zoals de bronmap van de toepassing en de doelmap voor de build, die u handmatig moet configureren.

1. Klik op het tabblad **Build** om de map voor statische uitvoer te configureren.

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="Tabblad Build":::

1. Typ **dist** in het tekstvak *Locatie van app-artefact*.

### <a name="review-and-create"></a>Controleren en maken

1. Klik op de knop **Beoordelen en maken** om te controleren of de details kloppen.
1. Klik op **Maken** om te beginnen met het maken van de resource, en richt ook een GitHub-actie in voor de implementatie.
1. Zodra de implementatie is voltooid, klikt u op **Ga naar resource**
1. Klik in het _Overzichtsvenster_ op de *URL*-koppeling om de geïmplementeerde toepassing te openen. 

Als de website onmiddellijk wordt geladen, is de werkstroom voor GitHub-acties nog steeds actief op de achtergrond. Zodra de werkstroom is voltooid, klikt u op Browser vernieuwen om de web-app weer te geven.

U kunt de status van de actiewerkstromen controleren door naar de acties voor uw opslagplaats te gaan:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Wijzigingen synchroniseren

Toen u de app maakte, is in Azure Static Web Apps een werkstroombestand voor GitHub-acties gemaakt in uw opslagplaats. U moet dit bestand overbrengen naar de lokale opslagplaats zodat de Git-geschiedenis kan worden gesynchroniseerd.

Ga terug naar de terminal en voer de volgende opdracht `git pull origin main` uit.

## <a name="configure-dynamic-routes"></a>Dynamische routes configureren

Ga naar de zojuist geïmplementeerde site en klik op een van de framework- of bibliotheeklogo's. In plaats van een pagina met details ziet u een 404-foutpagina.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="404 bij dynamische routes":::

De reden hiervoor is: toen met Nuxt.js de statische site werd gegenereerd, is dit alleen gebeurd voor de startpagina. Met Nuxt.js kunnen equivalente statische `.html`-bestanden worden gegenereerd voor elk `.vue`-paginabestand, met één uitzondering. 

Als de pagina een dynamische pagina is, bijvoorbeeld `_id.vue`, beschikt deze over onvoldoende informatie om een statische HTML te maken van een dergelijke dynamische pagina. U moet dan snel expliciet de mogelijke paden voor de dynamische routes opgeven.

## <a name="generate-static-pages-from-dynamic-routes"></a>Statische pagina's genereren uit dynamische routes

1. Werk het bestand _nuxt.config.js_ bij zodat Nuxt.js gebruikmaakt van een lijst met alle beschikbare gegevens om statische pagina’s te genereren voor elk framework/elke bibliotheek:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` is een asynchrone functie. U kunt in deze functie dus een aanvraag doen bij een API en de geretourneerde lijst gebruiken om de paden te genereren.

2. Push de nieuwe wijzigingen naar de GitHub-opslagplaats. Na enkele minuten is uw site opnieuw gebouwd met GitHub-acties. Nadat het bouwen is voltooid, verdwijnt de 404-fout.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="404 bij dynamische routes, opgelost":::

> [!div class="nextstepaction"]
> [Een aangepast domein instellen](custom-domain.md)
