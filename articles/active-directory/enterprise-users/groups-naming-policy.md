---
title: Groeps naamgevings beleid afdwingen in Azure Active Directory | Microsoft Docs
description: Naamgevings beleid instellen voor Microsoft 365 groepen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 458fbdf4cdfa40da871d34a62101020330c79265
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488790"
---
# <a name="enforce-a-naming-policy-on-microsoft-365-groups-in-azure-active-directory"></a>Een naamgevings beleid afdwingen voor Microsoft 365 groepen in Azure Active Directory

Als u consistente naam conventies wilt afdwingen voor Microsoft 365 groepen die door uw gebruikers zijn gemaakt of bewerkt, stelt u een groeps naamgevings beleid in voor uw organisaties in Azure Active Directory (Azure AD). U kunt bijvoorbeeld het naamgevings beleid gebruiken voor het communiceren van de functie van een groep, lidmaatschap, geografische regio of het maken van de groep. U kunt ook het naamgevings beleid gebruiken om groepen in het adres boek te categoriseren. U kunt het beleid gebruiken om te voor komen dat specifieke woorden worden gebruikt in groeps namen en aliassen.

> [!IMPORTANT]
> Voor het gebruik van Azure AD-naamgevings beleid voor Microsoft 365 groepen moet u een Azure Active Directory Premium P1-licentie of een Azure AD Basic EDU-licentie toewijzen voor elke unieke gebruiker die lid is van een of meer Microsoft 365 groepen.

Het naamgevings beleid wordt toegepast voor het maken of bewerken van groepen die zijn gemaakt in werk belastingen (bijvoorbeeld Outlook, micro soft teams, share point, Exchange of planner). Deze wordt toegepast op zowel de groeps naam als de groeps alias. Als u uw naamgevings beleid instelt in azure AD en u een bestaand naamgevings beleid voor de Exchange-groep hebt, wordt het Azure AD-naamgevings beleid afgedwongen in uw organisatie.

Wanneer groeps naamgevings beleid is geconfigureerd, wordt het beleid toegepast op nieuwe Microsoft 365 groepen die door eind gebruikers worden gemaakt. Naamgevings beleid is niet van toepassing op bepaalde Directory rollen, zoals globale beheerder of gebruikers beheerder (zie hieronder voor de volledige lijst met functies die zijn uitgesloten van groeps naamgevings beleid). Voor bestaande Microsoft 365 groepen wordt het beleid niet direct toegepast op het tijdstip van de configuratie. Nadat de groeps eigenaar de groeps naam voor deze groepen heeft bewerkt, wordt het naamgevings beleid afgedwongen.

## <a name="naming-policy-features"></a>Naamgeving van beleids functies

U kunt het naamgevings beleid voor groepen op twee verschillende manieren afdwingen:

- **Naamgevings beleid voor voegsel-achtervoegsel** U kunt voor voegsels of achtervoegsels definiëren die vervolgens automatisch worden toegevoegd om een naamgevings Conventie voor uw groepen af te dwingen (bijvoorbeeld in de groeps naam "GRP \_ Japan \_ mijn groeps \_ techniek", GRP \_ Japan \_ het voor voegsel en \_ technisch het achtervoegsel is). 

- **Aangepaste geblokkeerde woorden** U kunt een set geblokkeerde woorden die specifiek zijn voor uw organisatie uploaden om te worden geblokkeerd in groepen die zijn gemaakt door gebruikers (bijvoorbeeld "CEO, Payroll, HR").

### <a name="prefix-suffix-naming-policy"></a>Naamgevings beleid voor voegsel-achtervoegsel

De algemene structuur van de naamgevings regels is ' voor voegsel [GroupName] achtervoegsel '. Hoewel u meerdere voor voegsels en achtervoegsels kunt definiëren, kunt u slechts één exemplaar van [GroupName] in de instelling hebben. De voor voegsels of achtervoegsels kunnen bestaan uit vaste teken reeksen of gebruikers kenmerken, zoals \[ afdeling, \] die worden vervangen op basis van de gebruiker die de groep maakt. Het totale toegestane aantal tekens voor de teken reeksen voor voor voegsel en achtervoegsel, inclusief groeps naam, is 53 tekens. 

