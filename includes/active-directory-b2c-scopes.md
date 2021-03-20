---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 3ebe1ec4c0292a530e5ef2c754e9b002e931300e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994303"
---
#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/) 

1. Selecteer **App-registraties**.
1. Selecteer de toepassing *webapi1* om de bijbehorende pagina **Overzicht** te openen.
1. Selecteer onder **Beheren** **Een API beschikbaar maken**.
1. Selecteer naast **URI voor de toepassings-id** de link **Instellen**.
1. Vervang de standaardwaarde (een GUID) door `api` en selecteer vervolgens **Opslaan**. De volledige URI wordt weergegeven en moet de volgende indeling hebben: `https://your-tenant-name.onmicrosoft.com/api`. Wanneer uw webtoepassing een toegangstoken voor de API aanvraagt, moet deze de URI toevoegen als het voorvoegsel voor elk bereik dat u voor de API definieert.
1. Onder **Bereiken die door deze API worden bepaald** selecteert u **Een bereik toevoegen**.
1. Voer de volgende waarden in om een bereik te maken dat leestoegang tot de API definieert, en selecteer vervolgens **Bereik toevoegen**:
    1. **Naam van bereik**: `demo.read`
    1. **Weergavenaam van beheerderstoestemming**: `Read access to demo API`
    1. **Beschrijving van beheerderstoestemming**: `Allows read access to the demo API`
1. Selecteer **Een bereik toevoegen**, voer de volgende waarden in om een bereik toe te voegen waarmee schrijftoegang tot de API wordt gedefinieerd en selecteer vervolgens **Bereik toevoegen**:
    1. **Naam van bereik**: `demo.write`
    1. **Weergavenaam van beheerderstoestemming**: `Write access to demo API`
    1. **Beschrijving van beheerderstoestemming**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Selecteer **Toepassingen (verouderd)** .
1. Selecteer de toepassing *webapi1* om de bijbehorende pagina **Eigenschappen** te openen.
1. Selecteer **Gepubliceerde bereiken**. Gepubliceerde bereiken kunnen worden gebruikt om een clienttoepassing bepaalde machtigingen te verlenen voor de web-API.
1. Voer voor **BEREIK** `demo.read` in, en voor **BESCHRIJVING** `Read access to the web API`.
1. Voer voor **BEREIK** `demo.write` in, en voor **BESCHRIJVING** `Write access to the web API`.
1. Selecteer **Opslaan**.