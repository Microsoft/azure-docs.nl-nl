---
title: Verloopdatum instellen Microsoft 365 groepen - Azure Active Directory | Microsoft Docs
description: Verloopdatum instellen voor Microsoft 365 groepen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8af1a5e73592dc1c3392f0bc1fecfe6139a54710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529835"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Het verloopbeleid configureren voor Microsoft 365 groepen

In dit artikel wordt beschreven hoe u de levenscyclus van Microsoft 365 groepen beheert door een verloopbeleid voor deze groepen in te stellen. U kunt verloopbeleid alleen instellen voor Microsoft 365 groepen in Azure Active Directory (Azure AD).

Nadat u een groep hebt ingesteld op verlopen:

- Groepen met gebruikersactiviteiten worden automatisch vernieuwd zodra de verloopdatum nadert.
- Eigenaren van de groep krijgen een melding om de groep te vernieuwen als de groep niet automatisch wordt vernieuwd.
- Een groep die niet wordt vernieuwd, wordt verwijderd.
- Elke Microsoft 365 die wordt verwijderd, kan binnen 30 dagen door de groepseigenaren of de beheerder worden hersteld.

Op dit moment kan slechts één verloopbeleid worden geconfigureerd voor alle Microsoft 365 groepen in een Azure AD-organisatie.

> [!NOTE]
> Voor het configureren en gebruiken van het verloopbeleid voor Microsoft 365-groepen moet u beschikken over Azure AD Premium-licenties, maar niet per se toewijzen aan de leden van alle groepen waarop het verloopbeleid wordt toegepast.

Zie PowerShell voor [Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)voor meer informatie over het downloaden en installeren van de Azure AD PowerShell-cmd Azure Active Directory lets.

## <a name="activity-based-automatic-renewal"></a>Automatische verlenging op basis van activiteit

Met Azure AD Intelligence worden groepen nu automatisch vernieuwd op basis van of ze onlangs zijn gebruikt. Deze functie elimineert de noodzaak van handmatige actie door groepseigenaren, omdat deze is gebaseerd op gebruikersactiviteit in groepen in Microsoft 365 services zoals Outlook, SharePoint of Teams. Als een eigenaar of groepslid bijvoorbeeld een document in SharePoint uploadt, een Teams-kanaal bezoekt of een e-mail naar de groep verzendt in Outlook, wordt de groep ongeveer 35 dagen voordat de groep verloopt automatisch vernieuwd en ontvangt de eigenaar geen verlengingsmeldingen.

### <a name="activities-that-automatically-renew-group-expiration"></a>Activiteiten die groepsverloop automatisch verlengen

De volgende gebruikersacties veroorzaken automatische groepsvernieuwing:

- SharePoint: bestanden weergeven, bewerken, downloaden, verplaatsen, delen of uploaden
- Outlook: groep lid maken, groepsbericht lezen/schrijven vanuit groepsruimte, zoals een bericht (in Outlook Web Access)
- Teams: Een Teams-kanaal bezoeken

### <a name="auditing-and-reporting"></a>Controle en rapportage

Beheerders kunnen een lijst met automatisch vernieuwde groepen op halen uit de activiteitencontrolelogboeken in Azure AD.

