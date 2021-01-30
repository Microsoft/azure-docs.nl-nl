---
title: Azure Storage firewalls en virtuele netwerken configureren | Microsoft Docs
description: Configureer gelaagde netwerk beveiliging voor uw opslag account met Azure Storage firewalls en Azure Virtual Network.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c8807f0200f96dc12a3b3d43fa50a91bec85ed38
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071177"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage-firewalls en virtuele netwerken configureren

Azure Storage biedt een gelaagd beveiligingsmodel. Met dit model kunt u het toegangs niveau voor uw opslag accounts beveiligen en beheren die uw toepassingen en bedrijfs omgevingen vereisen, op basis van het type en de subset van netwerken of bronnen die worden gebruikt. Wanneer netwerk regels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set van netwerken of via de opgegeven set met Azure-resources, toegang tot een opslag account. U kunt de toegang tot uw opslag account beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen, IP-adresbereiken, subnetten in een Azure-Virtual Network (VNet) of bron exemplaren van sommige Azure-Services.

Opslag accounts hebben een openbaar eind punt dat toegankelijk is via internet. U kunt ook [privé-eind punten maken voor uw opslag account](storage-private-endpoints.md), waarmee een privé-IP-adres van uw vnet wordt toegewezen aan het opslag account, en waarmee al het verkeer tussen uw vnet en het opslag account via een persoonlijke verbinding wordt beveiligd. De firewall van Azure Storage biedt toegangs beheer voor het open bare eind punt van uw opslag account. U kunt ook de firewall gebruiken om alle toegang via het open bare eind punt te blok keren bij het gebruik van privé-eind punten. Met de firewall configuratie van de opslag kunt u ook vertrouwde Azure platform-services selecteren om het opslag account veilig te benaderen.

Een toepassing die toegang heeft tot een opslag account wanneer er netwerk regels actief zijn, hebben nog steeds de juiste autorisatie voor de aanvraag. Autorisatie wordt ondersteund met Azure Active Directory-referenties (Azure AD) voor blobs en wacht rijen, met een geldige account toegangs sleutel of met een SAS-token.

> [!IMPORTANT]
> Door de firewall regels voor uw opslag account in te scha kelen, worden binnenkomende aanvragen voor gegevens standaard geblokkeerd, tenzij de aanvragen afkomstig zijn van een service die binnen een Azure-Virtual Network (VNet) of van toegestane open bare IP-adressen valt. Aanvragen die zijn geblokkeerd, zijn onder andere die van andere Azure-Services, van de Azure Portal, van de services logboek registratie en metrische gegevens, enzovoort.
>
> U kunt toegang verlenen tot Azure-Services die binnen een VNet worden bediend door verkeer van het subnet dat als host fungeert voor het service-exemplaar toe te staan. U kunt ook een beperkt aantal scenario's inschakelen via het hieronder beschreven uitzonderings mechanisme. Als u toegang wilt krijgen tot gegevens van het opslag account via de Azure Portal, moet u zich op een computer bevindt binnen de vertrouwde grens (ofwel IP of VNet) die u hebt ingesteld.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenario's

Als u uw opslag account wilt beveiligen, moet u eerst een regel configureren om de toegang tot verkeer van alle netwerken (met inbegrip van Internet verkeer) op het open bare eind punt standaard te weigeren. Vervolgens configureert u regels die toegang verlenen tot verkeer van specifieke VNets. U kunt ook regels configureren voor het verlenen van toegang tot verkeer van geselecteerde open bare IP-adresbereiken, en het inschakelen van verbindingen van specifieke internet-of lokale clients. Met deze configuratie kunt u een beveiligde netwerk grens maken voor uw toepassingen.

U kunt Firewall regels combi neren die toegang toestaan vanaf specifieke virtuele netwerken en vanaf open bare IP-adresbereiken op hetzelfde opslag account. Opslag firewall regels kunnen worden toegepast op bestaande opslag accounts of bij het maken van nieuwe opslag accounts.

