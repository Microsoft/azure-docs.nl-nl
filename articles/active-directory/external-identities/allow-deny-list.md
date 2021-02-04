---
title: 'Uitnodigingen voor specifieke organisaties toestaan of blok keren: Azure AD'
description: Laat zien hoe een beheerder met behulp van de Azure Portal of Power shell een lijst voor toegang of weigeren kan instellen om B2B-gebruikers toe te staan of te blok keren voor bepaalde domeinen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb5a785cba4a560fc15963363d4e5f6b811af97a
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548828"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Uitnodigingen aan B2B-gebruikers van specifieke organisaties toestaan of blokkeren

U kunt een lijst met toegestane of een lijst weigeren gebruiken om uitnodigingen voor B2B-gebruikers van specifieke organisaties toe te staan of te blok keren. Als u bijvoorbeeld persoonlijke e-mailadres domeinen wilt blok keren, kunt u een Deny-lijst instellen die domeinen bevat, zoals Gmail.com en Outlook.com. Of als uw bedrijf een partnerschap heeft met andere bedrijven, zoals Contoso.com, Fabrikam.com en Litware.com, en u uitnodigingen wilt beperken tot alleen deze organisaties, kunt u Contoso.com, Fabrikam.com en Litware.com toevoegen aan de lijst met toegestane apps.
  
## <a name="important-considerations"></a>Belangrijke overwegingen

- U kunt een acceptatie lijst of een weiger lijst maken. U kunt niet beide typen lijsten instellen. Standaard zijn alle domeinen die zich niet in de acceptatie lijst bevinden in de Deny-lijst en vice versa. 
- U kunt slechts één beleid per organisatie maken. U kunt het beleid bijwerken zodat er meer domeinen worden toegevoegd, of u kunt het beleid verwijderen om een nieuw domein te maken. 
- Het aantal domeinen dat u kunt toevoegen aan een lijst met toegestane of geweigerde lijsten, wordt alleen beperkt door de grootte van het beleid. Deze limiet is van toepassing op het aantal tekens, zodat u meer domeinen of minder domeinen kunt hebben. De maximale grootte van het hele beleid is 25 KB (25.000 tekens), waaronder de lijst met toegestane of geweigerde en andere para meters die voor andere functies zijn geconfigureerd.
- Deze lijst werkt onafhankelijk van OneDrive voor bedrijven en share point online-lijsten voor toestaan/blok keren. Als u het delen van afzonderlijke bestanden in share point online wilt beperken, moet u een lijst voor toestaan of weigeren instellen voor OneDrive voor bedrijven en share point online. Zie [beperkte domeinen delen in share point online en OneDrive voor bedrijven](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9)voor meer informatie.
- De lijst is niet van toepassing op externe gebruikers die de uitnodiging al hebben ingewisseld. De lijst wordt afgedwongen nadat de lijst is ingesteld. Als een uitnodiging voor een gebruiker de status in behandeling heeft en u een beleid instelt waarmee het domein wordt geblokkeerd, mislukt de gebruiker de uitnodiging inwisselen.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Beleid voor het toestaan of weigeren van de lijst in de Portal instellen

Standaard is de instelling **uitnodigingen voor het verzenden van een domein (de meeste inbegrepen)** ingeschakeld. In dit geval kunt u B2B-gebruikers uit elke organisatie uitnodigen.

### <a name="add-a-deny-list"></a>Een Deny-lijst toevoegen

Dit is het meest typische scenario, waarbij uw organisatie bijna elke organisatie wil gebruiken, maar wil voor komen dat gebruikers van specifieke domeinen worden uitgenodigd als B2B-gebruikers.

Een weiger lijst toevoegen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Azure Active Directory**  >  **gebruikers**  >  **instellingen**.
3. Onder **externe gebruikers** selecteert u **externe instellingen voor samen werking beheren**.
4. Onder **samenwerkings beperkingen** selecteert **u uitnodigingen voor de opgegeven domeinen weigeren**.
5. Voer onder **doel domeinen** de naam in van een van de domeinen die u wilt blok keren. Voer voor meerdere domeinen elk domein in op een nieuwe regel. Bijvoorbeeld:

   ![De optie weigeren met toegevoegde domeinen weer geven](./media/allow-deny-list/DenyListSettings.png)
 
6. Wanneer u klaar bent, klikt u op **Opslaan**.

Nadat u het beleid hebt ingesteld, ontvangt u een bericht dat het domein van de gebruiker momenteel is geblokkeerd door uw uitnodigings beleid als u een gebruiker probeert uit te nodigen van een geblokkeerd domein.
 
### <a name="add-an-allow-list"></a>Een acceptatie lijst toevoegen

