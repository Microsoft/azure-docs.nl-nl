---
title: Problemen met het ontgrendelen van schijven Azure Data Box Disk oplossen | Microsoft Docs
description: Meer informatie over de werk stromen voor het oplossen van problemen met het hulp programma voor ontgrendelen met Azure Data Box Disk. Raadpleeg Data Box Disk fouten van het hulp programma voor ontgrendelen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: alkohli
ms.openlocfilehash: 866cf01243983863292ada0b086f8f5b2f94e412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87925558"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Problemen met het ontgrendelen van schijven oplossen in Azure Data Box Disk

Dit artikel is van toepassing op Microsoft Azure Data Box Disk en beschrijft de werk stromen die worden gebruikt om problemen op te lossen bij het gebruik van het hulp programma voor ontgrendelen. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Fouten met het ontgrendelingsprogramma voor Data Box Disk


| Fout bericht/gedrag hulpprogramma      | Aanbevelingen                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Het huidige .NET Framework wordt niet ondersteund. De ondersteunde versies zijn 4.5 of hoger.<br><br>Hulpprogramma wordt beëindigd met een bericht.  | .NET 4.5 is niet geïnstalleerd. Installeer .NET 4.5 of hoger op de hostcomputer waarop het ontgrendelingsprogramma voor Data Box Disk wordt uitgevoerd.                                                                            |
| Kan geen volumes ontgrendelen of verifiëren. Neem contact op met Microsoft Ondersteuning.  <br><br>Vergrendelde stations kunnen niet worden ontgrendeld of geverifieerd door het hulpprogramma. | De vergrendelde stations kunnen niet worden ontgrendeld door het hulpprogramma met de verstrekte wachtwoordsleutel. Neem contact op met Microsoft Ondersteuning voor volgende stappen.                                                |
| De volgende volumes zijn ontgrendeld en geverifieerd. <br>Volumestationsletters: E:<br>Er konden geen volumes worden ontgrendeld met de volgende wachtwoordsleutels: werwerqomnf, qwerwerqwdfda <br><br>Door het hulpprogramma worden sommige stations ontgrendeld en worden de letters van stations waarbij het is mislukt weergegeven.| Gedeeltelijk geslaagd. Sommige van de stations kunnen niet worden ontgrendeld met de verstrekte wachtwoordsleutel. Neem contact op met Microsoft Ondersteuning voor volgende stappen. |
| De vergrendelde volumes zijn niet gevonden. Controleer of de schijf die is ontvangen van Microsoft op de juiste manier is aangesloten en is vergrendeld.          | Er zijn geen vergrendelde stations gevonden door het hulpprogramma. De stations zijn al ontgrendeld of niet gedetecteerd. Zorg ervoor dat de stations zijn verbonden en zijn vergrendeld. <br> <br>Deze fout kan ook worden weer gegeven als u uw schijven hebt geformatteerd. Als u uw schijven hebt geformatteerd, zijn deze nu onbruikbaar. Neem contact op met Microsoft Ondersteuning voor volgende stappen.                                                          |
| Onherstelbare fout: ongeldige parameter<br>Parameternaam: invalid_arg<br>GEBRUIK:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Voorbeeld: DataBoxDiskUnlock /PassKeys:passkey1; passkey2; passkey3<br>Voorbeeld: DataBoxDiskUnlock /SystemCheck<br>Voorbeeld: DataBoxDiskUnlock/Help<br><br>/PassKeys:       Haal deze wachtwoordsleutel op vanuit de Azure DataBox Disk-order. De wachtwoordsleutel ontgrendelt uw schijven.<br>/ Help:           Deze optie biedt Help bij het gebruik van cmdlets en voorbeelden.<br>/ SystemCheck:     met deze optie wordt gecontroleerd of uw systeem voldoet aan de vereisten voor het uitvoeren van het hulpprogramma.<br><br>Druk op een willekeurige toets om af te sluiten. | Ongeldige parameter ingevoerd. De enige toegestane para meters zijn/SystemCheck,/PassKey en/Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Problemen voor schijven ontgrendelen bij gebruik van een Windows-client

In deze sectie vindt u een aantal veelvoorkomende problemen tijdens de implementatie van Data Box Disk wanneer u een Windows-client gebruikt voor het kopiëren van gegevens.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Probleem: kan station niet ontgrendelen met BitLocker
 
**Oorzaak** 

U hebt het wacht woord in het dialoog venster van BitLocker gebruikt en probeert de schijf te ontgrendelen via het dialoog venster BitLocker-ontgrendelings stations. Dit werkt niet.

**Oplossing**

Als u de Data Box schijven wilt ontgrendelen, moet u het hulp programma Data Box Disk ontgrendelen gebruiken en het wacht woord van de Azure Portal opgeven. Ga voor meer informatie naar [zelf studie: Azure data Box Disk uitpakken, verbinding maken en ontgrendelen](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Probleem: kan sommige volumes niet ontgrendelen of controleren. Neem contact op met Microsoft Ondersteuning.
 
**Oorzaak**

Mogelijk wordt de volgende fout in het fouten logboek weer geven en kunnen sommige volumes niet worden ontgrendeld of geverifieerd.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Dit geeft aan dat er waarschijnlijk de juiste versie van Windows Power shell op uw Windows-client ontbreekt.

**Oplossing**

U kunt [Windows Power shell v 5,0](https://www.microsoft.com/download/details.aspx?id=54616) installeren en de bewerking opnieuw proberen.
 
Als u de volumes nog steeds niet kunt ontgrendelen, kopieert u de logboeken van de map met het hulp programma voor het ontgrendelen van Data Box Disk en [neemt u contact op met Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van validatie problemen](data-box-disk-troubleshoot.md).