De firewall regels voor opslag zijn van toepassing op het open bare eind punt van een opslag account. U hebt geen firewall toegangs regels nodig om verkeer voor privé-eind punten van een opslag account toe te staan. Het proces voor het goed keuren van het maken van een persoonlijk eind punt verleent impliciete toegang tot verkeer van het subnet dat als host fungeert voor het persoonlijke eind punt.

Netwerk regels worden afgedwongen voor alle netwerk protocollen voor Azure Storage, inclusief REST en SMB. Om toegang te krijgen tot gegevens met behulp van hulpprogram ma's zoals de Azure Portal, Storage Explorer en AZCopy, moeten expliciete netwerk regels worden geconfigureerd.

Zodra de netwerk regels zijn toegepast, worden ze afgedwongen voor alle aanvragen. SAS-tokens die toegang verlenen tot een specifiek IP-adres, kunnen de toegang tot de token houder beperken, maar geen nieuwe toegang verlenen naast geconfigureerde netwerk regels.

Schijf verkeer van de virtuele machine (inclusief koppelings-en ontkoppelings bewerkingen en schijf-i/o) wordt niet beïnvloed door netwerk regels. REST-toegang tot pagina-blobs wordt beveiligd door netwerk regels.

Klassieke opslag accounts bieden geen ondersteuning voor firewalls en virtuele netwerken.

U kunt niet-beheerde schijven gebruiken in opslag accounts met netwerk regels die zijn toegepast om een back-up te maken van Vm's en deze te herstellen met behulp van een uitzonde ring. Dit proces wordt beschreven in de sectie [uitzonde ringen beheren](#manage-exceptions) van dit artikel. Firewall-uitzonde ringen zijn niet van toepassing op Managed disks als ze al worden beheerd door Azure.

## <a name="change-the-default-network-access-rule"></a>Standaardregel voor netwerktoegang wijzigen

Standaard accepteren opslagaccounts verbindingen van clients in elk netwerk. Als u de toegang wilt beperken tot bepaalde netwerken, moet u eerst de standaardactie wijzigen.

> [!WARNING]
> Als u wijzigingen aanbrengt in de netwerkregels, kan dit van invloed zijn op de mogelijkheid van uw toepassingen om verbinding te maken met Azure Storage. Als u de standaard netwerk regel instelt op **weigeren** , wordt alle toegang tot de gegevens geblokkeerd, tenzij specifieke netwerk regels die toegang **verlenen** , ook worden toegepast. Zorg ervoor dat u alleen toegang verleent tot netwerken die gebruikmaken van netwerkregels voordat u de standaardregel wijzigt om de toegang te weigeren.

### <a name="managing-default-network-access-rules"></a>Standaardregels voor netwerktoegang beheren

U kunt de standaard regels voor netwerk toegang voor opslag accounts beheren via de Azure Portal, Power shell of CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar het opslagaccount dat u wilt beveiligen.

2. Selecteer in het menu instellingen de naam **netwerk**.

3. Als u de toegang standaard wilt weigeren, kiest u toegang vanaf **geselecteerde netwerken** toestaan. Als u verkeer van alle netwerken wilt toestaan, verleent u toegang vanaf **Alle netwerken**.

4. Klik op **Opslaan** om uw wijzigingen toe te passen.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [Meld u aan](/powershell/azure/authenticate-azureps).

2. De status van de standaard regel voor het opslag account weer geven.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Stel de standaard regel in om netwerk toegang standaard te weigeren.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Stel de standaard regel in op het standaard toestaan van netwerk toegang.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure cli](/cli/azure/install-azure-cli) en [Meld u aan](/cli/azure/authenticate-azure-cli).

2. De status van de standaard regel voor het opslag account weer geven.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Stel de standaard regel in om netwerk toegang standaard te weigeren.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Stel de standaard regel in op het standaard toestaan van netwerk toegang.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Toegang verlenen vanuit een virtueel netwerk

U kunt opslag accounts configureren om alleen toegang toe te staan vanaf specifieke subnetten. De toegestane subnetten kunnen deel uitmaken van een VNet in hetzelfde abonnement of in een ander abonnement, met inbegrip van abonnementen die horen bij een andere Azure Active Directory Tenant.

