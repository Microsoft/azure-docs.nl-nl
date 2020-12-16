---
title: 'Quickstart: Een app registreren in het Microsoft Identity Platform | Azure'
description: In deze quickstart leert u hoe u een toepassing kunt registreren met het Microsoft Identity Platform.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 4e9e6c9179c7fbf405cf8fc661a209da3c9d3b61
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031263"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snelstart: Een toepassing registreren bij het Microsoft-identiteitsplatform

In deze quickstart registreert u een app in de Azure-portal, zodat het Microsoft Identity Platform verificatie- en autorisatieservices kan bieden aan uw toepassing en de gebruikers ervan.

Elke toepassing waarvoor via het Microsoft Identity Platform IAM (Identity and Access Management) moet worden uitgevoerd, moet zijn geregistreerd. Of het nu gaat om een clienttoepassing, zoals een web-app of mobiele app, of om een web-API die een client-app ondersteunt, als u de toepassing registreert brengt u een vertrouwensrelatie tot stand tussen de toepassing en de id-provider, het Microsoft Identity Platform.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement - [gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Voltooiing van [quickstart: Een tenant instellen](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Een toepassing registreren

Het registreren van uw toepassing brengt een vertrouwensrelatie tot stand tussen uw app en het Microsoft Identity Platform. De vertrouwensrelatie heeft één richting: uw app vertrouwt Microsoft Identity Platform, en niet andersom.

Volg deze stappen om de app-registratie te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Geef op wie de toepassing kan gebruiken, ook wel aangeduid als de *doelgroep voor aanmelden*.

    | Ondersteunde accounttypen | Beschrijving |
    |-------------------------|-------------|
    | **Alleen accounts in deze organisatiemap** | Selecteer deze optie als u een toepassing bouwt die alleen is bedoeld voor gebruikers (of gasten) in *uw* tenant.<br><br>Dit wordt vaak een *LOB*-toepassing (Line-of-Business) genoemd. Dit is een toepassing met **één tenant** in het Microsoft Identity Platform. |
    | **Accounts in elke organisatiemap** | Selecteer deze optie als u wilt dat gebruikers in *een willekeurige* Azure AD-tenant uw toepassing kunnen gebruiken. Deze optie is geschikt als u bijvoorbeeld een SaaS-toepassing (Software-as-a-Service) bouwt die u aan meerdere organisaties wilt leveren.<br><br>Dit wordt een toepassing met **meerdere tenants** genoemd in het Microsoft Identity Platform. |
    | **Accounts in elke organisatiemap en persoonlijke Microsoft-accounts** | Selecteer deze optie om de breedste groep klanten te bereiken.<br><br>Als u deze optie selecteert registreert u een toepassing met **meerdere tenants** die ook ondersteuning kan bieden voor gebruikers met een persoonlijk **Microsoft-account** (MSA). |
    | **Persoonlijk Microsoft-account** | Selecteer deze optie als u een toepassing bouwt die alleen is bedoeld voor gebruikers met een persoonlijk Microsoft-account. Persoonlijke Microsoft-accounts inclusief Skype-, Xbox-, Live- en Hotmail-accounts. |

1. Voer niets in bij **Omleidings-URI (optioneel)** . U configureert deze in de volgende sectie.
1. Selecteer **Registreren** om de initiële app-registratie te voltooien.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Schermopname van de Azure-portal in een webbrowser, met het deelvenster Een toepassing registreren.":::

Wanneer de registratie is voltooid, wordt in de Azure-portal het deelvenster **Overzicht** van de app-registratie weergegeven. Dit venster bevat de bijbehorende **Toepassings-(client-)id**. Deze waarde wordt ook wel gewoon *client-id* genoemd en is een unieke aanduiding van uw toepassing in het Microsoft Identity Platform.

De code van uw toepassing (meestal een verificatiebibliotheek die wordt gebruikt in uw toepassing) gebruikt ook de client-id als één aspect bij het valideren van de beveiligingstokens die worden ontvangen van het Identity Platform.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Schermopname van de Azure-portal in een webbrowser, met het deelvenster Overzicht van de app-registratie.":::

## <a name="add-a-redirect-uri"></a>Een omleidings-URI toevoegen

Een omleidings-URI is de locatie waar het Microsoft Identity Platform een client van de gebruiker naartoe omleidt en beveiligingstokens naartoe stuurt na de verificatie.

In een productiewebtoepassing is de omleidings-URI bijvoorbeeld vaak een openbaar eindpunt waar de app wordt uitgevoerd, zoals `https://contoso.com/auth-response`. Tijdens de ontwikkeling is het gebruikelijk om ook het eindpunt toe te voegen waar u de app lokaal uitvoert, zoals `https://127.0.0.1/auth-response` of `http://localhost/auth-response`.

U kunt omleidings-URI's voor uw geregistreerde toepassingen toevoegen en wijzigen door de bijbehorende [platforminstellingen](#configure-platform-settings) te configureren.

### <a name="configure-platform-settings"></a>Platforminstellingen configureren

Instellingen voor elk toepassingstype, waaronder omleidings-URI's, worden geconfigureerd in **Platformconfiguraties** in de Azure-portal. Voor sommige platformen, zoals **Webtoepassingen** en **Toepassingen met één pagina**, moet u handmatig een omleidings-URI opgeven. Voor andere platformen, zoals mobiele toepassingen en desktoptoepassingen, kunt u kiezen uit omleidings-URI's die voor u zijn gegenereerd tijdens het configureren van de andere bijbehorende instellingen.

Als u toepassingsinstellingen wilt configureren op basis van het platform of apparaat, doet u het volgende:

1. Selecteer de toepassing in **App-registraties** in de Azure-portal.
1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer **Een platform toevoegen** onder **Platformconfiguraties**.
1. Selecteer in **Platforms configureren** de tegel voor uw toepassingstype (platform) om de bijbehorende instellingen te configureren.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Schermopname van het deelvenster Platformconfiguratie in de Azure-portal" border="false":::

    | Platform | Configuratie-instellingen |
    | -------- | ---------------------- |
    | **Web** | Voer een **Omleidings-URI** voor de app in, de locatie waar het Microsoft Identity Platform een client van de gebruiker naartoe omleidt en beveiligingstokens naartoe stuurt na de verificatie.<br/><br/>Selecteer dit platform voor standaardwebtoepassingen die worden uitgevoerd op een server. |
    | **Toepassing met één pagina** | Voer een **Omleidings-URI** voor de app in, de locatie waar het Microsoft Identity Platform een client van de gebruiker naartoe omleidt en beveiligingstokens naartoe stuurt na de verificatie.<br/><br/>Selecteer dit platform als u een web-app aan de clientzijde bouwt in JavaScript of met een framework zoals Angular, Vue.js, React.js of Blazor WebAssembly. |
    | **iOS / macOS** | Voer de **Bundel-id** van de app in. Deze vindt u in XCode in *Info.plist*, of in de instellingen voor de build.<br/><br/>Er wordt een omleidings-URI gegenereerd wanneer u een bundel-id opgeeft. |
    | **Android** | Voer de **Pakketnaam** van de app in. Deze vindt u in het bestand *AndroidManifest.xml*. Genereer vervolgens de **Hash voor ondertekening** en voer deze in.<br/><br/>Er wordt een omleidings-URI gegenereerd wanneer u deze instellingen opgeeft. |
    | **Mobiele toepassingen en desktoptoepassingen** | Selecteer een van de **Voorgestelde omleidings-URI's** of geef een **Aangepaste omleidings-URI** op.<br/>Voor desktoptoepassingen kunt u het beste het volgende doen:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Selecteer dit platform voor mobiele toepassingen die niet gebruikmaken van de nieuwste MSAL-versie (Microsoft Authentication Library) of die geen broker gebruiken. Selecteer dit platform ook voor desktoptoepassingen. |
1. Selecteer **Configureren** om de platformconfiguratie te voltooien.

### <a name="redirect-uri-restrictions"></a>URI-beperkingen omleiden

Er gelden bepaalde beperkingen voor de indeling van de omleidings-URI's die u toevoegt aan een app-registratie. Raadpleeg [Beperkingen en limieten voor omleidings-URI's (antwoord-URL's)](reply-url.md) voor meer informatie over deze beperkingen.

## <a name="add-credentials"></a>Referenties toevoegen

Referenties worden gebruikt voor vertrouwelijke clienttoepassingen die toegang hebben tot een web-API. Voorbeelden van vertrouwelijke clients zijn web-apps, andere web-API's, of service- en daemontoepassingen. Met referenties kan uw toepassing zichzelf verifiëren, waardoor er geen interactie van een gebruiker tijdens runtime nodig is.

U kunt zowel certificaten als clientgeheimen (een tekenreeks) toevoegen als referenties voor de registratie van uw vertrouwelijke client-app.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Schermopname van de Azure-portal, met het deelvenster Certificaten en geheimen in een app-registratie":::

### <a name="add-a-certificate"></a>Een certificaat toevoegen

Certificaten worden ook wel een *openbare sleutel* genoemd en zijn het aanbevolen referentietype, omdat ze een hoger controleniveau bieden dan een clientgeheim.

1. Selecteer de toepassing in **App-registraties** in de Azure-portal.
1. Selecteer **Certificaten en geheimen** > **Certificaat uploaden**.
1. Selecteer het bestand dat u wilt uploaden. Dit moet een van de volgende bestandstypen zijn: .cer, .pem, .crt.
1. Selecteer **Toevoegen**.

### <a name="add-a-client-secret"></a>Een clientgeheim toevoegen

Het clientgeheim, ook wel *toepassingswachtwoord* genoemd, is een tekenreekswaarde die de app kan gebruiken (in plaats van een certificaat) om zichzelf te identificeren. Dit type is het eenvoudigste van de twee referentietypen om te gebruiken, en wordt vaak gebruikt in de ontwikkelfase, maar wordt beschouwd als minder veilig dan een certificaat. Voor toepassingen die worden uitgevoerd in de productieomgeving, kunt u beter certificaten gebruiken.

1. Selecteer de toepassing in **App-registraties** in de Azure-portal.
1. Selecteer **Certificaten en geheimen** >  **Nieuw clientgeheim**.
1. Voeg een beschrijving voor uw clientgeheim toe.
1. Selecteer een duur.
1. Selecteer **Toevoegen**.
1. **Noteer de waarde van het geheim** voor gebruik in de code van de clienttoepassing. Als u deze pagina verlaat wordt de waarde *nooit meer weergegeven*.

## <a name="next-steps"></a>Volgende stappen

Clienttoepassingen hebben meestal toegang nodig tot resources in een web-API. Naast het beveiligen van de clienttoepassing met het Microsoft Identity Platform kunt u het platform gebruiken om toegang op basis van machtigingen met een bepaald bereik voor uw web-API te autoriseren.

Ga verder met de volgende quickstart in de reeks om nog een app-registratie te maken voor uw web-API en de bijbehorende bereiken beschikbaar te maken.

> [!div class="nextstepaction"]
> [Een toepassing configureren om een web-API beschikbaar te maken](quickstart-configure-app-expose-web-apis.md)
