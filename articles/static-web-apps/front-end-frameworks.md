---
title: Front-end-Frameworks configureren met de preview-versie van statische Web Apps van Azure
description: Instellingen voor populaire front-end-frameworks die nodig zijn voor statische Azure-Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 9f02c9ad10e2b03bddc7c3ca2cfb54932464b69a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731748"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Front-end-frameworks en-bibliotheken configureren met de preview-versie van Azure static Web Apps

De statische Web Apps van Azure vereist dat u de juiste configuratie waarden hebt in het [Build-configuratie bestand](github-actions-workflow.md) voor uw front-end-Framework of-bibliotheek.

## <a name="configuration"></a>Configuratie

De volgende tabel bevat de instellingen voor een reeks frameworks en bibliotheken<sup>1</sup>.

De bedoeling van de tabel kolommen wordt uitgelegd door de volgende items:

- **Uitvoer locatie**: hier wordt de waarde weer gegeven voor `output_location` . Dit is de [map voor de samengestelde versies van toepassings bestanden](github-actions-workflow.md#build-and-deploy).

- **Aangepaste build-opdracht**: wanneer voor het Framework een andere opdracht dan of is vereist `npm run build` `npm run azure:build` , kunt u een [aangepaste build-opdracht](github-actions-workflow.md#custom-build-commands)definiëren.

| Framework | Locatie van app-artefact | Opdracht voor aangepaste build |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n.v.t. <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Hoek, universeel](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | n.v.t. |
| [Backbone.js](https://backbonejs.org/) | `/` | n.v.t. |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | n.v.t. |
| [Ember](https://emberjs.com/) | `dist` | n.v.t. |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | n.v.t. |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | n.v.t. |
| [Hyperapp](https://hyperapp.dev/) | `/` | n.v.t. |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | n.v.t. |
| [jQuery](https://jquery.com/) | `/` | n.v.t. |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | n.v.t. |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | n.v.t. |
| [Marko](https://markojs.com/) | `public` | n.v.t. |
| [Meteoor](https://www.meteor.com/) | `bundle` | n.v.t. |
| [Mithril](https://mithril.js.org/) | `dist` | n.v.t. |
| [Omvat](https://www.polymer-project.org/) | `build/default` | n.v.t. |
| [Preact](https://preactjs.com/) | `build` | n.v.t. |
| [React](https://reactjs.org/) | `build` | n.v.t. |
| [RedwoodJS](https://redwoodjs.com/) | `web/dist` | `yarn rw build` |
| [Muis](https://stenciljs.com/) | `www` | n.v.t. |
| [Svelte](https://svelte.dev/) | `public` | n.v.t. |
| [Three.js](https://threejs.org/) | `/` | n.v.t. |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | n.v.t. |
| [Vue.js](https://vuejs.org/) | `dist` | n.v.t. |

<sup>1</sup> de bovenstaande tabel is niet bedoeld als een uitputtende lijst van frameworks en bibliotheken die samen werken met Azure static web apps.

<sup>2</sup> niet van toepassing

## <a name="next-steps"></a>Volgende stappen

- [Bouw- en werkstroomconfiguratie](github-actions-workflow.md)