Schakel een [service-eind punt](../../virtual-network/virtual-network-service-endpoints-overview.md) in voor Azure Storage binnen het VNet. Het service-eind punt routeert verkeer van het VNet via een optimaal pad naar de Azure Storage-service. De identiteiten van het subnet en het virtuele netwerk worden ook met elke aanvraag verzonden. Beheerders kunnen vervolgens netwerk regels configureren voor het opslag account waarmee aanvragen kunnen worden ontvangen van specifieke subnetten in een VNet. Clients die toegang hebben verleend via deze netwerk regels, moeten blijven voldoen aan de autorisatie vereisten van het opslag account om toegang te krijgen tot de gegevens.

Elk opslag account ondersteunt Maxi maal 200 regels voor virtuele netwerken, die kunnen worden gecombineerd met [IP-netwerk regels](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Beschik bare virtuele netwerk regio's

Over het algemeen werken service-eind punten tussen virtuele netwerken en service-exemplaren in dezelfde Azure-regio. Wanneer u service-eind punten met Azure Storage gebruikt, neemt deze scope toe met de [gekoppelde regio](../../best-practices-availability-paired-regions.md). Service-eind punten bieden continuïteit toe tijdens een regionale failover en toegang tot alleen-lezen GRS-exemplaren (geo-redundante opslag). Netwerk regels waarmee toegang vanuit een virtueel netwerk wordt verleend aan een opslag account, hebben ook toegang tot een RA-GRS-exemplaar.

Wanneer u na een regionale onderbreking een herstel na nood geval wilt plannen, moet u de VNets in de gepaarde regio vooraf maken. Schakel service-eind punten in voor Azure Storage, met netwerk regels die toegang tot deze alternatieve virtuele netwerken verlenen. Pas deze regels vervolgens toe op uw Geo-redundante opslag accounts.

> [!NOTE]
> Service-eind punten zijn niet van toepassing op verkeer buiten de regio van het virtuele netwerk en het toegewezen regio paar. U kunt alleen netwerk regels Toep assen die toegang van virtuele netwerken verlenen aan opslag accounts in de primaire regio van een opslag account of in de aangewezen gekoppelde regio.

### <a name="required-permissions"></a>Vereiste machtigingen

Als u een regel voor een virtueel netwerk wilt Toep assen op een opslag account, moet de gebruiker de juiste machtigingen hebben voor de subnetten die worden toegevoegd. De benodigde machtiging is *lid van de service aan een subnet* en is opgenomen in de ingebouwde rol *Inzender voor opslag accounts* . Het kan ook worden toegevoegd aan aangepaste roldefinities.

Het opslag account en de virtuele netwerken waartoe toegang wordt verleend, kunnen zich in verschillende abonnementen bevinden, met inbegrip van abonnementen die deel uitmaken van een andere Azure AD-Tenant.

> [!NOTE]
> Het configureren van regels die toegang verlenen tot subnetten in virtuele netwerken die deel uitmaken van een andere Azure Active Directory Tenant, worden momenteel alleen ondersteund via Power shell, CLI en REST Api's. Deze regels kunnen niet worden geconfigureerd via de Azure Portal, maar kunnen wel worden weer gegeven in de portal.

### <a name="managing-virtual-network-rules"></a>Regels voor virtuele netwerken beheren

U kunt regels voor virtuele netwerken voor opslag accounts beheren via de Azure Portal, Power shell of CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar het opslagaccount dat u wilt beveiligen.

2. Selecteer in het menu instellingen de naam **netwerk**.

3. Controleer of u hebt geselecteerd voor toegang tot **geselecteerde netwerken**.

4. Als u toegang wilt verlenen tot een virtueel netwerk met een nieuwe netwerk regel, selecteert u onder **virtuele netwerken** **bestaande virtuele netwerk toevoegen**, selecteert u opties voor **virtuele netwerken** en **subnetten** en selecteert u vervolgens **toevoegen**. Als u een nieuw virtueel netwerk wilt maken en toegang wilt verlenen, selecteert u **nieuw virtueel netwerk toevoegen**. Geef de benodigde informatie op voor het maken van het nieuwe virtuele netwerk en selecteer vervolgens **maken**.

    > [!NOTE]
    > Als een service-eind punt voor Azure Storage niet eerder is geconfigureerd voor het geselecteerde virtuele netwerk en subnetten, kunt u deze configureren als onderdeel van deze bewerking.
    >
    > Momenteel worden alleen virtuele netwerken die deel uitmaken van dezelfde Azure Active Directory Tenant weer gegeven voor selectie tijdens het maken van de regel. Als u toegang wilt verlenen tot een subnet in een virtueel netwerk dat deel uitmaakt van een andere Tenant, gebruikt u Power shell, CLI of REST Api's.

5. Als u een virtuele netwerk-of subnet-regel wilt verwijderen, selecteert u **...** om het context menu voor het virtuele netwerk of subnet te openen, en selecteer **verwijderen**.

6. Selecteer **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [Meld u aan](/powershell/azure/authenticate-azureps).

2. Regels van het virtuele netwerk weer geven.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Service-eind punt inschakelen voor Azure Storage op een bestaand virtueel netwerk en subnet.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Als u een netwerk regel wilt toevoegen voor een subnet in een VNet dat deel uitmaakt van een andere Azure AD-Tenant, gebruikt u een volledig gekwalificeerde **VirtualNetworkResourceId** -para meter in de notatie "/Subscriptions/Subscription-id/resourceGroups/resourceGroup-name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

5. Een netwerk regel verwijderen voor een virtueel netwerk en een subnet.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op **weigeren** of dat netwerk regels geen effect hebben.

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure cli](/cli/azure/install-azure-cli) en [Meld u aan](/cli/azure/authenticate-azure-cli).

