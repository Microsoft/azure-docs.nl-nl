---
title: Verval datum instellen voor Microsoft 365 groepen-Azure Active Directory | Microsoft Docs
description: Verval datum instellen voor Microsoft 365 groepen in Azure Active Directory
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
ms.openlocfilehash: 34ac4a3b7f55c42711e4bc33c75bba964b49c65e
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551625"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Het verloop beleid voor Microsoft 365 groepen configureren

In dit artikel leest u hoe u de levens cyclus van Microsoft 365 groepen kunt beheren door een verloop beleid in te stellen. U kunt het verloop beleid alleen instellen voor Microsoft 365 groepen in Azure Active Directory (Azure AD).

Zodra u een groep hebt ingesteld op verlopen:

- Groepen met gebruikers activiteiten worden automatisch vernieuwd als de verval datum bijna is bereikt.
- Eigen aren van de groep worden gewaarschuwd om de groep te vernieuwen als de groep niet automatisch wordt vernieuwd.
- Alle groepen die niet worden vernieuwd, worden verwijderd.
- Elke Microsoft 365 groep die wordt verwijderd, kan binnen 30 dagen worden hersteld door de groeps eigenaren of de beheerder.

Op dit moment kan slechts één verloop beleid worden geconfigureerd voor alle Microsoft 365 groepen in een Azure AD-organisatie.

> [!NOTE]
> Het configureren en gebruiken van het verloop beleid voor Microsoft 365 groepen vereist dat u niet noodzakelijkerwijs Azure AD Premium licenties toewijst voor de leden van alle groepen waarop het verloop beleid wordt toegepast.

Zie [Azure Active Directory Power shell for graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)voor meer informatie over het downloaden en installeren van de Azure AD Power shell-cmdlets.

## <a name="activity-based-automatic-renewal"></a>Automatische verlenging op basis van activiteit

Met Azure AD Intelligence worden groepen nu automatisch vernieuwd op basis van het feit of ze onlangs zijn gebruikt. Deze functie elimineert de nood zaak voor hand matige actie door groeps eigenaren, omdat deze is gebaseerd op gebruikers activiteit in groepen tussen Microsoft 365 services zoals Outlook, share point of teams. Als een eigenaar of een groepslid bijvoorbeeld een document uploadt in share point, gaat u naar een teams kanaal of stuurt u een e-mail naar de groep in Outlook, wordt de groep automatisch vernieuwd rond 35 dagen voordat de groep verloopt en ontvangt de eigenaar geen verlengings meldingen.

### <a name="activities-that-automatically-renew-group-expiration"></a>Activiteiten die het verlopen van groepen automatisch vernieuwen

De volgende gebruikers acties veroorzaken automatische groeps verlenging:

- Share point: bestanden weer geven, bewerken, downloaden, verplaatsen, delen of uploaden
- Outlook: lid worden van groep, bericht van groep lezen/schrijven van groeps ruimte, zoals een bericht (in Outlook Web Access)
- Teams: een teams-kanaal bezoeken

### <a name="auditing-and-reporting"></a>Controle en rapportage

Beheerders kunnen een lijst met automatisch hernieuwde groepen ophalen uit de audit logboeken voor activiteiten in azure AD.

