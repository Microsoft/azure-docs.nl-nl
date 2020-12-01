---
title: 'Azure AD Connect: machtigingen voor het AD DS-Connector account configureren | Microsoft Docs'
description: Dit document bevat informatie over het configureren van het AD DS Connector-account met de nieuwe ADSyncConfig Power shell-module
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62bfc528886767bc09159ca2a2696c8c9264b307
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349936"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: De machtigingen van een AD DS Connector-account configureren 

De Power shell-module met de naam [ADSyncConfig. psm1](reference-connect-adsyncconfig.md) is geïntroduceerd in Build 1.1.880.0 (uitgebracht in augustus 2018), die een verzameling cmdlets bevat waarmee u de juiste Active Directory machtigingen voor uw Azure AD Connect-implementatie kunt configureren. 

## <a name="overview"></a>Overzicht 
De volgende Power shell-cmdlets kunnen worden gebruikt voor het instellen van Active Directory machtigingen van het AD DS Connector-account, voor elke functie die u selecteert om in Azure AD Connect in te scha kelen. Als u problemen wilt voor komen, moet u Active Directory-machtigingen vooraf voorbereiden wanneer u Azure AD Connect wilt installeren met behulp van een aangepast domein account om verbinding te maken met uw forest. Deze ADSyncConfig-module kan ook worden gebruikt om machtigingen te configureren nadat Azure AD Connect is geïmplementeerd.

