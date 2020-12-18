---
title: 'Azure AD Connect: ADSyncTools Power shell-referentie | Microsoft Docs'
description: Dit document bevat referentie-informatie voor de Power shell-module ADSyncTools. psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1652c52dcc6870e396d74a2f38fe63c304d37df5
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672265"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools Power shell-referentie
De volgende documentatie bevat referentie-informatie voor de Power shell-module ADSyncTools. psm1, die deel uitmaakt van Azure AD Connect.

## <a name="install-the-adsynctools-powershell-module"></a>De ADSyncTools Power shell-module installeren
Ga als volgt te werk om de ADSyncTools Power shell-module te installeren:

1.  Windows Power shell openen met beheer upaccount
2.  Typ of kopieer en plak het volgende: 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Druk op ENTER.
4.  Als u wilt controleren of de module is geïnstalleerd, voert u de volgende in of kopieert en plakt u deze:
    ```powershell
    Get-module AdSyncTools
    ```
5.  U ziet nu informatie over de module.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMENVATTING
De mS-DS-ConsistencyGuid wissen van een AD-gebruiker

### <a name="syntax"></a>SYNTAXIS

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
De waarde in mS-DS-ConsistencyGuid voor de doel-AD-gebruiker wissen

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doel gebruiker in AD om in te stellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>SAMENVATTING
{{De samen vatting invullen}}

### <a name="syntax"></a>SYNTAXIS

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Hier een voorbeeld beschrijving toevoegen}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>SAMENVATTING
{{De samen vatting invullen}}

