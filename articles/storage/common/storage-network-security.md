---
title: Firewalls Azure Storage virtuele netwerken configureren | Microsoft Docs
description: Configureer gelaagde netwerkbeveiliging voor uw opslagaccount met behulp Azure Storage firewalls en Azure Virtual Network.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 5e8123c252d99b2999eeef42fecae189a05e382b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778117"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage-firewalls en virtuele netwerken configureren

Azure Storage biedt een gelaagd beveiligingsmodel. Met dit model kunt u het toegangsniveau voor uw opslagaccounts beveiligen en beheren dat uw toepassingen en bedrijfsomgevingen nodig hebben, op basis van het type en de subset van de gebruikte netwerken of resources. Wanneer netwerkregels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken of via de opgegeven set Azure-resources toegang tot een opslagaccount. U kunt de toegang tot uw opslagaccount beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen, IP-bereiken, subnetten in een Azure Virtual Network (VNet) of resource-exemplaren van sommige Azure-services.

Opslagaccounts hebben een openbaar eindpunt dat toegankelijk is via internet. U kunt ook privé-eindpunten maken voor uw [opslagaccount,](storage-private-endpoints.md)waarmee een privé-IP-adres van uw VNet aan het opslagaccount wordt toegewezen en al het verkeer tussen uw VNet en het opslagaccount via een privékoppeling wordt beveiligd. De firewall van Azure Storage biedt toegangsbeheer voor het openbare eindpunt van uw opslagaccount. U kunt de firewall ook gebruiken om alle toegang via het openbare eindpunt te blokkeren wanneer u privé-eindpunten gebruikt. Met uw firewallconfiguratie voor opslag kunt u ook vertrouwde Azure-platformservices selecteren om veilig toegang te krijgen tot het opslagaccount.

Een toepassing die toegang heeft tot een opslagaccount wanneer netwerkregels van kracht zijn, vereist nog steeds de juiste autorisatie voor de aanvraag. Autorisatie wordt ondersteund Azure Active Directory referenties (Azure AD) voor blobs en wachtrijen, met een geldige accounttoegangssleutel of met een SAS-token.

> [!IMPORTANT]
> Door firewallregels in te stellen voor uw opslagaccount worden binnenkomende aanvragen voor gegevens standaard geblokkeerd, tenzij de aanvragen afkomstig zijn van een service die binnen een Azure Virtual Network (VNet) of van toegestane openbare IP-adressen werkt. Aanvragen die worden geblokkeerd, zijn onder andere aanvragen van andere Azure-services, Azure Portal, logboekregistratie- en metrische gegevensservices, en meer.
>
> U kunt toegang verlenen tot Azure-services die vanuit een VNet worden uitgevoerd door verkeer toe te staan van het subnet dat als host voor het service-exemplaar wordt gebruikt. U kunt ook een beperkt aantal scenario's inschakelen via het uitzonderingenmechanisme dat hieronder wordt beschreven. Als u toegang wilt krijgen tot gegevens van het opslagaccount via de Azure Portal, moet u zich op een computer binnen de vertrouwde grens (IP of VNet) die u hebt ingesteld.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenario's

Als u uw opslagaccount wilt beveiligen, moet u eerst een regel configureren om de toegang tot verkeer van alle netwerken (inclusief internetverkeer) op het openbare eindpunt standaard te weigeren. Vervolgens moet u regels configureren die toegang verlenen tot verkeer van specifieke VNets. U kunt ook regels configureren om toegang te verlenen tot verkeer van geselecteerde IP-adresbereiken voor openbaar internet, waardoor verbindingen van specifieke internet- of on-premises clients mogelijk worden. Met deze configuratie kunt u een beveiligde netwerkgrens voor uw toepassingen bouwen.

U kunt firewallregels combineren die toegang toestaan vanuit specifieke virtuele netwerken en vanuit openbare IP-adresbereiken in hetzelfde opslagaccount. Firewallregels voor opslag kunnen worden toegepast op bestaande opslagaccounts of bij het maken van nieuwe opslagaccounts.

Firewallregels voor Opslag zijn van toepassing op het openbare eindpunt van een opslagaccount. U hebt geen firewalltoegangsregels nodig om verkeer toe te staan voor privé-eindpunten van een opslagaccount. Het proces voor het goedkeuren van het maken van een privé-eindpunt verleent impliciete toegang tot verkeer van het subnet dat als host voor het privé-eindpunt wordt gebruikt.

