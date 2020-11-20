---
title: Uw StorSimple 8000 Series-apparaat implementeren in Azure Portal
description: Hier vindt u de stappen en aanbevolen procedures voor de implementatie van het StorSimple 8000-apparaat met Update 3 en later, en de StorSimple-apparaatbeheerfunctie.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: alkohli
ms.openlocfilehash: 2c9feb1131f6d2d0eb75ac71e27dc46c226c52c1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961053"
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>Een on-premises StorSimple-apparaat implementeren (Update 3 en hoger)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Welkom bij de implementatie van Microsoft Azure StorSimple-apparaten. Deze zelfstudies voor implementatie zijn van toepassing op StorSimple 8000 met Update 3 en hoger. Deze reeks zelfstudies bevat een configuratiecontrolelijst, configuratievereisten en gedetailleerde configuratiestappen voor uw StorSimple-apparaat.

Bij de informatie in deze zelfstudies wordt ervan uitgegaan dat u de voorzorgsmaatregelen hebt gelezen en uw StorSimple-apparaat hebt uitgepakt, geplaatst en alle kabels hebt aangesloten. Als u dit nog moet doen, lees dan eerst de [voorzorgsmaatregelen](storsimple-8000-safety.md). Volg de specifieke instructies van het apparaat om het apparaat uit te pakken, in het rek te plaatsen en alle kabels aan te sluiten.

* [De 8100 uitpakken, op het rek monteren en bekabelen](storsimple-8100-hardware-installation.md)
* [De 8600 uitpakken, op het rek monteren en bekabelen](storsimple-8600-hardware-installation.md)

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. U wordt geadviseerd om de configuratiecontrolelijst te raadplegen voordat u begint. Het implementatie- en configuratieproces kan enige tijd duren.

