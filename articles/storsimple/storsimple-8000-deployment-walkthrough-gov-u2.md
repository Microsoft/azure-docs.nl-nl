---
title: StorSimple 8000 Series-apparaat implementeren in overheids Portal | Microsoft Docs
description: Hierin worden de stappen en aanbevolen procedures beschreven voor het implementeren van het StorSimple 8000 Series-apparaat met update 3 en hoger, en de service in de Azure Government Portal.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: alkohli
ms.openlocfilehash: d736c09fc1c9490f79dfc526895970e01b8b45cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94963178"
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Uw on-premises StorSimple-apparaat implementeren in de overheids Portal

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Welkom bij de implementatie van Microsoft Azure StorSimple-apparaten. Deze zelf studies voor implementatie zijn van toepassing op de StorSimple 8000-serie met update 3-software of later in de Azure Government Portal. Deze reeks zelf studies bevat een configuratie controlelijst, een lijst met configuratie vereisten en gedetailleerde configuratie stappen voor uw StorSimple-apparaat.

Bij de informatie in deze zelfstudies wordt ervan uitgegaan dat u de voorzorgsmaatregelen hebt gelezen en uw StorSimple-apparaat hebt uitgepakt, geplaatst en alle kabels hebt aangesloten. Als u dit nog moet doen, lees dan eerst de [voorzorgsmaatregelen](storsimple-8000-safety.md). Volg de specifieke instructies van het apparaat om het apparaat uit te pakken, in het rek te plaatsen en alle kabels aan te sluiten.

* [De 8100 uitpakken, op het rek monteren en bekabelen](storsimple-8100-hardware-installation.md)
* [De 8600 uitpakken, op het rek monteren en bekabelen](storsimple-8600-hardware-installation.md)

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. U wordt geadviseerd om de configuratiecontrolelijst te raadplegen voordat u begint. Het implementatie- en configuratieproces kan enige tijd duren.