2. Regels van het virtuele netwerk weer geven.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Service-eind punt inschakelen voor Azure Storage op een bestaand virtueel netwerk en subnet.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Als u een regel wilt toevoegen voor een subnet in een VNet dat deel uitmaakt van een andere Azure AD-Tenant, gebruikt u een volledig gekwalificeerde subnet-ID in de vorm "/Subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > U kunt de para meter **abonnement** gebruiken om de subnet-id op te halen voor een VNet dat deel uitmaakt van een andere Azure AD-Tenant.

5. Een netwerk regel verwijderen voor een virtueel netwerk en een subnet.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op **weigeren** of dat netwerk regels geen effect hebben.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Toegang verlenen vanuit een IP-bereik

U kunt opslag accounts configureren om toegang toe te staan vanaf specifieke IP-adresbereiken voor het open bare Internet. Deze configuratie verleent toegang tot specifieke op internet gebaseerde services en on-premises netwerken en blokkeert algemeen Internet verkeer.

Geef toegestane Internet adresbereiken op met behulp van [CIDR-notatie](https://tools.ietf.org/html/rfc4632) in de vorm *16.17.18.0/24* of als afzonderlijke IP-adressen, zoals *16.17.18.19*.

   > [!NOTE]
   > Kleine adresbereiken die gebruikmaken van de grootte van het voor voegsel/31 of/32, worden niet ondersteund. Deze bereiken moeten worden geconfigureerd met behulp van afzonderlijke IP-adres regels.

IP-netwerk regels zijn alleen toegestaan voor **open bare Internet** -IP-adressen. IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) zijn niet toegestaan in IP-regels. Particuliere netwerken bevatten adressen die beginnen met _10. *_, _172,16. *_  -  _172,31. *_ en _192,168. *_.

   > [!NOTE]
   > IP-netwerk regels hebben geen invloed op aanvragen die afkomstig zijn uit dezelfde Azure-regio als het opslag account. Gebruik [regels voor virtuele netwerken](#grant-access-from-a-virtual-network) om aanvragen van dezelfde regio toe te staan.

  > [!NOTE]
  > Services die zijn geïmplementeerd in dezelfde regio als het opslag account, gebruiken persoonlijke Azure IP-adressen voor communicatie. U kunt de toegang tot specifieke Azure-Services dus niet beperken op basis van het open bare IP-adres bereik.

Alleen IPV4-adressen worden ondersteund voor de configuratie van firewall regels voor opslag.

Elk opslag account ondersteunt Maxi maal 200 IP-netwerk regels.

### <a name="configuring-access-from-on-premises-networks"></a>Toegang vanaf on-premises netwerken configureren

Als u toegang wilt verlenen vanaf uw on-premises netwerken naar uw opslag account met een IP-netwerk regel, moet u de Internet gerichte IP-adressen identificeren die door uw netwerk worden gebruikt. Neem contact op met uw netwerk beheerder voor hulp.

Als u [ExpressRoute](../../expressroute/expressroute-introduction.md) gebruikt vanuit uw on-premises netwerk voor openbare peering of Microsoft-peering, moet u de NAT IP-adressen opgeven die worden gebruikt. Voor openbare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Deze worden toegepast op Azure-serviceverkeer wanneer het verkeer het Microsoft Azure-backbone-netwerk binnenkomt. Voor micro soft-peering worden de IP-adressen van NAT die worden gebruikt door de klant of door de service provider verschaft. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Meer informatie over [NAT voor openbare peering en Microsoft-peering met ExpressRoute.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>IP-netwerk regels beheren

U kunt IP-netwerk regels voor opslag accounts beheren via de Azure Portal, Power shell of CLIv2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar het opslagaccount dat u wilt beveiligen.

2. Selecteer in het menu instellingen de naam **netwerk**.

3. Controleer of u hebt geselecteerd voor toegang tot **geselecteerde netwerken**.

4. Als u toegang tot een IP-adres bereik voor Internet wilt verlenen, voert u het adres bereik of het adres bereik (in CIDR-indeling) in onder **firewall**  >  **adressen**.

5. Als u een IP-netwerk regel wilt verwijderen, selecteert u het prullenbak pictogram naast het adres bereik.

6. Klik op **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [Meld u aan](/powershell/azure/authenticate-azureps).

2. IP-netwerk regels weer geven.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Voeg een netwerk regel toe voor een afzonderlijk IP-adres.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Een netwerk regel voor een IP-adres bereik toevoegen.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Verwijder een netwerk regel voor een afzonderlijk IP-adres.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Verwijder een netwerk regel voor een IP-adres bereik.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op **weigeren** of dat netwerk regels geen effect hebben.

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure cli](/cli/azure/install-azure-cli) en [Meld u aan](/cli/azure/authenticate-azure-cli).

1. IP-netwerk regels weer geven.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Voeg een netwerk regel toe voor een afzonderlijk IP-adres.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Een netwerk regel voor een IP-adres bereik toevoegen.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Verwijder een netwerk regel voor een afzonderlijk IP-adres.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Verwijder een netwerk regel voor een IP-adres bereik.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op **weigeren** of dat netwerk regels geen effect hebben.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Toegang verlenen vanuit Azure-resource-instanties (preview-versie)

In sommige gevallen is het mogelijk dat een toepassing afhankelijk is van Azure-resources die niet kunnen worden geïsoleerd via een virtueel netwerk of een IP-adres regel. U wilt echter nog steeds toegang tot de Azure-resources van uw toepassing beveiligen en beperken. U kunt opslag accounts configureren om toegang tot specifieke bron exemplaren van sommige Azure-Services toe te staan door een resource-instantie regel te maken. 

De typen bewerkingen die een bron exemplaar kan uitvoeren op de gegevens van het opslag account, worden bepaald door de [Azure-roltoewijzingen](storage-auth-aad.md#assign-azure-roles-for-access-rights) van het bron exemplaar. Bron exemplaren moeten afkomstig zijn van dezelfde Tenant als uw opslag account, maar ze kunnen deel uitmaken van een abonnement in de Tenant.

De lijst met ondersteunde Azure-Services wordt weer gegeven in de sectie [vertrouwde toegang op basis van door het systeem toegewezen beheerde identiteit](#trusted-access-system-assigned-managed-identity) van dit artikel.

> [!NOTE]
> Deze functie bevindt zich in de open bare preview en is beschikbaar in alle open bare Cloud regio's. 

### <a name="portal"></a>[Portal](#tab/azure-portal)

U kunt resource netwerk regels toevoegen of verwijderen in de Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) om aan de slag te gaan.

2. Zoek uw opslagaccount en geef het accountoverzicht weer.

3. Selecteer **netwerken** om de configuratie pagina voor netwerken weer te geven.

4. Kies in de vervolg keuzelijst **resource type** het resource type van het bron exemplaar. 

5. Kies in de vervolg keuzelijst **exemplaar naam** het resource-exemplaar. U kunt er ook voor kiezen om alle resource-instanties in de actieve Tenant, het abonnement of de resource groep op te nemen.

6. Klik op **Opslaan** om uw wijzigingen toe te passen. De bron instantie wordt weer gegeven in de sectie **bron exemplaren** van de pagina netwerk instellingen. 

Als u het resource-exemplaar wilt verwijderen, selecteert u het pictogram verwijderen ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) naast het resource-exemplaar.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt Power shell-opdrachten gebruiken om resource netwerk regels toe te voegen of te verwijderen.

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op **weigeren** of dat netwerk regels geen effect hebben.

#### <a name="install-the-preview-module"></a>De preview-module installeren

Installeer de nieuwste versie van de PowershellGet-module. Sluit vervolgens de Power shell-console en open deze opnieuw.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Installeer **AZ. Storage** preview-module.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps) voor meer informatie over het installeren van Power shell-modules.

