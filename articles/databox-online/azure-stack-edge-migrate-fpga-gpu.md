---
title: Migratie handleiding voor het fysieke apparaat Azure Stack Edge Pro FPGA naar GPU
description: Deze hand leiding bevat instructies voor het migreren van werk belastingen van een Azure Stack Edge Pro FPGA-apparaat naar een Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 24d6528a105d593d1cb4c9c66d981c8787f85633
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573270"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Werk belastingen migreren van een Azure Stack Edge Pro-FPGA naar een Azure Stack Edge Pro GPU

In dit artikel wordt beschreven hoe u werk belastingen en gegevens migreert van een Azure Stack Edge Pro FPGA-apparaat naar een Azure Stack Edge Pro GPU-apparaat. Het migratie proces begint met een vergelijking van de twee apparaten, een migratie plan en een beoordeling van de overwegingen voor de migratie. De migratie procedure geeft gedetailleerde stappen die eindigen op verificatie en het opschonen van apparaten.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>Info over migratie

Migratie is het proces van het verplaatsen van werk belastingen en toepassings gegevens van de ene opslag locatie naar de andere. Dit houdt in dat u een exacte kopie van de huidige gegevens van een organisatie van het ene opslag apparaat naar een ander opslag apparaat kunt maken, bij voor keur zonder de actieve toepassingen te onderbreken of uit te scha kelen en vervolgens alle I/O-activiteit (invoer/uitvoer) naar het nieuwe apparaat om te leiden. 

In deze migratie handleiding vindt u stapsgewijze instructies voor de stappen die nodig zijn om gegevens te migreren van een Azure Stack Edge Pro FPGA-apparaat naar een Azure Stack Edge Pro GPU-apparaat. Dit document is bedoeld voor IT-professionals en kennis werkers die verantwoordelijk zijn voor het besturings systeem, de implementatie en het beheer van Azure Stack edge-apparaten in het Data Center.

In dit artikel wordt het apparaat van de Azure Stack Edge Pro FPGA aangeduid als het *bron* apparaat en is het apparaat van de Azure stack Edge Pro GPU het *doel* apparaat. 

## <a name="comparison-summary"></a>Vergelijkings overzicht

