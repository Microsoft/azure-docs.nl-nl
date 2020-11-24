---
title: Verificatie en autorisatie
description: Meer informatie over de verschillende manieren waarop een app of service kan worden geverifieerd bij Azure spatiale ankers en de niveaus van beheer die u nodig hebt om toegang te krijgen tot ruimtelijke ankers.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 0166a3b6031f9e1d364a37db99be5bc5a65267df
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95484607"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Verificatie en autorisatie voor Azure spatiale ankers

In dit artikel vindt u informatie over de verschillende manieren waarop u kunt verifiëren voor Azure spatiale ankers vanuit uw app of webservice. U leert ook hoe u met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) in Azure Active Directory (Azure AD) kunt gebruiken voor het beheren van de toegang tot uw ruimtelijke-ankers accounts.

## <a name="overview"></a>Overzicht

![Diagram met een overzicht van verificatie voor Azure spatiale ankers.](./media/spatial-anchors-authentication-overview.png)

Om toegang te krijgen tot een bepaald Azure spatiale-anker account moeten clients eerst een toegangs token verkrijgen van de Azure Mixed Reality-beveiligings token service (STS). Tokens die zijn verkregen van STS, hebben een levens duur van 24 uur. Ze bevatten informatie die ruimtelijke ankers worden gebruikt om autorisatie beslissingen te nemen voor het account en ervoor te zorgen dat alleen geautoriseerde principals toegang hebben tot het account.

Toegangs tokens kunnen worden verkregen in Exchange voor de account sleutels of tokens die zijn uitgegeven door Azure AD.

Met account sleutels kunt u snel aan de slag met de Azure spatiale ankers-service. Maar voordat u uw toepassing implementeert voor productie, raden we u aan uw app bij te werken voor het gebruik van Azure AD-verificatie.

U kunt Azure AD-verificatie tokens op twee manieren verkrijgen:

- Als u een bedrijfs toepassing bouwt en uw bedrijf Azure AD als identiteits systeem gebruikt, kunt u Azure AD-verificatie op basis van gebruikers gebruiken in uw app. U kunt vervolgens toegang verlenen tot uw ruimtelijk-anker accounts met behulp van uw bestaande Azure AD-beveiligings groepen. U kunt ook rechtstreeks toegang verlenen aan gebruikers in uw organisatie.
- Anders wordt u aangeraden Azure AD-tokens te verkrijgen van een webservice die ondersteuning biedt voor uw app. U wordt aangeraden deze methode voor productie toepassingen te gebruiken, omdat u hiermee voor komt dat u de referenties voor toegang tot Azure spatiale ankers in uw client toepassing kunt insluiten.

## <a name="account-keys"></a>Account sleutels

De eenvoudigste manier om aan de slag te gaan is door account sleutels te gebruiken voor toegang tot uw Azure spatiale-ankers account. U kunt uw account sleutels ophalen op het Azure Portal. Ga naar uw account en selecteer het tabblad **sleutels** :

