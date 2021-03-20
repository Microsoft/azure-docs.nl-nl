---
title: Toepassingen beschikbaar stellen op internet met behulp van Application Gateway en Azure Firewall
description: Toepassingen beschikbaar stellen op internet met behulp van Application Gateway en Azure Firewall
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738715"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Toepassingen beschikbaar stellen op internet met behulp van Application Gateway en Azure Firewall

In dit document wordt uitgelegd hoe u toepassingen beschikbaar maakt op internet met behulp van Application Gateway en Azure Firewall. Wanneer een Azure lente-Cloud service-exemplaar in uw virtuele netwerk is geïmplementeerd, zijn de toepassingen op het service-exemplaar alleen toegankelijk in het particuliere netwerk. Als u de toepassingen toegankelijk wilt maken op internet, moet u de integratie met **Azure-toepassing gateway** en eventueel met **Azure firewall**.

## <a name="prerequisites"></a>Vereisten

- [Azure CLI-versie 2.0.4 of hoger](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Variabelen definiëren

Definieer de variabelen voor de resource groep en het virtuele netwerk die u hebt gemaakt als gericht in [Azure lente-Cloud implementeren in azure Virtual Network (VNet-injectie)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md). Pas de waarden aan op basis van uw echte omgeving.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure CLI en kies uw actieve abonnement.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

De **Azure-toepassing-gateway** die moet worden gemaakt, wordt toegevoegd aan hetzelfde virtuele netwerk als of gekoppeld virtueel netwerk met--het Azure lente-Cloud service-exemplaar. Maak eerst een nieuw subnet voor de Application Gateway in het virtuele netwerk met `az network vnet subnet create` en maak een openbaar IP-adres als frontend van de Application Gateway met `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Een toepassingsgateway maken

Maak een toepassings gateway met behulp `az network application-gateway create` van en geef de persoonlijke Fully Qualified Domain Name (FQDN) van uw toepassing op als servers in de back-end-pool. Werk vervolgens de HTTP-instelling `az network application-gateway http-settings update` bij met om de hostnaam uit de back-end-groep te gebruiken.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Het kan tot 30 minuten duren om de toepassingsgateway te maken in Azure. Nadat de app is gemaakt, controleert u de status van de back-end met `az network application-gateway show-backend-health` .  Hiermee wordt gecontroleerd of de toepassings gateway uw toepassing bereikt via de persoonlijke FQDN.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

De uitvoer geeft de status in orde van de back-end-groep aan.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Toegang tot uw toepassing via het open bare frontend-IP-adres van de toepassings gateway

Haal het open bare IP-adres van de toepassings gateway op met behulp van `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Kopieer het openbare IP-adres en plak het in de adresbalk van de browser.

  ![App in openbaar IP](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Zie ook

- [Problemen met Azure Spring Cloud in VNET oplossen](spring-cloud-troubleshooting-vnet.md)
- [Verantwoordelijkheden van de klant voor het uitvoeren van Azure Spring Cloud in VNET](spring-cloud-vnet-customer-responsibilities.md)