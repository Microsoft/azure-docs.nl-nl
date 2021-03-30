---
title: 'Azure AD Connect: ADSyncConfig Power shell-referentie | Microsoft Docs'
description: Dit document bevat referentie-informatie voor de Power shell-module ADSyncConfig. psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2126aceba8724b46de094d14db754d704500c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85850972"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Azure AD Connect: ADSyncConfig Power shell-referentie
De volgende documentatie bevat referentie-informatie voor de Power shell-module ADSyncConfig. psm1, die deel uitmaakt van Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>SAMENVATTING
Hiermee worden de account naam en het domein opgehaald die in elke AD-connector zijn geconfigureerd

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>BESCHRIJVING
Deze functie maakt gebruik van de cmdlet Get-ADSyncConnector die aanwezig is in AAD Connect om op te halen uit de verbindings parameters een tabel met de AD connector (s)-account.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>SAMENVATTING
Hiermee worden AD-objecten opgehaald waarvoor de overname van machtigingen is uitgeschakeld

### <a name="syntax"></a>SYNTAXIS

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Zoekt in AD vanuit de para meter SearchBase en retourneert alle objecten, gefilterd op de para meter object class, waarvoor de ACL-overname momenteel is uitgeschakeld.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
Objecten zoeken met uitgeschakelde overname in het domein contoso (standaard retourneert alleen ' organizationalUnit-objecten)
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'
```

#### <a name="example-2"></a>VOOR BEELD 2
Gebruikers objecten met uitgeschakelde overname in domein ' Contoso ' zoeken
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'
```

#### <a name="example-3"></a>VOOR BEELD 3
Alle typen objecten zoeken met uitgeschakelde overname in een organisatie-eenheid
```
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'
```



### <a name="parameters"></a>PARAMETERS

#### <a name="-searchbase"></a>-SearchBase
De SearchBase voor de LDAP-query die een AD-domein DN of een FQDN kan zijn

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

#### <a name="-objectclass"></a>-Object klasse
De klasse van de objecten die u wilt zoeken. Dit kan ' * ' zijn (voor elke object klasse), ' gebruiker ', ' groep ', ' container ', enzovoort. Deze functie zoekt standaard naar de object klasse ' organizationalUnit '.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en-domein voor algemene Lees machtigingen.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>User Domain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met de functie Set-ADSyncBasicReadPermissions krijgt u de vereiste machtigingen voor het AD-synchronisatie account, waaronder de volgende:
1.
Lees de eigenschap toegang voor alle kenmerken voor alle onderliggende computer objecten
2.
Lees de eigenschap toegang voor alle kenmerken voor alle objecten van het onderliggende apparaat
3.
Lees de eigenschap toegang voor alle kenmerken voor alle onderliggende foreignsecurityprincipal-objecten
5.
Lees de eigenschap toegang voor alle kenmerken voor alle onderliggende gebruikers objecten
6.
Lees de eigenschap toegang voor alle kenmerken voor alle onderliggende inetOrgPerson-objecten
7.
Lees de eigenschap toegang voor alle kenmerken voor alle onderliggende groeps objecten
8.
Lees de eigenschap toegang voor alle kenmerken voor alle objecten van de onderliggende contact persoon

Deze machtigingen worden toegepast op alle domeinen in het forest.
U kunt desgewenst een DN-naam opgeven in de ADobjectDN-para meter om deze machtigingen alleen voor dat AD-object in te stellen (met inbegrip van overname op subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOOR BEELD 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOOR BEELD 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
De naam van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Het domein van het Active Directory account dat wordt of wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DN van het Active Directory-account dat door Azure AD Connect Sync wordt gebruikt om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DN-naam van het AD-doel object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele para meter om aan te geven of de AdminSDHolder-container niet moet worden bijgewerkt met deze machtigingen

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

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en-domein voor hybride Exchange-functie.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>User Domain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met de functie Set-ADSyncExchangeHybridPermissions krijgt u de vereiste machtigingen voor het AD-synchronisatie account, waaronder de volgende:
1.
Lezen/schrijven-eigenschaps toegang voor alle kenmerken voor alle onderliggende gebruikers objecten
2.
Lezen/schrijven-eigenschaps toegang voor alle kenmerken voor alle onderliggende inetOrgPerson-objecten
3.
Lezen/schrijven-eigenschaps toegang voor alle kenmerken voor alle onderliggende groeps objecten
4.
Lezen/schrijven-eigenschaps toegang voor alle kenmerken voor alle objecten van de onderliggende contact persoon

Deze machtigingen worden toegepast op alle domeinen in het forest.
U kunt desgewenst een DN-naam opgeven in de ADobjectDN-para meter om deze machtigingen alleen voor dat AD-object in te stellen (met inbegrip van overname op subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOOR BEELD 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOOR BEELD 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
De naam van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Het domein van het Active Directory account dat wordt of wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DN van het Active Directory-account dat door Azure AD Connect Sync wordt gebruikt om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DN-naam van het AD-doel object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele para meter om aan te geven of de AdminSDHolder-container niet moet worden bijgewerkt met deze machtigingen

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

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory forest en domein voor de functie open bare mappen van Exchange mail.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>User Domain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met de functie Set-ADSyncExchangeMailPublicFolderPermissions krijgt u de vereiste machtigingen voor het AD-synchronisatie account, waaronder de volgende:
1.
Lees de eigenschap toegang voor alle kenmerken voor alle onderliggende publicfolder-objecten

Deze machtigingen worden toegepast op alle domeinen in het forest.
U kunt desgewenst een DN-naam opgeven in de ADobjectDN-para meter om deze machtigingen alleen voor dat AD-object in te stellen (met inbegrip van overname op subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOOR BEELD 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOOR BEELD 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
De naam van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Het domein van het Active Directory account dat wordt of wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DN van het Active Directory-account dat door Azure AD Connect Sync wordt gebruikt om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DN-naam van het AD-doel object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele para meter om aan te geven of de AdminSDHolder-container niet moet worden bijgewerkt met deze machtigingen

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

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en-domein voor de functie mS-DS-ConsistencyGuid.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>User Domain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met de functie Set-ADSyncMsDsConsistencyGuidPermissions krijgt u de vereiste machtigingen voor het AD-synchronisatie account, waaronder de volgende:
1.
Toegang tot eigenschap lezen/schrijven voor het mS-DS-ConsistencyGuid-kenmerk voor alle onderliggende gebruikers objecten

Deze machtigingen worden toegepast op alle domeinen in het forest.
U kunt desgewenst een DN-naam opgeven in de ADobjectDN-para meter om deze machtigingen alleen voor dat AD-object in te stellen (met inbegrip van overname op subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOOR BEELD 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOOR BEELD 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
De naam van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Het domein van het Active Directory account dat wordt of wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DN van het Active Directory-account dat door Azure AD Connect Sync wordt gebruikt om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DN-naam van het AD-doel object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele para meter om aan te geven of de AdminSDHolder-container niet moet worden bijgewerkt met deze machtigingen

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

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en-domein voor het synchroniseren van wacht woord-hashes.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>User Domain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met de functie Set-ADSyncPasswordHashSyncPermissions krijgt u de vereiste machtigingen voor het AD-synchronisatie account, waaronder de volgende:
1.
Directory wijzigingen repliceren
2.
Directory wijzigingen repliceren

Deze machtigingen worden toegewezen aan alle domeinen in het forest.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
De naam van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Het domein van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DN van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
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

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en-domein voor het terugschrijven van wacht woorden van Azure AD.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>User Domain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met de functie Set-ADSyncPasswordWritebackPermissions krijgt u de vereiste machtigingen voor het AD-synchronisatie account, waaronder de volgende:
1.
Wacht woord opnieuw instellen voor onderliggende gebruikers objecten
2.
Eigenschap toegang schrijven voor het kenmerk lockoutTime voor alle onderliggende gebruikers objecten
3.
Eigenschap toegang schrijven voor het kenmerk pwdLastSet voor alle onderliggende gebruikers objecten

Deze machtigingen worden toegepast op alle domeinen in het forest.
U kunt desgewenst een DN-naam opgeven in de ADobjectDN-para meter om deze machtigingen alleen voor dat AD-object in te stellen (met inbegrip van overname op subobjecten).

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOOR BEELD 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOOR BEELD 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
De naam van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Het domein van het Active Directory account dat wordt of wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DN van het Active Directory-account dat door Azure AD Connect Sync wordt gebruikt om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DN-naam van het AD-doel object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele para meter om aan te geven of de AdminSDHolder-container niet moet worden bijgewerkt met deze machtigingen

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

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>SAMENVATTING
De machtigingen voor een AD-object verhogen dat niet anderszins is opgenomen in een AD-beveiligde beveiligings groep.
Een typisch voor beeld is het AD Connect-account (MSOL) dat door AAD Connect automatisch wordt gemaakt.
Dit account heeft gerepliceerde machtigingen op alle domeinen. Dit kan echter eenvoudig worden aangetast omdat het niet is beveiligd.

### <a name="syntax"></a>SYNTAXIS

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met de functie Set-ADSyncRestrictedPermissions worden machtigingen uitgebreid met het gegeven account.
Het verhogen van de machtigingen bestaat uit de volgende stappen:
1. Overname uitschakelen voor het opgegeven object
2. Verwijder alle Ace's van het specifieke object, met uitzonde ring van Ace's die specifiek voor zichzelf zijn.
We willen de standaard machtigingen intact houden wanneer het gaat om zelf.
3. Wijs deze specifieke machtigingen toe:

   | Type | Naam | Access | Van toepassing op |
   |------|------|--------|------------|
   | Toestaan | SYSTEEM | Volledig beheer | Dit object |
   | Toestaan | Ondernemingsadministrators | Volledig beheer | Dit object |
   | Toestaan | Domeinadministrators | Volledig beheer | Dit object | 
   | Toestaan | Beheerders | Volledig beheer | Dit object |
   | Toestaan | Ondernemings domein controllers | Inhoud weer geven <br> Alle eigenschappen lezen <br> Lees machtigingen | Dit object |
   | Toestaan | Geverifieerde gebruikers | Inhoud weer geven <br> Alle eigenschappen lezen <br> Lees machtigingen | Dit object |

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DN-namen van het Active Directory-account waarvan de machtigingen moeten worden gescherpt.
Dit is doorgaans het MSOL_nnnnnnnnnn account of een aangepast domein account dat in uw AD-connector is geconfigureerd.

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

#### <a name="-credential"></a>-Credential
Beheerders referenties met de benodigde bevoegdheden voor het beperken van de machtigingen voor het ADConnectorAccountDN-account. Dit is doorgaans de beheerder van de onderneming of het domein. Gebruik de Fully Qualified Domain Name van het Administrator-account om fouten bij het opzoeken van accounts te voor komen.
Voor beeld: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
Wanneer DisableCredentialValidation wordt gebruikt, wordt met de functie niet gecontroleerd of de referenties die zijn ingevoerd in-Credential, geldig zijn in AD en als het account dat is gegeven de benodigde bevoegdheden heeft om de machtigingen voor het ADConnectorAccountDN-account te beperken.

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

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>SAMENVATTING
Initialiseer uw Active Directory-forest en-domein voor het terugschrijven van groepen vanuit Azure AD.

### <a name="syntax"></a>SYNTAXIS

#### <a name="userdomain"></a>User Domain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Met de functie Set-ADSyncUnifiedGroupWritebackPermissions krijgt u de vereiste machtigingen voor het AD-synchronisatie account, waaronder de volgende:
1.
Algemene voor lezen/schrijven, verwijderen, verwijderen van structuur en Create\Delete onderliggend item voor alle object typen en subobjecten voor groepen

Deze machtigingen worden toegepast op alle domeinen in het forest.
U kunt desgewenst een DN-naam opgeven in de ADobjectDN-para meter om deze machtigingen alleen voor dat AD-object in te stellen (met inbegrip van overname op subobjecten).
In dit geval is ADobjectDN de DN-naam van de container die u wilt koppelen met de functie GroupWriteback.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>VOOR BEELD 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>VOOR BEELD 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>VOOR BEELD 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
De naam van het Active Directory-account dat wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Het domein van het Active Directory account dat wordt of wordt gebruikt door Azure AD Connect Sync om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
De DN van het Active Directory-account dat door Azure AD Connect Sync wordt gebruikt om objecten in de Directory te beheren.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DN-naam van het AD-doel object om machtigingen in te stellen (optioneel)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Optionele para meter om aan te geven of de AdminSDHolder-container niet moet worden bijgewerkt met deze machtigingen

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

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>SAMENVATTING
Hiermee worden machtigingen van een opgegeven AD-object weer gegeven.

### <a name="syntax"></a>SYNTAXIS

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>BESCHRIJVING
Deze functie retourneert alle AD-machtigingen die momenteel zijn ingesteld voor een bepaald AD-object dat is opgegeven in de para meter-ADobjectDN.
De ADobjectDN moet worden vermeld in een DN-indeling.

### <a name="examples"></a>VOORBEELDEN

#### <a name="example-1"></a>VOOR BEELD 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Opvulling ADobjectDN beschrijving}}

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

#### <a name="commonparameters"></a>CommonParameters
Deze cmdlet biedt ondersteuning voor de meest gebruikte parameters: -Debug, - ErrorAction, - ErrorVariable, - InformationAction, -InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable - Verbose, - WarningAction en -WarningVariable.
Zie about_CommonParameters (voor meer informatie https://go.microsoft.com/fwlink/?LinkID=113216) .
