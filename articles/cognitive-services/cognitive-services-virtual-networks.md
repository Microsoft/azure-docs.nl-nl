---
title: Virtuele netwerken
titleSuffix: Azure Cognitive Services
description: Configureer gelaagde netwerk beveiliging voor uw Cognitive Services-resources.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: aahi
ms.openlocfilehash: 3b6c2a5a50cedadd8818eae735df55b661e794ef
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97034017"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Virtuele Azure Cognitive Services-netwerken configureren

Azure Cognitive Services biedt een gelaagd beveiligings model. Met dit model kunt u uw Cognitive Services-accounts beveiligen met een specifieke subset van netwerken. Als er netwerkregels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot het account. U kunt de toegang tot uw resources beperken met behulp van aanvraagfiltering. Het toestaan van alleen aanvragen die afkomstig zijn van opgegeven IP-adressen, IP-bereiken of van een lijst met subnetten in [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md).

Een toepassing die toegang heeft tot een Cognitive Services resource wanneer de netwerk regels van kracht zijn, is autorisatie vereist. Autorisatie wordt ondersteund met [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD)-referenties of met een geldige API-sleutel.

> [!IMPORTANT]
> Als u firewall regels inschakelt voor uw Cognitive Services account, worden binnenkomende aanvragen voor gegevens standaard geblokkeerd. Voor het toestaan van aanvragen via moet aan een van de volgende voor waarden worden voldaan:

> * De aanvraag moet afkomstig zijn van een service in een Azure Virtual Network (VNet) in de lijst met toegestane subnetten van het doel Cognitive Services account. Het eind punt van aanvragen die afkomstig zijn van VNet, moet worden ingesteld als het [aangepaste subdomein](cognitive-services-custom-subdomains.md) van uw Cognitive Services-account.
> * Of de aanvraag moet afkomstig zijn uit een lijst met toegestane IP-adressen.
>
> Aanvragen die zijn geblokkeerd, zijn onder andere die van andere Azure-Services, van de Azure Portal, van de services logboek registratie en metrische gegevens, enzovoort.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenario's

Als u uw Cognitive Services resource wilt beveiligen, moet u eerst een regel configureren om de toegang tot verkeer van alle netwerken (met inbegrip van Internet verkeer) standaard te weigeren. Vervolgens configureert u regels die toegang verlenen tot verkeer van specifieke VNets. Met deze configuratie kunt u een beveiligde netwerk grens maken voor uw toepassingen. U kunt ook regels configureren voor het verlenen van toegang tot verkeer van open bare IP-adresbereiken voor het Internet, het inschakelen van verbindingen van specifieke internet-of lokale clients.

Netwerk regels worden op alle netwerk protocollen afgedwongen voor Azure Cognitive Services, inclusief REST en WebSocket. Om toegang te krijgen tot gegevens met behulp van hulpprogram ma's zoals de Azure test consoles, moeten expliciete netwerk regels worden geconfigureerd. U kunt netwerk regels Toep assen op bestaande Cognitive Services resources of wanneer u nieuwe Cognitive Services resources maakt. Zodra de netwerk regels zijn toegepast, worden ze afgedwongen voor alle aanvragen.

## <a name="supported-regions-and-service-offerings"></a>Ondersteunde regio's en service aanbiedingen

