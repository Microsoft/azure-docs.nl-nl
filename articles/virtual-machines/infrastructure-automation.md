---
title: Infrastructuur Automation-hulpprogram ma's gebruiken
description: Meer informatie over het gebruik van infrastructuur Automation-hulpprogram ma's zoals Ansible, chef, puppet, terraform en Packer voor het maken en beheren van virtuele machines in Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: a20c19c3da89801570ca4d74a646e3cf0d8434b6
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675899"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Infrastructuur Automation-hulpprogram ma's gebruiken met virtuele machines in azure

Om Azure virtual machines (Vm's) op een consistente manier te maken en te beheren, is een vorm van automatisering doorgaans gewenst. Er zijn veel hulpprogram ma's en oplossingen waarmee u de volledige levens cyclus van Azure-infrastructuur implementatie en-beheer kunt automatiseren. In dit artikel worden enkele van de hulpprogram ma's voor het automatiseren van de infra structuur geïntroduceerd die u in azure kunt gebruiken. Deze hulpprogram ma's passen doorgaans in op een van de volgende benaderingen:

- De configuratie van Vm's automatiseren
    - Hulpprogram ma's zijn onder andere [Ansible](#ansible), [chef](#chef), [Puppet](#puppet)en [Azure Resource Manager sjabloon](#azure-resource-manager-template).
    - Hulpprogram ma's die specifiek zijn voor VM-aanpassing zijn onder andere [Cloud-init](#cloud-init) voor Linux Vm's, [Power shell desired state Configuration (DSC)](#powershell-dsc)en de [aangepaste script extensie voor Azure](#azure-custom-script-extension) voor alle Azure-vm's.

- Infrastructuur beheer automatiseren
    - Hulpprogram ma's [voor het](#packer) automatiseren van aangepaste VM-installatie kopieën en [terraform](#terraform) voor het automatiseren van het proces voor het maken van de infra structuur.
    - [Azure Automation](#azure-automation) kunt acties uitvoeren in uw Azure-en on-premises infra structuur.

- Toepassings implementatie en-levering automatiseren
    - Voor beelden hiervan zijn [Azure DevOps Services](#azure-devops-services) en [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) is een automatiserings engine voor configuratie beheer, het maken van vm's of het implementeren van toepassingen. Ansible maakt gebruik van een model zonder agent, meestal met SSH-sleutels, om doel machines te verifiëren en te beheren. Configuratie taken worden gedefinieerd in playbooks, met een aantal Ansible-modules die beschikbaar zijn om specifieke taken uit te voeren. Zie [How Ansible Works](https://www.ansible.com/how-ansible-works)(Engelstalig) voor meer informatie.

Leer hoe u het volgende doet:

- [Ansible op Linux installeren en configureren voor gebruik met Azure](/azure/developer/ansible/install-on-linux-vm).
- [Maak een virtuele Linux-machine](/azure/developer/ansible/vm-configure).
- [Een virtuele Linux-machine beheren](/azure/developer/ansible/vm-manage).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) is een automatiserings platform waarmee u kunt bepalen hoe uw infra structuur is geconfigureerd, geïmplementeerd en beheerd. Er zijn extra onderdelen opgenomen chefe habitat voor het automatiseren van de toepassings levenscyclus in plaats van de-infra structuur en de chef-specificatie die helpt bij de automatisering van de naleving van beveiligings-en beleids vereisten. Chef-clients worden geïnstalleerd op doel computers, met een of meer centrale Chef-servers die de configuraties opslaan en beheren. Zie [een overzicht van chef](https://docs.chef.io/chef_overview.html)voor meer informatie.

Leer hoe u het volgende doet:

- [Implementeer chef automatiseren vanuit Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Installeer chef in Windows en maak Azure-vm's](/azure/developer/chef/windows-vm-configure).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) is een platform voor automatisering op bedrijfs niveau waarmee het leverings-en implementatie proces van de toepassing wordt afgehandeld. Agents worden geïnstalleerd op doel machines zodat Puppet Master manifesten kan uitvoeren die de gewenste configuratie van de Azure-infra structuur en Vm's definiëren. Puppet kan worden geïntegreerd met andere oplossingen, zoals Jenkins en GitHub voor een verbeterde devops-werk stroom. Zie [How Puppet Works](https://puppet.com/products/how-puppet-works)(Engelstalig) voor meer informatie.

Leer hoe u het volgende doet:

- [Puppet implementeren](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Omdat Cloud-init wordt aangeroepen tijdens het eerste opstart proces, zijn er geen extra stappen of vereiste agents om uw configuratie toe te passen.  `#cloud-config`Zie de [Cloud-init-documentatie site](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)voor meer informatie over het correct format teren van uw bestanden.  `#cloud-config` bestanden zijn tekst bestanden die worden gecodeerd in base64.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

We werken samen met onze goedgekeurde Linux distributie-partners om ervoor te zorgen dat installatie kopieën met Cloud-init beschikbaar zijn op de Azure Marketplace. Met deze installatie kopieën kunnen uw Cloud-init-implementaties en-configuraties naadloos samen werken met Vm's en virtuele-machine schaal sets.
Meer informatie over Cloud-init op Azure:

- [Cloud-init-ondersteuning voor virtuele Linux-machines in azure](./linux/using-cloud-init.md)
- [Probeer een zelf studie over automatische VM-configuratie met behulp van Cloud-init](./linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Power shell desired state Configuration (DSC)](/powershell/scripting/dsc/overview/overview) is een beheer platform voor het definiëren van de configuratie van doel computers. DSC kan ook worden gebruikt op Linux via de [Omi-server (open Management Infrastructure)](https://collaboration.opengroup.org/omi/).

DSC-configuraties bepalen wat er op een machine moet worden geïnstalleerd en hoe de host moet worden geconfigureerd. Een lokale Configuration Manager (LCM)-engine wordt uitgevoerd op elk doel knooppunt dat gevraagde acties verwerkt op basis van gepushte configuraties. Een pull-server is een webservice die wordt uitgevoerd op een centrale host om de DSC-configuraties en de bijbehorende resources op te slaan. De pull-server communiceert met de ICM-engine op elke doelhost om de vereiste configuraties te bieden en te rapporteren over de naleving.

Leer hoe u het volgende doet:

- [Maak een basis DSC-configuratie](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Een DSC-pull-server configureren](/powershell/scripting/dsc/pull-server/pullserver).
- [Gebruik DSC voor Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Aangepaste scriptextensie voor Azure
De aangepaste script extensie van Azure voor [Linux](./extensions/custom-script-linux.md) of [Windows](./extensions/custom-script-windows.md) downloadt en voert scripts uit op Azure vm's. U kunt de uitbrei ding gebruiken wanneer u een virtuele machine maakt of wanneer de virtuele machine in gebruik is.

Scripts kunnen worden gedownload uit Azure Storage of een open bare locatie, zoals een GitHub-opslag plaats. Met de aangepaste script extensie kunt u scripts schrijven in elke taal die op de bron-VM wordt uitgevoerd. Deze scripts kunnen worden gebruikt voor het installeren van toepassingen of het configureren van de virtuele machine naar wens. Als u referenties wilt beveiligen, kunnen gevoelige gegevens, zoals wacht woorden, worden opgeslagen in een beveiligde configuratie. Deze referenties worden alleen in de virtuele machine ontsleuteld.

Leer hoe u het volgende doet:

- [Maak een virtuele Linux-machine met de Azure CLI en gebruik de aangepaste script extensie](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx?toc=%2fcli%2fazure%2ftoc.json).
- [Maak een Windows-VM met Azure PowerShell en gebruik de aangepaste script extensie](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis).


## <a name="packer"></a>Packer
De [verpakker](https://www.packer.io) automatiseert het bouw proces wanneer u een aangepaste VM-installatie kopie maakt in Azure. U gebruikt Packer voor het definiëren van het besturings systeem en het uitvoeren van scripts na de configuratie waarmee de virtuele machine wordt aangepast aan uw specifieke behoeften. Eenmaal geconfigureerd, wordt de virtuele machine vastgelegd als een beheerde schijf installatie kopie. De verpakker automatiseert het proces voor het maken van de bron-VM, netwerk-en opslag resources, het uitvoeren van configuratie scripts en het maken van de VM-installatie kopie.

Leer hoe u het volgende doet:

- [Gebruik Packer voor het maken van een installatie kopie van een virtuele Linux-machine in azure](./linux/build-image-with-packer.md).
- [Gebruik Packer om een Windows VM-installatie kopie te maken in azure](./windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) is een Automation-hulp programma waarmee u een volledige Azure-infra structuur kunt definiëren en maken met een taal voor de indeling van één sjabloon-de HashiCorp-configuratie taal (HCL). Met terraform definieert u sjablonen waarmee het proces wordt geautomatiseerd voor het maken van netwerk-, opslag-en VM-bronnen voor een bepaalde toepassings oplossing. U kunt uw bestaande terraform-sjablonen gebruiken voor andere platforms met Azure om consistentie te garanderen en de implementatie van de infra structuur te vereenvoudigen zonder dat u hoeft te converteren naar een Azure Resource Manager sjabloon.

Leer hoe u het volgende doet:

- [Installeer en configureer terraform met Azure](/azure/developer/terraform/getting-started-cloud-shell).
- [Een Azure-infra structuur maken met terraform](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) gebruikt runbooks voor het verwerken van een reeks taken op de vm's die u wilt richten. Azure Automation wordt gebruikt om bestaande Vm's te beheren in plaats van een infra structuur te maken. Azure Automation kunnen worden uitgevoerd op zowel Linux-als Windows-Vm's, evenals on-premises virtuele of fysieke machines met een hybride runbook worker. Runbooks kunnen worden opgeslagen in een opslag plaats voor bron beheer, zoals GitHub. Deze runbooks kunnen vervolgens hand matig of volgens een gedefinieerd schema worden uitgevoerd.

Azure Automation biedt ook een desired state Configuration (DSC)-service waarmee u definities kunt maken voor het configureren van een bepaalde set Vm's. DSC zorgt er vervolgens voor dat de vereiste configuratie wordt toegepast en dat de virtuele machine consistent blijft. Azure Automation DSC wordt uitgevoerd op Windows-en Linux-computers.

Leer hoe u het volgende doet:

- [Een Power shell-Runbook maken](../automation/learn/automation-tutorial-runbook-textual-powershell.md).
- [Gebruik Hybrid Runbook worker om on-premises resources te beheren](../automation/automation-hybrid-runbook-worker.md).
- [Gebruik Azure Automation DSC](../automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) is een suite met hulpprogram ma's die u helpen bij het delen en volgen van code, het gebruik van geautomatiseerde builds en het maken van een volledige, doorlopende CI/cd-pijp lijn voor integratie en ontwikkeling. Azure DevOps Services kan worden geïntegreerd met Visual Studio en andere editors om het gebruik te vereenvoudigen. Azure DevOps Services kan ook virtuele Azure-machines maken en configureren en vervolgens code implementeren.

Meer informatie over:

- [Azure DevOps Services](/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://jenkins.io) is een continue integratie server die helpt toepassingen te implementeren en testen en om automatische pijp lijnen te maken voor het leveren van code. Er zijn honderden invoeg toepassingen om het kern Jenkins-platform uit te breiden en u kunt ook integreren met vele andere producten en oplossingen via webhooks. U kunt Jenkins hand matig installeren op een virtuele Azure-machine, Jenkins uitvoeren vanuit een docker-container of een vooraf gemaakte Azure Marketplace-installatie kopie gebruiken.

Leer hoe u het volgende doet:

- [Maak een ontwikkel infrastructuur op een virtuele Linux-machine in azure met Jenkins, github en docker](/azure/developer/jenkins/pipeline-with-github-and-docker).


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
[Azure Resource Manager](../azure-resource-manager/templates/overview.md) is de implementatie- en beheerservice voor Azure. Er wordt een Management-laag geboden waarmee u resources in uw Azure-abonnement kunt maken, bijwerken en verwijderen. U kunt beheerfuncties gebruiken, zoals toegangscontrole, vergrendelingen en tags, om uw resources te beveiligen en te organiseren na de implementatie.

Leer hoe u het volgende doet:

- [Implementatie van virtuele machines met behulp van een resource manager-sjabloon](./linux/spot-template.md).
- [Een virtuele Windows-machine maken op basis van een resource manager-sjabloon](./windows/ps-template.md).
- [Down load de sjabloon voor een virtuele machine](/previous-versions/azure/virtual-machines/windows/download-template).
- [Een Azure Image Builder-sjabloon maken](./linux/image-builder-json.md).

## <a name="next-steps"></a>Volgende stappen
Er zijn veel verschillende opties voor het gebruik van infrastructuur Automation-hulpprogram ma's in Azure. U hebt de vrijheid om de oplossing te gebruiken die het beste past bij uw behoeften en omgeving. Zie de aanpassing van een [Linux](./linux/tutorial-automate-vm-deployment.md) -of [Windows](./windows/tutorial-automate-vm-deployment.md) -VM automatiseren om aan de slag te gaan en enkele van de hulpprogram ma's in azure uit te proberen.