Netwerkregels worden afgedwongen op alle netwerkprotocollen voor Azure-opslag, inclusief REST en SMB. Als u toegang wilt krijgen tot gegevens met behulp van hulpprogramma's zoals Azure Portal, Storage Explorer en AZCopy, moeten expliciete netwerkregels worden geconfigureerd.

Zodra netwerkregels zijn toegepast, worden ze afgedwongen voor alle aanvragen. SAS-tokens die toegang verlenen tot een specifiek IP-adres, beperken de toegang van de tokenhouder, maar verlenen geen nieuwe toegang buiten de geconfigureerde netwerkregels.

Schijfverkeer van virtuele machines (inclusief bewerkingen voor het loskoppelen en ontkoppelen van schijven) wordt niet beïnvloed door netwerkregels. REST-toegang tot pagina-blobs wordt beveiligd door netwerkregels.

Klassieke opslagaccounts bieden geen ondersteuning voor firewalls en virtuele netwerken.

U kunt niet-mande schijven in opslagaccounts gebruiken met netwerkregels die zijn toegepast om back-up te maken van VM's en deze te herstellen door een uitzondering te maken. Dit proces wordt beschreven in de [sectie Uitzonderingen beheren](#manage-exceptions) van dit artikel. Firewall-uitzonderingen zijn niet van toepassing op beheerde schijven, omdat ze al worden beheerd door Azure.

## <a name="change-the-default-network-access-rule"></a>Standaardregel voor netwerktoegang wijzigen

Standaard accepteren opslagaccounts verbindingen van clients in elk netwerk. Als u de toegang wilt beperken tot bepaalde netwerken, moet u eerst de standaardactie wijzigen.

> [!WARNING]
> Als u wijzigingen aanbrengt in de netwerkregels, kan dit van invloed zijn op de mogelijkheid van uw toepassingen om verbinding te maken met Azure Storage. Als u de standaardnetwerkregel instelt **op weigeren,** wordt alle toegang tot de gegevens blokkeert, tenzij er ook specifieke netwerkregels worden toegepast die toegang verlenen.  Zorg ervoor dat u alleen toegang verleent tot netwerken die gebruikmaken van netwerkregels voordat u de standaardregel wijzigt om de toegang te weigeren.

### <a name="managing-default-network-access-rules"></a>Standaardregels voor netwerktoegang beheren

U kunt standaardregels voor netwerktoegang voor opslagaccounts beheren via Azure Portal, PowerShell of CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar het opslagaccount dat u wilt beveiligen.

2. Selecteer in het instellingenmenu **Netwerken.**

3. Als u toegang standaard wilt weigeren, kiest u ervoor om toegang vanuit geselecteerde **netwerken toe te staan.** Als u verkeer van alle netwerken wilt toestaan, verleent u toegang vanaf **Alle netwerken**.

4. Klik op **Opslaan** om uw wijzigingen toe te passen.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [meld u aan.](/powershell/azure/authenticate-azureps)

2. De status van de standaardregel voor het opslagaccount weergeven.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Stel de standaardregel in om netwerktoegang standaard te weigeren.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Stel de standaardregel in om standaard netwerktoegang toe te staan.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure CLI en](/cli/azure/install-azure-cli) meld u [aan.](/cli/azure/authenticate-azure-cli)

2. De status van de standaardregel voor het opslagaccount weergeven.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Stel de standaardregel in om netwerktoegang standaard te weigeren.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Stel de standaardregel in om standaard netwerktoegang toe te staan.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Toegang verlenen vanuit een virtueel netwerk

U kunt opslagaccounts zo configureren dat alleen toegang vanuit specifieke subnetten wordt toegestaan. De toegestane subnetten kunnen deel uitmaken van een VNet in hetzelfde abonnement of van een ander abonnement, waaronder abonnementen die behoren tot een andere Azure Active Directory tenant.

Schakel een [service-eindpunt in](../../virtual-network/virtual-network-service-endpoints-overview.md) voor Azure Storage binnen het VNet. Het service-eindpunt routeert verkeer van het VNet via een optimaal pad naar Azure Storage service. De identiteiten van het subnet en het virtuele netwerk worden ook bij elke aanvraag verzonden. Beheerders kunnen vervolgens netwerkregels configureren voor het opslagaccount waarmee aanvragen kunnen worden ontvangen van specifieke subnetten in een VNet. Clients die via deze netwerkregels toegang krijgen, moeten blijven voldoen aan de autorisatievereisten van het opslagaccount voor toegang tot de gegevens.

