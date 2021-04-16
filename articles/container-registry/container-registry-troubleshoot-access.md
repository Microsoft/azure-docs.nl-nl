---
title: Netwerkproblemen met het register oplossen
description: Symptomen, oorzaken en oplossing van veelvoorkomende problemen bij het openen van een Azure-containerregister in een virtueel netwerk of achter een firewall
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 0fdedf109703eb443904989d2c0b2d75a6ba5bb1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481222"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Netwerkproblemen met het register oplossen

Dit artikel helpt u bij het oplossen van problemen die kunnen ontstaan bij het openen van een Azure-containerregister in een virtueel netwerk of achter een firewall of proxyserver. 

## <a name="symptoms"></a>Symptomen

Kan een of meer van de volgende omvatten:

* Kan geen afbeeldingen pushen of pullen en er wordt een foutmelding weergegeven `dial tcp: lookup myregistry.azurecr.io`
* Kan geen afbeeldingen pushen of pullen en er wordt een foutmelding weergegeven `Client.Timeout exceeded while awaiting headers`
* Kan geen push- of pull-afbeeldingen uitvoeren en u ontvangt een Azure CLI-fout `Could not connect to the registry login server`
* Kan geen afbeeldingen uit het register naar een Azure Kubernetes Service of een andere Azure-service
* Kan geen toegang krijgen tot een register achter een HTTPS-proxy en u ontvangt een `Error response from daemon: login attempt failed with status: 403 Forbidden` foutmelding of `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* Kan de instellingen van het virtuele netwerk niet configureren en u ontvangt een foutmelding `Failed to save firewall and virtual network settings for container registry`
* Kan geen toegang krijgen tot registerinstellingen in een Azure Portal of register beheren met behulp van de Azure CLI
* Kan geen instellingen voor virtuele netwerken of regels voor openbare toegang toevoegen of wijzigen
* ACR-taken kan geen push- of pull-afbeeldingen maken
* Azure Security Center kunt geen afbeeldingen in het register scannen of scanresultaten worden niet weergegeven in Azure Security Center
* Er wordt een `host is not reachable` foutbericht weergegeven wanneer u probeert toegang te krijgen tot een register dat is geconfigureerd met een privé-eindpunt.

## <a name="causes"></a>Oorzaken

* Een clientfirewall of proxy verhindert toegang - [oplossing](#configure-client-firewall-access)
* Toegangsregels voor openbare netwerken in het register verhinderen toegang - [oplossing](#configure-public-access-to-registry)
* Configuratie van virtueel netwerk verhindert toegang - [oplossing](#configure-vnet-access)
* U probeert Azure Security Center of bepaalde andere Azure-services te integreren met een register met een privé-eindpunt, service-eindpunt of openbare IP-toegangsregels - [oplossing](#configure-service-access)

## <a name="further-diagnosis"></a>Verdere diagnose 

Voer de [opdracht az acr check-health](/cli/azure/acr#az-acr-check-health) uit voor meer informatie over de status van de registeromgeving en optioneel toegang tot een doelregister. U kunt bijvoorbeeld bepaalde problemen met de netwerkverbinding of configuratie vaststellen. 

Zie [De status van een Azure-containerregister controleren voor](container-registry-check-health.md) opdrachtvoorbeelden. Als er fouten worden gerapporteerd, bekijkt u [de foutverwijzing](container-registry-health-error-reference.md) en de volgende secties voor aanbevolen oplossingen.

Als u problemen ondervindt met het gebruik van een Azure Kubernetes Service met een geïntegreerd register, voer dan de opdracht [az aks check-acr](/cli/azure/aks#az_aks_check_acr) uit om te controleren of het AKS-cluster het register kan bereiken.

> [!NOTE]
> Sommige symptomen van netwerkconnectiviteit kunnen zich ook voordoen wanneer er problemen zijn met registerverificatie of autorisatie. Zie [Problemen met registerregistratie oplossen.](container-registry-troubleshoot-login.md)

## <a name="potential-solutions"></a>Mogelijke oplossingen

### <a name="configure-client-firewall-access"></a>Clientfirewalltoegang configureren

Als u toegang wilt krijgen tot een register achter een clientfirewall of proxyserver, configureert u firewallregels voor toegang tot de openbare REST- en gegevens-eindpunten van het register. Als [toegewezen gegevens eindpunten zijn ingeschakeld,](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) hebt u regels nodig voor toegang:

* REST-eindpunt: `<registryname>.azurecr.io`
* Gegevens-eindpunt(en): `<registry-name>.<region>.data.azurecr.io`

Configureer voor een geo-gerepliceerd register de toegang tot het gegevens-eindpunt voor elke regionale replica.

Zorg ervoor dat achter een HTTPS-proxy zowel uw Docker-client als de Docker-daemon zijn geconfigureerd voor proxygedrag. Als u de proxyinstellingen voor de Docker-daemon wijzigt, moet u de daemon opnieuw opstarten. 

Registerresourcelogboeken in de tabel ContainerRegistryLoginEvents kunnen helpen bij het vaststellen van een poging tot verbinding die is geblokkeerd.

Gerelateerde links:

* [Regels configureren voor toegang tot een Azure-containerregister achter een firewall](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS-proxyconfiguratie](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Geo-replicatiein Azure Container Registry](container-registry-geo-replication.md)
* [Azure Container Registry voor diagnostische evaluatie en controle](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Openbare toegang tot register configureren

Als u via internet toegang wilt krijgen tot een register, controleert u of het register openbare netwerktoegang vanaf uw client toestaat. Standaard biedt een Azure-containerregister toegang tot de openbare register-eindpunten vanuit alle netwerken. Een register kan de toegang tot geselecteerde netwerken of geselecteerde IP-adressen beperken. 

Als het register is geconfigureerd voor een virtueel netwerk met een service-eindpunt, wordt met het uitschakelen van openbare netwerktoegang ook de toegang via het service-eindpunt uitgeschakeld. Als uw register is geconfigureerd voor een virtueel netwerk met Private Link, zijn IP-netwerkregels niet van toepassing op de privé-eindpunten van het register. 

Gerelateerde links:

* [Regels voor openbare IP-netwerken configureren](container-registry-access-selected-networks.md)
* [Privé verbinding maken met een Azure-containerregister met behulp van Azure Private Link](container-registry-private-link.md)
* [Toegang tot een containerregister beperken met behulp van een service-eindpunt in een virtueel Azure-netwerk](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>VNet-toegang configureren

Controleer of het virtuele netwerk is geconfigureerd met een privé-eindpunt voor Private Link of een service-eindpunt (preview). Momenteel wordt Azure Bastion eindpunt niet ondersteund.

Als een privé-eindpunt is geconfigureerd, controleert u of dns de openbare  FQDN van het register, zoals myregistry.azurecr.io, omkeert naar het privé-IP-adres van het register. Gebruik een netwerkprogramma zoals `dig` of `nslookup` voor DNS-zoekactie. Zorg ervoor [dat DNS-records zijn geconfigureerd voor](container-registry-private-link.md#dns-configuration-options) de FQDN van het register en voor elk van de FQDN's van het gegevens-eindpunt.

Controleer de NSG-regels en servicetags die worden gebruikt om verkeer van andere resources in het netwerk naar het register te beperken. 

Als een service-eindpunt voor het register is geconfigureerd, controleert u of er een netwerkregel is toegevoegd aan het register die toegang toestaat vanuit dat netwerksubnet. Het service-eindpunt ondersteunt alleen toegang vanaf virtuele machines en AKS-clusters in het netwerk.

Als u de registertoegang wilt beperken met behulp van een virtueel netwerk in een ander Azure-abonnement, moet u ervoor zorgen dat u de `Microsoft.ContainerRegistry` resourceprovider in dat abonnement registreert. [Registreer de resourceprovider](../azure-resource-manager/management/resource-providers-and-types.md) voor Azure Container Registry met de Azure Portal, Azure CLI of andere Azure-hulpprogramma's.

Als Azure Firewall of een vergelijkbare oplossing is geconfigureerd in het netwerk, controleert u of het verkeer dat afkomstig is van andere resources, zoals een AKS-cluster, is ingeschakeld om de register-eindpunten te bereiken.

Gerelateerde links:

* [Privé verbinding maken met een Azure-containerregister met behulp van Azure Private Link](container-registry-private-link.md)
* [Verbindingsproblemen met het Azure-privé-eindpunt oplossen](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [De toegang tot een containerregister beperken met behulp van een service-eindpunt in een virtueel Azure-netwerk](container-registry-vnet.md)
* [Vereiste uitgaande netwerkregels en FQDN's voor AKS-clusters](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: DNS-resolutie debuggen](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Servicetags voor virtuele netwerken](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Servicetoegang configureren

Op dit moment is toegang tot een containerregister met netwerkbeperkingen niet toegestaan vanuit verschillende Azure-services:

* Azure Security Center kan geen scan op beveiligingsleed van afbeeldingen [uitvoeren](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) in een register dat de toegang beperkt tot privé-eindpunten, geselecteerde subnetten of IP-adressen. 
* Resources van bepaalde Azure-services hebben geen toegang tot een containerregister met netwerkbeperkingen, waaronder Azure App Service en Azure Container Instances.

Als toegang tot of integratie van deze Azure-services met uw containerregister is vereist, verwijdert u de netwerkbeperking. Verwijder bijvoorbeeld de privé-eindpunten van het register of verwijder of wijzig de openbare toegangsregels van het register.

Vanaf januari 2021 kunt u een register met beperkte netwerktoegang configureren om toegang toe te [staan](allow-access-trusted-services.md) vanuit bepaalde vertrouwde services.

Gerelateerde links:

* [Azure Container Registry scannen van afbeeldingen door Security Center](../security-center/defender-for-container-registries-introduction.md)
* Feedback [geven](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Vertrouwde services veilig toegang geven tot een containerregister met beperkte netwerktoegang](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Geavanceerde probleemoplossing

Als [het verzamelen van resourcelogboeken](container-registry-diagnostics-audit-logs.md) is ingeschakeld in het register, controleert u het logboek ContainterRegistryLoginEvents. In dit logboek worden verificatiegebeurtenissen en -statussen op basis van de binnenkomende identiteit en het IP-adres op de gegevens op een andere plaats op de server op te geven. Zoek in het logboek naar [mislukte registerverificaties.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Gerelateerde links:

* [Logboeken voor diagnostische evaluatie en controle](container-registry-diagnostics-audit-logs.md)
* [Veelgestelde vragen over containerregisters](container-registry-faq.md)
* [Azure-beveiligingsbasislijn voor Azure Container Registry](security-baseline.md)
* [Aanbevolen procedures voor Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Volgende stappen

Als u het probleem hier niet oplost, bekijkt u de volgende opties.

* Andere onderwerpen over het oplossen van problemen met registers zijn:
  * [Problemen met aanmelden bij register oplossen](container-registry-troubleshoot-login.md) 
  * [Problemen met registerprestaties oplossen](container-registry-troubleshoot-performance.md)
* [Ondersteuningsopties voor de](https://azure.microsoft.com/support/community/) community
* [Microsoft Q&A](/answers/products/)
* [Een ondersteuningsticket openen](https://azure.microsoft.com/support/create-ticket/)