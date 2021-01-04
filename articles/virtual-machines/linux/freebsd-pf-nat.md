---
title: Pakket filter van FreeBSD gebruiken om een firewall te maken in azure
description: Meer informatie over het implementeren van een NAT-firewall met de PF van FreeBSD in Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: c7027dd86945e53149b3c4daeb9d6faee3593e95
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97702239"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Het pakket filter van FreeBSD gebruiken om een beveiligde firewall te maken in azure
In dit artikel wordt beschreven hoe u een NAT-firewall implementeert met behulp van het pakket filter van FreeBSD via Azure Resource Manager sjabloon voor een algemeen webserver scenario.

## <a name="what-is-pf"></a>Wat is PF?
PF (pakket filter, geschreven, ook wel PF) is een stateful BSD-pakket filter met licentie, een centraal onderdeel van software voor firewalling. PF is sinds snelle ontwikkeling en heeft nu verschillende voor delen ten opzichte van andere beschik bare firewalls. Network Address Translation (NAT) bevindt zich op de eerste dag, waarna pakket planner en actieve wachtrij beheer zijn geïntegreerd in PF, door de ALTQ te integreren en te configureren via de configuratie van PF. Functies zoals pfsync en KARPER voor failover en redundantie, authpf voor sessie verificatie en FTP-proxy om de firewall van het FTP-protocol te vereenvoudigen, hebben ook uitgebreide PF. Kortom, PF is een krachtige en boordevol firewall. 

## <a name="get-started"></a>Aan de slag
Als u geïnteresseerd bent in het instellen van een beveiligde firewall in de Cloud voor uw webservers, kunt u aan de slag gaan. U kunt de scripts die worden gebruikt in deze Azure Resource Manager sjabloon ook Toep assen om uw netwerk topologie in te stellen.
Met de Azure Resource Manager sjabloon wordt een virtuele FreeBSD-machine ingesteld waarmee NAT/Redirection wordt uitgevoerd met behulp van PF en twee FreeBSD virtuele machines met de nginx-webserver geïnstalleerd en geconfigureerd. Naast het uitvoeren van NAT voor het uitgaande verkeer van twee webservers, onderschept de virtuele machine voor NAT/omleiding HTTP-aanvragen en stuurt deze door naar de twee webservers op Round-Robin. Het VNet maakt gebruik van de persoonlijke niet-Routeer bare IP-adres ruimte 10.0.0.2/24, en u kunt de para meters van de sjabloon wijzigen. De sjabloon Azure Resource Manager definieert ook een route tabel voor het hele VNet. Dit is een verzameling afzonderlijke routes die wordt gebruikt om Azure-standaard routes te overschrijven op basis van het doel-IP-adres. 

![Diagram toont een openbaar I P-adres op een NAT-exemplaar dat omleidt door de Round-Robin methode naar twee virtuele machines met back-end die nginx-webservers hosten.](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implementeren via Azure CLI
U hebt de nieuwste [Azure cli](/cli/azure/install-az-cli2) geïnstalleerd en u moet zijn aangemeld bij een Azure-account met behulp van [AZ login](/cli/azure/reference-index). Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voor beeld wordt de naam van een resource groep gemaakt `myResourceGroup` op de `West US` locatie.

```azurecli
az group create --name myResourceGroup --location westus
```

Implementeer vervolgens de sjabloon PF-FreeBSD-Setup met [AZ Deployment Group Create](/cli/azure/deployment/group). Down load azuredeploy.parameters.jsop onder hetzelfde pad en Definieer uw eigen resource waarden, zoals `adminPassword` , `networkPrefix` en `domainNamePrefix` . 

```azurecli
az deployment group create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Na ongeveer vijf minuten krijgt u de gegevens van `"provisioningState": "Succeeded"` . Vervolgens kunt u met behulp van het open bare IP-adres of de FQDN-naam van de frontend-VM (NAT) naar de front-end-VM (NAT) of toegang tot nginx-webserver in een browser. In het volgende voor beeld worden de FQDN en het open bare IP-adres weer gegeven die zijn toegewezen aan de frontend-VM (NAT) in de `myResourceGroup` resource groep. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Volgende stappen
Wilt u uw eigen NAT instellen in azure? Open source, gratis maar krachtig? Vervolgens is PF een goede keuze. Als u de sjabloon PF-FreeBSD-Setup gebruikt, hebt u slechts vijf minuten nodig voor het instellen van een NAT-firewall met Round Robin-taak verdeling met behulp van de PF van FreeBSD in azure voor een gemeen schappelijke webserver scenario. 

Als u meer wilt weten over de aanbieding van FreeBSD in azure, raadpleegt u [Inleiding tot FreeBSD op Azure](freebsd-intro-on-azure.md).

Raadpleeg [FreeBSD Handbook](https://www.freebsd.org/doc/handbook/firewalls-pf.html) of [PF-gebruikers handleiding](https://www.freebsd.org/doc/handbook/firewalls-pf.html)als u meer wilt weten over PF.
