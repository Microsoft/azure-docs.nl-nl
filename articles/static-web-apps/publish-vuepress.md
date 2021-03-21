---
title: 'Zelfstudie: Een VuePress-site publiceren in Azure Static Web Apps'
description: In deze zelfstudie ziet u hoe u een VuePress-toepassing implementeert in Azure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 6f0616df885a7f8fcd76337c810bc368aa02f3c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650440"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Zelfstudie: Een VuePress-site publiceren in Azure Static Web Apps Preview

In dit artikel ziet u hoe u een [VuePress](https://vuepress.vuejs.org/)-webtoepassing maakt en implementeert in [Azure Static Web Apps](overview.md). Het uiteindelijke resultaat is een nieuwe Azure Static Web Apps-toepassing met de bijbehorende GitHub-acties, waarmee u beheert hoe de app wordt gebouwd en gepubliceerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een VuePress-app maken
> - Een Azure Static Web Apps-toepassing instellen
> - De VuePress-app implementeren in Azure

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Als u nog geen account hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/).
- Een GitHub-account. Als u nog geen account hebt, kunt u [gratis een account maken](https://github.com/join).
- [Node.js](https://nodejs.org) geïnstalleerd.

## <a name="create-a-vuepress-app"></a>Een VuePress-app maken

Een VuePress-app maken via de CLI (opdrachtregelinterface):

1. Maak een nieuwe map voor de VuePress-app.

   ```bash
   mkdir static-site
   ```

1. Voeg een _LEESMIJ.md_-bestand toe aan de map.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Initialiseer het _package.json_-bestand.

   ```bash
   npm init -y
   ```

1. Voeg VuePress toe als een `devDependency`.

   ```bash
   npm install --save-dev vuepress
   ```

1. Open het _package.json_-bestand in een teksteditor en voeg een build-opdracht toe aan de sectie [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html).

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Maak een _.gitignore_-bestand om de map _knooppunt\_modules_ uit te sluiten.

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Initialiseer een Git-opslagplaats.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Push de toepassing naar GitHub

U hebt een opslagplaats in GitHub nodig om verbinding te maken met Azure Static Web Apps. In de volgende stappen ziet u hoe u een opslagplaats maakt voor uw site.

1. Maak in [https://github.com/new](https://github.com/new) een lege GitHub-opslagplaats (maak geen Leesmij-bestand) met de naam **statische-vuepress-app**.

1. Voeg de GitHub-opslagplaats als externe locatie toe aan de lokale opslagplaats. Zorg ervoor dat u de tijdelijke aanduiding `<YOUR_USER_NAME>` in de volgende opdracht vervangt door uw GitHub-gebruikersnaam.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Push de lokale opslagplaats naar GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>De web-app implementeren

De volgende stappen laten zien hoe u een nieuwe Static Web Apps-toepassing maakt en deze implementeert in een productieomgeving.

### <a name="create-the-application"></a>De toepassing maken

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Klik op **Een resource maken**
1. Ga naar **Static Web Apps**
1. Klik op **Static Web Apps (preview)**
1. Klik op **Maken**.

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Een Static Web Apps-resource (preview) maken in de portal":::

1. Accepteer bij **Abonnement** het abonnement dat wordt vermeld, of selecteer een nieuw abonnement in de vervolgkeuzelijst.

1. Selecteer in _Resourcegroep_ de optie **Nieuw**. Voer bij _Naam van nieuwe resourcegroep_ in: **vuepress-static-app**. Selecteer vervolgens **OK**.

1. Voer vervolgens een naam in voor uw app in het vak **Naam**. Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `-`.

1. Selecteer voor _Regio_ een beschikbare regio dicht bij u in de buurt.

1. Selecteer voor _SKU_ de optie **Gratis**.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Ingevulde gegevens":::

1. Klik op de knop **Aanmelden bij GitHub**.

1. Selecteer de **Organisatie** waaronder u de opslagplaats hebt gemaakt.

1. Selecteer de **statische-vuepress-app** als de _Opslagplaats_.

1. Selecteer **hoofd** voor de _vertakking_ .

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Voltooide GitHub-gegevens":::

### <a name="build"></a>Ontwikkelen

Vervolgens voegt u configuratie-instellingen toe die tijdens het bouwproces worden gebruikt om de app te bouwen. Met de volgende instellingen wordt het werkstroombestand voor GitHub-acties geconfigureerd.

1. Klik op de knop **Volgende: Build >** om de configuratie van de build te bewerken

1. Stel _App-locatie_ in op **/** .

1. Stel _Locatie van app-artefact_ in op **.vuepress/dist**.

Een waarde voor _API-locatie_ is niet nodig omdat u momenteel geen API implementeert.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Build-instellingen":::

### <a name="review-and-create"></a>Controleren en maken

1. Klik op de knop **Beoordelen en maken** om te controleren of de details kloppen.

1. Klik op **Maken** om te beginnen met het maken van de Azure Static Web Apps-resource, en richt een GitHub-actie in voor de implementatie.

1. Zodra de implementatie is voltooid, klikt u op **Naar de resource gaan**.

1. Klik in het resourcescherm op de _URL_-koppeling om de geïmplementeerde toepassing te openen. Mogelijk moet u enkele minuten wachten voor de GitHub-actie is voltooid.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Geïmplementeerde toepassing":::

### <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](custom-domain.md)