> [!NOTE]
> De StorSimple-implementatiegegevens die zijn gepubliceerd op de website van Microsoft Azure zijn alleen van toepassing op apparaten uit de StorSimple 8000-serie. Ga voor volledige informatie over de 7000-serie apparaten naar: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) . Raadpleeg de [Introductiehandleiding van het StorSimple-systeem](http://onlinehelp.storsimple.com/111_Appliance/) voor informatie over de implementatie van de 7000-serie. 


## <a name="deployment-steps"></a>Implementatiestappen
Voer de volgende verplichte stappen uit om uw StorSimple-apparaat te configureren en te verbinden met uw StorSimple-apparaatbeheerfunctie. Naast de verplichte stappen zijn er nog optionele stappen en procedures die u tijdens de implementatie misschien nodig hebt. In de stapsgewijze implementatie-instructies wordt aangeven wanneer u deze optionele stappen moet uitvoeren.

| Stap | Beschrijving |
| --- | --- |
| **VEREISTEN** |Deze moeten zijn worden voltooid ter voorbereiding van de implementatie. |
| [Configuratiecontrolelijst voor implementatie](#deployment-configuration-checklist) |Gebruik deze controlelijst om informatie te verzamelen en te registreren voorafgaand aan en tijdens de implementatie. |
| [Vereisten voor implementatie](#deployment-prerequisites) |Hiermee wordt gecontroleerd of de omgeving gereed is voor implementatie. |
|  | |
| **STAPSGEWIJZE IMPLEMENTATIE** |Deze stappen zijn vereist om uw StorSimple-apparaat in productie te nemen. |
| [Stap 1: een nieuwe service maken](#step-1-create-a-new-service) |Stel cloudbeheer en -opslag voor uw StorSimple-apparaat in. *Sla deze stap over als u een bestaande service voor andere StorSimple-apparaten hebt*. |
| [Stap 2: de serviceregistratiesleutel ophalen](#step-2-get-the-service-registration-key) |Gebruik deze sleutel om uw StorSimple-apparaat te registreren en te verbinden met de managementservice. |
| [Stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Verbind het apparaat met uw netwerk en registreer het bij Azure om de installatie met behulp van de beheerservice te voltooien. |
| [Stap 4: minimale apparaatconfiguratie voltooien](#step-4-complete-minimum-device-setup)</br>[Best practice: het StorSimple-apparaat bijwerken](#scan-for-and-apply-updates) |Gebruik de managementservice om de installatie van het apparaat te voltooien en het in te schakelen voor opslag. |
| [Stap 5: een volume container maken](#step-5-create-a-volume-container) |Maak een container voor het inrichten van volumes. Een volumecontainer heeft opslagaccount, bandbreedte en versleutelingsinstellingen voor alle volumes in de container. |
| [Stap 6: een volume maken](#step-6-create-a-volume) |Richt opslagvolumes op het StorSimple-apparaat in voor uw servers. |
| [Stap 7: een volume koppelen, initialiseren en formatteren](#step-7-mount-initialize-and-format-a-volume)</br>[Optioneel: MPIO configureren](storsimple-8000-configure-mpio-windows-server.md) |Verbind uw servers met de iSCSI-opslag die is geleverd door het apparaat. U kunt MPIO desgewenst configureren om ervoor te zorgen dat uw servers koppelings-, netwerk- en interfacefouten kunnen tolereren. |
| [Stap 8: een back-up maken](#step-8-take-a-backup) |Stel een back-upbeleid in om uw gegevens te beschermen. |
|  | |
| **OVERIGE PROCEDURES** |Mogelijk moet u deze procedures raadplegen tijdens de implementatie van uw oplossing. |
| [Een nieuw opslagaccount voor de service configureren](#configure-a-new-storage-account-for-the-service) | |
| [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) | |
| [De IQN van een Windows Server-host ophalen](#get-the-iqn-of-a-windows-server-host) | |
| [Een hand matige back-up maken](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Configuratiecontrolelijst voor implementatie
Voordat u uw apparaat implementeert, moet u informatie verzamelen om de software op uw StorSimple-apparaat te configureren. De implementatie van het StorSimple-apparaat in uw omgeving verloopt gestroomlijnder wanneer u deze gegevens zoveel mogelijk op voorhand voorbereidt. Download en gebruik deze controlelijst om de configuratiegegevens te noteren terwijl u het apparaat implementeert.

* [Configuratiecontrolelijst voor implementatie van StorSimple downloaden](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Vereisten voor implementatie
In de volgende secties worden de configuratievereisten voor de StorSimple-apparaatbeheerfunctie en het StorSimple-apparaat toegelicht.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie
Zorg voordat u begint voor het volgende:

* U hebt een Microsoft-account met toegangsreferenties.
* U hebt een Microsoft Azure Storage-account met toegangsreferenties.
* Uw Microsoft Azure-abonnement is ingeschakeld voor de StorSimple-apparaatbeheerfunctie. Uw abonnement moet zijn aangeschaft via de [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).
* U hebt toegang tot terminalemulatiesoftware, zoals PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Voor het apparaat in het datacenter
Zorg voordat u het apparaat configureert, dat uw apparaat volledig is uitgepakt, is gemonteerd op het rek en volledig is bekabeld voor voeding, netwerk- en seriële toegang zoals is beschreven in:

* [Een 8100-apparaat uitpakken, op het rek monteren en bekabelen](storsimple-8100-hardware-installation.md)
* [Een 8600-apparaat uitpakken, op het rek monteren en bekabelen](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Voor het netwerk in het datacenter
Zorg voordat u begint voor het volgende:

* De poorten in de firewall van het netwerkcentrum zijn geopend om iSCSI- en cloud-verkeer toe te staan, zoals is beschreven in [Netwerkvereisten voor uw StorSimple-apparaat](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>Stapsgewijze implementatie
Gebruik de volgende stapsgewijze instructies om uw StorSimple-apparaat te implementeren in het datacenter.

## <a name="step-1-create-a-new-service"></a>Stap 1: een nieuwe service maken
Met een StorSimple-apparaatbeheerfunctie kunt u meerdere StorSimple-apparaten beheren. Voer de volgende stappen uit om een exemplaar van de StorSimple-apparaatbeheerfunctie uit te voeren.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> Als u de service niet hebt ingeschakeld om automatisch een opslagaccount te maken, moet u minimaal één opslagaccount maken nadat u een service hebt gemaakt. Dit opslagaccount wordt gebruikt bij het maken van een volumecontainer.
>
> * Als u niet automatisch een opslagaccount hebt gemaakt, gaat u naar [Een nieuw opslagaccount voor de service configureren](#configure-a-new-storage-account-for-the-service) voor gedetailleerde instructies.
> * Als u het automatisch maken van een opslagaccount hebt ingeschakeld, gaat u naar [Stap 2: de serviceregistratiesleutel ophalen](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Stap 2: de serviceregistratiesleutel ophalen
Wanneer de StorSimple-apparaatbeheerfunctie bedrijfsklaar is, moet u de serviceregistratiesleutel ophalen. Deze sleutel wordt gebruikt om het StorSimple-apparaat te registreren en te verbinden met de service.

Voer de volgende stappen uit in Azure Portal.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Stap 3: het apparaat configureren en registreren via Windows PowerShell voor StorSimple
Gebruik Windows PowerShell voor StorSimple om de eerste installatie van uw StorSimple-apparaat uit te voeren zoals wordt uitgelegd in de volgende procedure. U moet terminalemulatiesoftware gebruiken om deze stap uit te voeren. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) voor meer informatie.

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Stap 4: minimale apparaatconfiguratie voltooien
Voor de minimale apparaatconfiguratie van uw StorSimple-apparaat moet u het volgende doen: 

* Geef een beschrijvende naam voor het apparaat op.
* Stel de tijdzone van het apparaat in.
* Vaste IP-adressen toewijzen aan beide domeincontrollers

Voer de volgende stappen in Azure Portal uit om de minimale configuratie van het apparaat uit te voeren.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

Nadat u de minimale apparaatconfiguratie hebt voltooid, is het raadzaam om te [zoeken naar de nieuwste updates en deze toe te passen](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>Stap 5: een volumecontainer maken
Een volumecontainer heeft opslagaccount, bandbreedte en versleutelingsinstellingen voor alle volumes in de container. U moet een volumecontainer maken voordat u de volumes op uw StorSimple-apparaat kunt gaan inrichten.

Voer de volgende stappen uit in Azure Portal om een volumecontainer te maken.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Stap 6: een volume maken
Wanneer u een volumecontainer hebt gemaakt, kunt u een opslagvolume inrichten op het StorSimple-apparaat voor uw servers. Voer de volgende stappen uit in Azure Portal om een volume te maken.

> [!IMPORTANT]
> Met de StorSimple-apparaatbeheerfunctie kunt u zowel thin als volledig ingerichte volumes maken. U kunt echter geen gedeeltelijk ingerichte volumes maken.


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Stap 7: een volume koppelen, initialiseren en formatteren
De volgende stappen worden uitgevoerd op uw Windows Server-host.

> [!IMPORTANT]
> * Voor hoge beschikbaarheid van uw StorSimple-oplossing wordt u aangeraden om de MPIO op de hostservers te configureren (optioneel) voordat u iSCSI configureert. MPIO-configuratie op hostservers zorgt ervoor dat de servers een koppelings-, netwerk- of interfacefout kunnen tolereren.
> * Voor MPIO- en iSCSI-installatie- en configuratie-instructies op een Windows Server-host gaat u naar [MPIO configureren voor uw StorSimple-apparaat](storsimple-8000-configure-mpio-windows-server.md). Deze omvatten ook de stappen voor het koppelen, initialiseren en formatteren van StorSimple-volumes.
> * Voor MPIO- en iSCSI-installatie- en configuratie-instructies op een Linux-host gaat u naar [MPIO configureren voor uw StorSimple Linux-host](storsimple-configure-mpio-on-linux.md).

Als u besluit geen MPIO te configureren, voer dan de volgende stappen uit om uw StorSimple-volumes te koppelen, te initialiseren en te formatteren op een Windows Server-host.

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Stap 8: een back-up maken
Back-ups bieden tijdgebonden bescherming van volumes en verbeteren de herstelmogelijkheden met minimale hersteltijden. U kunt twee soorten back-ups uitvoeren op een StorSimple-apparaat: lokale momentopnamen en cloudmomentopnamen. Beide back-uptypen kunnen **gepland** of **handmatig** zijn.

Voer de volgende stappen uit in Azure Portal om een geplande back-up te maken.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

U kunt op elk moment een handmatige back-up maken. Voor procedures gaat u naar [Een handmatige back-up maken](#create-a-manual-backup).

U hebt de apparaatconfiguratie voltooid.

## <a name="configure-a-new-storage-account-for-the-service"></a>Een nieuw opslagaccount voor de service configureren
Dit is een optionele stap die u alleen hoeft uit te voeren als u het automatisch maken van een opslagaccount met uw service niet hebt ingeschakeld. U hebt een Microsoft Azure Storage-account nodig om een StorSimple-volumecontainer te maken.

Als u een Azure Storage-account in een andere regio wilt maken, raadpleeg dan [Over Azure Storage-accounts](../storage/common/storage-account-create.md) voor stapsgewijze instructies.

Voer de volgende stappen uit op de pagina **StorSimple-apparaatbeheerfunctie** van Azure Portal.

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat
U moet terminalemulatiesoftware, zoals PuTTY, gebruiken om verbinding te maken met Windows PowerShell voor StorSimple. U kunt PuTTY gebruiken wanneer u het apparaat rechtstreeks benadert via de seriële console of door een Telnet-sessie te openen vanaf een externe computer.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Updates zoeken en toepassen
Het bijwerken van een apparaat kan enkele uren duren. Ga voor gedetailleerde stappen voor het installeren van de meest recente update naar [Update 5 installeren](storsimple-8000-install-update-5.md).


## <a name="get-the-iqn-of-a-windows-server-host"></a>Het IQN ophalen van een Windows Server-host
Voer de volgende stappen uit om de IQN (iSCSI Qualified Name) van een Windows-host te verkrijgen waarop Windows Server® 2012 wordt uitgevoerd.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Een handmatige back-up maken
Voer de volgende stappen uit in Azure Portal als u voor één volume op het StorSimple-apparaat een handmatige back-up op aanvraag wilt maken.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="view-the-pinout-diagram-for-serial-cable-for-storsimple"></a>Het pin-outdiagram weergeven voor de seriële kabel voor StorSimple
Het volgende pin-outdiagram kan worden gebruikt voor de seriële consolekabel van StorSimple.

Hier is de DB9 vrouwelijke connector P1 is en is de connector van 3,5 mm P2.

![Pin-out diagram 1 voor seriële consolekabel van StorSimple](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram1.png)

De punt van de stereoaansluiting wordt beschouwd als PIN 3 RX, de middelste is PIN 2 TX is en de basis is PIN 1 GND, zoals wordt weergegeven in het volgende diagram.

![Pin-out diagram 2 voor seriële consolekabel van StorSimple](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram2.png)



## <a name="next-steps"></a>Volgende stappen
* [Een StorSimple-cloudapparaat configureren](storsimple-8000-cloud-appliance-u2.md).
* [Gebruik de StorSimple-apparaatbeheerfunctie om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).