Voor voegsels en achtervoegsels kunnen speciale tekens bevatten die worden ondersteund in groeps naam en groeps alias. Alle tekens in het voor voegsel of achtervoegsel dat niet wordt ondersteund in de groeps alias, worden nog steeds toegepast in de groeps naam, maar verwijderd uit de groeps alias. Vanwege deze beperking kunnen de voor voegsels en achtervoegsels die worden toegepast op de groeps naam afwijken van de voor waarden die zijn toegepast op de groeps alias. 

#### <a name="fixed-strings"></a>Vaste teken reeksen

U kunt teken reeksen gebruiken om het gemakkelijker te maken om groepen te scannen en te onderscheiden in de algemene adres lijst en in de linker navigatie koppelingen van groeps werkbelastingen. Enkele veelvoorkomende voor voegsels zijn tref woorden zoals ' GRP \_ name ', ' \# name ', ' \_ name '

#### <a name="user-attributes"></a>Gebruikers kenmerken

U kunt kenmerken gebruiken waarmee u en uw gebruikers kunnen bepalen welke afdeling, kantoor of geografische regio waarvoor de groep is gemaakt. Als u bijvoorbeeld uw naamgevings beleid definieert als `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"` , en `User’s department = Engineering` , dan kan de naam van een afgedwongen groep ' groeps techniek ' zijn. Ondersteunde Azure AD-kenmerken zijn \[ afdeling \] , \[ bedrijf \] , \[ kantoor \] , \[ staat of provincie \] , \[ CountryorRegion \] , \[ titel \] . Niet-ondersteunde gebruikers kenmerken worden beschouwd als vaste teken reeksen. bijvoorbeeld ' \[ Post code \] '. Extensie kenmerken en aangepaste kenmerken worden niet ondersteund.

U kunt het beste kenmerken gebruiken waarvoor waarden zijn ingevuld voor alle gebruikers in uw organisatie en geen kenmerken met lange waarden gebruiken.

### <a name="custom-blocked-words"></a>Aangepaste geblokkeerde woorden

Een geblokkeerde woorden lijst is een door komma's gescheiden lijst met zinsdelen die in groeps namen en aliassen worden geblokkeerd. Er worden geen Zoek opdrachten voor subtekenreeksen uitgevoerd. Er is een exacte overeenkomst tussen de groeps naam en een of meer aangepaste geblokkeerde woorden vereist om een fout te activeren. Zoeken in subtekenreeksen wordt niet uitgevoerd, zodat gebruikers algemene woorden als Class kunnen gebruiken, zelfs als ' Lass ' een geblokkeerd woord is.

Geblokkeerde woorden lijst regels:

- Geblokkeerde woorden zijn niet hoofdletter gevoelig.
- Wanneer een gebruiker een geblokkeerd woord als onderdeel van een groeps naam invoert, wordt een fout bericht met het geblokkeerde woord weer gegeven.
- Er zijn geen teken beperkingen voor geblokkeerde woorden.
- Er is een bovengrens van 5000-zinnen die in de lijst met geblokkeerde woorden kunnen worden geconfigureerd. 

### <a name="roles-and-permissions"></a>Rollen en machtigingen

Als u een naamgevings beleid wilt configureren, is een van de volgende rollen vereist:
- Globale beheerder
- Groeps beheerder


Geselecteerde beheerders kunnen worden uitgesloten van dit beleid, in alle werk belastingen en eind punten van groepen, zodat ze groepen kunnen maken met behulp van geblokkeerde woorden en met hun eigen naamgevings regels. Hieronder ziet u de lijst met beheerders rollen die zijn uitgesloten van het groeps naamgevings beleid.

- Globale beheerder
- Ondersteuning voor partner Tier 1
- Ondersteuning voor partner Tier 2
- Gebruikersbeheerder
- Schrijvers van mappen

