---
title: 'Zelfstudie: Een Jekyll-site publiceren in Azure Static Web Apps'
description: Bekijk meer informatie over hoe u een Jekyll-toepassing implementeert in Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 8c6764ad5b63aa2fde07326ab986404ea4312316
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99585174"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Zelfstudie: Een Jekyll-site publiceren in Azure Static Web Apps Preview

In dit artikel wordt beschreven u hoe u een [Jekyll](https://jekyllrb.com/)-webtoepassing maakt en implementeert in [Azure Static Web Apps](overview.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Jekyll-website maken
> - Een Azure Static Web Apps-toepassing instellen
> - De Jekyll-app implementeren in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat [Jekyll](https://jekyllrb.com/docs/installation/) is geïnstalleerd.
  - Indien nodig kunt u het Windows-subsysteem voor Linux gebruiken en de instructies voor Ubuntu volgen.
- Een Azure-account met een actief abonnement. Als u nog geen account hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/).
- Een GitHub-account. Als u nog geen account hebt, kunt u [gratis een account maken](https://github.com/join).

## <a name="create-jekyll-app"></a>Jekyll-app maken

Maak een Jekyll-app met behulp van de Jekyll-CLI (opdrachtregelinterface):

1. Voer vanaf de terminal de Jekyll-CLI uit om een nieuwe app te maken.

   ```bash
   jekyll new static-app
   ```

1. Ga naar de nieuwe app.

   ```bash
   cd static-app
   ```

1. Initialiseer een nieuwe Git-opslagplaats.

   ```bash
    git init
   ```

1. Voer de wijzigingen door.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Push de toepassing naar GitHub

Azure Static Web Apps gebruikt GitHub om uw website te publiceren. In de volgende stappen wordt uitgelegd hoe u een GitHub-opslagplaats maakt.

1. Maak in [https://github.com/new](https://github.com/new) een lege GitHub-opslagplaats (maak geen Leesmij-bestand) met de naam **jekyll-azure-static**.

1. Voeg de GitHub-opslagplaats als externe locatie toe aan de lokale opslagplaats. Zorg ervoor dat u de tijdelijke aanduiding `<YOUR_USER_NAME>` in de volgende opdracht vervangt door uw GitHub-gebruikersnaam.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Push de lokale opslagplaats naar GitHub.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Uw Git-vertakking heeft mogelijk een andere naam dan `main` . Vervang `main` in deze opdracht door de juiste waarde.

## <a name="deploy-your-web-app"></a>De web-app implementeren

De volgende stappen laten zien hoe u een nieuwe statische site-app maakt en deze implementeert in een productieomgeving.

### <a name="create-the-application"></a>De toepassing maken

1. Navigeer naar [Azure Portal](https://portal.azure.com).

1. Klik op **Een resource maken**.

1. Zoek **Static Web Apps**.

1. Klik op **Static Web Apps (preview)** .

1. Klik op **Create**.

1. Accepteer bij **Abonnement** het abonnement dat wordt vermeld, of selecteer een nieuw abonnement in de vervolgkeuzelijst.

1. Selecteer in _Resourcegroep_ de optie **Nieuw**. Voer bij _Naam van nieuwe resourcegroep_ in: **jekyll-static-app**. Selecteer **OK**.

1. Voer vervolgens een naam in voor uw app in het vak _Naam_. Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `-`.

1. Selecteer voor _Regio_ een beschikbare regio dicht bij u in de buurt.

1. Selecteer voor _SKU_ de optie **Gratis**.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Ingevulde gegevens":::

1. Klik op de knop **Aanmelden bij GitHub**.

1. Selecteer de **Organisatie** waaronder u de opslagplaats hebt gemaakt.

1. Selecteer de **jekyll-static-app** als de _opslagplaats_.

1. Selecteer **hoofd** voor de _vertakking_ .

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Voltooide GitHub-gegevens":::

### <a name="build"></a>Ontwikkelen

Vervolgens voegt u configuratie-instellingen toe die tijdens het bouwproces worden gebruikt om de app te bouwen. Met de volgende instellingen wordt het werkstroombestand voor GitHub-acties geconfigureerd.

1. Klik op de knop **Volgende: Build >** om de configuratie van de build te bewerken.

1. Stel _App-locatie_ in op **/_site**.

1. Laat _Locatie van app-artefact_ leeg.

   Een waarde voor _API-locatie_ is niet nodig omdat u momenteel geen API implementeert.

### <a name="review-and-create"></a>Controleren en maken

1. Klik op de knop **Beoordelen en maken** om te controleren of de details kloppen.

1. Klik op **Maken** om te beginnen met het maken van de Azure Static Web Apps-resource, en richt een GitHub-actie in voor de implementatie.

1. De implementatie mislukt eerst omdat er enkele specifieke instellingen voor Jekyll vereist zijn in het werkstroombestand. Navigeer naar de terminal en haal de doorvoer op met de GitHub-actie op de computer om deze instellingen toe te voegen.

   ```bash
   git pull
   ```

1. Open de Jekyll-app in een teksteditor en open het bestand _.github/workflows/azure-pages-<NAAM_VAN-WERKSTROOM>.yml_.

1. Voeg vóór de regel `- name: Build And Deploy` het volgende configuratie blok toe.

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Voer de bijgewerkte werkstroom door en push deze naar GitHub.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. Wacht totdat de GitHub-actie is voltooid.

1. Klik in het _overzichtsvenster_ in de Azure-portal op de _URL_-koppeling om de geïmplementeerde toepassing te openen.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Geïmplementeerde toepassing":::

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](custom-domain.md)