![Automatische verlenging van groepen op basis van activiteit](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Rollen en machtigingen

Hieronder vindt u rollen die een verloopdatum kunnen configureren en gebruiken Microsoft 365 groepen in Azure AD.

Rol | Machtigingen
-------- | --------
Globale beheerder, Groepsbeheerder of Gebruikersbeheerder | U kunt de instellingen voor het verloopbeleid voor Microsoft 365 groepen maken, lezen, bijwerken of verwijderen<br>Kan elke Microsoft 365 vernieuwen
Gebruiker | Kan een groep Microsoft 365 van hen vernieuwen<br>Kan een Microsoft 365 herstellen die ze zelf hebben<br>Kan de instellingen voor het verloopbeleid lezen

Zie Restore a deleted Microsoft 365 group in Azure Active Directory (Een verwijderde groep herstellen in Azure Active Directory) voor meer informatie over [machtigingen voor het herstellen van een verwijderde Microsoft 365.](groups-restore-deleted.md)

## <a name="set-group-expiration"></a>Verloopdatum van de groep instellen

1. Open het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een account dat een globale beheerder is in uw Azure AD-organisatie.

2. Selecteer **Groepen** en selecteer vervolgens **Vervaldatum om** de verloopinstellingen te openen.
  
   ![Verloopinstellingen voor groepen](./media/groups-lifecycle/expiration-settings.png)

3. Op de **pagina Vervaldatum** kunt u het volgende doen:

    - Stel de levensduur van de groep in dagen in. U kunt een van de vooraf ingestelde waarden of een aangepaste waarde selecteren (moet 30 dagen of meer zijn).
    - Geef een e-mailadres op waar de meldingen over verlenging en verloop moeten worden verzonden wanneer een groep geen eigenaar heeft.
    - Selecteer welke Microsoft 365 verlopen. U kunt de vervaldatum instellen voor:
      - **Alle** Microsoft 365 groepen
      - Een lijst met **geselecteerde** Microsoft 365 groepen
      - **Geen om** verloop voor alle groepen te beperken
    - Sla uw instellingen op wanneer u klaar bent door **Opslaan te selecteren.**

> [!NOTE]
> - Wanneer u voor het eerst een verloopdatum in stelt, worden groepen die ouder zijn dan het verloopinterval ingesteld op 35 dagen tot de vervaldatum, tenzij de groep automatisch wordt vernieuwd of de eigenaar deze vernieuwt.
> - Wanneer een dynamische groep wordt verwijderd en hersteld, wordt deze gezien als een nieuwe groep en opnieuw ingevuld volgens de regel. Dit proces kan tot 24 uur duren.
> - Verloopbericht voor groepen die in Teams worden gebruikt, wordt weergegeven in de feed Teams-eigenaren.
> - Wanneer u verloop voor geselecteerde groepen inschakelen, kunt u maximaal 500 groepen aan de lijst toevoegen. Als u meer dan 500 groepen moet toevoegen, kunt u verloop voor al uw groepen inschakelen. In dat scenario is de beperking van 500 groepen niet van toepassing.

## <a name="email-notifications"></a>E-mailmeldingen

Als groepen niet automatisch worden vernieuwd, worden e-mailmeldingen zoals deze 30 dagen, 15 dagen en 1 dag vóór de vervaldatum van de groep verzonden naar de Microsoft 365-groepseigenaren. De taal van het e-mailbericht wordt bepaald door de voorkeurstaal van de eigenaar van de groep of de taalinstelling van Azure AD. Als de groepseigenaar een voorkeurstaal heeft gedefinieerd of meerdere eigenaren dezelfde voorkeurstaal hebben, wordt die taal gebruikt. In alle andere gevallen wordt de taalinstelling van Azure AD gebruikt.

![E-mailmeldingen verlopen](./media/groups-lifecycle/expiration-notification.png)

Vanuit de **e-mailmelding** Voor groep vernieuwen hebben groepseigenaren rechtstreeks toegang tot de pagina met groepsdetails in [Toegangsvenster](https://account.activedirectory.windowsazure.com/r#/applications). Daar kunnen de gebruikers meer informatie krijgen over de groep, zoals de beschrijving, wanneer deze voor het laatst is vernieuwd, wanneer deze verloopt en de mogelijkheid om de groep te vernieuwen. De pagina met groepsdetails bevat nu ook koppelingen naar de resources Microsoft 365 groep, zodat de groepseigenaar de inhoud en activiteit in de groep gemakkelijk kan bekijken.

Wanneer een groep verloopt, wordt de groep één dag na de vervaldatum verwijderd. Er wordt een e-mailmelding zoals deze verzonden naar de Microsoft 365 van de groep met informatie over de vervaldatum en de daaropvolgende verwijdering van Microsoft 365 groep.

![E-mailmeldingen over groeps verwijderen](./media/groups-lifecycle/deletion-notification.png)

De groep kan binnen 30 dagen na  de verwijdering worden hersteld door Groep herstellen te selecteren of door PowerShell-cmdlets te gebruiken, zoals beschreven in Een verwijderde [Microsoft 365-groep herstellen in Azure Active Directory](groups-restore-deleted.md). Houd er rekening mee dat de herstelperiode van 30 dagen voor de groep niet kan worden aangepast.

Als de groep die u herstelt documenten, SharePoint-sites of andere permanente objecten bevat, kan het tot 24 uur duren voordat de groep en de inhoud ervan volledig zijn hersteld.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Vervaldatum van Microsoft 365 groep ophalen

Naast de Toegangsvenster waar gebruikers groepsdetails kunnen bekijken, waaronder de vervaldatum en de laatste verlengde datum, kan de vervaldatum van een Microsoft 365-groep worden opgehaald uit Microsoft Graph REST API Beta. expirationDateTime als groeps-eigenschap is ingeschakeld in Microsoft Graph Beta. Deze kan worden opgehaald met een GET-aanvraag. Raadpleeg dit voorbeeld voor [meer informatie.](/graph/api/group-get?view=graph-rest-beta#example&preserve-view=true)

> [!NOTE]
> Als u groepslidmaatschap wilt beheren op Toegangsvenster, moet 'Toegang tot groepen beperken in Toegangsvenster' worden ingesteld op Nee in Azure Active Directory Algemene instelling voor groepen.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Hoe Microsoft 365 groepsverloop werkt met een postvak in juridische wacht

Wanneer een groep verloopt en wordt verwijderd, worden de gegevens van de groep 30 dagen na het verwijderen definitief verwijderd uit apps zoals Planner, Sites of Teams, maar het groepspostvak dat in juridische stand is en niet permanent wordt verwijderd. De beheerder kan Exchange-cmdlets gebruiken om het postvak te herstellen om de gegevens op te halen.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Hoe Microsoft 365 verlopen van groepen werkt met bewaarbeleid

Het retentiebeleid wordt geconfigureerd via het Beveiligings- en compliancecentrum. Als u een bewaarbeleid voor Microsoft 365-groepen hebt ingesteld en een groep verloopt en wordt verwijderd, worden de groepsgesprekken in het groepspostvak en de bestanden in de groepssite bewaard in de bewaarcontainer voor het specifieke aantal dagen dat is gedefinieerd in het bewaarbeleid. Gebruikers zien de groep of de inhoud ervan niet na het verlopen, maar kunnen de site- en postvakgegevens herstellen via e-discovery.

## <a name="powershell-examples"></a>PowerShell-voorbeelden

Hier zijn voorbeelden van hoe u PowerShell-cmdlets kunt gebruiken voor het configureren van de verloopinstellingen voor Microsoft 365 groepen in uw Azure AD-organisatie:

1. Installeer de PowerShell v2.0-module en meld u aan bij de PowerShell-prompt:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. De verloopinstellingen configureren Gebruik de cmdlet New-AzureADMSGroupLifecyclePolicy om de levensduur voor alle Microsoft 365-groepen in de Azure AD-organisatie in te stellen op 365 dagen. Vernieuwingsmeldingen voor Microsoft 365 zonder eigenaars worden verzonden naar emailaddress@contoso.com ' '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Haal het bestaande beleid Get-AzureADMSGroupLifecyclePolicy op: met deze cmdlet worden de huidige instellingen voor het verlopen Microsoft 365 de groep opgehaald die zijn geconfigureerd. In dit voorbeeld ziet u:

   - De beleids-id
   - De levensduur voor alle Microsoft 365 groepen in de Azure AD-organisatie is ingesteld op 365 dagen
   - Vernieuwingsmeldingen voor Microsoft 365 groepen zonder eigenaars worden verzonden naar emailaddress@contoso.com '.'
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Werk het bestaande beleid Set-AzureADMSGroupLifecyclePolicy bij: Deze cmdlet wordt gebruikt om een bestaand beleid bij te werken. In het onderstaande voorbeeld wordt de levensduur van de groep in het bestaande beleid gewijzigd van 365 dagen in 180 dagen.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Voeg specifieke groepen toe aan het beleid Add-AzureADMSLifecyclePolicyGroup: met deze cmdlet wordt een groep toegevoegd aan het levenscyclusbeleid. Bijvoorbeeld:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Verwijder de bestaande beleidsinstellingen Remove-AzureADMSGroupLifecyclePolicy: met deze cmdlet worden de instellingen voor het verlopen Microsoft 365 groep verwijderd, maar is de beleids-id vereist. Met deze cmdlet wordt de vervaldatum voor Microsoft 365 uitgeschakeld.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
De volgende cmdlets kunnen worden gebruikt om het beleid gedetailleerder te configureren. Zie [PowerShell-documentatie voor meer informatie.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups)

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure AD-groepen.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Leden van een groep beheren](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
