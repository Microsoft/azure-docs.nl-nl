---
title: Omleidings-URI-beperkingen (antwoord-URL) | Azure
titleSuffix: Microsoft identity platform
description: Een beschrijving van de beperkingen en beperkingen van de indeling van de omleidings-URI (antwoord-URL) die wordt afgedwongen door het micro soft Identity-platform.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 11/23/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: 30ea74b249937544a0bf9811cad60f02c1ca45c7
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95752780"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Omleidings-URI (antwoord-URL) beperkingen en beperkingen

Een omleidings-URI of antwoord-URL is de locatie waar de autorisatie server de gebruiker verzendt zodra de app is geautoriseerd en een autorisatie code of toegangs token heeft toegekend. De autorisatie server verzendt de code of het token naar de omleidings-URI. Daarom is het belang rijk dat u de juiste locatie registreert als onderdeel van het registratie proces van de app.

 De volgende beperkingen zijn van toepassing op omleidings-URI's:

* De omleidings-URI moet beginnen met het schema `https` . Er zijn enkele [uitzonde ringen voor localhost](#localhost-exceptions) -omleidings-uri's.

* De omleidings-URI is hoofdletter gevoelig. Het hoofdlettergebruik moet overeenkomen met het URL-pad van de actieve toepassing. Als uw toepassing bijvoorbeeld een deel van het pad bevat `.../abc/response-oidc` , moet u niet opgeven `.../ABC/response-oidc` in de omleidings-URI. Omdat de webbrowser paden als hoofdlettergevoelig behandelt, kunnen cookies die zijn gekoppeld aan `.../abc/response-oidc` worden uitgesloten als ze worden omgeleid naar de qua hoofdlettergebruik niet-overeenkomende URL `.../ABC/response-oidc`.

## <a name="maximum-number-of-redirect-uris"></a>Maximum aantal omleidings-Uri's

In deze tabel ziet u het maximum aantal omleidings-Uri's dat u kunt toevoegen aan een app-registratie in het micro soft Identity-platform.

| Accounts waarbij wordt aangemeld | Maximum aantal omleidings-Uri's | Description |
|--------------------------|---------------------------------|-------------|
| Micro soft-werk-of school accounts in de Tenant van de Azure Active Directory van een organisatie (Azure AD) | 256 | `signInAudience` het veld in het toepassings manifest is ingesteld op *AzureADMyOrg* of *AzureADMultipleOrgs* |
| Persoonlijke micro soft-accounts en werk-en school accounts | 100 | `signInAudience` het veld in het manifest van de toepassing is ingesteld op *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximale URI-lengte

U kunt Maxi maal 256 tekens gebruiken voor elke omleidings-URI die u toevoegt aan een app-registratie.

## <a name="supported-schemes"></a>Ondersteunde schema's

Het toepassings model van Azure Active Directory (Azure AD) ondersteunt momenteel zowel HTTP-als HTTPS-schema's voor apps die werk-of school accounts registreren in de Azure AD-Tenant van elke organisatie. Deze account typen worden opgegeven met de `AzureADMyOrg` `AzureADMultipleOrgs` waarden en in het `signInAudience` veld van het toepassings manifest. Voor apps die zich aanmelden met persoonlijke micro soft-accounts (MSA) *en* werk-en school accounts (de `signInAudience` is ingesteld op `AzureADandPersonalMicrosoftAccount` ), is alleen het HTTPS-schema toegestaan.

Als u omleidings-Uri's wilt toevoegen met een HTTP-schema naar app-registraties waarmee werk-of school accounts worden ondertekend, moet u de manifest editor van de toepassing gebruiken in [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in de Azure Portal. Hoewel het mogelijk is om een HTTP-gebaseerde omleidings-URI in te stellen met behulp van de manifest editor, raden we u *ten zeerste* aan het HTTPS-schema voor uw omleidings-uri's te gebruiken.

## <a name="localhost-exceptions"></a>Localhost-uitzonde ringen

Per [RFC 8252-secties 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) en [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), worden ' loop back-' of ' localhost ' omleidings-uri's geleverd met twee speciale overwegingen:

1. `http` URI-schema's zijn acceptabel omdat de omleiding nooit het apparaat verlaat. Als zodanig zijn beide Uri's acceptabel:
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. Als gevolg van tijdelijke poortbereiken die vaak vereist zijn voor systeem eigen toepassingen, wordt het poort onderdeel (bijvoorbeeld `:5001` of `:443` ) genegeerd voor de doel einden van het afstemmen van een omleidings-URI. Als gevolg hiervan worden al deze Uri's als gelijkwaardig beschouwd:
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Uit het oogpunt van ontwikkeling betekent dit een aantal dingen:

* Registreer niet meerdere omleidings-Uri's waarbij alleen de poort verschilt. Op de aanmeldings server wordt één wille keurig gekozen en wordt het gedrag gebruikt dat aan die omleidings-URI is gekoppeld (bijvoorbeeld of het een `web` -, `native` -of `spa` -type omleiding is).

    Dit is vooral belang rijk wanneer u verschillende verificatie stromen wilt gebruiken in dezelfde toepassings registratie, bijvoorbeeld zowel de autorisatie code subsidie als de impliciete stroom. Om het juiste reactie gedrag te koppelen aan elke omleidings-URI, moet de aanmeldings server kunnen onderscheiden van de omleidings-Uri's en kan dit niet doen als alleen de poort verschilt.
* Als u meerdere omleidings-Uri's op localhost wilt registreren om verschillende stromen tijdens de ontwikkeling te testen, moet u deze onderscheiden met de *padcomponent* van de URI. `http://localhost/MyWebApp`Komt bijvoorbeeld niet overeen `http://localhost/MyNativeApp` .
* Het IPv6-loop back-adres ( `[::1]` ) wordt momenteel niet ondersteund.

#### <a name="prefer-127001-over-localhost"></a>Liever 127.0.0.1 via localhost

Als u wilt voor komen dat uw app wordt verbroken door onjuist geconfigureerde firewalls of de naam van netwerk interfaces is gewijzigd, gebruikt u het IP-adres van de letterlijke waarde `127.0.0.1` in de omleidings-URI in plaats van `localhost` . Bijvoorbeeld `https://127.0.0.1`.

U kunt echter het tekstvak **omleidings-uri's** in de Azure Portal gebruiken om een omleidings-URI op basis van loop back toe te voegen die gebruikmaakt van het `http` schema:

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Fout dialoogvenster in Azure Portal met niet-toegestane http-gebaseerde loop back omleidings-URI":::

Als u een omleidings-URI wilt toevoegen die gebruikmaakt `http` van het schema met het `127.0.0.1` loop back-adres, moet u het kenmerk [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) momenteel wijzigen in het manifest van de [toepassing](reference-app-manifest.md).

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Beperkingen voor joker tekens in omleidings-Uri's

Joker teken-Uri's zoals `https://*.contoso.com` mogelijk handig lijken, maar moeten worden vermeden vanwege beveiligings implicaties. Volgens de OAuth 2,0-specificatie ([sectie 3.1.2 van RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) moet een omleidings EINDPUNT-URI een absolute URI zijn.

Uri's met Joker tekens worden momenteel niet ondersteund in app-registraties die zijn geconfigureerd voor het aanmelden van persoonlijke micro soft-accounts en werk-of school accounts. Joker teken-Uri's zijn echter toegestaan voor apps die zijn geconfigureerd voor aanmelding alleen werk-of school accounts in de Azure AD-Tenant van een organisatie.

Als u omleidings-Uri's wilt toevoegen met Joker tekens naar app-registraties waarmee werk-of school accounts worden ondertekend, moet u de manifest editor van de toepassing gebruiken in [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in de Azure Portal. Hoewel het mogelijk is om een omleidings-URI met een Joker teken in te stellen met behulp van de manifest editor, raden wij u *ten zeerste* aan de [sectie 3.1.2 van RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) te voldoen en alleen absolute uri's te gebruiken.

Als uw scenario meer omleidings-Uri's vereist dan de Maxi maal toegestane limiet, moet u rekening houden met de volgende [status parameter methode](#use-a-state-parameter) in plaats van een omleidings-URI voor joker tekens.

#### <a name="use-a-state-parameter"></a>Een para meter State gebruiken

Als u meerdere subdomeinen hebt en uw scenario vereist dat, wanneer de verificatie is geslaagd, gebruikers omleiden naar dezelfde pagina als waar ze worden gestart, kan het handig zijn om een para meter State te gebruiken.

In deze benadering:

1. Maak een ' gedeelde ' omleidings-URI per toepassing voor het verwerken van de beveiligings tokens die u van het autorisatie-eind punt ontvangt.
1. Uw toepassing kan toepassingsspecifieke para meters (zoals subdomein-URL waar de gebruiker afkomstig is of iets zoals huismerk gegevens) verzenden in de para meter State. Wanneer u een para meter State gebruikt, beschermt u tegen CSRF-beveiliging zoals beschreven in [sectie 10,12 van RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)).
1. De toepassingsspecifieke para meters bevatten alle informatie die nodig is voor de toepassing om de juiste ervaring voor de gebruiker te genereren, dat wil zeggen, de juiste toepassings status te maken. De Azure AD-autorisatie-eind punt verwijdert HTML uit de para meter State, dus zorg ervoor dat u geen HTML-inhoud in deze para meter opgeeft.
1. Wanneer Azure AD een reactie verzendt naar de ' gedeelde ' omleidings-URI, wordt de para meter State teruggestuurd naar de toepassing.
1. De toepassing kan vervolgens de waarde in de para meter State gebruiken om te bepalen met welke URL de gebruiker verder moet worden verzonden. Zorg ervoor dat u valideert voor CSRF-beveiliging.

> [!WARNING]
> Met deze aanpak kan een aangetaste client de aanvullende para meters wijzigen die in de para meter State worden verzonden, waardoor de gebruiker wordt omgeleid naar een andere URL. Dit is de [Open redirector-bedreiging](https://tools.ietf.org/html/rfc6819#section-4.2.4) die wordt beschreven in RFC 6819. Daarom moet de client deze para meters beveiligen door de status te versleutelen of op een andere manier te verifiëren, zoals het valideren van de domein naam in de omleidings-URI op basis van het token.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [toepassings manifest](reference-app-manifest.md)van de app-registratie.
