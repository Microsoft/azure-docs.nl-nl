---
title: 'Zelfstudie: Een Gatsby-site publiceren in Azure Static Web Apps'
description: In deze zelfstudie ziet u hoe u een Gatsby-toepassing implementeert in Azure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4430ed34858077b13b4fec69756c1c7e9f3ef7ac
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652353"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Zelfstudie: Een Gatsby-site publiceren in Azure Static Web Apps Preview

In dit artikel ziet u hoe u een [Gatsby](https://gatsbyjs.org)-webtoepassing maakt en implementeert in [Azure Static Web Apps](overview.md). Het uiteindelijke resultaat is een nieuwe Static Web Apps-site (met de bijbehorende GitHub-acties), waarmee u beheert hoe de app wordt gebouwd en gepubliceerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Gatsby-app maken
> - Een Azure Static Web Apps-site instellen
> - De Gatsby-app implementeren in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Als u nog geen account hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/).
- Een GitHub-account. Als u nog geen account hebt, kunt u [gratis een account maken](https://github.com/join).
- [Node.js](https://nodejs.org) geïnstalleerd.

## <a name="create-a-gatsby-app"></a>Een Gatsby-app maken

Een Gatsby-app maken met behulp van de Gatsby-CLI (opdrachtregelinterface):

1. Open een terminal
1. Gebruik het hulpprogramma [npx](https://www.npmjs.com/package/npx) om een nieuwe app te maken met de Gatsby-CLI. Dit kan enkele minuten duren.

   ```bash
   npx gatsby new static-web-app
   ```

1. Ga naar de nieuwe app

   ```bash
   cd static-web-app
   ```

1. Initialiseer een Git-opslagplaats

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Push de toepassing naar GitHub

U moet een opslagplaats in GitHub hebben om een nieuwe Azure Static Web Apps-resource te maken.

1. Maak in [https://github.com/new](https://github.com/new) een lege GitHub-opslagplaats (maak geen Leesmij-bestand) met de naam **statische-gatsby-web-app**.

1. Voeg vervolgens de GitHub-opslagplaats die u zojuist hebt gemaakt, als externe locatie toe aan de lokale opslagplaats. Zorg ervoor dat u de tijdelijke aanduiding `<YOUR_USER_NAME>` in de volgende opdracht vervangt door uw GitHub-gebruikersnaam.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. Push de lokale opslagplaats naar GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>De web-app implementeren

De volgende stappen laten zien hoe u een nieuwe statische site-app maakt en deze implementeert in een productieomgeving.

### <a name="create-the-application"></a>De toepassing maken

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Klik op **Een resource maken**
1. Zoek **Static Web Apps**
1. Klik op **Static Web Apps (preview)**
1. Klik op **Maken**.

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Een Static Web Apps-resource (preview) maken in de portal":::

1. Accepteer bij _Abonnement_ het abonnement dat wordt vermeld, of selecteer een nieuw abonnement in de vervolgkeuzelijst.

1. Selecteer in _Resourcegroep_ de optie **Nieuw**. Voer bij _Naam van nieuwe resourcegroep_ in: **gatsby-static-web-app**. Selecteer vervolgens **OK**.

1. Voer vervolgens een naam in voor uw app in het vak **Naam**. Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `-`.

1. Selecteer voor _Regio_ een beschikbare regio dicht bij u in de buurt.

1. Selecteer voor _SKU_ de optie **Gratis**.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Ingevulde gegevens":::

1. Klik op de knop **Aanmelden bij GitHub**.

1. Selecteer de **Organisatie** waaronder u de opslagplaats hebt gemaakt.

1. Selecteer de **gatsby-static-web-app** als de _Opslagplaats_ .

1. Selecteer **hoofd** voor de _vertakking_ .

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Voltooide GitHub-gegevens":::

### <a name="build"></a>Ontwikkelen

Voeg vervolgens configuratie-instellingen toe die tijdens het bouwproces worden gebruikt om de app te bouwen.

1. Klik op de knop **Volgende: Build >** om de configuratie van de build te bewerken

1. Als u de instellingen van de stap in GitHub-acties wilt configureren, stelt u _App-locatie_ in op **/** .

1. Stel _Locatie van app-artefact_ in op **openbaar**.

   Een waarde voor _API-locatie_ is niet nodig omdat u momenteel geen API implementeert.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Build-instellingen":::

### <a name="review-and-create"></a>Controleren en maken

1. Klik op de knop **Beoordelen en maken** om te controleren of de details kloppen.

1. Klik op **Maken** om te beginnen met het maken van de statische Azure-web-app, en richt een GitHub-actie in voor de implementatie.

1. Zodra de implementatie is voltooid, klikt u op **Naar de resource gaan**.

1. Klik in het resourcescherm op de _URL_-koppeling om de geïmplementeerde toepassing te openen. Mogelijk moet u enkele minuten wachten voor de GitHub-actie is voltooid.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Geïmplementeerde toepassing":::

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](custom-domain.md)
