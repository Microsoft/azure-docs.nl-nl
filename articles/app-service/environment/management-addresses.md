---
title: Beheeradressen
description: Zoek de beheer adressen die worden gebruikt voor het beheren van een App Service Environment. Ze zijn geconfigureerd in een route tabel om problemen met asymmetrische route ring te voor komen.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 02/11/2021
ms.author: ccompy
ms.custom: seodec18, references_regions
ms.openlocfilehash: 7810ca71ccdd8714773ffd459ef38b4bf80de02c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371614"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment-beheer adressen

De App Service Environment (ASE) is een implementatie met één Tenant van de Azure App Service die wordt uitgevoerd in uw Azure-Virtual Network (VNet).  Terwijl de ASE wordt uitgevoerd in uw VNet, moet deze nog steeds toegankelijk zijn vanaf een aantal toegewezen IP-adressen die worden gebruikt door de Azure App Service voor het beheren van de service.  In het geval van een ASE passeert het beheer verkeer het door de gebruiker beheerde netwerk. Als dit verkeer wordt geblokkeerd of niet wordt gerouteerd, wordt de ASE onderbroken. Lees voor meer informatie over de ASE-netwerk afhankelijkheden [en de app service Environment][networking]. Voor algemene informatie over de ASE kunt u beginnen met [Inleiding tot de app service Environment][intro].

Alle as hebben een open bare VIP waar beheer verkeer binnenkomt. Het binnenkomende beheer verkeer van deze adressen wordt opgehaald van op de poorten 454 en 455 op het open bare VIP van uw ASE. Dit document bevat de App Service bron adressen voor beheer verkeer naar het ASE. Deze adressen bevinden zich ook in het IP-service label met de naam AppServiceManagement.

De hieronder vermelde adressen kunnen in een route tabel worden geconfigureerd om asymmetrische routerings problemen met het beheer verkeer te voor komen. Routes handelen op het IP-niveau naar het verkeer en hebben geen inzicht in de richting van het verkeer of het verkeer maakt deel uit van een TCP-antwoord bericht. Als het antwoord adres voor een TCP-aanvraag afwijkt van het adres dat is verzonden naar, hebt u een asymmetrisch routerings probleem. Om te voor komen dat er asymmetrische routerings problemen optreden met uw ASE-beheer verkeer, moet u ervoor zorgen dat antwoorden worden verzonden vanaf hetzelfde adres waarnaar ze zijn verzonden. Lees voor meer informatie over het configureren van uw ASE voor het uitvoeren van een omgeving waar uitgaand verkeer on-premises wordt verzonden [uw ASE configureren met geforceerde tunneling][forcedtunnel]

## <a name="list-of-management-addresses"></a>Lijst met beheer adressen ##

| Region | Adressen |
|--------|-----------|
| Alle openbare regio's | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.203.64, 191.236.154.88, 52.181.183.11 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure China | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Een netwerk beveiligings groep configureren

Met netwerk beveiligings groepen hoeft u zich geen zorgen te maken over de afzonderlijke adressen of om uw eigen configuratie te behouden. Er is een IP-service-tag met de naam AppServiceManagement die up-to-date wordt gehouden met alle adressen. Als u dit IP-service label in uw NSG wilt gebruiken, gaat u naar de portal, opent u de gebruikers interface voor netwerk beveiligings groepen en selecteert u regels voor binnenkomende beveiliging. Als u een vooraf bestaande regel voor het inkomende beheer verkeer hebt, bewerkt u deze. Als deze NSG niet is gemaakt met uw ASE, of als het om een nieuw item gaat, selecteert u **toevoegen**. Selecteer in de vervolg keuzelijst bron de optie **service label**.  Selecteer in het label bron service de optie **AppServiceManagement**. Stel de bronbereiken van de bron poort in op \* een doel poort **454-455** bereik op **een wille keurige** locatie en op het **TCP**-protocol, en op te **geven** actie. Als u de regel maakt, moet u de prioriteit instellen. 

![een NSG maken met het servicetag][1]

## <a name="configuring-a-route-table"></a>Een route tabel configureren

De beheer adressen kunnen in een route tabel met de volgende hop van Internet worden geplaatst om ervoor te zorgen dat alle inkomende beheer verkeer kan terugvallen op hetzelfde pad. Deze routes zijn nodig bij het configureren van geforceerde tunneling. Als u de route tabel wilt maken, kunt u de portal, Power shell of Azure CLI gebruiken.  De opdrachten voor het maken van een route tabel met behulp van Azure CLI vanuit een Power shell-prompt vindt u hieronder. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.203.64", "191.236.154.88", "52.181.183.11"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Nadat de route tabel is gemaakt, moet u deze instellen in het ASE-subnet.  

## <a name="get-your-management-addresses-from-api"></a>Uw beheer adressen ophalen van API ##

U kunt de beheer adressen die overeenkomen met uw ASE weer geven met de volgende API-aanroepen.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

De API retourneert een JSON-document dat alle inkomende adressen voor uw ASE bevat. De lijst met adressen bevat de beheer adressen, het VIP dat door uw ASE wordt gebruikt en het adres bereik van het ASE-subnet zelf.  

Als u de API wilt aanroepen met de [armclient](https://github.com/projectkudu/ARMClient) , gebruikt u de volgende opdrachten, maar vervangt u de naam van uw abonnements-id, resource groep en ASE.  

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