#### <a name="grant-access"></a>Toegang verlenen

Voeg een netwerk regel toe die toegang verleent vanuit een bron exemplaar.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Geef meerdere bron instanties tegelijk op door de netwerk regel instellingen te wijzigen.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Toegang intrekken

Een netwerk regel verwijderen waarmee toegang wordt verleend vanuit een bron exemplaar.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Alle netwerk regels verwijderen waarmee toegang wordt verleend vanuit bron exemplaren.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Een lijst met toegestane resource-instanties weer geven

Bekijk een volledige lijst met resource exemplaren waaraan toegang is verleend tot het opslag account.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt Azure CLI-opdrachten gebruiken om resource netwerk regels toe te voegen of te verwijderen.

#### <a name="install-the-preview-extension"></a>De preview-extensie installeren

1. Open de [Azure Cloud shell](../../cloud-shell/overview.md)of open een opdracht console toepassing zoals Windows Power shell als u de Azure cli lokaal hebt [geïnstalleerd](/cli/azure/install-azure-cli) .

2. Controleer vervolgens of de versie van de Azure CLI die u hebt geïnstalleerd `2.13.0` of hoger is met behulp van de volgende opdracht.

   ```azurecli
   az --version
   ```

   Als uw versie van Azure CLI lager is dan `2.13.0` , installeert u een nieuwere versie. Raadpleeg [De Azure CLI installeren](/cli/azure/install-azure-cli).

