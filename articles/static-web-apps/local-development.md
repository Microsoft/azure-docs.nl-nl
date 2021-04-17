---
title: Lokale ontwikkeling instellen voor Azure Static Web Apps
description: Meer informatie over het instellen van uw lokale ontwikkelomgeving voor Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: e19d39a32d48ec55473bb957595d47ec5148e74b
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588782"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Lokale ontwikkeling instellen voor Azure Static Web Apps Preview

Wanneer een site in de cloud wordt gepubliceerd, Azure Static Web Apps veel services die samenwerken alsof ze dezelfde toepassing zijn. Deze services omvatten:

- De statische web-app
- Azure Functions API
- Verificatie- en autorisatieservices
- Routerings- en configuratieservices

Deze services moeten met elkaar communiceren en Azure Static Web Apps deze integratie voor u in de cloud verwerkt.

Deze services worden echter niet automatisch aan elkaar gekoppeld als ze lokaal worden uitgevoerd.

Om een vergelijkbare ervaring te bieden als wat u in Azure krijgt, biedt [Azure Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli) de volgende services:

- Een lokale statische siteserver
- Een proxy naar de front-end framework-ontwikkelingsserver
- Een proxy naar uw API-eindpunten - beschikbaar via Azure Functions Core Tools
- Een mock-verificatie- en autorisatieserver
- Afdwinging van lokale routes en configuratie-instellingen

## <a name="how-it-works"></a>Uitleg

In de volgende grafiek ziet u hoe aanvragen lokaal worden verwerkt.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azure Static Web App CLI-aanvraag- en -antwoordstroom":::

> [!IMPORTANT]
> `http://localhost:4280`Navigeer naar om toegang te krijgen tot de toepassing die wordt bediend door de CLI.

- **Aanvragen** die naar de poort `4280` worden gedaan, worden doorgestuurd naar de juiste server, afhankelijk van het type aanvraag.

- **Aanvragen voor** statische inhoud, zoals HTML of CSS, worden verwerkt door de interne CLI-inhoudsserver of door de front-end frameworkserver voor het debuggen.

- **Verificatie- en** autorisatieaanvragen worden verwerkt door een emulator, die een profiel voor een valse identiteit aan uw app biedt.

- **Functions Core Tools-runtime** verwerkt aanvragen naar de API van de site.

- **Antwoorden van** alle services worden naar de browser geretourneerd alsof ze allemaal één toepassing zijn.

## <a name="prerequisites"></a>Vereisten

- **Bestaande Azure Static Web Apps site:** als u er nog geen hebt, begint u met de [starter-app vanilla-api.](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate)
- **[Node.js](https://nodejs.org) met npm:** voer de [Node.js LTS-versie](https://nodejs.org) uit, die toegang tot [npm bevat.](https://www.npmjs.com/)
- **[Visual Studio Code: wordt](https://code.visualstudio.com/)** gebruikt voor het debuggen van de API-toepassing, maar niet vereist voor de CLI.

## <a name="get-started"></a>Aan de slag

Open een terminal naar de hoofdmap van uw bestaande Azure Static Web Apps site.

1. Installeer de CLI.

    `npm install -g @azure/static-web-apps-cli`

1. Bouw uw app indien nodig door uw toepassing.

    Voer `npm run build` uit of de equivalente opdracht voor uw project.

1. Wijzig in de uitvoermap voor uw app. Uitvoermappen worden vaak _build of_ iets soortgelijks genoemd.

1. Start de CLI.

    `swa start`

1. http://localhost:4280Navigeer naar om de app in de browser te bekijken.

### <a name="other-ways-to-start-the-cli"></a>Andere manieren om de CLI te starten

| Description | Opdracht |
|--- | --- |
| Een specifieke map gebruiken | `swa start ./output-folder` |
| Een lopende framework-ontwikkelingsserver gebruiken | `swa start http://localhost:3000` |
| Een Functions-app starten in een map | `swa start ./output-folder --api ./api` |
| Een actief Functions-app gebruiken | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Autorisatie en verificatie-emulatie

Met Static Web Apps CLI wordt de [beveiligingsstroom geëmuleerd die](./authentication-authorization.md) in Azure is geïmplementeerd. Wanneer een gebruiker zich aanmeldt, kunt u een profiel voor een valse identiteit definiëren dat wordt geretourneerd naar de app.

Wanneer u bijvoorbeeld naar navigeert, wordt er een pagina geretourneerd waarmee `/.auth/login/github` u een identiteitsprofiel kunt definiëren.

> [!NOTE]
> De emulator werkt met elke beveiligingsprovider, niet alleen met GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Lokale verificatie en autorisatie-emulator":::

De emulator biedt een pagina waarmee u de volgende [clientprincipaalwaarden kunt](./user-information.md#client-principal-data) verstrekken:

| Waarde | Beschrijving |
| --- | --- |
| **Gebruikersnaam** | De accountnaam die is gekoppeld aan de beveiligingsprovider. Deze waarde wordt weergegeven als de `userDetails` eigenschap in de clientprincipal en wordt automatisch gegenereerd als u geen waarde op geeft. |
| **Gebruikers-id** | Waarde automatisch gegenereerd door de CLI.  |
| **Rollen** | Een lijst met rolnamen, waarbij elke naam zich op een nieuwe regel.  |

Nadat u bent aangemeld:

- U kunt het eindpunt of een functie-eindpunt gebruiken om `/.auth/me` de [client-principal](./user-information.md)van de gebruiker op te halen.

- Als u navigeert `./auth/logout` naar , wordt de client-principal geweken en wordt de mock-gebruiker afmeldt.

## <a name="debugging"></a>Foutopsporing

Er zijn twee contexten voor het debuggen van gegevens in een statische web-app. De eerste is voor de site met statische inhoud en de tweede voor API-functies. Lokalebuggen is mogelijk doordat de cli Static Web Apps ontwikkelservers kan gebruiken voor een van deze contexten of beide contexten.

In de volgende stappen ziet u een algemeen scenario waarin gebruik wordt gemaakt van ontwikkelservers voor beide contexten voor het debuggen van gegevens.

1. Start de statische siteontwikkelingsserver. Deze opdracht is specifiek voor het front-end-framework dat u gebruikt, maar wordt vaak geleverd in de vorm van opdrachten `npm run build` zoals `npm start` , of `npm run dev` .

1. Open de map API-toepassing in Visual Studio Code en start een debuggingssessie.

1. Geef de adressen voor de statische server en API-server door aan de `swa start` opdracht door ze in volgorde weer te geven.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

De volgende schermafbeeldingen tonen de terminals voor een typisch scenario voor het debuggen:

De site met statische inhoud wordt uitgevoerd via `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Statische siteontwikkelingsserver":::

Met Azure Functions API-toepassing wordt een foutopsporingssessie uitgevoerd in Visual Studio Code.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio code-API-debuggen":::

De Static Web Apps CLI wordt gestart met behulp van beide ontwikkelservers.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure Static Web Apps CLI-terminal":::

Aanvragen die via de poort gaan, worden nu doorgeleid naar de ontwikkelingsserver voor statische inhoud of de `4280` API-debuggingssessie.

Zie de CLI-opslagplaats voor meer informatie over verschillende scenario's voor Azure Static Web Apps voor het aanpassen van poorten [en serveradressen.](https://github.com/Azure/static-web-apps-cli)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw toepassing configureren](configuration.md)
