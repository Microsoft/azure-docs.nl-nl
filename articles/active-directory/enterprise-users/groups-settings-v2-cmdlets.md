---
title: Power shell v2-voor beelden voor het beheren van groepen-Azure AD | Microsoft Docs
description: Op deze pagina vindt u Power shell-voor beelden voor het beheren van uw groepen in Azure Active Directory
keywords: Azure AD, Azure Active Directory, Power shell, groepen, groeps beheer
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12613362d9fc67dea14b41ece19a34febb489dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860640"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Active Directory versie 2-cmdlets voor groeps beheer

> [!div class="op_single_selector"]
> - [Azure-portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
>
>

In dit artikel vindt u voor beelden van het gebruik van Power shell voor het beheren van uw groepen in Azure Active Directory (Azure AD).  Ook wordt uitgelegd hoe u kunt instellen met de Azure AD Power shell-module. Eerst moet u [de Azure AD Power shell-module downloaden](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>De Azure AD Power shell-module installeren

Als u de Azure AD Power shell-module wilt installeren, gebruikt u de volgende opdrachten:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Als u wilt controleren of de module gereed is voor gebruik, gebruikt u de volgende opdracht:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

U kunt nu beginnen met het gebruik van de cmdlets in de module. Raadpleeg de online-referentie documentatie voor [Azure Active Directory Power shell-versie 2](/powershell/azure/active-directory/install-adv2)voor een volledige beschrijving van de cmdlets in de Azure ad-module.

> [!NOTE]
> De Azure AD Power shell-cmdlets werken niet met de nieuwe Power shell 7, omdat deze is gebaseerd op .net core. We zijn op de hoogte en dit is in het proces van het downloaden van updates. Vanaf nu stellen we suggesties voor het gebruik van de Windows Power shell 5. x-module die moet worden gebruikt voor Azure AD Power shell-bewerkingen. 


## <a name="connect-to-the-directory"></a>Verbinding maken met de map

Voordat u groepen kunt gaan beheren met Azure AD Power shell-cmdlets, moet u uw Power shell-sessie verbinden met de map die u wilt beheren. Gebruik de volgende opdracht:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

De cmdlet vraagt u om de referenties die u wilt gebruiken voor toegang tot uw Directory. In dit voor beeld gebruiken we karen@drumkit.onmicrosoft.com om toegang te krijgen tot de demo Directory. De cmdlet retourneert een bevestiging om weer te geven dat de sessie is verbonden met uw map:

```powershell
    Account                       Environment Tenant ID
    -------                       ----------- ---------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

U kunt nu beginnen met het gebruik van de AzureAD-cmdlets voor het beheren van groepen in uw Directory.

## <a name="retrieve-groups"></a>Groepen ophalen

Gebruik de cmdlet Get-AzureADGroups om bestaande groepen uit uw directory op te halen. 

Als u alle groepen in de Directory wilt ophalen, gebruikt u de cmdlet zonder para meters:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Met de cmdlet worden alle groepen in de verbonden Directory geretourneerd.

U kunt de para meter-objectID gebruiken voor het ophalen van een specifieke groep waarvoor u de object-id van de groep opgeeft:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

De cmdlet retourneert nu de groep waarvan de object-id overeenkomt met de waarde van de para meter die u hebt ingevoerd:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

U kunt zoeken naar een specifieke groep met behulp van de para meter-filter. Deze para meter gebruikt een ODATA-filter component en retourneert alle groepen die overeenkomen met het filter, zoals in het volgende voor beeld:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> De Azure AD Power shell-cmdlets implementeren de OData-query standaard. Zie **$filter** in [de query opties van het odata-systeem met behulp van het odata-eind punt](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)#BKMK_filter)voor meer informatie.

## <a name="create-groups"></a>Groepen maken

Gebruik de cmdlet New-AzureADGroup om een nieuwe groep te maken in uw Directory. Met deze cmdlet wordt een nieuwe beveiligings groep gemaakt met de naam ' Marketing ':

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Update groepen

Als u een bestaande groep wilt bijwerken, gebruikt u de cmdlet Set-AzureADGroup. In dit voor beeld wijzigen we de eigenschap DisplayName van de groep ' intune-beheerders '. Eerst vinden we de groep met behulp van de cmdlet Get-AzureADGroup en filteren met behulp van het kenmerk DisplayName:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Vervolgens wijzigen we de eigenschap Description in de nieuwe waarde ' intune-apparaten beheerders ':

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Als we de groep nu opnieuw vinden, zien we dat de eigenschap Beschrijving is bijgewerkt met de nieuwe waarde:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Groepen verwijderen

Als u groepen uit uw Directory wilt verwijderen, gebruikt u de cmdlet Remove-AzureADGroup als volgt:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Groepslidmaatschap beheren

### <a name="add-members"></a>Leden toevoegen

Als u nieuwe leden wilt toevoegen aan een groep, gebruikt u de cmdlet Add-AzureADGroupMember. Met deze opdracht wordt een lid toegevoegd aan de groep intune-beheerders die we in het vorige voor beeld hebben gebruikt:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

De para meter-ObjectId is de ObjectID van de groep waaraan we een lid willen toevoegen en de-RefObjectId is de ObjectID van de gebruiker die u als lid wilt toevoegen aan de groep.

### <a name="get-members"></a>Leden ophalen

Als u de bestaande leden van een groep wilt ophalen, gebruikt u de cmdlet Get-AzureADGroupMember, zoals in dit voor beeld:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Leden verwijderen

Als u het eerder aan de groep toegevoegde lid wilt verwijderen, gebruikt u de cmdlet Remove-AzureADGroupMember, zoals hier wordt weer gegeven:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Leden controleren

Gebruik de cmdlet Select-AzureADGroupIdsUserIsMemberOf om de groepslid maatschappen van een gebruiker te controleren. Deze cmdlet krijgt de ObjectId van de gebruiker waarvoor de groepslid maatschappen moeten worden gecontroleerd en een lijst met groepen waarvoor de lidmaatschappen moeten worden gecontroleerd. De lijst met groepen moet worden weer gegeven in de vorm van een complexe variabele van het type micro soft. open. AzureAD. model. GroupIdsForMembershipCheck. Daarom moet u eerst een variabele met dat type maken:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

We geven vervolgens waarden op voor de groupIds om het kenmerk ' GroupIds ' van deze complexe variabele te controleren:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Als we nu de groepslid maatschappen van een gebruiker met ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea willen controleren op basis van de groepen in $g, moeten we het volgende gebruiken:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

De geretourneerde waarde is een lijst met groepen waarvan deze gebruiker lid is. U kunt deze methode ook Toep assen om het lidmaatschap van contact personen, groepen of service-principals voor een bepaalde lijst met groepen te controleren met Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf of Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Het maken van groepen door uw gebruikers uitschakelen

U kunt voor komen dat gebruikers zonder beheerders rechten beveiligings groepen maken. Het standaard gedrag in micro soft Online Directory Services (MSODS) is om niet-beheerders gebruikers toe te staan groepen te maken, ongeacht of self-service groeps beheer (SSGM) ook is ingeschakeld. De instelling SSGM bepaalt alleen gedrag in het toegangs venster mijn apps.

Het maken van groepen uitschakelen voor gebruikers die geen beheerder zijn:

1. Controleer of niet-beheerders gebruikers groepen mogen maken:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Als deze wordt geretourneerd `UsersPermissionToCreateGroupsEnabled : True` , kunnen gebruikers die geen beheerder zijn, groepen maken. U kunt deze functie als volgt uitschakelen:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Eigen aren van groepen beheren

Als u eigen aren wilt toevoegen aan een groep, gebruikt u de cmdlet Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

De para meter-ObjectId is de ObjectID van de groep waaraan we een eigenaar willen toevoegen en de-RefObjectId is de ObjectID van de gebruiker of service-principal die u wilt toevoegen als een eigenaar van de groep.

Gebruik de cmdlet Get-AzureADGroupOwner om de eigen aren van een groep op te halen:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

De cmdlet retourneert de lijst met eigen aren (gebruikers en service-principals) voor de opgegeven groep:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Als u een eigenaar uit een groep wilt verwijderen, gebruikt u de cmdlet Remove-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Gereserveerde aliassen

Wanneer een groep wordt gemaakt, kan de eind gebruiker met bepaalde eind punten een mailnickname of alias opgeven die moet worden gebruikt als onderdeel van het e-mail adres van de groep. Groepen met de volgende Maxi maal privileged e-mail aliassen kunnen alleen worden gemaakt door een globale beheerder van Azure AD. 
  
* verslaving
* beheerder
* beheerder
* hostmaster
* majordomo
* beheerder
* basis
* beveiligd
* security
* SSL-beheerder
* Webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Terugschrijven naar on-premises groep (preview-versie)

Vandaag worden nog veel groepen beheerd in on-premises Active Directory. Voor het beantwoorden van aanvragen om Cloud groepen te synchroniseren met on-premises, Microsoft 365 Groups-functie voor het terugschrijven van Azure AD is nu beschikbaar als preview-versie.

Microsoft 365 groepen worden gemaakt en beheerd in de Cloud. Met de back-upfunctie kunt u Microsoft 365 groepen als distributie groepen wegschrijven naar een Active Directory-forest waarop Exchange is geïnstalleerd. Gebruikers met on-premises Exchange-post vakken kunnen vervolgens e-mails van deze groepen verzenden en ontvangen. De functie voor het terugschrijven van groepen biedt geen ondersteuning voor Azure AD-beveiligings groepen of-distributie groepen.

Raadpleeg de documentatie voor de [Azure AD Connect Sync-Service](../hybrid/how-to-connect-syncservice-features.md)voor meer informatie.

Het terugschrijven van Microsoft 365 groep is een open bare preview-functie van Azure Active Directory (Azure AD) en is beschikbaar in elk betaald licentie abonnement voor Azure AD. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor een aantal juridische informatie over voor beelden.

## <a name="next-steps"></a>Volgende stappen

U kunt meer Azure Active Directory Power shell-documentatie vinden op [Azure Active Directory-cmdlets](/powershell/azure/active-directory/install-adv2).

* [Toegang tot resources beheren met Azure Active Directory groepen](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
