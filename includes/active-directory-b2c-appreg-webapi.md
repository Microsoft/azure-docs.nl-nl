---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 86ca23b23d24174d22deafba68ce170bb7717926
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025861"
---
Web-API-resources moeten worden geregistreerd in uw tenant voordat deze in staat zijn om beveiligde resourceaanvragen door clienttoepassingen die een toegangstoken aanbieden, kunnen accepteren en erop kunnen reageren.

Als u een toepassing wilt registreren in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *webapi1*.
1. Selecteer onder **Omleidings-id** de optie **Web**, en voer vervolgens een eindpunt in waarop met Azure AD B2C tokens worden geretourneerd die zijn aangevraagd via de toepassing. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `http://localhost:5000`.
1. Selecteer **Registreren**.
1. Noteer de **Toepassings-id (client)** voor gebruik in een latere stap.

Schakel vervolgens de impliciete toekenningsstroom in:

1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer **De nieuwe ervaring proberen** (indien weergegeven).
1. Schakel onder **Impliciete toekenning** de selectievakjes **Toegangstokens** en **Id-tokens** in.
1. Selecteer **Opslaan**.

#### <a name="applications"></a>[Toepassingen](#tab/applications/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **Toepassingen (verouderd)** , en selecteer vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
1. Selecteer **Ja** bij **Web-app/web-API**.
1. Selecteer **ja** voor **Impliciete stroom toestaan**.
1. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `https://localhost:5000`.
1. Voeg voor **App-ID-URI** een id voor een API-eindpunt toe aan de weergegeven URI. Voer voor deze zelfstudie `api` in, zodat de volledige URI lijkt op `https://contosob2c.onmicrosoft.com/api`.
1. Selecteer **Maken**.
1. Noteer de **TOEPASSINGS-ID** voor gebruik in een latere stap.