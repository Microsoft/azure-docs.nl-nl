---
title: Wat is Azure Static Web Apps?
description: De belangrijkste functies en functionaliteit van Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: f4e900c8bcdcb5febec39421e098b19ad10b8b00
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558887"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Wat is Azure Static Web Apps Preview?

Azure Static Web Apps is een service waarmee automatisch volledige stack-web-apps worden gebouwd en geïmplementeerd in Azure vanuit een GitHub-opslagplaats.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Overzicht van Static Web Apps":::

De werkstroom van Azure Static Web Apps wordt aangepast aan de dagelijkse werkstroom van een ontwikkelaar. Apps worden gebouwd en geïmplementeerd op basis van GitHub-interacties.

Wanneer u een Azure Static Web Apps-resource maakt, wordt in Azure een werkstroom voor GitHub-acties ingesteld in de opslagplaats voor broncode van de app. Met deze werkstroom wordt een vertakking van uw keuze bewaakt. Elke keer dat u doorvoeringen pusht of pull-aanvragen accepteert in de bewaakte vertakking, worden met de GitHub-actie automatisch uw app en de bijbehorende API gebouwd en geïmplementeerd in Azure.

Statische web-apps zijn doorgaans gebouwd met behulp van bibliotheken en frameworks, zoals hoek, reageren, svelte, vue of razendsnelle, waarbij server side rendering niet is vereist. Deze apps zijn onder andere HTML-, CSS-, JavaScript- en afbeeldingsactiva waaruit de toepassing bestaat. Met een traditionele webserver worden deze activa geleverd vanaf één server naast eventuele vereiste API-eindpunten.

Met Static Web Apps worden statische activa gescheiden van een traditionele webserver, en worden in plaats hiervan geleverd vanaf punten die geografisch wereldwijd zijn gedistribueerd. Deze distributie maakt het uitvoeren van bestanden veel sneller dan bestanden die zich fysiek dichter bij eindgebruikers bevinden. Daarnaast worden API-eindpunten gehost met behulp van een [serverloze architectuur](../azure-functions/functions-overview.md), waardoor er helemaal geen volledige back-end-server meer nodig is.

## <a name="key-features"></a>Belangrijke functies

- **Webhosting** voor statische inhoud zoals HTML, CSS, JavaScript en afbeeldingen.
- Ondersteuning voor **Geïntegreerde API** geboden in Azure Functions.
- **Eersteklas GitHub-integratie** waarbij wijzigingen in de opslagplaats builds en implementaties activeren.
- **Wereldwijd gedistribueerde** statische inhoud, waardoor inhoud zich dichter bij de gebruikers bevindt.
- **Gratis SSL-certificaten** die automatisch worden verlengd.
- **Aangepaste domeinen** om uw app merkaanpassingen te bieden.
- **Naadloos beveiligingsmodel** met een omgekeerde proxy bij het aanroepen van API's, waarvoor geen CORS-configuratie is vereist.
- **Integratie van verificatieproviders** met Azure Active Directory, Facebook, Google, GitHub en Twitter.
- **Aanpasbare autorisatieroldefinitie** en toewijzingen.
- **Back-end-routeringsregels** die volledig beheer mogelijk maken van de inhoud en routes die u levert.
- **Gegenereerde faseringsversies** mogelijk gemaakt met pull-aanvragen die preview-versies van uw site mogelijk maken vóór het publiceren.

## <a name="what-you-can-do-with-static-web-apps"></a>Wat u kunt doen met Static Web Apps

- **Moderne webtoepassingen bouwen** met JavaScript-frameworks en bibliotheken, zoals [Angular](getting-started.md?tabs=angular), [React](getting-started.md?tabs=react), [Svelte](/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=react) of [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) gebruiken om WebAssembly-toepassingen te maken, met een back-end van [Azure Functions](apis.md).
- **Statische sites publiceren** met frameworks zoals [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Webtoepassingen implementeren** met frameworks zoals [Nuxt.js](deploy-nextjs.md) en [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eerste statische app bouwen](getting-started.md)