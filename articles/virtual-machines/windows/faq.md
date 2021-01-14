---
title: Veelgestelde vragen over virtuele Windows-machines in azure
description: Hierin vindt u antwoorden op enkele veelgestelde vragen over virtuele Windows-machines die zijn gemaakt met het Resource Manager-model.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: eaa3f56389e8cc101b57aefce833266fa20f66f7
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197469"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Veelgestelde vragen over Windows Virtual Machines
In dit artikel worden enkele veelgestelde vragen over virtuele Windows-machines die zijn gemaakt in azure, behandeld met het Resource Manager-implementatie model. Voor de Linux-versie van dit onderwerp raadpleegt u [Veelgestelde vragen over Linux virtual machines](../linux/faq.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Wat kan ik uitvoeren op een VM van Azure?
Alle abonnees kunnen serversoftware uitvoeren op een virtuele machine van Azure. Voor informatie over het ondersteunings beleid voor het uitvoeren van micro soft server-software in azure, Zie [micro soft-server software ondersteuning voor azure virtual machines](https://support.microsoft.com/kb/2721672).

Bepaalde versies van Windows 7, Windows 8,1 en Windows 10 zijn beschikbaar voor MSDN Azure-voor delen-abonnees en MSDN dev en test betalen per gebruik, voor ontwikkel-en test taken. Zie het Engelstalige blogbericht [Windows Client images for MSDN subscribers](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) voor meer informatie, zoals instructies en beperkingen. 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hoeveel opslagruimte kan ik gebruiken met een virtuele machine?
Elke gegevens schijf kan Maxi maal 32.767 GiB zijn. Het aantal gegevensschijven dat u kunt gebruiken, is afhankelijk van de grootte van de virtuele machine. Zie [Grootten voor virtuele machines](../sizes.md) voor meer informatie.

Azure Managed Disks zijn de aanbevolen aanbiedingen voor schijf opslag voor gebruik met Azure Virtual Machines voor een permanente opslag van gegevens. U kunt meerdere Managed Disks gebruiken voor elke virtuele machine. Managed Disks bieden twee soorten duurzame opslag opties: Premium en Standard Managed Disks. Zie [Managed disks prijzen](https://azure.microsoft.com/pricing/details/managed-disks)voor prijs informatie.

Azure Storage-accounts kunnen ook opslag bieden voor de schijf met het besturings systeem en alle gegevens schijven. Elke schijf is een VHD-bestand dat wordt opgeslagen als een pagina-blob. Zie [deze pagina](https://azure.microsoft.com/pricing/details/storage/) voor prijsinformatie.

## <a name="how-can-i-access-my-virtual-machine"></a>Hoe kan ik toegang krijgen tot mijn virtuele machine?
Een externe verbinding tot stand brengen met behulp van Verbinding met extern bureaublad (RDP) voor een Windows-VM. Zie [verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](connect-logon.md)voor instructies. Er worden Maxi maal twee gelijktijdige verbindingen ondersteund, tenzij de server is geconfigureerd als een Extern bureaublad-services-sessiehost.  

Als u problemen ondervindt met Extern bureaublad, raadpleegt u [extern bureaublad verbindingen met een op Windows gebaseerde Azure Virtual Machine oplossen](../troubleshooting/troubleshoot-rdp-connection.md?toc=/azure/virtual-machines/windows/toc.json). 

Als u bekend bent met Hyper-V, is het mogelijk dat u op zoek bent naar een hulp programma dat lijkt op VMConnect. Azure biedt geen vergelijkbaar hulp programma omdat console toegang tot een virtuele machine niet wordt ondersteund.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Kan ik de tijdelijke schijf (standaard D: station) gebruiken om gegevens op te slaan?
Gebruik niet de tijdelijke schijf om gegevens op te slaan. Het is alleen tijdelijke opslag, waardoor het risico bestaat dat er gegevens verloren gaan die niet kunnen worden hersteld. Gegevens verlies kan optreden wanneer de virtuele machine naar een andere host wordt verplaatst. Hiervoor zijn verschillende redenen te bedenken, zoals het aanpassen van de grootte van een virtuele machine, het bijwerken van de host of een hardwarefout op de host.

Als u een toepassing hebt die de D: stationsletter moet gebruiken, kunt u stationsletters opnieuw toewijzen zodat de tijdelijke schijf iets anders dan D:. gebruikt. Zie [Use the D: drive as a data drive on a Windows VM](change-drive-letter.md) (Het station D: als gegevensstation gebruiken op een Windows-VM) voor instructies.


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hoe wijzig ik de stationsletter van de tijdelijke schijf?
U kunt de stationsletter wijzigen door het wissel bestand te verplaatsen en stationsletters opnieuw toe te wijzen, maar u moet er wel voor zorgen dat u de stappen in een specifieke volg orde uitvoert. Zie [Use the D: drive as a data drive on a Windows VM](change-drive-letter.md) (Het station D: als gegevensstation gebruiken op een Windows-VM) voor instructies.

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Kan ik een bestaande VM toevoegen aan een beschikbaarheidsset?
Nee. Als u wilt dat uw VM deel uitmaakt van een beschikbaarheidsset, moet u de virtuele machine in de set maken. Er is momenteel geen manier om een virtuele machine toe te voegen aan een beschikbaarheidsset nadat deze is gemaakt.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Kan ik een virtuele machine uploaden naar Azure?
Ja. Zie [on-premises Vm's migreren naar Azure](on-prem-to-azure.md)voor instructies.

## <a name="can-i-resize-the-os-disk"></a>Kan ik het formaat van de besturingssysteem schijf wijzigen?
Ja. Zie [het station van het besturings systeem uitbreiden van een virtuele machine in een Azure-resource groep](expand-os-disk.md)voor instructies.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan ik een bestaande virtuele machine van Azure kopiëren of klonen?
Ja. Met beheerde installatie kopieën kunt u een installatie kopie van een virtuele machine maken en vervolgens de installatie kopie gebruiken om meerdere nieuwe Vm's te bouwen. Zie [een aangepaste installatie kopie van een virtuele machine maken](tutorial-custom-images.md)voor instructies.

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Waarom zie ik Canada-centraal en Canada-oost regio's niet via Azure Resource Manager?

De twee nieuwe regio's van Canada-centraal en Canada-oost worden niet automatisch geregistreerd voor het maken van virtuele machines voor bestaande Azure-abonnementen. Deze registratie wordt automatisch uitgevoerd wanneer een virtuele machine via de Azure Portal naar een andere regio wordt geïmplementeerd met behulp van Azure Resource Manager. Nadat een virtuele machine is geïmplementeerd naar een andere Azure-regio, moeten de nieuwe regio's beschikbaar zijn voor de volgende virtuele machines.

## <a name="does-azure-support-linux-vms"></a>Biedt Azure ondersteuning voor Linux Vm's?
Ja. Zie [een virtuele Linux-machine in azure maken met behulp van de portal](../linux/quick-create-portal.md)om snel een virtuele Linux-machine te maken om uit te proberen.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan ik een NIC toevoegen aan mijn virtuele machine nadat deze is gemaakt?
Ja, dit is nu mogelijk. De virtuele machine moet eerst worden gestopt met het ongedaan maken van de toewijzing. Vervolgens kunt u een NIC toevoegen of verwijderen (tenzij dit de laatste NIC op de VM is). 

## <a name="are-there-any-computer-name-requirements"></a>Zijn er computer naam vereisten?
Ja. De computer naam mag Maxi maal 15 tekens lang zijn. Zie [regels en beperkingen voor naam conventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) voor meer informatie over het benoemen van uw resources.

## <a name="are-there-any-resource-group-name-requirements"></a>Zijn er vereisten voor de naam van de resource groep?
Ja. De naam van de resource groep mag Maxi maal 90 tekens lang zijn. Zie [regels en beperkingen voor naam conventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) voor meer informatie over resource groepen.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Wat zijn de vereisten van de gebruikers naam bij het maken van een VM?

Gebruikers namen mogen Maxi maal 20 tekens lang zijn en mogen niet eindigen op een punt ("."). 

De volgende gebruikers namen zijn niet toegestaan:

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Wat zijn de wachtwoord vereisten bij het maken van een VM?

Er zijn verschillende vereisten voor wachtwoord lengte, afhankelijk van het hulp programma dat u gebruikt:
 - Portal: tussen 12-72 tekens
 - Power shell-tussen 8-123 tekens
 - CLI-tussen 12-123

* Kleine letters bevatten
* Hoofdletters bevatten
* Een cijfer bevatten
* Een speciaal teken bevatten (overeenkomst met reguliere expressie [\W_])

De volgende wacht woorden zijn niet toegestaan:

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@ $ $w 0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>PA $ $word</td>
        <td>pass@word1</td>
        <td>Wacht woord!</td>
        <td>Wachtwoord1</td>
        <td>Password22</td>
    </tr>
</table>