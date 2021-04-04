---
title: Portal prep voor StorSimple Virtual array
description: De eerste zelf studie voor het implementeren van StorSimple Virtual array omvat het voorbereiden van de Azure Portal
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1a3d0b05f04ad8c182d428843479160666b96ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87070316"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>StorSimple Virtual array implementeren: de Azure Portal voorbereiden

![Diagram met de stappen die nodig zijn voor het implementeren van een virtuele matrix. De eerste stap is het label aan de slag en is gemarkeerd.](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Dit is het eerste artikel in de reeks implementatie zelfstudies die vereist zijn om uw virtuele array volledig te implementeren als een bestands server of een iSCSI-server met behulp van het Resource Manager-model. In dit artikel wordt de voor bereiding beschreven voor het maken en configureren van uw StorSimple Apparaatbeheer-service vóór het inrichten van een virtuele matrix. In dit artikel vindt u ook koppelingen naar een configuratie controlelijst en configuratie vereisten.

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. U wordt aangeraden de controle lijst voor implementatie configuratie te bekijken voordat u begint. Het voorbereiden van de portal duurt minder dan 10 minuten.

De informatie die in dit artikel wordt gepubliceerd, is van toepassing op de implementatie van StorSimple virtuele arrays in de Azure Portal en Microsoft Azure Government Cloud.

### <a name="get-started"></a>Aan de slag
De implementatie werk stroom bestaat uit het voorbereiden van de portal, het inrichten van een virtuele matrix in uw gevirtualiseerde omgeving en het volt ooien van de installatie. Om aan de slag te gaan met de implementatie van de StorSimple-virtuele matrix als een bestands server of een iSCSI-server, moet u de volgende tabellen in tabel vorm raadplegen.

#### <a name="deployment-articles"></a>Implementatie artikelen

Raadpleeg de volgende artikelen in de voorgeschreven volg orde om uw virtuele StorSimple-matrix te implementeren.

| **#** | **In deze stap** | **U doet dit...** | **En deze documenten gebruiken.** |
| --- | --- | --- | --- |
| 1. |**De Azure Portal instellen** |Maak en configureer uw StorSimple Apparaatbeheer-service voordat u een virtuele StorSimple-matrix inricht. |[De portal voorbereiden](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**De virtuele matrix inrichten** |Voor Hyper-V, inrichten en verbinding maken met een virtuele StorSimple-matrix op een hostsysteem waarop Hyper-V wordt uitgevoerd op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2. <br></br> <br></br> Voor VMware kunt u een virtuele StorSimple-matrix inrichten en er verbinding mee maken op een hostsysteem met VMware ESXi 5,0, 5,5, 6,0 of 6,5.<br></br> |[Een virtuele matrix inrichten in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Een virtuele matrix inrichten in VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**De virtuele matrix instellen** |Voor uw bestands server voert u de eerste installatie uit, registreert u de StorSimple-Bestands server en voltooit u de installatie van het apparaat. Vervolgens kunt u SMB-shares inrichten. <br></br> <br></br> Voor uw iSCSI-server voert u de eerste installatie uit, registreert u uw StorSimple iSCSI-server en voltooit u de installatie van het apparaat. U kunt vervolgens iSCSI-volumes inrichten. |[Virtuele array instellen als bestands server](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Virtuele array instellen als iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md) |

U kunt nu Azure Portal gaan instellen.

## <a name="configuration-checklist"></a>Configuratie Controlelijst

In de controle lijst voor configuratie wordt beschreven welke gegevens u moet verzamelen voordat u de software op uw virtuele StorSimple-matrix configureert. Als u deze informatie van tevoren voorbereidt, kunt u het implementatie proces van het StorSimple-apparaat in uw omgeving stroom lijnen. Afhankelijk van of de virtuele StorSimple-matrix is geïmplementeerd als een bestands server of een iSCSI-server, hebt u een van de volgende controle lijsten nodig.

* Down load de StorSimple-controle lijst voor de configuratie van de [Bestands server van de virtuele matrix](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Down load de [controle lijst voor iSCSI-server configuratie van StorSimple Virtual array](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Vereisten

Hier vindt u de configuratie vereisten voor uw StorSimple Apparaatbeheer-service, uw virtuele StorSimple-matrix en het datacenter netwerk.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie

Zorg voordat u begint voor het volgende:

* U hebt een Microsoft-account met toegangsreferenties.
* U hebt een Microsoft Azure Storage-account met toegangsreferenties.
* Uw Microsoft Azure-abonnement moet zijn ingeschakeld voor de StorSimple Apparaatbeheer-service.

### <a name="for-the-storsimple-virtual-array"></a>Voor de virtuele StorSimple-matrix

Voordat u een virtuele matrix implementeert, moet u het volgende doen:

* U hebt toegang tot een hostsysteem waarop Hyper-V wordt uitgevoerd op Windows Server 2008 R2 of hoger of VMware (ESXi 5,0, 5,5, 6,0 of 6,5) dat kan worden gebruikt om een apparaat in te richten.
* Het hostsysteem kan de volgende resources voor het inrichten van uw virtuele matrix toewijzen:
  
  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele matrix als bestands server te configureren, ondersteunt 8 GB 2.000.000-bestanden. U hebt 16 GB RAM nodig om 2-4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface.
  * Een virtuele schijf van 500 GB voor systeem gegevens.

### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

* Het netwerk in uw Data Center wordt geconfigureerd volgens de netwerk vereisten voor uw StorSimple-apparaat. Zie de systeem vereisten voor de [virtuele matrix van StorSimple](storsimple-ova-system-requirements.md)voor meer informatie.
* Uw virtuele StorSimple-matrix heeft altijd een specifieke 5 Mbps internet bandbreedte (of meer) beschikbaar. Deze bandbreedte mag niet worden gedeeld met andere toepassingen.

## <a name="step-by-step-preparation"></a>Stapsgewijze voor bereiding

Gebruik de volgende stapsgewijze instructies om uw portal voor te bereiden op de StorSimple-Apparaatbeheer service.

## <a name="step-1-create-a-new-service"></a>Stap 1: een nieuwe service maken

Eén exemplaar van de StorSimple-Apparaatbeheer service kan meerdere virtuele StorSimple-matrices beheren. Voer de volgende stappen uit om een exemplaar van de StorSimple-apparaatbeheerfunctie uit te voeren. Als u een bestaande StorSimple-Apparaatbeheer service hebt voor het beheren van uw virtuele matrices, slaat u deze stap over en gaat u naar [stap 2: de service registratie sleutel ophalen](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Als u de service niet hebt ingeschakeld om automatisch een opslagaccount te maken, moet u minimaal één opslagaccount maken nadat u een service hebt gemaakt.
> 
> * Als u niet automatisch een opslagaccount hebt gemaakt, gaat u naar [Een nieuw opslagaccount voor de service configureren](#optional-step-configure-a-new-storage-account-for-the-service) voor gedetailleerde instructies.
> * Als u het automatisch maken van een opslagaccount hebt ingeschakeld, gaat u naar [Stap 2: de serviceregistratiesleutel ophalen](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Stap 2: de serviceregistratiesleutel ophalen

Wanneer de StorSimple-apparaatbeheerfunctie bedrijfsklaar is, moet u de serviceregistratiesleutel ophalen. Deze sleutel wordt gebruikt om het StorSimple-apparaat te registreren en te verbinden met de service.

Voer de volgende stappen uit in [Azure Portal](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> De service registratie sleutel wordt gebruikt voor het registreren van alle StorSimple-apparaten Apparaatbeheer die moeten worden geregistreerd bij uw StorSimple Apparaatbeheer-service.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Stap 3: de installatie kopie van de virtuele matrix downloaden

Nadat u de service registratie sleutel hebt, moet u de juiste installatie kopie van de virtuele matrix downloaden om een virtuele matrix op uw hostsysteem in te richten. De installatie kopieën van de virtuele array zijn specifiek voor het besturings systeem en kunnen worden gedownload van de Snel starten pagina in de Azure Portal.

> [!IMPORTANT]
> De software die wordt uitgevoerd op de virtuele StorSimple-matrix mag alleen worden gebruikt met de StorSimple-Apparaatbeheer service.
> 
> 

Voer de volgende stappen uit in [Azure Portal](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>De installatie kopie van de virtuele matrix ophalen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/). 
2. Klik in het Azure Portal op **bladeren > StorSimple Device Managers**.
3. Selecteer een bestaande StorSimple-Apparaatbeheer service. Klik in de Blade **StorSimple Apparaatbeheer** op **snel starten**. 
4. Klik op de koppeling die overeenkomt met de afbeelding die u wilt downloaden van het micro soft Download centrum. De installatiekopieën zijn ongeveer 4,8 GB.
   
   * VHDX voor Hyper-V op Windows Server 2012 en hoger
   * VHD voor Hyper-V op Windows Server 2008 R2 en hoger
   * VMDK voor VMWare ESXi 5,0, 5,5, 6,0 of 6,5
5. Download het bestand en pak het uit op een lokale schijf, en onthoud waar het zipbestand is uitgepakt.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Optionele stap: een nieuw opslag account voor de service configureren

Deze stap is optioneel en moet alleen worden uitgevoerd als u het automatisch maken van een opslag account bij uw service niet hebt ingeschakeld.

Als u een Azure-opslag account in een andere regio wilt maken, raadpleegt u [een opslag account maken](../storage/common/storage-account-create.md) voor stapsgewijze instructies.

Voer de volgende stappen uit in de [Azure Portal](https://ms.portal.azure.com/) op de pagina StorSimple Apparaatbeheer-service om een bestaand Microsoft Azure Storage-account toe te voegen.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Een opslag account referentie toevoegen die hetzelfde Azure-abonnement als de Apparaatbeheer-service heeft

1. Navigeer naar uw Apparaatbeheer-service, selecteer en dubbel klik erop. Hiermee opent u de Blade **overzicht** .
2. Selecteer **referenties voor het opslag account** in de **configuratie** sectie.
3. Klik op **Add**.
4. Ga als volgt te werk op de Blade **een opslag account toevoegen** :
   
   1. Selecteer bij **abonnement** de optie **actueel**.
   
   2. Geef de naam van uw Azure Storage-account op.
   
   3. Selecteer **inschakelen** om een beveiligd kanaal te maken voor de netwerk communicatie tussen uw StorSimple-apparaat en de Cloud. Selecteer alleen **uitschakelen** als u binnen een privécloud werkt.
   
   4. Klik op **Add**. U wordt gewaarschuwd nadat het opslag account is gemaakt.<br></br>
   
      ![Een bestaande opslag account referentie toevoegen](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Volgende stap

De volgende stap is het inrichten van een virtuele machine voor uw virtuele StorSimple-matrix. Raadpleeg hier de specifieke instructies voor het besturingssysteem van uw host:

* [Een virtuele StorSimple-matrix inrichten in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Een virtuele StorSimple-matrix inrichten in VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

