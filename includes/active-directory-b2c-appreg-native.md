---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 2ad6b90616077a6d18550e86692b109bda622af7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027163"
---
Als u een toepassing wilt registreren in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst of een identiteitsprovider**.
1. Gebruik **Omleidings-URI**, gebruik het vervolgkeuzemenu om **Openbare client/systeemeigen (mobiel en desktop)** te selecteren.
1. Voer een omleidings-URI in met een uniek schema. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Ontwikkelen**: voor ontwikkelen kunt u de omleidings-URI instellen op `http://localhost`, zodat Azure AD B2C elke poort in de aanvraag accepteert. Als de geregistreerde URI een poort bevat, wordt alleen deze poort door Azure AD B2C gebruikt. Als de geregistreerde omleidings-URI bijvoorbeeld `http://localhost` is, kan de omleidings-URI in de aanvraag `http://localhost:<randomport>` zijn. Als de geregistreerde omleidings-URI `http://localhost:8080` is, moet de omleidings-URI in de aanvraag `http://localhost:8080` zijn.
    * **Uniek**: Het schema van de omleidings-URI moet voor elke toepassing uniek zijn. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    * **Volledig**: De omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Bijvoorbeeld: `//oauth/` werkt en `//oauth` mislukt. Neem geen speciale tekens op in de URI, bijvoorbeeld lage streepjes.
1. Selecteer in **Machtigingen** het selectievakje *Beheerdersgoedkeuring verlenen aan machtigingen van OpenID en offline_access*.
2. Selecteer **Registreren**.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **Toepassingen (verouderd)** , en selecteer vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *nativeapp1*.
1. Voor **Systeemeigen client** selecteert u **Ja**.
1. Voer een **Aangepaste omleidings-URI** met een uniek schema in. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Er zijn twee belangrijke overwegingen bij het kiezen van een omleidings-URI:
    * **Uniek**: Het schema van de omleidings-URI moet voor elke toepassing uniek zijn. In het voorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` is `com.onmicrosoft.contosob2c.exampleapp` het schema. Dit patroon moet worden gevolgd. Als twee toepassingen hetzelfde schema delen, kan de gebruiker een toepassing kiezen. Als de gebruiker onjuist kiest, mislukt de aanmelding.
    * **Volledig**: De omleidings-URI moet een schema en een pad hebben. Het pad moet ten minste één slash na het domein bevatten. Bijvoorbeeld: `//oauth/` werkt en `//oauth` mislukt. Neem geen speciale tekens op in de URI, bijvoorbeeld lage streepjes.
1. Selecteer **Maken**.