![Automatisch verlengen van groepen op basis van activiteit](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Rollen en machtigingen

Hieronder vindt u rollen die het verlopen van Microsoft 365 groepen in azure AD kunnen configureren en gebruiken.

Rol | Machtigingen
-------- | --------
Globale beheerder, groeps beheerder of gebruikers beheerder | Kan de beleids instellingen voor verloop van Microsoft 365 groepen maken, lezen, bijwerken of verwijderen<br>Kan elke Microsoft 365 groep vernieuwen
Gebruiker | Kan een Microsoft 365 groep vernieuwen waarvan ze eigenaar zijn<br>Kan een Microsoft 365 groep herstellen waarvan ze eigenaar zijn<br>Kan de instellingen voor het verloop beleid lezen

Zie [een verwijderde Microsoft 365 groep herstellen in azure Active Directory](groups-restore-deleted.md)voor meer informatie over machtigingen voor het herstellen van een verwijderde groep.

## <a name="set-group-expiration"></a>Verloopdatum van de groep instellen

1. Open het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat een globale beheerder is in uw Azure AD-organisatie.

2. Selecteer **groepen** en selecteer vervolgens **verval datum** om de verloop instellingen te openen.
  
   ![Verloop instellingen voor groepen](./media/groups-lifecycle/expiration-settings.png)

3. Op de pagina **verloop** kunt u het volgende doen:

    - Stel de levens duur van de groep in op dagen. U kunt een van de vooraf ingestelde waarden selecteren, of een aangepaste waarde (30 dagen of langer).
    - Geef een e-mail adres op waarnaar meldingen over verlenging en verloop moeten worden verzonden wanneer een groep geen eigenaar heeft.
    - Selecteer welke Microsoft 365 groepen verlopen. U kunt de verval datum instellen voor:
      - **Alle** Microsoft 365 groepen
      - Een lijst met **geselecteerde** Microsoft 365 groepen
      - **Geen** om de verval datum voor alle groepen te beperken
    - Sla de instellingen op wanneer u klaar bent door **Opslaan** te selecteren.

> [!NOTE]
> - Wanneer u de verval datum voor het eerst instelt, worden de groepen die ouder zijn dan het verloop interval ingesteld op 35 dagen tot de verval datum, tenzij de groep automatisch wordt vernieuwd of de eigenaar deze vernieuwt.
> - Wanneer een dynamische groep wordt verwijderd en teruggezet, wordt deze weer gegeven als een nieuwe groep en op basis van de regel opnieuw ingevuld. Dit proces kan Maxi maal 24 uur duren.
> - Verloop meldingen voor groepen die worden gebruikt in teams, worden weer gegeven in de team eigen aren van teams.
> - Wanneer u de verval datum voor geselecteerde groepen inschakelt, kunt u Maxi maal 500 groepen toevoegen aan de lijst. Als u meer dan 500 groepen wilt toevoegen, kunt u de verval datum voor al uw groepen instellen. In dat scenario is de limiet van 500-groep niet van toepassing.

## <a name="email-notifications"></a>E-mailmeldingen

Als groepen niet automatisch worden vernieuwd, worden e-mail meldingen, zoals deze, 30 dagen, 15 dagen en 1 dag vóór de verval datum van de groep verzonden naar de groeps eigenaren van Microsoft 365. De taal van het e-mail bericht wordt bepaald door de voorkeurs taal van de eigenaar van de groep of de Azure AD-taal instelling. Als de groeps eigenaar een voorkeurs taal heeft gedefinieerd of meerdere eigen aren dezelfde Voorkeurs taal hebben, wordt die taal gebruikt. Voor alle andere gevallen wordt de Azure AD-taal instelling gebruikt.

![E-mail meldingen over verloop](./media/groups-lifecycle/expiration-notification.png)

Vanuit het e-mail adres **groep vernieuwen** kunnen groeps eigenaren rechtstreeks toegang krijgen tot de pagina groeps Details in het [toegangs venster](https://account.activedirectory.windowsazure.com/r#/applications). Daar kunnen de gebruikers meer informatie krijgen over de groep, zoals de beschrijving, wanneer deze voor het laatst werd vernieuwd, wanneer de app verloopt, en ook de mogelijkheid om de groep te vernieuwen. De pagina groeps gegevens bevat nu ook koppelingen naar de Microsoft 365 groeps resources, zodat de eigenaar van de groep de inhoud en activiteit in hun groep gemakkelijk kan bekijken.

Wanneer een groep verloopt, wordt de groep één dag na de verval datum verwijderd. Er wordt een e-mail bericht, zoals deze, verzonden naar de groeps eigenaren van Microsoft 365 om hen hiervan op de hoogte te brengen over het verlopen en later verwijderen van hun Microsoft 365 groep.

![E-mail meldingen over verwijderen van groep](./media/groups-lifecycle/deletion-notification.png)

De groep kan binnen 30 dagen na verwijdering worden hersteld door **groep herstellen** te selecteren of door Power shell-cmdlets te gebruiken, zoals beschreven in [een verwijderde Microsoft 365 groep herstellen in azure Active Directory](groups-restore-deleted.md). Houd er rekening mee dat de herstelperiode van 30 dagen voor de groep niet kan worden aangepast.

Als de groep die u wilt herstellen, documenten, share point-sites of andere permanente objecten bevat, kan het tot 24 uur duren om de groep en de inhoud ervan volledig te herstellen.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>De verval datum van Microsoft 365 groep ophalen

Naast het toegangs venster waarin gebruikers groeps gegevens kunnen bekijken, zoals de verval datum en de datum van laatste vernieuwing, kan de verval datum van een Microsoft 365 groep worden opgehaald uit Microsoft Graph REST API Beta. expirationDateTime als een groeps eigenschap is ingeschakeld in Microsoft Graph bèta. Het kan worden opgehaald met een GET-aanvraag. Raadpleeg [dit voor beeld](/graph/api/group-get?view=graph-rest-beta#example)voor meer informatie.

> [!NOTE]
> Als u groepslid maatschappen in het toegangs venster wilt beheren, moet u ' toegang tot groepen in het toegangs venster beperken ' op ' nee ' instellen in Azure Active Directory algemene instelling voor groepen.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Hoe de verval datum van Microsoft 365 groep werkt met een postvak in wettelijke bewaring

Wanneer een groep verloopt en wordt verwijderd, wordt 30 dagen na het verwijderen van de gegevens van de groep uit apps zoals planner, sites of teams permanent verwijderd, maar wordt het groeps postvak dat zich in de juridische bewaring bevindt, bewaard en wordt het niet definitief verwijderd. De beheerder kan Exchange-cmdlets gebruiken om het postvak te herstellen om de gegevens op te halen.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Hoe de verval datum van Microsoft 365 groep werkt met het Bewaar beleid

Het Bewaar beleid wordt geconfigureerd door middel van het beveiligings-en nalevings centrum. Als u een Bewaar beleid hebt ingesteld voor Microsoft 365 groepen, wanneer een groep verloopt en wordt verwijderd, worden de groeps gesprekken in het postvak van de groep en de bestanden in de groeps site bewaard in de Bewaar container voor het specifieke aantal dagen dat is gedefinieerd in het Bewaar beleid. Gebruikers zien de groep of de inhoud niet na het verlopen, maar kunnen de site-en Postvak gegevens via e-discovery herstellen.

## <a name="powershell-examples"></a>Power shell-voor beelden

Hier volgen enkele voor beelden van hoe u Power shell-cmdlets kunt gebruiken om de verloop instellingen voor Microsoft 365 groepen in uw Azure AD-organisatie te configureren:

1. Installeer de Power shell v 2.0-module en meld u aan bij de Power shell-prompt:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. De verloop instellingen configureren gebruik de cmdlet New-AzureADMSGroupLifecyclePolicy om de levens duur van alle Microsoft 365 groepen in de Azure AD-organisatie tot 365 dagen in te stellen. Meldingen voor het vernieuwen van Microsoft 365 groepen zonder eigen aars worden verzonden naar emailaddress@contoso.com
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. De bestaande beleids Get-AzureADMSGroupLifecyclePolicy ophalen: met deze cmdlet worden de huidige instellingen voor de verloop datum van de Microsoft 365 groep opgehaald die zijn geconfigureerd. In dit voor beeld ziet u het volgende:

   - De beleids-ID
   - De levens duur voor alle Microsoft 365 groepen in de Azure AD-organisatie is ingesteld op 365 dagen
   - Meldingen voor het vernieuwen van Microsoft 365 groepen zonder eigen aars worden verzonden naar emailaddress@contoso.com .
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. De bestaande beleids set-AzureADMSGroupLifecyclePolicy bijwerken: deze cmdlet wordt gebruikt om een bestaand beleid bij te werken. In het onderstaande voor beeld wordt de levens duur van de groep in het bestaande beleid gewijzigd van 365 dagen naar 180 dagen.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Voeg specifieke groepen toe aan het beleid add-AzureADMSLifecyclePolicyGroup: met deze cmdlet wordt een groep toegevoegd aan het beleid voor levens cyclus. Bijvoorbeeld:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Verwijder het bestaande beleid Remove-AzureADMSGroupLifecyclePolicy: met deze cmdlet worden de instellingen voor de Microsoft 365 groeps verloop verwijderd, maar is de beleids-ID vereist. Met deze cmdlet wordt de verval datum voor Microsoft 365 groepen uitgeschakeld.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
De volgende cmdlets kunnen worden gebruikt om het beleid uitvoeriger te configureren. Zie [Power shell-documentatie](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#groups)voor meer informatie.

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
