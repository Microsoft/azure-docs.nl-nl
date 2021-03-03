---
title: Uw Azure percept DK bijwerken via een USB-verbinding
description: Meer informatie over hoe u Azure percept DK bijwerkt via een USB-verbinding
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662376"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Azure percept DK bijwerken via een USB-verbinding

Volg deze hand leiding voor informatie over het uitvoeren van een USB-update voor de draag kaart van Azure percept DK.

## <a name="prerequisites"></a>Vereisten
- Hostcomputer met een beschik bare USB-C-of USB-poort.
- Azure percept DK-Carrier Board (dev kit) en geleverde USB-C naar USB-C-kabel. Als uw hostcomputer een USB-A-poort, maar geen USB-C-poort heeft, kunt u een USB-C naar USB-een kabel gebruiken (afzonderlijk verkocht).
- [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) installeren (beheerders toegang nodig).
- Installeer het NXP UUU-hulp programma. [Down load de nieuwste versie](https://github.com/NXPmicro/mfgtools/releases) van uuu.exe bestand (voor Windows) of het UUU-bestand (voor Linux) op het tabblad assets.
- [Installeer 7-zip](https://www.7-zip.org/). Deze software wordt gebruikt voor het uitpakken van het onbewerkte installatie kopie bestand uit het gecomprimeerde XZ-bestand. Down load en installeer het juiste exe-bestand.

## <a name="steps"></a>Stappen
1.  Down load de volgende [drie USB-update bestanden](https://go.microsoft.com/fwlink/?linkid=2155734):
    - pe101-UEFI-***&lt; versie nummer &gt;***. RAW. xz
    - emmc_full.txt
    - Fast-hab-FW. RAW
 
1. Uitpakken naar pe101-UEFI-***&lt; versie &gt; nummer**_. onbewerkt van het gecomprimeerde pe101-UEFI-_ * _&lt; versie nummer &gt;_* *. RAW. xz-bestand. Weet u niet hoe u kunt uitpakken? Down load en installeer 7-zip. Klik vervolgens met de rechter muisknop op het **xz** -installatie kopie bestand en selecteer 7-zip &gt; extract hier.

1. Kopieer de volgende drie bestanden naar de map die het UUU-hulp programma bevat:
    - Geëxtraheerd pe101-UEFI-***&lt; versie nummer &gt;***. RAW-bestand (uit stap 2).
    - emmc_full.txt (uit stap 1).
    - Fast-hab-FW. RAW (uit stap 1).
 
1. Schakel de dev kit in.
1. [Verbinding maken met de dev kit via SSH](./how-to-ssh-into-percept-dk.md)
1. Open een Windows-opdracht prompt (Start &gt; cmd) of een Linux-Terminal en navigeer naar de map waarin de update bestanden worden opgeslagen. Voer de volgende opdracht uit om de update te initiëren:
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
Na het uitvoeren van deze opdrachten ziet u mogelijk een bericht waarin wordt aangegeven dat er wordt gewacht op het apparaat... in de opdracht prompt. Dit wordt verwacht en u moet door gaan met de volgende stap.
    
1. Verbind de Cable Carrier Board van de dev kit met de hostcomputer via een USB-kabel. U kunt altijd verbinding maken met de USB-C-poort van de draaggolf kaarten naar de USB-c-of USB-poort van de hostcomputer (USB-C naar USB-een kabel die afzonderlijk wordt verkocht), afhankelijk van de beschik bare poorten. 
 
1. Voer in de SSH/PuTTy-Terminal de volgende opdrachten in om de dev kit in te stellen op de USB-modus en vervolgens de dev kit opnieuw op te starten.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. U kunt een indicatie krijgen dat de hostcomputer het apparaat herkent en dat het update proces automatisch wordt gestart. Ga terug naar de opdracht prompt om de status weer te geven. Het proces duurt Maxi maal tien minuten en wanneer de update is voltooid, wordt het volgende bericht weer gegeven: "geslaagd 1 fout 0"
 
1. Als de update is voltooid, schakelt u het verwerkings bord uit. Koppel de USB-kabel los van de PC.  Sluit de Azure percept Vision-module aan op het draaggolf bord met behulp van de USB-kabel.

1. Schakel het draag signaal op de achtergrond uit.

## <a name="next-steps"></a>Volgende stappen

De dev kit is nu bijgewerkt. U kunt door gaan met de ontwikkeling en bewerking van uw Devkit.