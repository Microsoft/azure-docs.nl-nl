---
title: Overzicht van Linux-VM’s in Azure
description: Overzicht van virtuele Linux-machines in Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe1db967dac8a76119733f930e54a38160395932
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549835"
---
# <a name="linux-virtual-machines-in-azure"></a>Virtuele Linux-machines in Azure

Azure Virtual Machines (VM) vormen een van de diverse typen [schaalbare on-demand computerresources](/azure/architecture/guide/technology-choices/compute-decision-tree) die Azure biedt. Normaal gesproken kiest u voor een VM wanneer u meer controle nodig hebt over de computeromgeving dan andere opties bieden. In dit artikel vindt u informatie over wat u moet overwegen voordat u een VM maakt, hoe u deze maakt en hoe u deze beheert.

Een VM in Azure biedt u de flexibiliteit van virtualisatie zonder dat u de fysieke hardware hoeft te kopen en te beheren waarop de VM wordt uitgevoerd. U moet de VM echter wel onderhouden door taken uit te voeren, zoals het configureren, patchen en onderhouden van de software die erop wordt uitgevoerd.

Virtuele machines in Azure kunnen op verschillende manieren worden gebruikt. Een aantal voorbeelden:

* **Ontwikkeling en tests**: Azure-VM’s bieden een snelle en eenvoudige manier om een computer te maken met specifieke configuraties die nodig zijn voor de code van een toepassing en het testen ervan.
* **Toepassingen in de cloud**: De vraag naar uw toepassing kan variëren, dus kan het financieel verstandig zijn om deze uit te voeren op een VM in Azure. U betaalt voor extra virtuele machines wanneer u ze nodig hebt en schakelt ze uit wanneer u ze niet meer nodig hebt.
* **Uitgebreid datacenter**: Virtuele machines in een virtueel Azure-netwerk kunnen eenvoudig worden verbonden met het netwerk van uw organisatie.

Het aantal virtuele machines dat uw toepassing gebruikt, kan omhoog worden geschaald naar wat is vereist om te voldoen aan uw behoeften.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Waar moet ik over nadenken voordat ik een VM maak?
Er is altijd een groot aantal [overwegingen bij het ontwerpen](/azure/architecture/reference-architectures/n-tier/linux-vm) wanneer u de infrastructuur van een toepassing verder uitwerkt in Azure. Deze aspecten van een VM zijn belangrijk om over na te denken voordat u begint:

* De namen van uw toepassingsresources
* De locatie waar de resources worden opgeslagen
* De grootte van de VM
* Het maximumaantal VM's dat kan worden gemaakt
* Het besturingssysteem dat op de VM wordt uitgevoerd
* De configuratie van de VM nadat deze is gestart
* De gerelateerde resources die de VM nodig heeft

