---
title: Lokale ontwikkeling voor Azure static-Web Apps instellen
description: Meer informatie over het instellen van uw lokale ontwikkel omgeving voor Azure static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 8a45d490d060febc18d77c8487c9f562fd2a914a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275498"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Lokale ontwikkeling voor Azure static Web Apps-Preview instellen

Wanneer een Azure static Web Apps-site naar de Cloud wordt gepubliceerd, heeft er een groot aantal services die samen werken alsof ze dezelfde toepassing zijn. Deze services omvatten:

- De statische web-app
- Azure Functions-API
- Verificatie-en autorisatie Services
- Routerings-en configuratie Services

Deze services moeten met elkaar communiceren en Azure static Web Apps verwerkt deze integratie in de Cloud.

Als u lokaal uitvoert, worden deze services echter niet automatisch aan elkaar gekoppeld.

Om een vergelijk bare ervaring te bieden als voor wat u in azure krijgt, biedt de [statische web apps CLI van Azure](https://github.com/Azure/static-web-apps-cli) de volgende services:

- Een lokale statische site server
- Een proxy voor de front-end Framework-ontwikkelings server
- Een proxy voor uw API-eind punten-beschikbaar via Azure Functions Core Tools
- Een model verificatie-en autorisatie server
- Lokale routes en configuratie-instellingen afdwingen

## <a name="how-it-works"></a>Uitleg

In het volgende diagram ziet u hoe aanvragen lokaal worden verwerkt.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="IP-adres van de statische web-app voor Azure en de respons stroom":::

> [!IMPORTANT]
> Ga naar [http://localhost:4280](http://localhost:4280) om toegang te krijgen tot de toepassing die wordt geleverd door de cli.

- **Aanvragen** naar de poort `4280` worden doorgestuurd naar de juiste server, afhankelijk van het type aanvraag.

- Aanvragen voor **statische inhoud** , zoals HTML of CSS, worden verwerkt door de interne cli-server voor statische inhoud of door de front-end Framework-server voor fout opsporing.

- **Verificatie-en autorisatie** aanvragen worden verwerkt door een emulator, waarmee een echt identiteits profiel aan uw app wordt verstrekt.

- **Functions core tools runtime** verwerkt aanvragen voor de API van de site.

- **Antwoorden** van alle services worden teruggestuurd naar de browser alsof het allemaal één toepassing was.

## <a name="prerequisites"></a>Vereisten

- **Bestaande Azure Static web apps-site**: als u er geen hebt, kunt u beginnen met de [vanille-API starter-](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) app.
- **[Node.js](https://nodejs.org) met NPM**: Voer de [Node.js LTS](https://nodejs.org) -versie uit, waaronder toegang tot [NPM](https://www.npmjs.com/).
- **[Visual Studio code](https://code.visualstudio.com/)**: wordt gebruikt voor het opsporen van fouten in de API-toepassing, maar niet vereist voor de cli.

## <a name="get-started"></a>Aan de slag

Open een terminal naar de hoofdmap van uw bestaande Azure static Web Apps-site.

1. Installeer de CLI.

    `npm install -g @azure/static-web-apps-cli`

1. Bouw uw app als dat nodig is voor uw toepassing.

    Voer uit `npm run build` of de overeenkomende opdracht voor uw project.

1. Ga naar de uitvoermap voor uw app. Uitvoer mappen hebben vaak de naam _Build_ of iets dergelijks.

1. Start de CLI.

    `swa start`

1. Navigeer naar [http://localhost:4280](http://localhost:4280) om de app in de browser weer te geven.

### <a name="other-ways-to-start-the-cli"></a>Andere manieren om de CLI te starten

| Beschrijving | Opdracht |
|--- | --- |
| Een specifieke map gebruiken | `swa start ./output-folder` |
| Een actieve Framework-ontwikkelings server gebruiken | `swa start http://localhost:3000` |
| Een functions-app in een map starten | `swa start ./output-folder --api ./api` |
| Een actieve functions-app gebruiken | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Verificatie-en verificatie-emulatie

De statische Web Apps CLI emuleert de [beveiligings stroom](./authentication-authorization.md) die in Azure is geïmplementeerd. Wanneer een gebruiker zich aanmeldt, kunt u een valse identiteits profiel definiëren dat naar de app wordt geretourneerd.

Wanneer u bijvoorbeeld probeert te navigeren naar `/.auth/login/github` , wordt er een pagina geretourneerd waarmee u een identiteits profiel kunt definiëren.

> [!NOTE]
> De emulator werkt met elke beveiligings provider, niet alleen GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Lokale verificatie en autorisatie-emulator":::

De emulator biedt een pagina waarmee u de volgende principal-waarden van de [client](./user-information.md#client-principal-data) kunt opgeven:

| Waarde | Beschrijving |
| --- | --- |
| **Gebruikersnaam** | De account naam die aan de beveiligings provider is gekoppeld. Deze waarde wordt weer gegeven als de `userDetails` eigenschap in de client-Principal en wordt automatisch gegenereerd als u geen waarde opgeeft. |
| **Gebruikers-id** | De waarde die automatisch wordt gegenereerd door de CLI.  |
| **Rollen** | Een lijst met namen van rollen, waarbij elke naam op een nieuwe regel staat.  |

Zodra u bent aangemeld:

- U kunt het `/.auth/me` eind punt of een functie-eind punt gebruiken om de [client-Principal](./user-information.md)van de gebruiker op te halen.

- Als u navigeert `./auth/logout` , wordt de client-Principal gewist en wordt de gebruiker afgemeld.

## <a name="debugging"></a>Foutopsporing

Er zijn twee fout opsporingsgegevens in een statische web-app. De eerste is voor de site van de statische inhoud en de tweede is voor API-functies. Lokale fout opsporing is mogelijk door de statische Web Apps CLI toe te staan ontwikkel servers te gebruiken voor een of beide van deze contexten.

In de volgende stappen ziet u een algemeen scenario waarin ontwikkel servers worden gebruikt voor zowel fout opsporing als contexten.

1. Start de server voor de ontwikkeling van statische sites. Deze opdracht is specifiek voor het front-end-Framework dat u gebruikt, maar wordt vaak gebruikt in de vorm van opdrachten als `npm run build` , `npm start` of `npm run dev` .

1. Open de map API-toepassing in Visual Studio code en start een foutopsporingssessie.

1. Geef de adressen voor de statische server en API-server door aan de `swa start` opdracht door ze in de aangegeven volg orde weer te geven.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

De volgende scherm afbeeldingen tonen de terminals voor een typisch fout opsporingsprogramma:

De statische inhouds site wordt uitgevoerd via `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Ontwikkel server voor statische site":::

De Azure Functions API-toepassing voert een foutopsporingssessie uit in Visual Studio code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio code API-fout opsporing":::

De statische Web Apps CLI wordt gestart met behulp van beide ontwikkel servers.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure static Web Apps CLI-Terminal":::

Aanvragen die via poort gaan `4280` worden doorgestuurd naar de statische content development server of de API-fout opsporings sessie.

Zie de [Azure Static web apps cli-opslag plaats](https://github.com/Azure/static-web-apps-cli)voor meer informatie over verschillende scenario's voor fout opsporing, met richt lijnen voor het aanpassen van poorten en server adressen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw toepassing configureren](configuration.md)