Elk opslagaccount ondersteunt maximaal 200 regels voor virtuele netwerken, die kunnen worden gecombineerd met [IP-netwerkregels.](#grant-access-from-an-internet-ip-range)

### <a name="available-virtual-network-regions"></a>Beschikbare virtuele-netwerkregio's

Over het algemeen werken service-eindpunten tussen virtuele netwerken en service-exemplaren in dezelfde Azure-regio. Wanneer u service-eindpunten gebruikt met Azure Storage, neemt dit bereik toe met de [gekoppelde regio](../../best-practices-availability-paired-regions.md). Service-eindpunten bieden continuïteit tijdens een regionale failover en toegang tot ra-GRS-exemplaren (geografisch redundante opslag) met alleen-lezentoegang. Netwerkregels die vanuit een virtueel netwerk toegang verlenen tot een opslagaccount, verlenen ook toegang tot elke RA-GRS-instantie.

Bij het plannen van herstel na noodherstel tijdens een regionale storing moet u de VNets vooraf in de gekoppelde regio maken. Schakel service-eindpunten voor Azure Storage, met netwerkregels die toegang verlenen vanuit deze alternatieve virtuele netwerken. Pas deze regels vervolgens toe op uw geografisch redundante opslagaccounts.

> [!NOTE]
> Service-eindpunten zijn niet van toepassing op verkeer buiten de regio van het virtuele netwerk en het toegewezen regiopaar. U kunt alleen netwerkregels toepassen die vanuit virtuele netwerken toegang verlenen tot opslagaccounts in de primaire regio van een opslagaccount of in de aangewezen gekoppelde regio.

### <a name="required-permissions"></a>Vereiste machtigingen

Als u een regel voor een virtueel netwerk wilt toepassen op een opslagaccount, moet de gebruiker over de juiste machtigingen beschikken voor de subnetten die worden toegevoegd. Het toepassen van een regel [](../../role-based-access-control/built-in-roles.md#storage-account-contributor) kan worden uitgevoerd door een inzender voor opslagaccounts of een gebruiker die via een aangepaste Azure-rol toestemming heeft gekregen voor de bewerking van de `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [Azure-resourceprovider.](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork)

Het opslagaccount en de virtuele netwerken die toegang hebben gekregen, kunnen zich in verschillende abonnementen, waaronder abonnementen die deel uitmaken van een andere Azure AD-tenant.

> [!NOTE]
> Configuratie van regels die toegang verlenen tot subnetten in virtuele netwerken die deel uitmaken van een andere Azure Active Directory-tenant, worden momenteel alleen ondersteund via Powershell, CLI en REST API's. Dergelijke regels kunnen niet worden geconfigureerd via de Azure Portal, maar ze kunnen wel worden bekeken in de portal.

### <a name="managing-virtual-network-rules"></a>Regels voor virtuele netwerken beheren

U kunt regels voor virtuele netwerken voor opslagaccounts beheren via Azure Portal, PowerShell of CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar het opslagaccount dat u wilt beveiligen.

2. Selecteer in het instellingenmenu **Netwerken.**

3. Controleer of u hebt geselecteerd om toegang toe te staan vanuit **Geselecteerde netwerken.**

4. Als u toegang wilt verlenen tot een virtueel netwerk met een nieuwe  netwerkregel, selecteert u onder Virtuele netwerken de optie Bestaand virtueel netwerk **toevoegen,** selecteert u Opties voor virtuele netwerken en **Subnetten** en selecteert u **vervolgens Toevoegen.** Als u een nieuw virtueel netwerk wilt maken en toegang wilt verlenen, **selecteert u Nieuw virtueel netwerk toevoegen.** Geef de informatie op die nodig is om het nieuwe virtuele netwerk te maken en selecteer **vervolgens Maken.**

    > [!NOTE]
    > Als een service-eindpunt voor Azure Storage niet eerder is geconfigureerd voor het geselecteerde virtuele netwerk en de geselecteerde subnetten, kunt u dit als onderdeel van deze bewerking configureren.
    >
    > Op dit moment worden alleen virtuele netwerken die tot dezelfde tenant Azure Active Directory weergegeven voor selectie tijdens het maken van de regel. Als u toegang wilt verlenen tot een subnet in een virtueel netwerk dat behoort tot een andere tenant, gebruikt u Powershell, CLI of REST API's.

5. Als u een regel voor een virtueel netwerk of subnet wilt verwijderen, selecteert u **...** om het contextmenu voor het virtuele netwerk of subnet te openen en selecteert u **Verwijderen.**

6. Selecteer **Opslaan om** uw wijzigingen toe te passen.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [meld u aan.](/powershell/azure/authenticate-azureps)

2. Lijst met regels voor virtuele netwerken.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Schakel service-eindpunten in Azure Storage in een bestaand virtueel netwerk en subnet.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Als u een netwerkregel wilt toevoegen voor een subnet in een VNet dat tot een andere Azure AD-tenant behoort, gebruikt u een volledig gekwalificeerde **virtualNetworkResourceId-parameter** in de vorm '/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name'.

5. Verwijder een netwerkregel voor een virtueel netwerk en subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel in stelt](#change-the-default-network-access-rule) om te **weigeren,** anders hebben netwerkregels geen effect.

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure CLI en](/cli/azure/install-azure-cli) meld u [aan.](/cli/azure/authenticate-azure-cli)

2. Lijst met regels voor virtuele netwerken.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Schakel service-eindpunten in Azure Storage in een bestaand virtueel netwerk en subnet.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Als u een regel wilt toevoegen voor een subnet in een VNet dat tot een andere Azure AD-tenant behoort, gebruikt u een volledig gekwalificeerde subnet-id in de vorm "/subscriptions/ \<subscription-ID\> /resourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > U kunt de **abonnementsparameter** gebruiken om de subnet-id op te halen voor een VNet dat bij een andere Azure AD-tenant hoort.

5. Verwijder een netwerkregel voor een virtueel netwerk en subnet.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel in stelt](#change-the-default-network-access-rule) om te **weigeren,** anders hebben netwerkregels geen effect.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Toegang verlenen vanuit een ip-adresbereik op internet

U kunt IP-netwerkregels gebruiken om toegang vanuit specifieke IP-adresbereiken voor openbaar internet toe te staan door IP-netwerkregels te maken. Elk opslagaccount ondersteunt maximaal 200 regels. Deze regels verlenen toegang tot specifieke internetservices en on-premises netwerken en blokkeert algemeen internetverkeer.

De volgende beperkingen zijn van toepassing op IP-adresbereiken.

- IP-netwerkregels zijn alleen toegestaan voor openbare **IP-adressen op** internet. 

  IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in [RFC 1918),](https://tools.ietf.org/html/rfc1918#section-3)zijn niet toegestaan in IP-regels. Privénetwerken bevatten adressen die beginnen met _10.*_, _172.16.*_  -  _172.31.*_ en _192.168.*_.

- U moet toegestane internetadresbereiken met [CIDR-notatie](https://tools.ietf.org/html/rfc4632) in de notatie *16.17.18.0/24* of als afzonderlijke IP-adressen zoals *16.17.18.19 verstrekken.* 

- Kleine adresbereiken met de voorvoegselgrootten /31 of /32 worden niet ondersteund. Deze adresbereiken moeten worden geconfigureerd met behulp van afzonderlijke IP-adresregels. 

- Alleen IPV4-adressen worden ondersteund voor de configuratie van firewallregels voor opslag.

IP-netwerkregels kunnen niet worden gebruikt in de volgende gevallen:

- Om de toegang tot clients in dezelfde Azure-regio als het opslagaccount te beperken.
  
  IP-netwerkregels hebben geen invloed op aanvragen die afkomstig zijn uit dezelfde Azure-regio als het opslagaccount. Gebruik [regels voor virtuele netwerken](#grant-access-from-a-virtual-network) om aanvragen in dezelfde regio toe te staan. 

- De toegang beperken tot clients in een [gekoppelde regio](../../best-practices-availability-paired-regions.md) die zich in een VNet met een service-eindpunt.

- De toegang beperken tot Azure-services die zijn geïmplementeerd in dezelfde regio als het opslagaccount.

  Services die zijn geïmplementeerd in dezelfde regio als het opslagaccount, gebruiken privé-Azure IP-adressen voor communicatie. U kunt de toegang tot specifieke Azure-services dus niet beperken op basis van hun openbare uitgaande IP-adresbereik.

### <a name="configuring-access-from-on-premises-networks"></a>Toegang vanuit on-premises netwerken configureren

Als u via een IP-netwerkregel vanuit uw on-premises netwerken toegang wilt verlenen tot uw opslagaccount, moet u de internet gerichte IP-adressen identificeren die door uw netwerk worden gebruikt. Neem contact op met uw netwerkbeheerder voor hulp.

Als u [ExpressRoute](../../expressroute/expressroute-introduction.md) gebruikt vanuit uw on-premises netwerk voor openbare peering of Microsoft-peering, moet u de NAT IP-adressen opgeven die worden gebruikt. Voor openbare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Deze worden toegepast op Azure-serviceverkeer wanneer het verkeer het Microsoft Azure-backbone-netwerk binnenkomt. Voor Microsoft-peering worden de GEBRUIKTE NAT IP-adressen geleverd door de klant of door de serviceprovider. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Meer informatie over [NAT voor openbare peering en Microsoft-peering met ExpressRoute.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>IP-netwerkregels beheren

U kunt IP-netwerkregels voor opslagaccounts beheren via Azure Portal, PowerShell of CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar het opslagaccount dat u wilt beveiligen.

2. Selecteer in het instellingenmenu **Netwerken.**

3. Controleer of u hebt geselecteerd om toegang toe te staan vanuit **Geselecteerde netwerken.**

4. Als u toegang wilt verlenen tot een ip-adresbereik op internet, voert u het IP-adres of adresbereik in (in CIDR-indeling) onder  >  **Firewalladresbereik.**

5. Als u een IP-netwerkregel wilt verwijderen, selecteert u het prullenbakpictogram naast het adresbereik.

6. Klik op **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [meld u aan.](/powershell/azure/authenticate-azureps)

2. Lijst met IP-netwerkregels.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Voeg een netwerkregel toe voor een afzonderlijk IP-adres.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Voeg een netwerkregel toe voor een IP-adresbereik.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Verwijder een netwerkregel voor een afzonderlijk IP-adres.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Verwijder een netwerkregel voor een IP-adresbereik.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel in stelt](#change-the-default-network-access-rule) om te **weigeren** of dat netwerkregels geen effect hebben.

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure CLI en](/cli/azure/install-azure-cli) meld u [aan.](/cli/azure/authenticate-azure-cli)

1. Lijst met IP-netwerkregels.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Voeg een netwerkregel toe voor een afzonderlijk IP-adres.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Voeg een netwerkregel toe voor een IP-adresbereik.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Verwijder een netwerkregel voor een afzonderlijk IP-adres.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Verwijder een netwerkregel voor een IP-adresbereik.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel in stelt](#change-the-default-network-access-rule) om te **weigeren** of dat netwerkregels geen effect hebben.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Toegang verlenen vanuit Azure-resource-exemplaren (preview)

In sommige gevallen kan een toepassing afhankelijk zijn van Azure-resources die niet kunnen worden geïsoleerd via een virtueel netwerk of een IP-adresregel. U wilt echter nog steeds de toegang tot opslagaccounts beveiligen en beperken tot alleen de Azure-resources van uw toepassing. U kunt opslagaccounts configureren om toegang te verlenen tot specifieke resource-exemplaren van sommige Azure-services door een regel voor een resource-exemplaar te maken. 

De typen bewerkingen die een resource-exemplaar kan uitvoeren op opslagaccountgegevens, worden bepaald door de [Azure-roltoewijzingen](storage-auth-aad.md#assign-azure-roles-for-access-rights) van het resource-exemplaar. Resource-exemplaren moeten afkomstig zijn van dezelfde tenant als uw opslagaccount, maar ze kunnen deel uitmaken van elk abonnement in de tenant.

> [!NOTE]
> Deze functie is in openbare preview en is beschikbaar in alle regio's van de openbare cloud.

> [!NOTE]
> Regels voor resource-exemplaren worden momenteel alleen ondersteund voor Azure Synapse. Ondersteuning voor andere [Azure-services](#trusted-access-system-assigned-managed-identity) die worden vermeld in de sectie Vertrouwde toegang op basis van door het systeem toegewezen beheerde identiteit van dit artikel is de komende weken beschikbaar.


### <a name="portal"></a>[Portal](#tab/azure-portal)

U kunt resourcenetwerkregels toevoegen of verwijderen in de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) om aan de slag te gaan.

2. Zoek uw opslagaccount en geef het accountoverzicht weer.

3. Selecteer **Netwerken om** de configuratiepagina voor netwerken weer te geven.

4. Kies in **de vervolgkeuzelijst** Resourcetype het resourcetype van uw resource-exemplaar. 

5. Kies **de** resource-instantie in de vervolgkeuzelijst Exemplaarnaam. U kunt er ook voor kiezen om alle resource-exemplaren op te nemen in de actieve tenant, het abonnement of de resourcegroep.

6. Klik op **Opslaan** om uw wijzigingen toe te passen. Het resource-exemplaar wordt weergegeven in de **sectie Resource-exemplaren** van de pagina Netwerkinstellingen. 

Als u het resource-exemplaar wilt verwijderen, selecteert u het verwijderpictogram ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) naast het resource-exemplaar.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt PowerShell-opdrachten gebruiken om resourcenetwerkregels toe te voegen of te verwijderen.

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel in stelt](#change-the-default-network-access-rule) om te **weigeren,** anders hebben netwerkregels geen effect.

#### <a name="install-the-preview-module"></a>De preview-module installeren

Installeer de nieuwste versie van de PowershellGet-module. Sluit de PowerShell-console en open deze opnieuw.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Installeer **de Az. Storage** Preview-module.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Zie De module Azure PowerShell installeren voor meer informatie over het installeren [van PowerShell Azure PowerShell module](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>Toegang verlenen

Voeg een netwerkregel toe die toegang verleent vanuit een resource-exemplaar.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Geef meerdere resource-exemplaren tegelijk op door de netwerkregelset te wijzigen.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Toegang intrekken

Verwijder een netwerkregel die toegang verleent vanuit een resource-exemplaar.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Verwijder alle netwerkregels die toegang verlenen vanuit resource-instanties.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Een lijst met toegestane resource-instanties weergeven

Bekijk een volledige lijst met resource-exemplaren die toegang hebben gekregen tot het opslagaccount.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt Azure CLI-opdrachten gebruiken om resourcenetwerkregels toe te voegen of te verwijderen.

#### <a name="install-the-preview-extension"></a>De preview-extensie installeren

1. Open de [Azure Cloud Shell](../../cloud-shell/overview.md)of als u [](/cli/azure/install-azure-cli) de Azure CLI lokaal hebt geïnstalleerd, opent u een opdrachtconsoletoepassing zoals Windows PowerShell.

2. Controleer vervolgens of de versie van Azure CLI die u hebt geïnstalleerd, of hoger is `2.13.0` met behulp van de volgende opdracht.

   ```azurecli
   az --version
   ```

   Als uw versie van Azure CLI lager is dan `2.13.0` , installeert u een nieuwere versie. Raadpleeg [De Azure CLI installeren](/cli/azure/install-azure-cli).

3. Typ de volgende opdracht om de preview-extensie te installeren.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Toegang verlenen

Voeg een netwerkregel toe die toegang verleent vanuit een resource-exemplaar.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Toegang intrekken

Verwijder een netwerkregel die toegang verleent vanuit een resource-exemplaar.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Een lijst met toegestane resource-instanties weergeven

Bekijk een volledige lijst met resource-exemplaren die toegang hebben gekregen tot het opslagaccount.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>Toegang verlenen aan vertrouwde Azure-services 

Sommige Azure-services werken vanuit netwerken die niet kunnen worden opgenomen in uw netwerkregels. U kunt een subset van dergelijke vertrouwde Azure-services toegang verlenen tot het opslagaccount, terwijl u netwerkregels voor andere apps onderhoudt. Deze vertrouwde services gebruiken vervolgens sterke verificatie om veilig verbinding te maken met uw opslagaccount.

U kunt toegang verlenen tot vertrouwde Azure-services door een netwerkregel-uitzondering te maken. Zie de sectie Uitzonderingen beheren [](#manage-exceptions) van dit artikel voor stapsgewijs richtlijnen.

Wanneer u toegang verleent tot vertrouwde Azure-services, verleent u de volgende soorten toegang:

- Vertrouwde toegang voor geselecteerde bewerkingen voor resources die zijn geregistreerd in uw abonnement.
- Vertrouwde toegang tot resources op basis van door het systeem toegewezen beheerde identiteit.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Vertrouwde toegang voor resources die zijn geregistreerd in uw abonnement

Resources van sommige services, wanneer **ze zijn** geregistreerd in uw abonnement, hebben toegang tot uw opslagaccount **in** hetzelfde abonnement voor bepaalde bewerkingen, zoals het schrijven van logboeken of het maken van back-ups.  De volgende tabel beschrijft elke service en de toegestane bewerkingen. 

| Service                  | Naam van resourceprovider     | Bewerkingen toegestaan                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Back-ups en herstel van niet-mande schijven uitvoeren op virtuele IAAS-machines. (niet vereist voor beheerde schijven). [Meer informatie](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Hiermee kunt u gegevens importeren in Azure met behulp van Data Box. [Meer informatie](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Aangepaste installatie van installatie van installatie van afbeeldingen en artefacten. [Meer informatie](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft.EventGrid        | Schakel Blob Storage gebeurtenispublicatie in en Event Grid publiceren naar opslagwachtrijen. Meer informatie over [blob-opslaggebeurtenissen](../../event-grid/overview.md#event-sources) [en het publiceren naar wachtrijen.](../../event-grid/event-handlers.md) |
| Azure Event Hubs         | Microsoft.EventHub         | Gegevens archiveren met Event Hubs Capture. [Meer informatie](../../event-hubs/event-hubs-capture-overview.md). |
| Azure File Sync          | Microsoft.StorageSync      | Hiermee kunt u uw on-prem bestandsserver transformeren naar een cache voor Azure-bestands shares. Het toestaan van synchronisatie van meerdere plaatsen, snel herstel na noodherstel en back-ups in de cloud. [Meer informatie](../file-sync/file-sync-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | De initiële inhoud van het standaardbestandssysteem inrichten voor een nieuw HDInsight-cluster. [Meer informatie](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure Import Export      | Microsoft.ImportExport     | Hiermee schakelt u het importeren van gegevens Azure Storage importeren of exporteren van gegevens uit Azure Storage met behulp van Azure Storage Import/Export-service. [Meer informatie](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.Insights         | Hiermee kunt u bewakingsgegevens schrijven naar een beveiligd opslagaccount, waaronder resourcelogboeken, Azure Active Directory aanmeldings- en auditlogboeken en Microsoft Intune logboeken. [Meer informatie](../../azure-monitor/roles-permissions-security.md). |
| Azure-netwerken         | Microsoft.Network          | Sla netwerkverkeerslogboeken op en analyseer deze, Network Watcher en Traffic Analytics services. [Meer informatie](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Schakel replicatie in voor herstel na noodherstel van virtuele Azure IaaS-machines wanneer u cache-, bron- of doelopslagaccounts met firewall ingeschakeld gebruikt.  [Meer informatie](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Vertrouwde toegang op basis van door het systeem toegewezen beheerde identiteit

De volgende tabel bevat services die toegang kunnen hebben tot de gegevens van uw opslagaccount als de resource-exemplaren van deze services de juiste machtiging krijgen. Als u machtigingen wilt verlenen, moet u expliciet [een](storage-auth-aad.md#assign-azure-roles-for-access-rights) [Azure-rol](../../active-directory/managed-identities-azure-resources/overview.md) toewijzen aan de door het systeem toegewezen beheerde identiteit voor elk resource-exemplaar. In dit geval komt het toegangsbereik voor het exemplaar overeen met de Azure-rol die aan de beheerde identiteit is toegewezen. 

> [!TIP]
> De aanbevolen manier om toegang te verlenen tot specifieke resources is door regels voor resource-instanties te gebruiken. Zie de sectie Toegang verlenen vanuit [Azure-resource-exemplaren (preview)](#grant-access-specific-instances) van dit artikel om toegang te verlenen tot specifieke resource-exemplaren.


| Service                        | Naam van resourceprovider                 | Doel            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Hiermee wordt API Management-service toegang tot opslagaccounts achter de firewall mogelijk gemaakt met behulp van beleid. [Meer informatie](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Biedt Cognitive Search toegang tot opslagaccounts voor indexeren, verwerken en uitvoeren van query's. |
| Azure Cognitive Services       | Microsoft.CognitiveService/accounts    | Hiermee Cognitive Services toegang tot opslagaccounts. |
| Azure Container Registry Tasks | Microsoft.ContainerRegistry/registries | ACR-taken hebt toegang tot opslagaccounts bij het bouwen van containerafbeeldingen. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | Hiermee staat u toegang tot opslagaccounts toe via de ADF-runtime. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Geeft toegang tot opslagaccounts via Data Share. |
| Azure DevTest Labs             | Microsoft.DevTestLab/labs              | Geeft toegang tot opslagaccounts via DevTest Labs. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | Hiermee kunnen gegevens van een IoT-hub naar Blob Storage worden geschreven. [Meer informatie](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Hiermee kunnen logische apps toegang krijgen tot opslagaccounts. [Meer informatie](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning-service | Microsoft.MachineLearningServices      | Geautoriseerde Azure Machine Learning werkruimten schrijven experimentuitvoer, modellen en logboeken naar Blob Storage en lezen de gegevens. [Meer informatie](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). |
| Azure Media Services           | Microsoft.Media/mediaservices          | Hiermee krijgt u toegang tot opslagaccounts via Media Services. |
| Azure Migrate                  | Microsoft.Migrate/migrateprojects      | Geeft toegang tot opslagaccounts via Azure Migrate. |
| Azure Purview                  | Microsoft.Purview/accounts             | Hiermee heeft Purview toegang tot opslagaccounts. |
| Azure Remote Rendering         | Microsoft.MixedReality/remoteRenderingAccounts | Geeft toegang tot opslagaccounts via Remote Rendering. |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | Hiermee krijgt u toegang tot opslagaccounts via Site Recovery. |
| Azure SQL Database             | Microsoft.Sql                          | Hiermee kunt [u controlegegevens](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) schrijven naar opslagaccounts achter de firewall. |
| Azure Synapse Analytics        | Microsoft.Sql                          | Hiermee kunt u gegevens uit specifieke SQL-databases importeren en exporteren met behulp van de COPY-instructie of PolyBase (in een toegewezen pool), of de functie en externe tabellen `openrowset` in een serverloze pool. [Meer informatie](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics              | Hiermee staat u toe dat gegevens van een streaming-taak naar Blob Storage worden geschreven. [Meer informatie](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | Hiermee kunt u toegang tot gegevens in Azure Storage vanuit Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Toegang verlenen tot opslaganalyse

In sommige gevallen is toegang tot het lezen van resourcelogboeken en metrische gegevens vereist van buiten de netwerkgrens. Wanneer u vertrouwde services toegang tot het opslagaccount configureert, kunt u leestoegang voor de logboekbestanden, tabellen met metrische gegevens of beide toestaan door een netwerkregel-uitzondering te maken. Zie de sectie Uitzonderingen beheren  hieronder voor stapsgewijse richtlijnen. Zie Use Azure Storage analytics to collect logs and metrics data (Een analyse gebruiken om logboeken en metrische gegevens te verzamelen) voor meer informatie over het werken [met opslaganalyse.](./storage-analytics.md) 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Uitzonderingen beheren

U kunt uitzonderingen voor netwerkregelen beheren via Azure Portal, PowerShell of Azure CLI v2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar het opslagaccount dat u wilt beveiligen.

2. Selecteer in het instellingenmenu **Netwerken.**

3. Controleer of u hebt geselecteerd om toegang toe te staan vanuit **Geselecteerde netwerken.**

4. Selecteer **onder** Uitzonderingen de uitzonderingen die u wilt verlenen.

5. Klik op **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [meld u aan.](/powershell/azure/authenticate-azureps)

2. Geef de uitzonderingen voor de netwerkregels van het opslagaccount weer.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Configureer de uitzonderingen voor de netwerkregels van het opslagaccount.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Verwijder de uitzonderingen op de netwerkregels van het opslagaccount.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel zo in stelt](#change-the-default-network-access-rule) dat uitzonderingen **worden** weigert of dat het verwijderen van uitzonderingen geen effect heeft.

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure CLI en](/cli/azure/install-azure-cli) meld u [aan.](/cli/azure/authenticate-azure-cli)

2. Geef de uitzonderingen voor de netwerkregels voor het opslagaccount weer.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Configureer de uitzonderingen op de netwerkregels van het opslagaccount.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Verwijder de uitzonderingen op de netwerkregels van het opslagaccount.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaardregel zo in stelt](#change-the-default-network-access-rule) dat uitzonderingen **worden** weigert of dat het verwijderen van uitzonderingen geen effect heeft.

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Network-service-eindpunten in [Service-eindpunten.](../../virtual-network/virtual-network-service-endpoints-overview.md)

Dieper ingaan op Azure Storage beveiliging in [Azure Storage beveiligingshandleiding.](../blobs/security-recommendations.md)