---
title: Veelgestelde vragen over virtuele Linux-machines in azure
description: Hierin vindt u antwoorden op enkele veelgestelde vragen over virtuele Linux-machines die zijn gemaakt met het Resource Manager-model.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 22be45403a7863328c5f6f2c883886296b734914
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549087"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Veelgestelde vragen over Linux Virtual Machines
In dit artikel worden enkele veelgestelde vragen over virtuele Linux-machines die zijn gemaakt in azure, behandeld met het Resource Manager-implementatie model. Voor de Windows-versie van dit onderwerp raadpleegt u [Veelgestelde vragen over Windows virtual machines](../windows/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Wat kan ik uitvoeren op een VM van Azure?
Alle abonnees kunnen serversoftware uitvoeren op een virtuele machine van Azure. Zie voor meer informatie [Linux op Azure-Endorsed-distributies](endorsed-distros.md)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hoeveel opslagruimte kan ik gebruiken met een virtuele machine?
Elke gegevens schijf kan Maxi maal 32.767 GiB zijn. Het aantal gegevensschijven dat u kunt gebruiken, is afhankelijk van de grootte van de virtuele machine. Zie [Grootten voor virtuele machines](../sizes.md) voor meer informatie.

Azure Managed Disks zijn de aanbevolen aanbiedingen voor schijf opslag voor gebruik met Azure Virtual Machines voor een permanente opslag van gegevens. U kunt meerdere Managed Disks gebruiken voor elke virtuele machine. Managed Disks bieden twee soorten duurzame opslag opties: Premium en Standard Managed Disks. Zie [Managed disks prijzen](https://azure.microsoft.com/pricing/details/managed-disks)voor prijs informatie.

Azure Storage-accounts kunnen ook opslag bieden voor de schijf met het besturings systeem en alle gegevens schijven. Elke schijf is een VHD-bestand dat wordt opgeslagen als een pagina-blob. Zie [deze pagina](https://azure.microsoft.com/pricing/details/storage/) voor prijsinformatie.

## <a name="how-can-i-access-my-virtual-machine"></a>Hoe kan ik toegang krijgen tot mijn virtuele machine?
Stel een externe verbinding in om u aan te melden bij de virtuele machine met behulp van Secure Shell (SSH). Zie de instructies voor het maken van verbinding met [Windows](ssh-from-windows.md) of [van Linux en Mac](mac-create-ssh-keys.md). SSH biedt standaard ondersteuning voor maximaal tien gelijktijdige verbindingen. U kunt dit aantal verhogen door het configuratiebestand te bewerken.

Als u problemen ondervindt, raadpleegt u het [oplossen van problemen met ssh-verbindingen (Secure Shell)](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Kan ik de tijdelijke schijf (/dev/sdb1) gebruiken om gegevens op te slaan?
Gebruik niet de tijdelijke schijf (/dev/sdb1) om gegevens op te slaan. Het is alleen voor tijdelijke opslag. Het risico bestaat dat er gegevens verloren gaan die niet kunnen worden hersteld.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan ik een bestaande virtuele machine van Azure kopiëren of klonen?
Ja. Zie [een kopie maken van een virtuele Linux-machine in het Resource Manager-implementatie model](copy-vm.md)voor instructies.

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Waarom zie ik Canada-centraal en Canada-oost regio's niet via Azure Resource Manager?
De twee nieuwe regio's van Canada-centraal en Canada-oost worden niet automatisch geregistreerd voor het maken van virtuele machines voor bestaande Azure-abonnementen. Deze registratie wordt automatisch uitgevoerd wanneer een virtuele machine via de Azure Portal naar een andere regio wordt geïmplementeerd met behulp van Azure Resource Manager. Nadat een virtuele machine is geïmplementeerd naar een andere Azure-regio, moeten de nieuwe regio's beschikbaar zijn voor de volgende virtuele machines.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan ik een NIC toevoegen aan mijn virtuele machine nadat deze is gemaakt?
Ja, dit is nu mogelijk. De virtuele machine moet eerst worden gestopt met het ongedaan maken van de toewijzing. Vervolgens kunt u een NIC toevoegen of verwijderen (tenzij dit de laatste NIC op de VM is). 

## <a name="are-there-any-computer-name-requirements"></a>Zijn er computer naam vereisten?
Ja. De computer naam mag Maxi maal 64 tekens lang zijn. Zie [regels en beperkingen voor naam conventies](/azure/architecture/best-practices/resource-naming) voor meer informatie over het benoemen van uw resources.

## <a name="are-there-any-resource-group-name-requirements"></a>Zijn er vereisten voor de naam van de resource groep?
Ja. De naam van de resource groep mag Maxi maal 90 tekens lang zijn. Zie [regels en beperkingen voor naam conventies](/azure/architecture/best-practices/resource-naming) voor meer informatie over resource groepen.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Wat zijn de vereisten van de gebruikers naam bij het maken van een VM?

Gebruikers namen moeten 1-32 tekens lang zijn.

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
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Wat zijn de wachtwoord vereisten bij het maken van een VM?

Er zijn verschillende vereisten voor wachtwoord lengte, afhankelijk van het hulp programma dat u gebruikt:
 - Portal: tussen 12-72 tekens
 - Power shell-tussen 8-123 tekens
 - CLI-tussen 12-123 tekens
 - Azure Resource Manager-sjablonen (ARM)-12-72 tekens en besturings tekens zijn niet toegestaan
 

Wacht woorden moeten ook voldoen aan drie van de volgende 4 complexiteits vereisten:

* Kleine letters bevatten
* Hoofdletters bevatten
* Een cijfer bevatten
* Een speciaal teken bevatten (overeenkomst met reguliere expressie [\W_])

De volgende wacht woorden zijn niet toegestaan:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@ $ $w 0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA $ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Wacht woord!</td>
        <td style="text-align:center">Wachtwoord1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
