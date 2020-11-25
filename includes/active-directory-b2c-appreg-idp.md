---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 890b19cd8d6f4bc836f6f541a830c1a5feb8458f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028498"
---
Als u een toepassing wilt registreren in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *testapp1*.
1. Selecteer **accounts in een organisatorische Directory of een id-provider**.
1. Selecteer onder **Omleidings-URI** de optie **Web** en voer vervolgens `https://jwt.ms` in het URL-tekstvak in.
1. Selecteer in **Machtigingen** het selectievakje *Beheerdersgoedkeuring verlenen aan machtigingen van OpenID en offline_access*.
1. Selecteer **Registreren**.

Zodra de registratie van de toepassing is voltooid, schakelt u de impliciete toekenningsstroom in:

1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer **De nieuwe ervaring proberen** (indien weergegeven).
1. Schakel onder **Impliciete toekenning** de selectievakjes **Toegangstokens** en **Id-tokens** in.
1. Selecteer **Opslaan**.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld *testapp1*.
1. Selecteer **Ja** bij **Web-app/web-API**.
1. Voer bij **antwoord-URL**`https://jwt.ms`
1. Selecteer **Maken**.