## <a name="configure-naming-policy-in-azure-portal"></a>Naamgevings beleid configureren in Azure Portal

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een groeps beheerders account.
1. Selecteer **Groepen** en vervolgens **Naamgevingsbeleid** om de pagina Naamgevingsbeleid te openen.

    ![open de pagina Naamgevingsbeleid in het beheercentrum](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Het naamgevings beleid voor voegsel-achtervoegsels weer geven of bewerken

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Naamgevingsbeleid voor groepen**.
1. U kunt het huidige naamgevingsbeleid voor voorvoegsels of achtervoegsels afzonderlijk weergeven of bewerken door de kenmerken of tekenreeksen te selecteren die u wilt afdwingen als onderdeel van het naamgevingsbeleid.
1. Als u een voorvoegsel of achtervoegsel uit de lijst wilt verwijderen, selecteert u het voorvoegsel of achtervoegsel en selecteert u **Verwijderen**. U kunt meerdere items tegelijkertijd verwijderen.
1. Sla de wijzigingen op zodat het nieuwe beleid van kracht wordt door **Opslaan** te selecteren.

### <a name="edit-custom-blocked-words"></a>Aangepaste geblokkeerde woorden bewerken

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Geblokkeerde woorden**.

    ![lijst met geblokkeerde woorden bewerken en uploaden voor naamgevingsbeleid](./media/groups-naming-policy/blockedwords.png)

1. De huidige lijst met aangepaste geblokkeerde woorden weergeven of bewerken door **Downloaden** te selecteren.
1. Upload de nieuwe lijst met aangepaste geblokkeerde woorden door het bestandspictogram te selecteren.
1. Sla de wijzigingen op zodat het nieuwe beleid van kracht wordt door **Opslaan** te selecteren.

## <a name="install-powershell-cmdlets"></a>PowerShell-cmdlets installeren

Verwijder een oudere versie van Azure Active Directory PowerShell voor Graph Module voor Windows PowerShell en installeer [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell voor Graph - Release 2.0.0.137 voor openbare preview) voordat u de PowerShell-opdrachten uitvoert.

1. Open de Windows PowerShell-app als beheerder.
2. Verwijder eventuele oudere versies van AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Installeer de nieuwste versie van AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Als u wordt gevraagd om toegang te krijgen tot een niet-vertrouwde opslag plaats, voert u **Y** in. Het kan enkele minuten duren voordat de nieuwe module is geïnstalleerd.

## <a name="configure-naming-policy-in-powershell"></a>Naamgevings beleid configureren in Power shell

1. Open een Windows Power shell-venster op de computer. U kunt deze openen zonder verhoogde bevoegdheden.

1. Voer de volgende opdrachten uit als voorbereiding op het uitvoeren van de cmdlets.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   In het scherm **Sign in to your Account** dat verschijnt, voert u uw beheerdersaccount en wachtwoord in om verbinding te maken met uw service. Selecteer vervolgens **Aanmelden**.

1. Volg de stappen in [Azure Active Directory-cmdlets voor het configureren van groeps instellingen](../enterprise-users/groups-settings-cmdlets.md) voor het maken van groeps instellingen voor deze organisatie.

### <a name="view-the-current-settings"></a>Huidige instellingen weergeven

1. Haal het huidige naamgevings beleid op om de huidige instellingen weer te geven.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Geef de instellingen voor de huidige groep weer.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Het naamgevings beleid en aangepaste geblokkeerde woorden instellen

1. Stel de voor- en achtervoegsels van de groepsnaam in in Azure AD PowerShell. [GroupName] moet in de instelling worden opgenomen om de functie goed te laten werken.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Stel de aangepaste, geblokkeerde woorden in die u wilt verbieden. In het volgende voorbeeld wordt getoond hoe u uw eigen aangepaste woorden kunt toevoegen.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Sla de instellingen voor het nieuwe beleid op, zoals in het volgende voor beeld.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Dat is alles. U hebt uw naamgevings beleid ingesteld en uw geblokkeerde woorden toegevoegd.

## <a name="export-or-import-custom-blocked-words"></a>Aangepaste geblokkeerde woorden exporteren of importeren

Zie het artikel [Azure Active Directory-cmdlets voor het configureren van groeps instellingen](../enterprise-users/groups-settings-cmdlets.md)voor meer informatie.

Hier volgt een voor beeld van een Power shell-script voor het exporteren van meerdere geblokkeerde woorden:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Hier volgt een voor beeld van een Power shell-script voor het importeren van meerdere geblokkeerde woorden:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Het naamgevings beleid verwijderen

### <a name="remove-the-naming-policy-using-azure-portal"></a>Verwijder het naamgevingsbeleid met behulp van Azure Portal

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Beleid verwijderen**.
1. Nadat u de verwijdering hebt bevestigd, wordt het naamgevingsbeleid verwijderd, met inbegrip van het naamgevingsbeleid voor voorvoegsels en achtervoegsel en eventuele aangepaste geblokkeerde woorden.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Het naamgevings beleid verwijderen met behulp van Azure AD Power shell

1. Wis de voor- en achtervoegsels van de groepsnaam in Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Maak de aangepaste lijst met geblokkeerde woorden leeg.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Sla de instellingen op.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-microsoft-365-apps"></a>Ervaring op het Microsoft 365-apps

Nadat u een groeps naamgevings beleid hebt ingesteld in azure AD, zien ze wanneer een gebruiker een groep in een Microsoft 365-app maakt, het volgende:

- Een voor beeld van de naam op basis van uw naamgevings beleid (met voor voegsels en achtervoegsels) zodra de gebruiker in de groeps naam typt
- Als de gebruiker geblokkeerde woorden invoert, wordt een fout bericht weer gegeven zodat de geblokkeerde woorden kunnen worden verwijderd.

Workload | Naleving
----------- | -------------------------------
Azure Active Directory portals | De Azure AD-Portal en de toegangs venster Portal tonen het naamgevings beleid afgedwongen naam wanneer de gebruiker een groeps naam typt bij het maken of bewerken van een groep. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt een fout bericht met het geblokkeerde woord weer gegeven, zodat de gebruiker het kan verwijderen.
Outlook Web Access (OWA) | Outlook Web Access toont het naamgevings beleid afgedwongen naam wanneer de gebruiker een groeps naam of groeps alias typt. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt een fout bericht weer gegeven in de gebruikers interface samen met het geblokkeerde woord, zodat de gebruiker het kan verwijderen.
Outlook-bureau blad | Groepen die zijn gemaakt in Outlook Desktop, voldoen aan de naamgevings beleids instellingen. Outlook-bureau blad-app toont nog geen voor beeld van de afgedwongen groeps naam en retourneert geen aangepaste geblokkeerde Word-fouten wanneer de gebruiker de groeps naam invoert. Het naamgevings beleid wordt echter automatisch toegepast bij het maken of bewerken van een groep en gebruikers zien fout berichten als er aangepaste geblokkeerde woorden aanwezig zijn in de groeps naam of-alias.
Microsoft Teams | Micro soft teams toont de naam van het groeps beleid afgedwongen wanneer de gebruiker een team naam invoert. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt er een fout bericht samen met het geblokkeerde woord weer gegeven, zodat de gebruiker het kan verwijderen.
SharePoint  |  Share point toont het naamgevings beleid afgedwongen naam wanneer de gebruiker een site naam of groep e-mail adres typt. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt er een fout bericht weer gegeven, samen met het geblokkeerde woord, zodat de gebruiker het kan verwijderen.
Microsoft Stream | Microsoft Stream wordt de naam van het groeps beleid afgedwongen weer gegeven wanneer de gebruiker een groeps naam of groep e-mail alias typt. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt er een fout bericht met het geblokkeerde woord weer gegeven, zodat de gebruiker het kan verwijderen.
Outlook-app voor iOS en Android | Groepen die zijn gemaakt in Outlook-apps, zijn compatibel met het geconfigureerde naamgevings beleid. De mobiele app van Outlook toont nog geen de preview-versie van het naamgevings beleid en retourneert geen aangepaste geblokkeerde Word-fouten wanneer de gebruiker de groeps naam invoert. Het naamgevings beleid wordt echter automatisch toegepast op het klikken op maken/bewerken en gebruikers zien fout berichten als er aangepaste geblokkeerde woorden aanwezig zijn in de groeps naam of-alias.
Mobiele app-groepen | Groepen die zijn gemaakt in de mobiele app voor groepen, voldoen aan het naamgevings beleid. Met mobiele app-groepen wordt de preview van het naamgevings beleid niet weer gegeven en worden geen aangepaste geblokkeerde woord fouten geretourneerd wanneer de gebruiker de groeps naam invoert. Het naamgevings beleid wordt echter automatisch toegepast bij het maken of bewerken van een groep en gebruikers worden gepresenteerd met de juiste fouten als er aangepaste geblokkeerde woorden aanwezig zijn in de groeps naam of-alias.
Planner | Planner is compatibel met het naamgevings beleid. Planner toont de voorbeeld weergave van het naamgevings beleid bij het invoeren van de naam van het abonnement. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt een fout bericht weer gegeven bij het maken van het plan.
Dynamics 365 for Customer Engagement | Dynamics 365 voor klant betrokkenheid is compatibel met het naamgevings beleid. Dynamics 365 toont het naamgevings beleid afgedwongen naam wanneer de gebruiker een groeps naam of groep e-mail alias typt. Wanneer de gebruiker een aangepast geblokkeerd woord invoert, wordt er een fout bericht met het geblokkeerde woord weer gegeven, zodat de gebruiker het kan verwijderen.
School gegevens synchroniseren (SDS) | Groepen die zijn gemaakt via SDS, voldoen aan het naamgevings beleid, maar het naamgevings beleid wordt niet automatisch toegepast. SDS-beheerders moeten de voor voegsels en achtervoegsels toevoegen aan klassen namen waarvoor groepen moeten worden gemaakt en vervolgens naar SDS kunnen worden geüpload. Het maken of bewerken van een groep zou anders mislukken.
Klas lokale app | Groepen die zijn gemaakt in de app leslokaal, voldoen aan het naamgevings beleid, maar het naamgevings beleid wordt niet automatisch toegepast en de voor beeld van een naamgevings beleid wordt niet weer gegeven voor gebruikers bij het invoeren van de naam van een leslokaal groep. Gebruikers moeten de naam van de afgedwongen leslokaal groep invoeren met voor voegsels en achtervoegsels. Als dat niet het geval is, mislukt de bewerking voor het maken of bewerken van de leslokaal groep met fouten.
Power BI | Power BI-werk ruimten voldoen aan het naamgevings beleid.    
Yammer | Wanneer een gebruiker die is aangemeld bij Yammer met hun Azure Active Directory-account een groep maakt of een groeps naam bewerkt, wordt de groeps naam in overeenstemming met het naamgevings beleid. Dit geldt zowel voor Microsoft 365 verbonden groepen als voor alle andere Yammer-groepen.<br>Als er een Microsoft 365 verbonden groep is gemaakt voordat het naamgevings beleid is ingesteld, volgt de groeps naam niet automatisch het naamgevings beleid. Wanneer een gebruiker de groeps naam bewerkt, wordt u gevraagd het voor voegsel en achtervoegsel toe te voegen.
StaffHub  | StaffHub teams volgen het naamgevings beleid niet, maar de onderliggende Microsoft 365-groep wel. De StaffHub-team naam past de voor voegsels en achtervoegsels niet toe en controleert niet op aangepaste geblokkeerde woorden. Maar StaffHub past de voor voegsels en achtervoegsels toe en verwijdert geblokkeerde woorden uit de onderliggende Microsoft 365 groep.
Exchange Power shell | Exchange Power shell-cmdlets zijn compatibel met het naamgevings beleid. Gebruikers ontvangen de juiste fout berichten met suggesties voor voegsels en achtervoegsels en voor aangepaste geblokkeerde woorden als ze het naamgevings beleid niet volgen in de groeps naam en groeps alias (mailnickname).
Azure Active Directory Power shell-cmdlets | Azure Active Directory Power shell-cmdlets zijn compatibel met het naamgevings beleid. Gebruikers ontvangen de juiste fout berichten met suggesties voor voegsels en achtervoegsels en voor aangepaste geblokkeerde woorden als ze niet voldoen aan de naamgevings conventies in groeps namen en groeps aliassen.
Exchange-beheer centrum | Exchange-beheer centrum is compatibel met het naamgevings beleid. Gebruikers ontvangen de juiste fout berichten met suggesties voor voegsels en achtervoegsels en voor aangepaste geblokkeerde woorden als ze niet voldoen aan de naamgevings conventies in de groeps naam en groeps alias.
Het Microsoft 365-beheercentrum | Microsoft 365-beheer centrum is compatibel met het naamgevings beleid. Wanneer een gebruiker groeps namen maakt of bewerkt, wordt het naamgevings beleid automatisch toegepast en ontvangen gebruikers de juiste fouten wanneer ze aangepaste geblokkeerde woorden invoeren. Het Microsoft 365-beheer centrum toont nog geen voor beeld van het naamgevings beleid en retourneert geen aangepaste geblokkeerde Word-fouten wanneer de gebruiker de groeps naam invoert.

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure AD-groepen.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Verloop beleid voor Microsoft 365 groepen](groups-lifecycle.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Leden van een groep beheren](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