Dit is een meer beperkende configuratie, waar u specifieke domeinen kunt instellen in de acceptatie lijst en uitnodigingen beperkt tot andere organisaties of domeinen die niet worden vermeld. 

Als u een acceptatie lijst wilt gebruiken, moet u ervoor zorgen dat u de tijd hebt om volledig te evalueren wat uw bedrijf nodig heeft. Als u dit beleid te beperkend maakt, kunnen uw gebruikers ervoor kiezen documenten via e-mail te verzenden of andere niet-IT-goedgekeurde manieren van samen werking te vinden.


Een acceptatie lijst toevoegen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Azure Active Directory**  >  **gebruikers**  >  **instellingen**.
3. Onder **externe gebruikers** selecteert u **externe instellingen voor samen werking beheren**.
4. Selecteer onder **samenwerkings beperkingen** **alleen uitnodigingen toestaan voor de opgegeven domeinen (het meest beperkend)**.
5. Voer onder **doel domeinen** de naam in van een van de domeinen die u wilt toestaan. Voer voor meerdere domeinen elk domein in op een nieuwe regel. Bijvoorbeeld:

   ![Hiermee wordt de optie toestaan met toegevoegde domeinen weer gegeven](./media/allow-deny-list/AllowListSettings.png)
 
6. Wanneer u klaar bent, klikt u op **Opslaan**.

Nadat u het beleid hebt ingesteld, ontvangt u een bericht met de melding dat het domein van de gebruiker momenteel is geblokkeerd door uw uitnodigings beleid als u een gebruiker probeert uit te nodigen van een domein dat zich niet in de acceptatie lijst bevindt.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Scha kelen van de lijst weigeren naar weigering en vice versa 

Als u overschakelt van het ene naar het andere beleid, wordt hiermee de bestaande beleids configuratie verwijderd. Zorg ervoor dat u een back-up maakt van de details van uw configuratie voordat u de switch uitvoert. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Het beleid lijst toestaan of weigeren instellen met behulp van Power shell

### <a name="prerequisite"></a>Vereiste

> [!Note]
> De AzureADPreview-module is geen volledig ondersteunde module, omdat deze in preview is. 

Als u de lijst toestaan of weigeren wilt instellen met behulp van Power shell, moet u de preview-versie van de Azure Active Directory-module voor Windows Power Shell installeren. Installeer de AzureADPreview-module versie 2.0.0.98 of hoger.

De versie van de module controleren (en controleren of deze is geïnstalleerd):
 
1. Open Windows Power shell als een verhoogde gebruiker (als administrator uitvoeren). 
2. Voer de volgende opdracht uit om te zien of er versies van de Azure Active Directory-module voor Windows Power shell op uw computer zijn geïnstalleerd:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Als de module niet is geïnstalleerd of als u niet beschikt over een vereiste versie, voert u een van de volgende handelingen uit:

- Als er geen resultaten worden geretourneerd, voert u de volgende opdracht uit om de meest recente versie van de AzureADPreview-module te installeren:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Als alleen de AzureAD-module wordt weer gegeven in de resultaten, voert u de volgende opdrachten uit om de AzureADPreview-module te installeren: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Als alleen de AzureADPreview-module wordt weer gegeven in de resultaten, maar de versie kleiner is dan 2.0.0.98, voert u de volgende opdrachten uit om deze bij te werken: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Als de AzureAD-en AzureADPreview-modules in de resultaten worden weer gegeven, maar de versie van de AzureADPreview-module kleiner is dan 2.0.0.98, voert u de volgende opdrachten uit om deze bij te werken: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>De AzureADPolicy-cmdlets gebruiken om het beleid te configureren

Als u een lijst voor toestaan of weigeren wilt maken, gebruikt u de cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . In het volgende voor beeld ziet u hoe u een Deny-lijst instelt die het domein ' live.com ' blokkeert.

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Hieronder ziet u hetzelfde voor beeld, maar met de beleids definitie inline.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Als u het beleid lijst toestaan of weigeren wilt instellen, gebruikt u de cmdlet [set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Bijvoorbeeld:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Gebruik de cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) om het beleid op te halen. Bijvoorbeeld:

```powershell
$currentpolicy = Get-AzureADPolicy -All $true | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Als u het beleid wilt verwijderen, gebruikt u de cmdlet [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) . Bijvoorbeeld:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure AD B2B-samen werking?](what-is-b2b.md) voor een overzicht van Azure AD B2B.
- Zie [voorwaardelijke toegang voor B2B-samenwerkings gebruikers](conditional-access.md)voor meer informatie over voorwaardelijke toegang en B2B-samen werking.
