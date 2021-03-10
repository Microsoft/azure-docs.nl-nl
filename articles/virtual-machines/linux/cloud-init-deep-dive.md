---
title: Wat is Cloud-init?
description: Meer informatie over het inrichten van een Azure-VM met behulp van Cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 94dd57310375febb4bc9a55efa704a5fbf4e80e8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559440"
---
# <a name="diving-deeper-into-cloud-init"></a>Meer diep in de Cloud-init
Voor meer informatie over [Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) of het oplossen van problemen met een lager niveau, moet u weten hoe het werkt. Dit document markeert de belang rijke onderdelen en legt de details van Azure uit.

Wanneer Cloud-init is opgenomen in een gegeneraliseerde installatie kopie en er een virtuele machine wordt gemaakt op basis van die installatie kopie, worden configuraties verwerkt en worden er vijf fasen uitgevoerd tijdens de eerste keer opstarten. In deze fasen, zoals u kunt zien, kunt u in het punt Cloud-init configuraties Toep assen. 


## <a name="understand-cloud-init-configuration"></a>Cloud-Init configuratie begrijpen
Een virtuele machine configureren om te worden uitgevoerd op een platform, betekent dat Cloud-init meerdere configuraties moet Toep assen als een installatie kopie gebruiker, de belangrijkste configuraties waarmee u werkt `User data` (customData), die meerdere indelingen ondersteunt. deze worden [hier](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats)beschreven. U hebt ook de mogelijkheid om scripts (/var/lib/Cloud/scripts) toe te voegen en uit te voeren voor aanvullende configuratie. Hieronder vindt u meer informatie over dit onderwerp.

Sommige configuraties zijn al geïntegreerde in azure Marketplace-installatie kopieën die worden geleverd met Cloud-init, zoals:

1. **Cloud-gegevens bron** -Cloud-init bevat code die kan communiceren met Cloud platforms. deze worden data sources genoemd. Wanneer een virtuele machine wordt gemaakt op basis van een Cloud-init-installatie kopie in [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure), laadt Cloud-init de Azure-gegevens bron, die communiceert met de Azure-eind punten van de virtuele machine om de specifieke VM-configuratie op te halen.
2. **Runtime configuratie** (/run/Cloud-init)
3. **Image config** (/etc/Cloud), zoals `/etc/cloud/cloud.cfg` , `/etc/cloud/cloud.cfg.d/*.cfg` . Een voor beeld van waar dit wordt gebruikt in azure, is het gebruikelijk voor de installatie kopieën van Linux-besturings systemen met Cloud-init om een Azure data source-instructie te hebben die Cloud-init vertelt welke data source-service moet worden gebruikt. Hiermee wordt de tijd voor de Cloud-init opgeslagen:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Opstart fasen in de Cloud-init (verwerkings configuratie)

Bij het inrichten met Cloud-init zijn er vijf fasen van opstarten, die de configuratie van het proces en de weer gegeven in de logboeken.

1. [Generator fase](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): de in de Cloud init geplaatste generator wordt gestart en er wordt vastgesteld dat de Cloud-init moet worden opgenomen in de opstart doelen. als dit het geval is, wordt Cloud-init ingeschakeld. 

2. [Cloud-init Local fase](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): hier ziet u de lokale Azure-gegevens bron, waarmee Cloud-init kan worden ingeschakeld voor de interface met Azure en een netwerk configuratie, inclusief terugval, kan worden toegepast.

3. [Cloud-init init-fase (netwerk)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): het netwerk moet online zijn en de gegevens van de NIC en route tabel moeten worden gegenereerd. In deze fase worden de modules die in/etc/Cloud/Cloud.cfg worden vermeld, `cloud_init_modules` uitgevoerd. De virtuele machine in azure wordt gekoppeld, de tijdelijke schijf wordt geformatteerd, de hostnaam is ingesteld, samen met andere taken.

   Dit zijn enkele van de `cloud_init_modules` volgende:
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Na deze fase wordt door Cloud-init gesignaleerd naar het Azure-platform waarop de virtuele machine is ingericht. Sommige modules zijn mogelijk mislukt. niet alle module fouten zullen leiden tot een inrichtings fout.

4. [Cloud-init-configuratie fase](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): in deze fase worden de modules in `cloud_config_modules` gedefinieerd en weer gegeven in/etc/Cloud/Cloud.cfg uitgevoerd.


5. [Cloud-init laatste fase](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): in deze laatste fase worden de modules in `cloud_final_modules` weer gegeven in/etc/Cloud/Cloud.cfg. Hier worden de modules die te laat worden uitgevoerd in het opstart proces, zoals pakket installaties, en het uitvoeren van scripts, enzovoort. 

   -   Tijdens deze fase kunt u scripts uitvoeren door ze in de mappen onder te plaatsen `/var/lib/cloud/scripts` :
   - `per-boot` -scripts in deze map worden uitgevoerd op elke keer dat de computer opnieuw wordt opgestart
   - `per-instance` -scripts in deze map worden uitgevoerd wanneer een nieuw exemplaar voor het eerst wordt opgestart
   - `per-once` -scripts in deze map worden slechts één keer uitgevoerd

## <a name="next-steps"></a>Volgende stappen

[Problemen met Cloud-init oplossen](cloud-init-troubleshooting.md).
