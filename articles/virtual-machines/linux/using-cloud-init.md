---
title: Overzicht van Cloud-init-ondersteuning voor Linux-Vm's in azure
description: Overzicht van Cloud-init-functies voor het configureren van een virtuele machine bij het inrichten van de tijd in Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/14/2021
ms.author: danis
ms.openlocfilehash: ac907c2ea2ae53bd192c01232c66e0467025daae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563230"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-init-ondersteuning voor virtuele machines in azure
In dit artikel wordt de ondersteuning beschreven die bestaat voor [Cloud-init](https://cloudinit.readthedocs.io) voor het configureren van een virtuele machine (VM) of schaal sets voor virtuele machines bij het inrichten van de tijd in Azure. Deze Cloud-init-configuraties worden uitgevoerd bij de eerste keer opstarten zodra de resources zijn ingericht door Azure.  

Inrichting van de VM is het proces waarbij Azure de parameter waarden voor de VM doorloopt, zoals hostnaam, gebruikers naam, wacht woord, enzovoort, en deze beschikbaar maken voor de virtuele machine wanneer deze wordt opgestart. Een ' inrichtings agent ' gebruikt deze waarden, configureert de virtuele machine en meldt zich terug wanneer deze is voltooid. 

Azure ondersteunt twee inrichtings agenten [Cloud-init](https://cloudinit.readthedocs.io)en de [Azure Linux-agent (Wala)](../extensions/agent-linux.md).

## <a name="cloud-init-overview"></a>overzicht van Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) is een veelgebruikte benadering voor het aanpassen van een virtuele Linux-machine wanneer deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Omdat Cloud-init wordt aangeroepen tijdens het eerste opstart proces, zijn er geen extra stappen of vereiste agents om uw configuratie toe te passen.  `#cloud-config`Zie de [Cloud-init-documentatie site](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)voor meer informatie over het correct format teren van uw bestanden of andere invoer.  `#cloud-config` bestanden zijn tekst bestanden die worden gecodeerd in base64.

Cloud-init werkt ook over distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het systeem eigen pakket beheer programma voor de distributie die u selecteert.

We werken samen met onze goedgekeurde Linux distributie-partners om ervoor te zorgen dat installatie kopieën met Cloud-init beschikbaar zijn op de Azure Marketplace. Met deze installatiekopieën kunnen uw cloud-init-implementaties en -configuraties naadloos werken met VM's en virtuele-machineschaalsets. In eerste instantie werken we samen met de getekende Linux distributie-partners en upstream om Cloud-init-functies te garanderen met het besturings systeem op Azure, waarna de pakketten worden bijgewerkt en openbaar beschikbaar worden gesteld in de distributie-pakket opslagplaatsen. 

Er zijn twee fasen om Cloud-init beschikbaar te maken voor het geviseerde besturings systeem van Linux distributie op Azure, pakket ondersteuning en vervolgens de ondersteuning van afbeeldingen:
* ' Cloud-init-pakket ondersteuning op Azure ' documenteert welke Cloud-init-pakketten worden ondersteund of in de preview-versie, zodat u deze pakketten kunt gebruiken met het besturings systeem in een aangepaste installatie kopie.
* ' afbeelding Cloud-initiated ' documenten als de installatie kopie al is geconfigureerd voor het gebruik van Cloud-init.


### <a name="canonical"></a>Canonical
| Uitgever/versie| Aanbieding | SKU | Versie | afbeelding Cloud-init gereed | Cloud-init-pakket ondersteuning op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonieke 20,04 |UbuntuServer |18.04-LTS |meest recente |ja | ja |
|Canonieke 18,04 |UbuntuServer |18.04-LTS |meest recente |ja | ja |
|Canonieke 16,04|UbuntuServer |16.04-LTS |meest recente |ja | ja |
|Canonieke 14,04|UbuntuServer |14.04.5-LTS |meest recente |ja | ja |

### <a name="rhel"></a>RHEL
| Uitgever/versie | Aanbieding | SKU | Versie | afbeelding Cloud-init gereed | Cloud-init-pakket ondersteuning op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |ja | Ja, ondersteuning van pakket versie: *18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | Ja (Opmerking: dit is een voorbeeld afbeelding en **mag** niet meer worden gebruikt. dit wordt 1e september 2020) | N.v.t. |
|RedHat 7,7 (gen1)|RHEL |7.7 | 7.7.2020051912 | ja | Ja, ondersteuning van pakket versie: *18.5 -6. EL7*|
|RedHat 7,7 (Gen2)|RHEL | 77-Gen2 | 7.7.2020051913 | ja | Ja, ondersteuning van pakket versie: *18.5 -6. EL7*|
|RedHat 7,7 (gen1)|RHEL |7-LVM | 7.7.2020051921 | ja | Ja, ondersteuning van pakket versie: *18.5 -6. EL7*|
|RedHat 7,7 (Gen2)|RHEL | 7lvm-Gen2 | 7.7.2020051922  | ja | Ja, ondersteuning van pakket versie: *18.5 -6. EL7*|
|RedHat 7,7 (gen1) |RHEL-BYOS | RHEL-lvm77 | 7.7.20200416 | ja  | Ja, ondersteuning van pakket versie: *18.5 -6. EL7*|
|RedHat 8,1 (gen1) |RHEL |8,1-CI |8.1.2020042511 | Ja (Opmerking: dit is een voorbeeld afbeelding en zodra alle RHEL 8,1-installatie kopieën Cloud-init ondersteunen, wordt dit 1e augustus 2020 verwijderd) | Nee, ETA voor volledige ondersteuning van juni 2020|
|RedHat 8,1 (Gen2) |RHEL |81-CI-Gen2 |8.1.2020042524 | Ja (Opmerking: dit is een voorbeeld afbeelding en zodra alle RHEL 8,1-installatie kopieën Cloud-init ondersteunen, wordt dit 1e augustus 2020 verwijderd) | Nee, ETA voor volledige ondersteuning van juni 2020 |

* Alle RedHat: RHEL 7,8-en 8,2-installatie kopieën (gen1 en Gen2) worden ingericht met behulp van Cloud-init.

### <a name="centos"></a>CentOS

| Uitgever/versie | Aanbieding | SKU | Versie | afbeelding Cloud-init gereed | Cloud-init-pakket ondersteuning op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Open Logic 7,7 |CentOS |7-CI |7.7.20190920 |Ja (Opmerking: dit is een voorbeeld afbeelding en **mag** niet meer worden gebruikt. dit wordt 1e september 2020) | N.v.t. |
|Open Logic 7,7 |CentOS | 7.7 |7.7.2020062400 |ja | Ja, ondersteuning van pakket versie: `18.5-6.el7.centos.5`|
|Open Logic 7,7 (Gen2) |CentOS | 7_7-Gen2 |7.7.2020062401 |ja | Ja, ondersteuning van pakket versie: `18.5-6.el7.centos.5`|
|Open Logic 7,7 |CentOS-HPC | 7.7 |7.6.2020062600 |ja | Ja, ondersteuning van pakket versie: `18.5-6.el7.centos.5`|
|Open Logic 7,7 (Gen2) |CentOS-HPC | 7_7-Gen2 |7.6.2020062601 |ja | Ja, ondersteuning van pakket versie: `18.5-6.el7.centos.5`|
|Open Logic 8,1 |CentOS | 8_1 |8.1.2020062400 |ja | Ja, ondersteuning van pakket versie: `18.5-7.el8_1.1`|
|Open Logic 8,1 (Gen2) |CentOS | 8_1-Gen2 |8.1.2020062401 |ja | Ja, ondersteuning van pakket versie: `18.5-7.el8_1.1`|
|Open Logic 8,1 |CentOS-HPC | 8_1 |8.1.2020062400 |ja | Ja, ondersteuning van pakket versie: `18.5-7.el8_1.1`|
|Open Logic 8,1 (Gen2) |CentOS-HPC: 8_1-Gen2 | 8_1-Gen2 |8.1.2020062401 |ja | Ja, ondersteuning van pakket versie: `18.5-7.el8_1.1`|

* Alle open Logic: CentOS 7,8-en 8,2-installatie kopieën (gen1 en Gen2) worden ingericht met behulp van Cloud-init.

### <a name="oracle"></a>Oracle

| Uitgever/versie | Aanbieding | SKU | Versie | afbeelding Cloud-init gereed | Cloud-init-pakket ondersteuning op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77-CI |7.7.01| voor beeld van een afbeelding (Opmerking: dit is een voorbeeld afbeelding, en zodra alle Oracle 7,7-installatie kopieën Cloud-init ondersteunen, wordt dit mid 2020 verwijderd, wordt de melding weer gegeven.) | Nee, in de preview-versie is *het pakket: 18.5-3.0.1. EL7*

### <a name="suse-sles"></a>SUSE SLES
Deze SLES-installatie kopieën zijn bijgewerkt met het inrichten van Cloud-init, de Gen2-installatie kopie varianten zijn ook bijgewerkt.
* SuSE: SLES-15-SP1-{Basic/BYOS/HPC/HPC-BYOS/Chost-BYOS}: gen1:2020.06.10
* SuSE: SLES-SAP-15-SP1: gen1:2020.06.10
* SuSE: SLES-SAP-15-SP1-BYOS: gen1:2020.06.10
* SuSE: Manager-proxy-4-BYOS: gen1:2020.06.10
* SuSE: Manager-server-4-BYOS: gen1:2020.06.10
* SuSE: SLES-{BYOS/SAP/SAP-BYOS}: 15:2020.06.10
* SuSE: SLES-12-SP5: gen1:2020.06.10
* SuSE: SLES-12-SP5 {-BYOS/Basic/HPC-BYOS/HPC}: gen1:2020.06.10
* SuSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP4:2020.06.10
* SuSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP3:2020.06.10
* SuSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP2:2020.06.10


### <a name="debian"></a>Debian
| Uitgever/versie | Aanbieding | SKU | Versie | afbeelding Cloud-init gereed | Cloud-init-pakket ondersteuning op Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| Debian (gen1) |Debian-10 | 10-cloudinit |Cloud-init-preview| Ja (Opmerking: dit is een voorbeeld afbeelding die niet meer **mag** worden gebruikt. dit wordt 1e januari 2021 verwijderd) | Nee, in de preview-versie. |
| Debian (Gen2) |Debian-10 | 10-cloudinit-Gen2 |Cloud-init-preview| Ja (Opmerking: dit is een voorbeeld afbeelding die niet meer **mag** worden gebruikt. dit wordt 1e januari 2021 verwijderd) | Nee, in de preview-versie. |
| Debian (gen1) |Debian-10 | 10-cloudinit |10:0.20201013.422| ja | Ja, ondersteuning van pakket versie: `20.2-2~deb10u1` |
| Debian (Gen2) |Debian-10 | 10-cloudinit-Gen2 |0.20201013.422| ja | Ja, ondersteuning van pakket versie: `20.2-2~deb10u1` |


Momenteel biedt Azure Stack ondersteuning voor het inrichten van installatie kopieën die zijn ingeschakeld voor Cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Wat is het verschil tussen Cloud-init en de Linux-agent (WALA)?
WALA is een Azure-platformspecifieke agent die wordt gebruikt voor het inrichten en configureren van Vm's en het afhandelen van [Azure-extensies](../extensions/features-linux.md). 

We verbeteren de taak van het configureren van Vm's voor het gebruik van Cloud-init in plaats van de Linux-agent om te zorgen dat bestaande Cloud-init-klanten hun huidige Cloud-init-scripts kunnen gebruiken, of nieuwe klanten om te profiteren van de geavanceerde configuratie functionaliteit voor Cloud-init. Als u beschikt over bestaande investeringen in Cloud-init-scripts voor het configureren van Linux-systemen, zijn er **geen aanvullende instellingen vereist** om Cloud-init in te scha kelen. 

Cloud-init kan geen Azure-extensies verwerken, dus WALA is nog steeds vereist in de installatie kopie voor het verwerken van extensies, maar de inrichtings code moet zijn uitgeschakeld voor goedgekeurde Linux distributies-installatie kopieën die worden geconverteerd naar inrichten door Cloud-init, ze moeten WALA geïnstalleerd en correct worden ingesteld.

Bij het maken van een virtuele machine, als u de Azure CLI-switch niet opneemt `--custom-data` tijdens het inrichten, worden de minimale VM-inrichtings parameters vereist om de virtuele machine in te richten en de implementatie te volt ooien met de standaard waarden.  Als u verwijst naar de Cloud-init-configuratie met de `--custom-data` Switch, is de waarde die is opgenomen in uw aangepaste gegevens, beschikbaar voor Cloud-init wanneer de virtuele machine wordt opgestart.

Cloud-init-configuraties die zijn toegepast op Vm's, hebben geen tijds beperkingen en veroorzaken geen implementaties vanwege een time-out. Dit geldt niet voor WALA als u de standaard instellingen van WALA wijzigt om aangepaste gegevens te verwerken, kan dit niet groter zijn dan de totale time-outwaarde voor de VM-inrichting van 40mins, als dit het geval is, mislukt de virtuele machine maken.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Een virtuele machine die is ingeschakeld voor de Cloud-init implementeren
Het implementeren van een virtuele machine die is ingeschakeld voor de Cloud, is net zo eenvoudig als verwijzingen naar een distributie met Cloud-init tijdens de implementatie.  De Linux-distributies kunnen ervoor kiezen om Cloud-init in te scha kelen en te integreren in de basis installatie kopieën van Azure. Wanneer u de installatie kopie die u wilt implementeren hebt bevestigd, is Cloud-init ingeschakeld. u kunt de Azure CLI gebruiken om de installatie kopie te implementeren. 

De eerste stap bij het implementeren van deze installatie kopie is het maken van een resource groep met de opdracht [AZ Group Create](/cli/azure/group) . Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

De volgende stap is het maken van een bestand in uw huidige shell, met de naam *cloud-init.txt* en het plakken van de volgende configuratie. In dit voor beeld maakt u het bestand in het Cloud Shell niet op uw lokale computer. U kunt elke editor die u wilt gebruiken. Voer `sensible-editor cloud-init.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Kies #1 om de **nano** -editor te gebruiken. Controleer of het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
> [!NOTE]
> Cloud-init heeft meerdere [invoer typen](https://cloudinit.readthedocs.io/en/latest/topics/format.html), Cloud-init maakt gebruik van de eerste regel van de CustomData/User Data om aan te geven hoe de invoer moet worden verwerkt, bijvoorbeeld `#cloud-config` geeft aan dat de inhoud moet worden verwerkt als een Cloud-init-configuratie.


Druk op `ctrl-X` om het bestand te sluiten, typ `y` om het bestand op te slaan en druk op `enter` om de bestands naam te bevestigen bij afsluiten.

De laatste stap bestaat uit het maken van een virtuele machine met de opdracht [AZ VM Create](/cli/azure/vm) . 

In het volgende voor beeld wordt een VM gemaakt met de naam *centos74* en worden er SSH-sleutels gemaakt, als deze nog niet bestaan op een standaard sleutel locatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  Gebruik de `--custom-data`-parameter om door te geven in uw cloud-init-configuratiebestand. Geef het volledige pad naar *cloud-init.txt* op als u het bestand buiten uw huidige werkmap hebt opgeslagen. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Wanneer de virtuele machine is gemaakt, toont de Azure CLI informatie die specifiek is voor uw implementatie. Noteer het `publicIpAddress`. Dit adres wordt gebruikt voor toegang tot de virtuele machine.  Het duurt enige tijd voordat de virtuele machine is gemaakt, de pakketten die moeten worden geïnstalleerd en de app worden gestart. Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. U kunt een SSH-verbinding met de virtuele machine uitvoeren en de stappen volgen die worden beschreven in de sectie probleem oplossing om de Cloud-init-logboeken te bekijken. 

U kunt ook een virtuele machine die is ingeschakeld voor Cloud initialisatie implementeren door de [para meters in arm-sjabloon](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters)door te geven.

## <a name="troubleshooting-cloud-init"></a>Problemen met Cloud-init oplossen
Zodra de VM is ingericht, wordt de Cloud-init uitgevoerd via alle modules en het script dat is gedefinieerd in om `--custom-data` de virtuele machine te configureren.  Als u fouten of omissies van de configuratie moet oplossen, moet u zoeken naar de module naam ( `disk_setup` of `runcmd` bijvoorbeeld) in het Cloud-init-logboek dat zich bevindt in **/var/log/Cloud-init.log**.

> [!NOTE]
> Niet elke module fout leidt tot een onherstelbare algemene configuratie fout van de Cloud-init. Als bijvoorbeeld de module wordt gebruikt `runcmd` en het script mislukt, wordt de inrichting van de Cloud nog steeds gerapporteerd omdat de runcmd-module is uitgevoerd.

Raadpleeg de [Cloud-init-documentatie](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) voor meer informatie over de Cloud-init-logboek registratie 

## <a name="next-steps"></a>Volgende stappen

[Problemen met Cloud-init oplossen](cloud-init-troubleshooting.md).


Raadpleeg de volgende documenten voor voor beelden van configuratie wijzigingen in de Cloud-init:
 
- [Een extra Linux-gebruiker toevoegen aan een VM](cloudinit-add-user.md)
- [Een pakket beheer programma uitvoeren om bestaande pakketten bij de eerste keer opstarten bij te werken](cloudinit-update-vm.md)
- [Lokale hostnaam van VM wijzigen](cloudinit-update-vm-hostname.md) 
- [Een toepassings pakket installeren, configuratie bestanden bijwerken en sleutels invoeren](tutorial-automate-vm-deployment.md)
