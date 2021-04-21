---
title: Web Application Firewall inschakelen - Azure CLI
description: Meer informatie over hoe u webverkeer beperkt met een Web Application Firewall in een toepassingsgateway met behulp van Azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/29/2021
ms.author: victorh
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 390fdd4d9e9d0bc62589484ab0c4ba7468bcaf4b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773095"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Web Application Firewall inschakelen met behulp van Azure CLI

U kunt verkeer op een toepassingsgateway beperken met een [WAF (Web Application Firewall)](ag-overview.md). De WAF gebruikt [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-regels om uw toepassing te beveiligen. Deze regels omvatten bescherming tegen aanvallen als SQL-injectie, XSS-aanvallen (cross-site scripting) en sessiekapingen.

In dit artikel leert u het volgende:

 * Het netwerk instellen
 * Een toepassingsgateway maken met WAF ingeschakeld
 * Een virtuele-machineschaalset maken
 * Een opslagaccount maken en diagnostische gegevens configureren

![Voorbeeld van Web Application Firewall](../media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

U kunt deze procedure desgewenst voltooien met behulp van [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.4 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Gebruik de opdracht [az group create](/cli/azure/group#az_group_create) om een Azure-resourcegroep met de naam *myResourceGroupAG* te maken.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Deze virtuele netwerken en subnetten worden gebruikt om de netwerkverbinding naar de toepassingsgateway en de bijbehorende bronnen te leveren. Maak een virtueel netwerk met de naam *myVNet* en een subnet met de naam *myAGSubnet*. Maak vervolgens een openbaar IP-adres met de naam *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>Een toepassingsgateway maken met een WAF

U kunt [az network application-gateway create](/cli/azure/network/application-gateway) gebruiken om de toepassingsgateway *myAppGateway* te maken. Als u met de Azure CLI een toepassingsgateway maakt, geeft u configuratiegegevens op, zoals capaciteit, SKU en HTTP-instellingen. De toepassingsgateway wordt toegewezen aan *myAGSubnet* en *myAGPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Het kan enkele minuten duren voordat de toepassingsgateway is gemaakt. Nadat de toepassingsgateway is gemaakt, kunt u de volgende nieuwe functies ervan zien:

- *appGatewayBackendPool*: een toepassingsgateway moet ten minste één back-endadresgroep hebben.
- *appGatewayBackendHttpSettings*: hiermee wordt aangegeven dat voor de communicatie poort 80 en een HTTP-protocol worden gebruikt.
- *appGatewayHttpListener*: de standaard-listener die aan *appGatewayBackendPool* is gekoppeld.
- *appGatewayFrontendIP*: hiermee wordt *myAGPublicIPAddress* aan *appGatewayHttpListener* toegewezen.
- *rule1*: de standaardrouteringsregel die aan *appGatewayHttpListener* is gekoppeld.

## <a name="create-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset maken

In dit voorbeeld maakt u een virtuele-machineschaalset die twee servers biedt voor de back-endpool in de toepassingsgateway. De virtuele machines in de schaalset worden gekoppeld aan het subnet *myBackendSubnet*. U kunt [az vmss create](/cli/azure/vmss#az_vmss_create) gebruiken om de schaalset te maken.

Vervang \<username> en door uw waarden voordat u dit gaat \<password> uitvoeren.

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username <username> \
  --admin-password <password> \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>NGINX installeren

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Een opslagaccount maken en diagnostische gegevens configureren

In dit artikel gebruikt de toepassingsgateway een opslagaccount voor het opslaan van gegevens voor detectie- en preventiedoeleinden. U kunt ook Azure Monitor-logboeken of Event Hub gebruiken om gegevens vast te leggen. 

### <a name="create-a-storage-account"></a>Create a storage account

Maak een opslagaccount met de naam *myagstore1* met [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption-services blob
```

### <a name="configure-diagnostics"></a>Diagnostische gegevens configureren

Configureer diagnostische gegevens om gegevens vast te leggen in de logboeken ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog en ApplicationGatewayFirewallLog. Vervang `<subscriptionId>` door uw abonnements-id en configureer vervolgens de diagnostische gegevens met [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Gebruik [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) om het openbare IP-adres van de toepassingsgateway op te halen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Basis-URL testen in de toepassingsgateway](../media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de toepassingsgateway en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>Volgende stappen

[Regels voor Web Application Firewall aanpassen](application-gateway-customize-waf-rules-portal.md)
