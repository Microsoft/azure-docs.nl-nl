---
title: Taal aanpassing in Azure Active Directory B2C
description: Meer informatie over het aanpassen van de taal ervaring in uw gebruikers stromen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2c39d9809204328a6eba30c073b1fa81795924a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840284"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Taal aanpassing in Azure Active Directory B2C

Door de taal aanpassing in Azure Active Directory B2C (Azure AD B2C) kan uw gebruikers stroom voorzien in verschillende talen die aan de behoeften van uw klant voldoen. Micro soft biedt de vertalingen voor [36 talen](#supported-languages), maar u kunt ook uw eigen vertalingen voor elke taal opgeven. Zelfs als uw ervaring is bedoeld voor slechts één taal, kunt u tekst op de pagina's aanpassen.

## <a name="how-language-customization-works"></a>De werking van taal aanpassing

U gebruikt taal aanpassing om te selecteren in welke talen uw gebruikers stroom beschikbaar is. Nadat de functie is ingeschakeld, kunt u de query teken reeks parameter opgeven, `ui_locales` vanuit uw toepassing. Wanneer u Azure AD B2C aanroept, wordt uw pagina vertaald naar de land instellingen die u hebt opgegeven. Dit type configuratie geeft u volledige controle over de talen in uw gebruikers stroom en negeert de taal instellingen van de browser van de klant.

Mogelijk hebt u niet het controle niveau nodig over de talen die uw klant ziet. Als u geen `ui_locales` para meter opgeeft, wordt de gebruikers ervaring bepaald door de instellingen van de browser. U kunt nog steeds bepalen in welke talen uw gebruikers stroom wordt vertaald door deze toe te voegen als een ondersteunde taal. Als de browser van een klant is ingesteld om een taal weer te geven die u niet wilt ondersteunen, wordt in plaats daarvan de taal weer gegeven die u als standaard waarde hebt geselecteerd in ondersteunde cult uren.

* **gebruikers interface-land instellingen opgegeven taal**: nadat u de taal aanpassing hebt ingeschakeld, wordt uw gebruikers stroom vertaald naar de taal die u hier opgeeft.
* Door **browser aangevraagde taal**: als er geen `ui_locales` para meter is opgegeven, wordt uw gebruikers stroom vertaald naar de door de browser aangevraagde taal, *als de taal wordt ondersteund*.
* **Standaard taal van beleid**: als in de browser geen taal is opgegeven of als er een niet wordt ondersteund, wordt de gebruikers stroom vertaald naar de standaard taal van de gebruikers stroom.

> [!NOTE]
> Als u aangepaste gebruikers kenmerken gebruikt, moet u uw eigen vertalingen opgeven. Zie [uw teken reeksen aanpassen](#customize-your-strings)voor meer informatie.

## <a name="support-requested-languages-for-ui_locales"></a>Ondersteuning voor aangevraagde talen voor ui_locales

Beleids regels die zijn gemaakt voor de algemene Beschik baarheid van taal aanpassing, moeten deze functie eerst inschakelen. Beleids regels en gebruikers stromen die zijn gemaakt nadat de taal aanpassing standaard is ingeschakeld.

Wanneer u taal aanpassing inschakelt voor een gebruikers stroom, kunt u de taal van de gebruikers stroom beheren door de para meter toe te voegen `ui_locales` .

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom die u wilt inschakelen voor vertalingen.
1. Selecteer **talen**.
1. Selecteer **taal aanpassing inschakelen**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecteren welke talen in uw gebruikers stroom zijn ingeschakeld

Schakel een set talen in voor uw gebruikers stroom die moet worden omgezet naar wanneer dit wordt aangevraagd door de browser zonder de `ui_locales` para meter.

1. Zorg ervoor dat de taal aanpassing van uw gebruikers stroom is ingeschakeld vanuit de vorige instructies.
1. Selecteer op de pagina **talen** voor de gebruikers stroom een taal die u wilt ondersteunen.
1. Wijzig in het deel venster Eigenschappen de **instelling ingeschakeld** in **Ja**.
1. Selecteer **Opslaan** boven in het deel venster Eigenschappen.

>[!NOTE]
>Als er `ui_locales` geen para meter wordt gegeven, wordt de pagina alleen vertaald naar de browser taal van de klant als deze is ingeschakeld.
>

## <a name="customize-your-strings"></a>Uw teken reeksen aanpassen

Door taal aanpassing kunt u een wille keurige teken reeks in uw gebruikers stroom aanpassen.

1. Zorg ervoor dat de taal aanpassing voor uw gebruikers stroom is ingeschakeld in de vorige instructies.
1. Selecteer op de pagina **talen** voor de gebruikers stroom de taal die u wilt aanpassen.
1. Selecteer onder **bronnen bestanden op pagina niveau** de pagina die u wilt bewerken.
1. Selecteer **standaard waarden downloaden** (of **down loads negeren** als u deze taal eerder hebt bewerkt).

Deze stappen geven u een JSON-bestand dat u kunt gebruiken om te beginnen met het bewerken van uw teken reeksen.

### <a name="change-any-string-on-the-page"></a>Een wille keurige teken reeks op de pagina wijzigen

1. Open het JSON-bestand dat u hebt gedownload van de vorige instructies in een JSON-editor.
1. Zoek het element dat u wilt wijzigen. U kunt zoeken naar `StringId` de teken reeks die u zoekt, of zoeken naar het `Value` kenmerk dat u wilt wijzigen.
1. Werk het `Value` kenmerk bij met wat u wilt weer geven.
1. Voor elke teken reeks die u wilt wijzigen in, wijzigt u `Override` in `true` .
1. Sla het bestand op en upload uw wijzigingen. (U kunt het upload beheer vinden op dezelfde locatie als waar u het JSON-bestand hebt gedownload.)

> [!IMPORTANT]
> Als u een teken reeks moet overschrijven, moet u ervoor zorgen dat u de `Override` waarde instelt op `true` . Als de waarde niet wordt gewijzigd, wordt de vermelding genegeerd.

### <a name="change-extension-attributes"></a>Extensie kenmerken wijzigen

Als u de teken reeks voor een aangepast gebruikers kenmerk wilt wijzigen of een wilt toevoegen aan de JSON, heeft dit de volgende notatie:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Vervang door `<ExtensionAttribute>` de naam van uw aangepaste gebruikers kenmerk.

Vervang door `<ExtensionAttributeValue>` de nieuwe teken reeks die moet worden weer gegeven.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Een lijst met waarden opgeven met behulp van LocalizedCollections

Als u een lijst met waarden voor antwoorden wilt opgeven, moet u een `LocalizedCollections` kenmerk maken. `LocalizedCollections` is een matrix van `Name` en- `Value` paren. De volg orde van de items is de volg orde waarin ze worden weer gegeven. Gebruik de volgende indeling om toe te voegen `LocalizedCollections` :

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` is het gebruikers kenmerk waarvan dit `LocalizedCollections` kenmerk een antwoord is.
* `Name` is de waarde die wordt weer gegeven voor de gebruiker.
* `Value` is wat wordt geretourneerd in de claim wanneer deze optie is geselecteerd.

### <a name="upload-your-changes"></a>Uw wijzigingen uploaden

1. Nadat u de wijzigingen in het JSON-bestand hebt voltooid, gaat u terug naar de B2C-Tenant.
1. Selecteer **gebruikers stromen** en klik op de gebruikers stroom die u wilt inschakelen voor vertalingen.
1. Selecteer **talen**.
1. Selecteer de taal waarin u wilt vertalen.
1. Selecteer de pagina waar u vertalingen wilt opgeven.
1. Selecteer het mappictogram en selecteer het JSON-bestand dat u wilt uploaden.

De wijzigingen worden automatisch opgeslagen in de stroom van uw gebruikers.

## <a name="customize-the-page-ui-by-using-language-customization"></a>De pagina gebruikers interface aanpassen met behulp van taal aanpassing

Er zijn twee manieren om uw HTML-inhoud te lokaliseren. Een manier is om [taal aanpassing](user-flow-language-customization.md)in te scha kelen. Als u deze functie inschakelt, kunnen Azure AD B2C de OpenID Connect Connect-para meter, `ui-locales` naar uw eind punt door sturen. De inhouds server kan deze para meter gebruiken om aangepaste HTML-pagina's te bieden die specifiek zijn voor een specifieke taal.

U kunt ook inhoud vanaf verschillende locaties ophalen op basis van de land instelling die wordt gebruikt. In het eind punt waarvoor CORS is ingeschakeld, kunt u een mapstructuur instellen om inhoud voor specifieke talen te hosten. U belt het juiste nummer als u de Joker teken waarde gebruikt `{Culture:RFC5646}` . Stel dat dit uw aangepaste pagina-URI is:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

U kunt de pagina laden in `fr` . Wanneer de pagina HTML-en CSS-inhoud haalt, wordt deze opgehaald uit:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Aangepaste talen toevoegen

U kunt ook talen toevoegen waarnaar micro soft momenteel geen vertalingen levert. U moet de vertalingen opgeven voor alle teken reeksen in de gebruikers stroom. Taal-en land codes zijn beperkt tot die in de ISO 639-1-standaard.

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
2. Klik op de gebruikers stroom waaraan u aangepaste talen wilt toevoegen en klik vervolgens op **talen**.
3. Selecteer **aangepaste taal toevoegen** boven aan de pagina.
4. In het context venster dat wordt geopend, identificeert u in welke taal u de vertalingen levert door een geldige land instelling in te voeren.
5. Voor elke pagina kunt u een set onderdrukkingen downloaden voor Engels en werken aan de vertalingen.
6. Nadat u klaar bent met de JSON-bestanden, kunt u deze voor elke pagina uploaden.
7. Selecteer **inschakelen** en uw gebruikers stroom kan deze taal nu weer geven voor uw gebruikers.
8. Sla de taal op.

>[!IMPORTANT]
>Voordat u kunt opslaan, moet u de aangepaste talen inschakelen of overschrijvingen uploaden.

## <a name="additional-information"></a>Aanvullende informatie

### <a name="page-ui-customization-labels-as-overrides"></a>Labels voor pagina-UI aanpassen als onderdrukkingen

Wanneer u taal aanpassing inschakelt, worden uw vorige wijzigingen voor labels met de aanpassing van de pagina-UI persistent gemaakt in een JSON-bestand voor Engels (en). U kunt door gaan met het wijzigen van uw labels en andere teken reeksen door taal resources te uploaden bij het aanpassen van de taal.

### <a name="up-to-date-translations"></a>Up-to-date vertalingen

Micro soft streeft ernaar de meest recente vertalingen te leveren voor uw gebruik. Micro soft verbetert de vertalingen voortdurend en houdt ze op de naleving voor u. Micro soft identificeert bugs en wijzigingen in globale terminologie en maakt updates die naadloos werken in uw gebruikers stroom.

### <a name="support-for-right-to-left-languages"></a>Ondersteuning voor talen die van rechts naar links worden geschreven

Micro soft biedt momenteel geen ondersteuning voor talen die van rechts naar links worden geschreven. U kunt dit doen door aangepaste land instellingen te gebruiken en CSS te gebruiken voor het wijzigen van de manier waarop de teken reeksen worden weer gegeven. Als u deze functie nodig hebt, kunt u hieraan stemmen voor [Azure feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Vertalingen van de sociale ID-provider

Micro soft biedt de `ui_locales` para meter OIDC aan sociale aanmeldingen. Maar sommige aanbieders van sociale identiteiten, waaronder Facebook en Google, voldoen niet aan deze providers.

### <a name="browser-behavior"></a>Browser gedrag

Chrome en Firefox hebben beide aanvragen voor de ingestelde taal. Als het een ondersteunde taal is, wordt deze weer gegeven vóór de standaard waarde. Micro soft Edge vraagt momenteel geen taal aan en gaat rechtstreeks naar de standaard taal.

## <a name="supported-languages"></a>Ondersteunde talen

Azure AD B2C biedt ondersteuning voor de volgende talen. De talen van de gebruikers stroom worden door Azure AD B2C verschaft. De meldingen voor multi-factor Authentication (MFA) worden geleverd door [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Taal              | Taalcode | Gebruikersstromen         | MFA-meldingen  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabisch                | ar            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Bulgaars             | bg            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Bengaals                | bn            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Catalaans               | ca            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Tsjechisch                 | cs            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Deens                | da            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Duits                | de            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Grieks                 | el            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Engels               | en            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Spaans               | es            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Ests              | et            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Baskisch                | eu            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Fins               | fi            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Frans                | fr            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Galicisch              | gl            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Gujarati              | gu            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Hebreeuws                | he            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Kroatisch              | uur            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Hongaars             | hu            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Indonesisch            | id            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Italiaans               | it            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Japans              | ja            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Kazachs                | kk            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Kannada               | kn            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Koreaans                | ko            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Litouws            | lt            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Lets               | lv            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Mahrati               | mr            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Maleisisch                 | ms            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Noors (Bokmål)      | nb            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Nederlands                 | nl            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Noors             | nee            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Pools                | pl            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Portuguese - Brazil   | pt-br         | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Portugees - Portugal | pt-pt         | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Roemeens              | ro            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Russisch               | ru            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Slowaaks                | sk            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Sloveens             | sl            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Servisch - Cyrillisch    | SR-Cryl-CS    | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Servisch - Latijn       | SR-latn-cs    | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Zweeds               | sv            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Tamil                 | ta            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) |
| Thai                  | th            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Turks               | tr            | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Oekraïens             | uk            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Vietnamees            | vi            | ![X geeft Nee aan.](./media/user-flow-language-customization/no.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Chinese - vereenvoudigd  | zh-Hans       | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
| Chinese - traditioneel | zh-hant       | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) | ![Groen vinkje.](./media/user-flow-language-customization/yes.png) |
