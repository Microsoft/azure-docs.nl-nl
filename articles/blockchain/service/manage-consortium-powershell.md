---
title: Leden van Azure Block Chain Service consortium beheren-Power shell
description: Meer informatie over het beheren van Azure Block Chain Service consortium-leden met behulp van Azure PowerShell.
ms.date: 10/14/2019
ms.topic: how-to
ms.reviewer: zeyadr
ms.openlocfilehash: d40e55f177bda9edb40383b6e2c61c32633cd005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85211337"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Consortium leden beheren in azure Block Chain service met Power shell

U kunt Power shell gebruiken voor het beheren van Block Chain consortium-leden voor uw Azure Block Chain-service. Leden met beheerders bevoegdheden kunnen rollen uitnodigen, toevoegen, verwijderen en wijzigen voor alle deel nemers in het block Chain consortium. Leden met gebruikers bevoegdheden kunnen alle deel nemers in het block Chain consortium bekijken en hun leden weergave naam wijzigen.

## <a name="prerequisites"></a>Vereisten

* Maak een Block Chain-lid met behulp van de [Azure Portal](create-member.md).
* Zie [Azure Block Chain Service consortium](consortium.md)voor meer informatie over consortiums, leden en knoop punten.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

U kunt Cloud Shell ook openen in een afzonderlijk browser tabblad door naar [shell.Azure.com/powershell](https://shell.azure.com/powershell)te gaan. Selecteer **kopiëren** om de blokken code te kopiëren, plak deze in Cloud shell en selecteer **Enter** om het programma uit te voeren.

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

Installeer het Microsoft.AzureBlockchainService.ConsortiumManagement.PS-pakket van de PowerShell Gallery.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>De gegevens voorkeur instellen

U kunt meer informatie krijgen wanneer u de cmdlets uitvoert door de voorkeurs variabele informatie in te stellen. *$InformationPreference* is standaard ingesteld op *SilentlyContinue*.

Voor meer uitgebreide informatie over cmdlets, stelt u de voor keur in Power shell als volgt in:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Een Web3-verbinding tot stand brengen

Als u consortium leden wilt beheren, moet u een Web3-verbinding maken met het eind punt van uw Block Chain service-lid. U kunt dit script gebruiken om globale variabelen in te stellen voor het aanroepen van de consortium beheer-cmdlets.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Vervang door *\<Member account password\>* het wacht woord van het gebruikers account dat u hebt gebruikt bij het maken van het lid.

Zoek de andere waarden in de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar de pagina **overzicht** van de standaard Block Chain-service.

    ![Leden overzicht](./media/manage-consortium-powershell/member-overview.png)

    Vervang *\<Member account\>* en door *\<RootContract address\>* de waarden uit de portal.

1. Selecteer voor het eindpunt adres **transactie knooppunten** en selecteer vervolgens het **knoop punt standaard transactie**. Het standaard knooppunt heeft dezelfde naam als het block Chain-lid.
1. Selecteer **Verbindingsreeksen**.

    ![Verbindingsreeksen](./media/manage-consortium-powershell/connection-strings.png)

    Vervang door *\<Endpoint address\>* de waarde van **https (toegangs sleutel 1)** of **https (toegangs sleutel 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Het netwerk en de slimme contracten beheren

Gebruik de netwerk-en Smart-cmdlets om een verbinding tot stand te brengen met de slimme contracten van het block Chain-eind punt die verantwoordelijk zijn voor het consortium beheer.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Gebruik deze cmdlet om verbinding te maken met de slimme contracten van het consortium management. Deze contracten worden gebruikt voor het beheren en afdwingen van leden binnen het consortium.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| RootContractAddress | Hoofd contract adres van de consortium Management-slimme contracten | Yes |
| Web3Client | Web3Client-object verkregen van New-Web3Connection | Yes |

#### <a name="example"></a>Voorbeeld

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Gebruik deze cmdlet om een object te maken voor het opslaan van de informatie voor het beheer account van een extern knoop punt.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adres van Block Chain-lid | Yes |
| ManagedAccountPassword | Account wachtwoord | Yes |

#### <a name="example"></a>Voorbeeld

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Gebruik deze cmdlet om een verbinding tot stand te brengen met het RPC-eind punt van een transactie knooppunt.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Block Chain lid eindpunt adres | Yes |

#### <a name="example"></a>Voorbeeld

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>De consortium leden beheren

Gebruik de Management-cmdlets voor het consortium om leden in het consortium te beheren. De beschik bare acties zijn afhankelijk van uw consortium rol.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Gebruik deze cmdlet om details van leden of lijst leden van het consortium op te halen.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| Name | De naam van het service-lid van de Block Chain waarvoor u gegevens wilt ophalen. Wanneer een naam wordt ingevoerd, worden de details van het lid geretourneerd. Wanneer een naam wordt wegge laten, wordt een lijst met alle consortium leden geretourneerd. | No |
| Leden | Leden object verkregen van Import-ConsortiumManagementContracts | Yes |
| Web3Client | Web3Client-object verkregen van New-Web3Connection | Yes |

#### <a name="example"></a>Voorbeeld

Stel [een Web3-verbinding](#establish-a-web3-connection) in om de $ContractConnection variabele in te stellen.

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Voorbeelduitvoer

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Gebruik deze cmdlet om een Block Chain-lid te verwijderen.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| Name | Te verwijderen lidnaam | Yes |
| Leden | Leden object verkregen van Import-ConsortiumManagementContracts | Yes |
| Web3Account | Web3Account-object verkregen van Import-Web3Account | Yes |
| Web3Client | Web3Client-object verkregen van New-Web3Connection | Yes |

#### <a name="example"></a>Voorbeeld

Stel [een Web3-verbinding](#establish-a-web3-connection) in om de $ContractConnection-en $MemberAccount-variabelen in te stellen.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Gebruik deze cmdlet om kenmerken voor Block Chain-leden in te stellen, waaronder de weergave naam en de rol van het consortium.

Consortium beheerders kunnen **DisplayName** en **rol** instellen voor alle leden. Een consortium met de gebruikersrol kan alleen de weergave naam van hun eigen lid wijzigen.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| Name | Naam van het block Chain-lid | Yes |
| DisplayName | Nieuwe weergave naam | No |
| AccountAddress | Account adres | No |
| Leden | Leden object verkregen van Import-ConsortiumManagementContracts | Yes |
| Web3Account | Web3Account-object verkregen van Import-Web3Account | Yes |
| Web3Client |  Web3Client-object verkregen van New-Web3Connection| Yes |

#### <a name="example"></a>Voorbeeld

Stel [een Web3-verbinding](#establish-a-web3-connection) in om de $ContractConnection-en $MemberAccount-variabelen in te stellen.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Uitnodigingen van de consortium leden beheren

Gebruik de Management-cmdlets voor het consortium voor uitnodigingen van consortiums om uitnodigingen te beheren. De beschik bare acties zijn afhankelijk van uw consortium rol.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Gebruik deze cmdlet om nieuwe leden uit te nodigen voor het consortium.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-abonnements-ID van het lid dat moet worden uitgenodigd | Yes |
| Rol | De rol consortium. Waarden kunnen een beheerder of gebruiker zijn. BEHEERDER is de rol van beheerder van het consortium. De gebruiker is de rol van het consortium. | Yes |
| Leden | Leden object verkregen van Import-ConsortiumManagementContracts | Yes |
| Web3Account | Web3Account-object verkregen van Import-Web3Account | Yes |
| Web3Client | Web3Client-object verkregen van New-Web3Connection | Yes |

#### <a name="example"></a>Voorbeeld

Stel [een Web3-verbinding](#establish-a-web3-connection) in om de $ContractConnection-en $MemberAccount-variabelen in te stellen.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Gebruik deze cmdlet om de uitnodigings status van een lid van een consortium op te halen of weer te geven.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| SubscriptionId | De ID van het Azure-abonnement van het lid dat moet worden uitgenodigd. Als de abonnements-ID wordt vermeld, worden de uitnodigingen van de abonnements-ID geretourneerd. Als de abonnements-ID wordt wegge laten, wordt een lijst met alle uitnodigingen van het lid geretourneerd. | No |
| Leden | Leden object verkregen van Import-ConsortiumManagementContracts | Yes |
| Web3Client | Web3Client-object verkregen van New-Web3Connection | Yes |

#### <a name="example"></a>Voorbeeld

Stel [een Web3-verbinding](#establish-a-web3-connection) in om de $ContractConnection variabele in te stellen.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Voorbeelduitvoer

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Gebruik deze cmdlet om de uitnodiging van een lid van een consortium in te trekken.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| SubscriptionId | ID van het Azure-abonnement van het lid dat u wilt intrekken | Yes |
| Leden | Leden object verkregen van Import-ConsortiumManagementContracts | Yes |
| Web3Account | Web3Account-object verkregen van Import-Web3Account | Yes |
| Web3Client | Web3Client-object verkregen van New-Web3Connection | Yes |

#### <a name="example"></a>Voorbeeld

Stel [een Web3-verbinding](#establish-a-web3-connection) in om de $ContractConnection-en $MemberAccount-variabelen in te stellen.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Gebruik deze cmdlet om de **rol** voor een bestaande uitnodiging in te stellen. Alleen consortium beheerders kunnen uitnodigingen wijzigen.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parameter | Beschrijving | Vereist |
|-----------|-------------|:--------:|
| SubscriptionId | Azure-abonnements-ID van het lid dat moet worden uitgenodigd | Yes |
| Rol | Nieuwe consortium functie voor uitnodiging. Waarden kunnen **gebruiker** of **beheerder** zijn. | Yes |
| Leden |  Leden object verkregen van Import-ConsortiumManagementContracts | Yes |
| Web3Account | Web3Account-object verkregen van Import-Web3Account | Yes |
| Web3Client | Web3Client-object verkregen van New-Web3Connection | Yes |

#### <a name="example"></a>Voorbeeld

Stel [een Web3-verbinding](#establish-a-web3-connection) in om de $ContractConnection-en $MemberAccount-variabelen in te stellen.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Block Chain Service consortium](consortium.md) voor meer informatie over consortiums, leden en knoop punten.
