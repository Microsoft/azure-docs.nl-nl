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
ms.openlocfilehash: 430ab980f51ff06ad5e39d6402abf5f649cc6d39
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593092"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Snelstart: Een toepassing registreren bij het Microsoft-identiteitsplatform

In deze quickstart registreert u een app in de Azure-portal, zodat het Microsoft Identity Platform verificatie- en autorisatieservices kan bieden aan uw toepassing en de gebruikers ervan.

Het micro soft-identiteits platform voert alleen voor geregistreerde toepassingen een IAM (identiteits-en toegangs beheer) uit. Of het nu gaat om een clienttoepassing, zoals een web-app of mobiele app, of om een web-API die een client-app ondersteunt, als u de toepassing registreert brengt u een vertrouwensrelatie tot stand tussen de toepassing en de id-provider, het Microsoft Identity Platform.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Volt ooien van de Snelstartgids voor het [instellen van een Tenant](quickstart-create-new-tenant.md) .

## <a name="register-an-application"></a>Een toepassing registreren

Het registreren van uw toepassing brengt een vertrouwensrelatie tot stand tussen uw app en het Microsoft Identity Platform. De vertrouwensrelatie heeft één richting: uw app vertrouwt Microsoft Identity Platform, en niet andersom.

Volg deze stappen om de app-registratie te maken:

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u in het bovenste menu het **Directory-en abonnements** filter :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: om de Tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen deze naam zien. U kunt deze later wijzigen.
1. Geef op wie de toepassing kan gebruiken, soms ook wel het *publiek van de aanmelding*.

    | Ondersteunde accounttypen | Beschrijving |
    |-------------------------|-------------|
    | **Alleen accounts in deze organisatiemap** | Selecteer deze optie als u een toepassing bouwt die alleen is bedoeld voor gebruikers (of gasten) in *uw* tenant.<br><br>Deze app wordt vaak een LOB-toepassing ( *line-of-Business* ) genoemd en is een toepassing met *één Tenant* in het micro soft Identity-platform. |
    | **Accounts in elke organisatiemap** | Selecteer deze optie als u wilt dat gebruikers in *een* Azure Active Directory-Tenant (Azure AD) uw toepassing kunnen gebruiken. Deze optie is geschikt als u bijvoorbeeld een SaaS-toepassing (Software-as-a-Service) bouwt die u aan meerdere organisaties wilt leveren.<br><br>Dit type app wordt een *multi tenant* -toepassing genoemd in het micro soft Identity-platform. |
    | **Accounts in elke organisatiemap en persoonlijke Microsoft-accounts** | Selecteer deze optie om de breedste groep klanten te bereiken.<br><br>Als u deze optie selecteert, registreert u een *multi tenant* -toepassing die ook gebruikers kan ondersteunen met persoonlijke *micro soft-accounts*. |
    | **Persoonlijk Microsoft-account** | Selecteer deze optie als u een toepassing alleen wilt maken voor gebruikers met persoonlijke micro soft-accounts. Persoonlijke Microsoft-accounts inclusief Skype-, Xbox-, Live- en Hotmail-accounts. |

1. Geef niets op voor **omleidings-URI (optioneel)**. U configureert een omleidings-URI in de volgende sectie.
1. Selecteer **Registreren** om de initiële app-registratie te voltooien.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Scherm afbeelding van de Azure Portal in een webbrowser, met daarin het deel venster een toepassing registreren.":::

Wanneer de registratie is voltooid, wordt in de Azure Portal het deel venster **overzicht** van de app-registratie weer gegeven. U ziet de **toepassing-id (client)**. Deze waarde wordt ook wel de *client-id* genoemd en is een unieke aanduiding van uw toepassing in het micro soft Identity-platform.

De code van uw toepassing, of meer meestal een verificatie bibliotheek die in uw toepassing wordt gebruikt, gebruikt ook de client-ID. De ID wordt gebruikt als onderdeel van het valideren van de beveiligings tokens die worden ontvangen van het identiteits platform.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Scherm afbeelding van de Azure Portal in een webbrowser, met daarin het overzichts venster van de app-registratie.":::

