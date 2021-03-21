---
title: De gebruikersinterface aanpassen
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de gebruikers interface voor uw toepassingen die gebruikmaken van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055734"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>De gebruikers interface in Azure Active Directory B2C aanpassen

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

De gebruikers interface die Azure Active Directory B2C (Azure AD B2C) aan uw klanten wordt weer gegeven, kunt u voorzien van een naadloze gebruikers ervaring in uw toepassing. Deze ervaring omvat het aanmelden, aanmelden, het bewerken van profielen en het opnieuw instellen van wacht woorden. In dit artikel kunt u uw Azure AD B2C-pagina's aanpassen met behulp van de pagina sjabloon en de huis stijl van het bedrijf. 

> [!TIP]
> Zie How to [Customize the UI with HTML Temp late](customize-ui-with-html.md)(Engelstalig) voor meer informatie over het aanpassen van andere aspecten van uw gebruikers stroom pagina's buiten de pagina sjabloon, banner logo, achtergrond afbeelding of achtergrond kleur.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Overzicht

Azure AD B2C bieden verschillende ingebouwde sjablonen waaruit u kunt kiezen om uw gebruikers ervaring-pagina's een professioneel uiterlijk te geven. Deze pagina sjablonen kunnen ook worden gebruikt als uitgangs punt voor uw eigen aanpassing, met behulp van de [bedrijfs huisstijl](#company-branding) functie.

### <a name="ocean-blue"></a>Oceaan blauw

Voor beeld van de sjabloon oceaan blauw die wordt weer gegeven op de aanmeldings pagina voor aanmelden:

![Scherm afbeelding van oceaan blauw sjabloon](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Pastel grijs

Voor beeld van de sjabloon pastel grijs weer gegeven op de aanmeldings pagina voor aanmelden:

![Afbeelding van pastel grijze sjabloon](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Klassiek

Voor beeld van de klassieke sjabloon die wordt weer gegeven op de aanmeldings pagina voor aanmelden:

![Scherm afbeelding van klassieke sjabloon](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Aangepaste huisstijl

U kunt uw Azure AD B2C pagina's met een banner logo, achtergrond afbeelding en achtergrond kleur aanpassen met behulp van Azure Active Directory [bedrijfs huisstijl](../active-directory/fundamentals/customize-branding.md). De huis stijl van het bedrijf omvat het aanmelden, aanmelden, het bewerken van het profiel en het opnieuw instellen van het wacht woord. 

In het volgende voor beeld ziet u een *aanmeldings-en aanmeldings* pagina met een aangepast logo, een achtergrond afbeelding met de sjabloon blauw:

![Aanmeld-en aanmeldings pagina die wordt aangeboden door Azure AD B2C](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Een pagina sjabloon selecteren

::: zone pivot="b2c-user-flow"

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **gebruikers stromen**.
1. Selecteer een gebruikers stroom die u wilt aanpassen.
1. Onder **aanpassen** in het menu links selecteert u **pagina-indelingen** en selecteert u vervolgens een **sjabloon**.
    ![Vervolg keuzelijst sjabloon selectie in de pagina gebruikers stroom van Azure Portal](./media/customize-ui/select-page-template.png)

Wanneer u een sjabloon kiest, wordt de geselecteerde sjabloon toegepast op alle pagina's in uw gebruikers stroom. De URI voor elke pagina wordt weer gegeven in het veld **aangepaste pagina-URI** .

::: zone-end

::: zone pivot="b2c-custom-policy"

Als u een pagina sjabloon wilt selecteren, stelt u het `LoadUri` element van de [inhouds definities](contentdefinitions.md)in. In het volgende voor beeld ziet u de inhouds definitie-id's en de bijbehorende `LoadUri` . 

Oceaan blauw:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Pastel grijs:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Klassieke 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Bedrijfshuisstijl configureren

Als u de pagina's van uw gebruikers stroom wilt aanpassen, moet u eerst de huis stijl van het bedrijf in Azure Active Directory configureren. vervolgens schakelt u deze in uw gebruikers stromen in Azure AD B2C.

Begin met het instellen van het banner logo, de achtergrond afbeelding en de achtergrond kleur in de **bedrijfs huisstijl**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **bedrijfs huisstijl** onder **beheren**.
1. Volg de stappen in [huis stijl toevoegen aan de aanmeldings pagina van uw organisatie Azure Active Directory](../active-directory/fundamentals/customize-branding.md).

Houd bij het configureren van de huis stijl van het bedrijf in Azure AD B2C de volgende zaken:

* De huis stijl van het bedrijf in Azure AD B2C is momenteel beperkt tot de **achtergrond afbeelding**, het **vaandel logo** en de aanpassing van de **achtergrond kleur** . De andere eigenschappen in het deel venster huis stijl van bedrijf, bijvoorbeeld **Geavanceerde instellingen**, worden *niet ondersteund*.
* Op de pagina's van de gebruikers stroom wordt de achtergrond kleur weer gegeven voordat de achtergrond afbeelding wordt geladen. We raden u aan een achtergrond kleur te kiezen die nauw keurig overeenkomt met de kleuren in de achtergrond afbeelding voor een probleemloze laad ervaring.
* Het banner logo wordt weer gegeven in de verificatie-e-mail berichten die worden verzonden naar uw gebruikers wanneer ze een registratie gebruikers stroom initiëren.



## <a name="enable-company-branding-in-user-flow-pages"></a>Bedrijfs huisstijl inschakelen op pagina's van de gebruikers stroom

::: zone pivot="b2c-user-flow"

Zodra u de huis stijl van uw bedrijf hebt geconfigureerd, schakelt u deze in uw gebruikers stromen in.

1. Selecteer **Azure AD B2C** in het menu links van de Azure Portal.
1. Selecteer onder **beleids regels** **gebruikers stromen (beleid)**.
1. Selecteer de gebruikers stroom waarvoor u de huis stijl van het bedrijf wilt inschakelen. De huis stijl van het bedrijf wordt **niet ondersteund** voor de standaard *aanmeldings* en standaard *profiel bewerkings* typen.
1. Onder **aanpassen** selecteert u **pagina-indelingen** en selecteert u vervolgens de pagina die u wilt voorzien van een huis stijl. Selecteer bijvoorbeeld **Unified logging of aanmeldings pagina**.
1. Kies voor de versie van de **pagina-indeling (preview)** versie **1.2.0** of hoger.
1. Selecteer **Opslaan**.

Als u alle pagina's in de gebruikers stroom wilt gaan, stelt u de versie van de pagina-indeling in voor elke pagina-indeling in de gebruikers stroom.

![De selectie van de pagina-indeling in Azure AD B2C in het Azure Portal](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Zodra u de huis stijl van het bedrijf hebt geconfigureerd, schakelt u deze in uw aangepaste beleid in. Configureer de [versie](contentdefinitions.md#migrating-to-page-layout) van de pagina-indeling met de pagina `contract` versie voor *alle* inhouds definities in uw aangepaste beleid. De notatie van de waarde moet het woord `contract` : _urn: com: micro soft: AAD: B2C: elementen:**contract**:p Age-name: Version_ bevatten. Een pagina-indeling opgeven in uw aangepaste beleids regels die gebruikmaken van een oude waarde voor **DataUri** . Meer informatie over hoe u kunt [migreren naar de pagina-indeling](contentdefinitions.md#migrating-to-page-layout) met de pagina versie.

In het volgende voor beeld ziet u de inhouds definities met hun bijbehorende pagina contract en *oceaan blauw* pagina sjabloon: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de gebruikers interface van uw toepassingen kunt aanpassen in [het aanpassen van de gebruikers interface van uw toepassing in azure Active Directory B2C](customize-ui-with-html.md).