Virtuele netwerken (VNETs) worden ondersteund in [regio's waar Cognitive services beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/). Cognitive Services ondersteunt service tags voor de configuratie van netwerk regels. De hieronder vermelde services zijn opgenomen in het **CognitiveServicesManagement** -service label.

> [!div class="checklist"]
> * Anomaly Detector
> * Computer Vision
> * Content Moderator
> * Custom Vision
> * Face
> * Form Recognizer
> * Immersive Reader
> * Language Understanding (LUIS)
> * Personalizer
> * Speech Services
> * Text Analytics
> * QnA Maker
> * Translator Text


> [!NOTE]
> Als u LUIS gebruikt, kunt u met de tag **CognitiveServicesManagement** alleen de service gebruiken met de SDK of de rest API. Als u de LUIS-Portal vanuit een virtueel netwerk wilt openen en gebruiken, moet u de volgende tags gebruiken:  
> * **AzureActiveDirectory**
> * **AzureFrontDoor. front-end**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>Standaardregel voor netwerktoegang wijzigen

Cognitive Services resources accepteren standaard verbindingen van clients in elk netwerk. Als u de toegang wilt beperken tot bepaalde netwerken, moet u eerst de standaardactie wijzigen.

> [!WARNING]
> Het wijzigen van de netwerk regels kan invloed hebben op de mogelijkheid om verbinding te maken met Azure Cognitive Services. Als u de standaard netwerk regel instelt op **weigeren** , wordt alle toegang tot de gegevens geblokkeerd, tenzij specifieke netwerk regels die toegang **verlenen** , ook worden toegepast. Zorg ervoor dat u alleen toegang verleent tot netwerken die gebruikmaken van netwerkregels voordat u de standaardregel wijzigt om de toegang te weigeren. Als u IP-adressen voor vermeldingen voor uw on-premises netwerk wilt toestaan, moet u alle mogelijke uitgaande open bare IP-adressen van uw on-premises netwerk toevoegen.

### <a name="managing-default-network-access-rules"></a>Standaardregels voor netwerktoegang beheren

U kunt de standaard regels voor netwerk toegang voor Cognitive Services resources beheren via de Azure Portal, Power shell of de Azure CLI.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Ga naar de Cognitive Services resource die u wilt beveiligen.

1. Selecteer in het menu **resource beheer** de naam **virtueel netwerk**.

   ![Optie voor virtueel netwerk](media/vnet/virtual-network-blade.png)

1. Als u de toegang standaard wilt weigeren, kiest u toegang vanaf **geselecteerde netwerken** toestaan. Met de **geselecteerde netwerken** alleen ingesteld, niet begeleid door geconfigureerde **virtuele netwerken** **of** adresbereiken: alle toegang wordt daad werkelijk geweigerd. Als alle toegang wordt geweigerd, zijn aanvragen voor het gebruik van de Cognitive Services resource niet toegestaan. De Azure Portal, Azure PowerShell of, Azure CLI, kan nog steeds worden gebruikt voor het configureren van de Cognitive Services resource.
1. Als u verkeer van alle netwerken wilt toestaan, verleent u toegang vanaf **Alle netwerken**.

   ![Virtuele netwerken weigeren](media/vnet/virtual-network-deny.png)

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-az-ps) en [Meld](/powershell/azure/authenticate-azureps)u aan of selecteer **het bestand try**.

1. De status van de standaard regel voor de Cognitive Services resource weer geven.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Stel de standaard regel in om netwerk toegang standaard te weigeren.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Stel de standaard regel in op het standaard toestaan van netwerk toegang.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installeer de [Azure cli](/cli/azure/install-azure-cli) en [Meld](/cli/azure/authenticate-azure-cli)u aan of selecteer **proberen**.

1. De status van de standaard regel voor de Cognitive Services resource weer geven.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Stel de standaard regel in om netwerk toegang standaard te weigeren.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Stel de standaard regel in op het standaard toestaan van netwerk toegang.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

**_

## <a name="grant-access-from-a-virtual-network"></a>Toegang verlenen vanuit een virtueel netwerk

U kunt Cognitive Services-resources zodanig configureren dat alleen toegang vanaf specifieke subnetten is toegestaan. De toegestane subnetten kunnen deel uitmaken van een VNet in hetzelfde abonnement of in een ander abonnement, met inbegrip van abonnementen die deel uitmaken van een andere Azure Active Directory Tenant.

Schakel een [service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) in voor Azure Cognitive services binnen het VNet. Het service-eind punt routeert verkeer van het VNet via een optimaal pad naar de Azure Cognitive Services-service. De identiteiten van het subnet en het virtuele netwerk worden ook met elke aanvraag verzonden. Beheerders kunnen vervolgens netwerk regels configureren voor de Cognitive Services resource waarmee aanvragen kunnen worden ontvangen van specifieke subnetten in een VNet. Clients die toegang hebben verleend via deze netwerk regels, moeten blijven voldoen aan de autorisatie vereisten van de Cognitive Services resource om toegang te krijgen tot de gegevens.