## <a name="add-a-redirect-uri"></a>Een omleidings-URI toevoegen

Een *omleidings-URI* is de locatie waar het micro soft-identiteits platform de client van een gebruiker omleidt en beveiligings tokens verzendt na verificatie.

In een productiewebtoepassing is de omleidings-URI bijvoorbeeld vaak een openbaar eindpunt waar de app wordt uitgevoerd, zoals `https://contoso.com/auth-response`. Tijdens de ontwikkeling is het gebruikelijk om ook het eindpunt toe te voegen waar u de app lokaal uitvoert, zoals `https://127.0.0.1/auth-response` of `http://localhost/auth-response`.

U kunt omleidings-URI's voor uw geregistreerde toepassingen toevoegen en wijzigen door de bijbehorende [platforminstellingen](#configure-platform-settings) te configureren.

### <a name="configure-platform-settings"></a>Platforminstellingen configureren

Instellingen voor elk toepassingstype, waaronder omleidings-URI's, worden geconfigureerd in **Platformconfiguraties** in de Azure-portal. Voor sommige platformen, zoals **Webtoepassingen** en **Toepassingen met één pagina**, moet u handmatig een omleidings-URI opgeven. Voor andere platforms, zoals mobiele en desktop computers, kunt u een omleidings-Uri's selecteren die voor u worden gegenereerd wanneer u de andere instellingen configureert.

Als u toepassingsinstellingen wilt configureren op basis van het platform of apparaat, doet u het volgende:

1. Selecteer uw toepassing in **app-registraties** in het Azure Portal.
1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer **Een platform toevoegen** onder **Platformconfiguraties**.
1. Selecteer onder **platforms configureren** de tegel voor uw toepassings type (platform) om de instellingen te configureren.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Scherm afbeelding van het deel venster platform configuratie in de Azure Portal." border="false":::

    | Platform | Configuratie-instellingen |
    | -------- | ---------------------- |
    | **Web** | Voer een **omleidings-URI** in voor uw app. Deze URI is de locatie waar het micro soft-identiteits platform een client van een gebruiker omleidt en beveiligings tokens verzendt na verificatie.<br/><br/>Selecteer dit platform voor standaardwebtoepassingen die worden uitgevoerd op een server. |
    | **Toepassing met één pagina** | Voer een **omleidings-URI** in voor uw app. Deze URI is de locatie waar het micro soft-identiteits platform een client van een gebruiker omleidt en beveiligings tokens verzendt na verificatie.<br/><br/>Selecteer dit platform als u een web-app aan de client zijde bouwt met behulp van Java script of een framework zoals hoek, Vue.js, React.js of razendsnelle webassembly. |
    | **iOS / macOS** | Voer de app **-bundel-id** in. Zoek het in de **instellingen voor bouwen** of in Xcode in *info. plist*.<br/><br/>Er wordt een omleidings-URI gegenereerd wanneer u een **bundel-id** opgeeft. |
    | **Android** | Voer de naam van het app- **pakket** in. Zoek het naar het *AndroidManifest.xml* -bestand. Genereer ook de **hand tekening-hash** en voer deze in.<br/><br/>Er wordt een omleidings-URI gegenereerd wanneer u deze instellingen opgeeft. |
    | **Mobiele toepassingen en desktoptoepassingen** | Selecteer een van de **voorgestelde omleidings-uri's**. Of geef een **aangepaste omleidings-URI** op.<br/><br/>Voor desktop toepassingen kunt u het beste<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Selecteer dit platform voor mobiele toepassingen die niet gebruikmaken van de nieuwste micro soft Authentication Library (MSAL) of die geen Broker gebruikt. Selecteer dit platform ook voor desktoptoepassingen. |
1. Selecteer **Configureren** om de platformconfiguratie te voltooien.