![Scherm opname van het tabblad sleutels met de knop kopiëren voor de primaire sleutel gemarkeerd.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Er zijn twee sleutels beschikbaar. Beide zijn gelijktijdig geldig voor toegang tot het ruimtelijke-ankers account. We raden u aan de sleutel die u gebruikt voor toegang tot het account regel matig bij te werken. Als u twee afzonderlijke geldige sleutels hebt, worden deze updates zonder uitval tijd ingeschakeld. U hoeft alleen de primaire sleutel en de secundaire sleutel als alternatief bij te werken.

De SDK heeft ingebouwde ondersteuning voor verificatie via account sleutels. U hoeft alleen de `AccountKey` eigenschap in te stellen voor uw `cloudSession` object:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Nadat u die eigenschap hebt ingesteld, verwerkt de SDK de uitwisseling van de account sleutel voor een toegangs token en de nood zakelijke cache van tokens voor uw app.

> [!WARNING]
> U wordt aangeraden account sleutels te gebruiken voor snel voorbereiden, maar alleen tijdens ontwikkeling/prototypen. Het is niet raadzaam om uw toepassing te leveren voor productie met een Inge sloten account sleutel. Gebruik in plaats daarvan de op gebruikers gebaseerde of service gebaseerde Azure AD-verificatie methoden die worden beschreven in de volgende.

## <a name="azure-ad-user-authentication"></a>Gebruikers verificatie van Azure AD

Voor toepassingen die als doel Azure Active Directory gebruikers worden gebruikt, wordt u aangeraden een Azure AD-token voor de gebruiker te gebruiken. U kunt dit token verkrijgen met behulp van de [MSAL](../../active-directory/develop/msal-overview.md). Volg de stappen in de [Quick start voor het registreren van een app](../../active-directory/develop/quickstart-register-app.md), waaronder:

**In de Azure-Portal**
1.    Registreer uw toepassing in azure AD als een systeem eigen toepassing. Als onderdeel van de registratie moet u bepalen of uw toepassing multi tenant moet zijn. U moet ook de omleidings-Url's opgeven die voor uw toepassing zijn toegestaan.
1.  Ga naar het tabblad **API-machtigingen** .
2.  Selecteer **een machtiging toevoegen**.
    1.  Selecteer **resource provider Mixed Reality** op de **api's die mijn organisatie gebruikt** .
    2.  Selecteer **Gedelegeerde machtigingen**.
    3.  Selecteer **mixedreality. signin** onder **mixedreality**.
    4.  Selecteer **Machtigingen toevoegen**.
3.  Selecteer **toestemming geven** voor de beheerder.

2. Verleen uw toepassing of gebruikers toegang tot uw resource:
   1.    Ga in het Azure Portal naar de resource met ruimtelijke ankers.
   2.    Ga naar het tabblad **toegangs beheer (IAM)** .
   3.    Selecteer **Roltoewijzing toevoegen**.
   1.    [Selecteer een rol](#azure-role-based-access-control).
   2.    Voer in het vak **selecteren** de namen in van de gebruikers, groepen en/of toepassingen waaraan u toegang wilt toewijzen.
   3.    Selecteer **Opslaan**.

**In uw code**
1.    Zorg ervoor dat u de toepassings-ID en omleidings-URI van uw eigen Azure AD-toepassing gebruikt voor de para meters **client-id** en **RedirectUri** in MSAL.
2.    Stel de Tenant gegevens in:
        1.    Als uw toepassing **alleen mijn organisatie** ondersteunt, vervangt u deze waarde door uw **Tenant-id** of **Tenant naam**. Bijvoorbeeld contoso.microsoft.com.
        2.    Als uw toepassing **accounts in een organisatorische Directory** ondersteunt, vervangt u deze waarde door **organisaties**.
        3.    Als uw toepassing **alle Microsoft-account gebruikers** ondersteunt, vervangt u deze waarde door **common**.
3.    Stel op uw token aanvraag het **bereik** in op **' `https://sts.<account-domain>//.default` "**, waarbij `<account-domain>` wordt vervangen door het **account domein** voor uw Azure spatiale-ankers account. Een voor beeld van een bereik voor een Azure spatiale ankers-account in het account domein US-Oost **2. `https://sts.mixedreality.azure.com//.default`** Met dit bereik wordt aan Azure AD aangegeven dat uw toepassing een token aanvraagt voor de STS (Mixed Reality Security Token Service).

Nadat u deze stappen hebt voltooid, moet uw toepassing MSAL een Azure AD-token kunnen verkrijgen. U kunt deze Azure AD-token instellen als de `authenticationToken` op uw Cloud sessie configuratie object:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Verificatie van Azure AD-service

Als u apps wilt implementeren die gebruikmaken van ruimtelijke Azure-ankers naar productie, wordt u aangeraden een back-end-service te gebruiken die Broker verificatie aanvragen. Hier volgt een overzicht van het proces:

![Diagram met een overzicht van verificatie voor Azure spatiale ankers.](./media/spatial-anchors-aad-authentication.png)

Hier wordt ervan uitgegaan dat uw app een eigen mechanisme gebruikt om te verifiëren bij de back-end-service. (Bijvoorbeeld een Microsoft-account, PlayFab, Facebook, een Google ID of een aangepaste gebruikers naam en wacht woord.)  Nadat uw gebruikers zijn geverifieerd voor uw back-end-service, kan die service een Azure AD-Token ophalen, dit uitwisselen voor een toegangs token voor Azure spatiale ankers en terugsturen naar uw client toepassing.

Het Azure AD-toegangs token wordt opgehaald via de [MSAL](../../active-directory/develop/msal-overview.md). Volg de stappen in de [Snelstartgids een app registreren](../../active-directory/develop/quickstart-register-app.md), zoals:

**In de Azure-Portal**
1.    Registreer uw toepassing in azure AD:
        1.    Selecteer in de Azure Portal **Azure Active Directory** en selecteer vervolgens **app-registraties**.
        2.    Selecteer **Nieuwe registratie**.
        3.    Voer de naam van uw toepassing in, selecteer **Web-app/API** als het toepassings type en voer de verificatie-URL voor uw service in. Selecteer **Maken**.
2.    Selecteer in de toepassing **instellingen** en selecteer vervolgens het tabblad **certificaten en geheimen** . Maak een nieuw client geheim, selecteer een duur en selecteer vervolgens **toevoegen**. Zorg ervoor dat u de geheime waarde opslaat. U moet deze op te nemen in de code van de webservice.
3.    Verleen uw toepassing en/of gebruikers toegang tot uw resource:
        1.    Ga in het Azure Portal naar de resource met ruimtelijke ankers.
        2.    Ga naar het tabblad **toegangs beheer (IAM)** .
        3.    Selecteer **Roltoewijzing toevoegen**.
        4.    [Selecteer een rol](#azure-role-based-access-control).
        5.    Voer in het vak **selecteren** de naam of namen in van de toepassingen waaraan u toegang wilt toewijzen. Als u wilt dat de gebruikers van uw app verschillende rollen hebben voor het ruimtelijke-ankers account, registreert u meerdere toepassingen in azure AD en wijst u hieraan een afzonderlijke rol toe. Implementeer vervolgens uw autorisatie logica om de juiste rol voor uw gebruikers te gebruiken.

              > [!NOTE]
              > Selecteer in het deel venster **toewijzing van rol toevoegen** in **toegang toewijzen aan** de optie **Azure AD-gebruiker,-groep of Service-Principal**.

        6.    Selecteer **Opslaan**.

**In uw code**

>[!NOTE]
> U kunt het service voorbeeld gebruiken dat beschikbaar is op GitHub.

1.    Zorg ervoor dat u de toepassings-ID, het toepassings geheim en de omleidings-URI van uw eigen Azure AD-toepassing gebruikt als de **client-id**, het **geheim** en de **RedirectUri** -para meters in MSAL.
2.    Stel de Tenant-ID in op uw eigen Azure AD-Tenant-ID in de para meter **Authority** in MSAL.
3.    Stel op uw token aanvraag het **bereik** in op **' `https://sts.<account-domain>//.default` "**, waarbij `<account-domain>` wordt vervangen door het **account domein** voor uw Azure spatiale-ankers account. Een voor beeld van een bereik voor een Azure spatiale ankers-account in het account domein US-Oost **2. `https://sts.mixedreality.azure.com//.default`**

Nadat u deze stappen hebt voltooid, kan uw back-end-service een Azure AD-Token ophalen. Vervolgens kan het worden uitgewisseld voor een MR-token dat wordt teruggestuurd naar de client. Het gebruik van een Azure AD-token om een MR-token op te halen, wordt uitgevoerd via een REST-aanroep. Hier volgt een voor beeld van een oproep:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

De autorisatie-header heeft de volgende notatie: `Bearer <Azure_AD_token>`

Het antwoord bevat het MR-token in tekst zonder opmaak.

Dat MR-token vervolgens wordt geretourneerd naar de client. Uw client-app kan deze vervolgens instellen als toegangs token in de Cloud sessie configuratie:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[NDK VOOR C++](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Om u te helpen het niveau van toegang te beheren dat wordt verleend aan toepassingen, services of Azure AD-gebruikers van uw service, kunt u deze vooraf bestaande rollen toewijzen aan uw Azure Spatial-ankers-accounts:

- **Eigenaar van ruimtelijk ankers**. Toepassingen of gebruikers met deze rol kunnen ruimtelijke ankers maken, er query's op uitvoeren en ze verwijderen. Wanneer u bij uw account verifieert met behulp van account sleutels, wordt de rol van ruimtelijke ankers account eigenaar toegewezen aan de geverifieerde principal.
- De **Inzender van ruimtelijke ankers**. Toepassingen of gebruikers met deze rol kunnen ruimtelijke ankers en query's voor hen maken, maar ze kunnen ze niet verwijderen.
- **Lezer van ruimtelijk ankers-account**. Toepassingen of gebruikers met deze rol kunnen alleen een query uitvoeren op ruimtelijke ankers. Ze kunnen geen nieuwe maken, bestaande verwijderen of meta gegevens bijwerken. Deze rol wordt doorgaans gebruikt voor toepassingen waarbij sommige gebruikers de omgeving weghalen, maar andere kunnen alleen ankers intrekken die eerder in de omgeving zijn geplaatst.

## <a name="next-steps"></a>Volgende stappen

Uw eerste app maken met behulp van Azure spatiale ankers:

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
