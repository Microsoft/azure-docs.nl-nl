---
title: Een huis stijl toevoegen aan de aanmeldings pagina van uw organisatie
titleSuffix: Azure AD B2C
description: Meer informatie over hoe u de huis stijl van uw organisatie kunt toevoegen aan de Azure Active Directory B2C pagina's.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111301"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Een huis stijl toevoegen aan de Azure Active Directory B2C pagina's van uw organisatie

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

U kunt uw Azure AD B2C pagina's met een banner logo, achtergrond afbeelding en achtergrond kleur aanpassen met behulp van Azure Active Directory [bedrijfs huisstijl](../active-directory/fundamentals/customize-branding.md). De huis stijl van het bedrijf omvat het aanmelden, aanmelden, het bewerken van het profiel en het opnieuw instellen van het wacht woord. 

> [!TIP]
> Zie How to [Customize the UI with HTML Temp late](customize-ui-with-html.md)(Engelstalig) voor meer informatie over het aanpassen van andere aspecten van uw gebruikers stroom pagina's buiten het vaandel logo, de achtergrond afbeelding of de achtergrond kleur.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Als u de pagina's van uw gebruikers stroom wilt aanpassen, moet u eerst de huis stijl van het bedrijf in Azure Active Directory configureren. vervolgens schakelt u deze in op de pagina-indelingen van uw gebruikers stromen in Azure AD B2C.

## <a name="configure-company-branding"></a>Bedrijfshuisstijl configureren

Begin met het instellen van het banner logo, de achtergrond afbeelding en de achtergrond kleur in de **bedrijfs huisstijl**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **bedrijfs huisstijl** onder **beheren**.
1. Volg de stappen in [huis stijl toevoegen aan de aanmeldings pagina van uw organisatie Azure Active Directory](../active-directory/fundamentals/customize-branding.md).

Houd bij het configureren van de huis stijl van het bedrijf in Azure AD B2C de volgende zaken:

* De huis stijl van het bedrijf in Azure AD B2C is momenteel beperkt tot de **achtergrond afbeelding**, het **vaandel logo** en de aanpassing van de **achtergrond kleur** . De andere eigenschappen in het deel venster huis stijl van het bedrijf, bijvoorbeeld in **Geavanceerde instellingen**, worden *niet ondersteund*.
* Op de pagina's van de gebruikers stroom wordt de achtergrond kleur weer gegeven voordat de achtergrond afbeelding wordt geladen. We raden u aan een achtergrond kleur te kiezen die nauw keurig overeenkomt met de kleuren in de achtergrond afbeelding voor een probleemloze laad ervaring.
* Het banner logo wordt weer gegeven in de verificatie-e-mail berichten die worden verzonden naar uw gebruikers wanneer ze een registratie gebruikers stroom initiëren.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Huis stijl inschakelen op pagina's van gebruikers stroom

Zodra u de huis stijl van uw bedrijf hebt geconfigureerd, schakelt u deze in uw gebruikers stromen in.

1. Selecteer **Azure AD B2C** in het menu links van de Azure Portal.
1. Selecteer onder **beleids regels** **gebruikers stromen (beleid)**.
1. Selecteer de gebruikers stroom waarvoor u de huis stijl van het bedrijf wilt inschakelen. De huis stijl van het bedrijf wordt **niet ondersteund** voor de standaard *aanmeldings* en standaard *profiel bewerkings* typen.
1. Onder **aanpassen** selecteert u **pagina-indelingen** en selecteert u vervolgens de lay-out die u wilt toevoegen. Selecteer bijvoorbeeld **Unified logging of aanmeldings pagina**.
1. Kies voor de versie van de **pagina-indeling (preview)** versie **1.2.0** of hoger.
1. Selecteer **Opslaan**.

Als u alle pagina's in de gebruikers stroom wilt gaan, stelt u de versie van de pagina-indeling in voor elke pagina-indeling in de gebruikers stroom.

![De selectie van de pagina-indeling in Azure AD B2C in het Azure Portal](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Selecteer een pagina-indeling

Als u de huis stijl van het bedrijf wilt inschakelen, moet u [een versie van de pagina-indeling definiëren](contentdefinitions.md#migrating-to-page-layout) met `contract` de pagina versie voor *alle* inhouds definities in uw aangepaste beleid. De notatie van de waarde moet het woord `contract` : _urn: com: micro soft: AAD: B2C: elementen:**contract**:p Age-name: Version_ bevatten. Een pagina-indeling opgeven in uw aangepaste beleids regels die gebruikmaken van een oude waarde voor **DataUri** .

Meer informatie over het [migreren naar de pagina-indeling](contentdefinitions.md#migrating-to-page-layout) met de pagina versie.

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

In het volgende voor beeld ziet u een aangepast banner logo en een achtergrond afbeelding op een pagina voor *Aanmelden en aanmelden in* de gebruikers stroom die gebruikmaakt van de sjabloon blauw:

![Aanmeld-en aanmeldings pagina die wordt aangeboden door Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Bedrijfs huisstijl assets gebruiken in aangepaste HTML

Als u de huismerk activa van uw bedrijf in [aangepaste HTML](customize-ui-with-html.md)wilt gebruiken, voegt u de volgende tags toe buiten de `<div id="api">` Tag:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

De afbeeldings bron wordt vervangen door de achtergrond afbeelding en het logo banner.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de gebruikers interface van uw toepassingen kunt aanpassen in [het aanpassen van de gebruikers interface van uw toepassing in azure Active Directory B2C](customize-ui-with-html.md).