---
title: Azure Stack Edge Pro-apparaatfout voorbereiden
description: Hierin wordt beschreven hoe u een vervanging ophaalt voor een Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 986a3c56a1e0dcc79ab472a7e18d7eeb7e2fddb5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448348"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Voorbereiden op een Azure Stack Edge Pro GPU-apparaatfout

In dit artikel wordt beschreven hoe u een apparaatfout kunt voorbereiden door te beschrijven hoe u de apparaatconfiguratie en gegevens op uw Azure Stack Edge Pro GPU-apparaat kunt opslaan en er een back-up van wilt maken. 

Het artikel bevat geen stappen voor het maken van back-ups van Kubernetes-en IoT-containers die zijn geïmplementeerd op uw Azure Stack Edge Pro GPU-apparaat. 

## <a name="understand-device-failures"></a>Problemen met apparaten begrijpen

Uw Azure Stack Edge Pro GPU-apparaat kan twee soorten hardwarestoringen ondervinden.

- Toelaat bare fouten waarvoor u een hardware-onderdeel moet vervangen. Met deze fouten kunt u het apparaat op een gedegradeerde status uitvoeren. Voor beelden van deze fouten zijn één mislukte voeding (PSU) of één defecte schijf op het apparaat. In elk van deze gevallen kan het apparaat blijven werken. U wordt aangeraden om zo snel mogelijk contact op te nemen met Microsoft Ondersteuning om de mislukte onderdelen te vervangen.

- Niet-toegestane fouten waarvoor u het hele apparaat moet vervangen. Een voor beeld van deze fout is dat er twee schijven op uw apparaat zijn mislukt. In deze gevallen neemt u contact op met Microsoft Ondersteuning en nadat u hebt vastgesteld dat een apparaat moet worden vervangen, kunt u hiermee de vervanging van uw Azure Stack edge-apparaat vergemakkelijken.

Voor de voor bereiding op niet-toegestane storingen moet u een back-up maken van het volgende op uw apparaat:

- Informatie over de apparaatconfiguratie.
- Gegevens die zich bevinden in de rand van lokale shares en Edge-Cloud shares.
- Bestanden en mappen die zijn gekoppeld aan de virtuele machines die op het apparaat worden uitgevoerd.


## <a name="back-up-device-configuration"></a>Back-up van apparaatconfiguratie

Tijdens de eerste configuratie van het apparaat is het belang rijk dat u een kopie van de configuratie-informatie voor het apparaat bewaart, zoals beschreven in de [controle lijst](azure-stack-edge-gpu-deploy-checklist.md)voor de implementatie. Tijdens het herstel wordt deze configuratie-informatie gebruikt om toe te passen op het nieuwe vervangende apparaat. 

## <a name="protect-device-data"></a>Apparaatgegevens beschermen

De gegevens van het apparaat kunnen uit een van de volgende typen bestaan:

- Gegevens in Edge-Cloud shares
- Gegevens in lokale shares
- Bestanden en mappen op Vm's

In de volgende secties worden de stappen en aanbevelingen beschreven voor het beveiligen van elk type gegevens op uw apparaat.

## <a name="protect-data-in-edge-cloud-shares"></a>Gegevens in Edge-Cloud shares beveiligen

U kunt Edge-Cloud shares maken waarmee gegevens van uw apparaat worden gelaagd naar Azure. Afhankelijk van de beschik bare netwerk bandbreedte, configureert u bandbreedte sjablonen op het apparaat om gegevens verlies in het geval van een niet-toegestane fout te minimaliseren.

> [!IMPORTANT] 
> Als het apparaat een niet-toegestane fout heeft, kunnen lokale gegevens die niet zijn gelaagd van het apparaat naar Azure, verloren gaan. 

## <a name="protect-data-in-edge-local-shares"></a>Gegevens in Edge-lokale shares beveiligen

Als u Kubernetes of IoT Edge implementeert, configureert u zo dat de toepassings gegevens lokaal op het apparaat worden opgeslagen en niet worden gesynchroniseerd met Azure Storage. De gegevens worden opgeslagen op een share op het apparaat. Het kan belang rijk zijn om een back-up te maken van de gegevens in deze shares.

De volgende oplossingen voor gegevens beveiliging van derden kunnen een back-upoplossing bieden voor de gegevens in de lokale SMB-of NFS-shares. 

| Software van derden           | Verwijzing naar de oplossing                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | https://www.cohesity.com/solution/cloud/azure/ <br> Neem contact op met Cohesity voor meer informatie.          |
| CommVault                      | https://www.commvault.com/azure <br> Neem contact op met CommVault voor meer informatie.          |
| Veritas                        | http://veritas.com/azure <br> Neem contact op met Veritas voor meer informatie.   |


## <a name="protect-files-and-folders-on-vms"></a>Bestanden en mappen op Vm's beveiligen

Azure Stack Edge werkt met Azure Backup en andere oplossingen voor gegevens beveiliging van derden om een back-upoplossing te bieden voor het beveiligen van gegevens die zijn opgenomen in de Vm's die op het apparaat zijn geïmplementeerd. De volgende tabel bevat verwijzingen naar beschik bare oplossingen waaruit u kunt kiezen.


| Back-upoplossingen        | Ondersteund besturings systeem   | Naslaginformatie                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Microsoft Azure Recovery Services-agent (MARS) voor Azure Backup | Windows        | [Informatie over de MARS-agent](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Korte Microsoft Azure-integratie, oplossing voor back-up en herstel](https://www.cohesity.com/solution/cloud/azure) <br>Neem contact op met Cohesity voor meer informatie.                          |
| CommVault               | Windows, Linux | https://www.commvault.com/azure <br>Neem contact op met CommVault voor meer informatie.                          |
| Veritas                 | Windows, Linux | http://veritas.com/azure <br> Neem contact op met Veritas voor meer informatie.                    |



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [herstellen van een mislukt Azure stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-recover-device-failure.md).