3. Typ de volgende opdracht om de preview-uitbrei ding te installeren.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Toegang verlenen

Voeg een netwerk regel toe die toegang verleent vanuit een bron exemplaar.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Toegang intrekken

Een netwerk regel verwijderen waarmee toegang wordt verleend vanuit een bron exemplaar.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Een lijst met toegestane resource-instanties weer geven

Bekijk een volledige lijst met resource exemplaren waaraan toegang is verleend tot het opslag account.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-azure-services"></a>Toegang verlenen tot Azure-Services 

Sommige Azure-Services werken vanuit netwerken die niet in uw netwerk regels kunnen worden opgenomen. U kunt een subset van dergelijke betrouw bare Azure-Services toegang verlenen tot het opslag account, terwijl netwerk regels voor andere apps worden onderhouden. Deze vertrouwde services gebruiken vervolgens sterke verificatie om veilig verbinding te maken met uw opslag account. 

U kunt toegang verlenen aan vertrouwde Azure-Services door een netwerk regel uitzondering te maken. Zie de sectie [uitzonde ringen beheren](#manage-exceptions) in dit artikel voor stapsgewijze instructies. 

Wanneer u toegang verleent aan vertrouwde Azure-Services, verleent u de volgende typen toegang:

- Vertrouwde toegang voor Select-bewerkingen voor resources die zijn geregistreerd in uw abonnement.
- Vertrouwde toegang tot bronnen op basis van door het systeem toegewezen beheerde identiteit.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Vertrouwde toegang voor resources die zijn geregistreerd in uw abonnement

Resources van sommige services, **wanneer deze zijn geregistreerd in uw abonnement**, hebben toegang tot uw opslag account **in hetzelfde abonnement** voor Select-bewerkingen, zoals het schrijven van Logboeken of back-ups.  De volgende tabel beschrijft elke service en de toegestane bewerkingen. 

| Service                  | Naam van resource provider     | Toegestane bewerkingen                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Voer back-ups en herstel bewerkingen uit van niet-beheerde schijven in virtuele IAAS-machines. (niet vereist voor beheerde schijven). [Meer informatie](../../backup/backup-overview.md). |
| Azure Data Box           | Micro soft. DataBox          | Hiermee kunt u gegevens importeren naar Azure met behulp van Data Box. [Meer informatie](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Het maken van aangepaste installatie kopieën en artefact installatie. [Meer informatie](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Micro soft. EventGrid        | Schakel Blob Storage gebeurtenis publicatie in en sta Event Grid toe om naar opslag wachtrijen te publiceren. Meer informatie over [Blob Storage-gebeurtenissen](../../event-grid/overview.md#event-sources) en [het publiceren naar wacht rijen](../../event-grid/event-handlers.md). |
| Azure Event Hubs         | Microsoft.EventHub         | Gegevens archiveren met Event Hubs Capture. [Meer informatie](../../event-hubs/event-hubs-capture-overview.md). |
| Azure File Sync          | Micro soft. StorageSync      | Hiermee kunt u uw on-premises Bestands server transformeren naar een cache voor Azure-bestands shares. Het toestaan van synchronisatie op meerdere locaties, snelle herstel na nood gevallen en back-ups aan de Cloud zijde. [Meer informatie](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Richt de oorspronkelijke inhoud in van het standaard bestandssysteem voor een nieuw HDInsight-cluster. [Meer informatie](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure import-export      | Microsoft.ImportExport     | Hiermee kunt u gegevens importeren voor het Azure Storage of exporteren van gegevens uit Azure Storage met behulp van de Azure Storage import/export-service. [Meer informatie](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.Insights         | Hiermee staat u het schrijven van bewakings gegevens naar een beveiligd opslag account, inclusief bron logboeken, Azure Active Directory aanmeld-en audit logboeken en Microsoft Intune-Logboeken toe. [Meer informatie](../../azure-monitor/platform/roles-permissions-security.md). |
| Azure-netwerken         | Microsoft.Network          | U kunt Logboeken voor netwerk verkeer opslaan en analyseren, met inbegrip van de Network Watcher-en Traffic Analytics-Services. [Meer informatie](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Micro soft. SiteRecovery     | Schakel replicatie in voor herstel na nood gevallen van virtuele Azure IaaS-machines wanneer u gebruikmaakt van cache-, bron-of doel opslag accounts die gebruikmaken van een firewall.  [Meer informatie](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Vertrouwde toegang op basis van door het systeem toegewezen beheerde identiteit

De volgende tabel bevat de services die toegang kunnen hebben tot uw gegevens van uw opslag account als de bron exemplaren van die services de juiste machtiging krijgen. Als u machtigingen wilt verlenen, moet u expliciet [een Azure-rol toewijzen](storage-auth-aad.md#assign-azure-roles-for-access-rights) aan de door het [systeem toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) voor elk resource-exemplaar. In dit geval komt het toegangsbereik voor het exemplaar overeen met de Azure-rol die aan de beheerde identiteit is toegewezen. 

> [!TIP]
> De aanbevolen manier om toegang te verlenen tot specifieke bronnen is het gebruik van resource-instantie regels. Zie de sectie [toegang verlenen vanuit Azure resource instances (preview)](#grant-access-specific-instances) in dit artikel voor meer informatie over het verlenen van toegang tot specifieke bron exemplaren.


| Service                        | Naam van resource provider                 | Doel            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Hiermee wordt de API Management-service toegang tot opslag accounts achter firewall ingeschakeld met behulp van beleid. [Meer informatie](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Micro soft. Search/searchServices        | Hiermee kunnen Cognitive Search Services toegang krijgen tot opslag accounts voor indexering, verwerking en query's. |
| Azure Cognitive Services       | Micro soft. CognitiveService             | Hiermee wordt Cognitive Services toegang tot opslag accounts. |
| Azure Container Registry Tasks | Micro soft. ContainerRegistry/registers | ACR-taken hebben toegang tot opslag accounts tijdens het maken van container installatie kopieën. |
| Azure Data Factory             | Micro soft. DataFactory/fabrieken        | Hiermee hebt u toegang tot opslag accounts via de ADF-runtime. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Hiermee wordt toegang tot opslag accounts via een gegevens share toegestaan. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | Hiermee kunnen gegevens van een IoT-hub worden geschreven naar de Blob-opslag. [Meer informatie](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Hiermee kunnen logische apps toegang krijgen tot opslag accounts. [Meer informatie](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning-service | Microsoft.MachineLearningServices      | Geautoriseerde Azure Machine Learning-werk ruimten schrijven experiment-uitvoer, modellen en logboeken naar Blob Storage en lezen de gegevens. [Meer informatie](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). | 
| Azure Synapse Analytics       | Microsoft.Sql                          | Staat het importeren en exporteren van gegevens uit specifieke SQL-data bases toe met behulp van de instructie COPY of poly base (in toegewezen groep) of de `openrowset` functie en externe tabellen in de serverloze groep. [Meer informatie](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure SQL Database       | Microsoft.Sql                          | Staat het [schrijven](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) van controle gegevens naar opslag accounts achter de firewall toe. |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Hiermee staat u toe dat gegevens van een streaming-taak naar de Blob-opslag worden geschreven. [Meer informatie](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Micro soft. Synapse/werk ruimten          | Hiermee schakelt u toegang tot gegevens in Azure Storage van Azure Synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Toegang verlenen tot opslag analyse

In sommige gevallen is de toegang tot bron logboeken en-metrische gegevens vereist van buiten de grens van het netwerk. Bij het configureren van de toegang tot het opslag account met behulp van vertrouwde services, kunt u lees toegang toestaan voor de logboek bestanden, metrische tabellen of beide door een netwerk regel uitzondering te maken. Zie de sectie **uitzonde ringen beheren** hieronder voor stapsgewijze instructies. Zie [Azure Storage Analytics gebruiken voor het verzamelen van Logboeken en metrische gegevens](./storage-analytics.md)voor meer informatie over het werken met Storage Analytics. 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Uitzonde ringen beheren

U kunt netwerk regel uitzonderingen beheren via de Azure Portal, Power shell of Azure CLI v2.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar het opslagaccount dat u wilt beveiligen.

2. Selecteer in het menu instellingen de naam **netwerk**.

3. Controleer of u hebt geselecteerd voor toegang tot **geselecteerde netwerken**.

4. Selecteer onder **uitzonde ringen** de uitzonde ringen die u wilt verlenen.

5. Klik op **Opslaan** om uw wijzigingen toe te passen.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-Az-ps) en [Meld u aan](/powershell/azure/authenticate-azureps).

2. De uitzonde ringen voor de netwerk regels van het opslag account weer geven.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. De uitzonde ringen configureren voor de netwerk regels van het opslag account.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Verwijder de uitzonde ringen voor de netwerk regels van het opslag account.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op **weigeren** of dat het verwijderen van uitzonde ringen geen effect heeft.

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Installeer de [Azure cli](/cli/azure/install-azure-cli) en [Meld u aan](/cli/azure/authenticate-azure-cli).

2. De uitzonde ringen voor de netwerk regels van het opslag account weer geven.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. De uitzonde ringen configureren voor de netwerk regels van het opslag account.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Verwijder de uitzonde ringen voor de netwerk regels van het opslag account.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op **weigeren** of dat het verwijderen van uitzonde ringen geen effect heeft.

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Network Service-eind punten in [service-eind punten](../../virtual-network/virtual-network-service-endpoints-overview.md).

Dieper in de Azure Storage beveiliging in [Azure Storage beveiligings handleiding](../blobs/security-recommendations.md).
