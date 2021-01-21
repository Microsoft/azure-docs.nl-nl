---
title: 'Opstarten met het besturings systeem: de computer is onverwacht opnieuw opgestart of er is een onverwachte fout opgetreden'
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij de VM een onverwachte herstart of-fout ondervindt tijdens de installatie van Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: d8d2ab2bb3f24e1faa4791ebdc1ce3852f6a790e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632687"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Opstarten met het besturings systeem: de computer is onverwacht opnieuw opgestart of er is een onverwachte fout opgetreden

Dit artikel bevat stappen voor het oplossen van problemen waarbij de virtuele machine (VM) een onverwachte herstart of fout ondervindt tijdens de installatie van Windows.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname wordt weer gegeven met de volgende fout:

**De computer is onverwacht opnieuw opgestart of er is een onverwachte fout opgetreden. De Windows-installatie kan niet worden voortgezet. Als u Windows wilt installeren, klikt u op OK om de computer opnieuw op te starten en start u de installatie opnieuw.**

![Er is een fout opgetreden tijdens de installatie van Windows: de computer is onverwacht opnieuw opgestart of er is een onverwachte fout opgetreden. De Windows-installatie kan niet worden voortgezet. Als u Windows wilt installeren, klikt u op OK om de computer opnieuw op te starten en start u de installatie opnieuw.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Fout bij het starten van services: de computer is onverwacht opnieuw opgestart of er is een onverwachte fout opgetreden. De Windows-installatie kan niet worden voortgezet. Als u Windows wilt installeren, klikt u op OK om de computer opnieuw op te starten en start u de installatie opnieuw.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Oorzaak

Er wordt geprobeerd een [gegeneraliseerde installatie kopie](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)te starten met de computer, maar er is een probleem opgetreden vanwege een aangepast antwoord bestand (Unattend.xml) dat wordt verwerkt. **Aangepaste antwoord bestanden worden niet ondersteund in azure**. 

Het antwoord bestand is een speciaal XML-bestand met instellings definities en-waarden voor de configuratie-instellingen die u wilt automatiseren tijdens de installatie van een installatie van een Windows Server-besturings systeem. De configuratie opties bevatten instructies voor het partitioneren van schijven, waar u de Windows-installatie kopie kunt vinden die moet worden geïnstalleerd, de product codes die moeten worden toegepast en andere opdrachten die u wilt uitvoeren.

Opnieuw worden aangepaste antwoord bestanden niet ondersteund in Azure. Deze situatie treedt dus op wanneer een installatie kopie is voor bereid voor gebruik in azure, maar u hebt een aangepast Unattend.xml bestand opgegeven met behulp van **Sysprep** met een vlag die vergelijkbaar is met de volgende opdracht:

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

Gebruik in azure de optie **systeem OOBE (out-of-Box Experience) invoeren** in de **gebruikers interface van het hulp programma voor systeem voorbereiding** of gebruiken in `sysprep /oobe` plaats van het Unattend.xml-bestand.

Dit probleem wordt meestal gemaakt tijdens het gebruik van Sysprep met een on-premises VM om een gegeneraliseerde VM naar Azure te uploaden. In dit geval is het wellicht ook belang rijk om een gegeneraliseerde virtuele machine goed te uploaden.

## <a name="solution"></a>Oplossing

### <a name="do-not-use-unattendxml"></a>Gebruik niet Unattend.xml

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

U kunt dit probleem oplossen door [de Azure-richt lijnen voor het voorbereiden/vastleggen van een installatie kopie](../windows/upload-generalized-managed.md) te volgen en een nieuwe gegeneraliseerde installatie kopie voor te bereiden. Gebruik bij Sysprep **geen `/unattend:<your file’s name>` vlag**. Gebruik in plaats daarvan alleen de onderstaande vlaggen:

`sysprep /oobe /generalize /shutdown`

- Out-of-Box-Experience (OOBE) is de ondersteunde instelling voor virtuele Azure-machines.

U kunt ook de **gebruikers interface van het hulp programma voor systeem voorbereiding** gebruiken om dezelfde taak uit te voeren als de bovenstaande opdracht door de opties te selecteren die hieronder worden weer gegeven:

- Out-of-Box-Experience invoeren
- Generaliseren
- Afsluiten
 
![Venster hulp programma voor systeem voorbereiding met opties voor OOBE, generalize en afsluiten geselecteerd.](./media/unexpected-restart-error-during-vm-boot/3.png)