> [!NOTE]
> De StorSimple-implementatiegegevens die zijn gepubliceerd op de website van Microsoft Azure zijn alleen van toepassing op apparaten uit de StorSimple 8000-serie. Ga voor volledige informatie over de 7000-serie apparaten naar: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) . Raadpleeg de [Introductiehandleiding van het StorSimple-systeem](http://onlinehelp.storsimple.com/111_Appliance/) voor informatie over de implementatie van de 7000-serie.


## <a name="deployment-steps"></a>Implementatiestappen
Voer de volgende verplichte stappen uit om uw StorSimple-apparaat te configureren en te verbinden met uw StorSimple-apparaatbeheerfunctie. Naast de vereiste stappen zijn er optionele stappen en procedures die u tijdens de implementatie mogelijk moet volt ooien. In de stapsgewijze implementatie-instructies wordt aangeven wanneer u deze optionele stappen moet uitvoeren.

| Stap | Beschrijving |
| --- | --- |
| **VEREISTEN** |Deze moeten zijn worden voltooid ter voorbereiding van de implementatie. |
| [Configuratiecontrolelijst voor implementatie](#deployment-configuration-checklist) |Gebruik deze controlelijst om informatie te verzamelen en te registreren voorafgaand aan en tijdens de implementatie. |
| [Vereisten voor implementatie](#deployment-prerequisites) |Hiermee wordt gevalideerd of de omgeving gereed is voor implementatie. |
|  | |
| **STAPSGEWIJZE IMPLEMENTATIE** |Deze stappen zijn vereist om uw StorSimple-apparaat in productie te nemen. |
| [Stap 1: een nieuwe service maken](#step-1-create-a-new-service) |Stel cloudbeheer en -opslag voor uw StorSimple-apparaat in. *Sla deze stap over als u een bestaande service voor andere StorSimple-apparaten hebt*. |
| [Stap 2: de serviceregistratiesleutel ophalen](#step-2-get-the-service-registration-key) |Gebruik deze sleutel om uw StorSimple-apparaat te registreren en te verbinden met de Management service. |
| [Stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Verbind het apparaat met uw netwerk en registreer het bij Azure om de installatie met behulp van de managementservice te voltooien. |
| [Stap 4: de minimale configuratie van het apparaat volt ooien](#step-4-complete-minimum-device-setup) </br>Optioneel: het StorSimple-apparaat bijwerken. |Gebruik de managementservice om de installatie van het apparaat te voltooien en het in te schakelen voor opslag. |
| [Stap 5: een volume container maken](#step-5-create-a-volume-container) |Maak een container voor het inrichten van volumes. Een volumecontainer heeft opslagaccount, bandbreedte en versleutelingsinstellingen voor alle volumes in de container. |
| [Stap 6: een volume maken](#step-6-create-a-volume) |Richt opslagvolume(s) op het StorSimple-apparaat in voor uw servers. |
| [Stap 7: een volume koppelen, initialiseren en formatteren](#step-7-mount-initialize-and-format-a-volume) </br>Optioneel: MPIO configureren. |Verbind uw servers met de iSCSI-opslag die is geleverd door het apparaat. Configureer eventueel MPIO om ervoor te zorgen dat uw servers koppelings-, netwerk-en interface fouten kunnen verdragen. |
| [Stap 8: een back-up maken](#step-8-take-a-backup) |Stel een back-upbeleid in om uw gegevens te beschermen. |
|  | |
| **OVERIGE PROCEDURES** |Mogelijk moet u deze procedures raadplegen tijdens de implementatie van uw oplossing. |
| [Een nieuw opslagaccount voor de service configureren](#configure-a-new-storage-account-for-the-service) | |
| [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) | |
| [Updates zoeken en toepassen](#scan-for-and-apply-updates) | |
| [De IQN van een Windows Server-host ophalen](#get-the-iqn-of-a-windows-server-host) | |
| [Een hand matige back-up maken](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Configuratiecontrolelijst voor implementatie
Voordat u uw StorSimple-apparaat implementeert, moet u informatie verzamelen om de software op uw apparaat te configureren. De implementatie van het StorSimple-apparaat in uw omgeving verloopt gestroomlijnder wanneer u deze gegevens zoveel mogelijk op voorhand voorbereidt. Down load en gebruik deze controle lijst om de configuratie gegevens te noteren terwijl u uw apparaat implementeert.

[Configuratiecontrolelijst voor implementatie van StorSimple downloaden](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Vereisten voor implementatie
In de volgende secties worden de configuratievereisten voor de StorSimple-apparaatbeheerfunctie en het StorSimple-apparaat toegelicht.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie
Zorg voordat u begint voor het volgende:

* U hebt een Microsoft-account met toegangsreferenties.
* U hebt een Microsoft Azure Storage-account met toegangsreferenties.
* Uw Microsoft Azure-abonnement is ingeschakeld voor de StorSimple-apparaatbeheerfunctie. Uw abonnement moet zijn aangeschaft via de [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).
* U hebt toegang tot terminalemulatiesoftware, zoals PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Voor het apparaat in het datacenter
Zorg voordat u het apparaat configureert voor het volgende:

* Het apparaat is volledig uitgepakt, gemonteerd op het rek en volledig bekabeld voor voeding, netwerk- en seriële toegang zoals is beschreven in:
  
  * [Een 8100-apparaat uitpakken, op het rek monteren en bekabelen](storsimple-8100-hardware-installation.md)
  * [Een 8600-apparaat uitpakken, op het rek monteren en bekabelen](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter
Zorg voordat u begint voor het volgende:

* De poorten in de firewall van het netwerkcentrum zijn geopend om iSCSI- en cloud-verkeer toe te staan, zoals is beschreven in [Netwerkvereisten voor uw StorSimple-apparaat](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>Stapsgewijze implementatie
Gebruik de volgende stapsgewijze instructies om uw StorSimple-apparaat te implementeren in het datacenter.

## <a name="step-1-create-a-new-service"></a>Stap 1: een nieuwe service maken
Met een StorSimple-apparaatbeheerfunctie kunt u meerdere StorSimple-apparaten beheren. Voer de volgende stappen uit om een nieuw exemplaar van de StorSimple-Apparaatbeheer-service te maken.

[!INCLUDE [storsimple-8000-create-new-service-gov](../../includes/storsimple-8000-create-new-service-gov.md)]

> [!IMPORTANT]
> Als u de service niet hebt ingeschakeld om automatisch een opslagaccount te maken, moet u minimaal één opslagaccount maken nadat u een service hebt gemaakt. Dit opslagaccount wordt gebruikt bij het maken van een volumecontainer.
> 
> * Als u niet automatisch een opslagaccount hebt gemaakt, gaat u naar [Een nieuw opslagaccount voor de service configureren](#configure-a-new-storage-account-for-the-service) voor gedetailleerde instructies.
> * Als u het automatisch maken van een opslagaccount hebt ingeschakeld, gaat u naar [Stap 2: de serviceregistratiesleutel ophalen](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Stap 2: de serviceregistratiesleutel ophalen
Wanneer de StorSimple-apparaatbeheerfunctie bedrijfsklaar is, moet u de serviceregistratiesleutel ophalen. Deze sleutel wordt gebruikt om uw StorSimple-apparaat te registreren en te verbinden met de service.

Voer de volgende stappen uit in de Government-Portal.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple
Gebruik Windows PowerShell voor StorSimple om de eerste installatie van uw StorSimple-apparaat uit te voeren zoals wordt uitgelegd in de volgende procedure. U moet terminalemulatiesoftware gebruiken om deze stap uit te voeren. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) voor meer informatie.

[!INCLUDE [storsimple-8000-configure-and-register-device-gov](../../includes/storsimple-8000-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Stap 4: minimale apparaatconfiguratie voltooien
Voor de minimale apparaatconfiguratie van uw StorSimple-apparaat moet u het volgende doen:

* Geef een beschrijvende naam voor het apparaat op.
* Stel de tijdzone van het apparaat in.
* Vaste IP-adressen toewijzen aan beide domeincontrollers

Voer de volgende stappen uit in de Azure Government-Portal om de minimale installatie van het apparaat te volt ooien.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>Stap 5: een volumecontainer maken
Een volumecontainer heeft opslagaccount, bandbreedte en versleutelingsinstellingen voor alle volumes in de container. U moet een volumecontainer maken voordat u de volumes op uw StorSimple-apparaat kunt gaan inrichten.

Voer de volgende stappen uit in de Government-Portal om een volume container te maken.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Stap 6: een volume maken
Wanneer u een volumecontainer hebt gemaakt, kunt u een opslagvolume inrichten op het StorSimple-apparaat voor uw servers. Voer de volgende stappen uit in de Government-Portal om een volume te maken.

> [!IMPORTANT]
> StorSimple Apparaatbeheer kunnen alleen Thin provisioned volumes maken.  U kunt echter geen gedeeltelijk ingerichte volumes maken.

[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Stap 7: een volume koppelen, initialiseren en formatteren
Voer de volgende stappen uit op de Windows Server-host.

> [!IMPORTANT]
> * Voor hoge beschikbaarheid van uw StorSimple-oplossing wordt u aangeraden om de MPIO op de hostservers te configureren (optioneel) voordat u iSCSI configureert. MPIO-configuratie op hostservers zorgt ervoor dat de servers een koppelings-, netwerk- of interfacefout kunnen tolereren.
> * Voor MPIO- en iSCSI-installatie- en configuratie-instructies op een Windows Server-host gaat u naar [MPIO configureren voor uw StorSimple-apparaat](./storsimple-8000-configure-mpio-windows-server.md). Deze omvatten ook de stappen voor het koppelen, initialiseren en formatteren van StorSimple-volumes.
> * Voor MPIO- en iSCSI-installatie- en configuratie-instructies op een Linux-host gaat u naar [MPIO configureren voor uw StorSimple Linux-host](storsimple-configure-mpio-on-linux.md).

Als u besluit geen MPIO te configureren, voer dan de volgende stappen uit om uw StorSimple-volumes te koppelen, te initialiseren en te formatteren op een Windows Server-host.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Stap 8: een back-up maken
Back-ups bieden tijdgebonden bescherming van volumes en verbeteren de herstelmogelijkheden met minimale hersteltijden. U kunt twee soorten back-ups uitvoeren op een StorSimple-apparaat: lokale momentopnamen en cloudmomentopnamen. Beide back-uptypen kunnen **gepland** of **handmatig** zijn.

Voer de volgende stappen uit in de Government-Portal om een geplande back-up te maken.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

U kunt op elk moment een handmatige back-up maken. Voor procedures gaat u naar [Een handmatige back-up maken](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Een nieuw opslagaccount voor de service configureren
Dit is een optionele stap die u alleen hoeft uit te voeren als u het automatisch maken van een opslagaccount met uw service niet hebt ingeschakeld. U hebt een Microsoft Azure Storage-account nodig om een StorSimple-volumecontainer te maken.

Als u een Azure Storage-account in een andere regio wilt maken, raadpleeg dan [Over Azure Storage-accounts](../storage/common/storage-account-create.md) voor stapsgewijze instructies.

Voer de volgende stappen uit in de Government-Portal op de pagina **StorSimple Apparaatbeheer service** .

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat
U moet terminalemulatiesoftware, zoals PuTTY, gebruiken om verbinding te maken met Windows PowerShell voor StorSimple. U kunt PuTTY gebruiken wanneer u het apparaat rechtstreeks benadert via de seriële console of door een Telnet-sessie te openen vanaf een externe computer.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Updates zoeken en toepassen
Het bijwerken van een apparaat kan enkele uren duren. Ga voor gedetailleerde stappen voor het installeren van de meest recente update naar [Update 4 installeren](storsimple-8000-install-update-4.md).

## <a name="get-the-iqn-of-a-windows-server-host"></a>Het IQN ophalen van een Windows Server-host
Voer de volgende stappen uit om de IQN (iSCSI Qualified Name) van een Windows-host te verkrijgen waarop Windows Server® 2012 wordt uitgevoerd.

[!INCLUDE [Get IQN of your Windows Server host](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Een handmatige back-up maken
Voer de volgende stappen uit in de Government-Portal om een hand matige back-up op aanvraag te maken voor één volume op uw StorSimple-apparaat.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Volgende stappen
* Configureer een [virtueel apparaat](storsimple-8000-cloud-appliance-u2.md).
* Gebruik de [StorSimple Apparaatbeheer-service](storsimple-8000-manager-service-administration.md) om uw StorSimple-apparaat te beheren.