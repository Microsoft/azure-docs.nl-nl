---
title: Een web-API-toepassing toevoegen-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het toevoegen van een web-API-toepassing aan uw Active Directory B2C-Tenant.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0eeff0c8c338b09fbe375587db2b955a143138c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94949816"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Een web-API-toepassing toevoegen aan uw Azure Active Directory B2C-tenant

 Registreer Web-API-resources in uw Tenant, zodat ze aanvragen kunnen accepteren en hierop reageren door client toepassingen die een toegangs token aanbieden. In dit artikel wordt beschreven hoe u een web-API registreert in Azure Active Directory B2C (Azure AD B2C).

Als u een toepassing wilt registreren in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *webapi1*.
1. Selecteer onder **Omleidings-id** de optie **Web**, en voer vervolgens een eindpunt in waarop met Azure AD B2C tokens worden geretourneerd die zijn aangevraagd via de toepassing. In een productie toepassing kunt u de omleidings-URI instellen als een eind punt, bijvoorbeeld `https://localhost:5000` . Tijdens het ontwikkelen of testen kunt u het instellen op `https://jwt.ms` , een webtoepassing van micro soft die de gedecodeerde inhoud van een token weergeeft (de inhoud van het token verlaat uw browser nooit). U kunt op elk gewenst moment omleidings-URI's toevoegen en wijzigen in uw geregistreerde toepassingen.
1. Selecteer **Registreren**.
1. Noteer de **id van de toepassing (client)** voor gebruik in de code van uw web-API.

Als u een toepassing hebt die de impliciete toekennings stroom implementeert, bijvoorbeeld een [toepassing met één pagina op basis van Java script](tutorial-register-spa.md), kunt u de stroom inschakelen door de volgende stappen uit te voeren:

1. Selecteer **Verificatie** onder **Beheren**.
1. Schakel onder **Impliciete toekenning** de selectievakjes **Toegangstokens** en **Id-tokens** in.
1. Selecteer **Opslaan**.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Toepassingen (verouderd)** , en selecteer vervolgens **Toevoegen**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
6. Selecteer voor **Inclusief webtoepassing/ web-API** en **Impliciete stroom toestaan** **Ja**.
7. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In uw productie toepassing kunt u de antwoord-URL instellen op een waarde zoals `https://localhost:44332` . Voor test doeleinden, stelt u de antwoord-URL in op `https://jwt.ms` .
8. Voer voor **App ID URI** de id in die wordt gebruikt voor uw web-API. De volledige id-URI, inclusief het domein, wordt voor u gegenereerd. Bijvoorbeeld `https://contosotenant.onmicrosoft.com/api`.
9. Klik op **Create**.
10. Noteer op de eigenschappenpagina de toepassings-ID die u gebruikt wanneer u de webtoepassing configureert.

* * *

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. In deze zelfstudie gebruikt u bereiken om lees- en schrijfmachtigingen voor de web-API te definiëren.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een app, moet u uw toepassing machtigingen verlenen tot de API. In de [zelf studie: een toepassing registreren in azure Active Directory B2C](tutorial-register-applications.md), een webtoepassing met de naam *webapp1* is geregistreerd in azure AD B2C. U kunt deze toepassing gebruiken om de Web-API aan te roepen.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uw toepassing is geregistreerd voor het aanroepen van de beveiligde web-API. Een gebruiker voert een verificatie uit bij Azure AD B2C om de toepassing te kunnen gebruiken. De toepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.