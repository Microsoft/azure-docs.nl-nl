---
title: Data exfiltration beperken tot Azure Storage-Azure PowerShell
description: In dit artikel leert u hoe u de exfiltration van virtuele netwerken kunt beperken en beperken tot Azure Storage resources met een virtueel netwerk service Endpoint-beleid met behulp van Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: d8e300c9be8f3e59dc9443bf1f1806e4228992ad
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790356"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Gegevens exfiltration beheren om accounts te Azure Storage met het eindpunt beleid van een virtueel netwerk met behulp van Azure PowerShell

Met het eindpunt beleid van de virtuele netwerk service kunt u toegangs beheer Toep assen op Azure Storage accounts vanuit een virtueel netwerk via service-eind punten. Dit is een sleutel voor het beveiligen van uw workloads, het beheren van de opslag accounts die zijn toegestaan en het toestaan van gegevens exfiltration.
In dit artikel leert u het volgende:

* Maak een virtueel netwerk.
* Voeg een subnet toe en schakel service-eind punten in voor Azure Storage.
* Maak twee Azure Storage accounts en sta netwerk toegang toe vanuit het hierboven gemaakte subnet.
* Maak een service-eindpunt beleid om toegang tot een van de opslag accounts toe te staan.
* Een virtuele machine (VM) implementeren in het subnet.
* Controleer de toegang tot het toegestane opslag account vanuit het subnet.
* Controleren of de toegang is geweigerd voor het niet-toegestane opslag account van het subnet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, moet u voor dit artikel gebruikmaken van de Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Voordat u een virtueel netwerk maakt, moet u een resource groep maken voor het virtuele netwerk en alle andere resources die in dit artikel zijn gemaakt. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voor beeld wordt een virtueel netwerk met de naam *myVirtualNetwork* gemaakt met het adres voorvoegsel *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen

Maak een subnet in het virtuele netwerk. In dit voor beeld wordt een subnet met de naam *privé* gemaakt met een service-eind punt voor *micro soft. Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Netwerk toegang voor het subnet beperken

Beveiligings regels voor de netwerk beveiligings groep maken met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Met de volgende regel is uitgaande toegang mogelijk tot de open bare IP-adressen die zijn toegewezen aan de Azure Storage-service: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Met de volgende regel wordt de toegang geweigerd tot alle open bare IP-adressen. De vorige regel overschrijft deze regel vanwege de hogere prioriteit, waardoor toegang tot de open bare IP-adressen van Azure Storage mogelijk is.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Met de volgende regel kan vanaf elke locatie Remote Desktop Protocol (RDP) verkeer van het subnet binnenkomen. Extern bureau blad-verbindingen zijn toegestaan voor het subnet, zodat u de netwerk toegang tot een bron in een latere stap kunt bevestigen.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Maak een netwerkbeveiligingsgroep met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). In het volgende voor beeld wordt een netwerk beveiligings groep gemaakt met de naam *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Koppel de netwerk beveiligings groep aan het *persoonlijke* subnet met [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) en schrijf vervolgens de configuratie van het subnet naar het virtuele netwerk. In het volgende voor beeld wordt de *myNsgPrivate* -netwerk beveiligings groep gekoppeld aan het *privé* -subnet:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Netwerk toegang tot Azure Storage accounts beperken

De stappen die nodig zijn om netwerktoegang te beperken tot resources die zijn gemaakt met Azure-services waarvoor service-eindpunten zijn ingeschakeld, verschillen per service. Zie de documentatie voor afzonderlijke services voor specifieke stappen voor elke service. De rest van dit artikel bevat stappen voor het beperken van de netwerk toegang voor een Azure Storage-account, zoals een voor beeld.

### <a name="create-two-storage-accounts"></a>Twee opslag accounts maken

