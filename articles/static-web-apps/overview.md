---
title: Wat is Azure Static Web Apps?
description: De belangrijkste functies en functionaliteit van Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: 945979daa3c766b737e5b312a6c14e60204a6a55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874059"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Wat is Azure Static Web Apps Preview?

Azure Static Web Apps is een service die automatisch volledige stack web-apps bouwt en implementeert in Azure vanuit een codeopslagplaats.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Azure Static Web Apps overzichtsdiagram":::

De werkstroom van Azure Static Web Apps wordt aangepast aan de dagelijkse werkstroom van een ontwikkelaar. Apps worden gebouwd en geïmplementeerd op basis van codewijzigingen.

Wanneer u een resource Azure Static Web Apps, communiceert Azure rechtstreeks met GitHub of Azure DevOps om een vertakking van uw keuze te bewaken. Telkens wanneer u commits pusht of pull-aanvragen accepteert in de gevolgde vertakking, wordt er automatisch een build uitgevoerd en worden uw app en API geïmplementeerd in Azure.

Statische web-apps worden vaak gebouwd met behulp van bibliotheken en frameworks zoals Angular, React, Svelte, Vue of Blazor, waarbij server side rendering niet vereist is. Deze apps zijn onder andere HTML-, CSS-, JavaScript- en afbeeldingsactiva waaruit de toepassing bestaat. Met een traditionele webserver worden deze activa geleverd vanaf één server naast eventuele vereiste API-eindpunten.

Met Static Web Apps worden statische activa gescheiden van een traditionele webserver, en worden in plaats hiervan geleverd vanaf punten die geografisch wereldwijd zijn gedistribueerd. Deze distributie maakt het uitvoeren van bestanden veel sneller dan bestanden die zich fysiek dichter bij eindgebruikers bevinden. Daarnaast worden API-eindpunten gehost met behulp van een [serverloze architectuur](../azure-functions/functions-overview.md), waardoor er helemaal geen volledige back-end-server meer nodig is.

## <a name="key-features"></a>Belangrijke functies

- **Webhosting** voor statische inhoud zoals HTML, CSS, JavaScript en afbeeldingen.
- Ondersteuning voor **Geïntegreerde API** geboden in Azure Functions.
- **Eersteklas GitHub- en Azure DevOps-integratie** waarbij wijzigingen in de opslagplaats builds en implementaties activeren.
- **Wereldwijd gedistribueerde** statische inhoud, waardoor inhoud zich dichter bij de gebruikers bevindt.
- **Gratis SSL-certificaten** die automatisch worden verlengd.
- **Aangepaste domeinen** om uw app merkaanpassingen te bieden.
- **Naadloos beveiligingsmodel** met een omgekeerde proxy bij het aanroepen van API's, waarvoor geen CORS-configuratie is vereist.
- **Integratie van verificatieproviders** met Azure Active Directory, Facebook, Google, GitHub en Twitter.
- **Aanpasbare autorisatieroldefinitie** en toewijzingen.
- **Back-end-routeringsregels** die volledig beheer mogelijk maken van de inhoud en routes die u levert.
- **Gegenereerde faseringsversies** mogelijk gemaakt met pull-aanvragen die preview-versies van uw site mogelijk maken vóór het publiceren.

## <a name="what-you-can-do-with-static-web-apps"></a>Wat u kunt doen met Static Web Apps

- **Moderne webtoepassingen bouwen** met JavaScript-frameworks en bibliotheken, zoals [Angular](getting-started.md?tabs=angular), [React](getting-started.md?tabs=react), [Svelte](/learn/modules/publish-app-service-static-web-app-api/), [Vue](getting-started.md?tabs=vue) of [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) gebruiken om WebAssembly-toepassingen te maken, met een back-end van [Azure Functions](apis.md).
- **Statische sites publiceren** met frameworks zoals [Gatsby](publish-gatsby.md), [Hugo](publish-hugo.md), [VuePress](publish-vuepress.md).
- **Webtoepassingen implementeren** met frameworks zoals [Nuxt.js](deploy-nextjs.md) en [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eerste statische app bouwen](getting-started.md)
