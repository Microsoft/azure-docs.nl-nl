---
title: Versies van Java script en pagina-indeling
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van Java script en het gebruik van pagina-indelings versies in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6bb478038d398226db38dc20e49ed7a14e5d5d0a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592803"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Java script-en pagina-indelings versies in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C biedt een set verpakte inhoud die HTML, CSS en Java script bevat voor de elementen van de gebruikers interface in uw gebruikers stromen en aangepaste beleids regels. Java script inschakelen voor uw toepassingen:

::: zone pivot="b2c-user-flow"

* Selecteer een [pagina-indeling](page-layout.md)
* Schakel deze in op de gebruikers stroom met behulp van de Azure Portal
* [B2clogin.com](b2clogin.md) in uw aanvragen gebruiken

::: zone-end

::: zone pivot="b2c-custom-policy"

* Selecteer een [pagina-indeling](page-layout.md)
* Een element toevoegen aan uw [aangepaste beleid](custom-policy-overview.md)
* [B2clogin.com](b2clogin.md) in uw aanvragen gebruiken

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Een versie van een pagina-indeling selecteren

Als u van plan bent om Java script-client code in te scha kelen, moeten de elementen waarop u uw Java script baseert, onveranderbaar zijn. Als ze niet onveranderbaar zijn, kunnen wijzigingen op de pagina's van uw gebruikers onverwacht gedrag veroorzaken. Als u deze problemen wilt voor komen, dwingt u het gebruik van een pagina-indeling af en geeft u een versie van de pagina-indeling op om ervoor te zorgen dat de inhouds definities waarop u uw Java script hebt gebaseerd, onveranderbaar Zelfs als u niet van plan bent java script in te scha kelen, kunt u een versie van de pagina-indeling opgeven voor uw pagina's.

::: zone pivot="b2c-user-flow"

Een versie van de pagina-indeling opgeven voor de pagina's van de gebruikers stroom: 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
1. Selecteer **pagina-indelingen**. Kies een **naam** voor de indeling en kies vervolgens de versie van de **pagina-indeling (preview)**.

Zie voor meer informatie over de verschillende versies van de pagina-indeling het [logboek met versie wijzigingen](page-layout.md)voor de pagina.

![Instellingen voor pagina-indeling in portal met de vervolg keuzelijst versie van pagina-indeling](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Selecteer een [pagina-indeling](contentdefinitions.md#select-a-page-layout) voor de elementen van de gebruikers interface van uw toepassing.

Definieer een [versie](contentdefinitions.md#migrating-to-page-layout) van een pagina-indeling met pagina `contract` versie voor *alle* inhouds definities in uw aangepaste beleid. De notatie van de waarde moet het woord `contract` : _urn: com: micro soft: AAD: B2C: elementen:**contract**:p Age-name: Version_ bevatten. Meer informatie over het [migreren naar de pagina-indeling](contentdefinitions.md#migrating-to-page-layout) met de pagina versie.

In het volgende voor beeld ziet u de inhouds definitie-id's en de bijbehorende **DataUri** met het pagina contract: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>JavaScript inschakelen

::: zone pivot="b2c-user-flow"

In de **Eigenschappen** van de gebruikers stroom kunt u Java script inschakelen. Als u Java script inschakelt, wordt ook het gebruik van een pagina-indeling afgedwongen. U kunt vervolgens de versie van de pagina-indeling instellen voor de gebruikers stroom zoals beschreven in de volgende sectie.

![Eigenschappen pagina gebruikers stroom met de instelling Java script inschakelen gemarkeerd](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

U kunt het uitvoeren van scripts inschakelen door het element **ScriptExecution** toe te voegen aan het element [RelyingParty](relyingparty.md) .

1. Open uw aangepaste beleids bestand. Bijvoorbeeld *SignUpOrSignin.xml*.
1. Voeg het element **ScriptExecution** toe aan het element **RelyingParty** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Sla het bestand op en upload het.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Richt lijnen voor het gebruik van Java script

Volg deze richt lijnen bij het aanpassen van de interface van uw toepassing met behulp van Java script:

- Bind een Click-gebeurtenis op `<a>` HTML-elementen niet.
- Maak geen afhankelijkheid van Azure AD B2C code of opmerkingen.
- Wijzig de volg orde of hiërarchie van Azure AD B2C HTML-elementen niet. Gebruik een Azure AD B2C beleid om de volg orde van de elementen van de gebruikers interface te bepalen.
- U kunt een wille keurige REST-service aanroepen met de volgende overwegingen:
    - Mogelijk moet u de CORS van de REST-service instellen om HTTP-aanroepen aan de client zijde toe te staan.
    - Zorg ervoor dat de REST-service veilig is en alleen het HTTPS-protocol gebruikt.
    - Gebruik Java script niet rechtstreeks om Azure AD B2C-eind punten aan te roepen.
- U kunt uw Java script insluiten of u kunt een koppeling maken naar externe java script-bestanden. Wanneer u een extern java script-bestand gebruikt, moet u ervoor zorgen dat u de absolute URL gebruikt en geen relatieve URL.
- Java script-frameworks:
    - Azure AD B2C maakt gebruik van een specifieke versie van jQuery. Neem geen andere versie van jQuery op. Als u meer dan één versie op dezelfde pagina gebruikt, worden er problemen veroorzaakt.
    - Het gebruik van RequireJS wordt niet ondersteund.
    - De meeste Java script-frameworks worden niet ondersteund door Azure AD B2C.
- Azure AD B2C-instellingen kunnen worden gelezen door `window.SETTINGS` - `window.CONTENT` objecten, zoals de huidige taal van de gebruikers interface, aan te roepen. Wijzig de waarde van deze objecten niet.
- Als u het fout bericht Azure AD B2C wilt aanpassen, gebruikt u lokalisatie in een beleid.
- Als er iets kan worden bereikt met behulp van een beleid, is dit doorgaans de aanbevolen manier.

## <a name="javascript-samples"></a>JavaScript-voorbeelden

### <a name="show-or-hide-a-password"></a>Een wacht woord weer geven of verbergen

Een veelgebruikte manier om uw klanten te helpen hun registratie te laten slagen is om te voor komen dat ze hun wacht woord hebben ingevoerd. Met deze optie kunnen gebruikers zich registreren door ze in te scha kelen om hun wacht woord zo nodig eenvoudig te kunnen bekijken en te corrigeren. Een veld van het type wacht woord heeft een selectie vakje met een **wachtwoord label weer geven** .  Op deze manier kan de gebruiker het wacht woord als tekst zonder opmaak weer geven. Voeg dit code fragment toe aan uw registratie-of aanmeldings sjabloon voor een zelfbevestigende pagina:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Gebruiks voorwaarden toevoegen

Voeg de volgende code toe aan de pagina waar u een selectie vakje voor de **gebruiks voorwaarden** wilt toevoegen. Dit selectie vakje is doorgaans nodig voor de aanmeldings pagina's van uw lokale account en voor het account voor sociale accounts.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Vervang in de code door `termsOfUseUrl` de koppeling naar uw gebruiksrecht overeenkomst. Maak voor uw Directory een nieuw gebruikers kenmerk met de naam **termsOfUse** en voeg vervolgens **termsOfUse** als een gebruikers kenmerk toe.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de gebruikers interface van uw toepassingen kunt aanpassen in [het aanpassen van de gebruikers interface van uw toepassing in azure Active Directory B2C](customize-ui-with-html.md).