Maak een Azure-opslag account met [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Wanneer het opslag account is gemaakt, haalt u de sleutel voor het opslag account op in een variabele met [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

De sleutel wordt gebruikt om een bestands share in een latere stap te maken. Voer `$storageAcctKey` de waarde in en noteer deze, omdat u deze ook hand matig moet invoeren in een latere stap wanneer u de bestands share toewijst aan een station in een VM.

Herhaal nu de bovenstaande stappen om een tweede opslag account te maken.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Haal ook de sleutel van het opslag account op van dit account om later een bestands share te maken.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Een bestands share maken in elk opslag account

Maak een context voor uw opslag account en de sleutel met [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). In de context zijn de naam van het opslag account en de account sleutel ingekapseld:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Maak een bestands share met [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Alle netwerk toegang tot een opslag account weigeren

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk. Als u de toegang tot geselecteerde netwerken wilt beperken, wijzigt u de standaard actie voor *weigeren* met [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Nadat de netwerktoegang is geweigerd, is het opslagaccount niet via elk netwerk toegankelijk.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Netwerk toegang alleen inschakelen vanuit het VNet-subnet

Haal het gemaakte virtuele netwerk op met [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) en haal het privé-subnet object vervolgens op in een variabele met [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Netwerk toegang tot het opslag account via het *persoonlijke* subnet toestaan met [add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Beleid Toep assen om toegang tot een geldig opslag account toe te staan

Om ervoor te zorgen dat de gebruikers in het virtuele netwerk alleen toegang hebben tot de Azure Storage accounts die veilig zijn en mogen worden toegestaan, kunt u een service-eindpunt beleid maken met de lijst met toegestane opslag accounts in de definitie. Dit beleid wordt vervolgens toegepast op het subnet van het virtuele netwerk dat is verbonden met de opslag via service-eind punten.

### <a name="create-a-service-endpoint-policy"></a>Een service-eindpunt beleid maken

In deze sectie wordt de beleids definitie gemaakt met de lijst met toegestane resources voor toegang via service-eind punten

De resource-ID ophalen voor het eerste (toegestane) opslag account 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

De beleids definitie maken om de bovenstaande resource toe te staan

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Het service-eindpunt beleid maken met behulp van de beleids definitie die hierboven is gemaakt

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Het service-eindpunt beleid koppelen aan het subnet van het virtuele netwerk

Nadat u het service-eindpunt beleid hebt gemaakt, koppelt u dit aan het doel-subnet met de configuratie van het service-eind punt voor Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Toegangs beperking voor Azure Storage accounts valideren

### <a name="deploy-the-virtual-machine"></a>De virtuele machine implementeren

Als u de netwerk toegang tot een opslag account wilt testen, implementeert u een virtuele machine in het subnet.

Maak een virtuele machine in het *persoonlijke* subnet met [New-AzVM](/powershell/module/az.compute/new-azvm). Wanneer de volgende opdracht wordt uitgevoerd, wordt u gevraagd referenties op te geven. De waarden die u invoert, worden geconfigureerd als de gebruikersnaam en het wachtwoord voor de virtuele machine. Met de optie `-AsJob` wordt de virtuele machine op de achtergrond gemaakt, zodat u kunt doorgaan met de volgende stap.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Uitvoer die vergelijkbaar is met de volgende voorbeeld uitvoer wordt geretourneerd:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Toegang tot het *toegestane* opslag account bevestigen

Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van een virtuele machine op te halen. In het volgende voor beeld wordt het open bare IP-adres van de *VM myvmprivate* -VM geretourneerd:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Vervang `<publicIpAddress>` in de volgende opdracht door het openbare IP-adres dat met de vorige opdracht is geretourneerd en voer vervolgens de volgende opdracht in:

```powershell
mstsc /v:<publicIpAddress>
```

Er wordt een Remote Desktop Protocol-bestand (.rdp) gemaakt en gedownload naar uw computer. Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM. Selecteer **OK**. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.

Wijs op de *VM myvmprivate* -VM de Azure-bestands share van het toegestane opslag account toe aan station Z met behulp van Power shell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell retourneert uitvoer die er ongeveer zo uitziet als in dit voorbeeld:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

De Azure-bestandsshare is toegewezen aan station Z.

Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Bevestigen dat toegang wordt geweigerd voor *niet-toegestaan* opslag account

Probeer op dezelfde *VM myvmprivate* -VM de Azure-bestands share toe te wijzen aan station X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

De toegang tot de share is geweigerd en er wordt een fout bericht weer gegeven `New-PSDrive : Access is denied` . De toegang is geweigerd omdat het *notallowedaccount* van het opslag account zich niet in de lijst met toegestane resources in het service-eindpunt beleid bevindt. 

Sluit de externe bureaubladsessie naar de VM *myVmPublic*.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een service-eindpunt beleid toegepast op een Azure Virtual Network-Service-eind punt naar Azure Storage. U hebt Azure Storage accounts en beperkte netwerk toegang tot alleen bepaalde opslag accounts gemaakt (en dus ook andere) van een subnet van een virtueel netwerk. Zie voor meer informatie over service-eindpunt beleid [overzicht van service-eind punten](virtual-network-service-endpoint-policies-overview.md).
