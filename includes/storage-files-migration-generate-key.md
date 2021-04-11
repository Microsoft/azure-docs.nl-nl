---
title: bestand opnemen
description: bestand opnemen
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075563"
---
De versleutelings sleutels voor service gegevens worden gebruikt voor het versleutelen van vertrouwelijke klant gegevens, zoals referenties voor het opslag account, die worden verzonden vanuit uw StorSimple Manager-service naar het StorSimple-apparaat. U moet deze sleutels regel matig wijzigen als uw IT-organisatie een beleid voor sleutel rotatie heeft op de opslag apparaten. Het sleutel wijzigings proces kan enigszins verschillen, afhankelijk van het feit of er één apparaat of meerdere apparaten worden beheerd door de StorSimple Manager-service. Ga voor meer informatie naar [StorSimple beveiliging en gegevens bescherming](../articles/storsimple/storsimple-8000-security.md).

Het wijzigen van de versleutelings sleutel voor service gegevens is een proces met drie stappen:

1. Een apparaat machtigen om de versleutelings sleutel van de service gegevens te wijzigen met behulp van Windows Power shell-scripts voor Azure Resource Manager.
2. Met Windows PowerShell voor StorSimple initieert u de wijziging van de versleutelings sleutel voor de service gegevens.
3. Als u meer dan één StorSimple-apparaat hebt, werkt u de versleutelings sleutel voor service gegevens op de andere apparaten bij.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Stap 1: Windows Power shell-script gebruiken om een apparaat te autoriseren om de versleutelings sleutel van de service gegevens te wijzigen
Normaal gesp roken vraagt de beheerder aan dat de service beheerder een apparaat moet machtigen om de versleutelings sleutels voor de service gegevens te wijzigen. De service beheerder geeft het apparaat vervolgens toestemming om de sleutel te wijzigen.

Deze stap wordt uitgevoerd met behulp van het script op basis van Azure Resource Manager. De service beheerder kan een apparaat selecteren dat in aanmerking komt voor autorisatie. Het apparaat wordt vervolgens geautoriseerd om het wijzigings proces voor de versleutelings sleutel van de service gegevens te starten. 

Ga voor meer informatie over het gebruik van het script naar [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Welke apparaten kunnen worden geautoriseerd om de versleutelings sleutels voor service gegevens te wijzigen?
Een apparaat moet voldoen aan de volgende criteria voordat het kan worden geautoriseerd om de wijzigingen in de versleutelings sleutel van de service gegevens te initiëren:

* Het apparaat moet online zijn om in aanmerking te komen voor wijzigings autorisatie van de versleutelings sleutel voor service gegevens.
* U kunt hetzelfde apparaat na 30 minuten opnieuw autoriseren als de sleutel wijziging niet is geïnitieerd.
* U kunt een ander apparaat machtigen, mits de sleutel wijziging niet door het eerder geautoriseerde apparaat is geïnitieerd. Nadat het nieuwe apparaat is geautoriseerd, kan het oude apparaat de wijziging niet initiëren.
* U kunt een apparaat niet machtigen terwijl de overschakeling van de versleutelings sleutel voor de service gegevens wordt uitgevoerd.
* U kunt een apparaat machtigen wanneer een aantal van de apparaten die zijn geregistreerd bij de service, zijn doorgevoerd in de versleuteling, terwijl anderen dat niet doen. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Stap 2: gebruik Windows PowerShell voor StorSimple om de wijziging van de versleutelings sleutel voor de service gegevens te initiëren
Deze stap wordt uitgevoerd in de Windows PowerShell voor StorSimple-interface op het geautoriseerde StorSimple-apparaat.

> [!NOTE]
> Er kunnen geen bewerkingen worden uitgevoerd in de Azure Portal van uw StorSimple Manager-service totdat de sleutel rollover is voltooid.


Als u de seriële console van het apparaat gebruikt om verbinding te maken met de Windows Power shell-interface, voert u de volgende stappen uit.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>De wijziging van de versleutelings sleutel van de service gegevens initiëren
1. Selecteer optie 1 om u aan te melden met volledige toegang.
2. Typ in de opdrachtprompt:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Nadat de cmdlet is voltooid, krijgt u een nieuwe versleutelings sleutel voor service gegevens. Kopieer deze sleutel en sla deze op voor gebruik in stap 3 van dit proces. Deze sleutel wordt gebruikt om alle resterende apparaten bij te werken die zijn geregistreerd bij de StorSimple Manager-service.
   
   > [!NOTE]
   > Dit proces moet worden gestart binnen vier uur na het autoriseren van een StorSimple-apparaat.
   > 
   > 
   
   Deze nieuwe sleutel wordt vervolgens naar de service verzonden die moet worden gepusht naar alle apparaten die zijn geregistreerd bij de service. Er wordt dan een waarschuwing weer gegeven in het service dashboard. De service schakelt alle bewerkingen op de geregistreerde apparaten uit en de apparaat-beheerder moet de versleutelings sleutel voor de service gegevens vervolgens op de andere apparaten bijwerken. Het I/O's (hosts die gegevens verzenden naar de Cloud) wordt echter niet onderbroken.
   
   Als er één apparaat is geregistreerd bij uw service, is het rollover proces nu voltooid en kunt u de volgende stap overs Laan. Als er meerdere apparaten zijn geregistreerd bij uw service, gaat u verder met stap 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Stap 3: de versleutelings sleutel voor service gegevens op andere StorSimple-apparaten bijwerken
Deze stappen moeten worden uitgevoerd in de Windows Power shell-interface van uw StorSimple-apparaat als er meerdere apparaten zijn geregistreerd bij uw StorSimple Manager-service. De sleutel die u hebt verkregen in stap 2 moet worden gebruikt voor het bijwerken van alle resterende StorSimple-apparaten die zijn geregistreerd bij de StorSimple Manager-service.

Voer de volgende stappen uit om de service gegevens versleuteling op uw apparaat bij te werken.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>De versleutelings sleutel voor service gegevens op fysieke apparaten bijwerken
1. Gebruik Windows PowerShell voor StorSimple om verbinding te maken met de console. Selecteer optie 1 om u aan te melden met volledige toegang.
2. Typ het volgende achter de opdracht prompt:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Geef de versleutelings sleutel voor service gegevens op die u hebt verkregen in [stap 2: gebruik Windows PowerShell voor StorSimple om de wijziging van de versleutelings sleutel van de service gegevens te initiëren](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>De versleutelings sleutel voor service gegevens op alle 8010/8020-Cloud apparaten bijwerken
1. Down load en Setup [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) Power shell-script. 
2. Open Power shell en typ bij de opdracht prompt:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Met dit script wordt ervoor gezorgd dat de versleutelings sleutel voor service gegevens is ingesteld op alle 8010/8020-Cloud apparaten onder Apparaatbeheer.