In deze sectie vindt u een vergelijkend overzicht van de mogelijkheden van de Azure Stack Edge Pro GPU versus de Azure Stack Edge Pro FPGA-apparaten. De hardware in zowel de bron-als het doel apparaat is grotendeels identiek. alleen de hardware-versnellings kaart en de opslag capaciteit kunnen verschillen.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    Mogelijkheid  | Azure Stack Edge Pro GPU (doel apparaat)  | Azure Stack Edge Pro FPGA (bron apparaat)|
|----------------|-----------------------|------------------------|
| Hardware       | Hardwareversnelling: 1 of 2 NVIDIA T4-Gpu's <br> De specificaties compute, geheugen, netwerk interface, voeding en stroom kabel zijn identiek aan het apparaat met FPGA.  | Hardwareversnelling: Intel Arria 10 FPGA <br> De specificaties compute, geheugen, netwerk interface, voeding en stroom kabel zijn identiek aan het apparaat met GPU.          |
| Bruikbare opslag | 4,19 TB <br> Na het reserveren van ruimte voor pariteits tolerantie en intern gebruik | 12,5 TB <br> Na het reserveren van ruimte voor intern gebruik |
| Beveiliging       | Certificaten |                                                     |
| Workloads      | IoT Edge werk belastingen <br> VM-workloads <br> Kubernetes-werkbelastingen| IoT Edge werk belastingen |
| Prijzen        | [Prijzen](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Prijzen](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Migratie plan

Als u uw migratie plan wilt maken, moet u rekening houden met de volgende informatie:

- Een planning voor de migratie ontwikkelen. 
- Wanneer u gegevens migreert, kan er een downtime optreden. U wordt aangeraden de migratie te plannen tijdens een onderhouds periode voor uitval wanneer het proces wordt verstoord. In deze downtime kunt u configuraties instellen en herstellen, zoals verderop in dit document wordt beschreven.
- Inzicht in de totale duur van downtime en communiceer deze aan alle betrokkenen.
- Identificeer de lokale gegevens die moeten worden gemigreerd van het bron apparaat. Zorg er als voorzorgsmaatregel voor dat alle gegevens in de bestaande opslag een recente back-up hebben. 


## <a name="migration-considerations"></a>Overwegingen bij migratie 

Voordat u verdergaat met de migratie, moet u rekening houden met de volgende informatie: 

- Een Azure Stack Edge Pro GPU-apparaat kan niet worden geactiveerd op basis van een Azure Stack Edge Pro FPGA-resource. U moet een nieuwe resource maken voor het Azure Stack Edge Pro GPU-apparaat, zoals wordt beschreven in [een Azure stack Edge Pro GPU-order maken](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- De Machine Learning modellen die zijn geïmplementeerd op het bron apparaat dat gebruik maakt van de FPGA, moeten worden gewijzigd voor het doel apparaat met GPU. Als u hulp nodig hebt bij de modellen, kunt u contact opnemen met Microsoft Ondersteuning. De aangepaste modellen die zijn geïmplementeerd op het bron apparaat dat geen gebruik maakt van de FPGA (alleen gebruikte CPU), moeten op het doel apparaat werken (met behulp van CPU).
- De IoT Edge-modules die op het bron apparaat zijn geïmplementeerd, kunnen wijzigingen vereisen voordat de modules op het doel apparaat kunnen worden geïmplementeerd. 
- Het bron apparaat ondersteunt NFS 3,0-en 4,1-protocollen. Het doel apparaat ondersteunt alleen NFS 3,0-protocol.
- Het bron apparaat ondersteunt SMB-en NFS-protocollen. Het doel apparaat ondersteunt opslag via het REST-protocol met opslag accounts naast de SMB-en NFS-protocollen voor shares.
- De share toegang op het bron apparaat is via het IP-adres, terwijl de toegang tot de share op het doel apparaat via de apparaatnaam is.

## <a name="migration-steps-at-a-glance"></a>Migratie stappen in een oogopslag

Deze tabel geeft een overzicht van de algemene stroom voor migratie, met een beschrijving van de stappen die nodig zijn voor migratie en de locatie waar u deze stappen moet uitvoeren.

| In deze fase | Deze stap uitvoeren| Op dit apparaat |
|---------------|-------------|----------------|
| Bron apparaat voorbereiden *       | 1. record configuratie gegevens <br>2. back-up maken van gegevens delen <br>3. IoT Edge workloads voorbereiden| Bron apparaat  |
| Doel apparaat voorbereiden *       |1. een nieuwe order maken <br>2. configureren en activeren| Doel apparaat  |
| Gegevens migreren       | 1. gegevens migreren van shares <br>2. Implementeer IoT Edge workloads opnieuw| Doel apparaat  |
| Gegevens controleren            |Gemigreerde gegevens verifiëren |Doel apparaat  |
| Opschonen, retour neren              |Gegevens wissen en retour neren| Bron apparaat  |

**De bron-en doel apparaten kunnen parallel worden voor bereid.*

## <a name="prepare-source-device"></a>Bron apparaat voorbereiden

De voor bereiding omvat het identificeren van de Edge-Cloud shares, de rand van lokale shares en de IoT Edge-modules die op het apparaat zijn geïmplementeerd. 

### <a name="1-record-configuration-data"></a>1. record configuratie gegevens

Voer de volgende stappen uit op het bron apparaat via de lokale gebruikers interface.

Registreer de configuratie gegevens op het *bron* apparaat. Gebruik de [controle lijst voor implementatie](azure-stack-edge-gpu-deploy-checklist.md) om u te helpen bij het vastleggen van de apparaatconfiguratie. Tijdens de migratie gebruikt u deze configuratie-informatie voor het configureren van het nieuwe doel apparaat. 

### <a name="2-back-up-share-data"></a>2. back-up maken van gegevens delen

De gegevens van het apparaat kunnen uit een van de volgende typen bestaan:

- Gegevens in Edge-Cloud shares
- Gegevens in lokale shares

#### <a name="data-in-edge-cloud-shares"></a>Gegevens in Edge-Cloud shares

Edge-Cloud deelt laag gegevens van uw apparaat naar Azure. Voer de volgende stappen uit op het *bron* apparaat via de Azure Portal. 

- Maak een lijst van alle Edge-Cloud shares en gebruikers die u hebt op het bron apparaat.
- Maak een lijst van alle bandbreedte schema's die u hebt. U maakt deze bandbreedte schema's opnieuw op uw doel apparaat.
- Afhankelijk van de beschik bare netwerk bandbreedte, configureert u bandbreedte schema's op uw apparaat om de gegevenslaagte gegevens in de cloud te maximaliseren. Hiermee worden de lokale gegevens op het apparaat geminimaliseerd.
- Zorg ervoor dat de shares volledig in de Cloud zijn gelaagd. De lagen kunnen worden bevestigd door de status van de share te controleren in de Azure Portal.  

#### <a name="data-in-edge-local-shares"></a>Gegevens in Edge-lokale shares

Gegevens in Edge-rand blijven lokale shares op het apparaat. Voer de volgende stappen uit op het *bron* apparaat via de Azure Portal. 

- Maak een lijst van de Edge-lokale shares op het apparaat.
- Omdat u een eenmalige migratie van de gegevens uitvoert, maakt u een kopie van de lokale Edge-share gegevens naar een andere on-premises server. U kunt Kopieer hulpprogramma's zoals `robocopy` (SMB) of `rsync` (NFS) gebruiken om de gegevens te kopiëren. U kunt eventueel al een oplossing voor gegevens beveiliging van derden hebben geïmplementeerd om een back-up te maken van de gegevens in uw lokale shares. De volgende oplossingen van derden worden ondersteund voor gebruik met Azure Stack Edge Pro FPGA-apparaten:

    | Software van derden           | Verwijzing naar de oplossing                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Neem contact op met Cohesity voor meer informatie.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Neem contact op met CommVault voor meer informatie.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Neem contact op met Veritas voor meer informatie.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Neem contact op met Veeam voor meer informatie. |


### <a name="3-prepare-iot-edge-workloads"></a>3. IoT Edge workloads voorbereiden

- Als u IoT Edge modules hebt geïmplementeerd en FPGA-versnelling gebruikt, moet u mogelijk de modules wijzigen voordat deze op het GPU-apparaat worden uitgevoerd. Volg de instructies in [IOT Edge-modules wijzigen](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Doel apparaat voorbereiden

### <a name="1-create-new-order"></a>1. nieuwe order maken

U moet een nieuwe volg orde (en een nieuwe resource) maken voor het *doel* apparaat. Het doel apparaat moet worden geactiveerd op basis van de GPU-resource en niet voor de FPGA-resource.

Als u een order wilt plaatsen, [maakt u een nieuwe Azure stack Edge-resource](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) in de Azure Portal.


### <a name="2-set-up-activate"></a>2. instellen, activeren

U moet het *doel* apparaat instellen en activeren op basis van de nieuwe resource die u eerder hebt gemaakt. 

Volg deze stappen om het *doel* apparaat te configureren via de Azure portal:

1. Verzamel de vereiste informatie in de [implementatie controlelijst](azure-stack-edge-gpu-deploy-checklist.md). U kunt de informatie gebruiken die u hebt opgeslagen in de configuratie van het bron apparaat. 
1. [Uw apparaat](azure-stack-edge-gpu-deploy-install.md#cable-the-device) [uitpakken](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), op een [rek monteren](azure-stack-edge-gpu-deploy-install.md#rack-the-device) en aansluiten. 
1. [Verbinding maken met de lokale gebruikers interface van het apparaat](azure-stack-edge-gpu-deploy-connect.md).
1. Configureer het netwerk met behulp van een andere set IP-adressen (als u statische Ip's gebruikt) dan die u hebt gebruikt voor uw oude apparaat. Zie [netwerk instellingen configureren](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Wijs dezelfde apparaatnaam toe als het oude apparaat en geef een DNS-domein op. Zie [Apparaatinstellingen configureren](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)voor meer informatie.
1. Certificaten op het nieuwe apparaat configureren. Zie [certificaten configureren](azure-stack-edge-gpu-deploy-configure-certificates.md)voor meer informatie.
1. Haal de activerings sleutel op uit het Azure Portal en activeer het nieuwe apparaat. Zie hoe u [het apparaat activeert](azure-stack-edge-gpu-deploy-activate.md).

U bent nu klaar om de share gegevens te herstellen en de werk belastingen te implementeren die u op het oude apparaat hebt uitgevoerd.

## <a name="migrate-data"></a>Gegevens migreren

U gaat nu gegevens van het bron apparaat kopiëren naar de Edge-Cloud shares en Edge-lokale shares op uw *doel* apparaat.

### <a name="1-from-edge-cloud-shares"></a>1. van Edge-Cloud shares

Volg deze stappen om de gegevens op de Edge-Cloud shares op uw doel apparaat te synchroniseren:

1. [Voeg shares toe](azure-stack-edge-j-series-manage-shares.md#add-a-share) die overeenkomen met de share namen die op het bron apparaat zijn gemaakt. Wanneer u de shares maakt, moet u ervoor zorgen dat **BLOB-container selecteren** is ingesteld op het **gebruik van bestaande** en selecteert u vervolgens de container die is gebruikt met het vorige apparaat.
1. [Gebruikers toevoegen](azure-stack-edge-j-series-manage-users.md#add-a-user) die toegang tot het vorige apparaat hadden.
1. [De share](azure-stack-edge-j-series-manage-shares.md#refresh-shares) gegevens van Azure vernieuwen. Als de share wordt vernieuwd, worden alle Cloud gegevens van de bestaande container naar de shares opgehaald.
1. Maak de bandbreedte schema's opnieuw die moeten worden gekoppeld aan uw shares. Zie [een bandbreedte schema toevoegen](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) voor gedetailleerde stappen.


### <a name="2-from-edge-local-shares"></a>2. lokale shares van Edge

U hebt mogelijk een back-upoplossing van derden geïmplementeerd voor het beveiligen van de lokale share gegevens voor uw IoT-workloads. U moet deze gegevens nu herstellen.

Nadat het vervangende apparaat volledig is geconfigureerd, schakelt u het apparaat in voor lokale opslag. 

Voer de volgende stappen uit om de gegevens te herstellen vanaf lokale shares:

1. [Configureer Compute op het apparaat](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Voeg alle lokale shares op het doel apparaat toe. Zie de gedetailleerde stappen in [een lokale share toevoegen](azure-stack-edge-gpu-manage-shares.md#add-a-local-share).
1. Als u de SMB-shares op het bron apparaat opent, worden de IP-adressen gebruikt die op het doel apparaat worden gebruikt. Zie [verbinding maken met een SMB-share op Azure stack Edge Pro GPU](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-smb-share). Als u verbinding wilt maken met NFS-shares op het doel apparaat, moet u de nieuwe IP-adressen gebruiken die zijn gekoppeld aan het apparaat. Zie [verbinding maken met een NFS-share op Azure stack Edge Pro GPU](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-nfs-share). 

    Als u uw share gegevens hebt gekopieerd naar een tussenliggende server via SMB of NFS, kunt u de gegevens van de tussenliggende server kopiëren naar shares op het doel apparaat. Als zowel de bron als het doel apparaat *online* zijn, kunt u de gegevens ook rechtstreeks vanaf het bron apparaat kopiëren.

    Als u software van derden hebt gebruikt voor het maken van een back-up van de gegevens in de lokale shares, moet u de herstel procedure uitvoeren die wordt verstrekt door de keuze van de gegevens bescherming. Zie de verwijzingen in de volgende tabel.

    | Software van derden           | Verwijzing naar de oplossing                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Neem contact op met Cohesity voor meer informatie.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Neem contact op met CommVault voor meer informatie. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Neem contact op met Veritas voor meer informatie.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Neem contact op met Veeam voor meer informatie. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. Implementeer IoT Edge workloads opnieuw

Zodra de IoT Edge modules zijn voor bereid, moet u IoT Edge workloads op uw doel apparaat implementeren. Als u fouten in de implementatie van IoT Edge-modules ondervindt, raadpleegt u:

- [Veelvoorkomende problemen en oplossingen voor Azure IOT Edge](../iot-edge/troubleshoot-common-errors.md). 
- [IOT Edge runtime-fouten](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>Gegevens controleren

Controleer na de migratie of alle gegevens zijn gemigreerd en of de werk belastingen op het doel apparaat zijn geïmplementeerd.

## <a name="erase-data-return"></a>Gegevens wissen, retour neren

Nadat de gegevens migratie is voltooid, wist u de lokale gegevens en retourneert u het bron apparaat. Volg de stappen in [uw Azure stack Edge Pro-apparaat retour neren](azure-stack-edge-return-device.md).


## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het implementeren van IoT Edge workloads op Azure Stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-deploy-compute-module-simple.md)
