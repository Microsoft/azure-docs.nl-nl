---
title: 'Quickstart: Een app configureren om een web-API te openen | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart configureert u een app-registratie die een web-API aangeeft in het Microsoft Identity Platform om toegang tot resources middels bereiken (machtigingen) in te schakelen voor clienttoepassingen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 0b064e8491b5d4fa988e9f476143cc0711e3934a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651371"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Quickstart: Een clienttoepassing configureren voor toegang tot een web-API

In deze quickstart geeft u een clienttoepassing op die is geregistreerd bij het Microsoft Identity Platform met toegangsrechten op basis van bereiken en machtigingen voor uw eigen web-API. U geeft de clienttoepassing ook toegang tot Microsoft Graph.

Door de bereiken van een web-API op te geven in de registratie van uw clienttoepassing, kan de clienttoepassing via het Microsoft Identity Platform een toegangstoken verkrijgen die deze bereiken bevat. Binnen de code kan de web-API vervolgens toegang tot de eigen resources verlenen op basis van machtigingen en op basis van de bereiken in het toegangstoken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement - [gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Voltooiing van [quickstart: Een toepassing registreren](quickstart-register-app.md)
* Voltooiing van [quickstart: Een toepassing configureren om een web-API beschikbaar te maken](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Machtigingen toevoegen voor toegang tot uw web-API

In het eerste scenario verleent u een clienttoepassing toegang tot uw eigen web-API, die u allebei als onderdeel van de vereisten moet hebben geregistreerd. Als u nog geen clienttoepassing en web-API hebt geregistreerd, voltooit u de stappen in de twee artikelen over [Vereisten](#prerequisites).

Dit diagram laat de relatie tussen de twee app-registraties zijn. In deze sectie voegt u machtigingen toe aan de registratie van de clienttoepassing.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Lijndiagram van een web-API met weergegeven bereiken aan de rechterkant en een clienttoepassing aan de linkerkant met de bereiken die zijn geselecteerd als machtigingen" border="false":::

Wanneer u zowel de clienttoepassing als de Web-API hebt geregistreerd en u de API hebt weergegeven door bereiken te maken, kunt u de machtigingen van de client voor de API configureren door de volgende stappen uit te voeren:

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren die de registratie van uw clienttoepassing bevat.
1. Selecteer **Azure Active Directory** > **App-registraties** en selecteer uw clienttoepassing (dus *niet* uw web-API).
1. Selecteer **API-machtigingen** > **Een machtiging toevoegen** > **Mijn API's**.
1. Selecteer de web-API die u hebt geregistreerd als onderdeel van de vereisten.

    **Gedelegeerde machtigingen** is standaard geselecteerd. Gedelegeerde machtigingen zijn geschikt voor clienttoepassingen die toegang hebben tot een web-API als de aangemelde gebruiker en waarvan de toegang moet worden beperkt tot de machtigingen die u in de volgende stap selecteert. Laat **Gedelegeerde machtigingen** geselecteerd voor dit voorbeeld.

    **App-machtigingen** zijn bedoeld voor service- of daemonachtige toepassingen die zelf toegang tot een web-API nodig hebben, zonder tussenkomst van een gebruiker voor aanmelding of toestemming. Tenzij u toepassingsrollen voor uw web-API hebt gedefinieerd, is deze optie uitgeschakeld.
1. Vouw onder **Machtigingen selecteren** de resource uit waarvan u de bereiken hebt gedefinieerd voor uw web-API en selecteer de machtigingen die de clienttoepassing moet hebben namens de aangemelde gebruiker.

    Als u gebruik hebt gemaakt van de naam van het voorbeeldbereik zoals opgegeven in de vorige quickstart, ziet u **Employees.Read.All** en **Employees.Write.All**.
    Selecteer **Employees.Read.All** of een andere machtiging die u hebt gemaakt bij het voltooien van de vereisten.
1. Selecteer **Machtigingen toevoegen** om het proces te voltooien.

Nadat u machtigingen voor uw API hebt toegevoegd, ziet u de geselecteerde machtigingen onder **Geconfigureerde machtigingen**. In de volgende afbeelding ziet u dat het voorbeeld *Employees.Read.All* als gedelegeerde machtiging is toegevoegd aan de registratie van de clienttoepassing.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Deelvenster Geconfigureerde machtigingen in de Azure-portal met de zojuist toegevoegde machtiging":::

Mogelijk ziet u ook de machtiging *User.Read* voor de Microsoft Graph-API. Deze machtiging wordt automatisch toegevoegd wanneer u een app registreert in de Azure-portal.

## <a name="add-permissions-to-access-microsoft-graph"></a>Machtigingen toevoegen voor toegang tot Microsoft Graph

Naast toegang tot uw eigen web-API namens de aangemelde gebruiker, moet uw toepassing mogelijk ook de gegevens van de gebruiker (of andere gegevens) kunnen openen of wijzigen die zijn opgeslagen in Microsoft Graph. Het is ook mogelijk dat u een service- of daemon-app hebt die zelf toegang nodig heeft tot Microsoft Graph om bewerkingen uit te voeren zonder tussenkomst van de gebruiker.

### <a name="delegated-permission-to-microsoft-graph"></a>Gedelegeerde machtiging voor Microsoft Graph

Configureer gedelegeerde machtigingen voor Microsoft Graph om ervoor te zorgen dat uw clienttoepassing bewerkingen namens de aangemelde gebruiker kan uitvoeren, bijvoorbeeld door hun e-mail te lezen of hun profiel te wijzigen. Standaard worden gebruikers van uw clienttoepassing gevraagd wanneer ze zich aanmelden om toestemming te geven voor de gedelegeerde machtigingen die u hiervoor hebt geconfigureerd.

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren die de registratie van uw clienttoepassing bevat.
1. Selecteer **Azure Active Directory** > **App-registraties** en selecteer uw clienttoepassing.
1. Selecteer **API-machtigingen** > **Een machtiging toevoegen** > **Microsoft Graph**
1. Selecteer **Gedelegeerde machtigingen**. In Microsoft Graph worden veel machtigingen weergegeven, met de meestgebruikte bovenaan de lijst.
1. Selecteer onder **Machtigingen** de volgende machtigingen:

    | Machtiging       | Beschrijving                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | E-mailadres van gebruikers weergeven                           |
    | `offline_access` | Toegang onderhouden tot gegevens waartoe u toegang hebt verleend |
    | `openid`         | Gebruikers aanmelden                                       |
    | `profile`        | Basisprofiel van gebruikers weergeven                           |
1. Selecteer **Machtigingen toevoegen** om het proces te voltooien.

Wanneer u machtigingen configureert, worden gebruikers van uw app bij het aanmelden gevraagd om toestemming te geven dat uw app namens hen toegang kan krijgen tot de resource-API.

Als beheerder kunt u ook toestemming verlenen namens *alle* gebruikers, zodat deze niet meer worden gevraagd. Toestemming van de beheerder wordt verderop besproken in de sectie [Meer over API-machtigingen en beheerderstoestemming](#more-on-api-permissions-and-admin-consent) van dit artikel.

### <a name="application-permission-to-microsoft-graph"></a>App-machtiging voor Microsoft Graph

Configureer app-machtigingen voor een app die als zichzelf moet worden geverifieerd zonder tussenkomst van de gebruiker of toestemming. App-machtigingen worden meestal gebruikt door achtergrondservices of daemon-apps die toegang hebben tot een API op een 'headless' manier en door web-API's die toegang hebben tot een andere (downstream) API.

In de volgende stappen verleent u machtigingen voor de *Files.Read.All*-machtiging van Microsoft Graph als voorbeeld.

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren die de registratie van uw clienttoepassing bevat.
1. Selecteer **Azure Active Directory** > **App-registraties** en selecteer uw clienttoepassing.
1. Selecteer **API-machtigingen** > **Een machtiging toevoegen** > **Microsoft Graph** > **App-machtigingen**.
1. Alle machtigingen die door Microsoft Graph beschikbaar worden gesteld, worden weergegeven onder **Machtigingen selecteren**.
1. Selecteer de machtiging of machtigingen die u uw app wilt verlenen. U kunt bijvoorbeeld een daemon-app hebben waarmee bestanden in uw organisatie worden gescand en die u waarschuwt voor een specifiek bestandstype of specifieke bestandsnaam.

    Onder **Machtigingen selecteren** vouwt u **Bestanden** uit en selecteert u de machtiging *Files.Read.All*.
1. Selecteer **Machtigingen toevoegen**.

Voor sommige machtigingen, zoals *Files.Read.All* van Microsoft Graph, is toestemming van de beheerder nodig. U verleent beheerders toestemming door de knop **Toestemming beheerder verlenen** te selecteren, die later wordt beschreven in de sectie [Knop Toestemming beheerder](#admin-consent-button).

### <a name="configure-client-credentials"></a>Aanmeldingsgegevens voor de client configureren

Apps die gebruikmaken van app-machtigingen verifiëren zichzelf met hun eigen aanmeldingsgegevens, zonder tussenkomst van de gebruiker. Voordat uw toepassing (of API) toegang kan krijgen tot Microsoft Graph, uw eigen web-API of een andere API met behulp van toepassings machtigingen, moet u de referenties van die client app configureren.

Zie de sectie [Aanmeldingsgegevens toevoegen](quickstart-register-app.md#add-credentials) in de [quickstart voor meer informatie over het configureren van de aanmeldingsgegevens van een app: Een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>Meer over API-machtigingen en beheerderstoestemming

Het deelvenster **API-machtigingen** van een app-registratie bevat een tabel [Geconfigureerde machtigingen](#configured-permissions) en bevat mogelijk bovendien een tabel [Andere machtigingen verleend](#other-permissions-granted). In de volgende secties worden beide tabellen en de knop [Beheerderstoestemming](#admin-consent-button) beschreven.

### <a name="configured-permissions"></a>Geconfigureerde machtigingen

In de tabel **Geconfigureerde machtigingen** in het deelvenster **API-machtigingen** ziet u de lijst met machtigingen die uw app nodig heeft voor de standaardwerking, te weten de lijst *voor toegang tot vereiste resources*. Gebruikers of hun beheerders, moeten toestemming geven voor deze machtigingen voordat uw app kan worden gebruikt. Andere, optionele machtigingen kunnen later tijdens runtime worden aangevraagd (met behulp van dynamische toestemming).

Dit is de minimale lijst met machtigingen waarmee gebruikers moeten instemmen voor gebruik van uw app. Er kunnen er meer zijn, maar deze machtigingen zijn altijd vereist. Ter beveiliging en om gebruikers en beheerders een prettiger gevoel te geven bij gebruik van uw app, moet u nooit vragen om iets wat u niet nodig hebt.

De machtigingen die in deze tabel worden weergegeven, kunt u toevoegen of verwijderen door gebruik te maken van de stappen die hierboven worden beschreven of via [Andere verleende machtigingen](#other-permissions-granted) (beschreven in de volgende sectie). Als beheerder kunt u toestemming geven voor de volledige set van machtigingen van een API die in de tabel worden weergegeven en kunt u de toestemming voor afzonderlijke machtigingen intrekken.

### <a name="other-permissions-granted"></a>Overige verleende machtigingen

Mogelijk ziet u ook een tabel met de naam **Andere machtigingen die zijn verleend voor {uw tenant}** in het deelvenster **API-machtigingen**. In de tabel **Andere machtigingen die zijn verleend voor {uw tenant}** ziet u machtigingen die zijn verleend voor de tenant die niet expliciet zijn geconfigureerd voor het toepassingsobject. Deze machtigingen zijn dynamisch aangevraagd en verleend. Deze sectie wordt alleen weergegeven als minstens één van de machtigingen van toepassing is.

U kunt de volledige set API-machtigingen of afzonderlijke machtigingen die u in deze tabel ziet, toevoegen aan de tabel **Geconfigureerde machtigingen**. Als beheerder kunt u beheerderstoestemming intrekken voor API's of afzonderlijke machtigingen in deze sectie.

### <a name="admin-consent-button"></a>Knop Beheerderstoestemming

Met de knop **Toestemming beheerder verlenen voor {uw tenant}** kan een beheerder beheerderstoestemming verlenen voor de machtigingen die zijn geconfigureerd voor de toepassing. Wanneer u de knop selecteert, wordt er een dialoogvenster weergegeven waarin u wordt gevraagd om de actie voor de toestemming te bevestigen.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Knop Toestemming beheerder verlenen gemarkeerd in het deelvenster Geconfigureerde machtigingen van de Azure-portal":::

Nadat toestemming is verleend, worden de machtigingen waarvoor beheerderstoestemming nodig was, weergegeven als machtigingen waarvoor toestemming is verleend:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Tabel Machtigingen configureren in de Azure-portal met beheerderstoestemming verleend voor de machtiging Files.Read.All":::

De knop **Toestemming beheerder verlenen** is *uitgeschakeld* als u geen beheerder bent, of als er geen machtigingen zijn geconfigureerd voor de toepassing. Als u machtigingen hebt die zijn verleend maar nog niet zijn geconfigureerd, wordt u via de knop Beheerderstoestemming gevraagd om deze machtigingen af te handelen. U kunt deze toevoegen aan Geconfigureerde machtigingen, of u kunt ze verwijderen.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende quickstart in de reeks voor informatie over het configureren van de accounttypen die toegang moeten hebben tot de toepassing. U wilt de toegang bijvoorbeeld mogelijk beperken tot alleen de gebruikers in uw organisatie (één tenant), of ook toegang verlenen aan gebruikers in andere Azure AD-tenants (meerdere tenants) en aan gebruikers met persoonlijke Microsoft-accounts (MSA).

> [!div class="nextstepaction"]
> [De accounts wijzigen die worden ondersteund door een toepassing](quickstart-modify-supported-accounts.md)