Elke Cognitive Services Resource ondersteunt Maxi maal 100 regels voor virtuele netwerken, die kunnen worden gecombineerd met [IP-netwerk regels](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Vereiste machtigingen

Als u een regel voor een virtueel netwerk wilt Toep assen op een Cognitive Services resource, moet de gebruiker de juiste machtigingen hebben voor de subnetten die worden toegevoegd. De vereiste machtiging is de standaard _Contributor * rol of de rol *Cognitive Services Inzender* . De vereiste machtigingen kunnen ook worden toegevoegd aan aangepaste roldefinities.

Cognitive Services resource en de virtuele netwerken die toegang hebben verleend, kunnen zich in verschillende abonnementen bevinden, met inbegrip van abonnementen die deel uitmaken van een andere Azure AD-Tenant.

> [!NOTE]
> Het configureren van regels die toegang verlenen tot subnetten in virtuele netwerken die deel uitmaken van een andere Azure Active Directory Tenant, worden momenteel alleen ondersteund via Power shell, CLI en REST Api's. Deze regels kunnen niet worden geconfigureerd via de Azure Portal, maar kunnen wel worden weer gegeven in de portal.

### <a name="managing-virtual-network-rules"></a>Regels voor virtuele netwerken beheren

U kunt regels voor virtuele netwerken voor Cognitive Services resources beheren via de Azure Portal, Power shell of de Azure CLI.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Ga naar de Cognitive Services resource die u wilt beveiligen.

1. Selecteer in het menu **resource beheer** de naam **virtueel netwerk**.

1. Controleer of u hebt geselecteerd voor toegang tot **geselecteerde netwerken**.

1. Als u toegang wilt verlenen tot een virtueel netwerk met een bestaande netwerk regel, selecteert u **bestaande virtuele netwerk toevoegen** onder **virtuele netwerken**.

   ![Bestaand vNet toevoegen](media/vnet/virtual-network-add-existing.png)

1. Selecteer de opties voor **virtuele netwerken** en **subnetten** en selecteer vervolgens **inschakelen**.

   ![Bestaande vNet-gegevens toevoegen](media/vnet/virtual-network-add-existing-details.png)

1. Als u een nieuw virtueel netwerk wilt maken en toegang wilt verlenen, selecteert u **nieuw virtueel netwerk toevoegen**.

   ![Nieuw vNet toevoegen](media/vnet/virtual-network-add-new.png)

1. Geef de benodigde informatie op voor het maken van het nieuwe virtuele netwerk en selecteer vervolgens **maken**.

   ![VNet maken](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Als een service-eind punt voor Azure Cognitive Services niet eerder is geconfigureerd voor het geselecteerde virtuele netwerk en subnetten, kunt u deze configureren als onderdeel van deze bewerking.
    >
    > Momenteel worden alleen virtuele netwerken die deel uitmaken van dezelfde Azure Active Directory Tenant weer gegeven voor selectie tijdens het maken van de regel. Als u toegang wilt verlenen tot een subnet in een virtueel netwerk dat deel uitmaakt van een andere Tenant, gebruikt u Power shell, CLI of REST Api's.

1. Als u een virtuele netwerk-of subnet-regel wilt verwijderen, selecteert u **...** om het context menu voor het virtuele netwerk of subnet te openen, en selecteer **verwijderen**.

   ![VNet verwijderen](media/vnet/virtual-network-remove.png)

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-az-ps) en [Meld](/powershell/azure/authenticate-azureps)u aan of selecteer **het bestand try**.

1. Regels van het virtuele netwerk weer geven.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Service-eind punt inschakelen voor Azure Cognitive Services op een bestaand virtueel netwerk en subnet.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Als u een netwerk regel wilt toevoegen voor een subnet in een VNet dat deel uitmaakt van een andere Azure AD-Tenant, gebruikt u een volledig gekwalificeerde **VirtualNetworkResourceId** -para meter in de notatie "/Subscriptions/Subscription-id/resourceGroups/resourceGroup-name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

1. Een netwerk regel verwijderen voor een virtueel netwerk en een subnet.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installeer de [Azure cli](/cli/azure/install-azure-cli) en [Meld](/cli/azure/authenticate-azure-cli)u aan of selecteer **proberen**.

1. Regels van het virtuele netwerk weer geven.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Service-eind punt inschakelen voor Azure Cognitive Services op een bestaand virtueel netwerk en subnet.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Als u een regel wilt toevoegen voor een subnet in een VNet dat deel uitmaakt van een andere Azure AD-Tenant, gebruikt u een volledig gekwalificeerde subnet-ID in de notatie '/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name '.
    > 
    > U kunt de para meter **abonnement** gebruiken om de subnet-id op te halen voor een VNet dat deel uitmaakt van een andere Azure AD-Tenant.

1. Een netwerk regel verwijderen voor een virtueel netwerk en een subnet.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

**_

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op _ * weigeren * * of netwerk regels geen effect hebben.

## <a name="grant-access-from-an-internet-ip-range"></a>Toegang verlenen vanuit een IP-bereik

U kunt Cognitive Services resources zodanig configureren dat toegang vanaf specifieke IP-adresbereiken voor het Internet wordt toegestaan. Met deze configuratie wordt toegang verleend tot specifieke services en on-premises netwerken, waardoor het algemene Internet verkeer effectief wordt geblokkeerd.

Geef toegestane Internet adresbereiken op met behulp van [CIDR-notatie](https://tools.ietf.org/html/rfc4632) in het formulier `16.17.18.0/24` of als afzonderlijke IP-adressen, zoals `16.17.18.19` .

   > [!Tip]
   > Kleine adresbereiken die gebruikmaken van de grootte van het voor voegsel/31 of/32, worden niet ondersteund. Deze bereiken moeten worden geconfigureerd met behulp van afzonderlijke IP-adres regels.

IP-netwerk regels zijn alleen toegestaan voor **open bare Internet** -IP-adressen. IP-adresbereiken die zijn gereserveerd voor particuliere netwerken (zoals gedefinieerd in [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) zijn niet toegestaan in IP-regels. Particuliere netwerken bevatten adressen die beginnen met `10.*` , `172.16.*`  -  `172.31.*` en `192.168.*` .

   > [!NOTE]
   > IP-netwerk regels hebben geen invloed op aanvragen die afkomstig zijn uit dezelfde Azure-regio als de Cognitive Services resource. Gebruik [regels voor virtuele netwerken](#grant-access-from-a-virtual-network) om aanvragen van dezelfde regio toe te staan.

Er worden op dit moment alleen IPV4-adressen ondersteund. Elke Cognitive Services Resource ondersteunt Maxi maal 100 IP-netwerk regels, die kunnen worden gecombineerd met de regels voor het [virtuele netwerk](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Toegang vanaf on-premises netwerken configureren

Als u toegang wilt verlenen vanaf uw on-premises netwerken aan uw Cognitive Services-bron met een IP-netwerk regel, moet u de Internet gerichte IP-adressen identificeren die door uw netwerk worden gebruikt. Neem contact op met uw netwerk beheerder voor hulp.

Als u [ExpressRoute](../expressroute/expressroute-introduction.md) on-premises gebruikt voor open bare peering of micro soft-peering, moet u de NAT IP-adressen identificeren. Voor open bare peering gebruikt elk ExpressRoute-circuit standaard twee NAT IP-adressen. Elke wordt toegepast op Azure-service verkeer wanneer het verkeer de Microsoft Azure-netwerk-backbone binnenkomt. Voor micro soft-peering zijn de NAT IP-adressen die worden gebruikt door de klant of door de service provider verschaft. Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource. Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Meer informatie over [NAT voor openbare peering en Microsoft-peering met ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>IP-netwerk regels beheren

U kunt IP-netwerk regels voor Cognitive Services resources beheren via de Azure Portal, Power shell of de Azure CLI.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Ga naar de Cognitive Services resource die u wilt beveiligen.

1. Selecteer in het menu **resource beheer** de naam **virtueel netwerk**.

1. Controleer of u hebt geselecteerd voor toegang tot **geselecteerde netwerken**.

1. Als u toegang tot een IP-adres bereik voor Internet wilt verlenen, voert u het adres bereik of het adres bereik (in [CIDR-indeling](https://tools.ietf.org/html/rfc4632)) in onder **firewall**  >  **adressen**. Alleen geldige open bare IP-adressen (niet-gereserveerd) worden geaccepteerd.

   ![IP-bereik toevoegen](media/vnet/virtual-network-add-ip-range.png)

1. Als u een IP-netwerk regel wilt verwijderen, selecteert u het Prullenbak <span class="docon docon-delete x-hidden-focus"></span> pictogram naast het adres bereik.

   ![IP-bereik verwijderen](media/vnet/virtual-network-delete-ip-range.png)

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installeer de [Azure PowerShell](/powershell/azure/install-az-ps) en [Meld](/powershell/azure/authenticate-azureps)u aan of selecteer **het bestand try**.

1. IP-netwerk regels weer geven.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Voeg een netwerk regel toe voor een afzonderlijk IP-adres.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Een netwerk regel voor een IP-adres bereik toevoegen.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Verwijder een netwerk regel voor een afzonderlijk IP-adres.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Verwijder een netwerk regel voor een IP-adres bereik.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installeer de [Azure cli](/cli/azure/install-azure-cli) en [Meld](/cli/azure/authenticate-azure-cli)u aan of selecteer **proberen**.

1. IP-netwerk regels weer geven.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Voeg een netwerk regel toe voor een afzonderlijk IP-adres.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Een netwerk regel voor een IP-adres bereik toevoegen.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Verwijder een netwerk regel voor een afzonderlijk IP-adres.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Verwijder een netwerk regel voor een IP-adres bereik.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

**_

> [!IMPORTANT]
> Zorg ervoor dat u [de standaard regel instelt](#change-the-default-network-access-rule) op _ * weigeren * * of netwerk regels geen effect hebben.

## <a name="use-private-endpoints"></a>Privé-eindpunten gebruiken

U kunt [privé-eind punten](../private-link/private-endpoint-overview.md) voor uw Cognitive services-resources gebruiken om clients op een virtueel netwerk (VNet) toe te staan om veilig toegang te krijgen tot gegevens via een [privé-koppeling](../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de VNet-adres ruimte voor uw Cognitive Services-resource. Netwerk verkeer tussen de clients in het VNet en de resource gaat over het VNet en een privé koppeling op het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt geëlimineerd.

Met persoonlijke eind punten voor Cognitive Services resources kunt u het volgende doen:

* Beveilig uw Cognitive Services bron door de firewall zodanig te configureren dat alle verbindingen op het open bare eind punt voor de Cognitive Services service worden geblokkeerd.
* Verbeter de beveiliging van het VNet door u in staat te stellen exfiltration van gegevens van het VNet te blok keren.
* Maak veilig verbinding met Cognitive Services bronnen vanuit on-premises netwerken die verbinding maken met het VNet met behulp van [VPN-](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [expressroutes waaraan](../expressroute/expressroute-locations.md) met privé-peering.

### <a name="conceptual-overview"></a>Conceptueel overzicht

Een persoonlijk eind punt is een speciale netwerk interface voor een Azure-resource in uw [VNet](../virtual-network/virtual-networks-overview.md). Het maken van een persoonlijk eind punt voor uw Cognitive Services resource zorgt voor een veilige connectiviteit tussen clients in uw VNet en uw bron. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw VNet. De verbinding tussen het persoonlijke eind punt en de Cognitive Services-service maakt gebruik van een beveiligde persoonlijke koppeling.

Toepassingen in het VNet kunnen naadloos verbinding maken met de service via het persoonlijke eind punt, met behulp van dezelfde verbindings reeksen en autorisatie mechanismen die ze anders zouden gebruiken. De uitzonde ring hierop is de spraak Services, waarvoor een afzonderlijk eind punt is vereist. Zie de sectie over [privé-eind punten met de spraak Services](#private-endpoints-with-the-speech-services). Privé-eind punten kunnen worden gebruikt met alle protocollen die worden ondersteund door de Cognitive Services bron, inclusief REST.

Privé-eind punten kunnen worden gemaakt in subnetten die gebruikmaken van [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md). Clients in een subnet kunnen verbinding maken met een Cognitive Services bron met behulp van een persoonlijk eind punt, terwijl service-eind punten worden gebruikt voor toegang tot anderen.

Wanneer u een persoonlijk eind punt maakt voor een Cognitive Services resource in uw VNet, wordt een goedkeurings aanvraag voor goed keuring verzonden naar de eigenaar van de Cognitive Services resource. Als de gebruiker die het persoonlijke eind punt wil maken ook eigenaar van de resource is, wordt deze aanvraag voor toestemming automatisch goedgekeurd.

Cognitive Services resource-eigen aren kunnen toestemmings aanvragen en de persoonlijke eind punten beheren via het tabblad *privé-eind punten* voor de Cognitive Services resource in de [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints"></a>Privé-eindpunten

Wanneer u het persoonlijke eind punt maakt, moet u de Cognitive Services resource opgeven waarmee verbinding wordt gemaakt. Zie voor meer informatie over het maken van een persoonlijk eind punt:

* [Maak een persoonlijk eind punt met behulp van het privé koppelings centrum in de Azure Portal](../private-link/create-private-endpoint-portal.md)
* [Een persoonlijk eind punt maken met behulp van Azure CLI](../private-link/create-private-endpoint-cli.md)
* [Een persoonlijk eind punt maken met Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Verbinding maken met privé-eind punten

Clients op een VNet met behulp van het privé-eind punt moeten hetzelfde connection string gebruiken voor de Cognitive Services resource als clients die verbinding maken met het open bare eind punt. De uitzonde ring hierop is de spraak Services, waarvoor een afzonderlijk eind punt is vereist. Zie de sectie over [privé-eind punten met de spraak Services](#private-endpoints-with-the-speech-services). We vertrouwen op DNS-omzetting om de verbindingen van het VNet naar de Cognitive Services bron via een persoonlijke verbinding automatisch te routeren. 

Er wordt standaard een [privé-DNS-zone](../dns/private-dns-overview.md) gekoppeld aan het VNet met de vereiste updates voor de privé-eind punten. Als u echter uw eigen DNS-server gebruikt, moet u mogelijk aanvullende wijzigingen aanbrengen in uw DNS-configuratie. In de volgende sectie over [DNS-wijzigingen](#dns-changes-for-private-endpoints) worden de vereiste updates voor persoonlijke eind punten beschreven.

### <a name="private-endpoints-with-the-speech-services"></a>Privé-eind punten met de spraak Services

Zie [spraak Services gebruiken met privé-eind punten van de persoonlijke Azure-koppeling](Speech-Service/speech-services-private-link.md).

### <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privé-eind punten

Wanneer u een persoonlijk eind punt maakt, wordt de DNS CNAME-bron record voor de Cognitive Services resource bijgewerkt naar een alias in een subdomein met het voor voegsel '*privatelink*'. Standaard maken we ook een [privé-DNS-zone](../dns/private-dns-overview.md), die overeenkomt met het subdomein '*privatelink*', met de DNS a-bron records voor de privé-eind punten.

Wanneer u de eind punt-URL van buiten het VNet met het persoonlijke eind punt oplost, wordt deze omgezet in het open bare eind punt van de Cognitive Services resource. Wanneer het is opgelost vanuit het VNet dat het persoonlijke eind punt host, wordt de eind punt-URL omgezet naar het IP-adres van het privé-eind punt.

Met deze aanpak is toegang tot de Cognitive Services resource mogelijk via dezelfde connection string voor clients in het VNet die de persoonlijke eind punten en clients buiten het VNet hosten.

Als u een aangepaste DNS-server in uw netwerk gebruikt, moeten clients de Fully Qualified Domain Name (FQDN) voor het Cognitive Services resource-eind punt kunnen omzetten naar het IP-adres van het privé-eind punt. Configureer uw DNS-server voor het delegeren van het subdomein van uw privé-koppeling naar de privé-DNS-zone voor het VNet.

> [!TIP]
> Wanneer u een aangepaste of lokale DNS-server gebruikt, moet u uw DNS-server zo configureren dat de Cognitive Services resource naam in het subdomein ' privatelink ' wordt omgezet in het IP-adres van het privé-eind punt. U kunt dit doen door het subdomein ' privatelink ' te delegeren aan de privé-DNS-zone van het VNet of door de DNS-zone op de DNS-server te configureren en de DNS A-records toe te voegen.

Raadpleeg de volgende artikelen voor meer informatie over het configureren van uw eigen DNS-server voor de ondersteuning van persoonlijke eind punten:

* [Naamomzetting voor resources in virtuele Azure-netwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [DNS-configuratie voor privé-eind punten](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>Prijzen

Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link) voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

* De verschillende [Azure-Cognitive Services](./what-are-cognitive-services.md) verkennen
* Meer informatie over [Azure Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)