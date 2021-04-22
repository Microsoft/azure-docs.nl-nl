---
title: Beheeradressen
description: Zoek de beheeradressen die worden gebruikt om een App Service Environment. Ze zijn geconfigureerd in een routetabel om asymmetrische routeringsproblemen te voorkomen.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 03/22/2021
ms.author: ccompy
ms.custom: seodec18, references_regions, devx-track-azurecli
ms.openlocfilehash: 796ee38140e72a56f1f22b0594dd904a43ac53c0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865221"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment-beheeradressen

De App Service Environment (ASE) is een implementatie met één tenant van de Azure App Service die wordt uitgevoerd in uw Azure Virtual Network (VNet).  Hoewel de ASE wel wordt uitgevoerd in uw VNet, moet deze nog steeds toegankelijk zijn vanaf een aantal toegewezen IP-adressen die worden gebruikt door de Azure App Service om de service te beheren.  In het geval van een AS-omgeving passeert het beheerverkeer het door de gebruiker beheerde netwerk. Als dit verkeer wordt geblokkeerd of verkeerd wordt omgeleid, wordt de ASE geblokkeerd. Lees Netwerkoverwegingen en de App Service Environment voor meer informatie over de [ASE-App Service Environment.][networking] Voor algemene informatie over de ASE kunt u beginnen met [Inleiding tot de App Service Environment][intro].

Alle ASE's hebben een openbaar VIP waarmee beheerverkeer binnenkomt. Het binnenkomende beheerverkeer van deze adressen komt van binnen naar de poorten 454 en 455 op het openbare VIP van uw ASE. Dit document bevat de App Service bronadressen voor beheerverkeer naar de ASE. Deze adressen zijn ook in de IP-servicetag AppServiceManagement.

De onderstaande adressen kunnen worden geconfigureerd in een routetabel om asymmetrische routeringsproblemen met het beheerverkeer te voorkomen. Routes reageren op verkeer op IP-niveau en zijn niet op de hoogte van de verkeersrichting of dat het verkeer deel uitmaakt van een TCP-antwoordbericht. Als het antwoordadres voor een TCP-aanvraag verschilt van het adres waar deze naar is verzonden, hebt u een asymmetrisch routeringsprobleem. Om asymmetrische routeringsproblemen met uw ASE-beheerverkeer te voorkomen, moet u ervoor zorgen dat antwoorden worden teruggestuurd vanaf hetzelfde adres waar ze naar zijn verzonden. Lees Configure [your ASE with forced tunneling][forcedtunnel] (Uw ASE configureren met geforceerd tunnelen) voor meer informatie over het configureren van uw ASE voor gebruik in een omgeving waar uitgaand verkeer on-premises wordt verzonden.

## <a name="list-of-management-addresses"></a>Lijst met beheeradressen ##

| Region | Adressen |
|--------|-----------|
| Alle openbare regio's | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 20.37.74.128, 23.96.195.3, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.107.58.192, 51.107.154.192, 51.116.58.192, 51.116.155.0, 51.120.99.0, 51.120.219.0, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 65.52.250.128, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.50.128, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure China | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Een netwerkbeveiligingsgroep configureren

Met netwerkbeveiligingsgroepen hoeft u zich geen zorgen te maken over de afzonderlijke adressen of uw eigen configuratie te onderhouden. Er is een IP-servicetag met de naam AppServiceManagement die up-to-date blijft met alle adressen. Als u deze IP-servicetag in uw NSG wilt gebruiken, gaat u naar de portal, opent u de gebruikersinterface van uw netwerkbeveiligingsgroepen en selecteert u Inkomende beveiligingsregels. Als u een bestaande regel voor het binnenkomende beheerverkeer hebt, bewerkt u deze. Als deze NSG niet is gemaakt met uw ASE of als deze allemaal nieuw is, selecteert u **Toevoegen.** Selecteer servicetag in de **vervolgkeuzekeuzeop De bron.**  Selecteer appServiceManagement onder de tag **Bronservice.** Stel de bronpoortbereiken in op , Bestemming op Alle , Doelpoortbereiken \* **op 454-455,** Protocol op **TCP** en Actie op **Toestaan.**  Als u de regel maakt, moet u de Prioriteit instellen. 

![een NSG maken met de servicetag][1]

## <a name="configuring-a-route-table"></a>Een routetabel configureren

De beheeradressen kunnen in een routetabel met een volgende hop internet worden geplaatst om ervoor te zorgen dat al het inkomende beheerverkeer via hetzelfde pad kan teruggaan. Deze routes zijn nodig bij het configureren van geforceerd tunnelen. Als u de routetabel wilt maken, kunt u de portal, PowerShell of Azure CLI gebruiken.  Hieronder vindt u de opdrachten voor het maken van een routetabel met behulp van Azure CLI vanuit een PowerShell-prompt. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "20.37.74.128", "23.96.195.3", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.107.58.192", "51.107.154.192", "51.116.58.192", "51.116.155.0", "51.120.99.0", "51.120.219.0", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "65.52.250.128", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.50.128", "191.233.203.64", "191.236.154.88"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Nadat de routetabel is gemaakt, moet u deze instellen op uw ASE-subnet.  

## <a name="get-your-management-addresses-from-api"></a>Uw beheeradressen van de API op halen ##

U kunt de beheeradressen die overeenkomen met uw ASE, met de volgende API-aanroep.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

De API retourneert een JSON-document met alle binnenkomende adressen voor uw ASE. De lijst met adressen bevat de beheeradressen, het VIP dat wordt gebruikt door uw ASE en het adresbereik van het ASE-subnet zelf.  

Als u de API wilt aanroepen [met de armclient,](https://github.com/projectkudu/ARMClient) gebruikt u de volgende opdrachten, maar vervangt u uw abonnements-id, resourcegroep en ASE-naam.  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