![overzicht van AD DS-account](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Voor Azure AD Connect snelle installatie wordt een automatisch gegenereerd account (MSOL_nnnnnnnnnn) gemaakt in Active Directory met alle benodigde machtigingen. Daarom hoeft u deze ADSyncConfig-module niet te gebruiken tenzij u machtigingen overname hebt geblokkeerd voor organisatie-eenheden of op specifieke Active Directory objecten die u wilt synchroniseren met Azure AD. 
 
### <a name="permissions-summary"></a>Overzicht van bevoegdheden 
De volgende tabel bevat een overzicht van de vereiste machtigingen voor AD-objecten: 

| Functie | Machtigingen |
| --- | --- |
| functie MS-DS-ConsistencyGuid |Lees-en schrijf machtigingen voor het kenmerk MS-DS-ConsistencyGuid dat wordt beschreven in [ontwerp concepten-MS-DS-ConsistencyGuid gebruiken als source Anchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Wachtwoord-hash-synchronisatie |<li>Directory wijzigingen repliceren</li>  <li>Wijzigingen in Directory repliceren |
| Hybride implementatie van Exchange |Lees-en schrijf machtigingen voor de kenmerken die worden beschreven in [hybride write-back van Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) voor gebruikers, groepen en contact personen. |
| Open bare map voor Exchange-e-mail |Lees machtigingen voor de kenmerken die in de [open bare map voor Exchange mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) voor open bare mappen worden beschreven. | 
| Wachtwoord terugschrijven |Lees-en schrijf machtigingen voor de kenmerken die worden beschreven in [aan de slag met wachtwoord beheer](../authentication/tutorial-enable-sspr-writeback.md) voor gebruikers. |
| Apparaat terugschrijven |Lees-en schrijf machtigingen voor apparaatklassen en containers die worden beschreven in [write-back van apparaat](how-to-connect-device-writeback.md). |
| Groep terugschrijven |Lezen, maken, bijwerken en verwijderen van groeps objecten voor gesynchroniseerde **Office 365-groepen**.|

## <a name="using-the-adsyncconfig-powershell-module"></a>De ADSyncConfig Power shell-module gebruiken 
De ADSyncConfig-module vereist de [Remote Server Administration Tools (RSAT) voor AD DS](/windows-server/remote/remote-server-administration-tools) omdat deze afhankelijk is van de AD DS Power shell-module en-hulpprogram ma's. Als u RSAT voor AD DS wilt installeren, opent u een Windows Power shell-venster met ' uitvoeren als Administrator ' en voert u de volgende handelingen uit: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Configureren](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>U kunt ook het bestand **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** kopiëren naar een domein controller waarop al RSAT voor AD DS is geïnstalleerd en deze Power shell-module van daaruit gebruikt.

Als u de ADSyncConfig wilt gaan gebruiken, moet u de module laden in een Windows Power shell-venster: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Als u alle cmdlets die in deze module zijn opgenomen wilt controleren, kunt u het volgende typen:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Selecteren](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Elke cmdlet heeft dezelfde para meters om het AD DS Connector-account en een AdminSDHolder-switch in te voeren. Als u uw AD DS Connector-account wilt opgeven, kunt u de account naam en het domein opgeven, of alleen de DN-naam (account Distinguished Name).

Bijvoorbeeld:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Of

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Vervang `<ADAccountName>` `<ADDomainName>` en door `<ADAccountDN>` de juiste waarden voor uw omgeving.

Als u de machtigingen voor de AdminSDHolder-container niet wilt wijzigen, gebruikt u de switch `-SkipAdminSdHolders` . 

Standaard worden alle cmdlets voor set permissions geprobeerd AD DS machtigingen in te stellen voor de hoofdmap van elk domein in het forest, wat betekent dat de gebruiker die de Power shell-sessie uitvoert, domein beheerders rechten nodig heeft voor elk domein in het forest.  Vanwege deze vereiste wordt het aanbevolen een ondernemings Administrator te gebruiken vanuit de hoofdmap van het forest. Als uw Azure AD Connect-implementatie meerdere AD DS connectors heeft, moet deze dezelfde cmdlet uitvoeren op elk forest met een AD DS-connector. 

U kunt ook machtigingen voor een specifieke OE of AD DS object instellen met behulp van de para meter `-ADobjectDN` gevolgd door de DN van het doel object waarvoor u machtigingen wilt instellen. Wanneer u een doel-ADobjectDN gebruikt, worden met de cmdlet alleen machtigingen voor dit object ingesteld en niet op de hoofd-of AdminSDHolder-container van het domein. Deze para meter kan nuttig zijn wanneer u bepaalde Ou's of AD DS objecten hebt waarvoor de overname van machtigingen is uitgeschakeld (Zie AD DS objecten selecteren waarvoor de overname van machtigingen is uitgeschakeld) 

Uitzonde ringen op deze algemene para meters zijn de `Set-ADSyncRestrictedPermissions` cmdlet die wordt gebruikt om de machtigingen in te stellen voor het AD DS Connector-account zelf, en de `Set-ADSyncPasswordHashSyncPermissions` cmdlet omdat de vereiste machtigingen voor wachtwoord hash-synchronisatie alleen worden ingesteld op de hoofdmap van het domein, daarom bevat deze cmdlet geen `-ObjectDN` `-SkipAdminSdHolders` para meters.

### <a name="determine-your-ad-ds-connector-account"></a>Uw AD DS Connector-account bepalen 
Als Azure AD Connect al is geïnstalleerd en u wilt controleren welk account AD DS connector momenteel wordt gebruikt door Azure AD Connect, kunt u de cmdlet uitvoeren: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>AD DS objecten zoeken waarvoor de overname van machtigingen is uitgeschakeld 
Als u wilt controleren of er een AD DS object is met de machtiging overname uitgeschakeld, kunt u het volgende uitvoeren: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Deze cmdlet zoekt standaard alleen naar organisatie-eenheden met uitgeschakelde overname, maar u kunt als volgt andere AD DS-object klassen in `-ObjectClass` de para meter opgeven of ' * ' gebruiken voor alle object klassen: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>AD DS machtigingen van een object weer geven 
U kunt de onderstaande cmdlet gebruiken om de lijst met machtigingen weer te geven die momenteel zijn ingesteld voor een Active Directory-object door de DN-naam te verstrekken: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>De machtigingen van een AD DS Connector-account configureren 
 
### <a name="configure-basic-read-only-permissions"></a>Basis Read-Only machtigingen configureren 
Als u basis machtigingen voor alleen-lezen wilt instellen voor de AD DS Connector-account wanneer u geen Azure AD Connect-functie gebruikt, voert u de volgende handelingen uit: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


of 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Met deze cmdlet worden de volgende machtigingen ingesteld: 
 

|Type |Naam |Access |Van toepassing op| 
|-----|-----|-----|-----|
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen |Onderliggende objecten van apparaat| 
|Toestaan |AD DS Connector-account|Alle eigenschappen lezen |Onderliggend InetOrgPerson-objecten| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen |Onderliggende computer objecten| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen |Onderliggende foreignSecurityPrincipal-objecten| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen |Objecten van onderliggende groep| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen |Onderliggende gebruikers objecten| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen |Onderliggende contact personen-objecten| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>MS-DS-consistentie-GUID-machtigingen configureren 
Als u machtigingen wilt instellen voor de AD DS Connector-account wanneer u het kenmerk MS-DS-Consistency-GUID gebruikt als het bron anker (ook wel ' Azure de bron anker voor mij laten beheren '), voert u uit: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

of 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Naam |Access |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan|AD DS Connector-account|Eigenschap lezen/schrijven|Onderliggende gebruikers objecten|

### <a name="permissions-for-password-hash-synchronization"></a>Machtigingen voor het synchroniseren van wacht woord-hashes 
Om machtigingen in te stellen voor het AD DS Connector-account bij het gebruik van wachtwoord-hash-synchronisatie, voert u uit: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


of 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Naam |Access |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS Connector-account |Directory wijzigingen repliceren |Alleen dit object (root van het domein)| 
|Toestaan |AD DS Connector-account |Directory wijzigingen repliceren |Alleen dit object (root van het domein)| 
  
### <a name="permissions-for-password-writeback"></a>Machtigingen voor het terugschrijven van wacht woorden 
Om machtigingen in te stellen voor het AD DS Connector-account bij het gebruik van wacht woord terugschrijven, voert u uit: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


of

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Naam |Access |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS Connector-account |Wachtwoord opnieuw instellen |Onderliggende gebruikers objecten| 
|Toestaan |AD DS Connector-account |Eigenschap Write lockoutTime |Onderliggende gebruikers objecten| 
|Toestaan |AD DS Connector-account |Eigenschap Write pwdLastSet |Onderliggende gebruikers objecten| 

### <a name="permissions-for-group-writeback"></a>Machtigingen voor het terugschrijven van groepen 
Om machtigingen in te stellen voor het AD DS Connector-account bij gebruik van groeps terugschrijven, uitvoeren: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
of 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Naam |Access |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS Connector-account |Algemene lees-en schrijf bewerkingen |Alle kenmerken van de object type groep en subobjecten| 
|Toestaan |AD DS Connector-account |Onderliggend object maken/verwijderen |Alle kenmerken van de object type groep en subobjecten| 
|Toestaan |AD DS Connector-account |Structuur objecten verwijderen/verwijderen|Alle kenmerken van de object type groep en subobjecten|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Machtigingen voor hybride implementatie van Exchange 
Als u machtigingen wilt instellen voor de AD DS Connector-account bij gebruik van hybride implementatie van Exchange, voert u de volgende handelingen uit: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


of 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Met deze cmdlet worden de volgende machtigingen ingesteld:  
 

|Type |Naam |Access |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen/schrijven |Onderliggende gebruikers objecten| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen/schrijven |Onderliggend InetOrgPerson-objecten| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen/schrijven |Objecten van onderliggende groep| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen/schrijven |Onderliggende contact personen-objecten| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Machtigingen voor open bare Exchange-e-mail mappen (preview-versie) 
Als u machtigingen wilt instellen voor de AD DS Connector-account wanneer u gebruikmaakt van de functie open bare mappen van Exchange mail, voert u uit: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


of 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Naam |Access |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |AD DS Connector-account |Alle eigenschappen lezen |Onderliggende PublicFolder-objecten| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Machtigingen voor het AD DS Connector-account beperken 
Met dit Power shell-script worden de machtigingen uitgebreid voor het AD-Connector account dat is geleverd als para meter. Het verhogen van de machtigingen bestaat uit de volgende stappen: 

- Overname uitschakelen voor het opgegeven object 
- Verwijder alle Ace's van het specifieke object, met uitzonde ring van Ace's die specifiek zijn voor zichzelf, omdat we de standaard machtigingen intact moeten houden wanneer het gaat om zichzelf. 
 
  De para meter-ADConnectorAccountDN is het AD-account waarvan de machtigingen moeten worden gescherpt. Dit is doorgaans het MSOL_nnnnnnnnnnnn domein account dat in de AD DS-connector is geconfigureerd (zie uw AD DS Connector-account bepalen). De para meter-Credential is nodig om het beheerders account op te geven dat over de benodigde bevoegdheden beschikt om Active Directory machtigingen voor het doel-AD-object te beperken. Dit is doorgaans de beheerder van de onderneming of het domein.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Bijvoorbeeld: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Met deze cmdlet worden de volgende machtigingen ingesteld: 

|Type |Naam |Access |Van toepassing op|
|-----|-----|-----|-----| 
|Toestaan |SYSTEEM |Volledig beheer |Dit object 
|Toestaan |Ondernemingsadministrators |Volledig beheer |Dit object 
|Toestaan |Domeinadministrators |Volledig beheer |Dit object 
|Toestaan |Beheerders |Volledig beheer |Dit object 
|Toestaan |Ondernemings domein controllers |Inhoud weer geven |Dit object 
|Toestaan |Ondernemings domein controllers |Alle eigenschappen lezen |Dit object 
|Toestaan |Ondernemings domein controllers |Lees machtigingen |Dit object 
|Toestaan |Geverifieerde gebruikers |Inhoud weer geven |Dit object 
|Toestaan |Geverifieerde gebruikers |Alle eigenschappen lezen |Dit object 
|Toestaan |Geverifieerde gebruikers |Lees machtigingen |Dit object 

## <a name="next-steps"></a>Volgende stappen
- [Azure AD Connect: accounts en machtigingen](reference-connect-accounts-permissions.md)
- [Snelle installatie](how-to-connect-install-express.md)
- [Aangepaste installatie](how-to-connect-install-custom.md)
- [ADSyncConfig-verwijzing](reference-connect-adsyncconfig.md)