---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Geef het ContentDefinitions-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62bae22b6a4bb06b1e97c18e52ad614fd2439902
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489318"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

U kunt het uiterlijk van elk [zelf bevestigd technisch profiel](self-asserted-technical-profile.md)aanpassen. Azure Active Directory B2C (Azure AD B2C) voert code uit in de browser van uw klant en maakt gebruik van een moderne benadering genoemd cross-Origin Resource Sharing (CORS).

Als u de gebruikers interface wilt aanpassen, geeft u een URL op in het **ContentDefinition** -element met aangepaste HTML-inhoud. In het zelfondertekende technische profiel of **OrchestrationStep**, wijst u naar die inhouds definitie-id. De inhouds definitie kan een **LocalizedResourcesReferences** -element bevatten dat een lijst bevat met gelokaliseerde resources die moeten worden geladen. Azure AD B2C combineert elementen van de gebruikersinterface met de HTML-inhoud die vanaf de URL wordt geladen en presenteert vervolgens de pagina aan de gebruiker.

Het **ContentDefinitions** -element bevat URL'S naar HTML5-sjablonen die kunnen worden gebruikt in de reis van een gebruiker. De HTML5-pagina-URI wordt gebruikt voor een opgegeven gebruikers interface stap. Bijvoorbeeld het aanmelden of aanmelden, het opnieuw instellen van het wacht woord of de fout pagina's. U kunt het uiterlijk wijzigen door de LoadUri voor het HTML5-bestand te overschrijven. U kunt nieuwe inhouds definities maken op basis van uw behoeften. Dit element kan een gelokaliseerde bronnen verwijzing bevatten naar de lokalisatie-id die is opgegeven in het [lokalisatie](localization.md) -element.

In het volgende voor beeld ziet u de definitie-id van de inhoud en de definitie van gelokaliseerde bronnen:

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

De meta gegevens van het zelfondertekende technische profiel van **LocalAccountSignUpWithLogonEmail** bevatten de inhouds definitie-id **ContentDefinitionReferenceId** ingesteld op `api.localaccountsignup`

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

Het element **ContentDefinition** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id voor een inhouds definitie. De waarde is één opgegeven in de sectie **inhouds definitie-id's** verderop op deze pagina. |

Het **ContentDefinition** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Een teken reeks die de URL van de HTML5-pagina voor de inhouds definitie bevat. |
| RecoveryUri | 1:1 | Een teken reeks die de URL van de HTML-pagina bevat voor het weer geven van een fout met betrekking tot de inhouds definitie. De waarde moet momenteel niet worden gebruikt `~/common/default_page_error.html` . |
| DataUri | 1:1 | Een teken reeks die de relatieve URL bevat van een HTML-bestand dat de gebruikers ervaring biedt voor het aanroepen van de stap. |
| Metagegevens | 0:1 | Een verzameling sleutel-waardeparen die de meta gegevens bevat die worden gebruikt door de inhouds definitie. |
| LocalizedResourcesReferences | 0:1 | Een verzameling gelokaliseerde bronnen verwijzingen. Gebruik dit element om de lokalisatie van een gebruikers interface en claims-kenmerk aan te passen. |

### <a name="datauri"></a>DataUri

Het element **DataUri** wordt gebruikt om de pagina-id op te geven. Azure AD B2C gebruikt de pagina-id om UI-elementen en Java script aan de client zijde te laden en te initiëren. De notatie van de waarde is `urn:com:microsoft:aad:b2c:elements:page-name:version` . De volgende tabel geeft een lijst van de pagina-id's die u kunt gebruiken.

| Pagina-id | Beschrijving |
| ----- | ----------- |
| `globalexception` | Hiermee wordt een fout pagina weer gegeven wanneer een uitzonde ring of een fout wordt aangetroffen. |
| `providerselection`, `idpselection` | Een lijst met de id-providers waaruit gebruikers kunnen kiezen tijdens het aanmelden.  |
| `unifiedssp` | Geeft een formulier weer voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Deze waarde biedt ook de functie ' aanmeld functionaliteit blijven ' en ' uw wacht woord verg eten? ' . |
| `unifiedssd` | Geeft een formulier weer voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. |
| `multifactor` | Hiermee worden telefoon nummers gecontroleerd met behulp van tekst of spraak tijdens het registreren of aanmelden. |
| `selfasserted` | Hiermee wordt een formulier weer gegeven voor het verzamelen van gegevens van een gebruiker. Hiermee kunnen gebruikers bijvoorbeeld hun profiel maken of bijwerken. |

### <a name="select-a-page-layout"></a>Selecteer een pagina-indeling

