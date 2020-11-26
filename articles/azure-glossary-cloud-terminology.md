---
title: Azure-woorden lijst-Azure Dictionary | Microsoft Docs
description: Gebruik de woorden lijst van Azure om inzicht te krijgen in de Cloud terminologie op het Azure-platform. Deze korte Azure-woorden lijst bevat definities voor algemene Cloud voorwaarden voor Azure.
keywords: Azure Dictionary, Cloud terminologie, Azure glossary, terminologie definities, Cloud voorwaarden
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 33672e90406b94a1b5788803911c072cdc2577f7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186385"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure verklarende woorden lijst: een woorden lijst met Cloud terminologie op het Azure-platform

De Microsoft Azure verklarende woorden lijst is een korte woorden lijst met Cloud terminologie voor het Azure-platform. Zie ook:

* [Microsoft Azure en Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -definities van Azure-Services en hun AWS-equivalenten.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Cloud computing-voor waarden](https://azure.microsoft.com/overview/cloud-computing-dictionary/) : algemene Cloud voorwaarden van de branche.

## <a name="account"></a>account
Een account dat wordt gebruikt voor toegang tot en beheer van een Azure-abonnement. Dit wordt vaak een Azure-account genoemd, maar een account kan een van de volgende zijn: een bestaande werk-, school-of persoonlijke Microsoft-account. U kunt ook een account voor het beheren van een Azure-abonnement maken wanneer u zich aanmeldt voor de [gratis proef versie](https://azure.microsoft.com).  
Zie [registreren voor een Azure-abonnement met uw Microsoft 365 account](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) en [accounts die u kunt gebruiken om u aan te melden](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>API-app
Een andere naam voor de [App app service](#app-service-app).

## <a name="app-service-app"></a>App Service-app
De reken resources die [Azure app service](app-service/overview.md) biedt voor het hosten van een website of webtoepassing, Web-API of back-end van een [mobiele app](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop). App Service-apps worden ook wel *app Services*, *Web apps*, *API apps* en *mobiele apps* genoemd.

## <a name="availability-set"></a>beschikbaarheidsset
Een verzameling virtuele machines die samen worden beheerd om de redundantie en betrouw baarheid van toepassingen te bieden. Als u een beschikbaarheidsset gebruikt, zorgt u ervoor dat tijdens een geplande of niet-geplande onderhouds gebeurtenis ten minste één virtuele machine beschikbaar is.  
Zie [de beschik baarheid van virtuele Windows-machines beheren](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en [de beschik baarheid van virtuele Linux-machines beheren](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klassieke Azure-implementatie model
Een van de twee [implementatie modellen](./azure-resource-manager/management/deployment-models.md) die worden gebruikt voor het implementeren van resources in azure (het nieuwe model is Azure Resource Manager). Sommige Azure-Services bieden alleen ondersteuning voor het Resource Manager-implementatie model, sommige ondersteunen alleen het klassieke implementatie model en sommige ondersteunen beide. In de documentatie voor elke Azure-service wordt aangegeven welke model (en) ze ondersteunen.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure-opdracht regel interface (CLI)
Een opdracht regel interface die kan worden gebruikt voor het beheren van Azure-Services vanuit Windows, macOS en Linux.  Sommige services of service functies kunnen alleen worden beheerd via Power shell of de CLI. Zie [Azure cli](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Een opdracht regel interface voor het beheren van Azure-Services via een opdracht regel van Windows-Pc's. Sommige services of service functies kunnen alleen worden beheerd via Power shell of de CLI.
Zie [Azure PowerShell installeren en configureren](/powershell/azure/)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager implementatie model
Een van de twee [implementatie modellen](./azure-resource-manager/management/deployment-models.md) die worden gebruikt voor het implementeren van resources in Microsoft Azure (de andere is het klassieke implementatie model). Sommige Azure-Services bieden alleen ondersteuning voor het Resource Manager-implementatie model, sommige ondersteunen alleen het klassieke implementatie model en sommige ondersteunen beide. In de documentatie voor elke Azure-service wordt aangegeven welke model (en) ze ondersteunen.

## <a name="fault-domain"></a>fout domein
Het verzamelen van virtuele machines in een beschikbaarheidsset die mogelijk tegelijk kunnen mislukken. Een voor beeld is een groep machines in een rek die een gemeen schappelijke voedings bron en netwerk switch delen. In Azure worden de virtuele machines in een beschikbaarheidsset automatisch gescheiden in meerdere fout domeinen.  
Zie [de beschik baarheid van virtuele Windows-machines beheren](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) of [de beschik baarheid van virtuele Linux-machines beheren](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>geo
Een gedefinieerde grens voor gegevens locatie die doorgaans twee of meer regio's bevatten. De grenzen kunnen zich binnen of buiten de nationale grenzen bevinden en worden beïnvloed door de belasting verordening. Elke geo heeft ten minste één regio. Voor beelden van geografische gebieden zijn Azië en Stille Oceaan en Japan. Ook wel *geografie* genoemd.  
Zie [Azure-regio's](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>Geo-replicatie
Het proces van het automatisch repliceren van inhoud, zoals blobs, tabellen en wacht rijen binnen een regionaal paar.  
Zie [actieve Geo-Replication voor Azure SQL database](./azure-sql/database/auto-failover-group-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Een bestand dat de configuratie van het besturings systeem en de toepassing bevat die kan worden gebruikt om een wille keurig aantal virtuele machines te maken. In azure zijn er twee soorten installatie kopieën: VM-installatie kopie en installatie kopie van besturings systeem. Een VM-installatie kopie bevat een besturings systeem en alle schijven die zijn gekoppeld aan een virtuele machine wanneer de installatie kopie wordt gemaakt. Een besturingssysteem installatie kopie bevat alleen een gegeneraliseerd besturings systeem zonder configuratie van de gegevens schijf.  
Zie [navigeren en installatie kopieën van virtuele Windows-machines selecteren in azure met Power shell of de CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>limieten
Het aantal resources dat kan worden gemaakt of de prestatie-benchmark waarde die kan worden behaald. Limieten worden meestal gekoppeld aan abonnementen, services en aanbiedingen.  
Zie [Azure-abonnement en service limieten, quota's en beperkingen](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>load balancer
Een resource die binnenkomend verkeer tussen computers in een netwerk distribueert. In azure distribueert een load balancer verkeer naar virtuele machines die in een Load Balancer-set zijn gedefinieerd. Een [Load Balancer](load-balancer/load-balancer-overview.md) kan Internet Facing zijn of het kan intern zijn.  

## <a name="mobile-app"></a>mobiele app
Een andere naam voor de [App app service](#app-service-app).

## <a name="offer"></a>offer
De prijzen, tegoeden en gerelateerde voor waarden die van toepassing zijn op een Azure-abonnement.  
Bekijk de [pagina met details](https://azure.microsoft.com/support/legal/offer-details/) van de Azure-aanbieding

## <a name="portal"></a>portal
De beveiligde webportal die wordt gebruikt om Azure-Services te implementeren en te beheren.

## <a name="region"></a>regio
Een gebied binnen een geografische regio dat geen nationale grenzen overschrijdt en een of meer data centers bevat. Prijzen, regionale Services en aanbiedings typen worden weer gegeven op regio niveau. Een regio is doorgaans gekoppeld aan een andere regio, die Maxi maal honderd kilo meters kan zijn. Regionale paren kunnen worden gebruikt als mechanisme voor nood herstel en scenario's met hoge Beschik baarheid. Ook wel *locatie* genoemd.  
Zie [Azure-regio's](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resource
Een item dat deel uitmaakt van uw Azure-oplossing. Met elke Azure-service kunt u verschillende typen resources implementeren, zoals data bases of virtuele machines.   
Zie [Azure Resource Manager overzicht](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>resourcegroep
Een container in Resource Manager die gerelateerde resources voor een toepassing bevat. De resource groep kan alle resources voor een toepassing bevatten of alleen de resources die logisch zijn gegroepeerd. U kunt bepalen hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is.  
Zie [Azure Resource Manager overzicht](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Resource Manager-sjabloon
Een JSON-bestand waarmee declaratieve een of meer Azure-resources worden gedefinieerd en waarmee afhankelijkheden tussen de geïmplementeerde resources worden gedefinieerd. De sjabloon kan worden gebruikt om de resources consistent en herhaaldelijk te implementeren.  
Zie [Azure Resource Manager sjablonen ontwerpen](./azure-resource-manager/templates/template-syntax.md)

## <a name="resource-provider"></a>resource provider
Een service die de resources levert die u kunt implementeren en beheren via Resource Manager. Elke resourceprovider biedt bewerkingen voor het werken met de resources die zijn geïmplementeerd. Resource providers zijn toegankelijk via de Azure Portal, Azure PowerShell en verschillende Program ma's voor programmering.  
Zie [Azure Resource Manager overzicht](azure-resource-manager/management/overview.md)

## <a name="role"></a>role
Een manier om toegang te beheren die kan worden toegewezen aan gebruikers, groepen en services. Rollen kunnen acties uitvoeren zoals maken, beheren en lezen op Azure-resources.  
Zie [RBAC: ingebouwde rollen](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>service level agreement (SLA)
De overeenkomst met een beschrijving van de toezeg gingen van micro soft voor uptime en connectiviteit. Elke Azure-service heeft een specifieke SLA.  
Zie [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>Shared Access Signature (SAS)
Een hand tekening waarmee u beperkte toegang tot een bron kunt verlenen zonder uw account sleutel te tonen. [Azure Storage maakt bijvoorbeeld gebruik van SAS](./storage/common/storage-sas-overview.md) om client toegang toe te kennen aan objecten zoals blobs. [IOT hub maakt gebruik van SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) om apparaten toestemming te verlenen voor het verzenden van telemetrie.

## <a name="storage-account"></a>opslagaccount
Een account waarmee u toegang krijgt tot de Azure Blob-, wachtrij-, tabel-en bestands Services in Azure Storage. De naam van het opslag account definieert de unieke naam ruimte voor Azure Storage gegevens objecten.  
Zie [over Azure Storage-accounts](./storage/common/storage-account-create.md)

## <a name="subscription"></a>abonnement
De overeenkomst van een klant met micro soft waarmee ze Azure-Services kunnen verkrijgen. De abonnements prijzen en gerelateerde voor waarden gelden voor de aanbieding die is gekozen voor het abonnement.
Zie [Microsoft Online Subscription overeenkomst](https://azure.microsoft.com/support/legal/subscription-agreement/) en [hoe Azure-abonnementen zijn gekoppeld aan Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tag
Een indexerings term waarmee u resources kunt categoriseren op basis van uw vereisten voor het beheren of factureren. Wanneer u een complexe verzameling resources hebt, kunt u Tags gebruiken om deze activa te visualiseren op de manier die het meest zinvol is. U kunt bijvoorbeeld resources taggen die een vergelijkbare rol hebben in uw organisatie of bij dezelfde afdeling horen.  
Zie [Tags gebruiken om uw Azure-resources te organiseren](./azure-resource-manager/management/tag-resources.md)

## <a name="update-domain"></a>domein bijwerken
De verzameling virtuele machines in een beschikbaarheidsset die tegelijkertijd worden bijgewerkt. Virtuele machines in hetzelfde update domein worden samen opnieuw gestart tijdens gepland onderhoud. Azure start nooit meer dan één update domein per keer opnieuw op. Wordt ook wel een upgrade domein genoemd.  
Zie [de beschik baarheid van virtuele Windows-machines beheren](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en [de beschik baarheid van virtuele Linux-machines beheren](./virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>virtuele machine
De software-implementatie van een fysieke computer waarop een besturings systeem wordt uitgevoerd. Meerdere virtuele machines kunnen tegelijkertijd op dezelfde hardware worden uitgevoerd. In azure zijn virtuele machines beschikbaar in verschillende grootten.  
Raadpleeg de [documentatie van virtual machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>extensie van virtuele machine
Een resource die gedrag of functies implementeert die andere Program ma's kunnen gebruiken of waarmee u kunt communiceren met een actieve computer. U kunt bijvoorbeeld de VM-toegangs uitbreiding gebruiken om waarden voor externe toegang op een virtuele Azure-machine opnieuw in te stellen of te wijzigen.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Zie [over extensies en functies van virtuele machines (Windows)](./virtual-machines/extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) of [over extensies en functies van virtuele machines (Linux)](./virtual-machines/extensions/features-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>virtueel netwerk
Een netwerk waarmee u verbinding maakt tussen uw Azure-resources die zijn geïsoleerd van alle andere Azure-tenants. Met een [Azure-VPN gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) kunt u verbindingen tot stand brengen tussen virtuele netwerken en tussen een virtueel netwerk en een on-premises netwerk. U kunt de IP-adres blokken, DNS-instellingen, beveiligings beleid en route tabellen volledig beheren binnen een virtueel netwerk.  
Zie [Virtual Network overzicht](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Web-app
Een andere naam voor de [App app service](#app-service-app).

## <a name="see-also"></a>Zie ook

* [Aan de slag met Azure](https://azure.microsoft.com/get-started/)
* [Cloud Resource Center](https://azure.microsoft.com/resources/)  
* [Azure voor uw zakelijke toepassing](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure in uw Data Center](https://azure.microsoft.com/overview/business-apps-on-azure/)