### <a name="locations"></a>Locaties
Alle resources die in Azure zijn gemaakt, worden verdeeld over meerdere [geografische regio's](https://azure.microsoft.com/regions/) over de hele wereld. Normaal gesproken heet de regio **locatie** wanneer u een VM maakt. Voor een VM geeft de locatie aan waar de virtuele harde schijven zijn opgeslagen.

In deze tabel staan enkele manieren om een lijst met beschikbare locaties te verkrijgen.

| Methode | Beschrijving |
| --- | --- |
| Azure Portal |Selecteer een locatie in de lijst bij het maken van een VM. |
| Azure PowerShell |Gebruik de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| REST-API |Gebruik de bewerking [Locaties vermelden](/rest/api/resources/subscriptions). |
| Azure CLI |Gebruik de bewerking [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Beschikbaarheid
Voor Azure is een toonaangevende serviceovereenkomst (SLA) van 99,9% aangekondigd voor één VM-instantie. Hiervoor geldt wel als voorwaarde dat de virtuele machine wordt geïmplementeerd met Premium-opslag voor alle schijven.  Als u wilt dat uw VM-implementatie in aanmerking komt voor de SLA van 99,95%, moet u bovendien een beschikbaarheidsset maken met ten minste twee VM's waarop uw workload wordt uitgevoerd. Dit zorgt ervoor dat uw VM's worden verdeeld over meerdere foutdomeinen in de Azure-datacenters en worden geïmplementeerd op hosts met verschillende onderhoudsvensters. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

## <a name="vm-size"></a>VM-grootte
De [grootte](../sizes.md) van de VM die u gebruikt, wordt bepaald door de workload die u wilt uitvoeren. De grootte die u vervolgens kiest, bepaalt factoren als processorsnelheid, geheugen en opslagcapaciteit. Azure biedt een groot aantal verschillende grootten voor verschillende manieren van gebruik.

Azure rekent een [uurprijs](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) op basis van de grootte en het besturingssysteem van de VM. Voor niet-hele uren worden alleen de minuten van gebruik in rekening gebracht. De opslag wordt afzonderlijk berekend en in rekening gebracht.

## <a name="vm-limits"></a>VM-limieten
Uw abonnement heeft een standaard [quotumlimiet](../../azure-resource-manager/management/azure-subscription-service-limits.md) ingebouwd die de implementatie van veel VM’s voor uw project kan beïnvloeden. De huidige limiet per abonnement is 20 VM's per regio. Limieten kunnen worden verhoogd door [een ondersteuningsticket in te dienen met een aanvraag voor een verhoging](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Beheerde schijven

Managed Disks beheert het maken/beheren van Azure Storage-accounts op de achtergrond en zorgt ervoor dat u zich geen zorgen hoeft te maken over de schaalbaarheidslimieten van het opslagaccount. U hoeft alleen de schijfgrootte en prestatielaag (Standard of Premium) op te geven en Azure maakt en beheert de schijf voor u. Ook als u schijven toevoegt of de virtuele machine omhoog of omlaag schaalt, hoeft u zich geen zorgen te maken over de gebruikte opslag. Als u nieuwe VM's gaat maken, [gebruik dan de Azure CLI 2.0](quick-create-cli.md) of Azure Portal om VM's te maken met een beheerd besturingssysteem en beheerde gegevensschijven. Als u VM's hebt met niet-beheerde schijven, kunt u [deze converteren om ze te ondersteunen met Managed Disks](convert-unmanaged-to-managed-disks.md).

U kunt ook uw aangepaste installatiekopieën in één opslagaccount per Azure-regio beheren en deze gebruiken om honderden virtuele machines onder hetzelfde abonnement te maken. Voor meer informatie over Managed Disks raadpleegt u [Managed Disks Overview](../managed-disks-overview.md) (Overzicht van Managed Disks).

## <a name="distributions"></a>Distributies 
Microsoft Azure ondersteunt de uitvoering van een aantal populaire Linux-distributies die worden geleverd en onderhouden door een aantal partners.  U kunt beschikbare distributies vinden in Azure Marketplace. Microsoft werkt actief samen met diverse Linux-community's om nog meer varianten toe te voegen aan [de lijst met Linux-distributies die zijn goedgekeurd voor Azure](endorsed-distros.md).

Als de Linux-distributie van uw keuze niet op de lijst staat, kunt u zelf een Linux-VM maken ('Bring Your Own Linux') door [een Linux-VHD te maken en uploaden in Azure](create-upload-generic.md).

Microsoft werkt nauw samen met partners om ervoor te zorgen dat de beschikbare installatiekopieën worden bijgewerkt en geoptimaliseerd voor een Azure-runtime.  Voor meer informatie over aanbiedingen van Azure-partners klikt u op de volgende koppelingen:

* [Linux op door Azure goedgekeurde distributies](endorsed-distros.md)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Canonical - [Azure Marketplace - Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian - [Azure Marketplace - Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD - [Azure Marketplace - FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar - [Azure Marketplace - Flatcar Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS - [Azure Marketplace - RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami - [Bitnami Library voor Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS voor Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker - [Azure Marketplace - Docker-installatiekopieën](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

Voor een juiste DevOps-cultuur moet alle infrastructuur uit code bestaan.  Als alle infrastructuur bestaat uit code, kan deze namelijk eenvoudig opnieuw worden opgebouwd.  Azure werkt met de belangrijkste automatiseringstools zoals Ansible, Chef, SaltStack en Puppet.  Azure heeft ook eigen tools voor automatisering:

* [Azure-sjablonen](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

Azure biedt ondersteuning voor [cloud init](https://cloud-init.io/) voor de meeste Linux-distributies die dit ondersteunen.  We werken samen met onze goedgekeurde Linux-distributiepartners om ervoor te zorgen dat installatiekopieën met cloud-init beschikbaar zijn op de Azure Marketplace. Met deze installatiekopieën kunnen uw cloud-init-implementaties en -configuraties naadloos werken met VM's en virtuele-machineschaalsets.

* [Cloud-init gebruiken op virtuele Linux-machines in Azure](using-cloud-init.md)

## <a name="storage"></a>Storage
* [Inleiding tot Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Een schijf toevoegen aan een virtuele Linux-machine](add-disk.md)
* [Een gegevensschijf koppelen aan een Linux-VM in Azure Portal](attach-disk-portal.md)

## <a name="networking"></a>Netwerken
* [Overzicht van Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [IP-adressen in Azure](../../virtual-network/public-ip-addresses.md)
* [Poorten openen voor een Linux-VM in Azure](nsg-quickstart.md)
* [Een Fully Qualified Domain Name maken in Azure Portal](../create-fqdn.md)


## <a name="data-residency"></a>Gegevenslocatie

In Azure is de functie om het opslaan van klantgegevens in één regio in te schakelen, momenteel alleen beschikbaar in de regio Azië - zuidoost (Singapore) van het geografisch gebied Azië en Stille Oceaan en in Brazilië - zuid van het geografisch gebied Brazilië (staat Sao Paulo). Voor alle andere regio's worden klantgegevens opgeslagen in Geo. Zie [Trust Center](https://azure.microsoft.com/global-infrastructure/data-residency/) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Maak uw eerste VM.

- [Portal](quick-create-portal.md)
- [Azure-CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