### <a name="redirect-uri-restrictions"></a>URI-beperkingen omleiden

Er zijn enkele beperkingen voor de indeling van de omleidings-Uri's die u toevoegt aan een app-registratie. Zie [beperkingen en beperkingen voor omleidings-URI (antwoord-URL)](reply-url.md)voor meer informatie over deze beperkingen.

## <a name="add-credentials"></a>Referenties toevoegen

Referenties worden gebruikt door [vertrouwelijke client toepassingen](msal-client-applications.md) die toegang hebben tot een web-API. Voor beelden van vertrouwelijke clients zijn [Web-apps](scenario-web-app-call-api-overview.md), andere web- [api's](scenario-protected-web-api-overview.md)of [Service-type-en daemon-type toepassingen](scenario-daemon-overview.md). Met referenties kan uw toepassing zichzelf verifiëren, waardoor er geen interactie van een gebruiker tijdens runtime nodig is. 

U kunt zowel certificaten als clientgeheimen (een tekenreeks) toevoegen als referenties voor de registratie van uw vertrouwelijke client-app.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Scherm afbeelding van de Azure Portal, waarin het deel venster certificaten en geheimen in een app-registratie wordt weer gegeven.":::

### <a name="add-a-certificate"></a>Een certificaat toevoegen

Een certificaat wordt ook wel een *open bare sleutel* genoemd en is het aanbevolen referentie type. Het biedt meer zekerheid dan een client geheim. Zie voor meer informatie over het gebruik van een certificaat als verificatie methode in uw toepassing de referenties voor het [micro soft-identiteits platform toepassings verificatie certificaat](active-directory-certificate-credentials.md).

1. Selecteer uw toepassing in **app-registraties** in het Azure Portal.
1. Selecteer **Certificaten en geheimen** > **Certificaat uploaden**.
1. Selecteer het bestand dat u wilt uploaden. Dit moet een van de volgende bestands typen zijn: *. CER*, *. pem*, *. CRT*.
1. Selecteer **Toevoegen**.

### <a name="add-a-client-secret"></a>Een clientgeheim toevoegen

Het client geheim wordt ook wel een *toepassings wachtwoord* genoemd. Het is een teken reeks waarde die uw app kan gebruiken in plaats van een certificaat voor identiteit zelf. Het client geheim is het gemak van de twee referentie typen die kunnen worden gebruikt. Dit wordt vaak gebruikt tijdens de ontwikkeling, maar wordt beschouwd als minder veilig dan een certificaat. Gebruik certificaten in uw toepassingen die worden uitgevoerd in de productie omgeving. 

Zie [Aanbevolen procedures en aanbevelingen voor micro soft-identiteits platform](identity-platform-integration-checklist.md#security)voor meer informatie over aanbevelingen voor het beveiligen van de toepassing.

1. Selecteer uw toepassing in **app-registraties** in het Azure Portal.
1. Selecteer **Certificaten en geheimen** >  **Nieuw clientgeheim**.
1. Voeg een beschrijving voor uw clientgeheim toe.
1. Selecteer een duur.
1. Selecteer **Toevoegen**.
1. *Noteer de waarde van het geheim* voor gebruik in de code van uw client toepassing. Deze geheime waarde wordt *nooit weer gegeven* nadat u deze pagina verlaat.


## <a name="next-steps"></a>Volgende stappen

Clienttoepassingen hebben meestal toegang nodig tot resources in een web-API. U kunt uw client toepassing beveiligen met behulp van het micro soft Identity-platform. U kunt ook het platform gebruiken voor het machtigen van scoped, toegangs rechten op basis van machtigingen voor uw web-API.

Ga naar de volgende Snelstartgids in de reeks om een andere app-registratie voor uw web-API te maken en de scopes weer te geven.

> [!div class="nextstepaction"]
> [Een toepassing configureren om een web-API beschikbaar te maken](quickstart-configure-app-expose-web-apis.md)
