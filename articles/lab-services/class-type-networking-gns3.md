---
title: Stel een netwerk omgeving in met Azure Lab Services en GNS3 | Microsoft Docs
description: Meer informatie over het instellen van een Lab met Azure Lab Services voor het leren van netwerken met GNS3.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500511"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Een Lab instellen om een netwerk klasse te leren 
In dit artikel wordt beschreven hoe u een klasse instelt die gericht is op het toestaan van studenten, het configureren, testen en oplossen van problemen met virtuele en Real Networks met behulp van [GNS3](https://www.gns3.com/) -software. 

Dit artikel bevat twee hoofd secties. In de eerste sectie wordt beschreven hoe u het leslokaal Lab maakt. In de tweede sectie wordt beschreven hoe u de sjabloon machine maakt waarvoor geneste virtualisatie is ingeschakeld en waarop GNS3 is geïnstalleerd en geconfigureerd.

## <a name="lab-configuration"></a>Lab-configuratie
Als u dit Lab wilt instellen, hebt u een Azure-abonnement nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services of een bestaand account gebruiken. Raadpleeg de volgende zelf studie voor het maken van een nieuw Lab-account: [zelf studie voor het instellen van een Lab-account](tutorial-setup-lab-account.md).

Volg [deze zelf studie](tutorial-setup-classroom-lab.md) voor het maken van een nieuw lab en pas vervolgens de volgende instellingen toe:

| Grootte van de virtuele machine | Installatiekopie |
| -------------------- | ----- | 
| Groot (geneste virtualisatie) | Windows 10 Pro, versie 1909 |

## <a name="template-machine"></a>Sjabloon machine 

Nadat de sjabloon machine is gemaakt, start u de machine en maakt u er verbinding mee om de volgende drie belang rijke taken uit te voeren. 
 
1. Bereid de sjabloon machine voor op geneste virtualisatie.
2. Installeer GNS3.
3. Maak een geneste GNS3-VM in Hyper-V.
4. Configureer GNS3 voor het gebruik van de Windows Hyper-V-VM.
5. De juiste apparaten toevoegen.
6. Publicatie sjabloon.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Sjabloon machine voorbereiden voor geneste virtualisatie
- Volg de instructies in [dit artikel](how-to-enable-nested-virtualization-template-vm.md) om de virtuele machine van de sjabloon voor te bereiden op geneste virtualisatie. 

### <a name="install-gns3"></a>GNS3 installeren
- Volg de instructies voor het [installeren van GNS3 in Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Zorg ervoor dat u de installatie van de **GNS3-VM** in het onderdeel dialoog venster opneemt. Zie hieronder voor meer informatie.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Uiteindelijk bereikt u de selectie van de GNS3-VM. Zorg ervoor dat u de **Hyper-V-** optie selecteert.

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Met deze optie worden de Power shell-script-en VHD-bestanden gedownload om de GNS3-VM te maken in Hyper-V-beheer. Ga door met de installatie met de standaard waarden. **Nadat de installatie is voltooid, start u GNS3 niet opnieuw**.

### <a name="create-gns3-vm"></a>GNS3 VM maken
Zodra de installatie is voltooid, wordt een zip-bestand **' GNS3.VM.Hyper-V.2.2.17.zip '** gedownload naar dezelfde map als het installatie bestand, met de stations en het Power shell-script om de Hyper-V-VM te maken.
- **Alles uitpakken** op de GNS3.VM.Hyper-V.2.2.17.zip.  Met deze actie worden de stations en het Power shell-script uitgepakt voor het maken van de virtuele machine.
- **Voer uit met Power shell** op het Power shell-script voor create-vm.ps1 door met de rechter muisknop op het bestand te klikken.
- Een wijzigings aanvraag voor het uitvoerings beleid kan worden weer gegeven. Voer ' Y ' in om het script uit te voeren.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Nadat het script is voltooid, kunt u controleren of de virtuele machine GNS3 VM is gemaakt in Hyper-V-beheer.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>GNS3 configureren voor het gebruik van een Hyper-V-VM
Nu GNS3 is geïnstalleerd en de GNS3-VM is toegevoegd, start u GNS3 om de twee samen te koppelen.  De [wizard GNS3 Setup wordt automatisch gestart.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)  
- De **uitvoerings apparaten van de virtuele machine gebruiken.** in- of uitschakelen.  Gebruik de standaard waarden voor de rest van de wizard totdat u het **hulp programma VMware vmrun niet kunt vinden.** fout.

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Klik op **OK** en **Annuleer** de wizard.
- Als u de verbinding met de Hyper-V-VM wilt volt ooien, opent u de GNS3-  ->  **voor keuren** bewerken  ->  **VM** en selecteert u **de virtuele machine GNS3 inschakelen** en selecteert u de **Hyper-V-** optie.
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Passende apparaten toevoegen

Op dit moment wilt u de juiste [apparaten toevoegen voor de klasse.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Sjabloon publiceren

Nu de sjabloon-VM correct is ingesteld en gereed is voor publicatie, zijn er enkele belang rijke punten om te controleren.
- Zorg ervoor dat de GNS3-VM wordt uitgeschakeld of uitgeschakeld.  Als de virtuele machine nog wordt uitgevoerd, wordt de virtuele machine beschadigd.
- Sluit GNS3 af, het publiceren terwijl en wordt uitgevoerd kan leiden tot onbedoelde neven effecten.
- Opschonen van installatie bestanden of andere overbodige bestanden.

## <a name="cost"></a>Kosten  

Als u de kosten van dit Lab wilt schatten, kunt u het volgende voor beeld gebruiken: 
 
Voor een klasse van 25 studenten met 20 uur geplande en tien uur aan quota voor huis werk of-toewijzingen is de prijs voor het Lab: 

25 studenten * (20 + 10) uur * 84 Lab-eenheden * 0,01 USD per uur = 630 USD. 

**Belang rijk:** Kosten raming is alleen bedoeld als voor beeld.  Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor actuele Details over prijzen.

## <a name="conclusion"></a>Conclusie
In dit artikel werd uitgelegd hoe u stapsgewijs door de stappen voor het maken van een Lab voor netwerk configuratie met behulp van GNS3.

## <a name="next-steps"></a>Volgende stappen
De volgende stappen zijn gebruikelijk voor het instellen van elk lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users).