### <a name="syntax"></a>SYNTAXIS

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Hier een voorbeeld beschrijving toevoegen}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-database"></a>-Data Base
{{Fill Data Base Description}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instantie
{{Beschrijving van opvul exemplaar}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Beschrijving van het opvul wachtwoord}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Beschrijving van de opvul server}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-GebruikersNaam
{{Beschrijving van gebruikers naam Fill}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMENVATTING
ConsistencyGuid rapport exporteren

### <a name="syntax"></a>SYNTAXIS

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Genereert een ConsistencyGuid-rapport op basis van een import CSV-bestand van Import-ADSyncToolsImmutableIdMigration

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Alternatieve aanmeldings-ID gebruiken (e-mail adres)

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Uitvoer
Uitvoer bestandsnaam voor CSV-en logboek bestanden

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>SAMENVATTING
{{De samen vatting invullen}}

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Hier een voorbeeld beschrijving toevoegen}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-Hostnaam
{{Beschrijving van hostnaam vullen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>SAMENVATTING
Gebruiker van AD ophalen

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Retourneert een AD-object dat moet worden uitgevoerd: ondersteuning voor meerdere forests

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doel gebruiker in AD om ConsistencyGuid in te stellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMENVATTING
De mS-DS-ConsistencyGuid ophalen van een AD-gebruiker

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Retourneert de waarde in het kenmerk mS-DS-ConsistencyGuid van de doel-AD-gebruiker in de GUID-indeling

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doel gebruiker in AD om in te stellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>SAMENVATTING
De ObjectGuid ophalen van een AD-gebruiker

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Retourneert de waarde in het kenmerk ObjectGUID van de doel-AD-gebruiker in de GUID-indeling

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doel gebruiker in AD om in te stellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>SAMENVATTING
Geschiedenis van AAD Connect run ophalen

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Functie die de AAD Connect-uitvoerings geschiedenis in XML-indeling retourneert

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-days"></a>-Dagen
{{Beschrijving van de opvul dagen}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>SAMENVATTING
Gebruikers met gewijzigde Source Anchor-fouten ophalen

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Functie query's AAD Connect run geschiedenis en exporteert alle gebruikers die de fout rapporteren: het kenmerk source Anchor is gewijzigd.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
#Required Parameters
```

$sourcePath = Read-Host-prompt Geef het pad naar het logboek bestand op met de bestands naam "#" \<Source_Path\> "$outputPath = Read-Host-prompt" Voer het pad naar het bestand in met de bestands naam "#" \<Out_Path\> "

 Get-ADSyncToolsUsersSourceAnchorChanged-bronpad $sourcePath-outputPath $outputPath

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-sourcepath"></a>-Bronpad
{{Fill bronpad Description}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Opvulling outputPath beschrijving}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>SAMENVATTING
ImmutableID importeren uit AAD

### <a name="syntax"></a>SYNTAXIS

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Genereert een bestand met alle met Azure AD gesynchroniseerde gebruikers met de waarde ImmutableID in GUID-indeling: MSOnline Power shell-module

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-output"></a>-Uitvoer
Uitvoer CSV-bestand

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Gesynchroniseerde gebruikers ophalen uit Azure AD-Prullenbak

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>SAMENVATTING
{{De samen vatting invullen}}

### <a name="syntax"></a>SYNTAXIS

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Hier een voorbeeld beschrijving toevoegen}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-query"></a>-Query uitvoeren
{{Query beschrijving voor Fill}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Opvulling SqlConnection beschrijving}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SAMENVATTING
Script voor het verwijderen van verlopen certificaten uit het UserCertificate-kenmerk

### <a name="syntax"></a>SYNTAXIS

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met dit script worden alle objecten van een doel organisatie-eenheid in uw Active Directory domein gefilterd op object klasse (gebruiker/computer) en worden alle verlopen certificaten verwijderd die aanwezig zijn in het kenmerk UserCertificate.
Standaard (BackupOnly-modus) maakt het alleen back-ups van verlopen certificaten naar een bestand en worden er geen wijzigingen in AD aangebracht.
Als u-BackupOnly gebruikt $false dan wordt een verlopen certificaat dat aanwezig is in het kenmerk UserCertificate voor deze objecten, verwijderd uit AD nadat het naar het bestand is gekopieerd.
Er wordt een back-up van elk certificaat gemaakt met een gescheiden bestands naam: ObjectClass_ObjectGUID_CertThumprint. CER het script maakt ook een logboek bestand in CSV-indeling, waarin alle gebruikers worden weer gegeven met certificaten die geldig of verlopen zijn, inclusief de daad werkelijke uitgevoerde actie (overgeslagen/geëxporteerd/verwijderd).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates-TargetOU ' OE = users, OU = Corp, DC = contoso, DC = com '-gebruiker class object

#### <a name="example-2"></a>VOOR BEELD 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OE = computers, OE = Corp, DC = contoso, DC = com"-object class computer-BackupOnly $false

### <a name="parameters"></a>PARAMETERS

#### <a name="-targetou"></a>-TargetOU
Doel-OE voor het opzoeken van AD-objecten

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly worden geen certificaten uit AD verwijderd

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-Object klasse
Filter object klasse

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>SAMENVATTING
Korte beschrijving

### <a name="syntax"></a>SYNTAXIS

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>BESCHRIJVING
Lange beschrijving

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>SAMENVATTING
{{De samen vatting invullen}}

### <a name="syntax"></a>SYNTAXIS

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Hier een voorbeeld beschrijving toevoegen}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-Hostnaam
{{Beschrijving van hostnaam vullen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>SAMENVATTING
(taak) Hiermee wordt het AD UserCertificate-kenmerk teruggezet vanuit een certificaat bestand

### <a name="syntax"></a>SYNTAXIS

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>BESCHRIJVING
Lange beschrijving

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>SAMENVATTING
MS-DS-ConsistencyGuid instellen voor een AD-gebruiker

### <a name="syntax"></a>SYNTAXIS

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Een waarde instellen in het kenmerk mS-DS-ConsistencyGuid voor de AD-doel gebruiker

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-user"></a>-User
Doel gebruiker in AD om ConsistencyGuid in te stellen

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Waarde
ImmutableId (byte matrix, GUID, GUID-teken reeks of base64-teken reeks)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>SAMENVATTING
{{De samen vatting invullen}}

### <a name="syntax"></a>SYNTAXIS

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>BESCHRIJVING
{{Vul de beschrijving in}}

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>Voorbeeld 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Hier een voorbeeld beschrijving toevoegen}}

### <a name="parameters"></a>PARAMETERS

#### <a name="-hostname"></a>-Hostnaam
{{Beschrijving van hostnaam vullen}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-poort
{{Beschrijving van de opvul poort}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>SAMENVATTING
Hiermee maakt u een tracerings bestand van en een AD-import stap

### <a name="syntax"></a>SYNTAXIS

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Traceert alle LDAP-query's van een AAD Connect AD-import uitvoering van een opgegeven AD-watermerk controlepunt (partitie cookie). Hiermee maakt u een tracerings bestand '. \ ADimportTrace_yyyyMMddHHmmss. log ' in de huidige map.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Opvulling ADConnectorXML beschrijving}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-DC
XML-bestand van de AD-connector exporteren

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Doel domein controller

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
DN van de forest-hoofdmap

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Typen AD-objecten die moeten worden getraceerd \> * = alle object typen

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
Als domein Administrator al wordt uitgevoerd, hoeft u geen AD-referenties op te geven.
Hand matige invoer van water merk, in plaats van XML-bestand bijvoorbeeld $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA (...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>SAMENVATTING
Korte beschrijving

### <a name="syntax"></a>SYNTAXIS

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Lange beschrijving

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-context"></a>-Context
Beschrijving van param1 Help

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Beschrijving van Param2-Help

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Beschrijving van Param2-Help

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Beschrijving van Param2-Help

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>SAMENVATTING
Hiermee worden gebruikers bijgewerkt met de nieuwe ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SYNTAXIS

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Hiermee worden gebruikers bijgewerkt met de nieuwe ConsistencyGuid-waarde (ImmutableId) die is gemaakt van het ConsistencyGuid-rapport deze functie ondersteunt de WhatIf-switch Opmerking: ConsistencyGuid rapport moet worden geïmporteerd met tab Demiliter

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-distinguishedname"></a>-DN
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Actie
Bewerking

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Uitvoer
Uitvoer bestandsnaam voor logboek bestanden

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert.
De cmdlet wordt niet uitgevoerd.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Hiermee wordt u gevraagd om bevestiging voordat u de cmdlet uitvoert.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .
