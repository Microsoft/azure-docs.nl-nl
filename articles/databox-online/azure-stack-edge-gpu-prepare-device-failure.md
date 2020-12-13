---
title: Azure Stack Edge Pro-apparaatfout voorbereiden
description: Hierin wordt beschreven hoe u een vervanging ophaalt voor een Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: b437ce7b6894ebefe38b32f27d370d9f8c4bfe80
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369018"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Voorbereiden op een Azure Stack Edge Pro GPU-apparaatfout

In dit artikel wordt beschreven hoe u een apparaatfout kunt voorbereiden door te beschrijven hoe u de apparaatconfiguratie en gegevens op uw Azure Stack Edge Pro GPU-apparaat kunt opslaan en er een back-up van wilt maken. 

Het artikel bevat geen stappen voor het maken van back-ups van Kubernetes-en IoT-containers die zijn geïmplementeerd op uw Azure Stack Edge Pro GPU-apparaat. 

## <a name="understand-device-failures"></a>Problemen met apparaten begrijpen

Uw Azure Stack Edge Pro GPU-apparaat kan twee soorten hardwarestoringen ondervinden.

- Toelaat bare fouten waarvoor u een hardware-onderdeel moet vervangen. Met deze fouten kunt u het apparaat op een gedegradeerde status uitvoeren. Voor beelden van deze fouten zijn één mislukte voeding (PSU) of één defecte schijf op het apparaat. In elk van deze gevallen kan het apparaat blijven werken. Neem zo snel mogelijk contact op met Microsoft Ondersteuning om de mislukte onderdelen te vervangen.

- Niet-toegestane storingen waarvoor u het hele apparaat moet vervangen, bijvoorbeeld wanneer er twee schijven op uw apparaat zijn mislukt. In dergelijke gevallen neemt u direct contact op met Microsoft Ondersteuning. Nadat ze hebben vastgesteld dat de vervanging van een apparaat nodig is, helpt ondersteuning bij het vervangen van uw Azure Stack edge-apparaat.

Voor de voor bereiding op niet-toegestane storingen moet u een back-up maken van de volgende dingen op uw apparaat:

- Informatie over de apparaatconfiguratie
- Gegevens in Edge, lokale shares en Edge-Cloud shares
- Bestanden en mappen die zijn gekoppeld aan de virtuele machines die op het apparaat worden uitgevoerd


## <a name="back-up-device-configuration"></a>Back-up van apparaatconfiguratie

Tijdens de eerste configuratie van het apparaat is het belang rijk dat u een kopie van de configuratie-informatie voor het apparaat bewaart, zoals wordt beschreven in de [controle lijst](azure-stack-edge-gpu-deploy-checklist.md)voor de implementatie. Tijdens het herstel wordt deze configuratie-informatie gebruikt om toe te passen op het nieuwe vervangende apparaat. 

## <a name="protect-device-data"></a>Apparaatgegevens beschermen

De gegevens van het apparaat kunnen uit een van de volgende typen bestaan:

- Gegevens in Edge-Cloud shares
- Gegevens in lokale shares
- Bestanden en mappen op Vm's

In de volgende secties worden de stappen en aanbevelingen beschreven voor het beveiligen van elk type gegevens op uw apparaat.

## <a name="protect-data-in-edge-cloud-shares"></a>Gegevens in Edge-Cloud shares beveiligen

U kunt Edge-Cloud shares maken waarmee gegevens van uw apparaat worden gelaagd naar Azure. Afhankelijk van de beschik bare netwerk bandbreedte, configureert u bandbreedte sjablonen op het apparaat om gegevens verlies te minimaliseren als er sprake is van een niet-toegestane fout.

> [!IMPORTANT]
> Als het apparaat een niet-toegestane fout heeft, kunnen lokale gegevens die niet zijn gelaagd van het apparaat naar Azure, verloren gaan. 

## <a name="protect-data-in-edge-local-shares"></a>Gegevens in Edge-lokale shares beveiligen

Als u Kubernetes of IoT Edge implementeert, configureert u zo dat de toepassings gegevens lokaal op het apparaat worden opgeslagen en niet worden gesynchroniseerd met Azure Storage. De gegevens worden opgeslagen op een share op het apparaat. Het kan belang rijk zijn om een back-up te maken van de gegevens in deze shares.

De volgende oplossingen voor gegevens beveiliging van derden kunnen een back-upoplossing bieden voor de gegevens in de lokale SMB-of NFS-shares. 

| Software van derden           | Verwijzing naar de oplossing                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Neem contact op met Cohesity voor meer informatie.          |
| CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Neem contact op met CommVault voor meer informatie.          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Neem contact op met Veritas voor meer informatie.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Neem contact op met Veeam voor meer informatie. |


## <a name="protect-files-and-folders-on-vms"></a>Bestanden en mappen op Vm's beveiligen

Azure Stack Edge werkt met Azure Backup en andere oplossingen voor gegevens beveiliging van derden om een back-upoplossing te bieden voor het beveiligen van gegevens die zijn opgenomen in de virtuele machines die op het apparaat zijn geïmplementeerd. De volgende tabel bevat verwijzingen naar beschik bare oplossingen waaruit u kunt kiezen.


| Back-upoplossingen        | Ondersteund besturings systeem   | Naslaginformatie                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Microsoft Azure Recovery Services-agent (MARS) voor Azure Backup | Windows        | [Informatie over de MARS-agent](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Korte Microsoft Azure-integratie, oplossing voor back-up-& herstel](https://www.cohesity.com/solution/cloud/azure) <br>Neem contact op met Cohesity voor meer informatie.                          |
| CommVault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>Neem contact op met CommVault voor meer informatie.                          |
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> Neem contact op met Veritas voor meer informatie.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Neem contact op met Veeam voor meer informatie. |


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [herstellen van een mislukt Azure stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-recover-device-failure.md).