U kunt [Java script-client-side code](javascript-and-page-layout.md) inschakelen door `contract` tussen `elements` en het pagina Type in te voegen. Bijvoorbeeld `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Het [versie](page-layout.md) gedeelte van de `DataUri` specificeert het pakket inhoud met HTML, CSS en Java script voor de elementen van de gebruikers interface in uw beleid. Als u van plan bent om Java script-client code in te scha kelen, moeten de elementen waarop u uw Java script baseert, onveranderbaar zijn. Als ze niet onveranderbaar zijn, kunnen wijzigingen op de pagina's van uw gebruikers onverwacht gedrag veroorzaken. Als u deze problemen wilt voor komen, dwingt u het gebruik van een pagina-indeling af en geeft u een versie van de pagina-indeling op. Zo zorgt u ervoor dat alle inhouds definities die u op uw Java script hebt gebaseerd, onveranderbaar zijn. Zelfs als u Java script niet wilt inschakelen, moet u nog steeds de versie van de pagina-indeling opgeven voor uw pagina's.

In het volgende voor beeld ziet u de **DataUri** - `selfasserted` versie `1.2.0` :

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migreren naar pagina-indeling

De notatie van de waarde moet het woord `contract` : _urn: com: micro soft: AAD: B2C: elementen:**contract**:p Age-name: Version_ bevatten. Als u een pagina-indeling wilt opgeven in uw aangepaste beleids regels die gebruikmaken van een oude **DataUri** -waarde, gebruikt u de volgende tabel om te migreren naar de nieuwe indeling.

| Oude DataUri-waarde | Nieuwe DataUri-waarde |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |

In het volgende voor beeld ziet u de inhouds definitie-id's en de bijbehorende **DataUri** met de nieuwste versie van de pagina: 

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>Metagegevens

Een **META** gegevenselement bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Item | 0: n | De meta gegevens die betrekking hebben op de inhouds definitie. |

Het element **item** van het **META** gegevenselement bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Sleutel | Ja | De meta gegevens sleutel.  |

#### <a name="metadata-keys"></a>Meta gegevens sleutels

De inhouds definitie ondersteunt de volgende meta gegevens items:

| Sleutel | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| DisplayName | Nee | Een teken reeks die de naam van de inhouds definitie bevat. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

Het **LocalizedResourcesReferences** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | Een lijst met gelokaliseerde resource verwijzingen voor de inhouds definitie. |

Het **LocalizedResourcesReference** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Taal | Ja | Een teken reeks die een ondersteunde taal voor het beleid bevat per RFC 5646-Tags voor het identificeren van talen. |
| LocalizedResourcesReferenceId | Ja | De id van het **LocalizedResources** -element. |

In het volgende voor beeld wordt een registratie-of aanmeldings definitie weer gegeven met een verwijzing naar lokalisatie voor Engels, Frans en Spaans:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Zie [lokalisatie](localization.md)voor meer informatie over het toevoegen van lokalisatie ondersteuning aan uw inhouds definities.

## <a name="content-definition-ids"></a>Inhouds definitie-Id's

Het kenmerk ID van het element **ContentDefinition** geeft het type pagina aan dat is gekoppeld aan de inhouds definitie. Het element definieert de context waarin een aangepaste HTML5/CSS-sjabloon wordt toegepast. De volgende tabel beschrijft de set met inhouds definitie-Id's die worden herkend door het Framework voor identiteits ervaring en de pagina typen die aan hen zijn gerelateerd. U kunt uw eigen inhouds definities met een wille keurige ID maken.

| Id | Standaard sjabloon | Beschrijving |
| -- | ---------------- | ----------- |
| **API. error** | [uitzonde ring. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Fout pagina** : er wordt een fout pagina weer gegeven wanneer een uitzonde ring of een fout wordt aangetroffen. |
| **API. idpselections** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | Pagina voor het selecteren van de **identiteits provider** : geeft een lijst van id-providers waaruit gebruikers kunnen kiezen tijdens het aanmelden. De opties zijn doorgaans ondernemings-id-providers, sociale id-providers zoals Facebook en Google + of lokale accounts. |
| **API. idpselections. signup** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Selectie van ID-provider voor registratie** van id-providers die gebruikers kunnen kiezen tijdens het aanmelden. De opties zijn doorgaans ondernemings-id-providers, sociale id-providers zoals Facebook en Google + of lokale accounts. |
| **API. localaccountpasswordreset** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina verg eten wacht woord** : hier wordt een formulier weer gegeven dat gebruikers moeten volt ooien om het opnieuw instellen van een wacht woord te initiëren. |
| **API. localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | De **aanmeldings pagina van een lokaal account** : geeft een formulier weer voor het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan een tekstvak voor tekst invoer en wacht woord bevatten. |
| **API. localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | De pagina voor het registreren van een **lokaal account** : hier wordt een formulier weer gegeven voor het aanmelden voor een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, een keuze rondje, vervolg keuze vakjes en meervoudige selectie vakjes. |
| **API. Phone factor** | [multifactor-1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-factor Authentication-pagina** : verifieert telefoon nummers, met behulp van tekst of spraak tijdens het registreren of aanmelden. |
| **API. selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Aanmeldings pagina voor sociaal account** : Hiermee wordt een formulier weer gegeven dat gebruikers moeten volt ooien wanneer ze zich aanmelden met een bestaand account van een sociale ID-provider. Deze pagina is vergelijkbaar met de volgende aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor wacht woord invoer. |
| **API. selfasserted. profileupdate** | [updateprofile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina Profiel bijwerken** : hier wordt een formulier weer gegeven dat gebruikers kunnen gebruiken om hun profiel bij te werken. Deze pagina is vergelijkbaar met de registratie pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| **API. signuporsignin** | [Unified. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Unified Sign-up-of aanmeldings pagina** : Hiermee wordt het aanmelden en aanmelden door de gebruiker verwerkt. Gebruikers kunnen ondernemings-id-providers, sociale id-providers, zoals Facebook of Google + of lokale accounts gebruiken. |

## <a name="next-steps"></a>Volgende stappen

Zie voor een voor beeld van het aanpassen van de gebruikers interface met behulp van inhouds definities:

[De gebruikers interface van uw toepassing aanpassen met behulp van een aangepast beleid](customize-ui-with-html.md)
