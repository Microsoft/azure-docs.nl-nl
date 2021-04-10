---
title: Uw Data Science Virtual Machine bijwerken naar Ubuntu 18,04
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het upgraden van CentOS en Ubuntu 16,04 naar de nieuwste Ubuntu 18,04 Data Science Virtual Machine.
keywords: diep leren, AI, data Science tools, data Science virtual machine, team data Science process
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b897ff7527d2d60234162ccbdeb08a00260bb1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101659457"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Werk uw Data Science Virtual Machine bij naar Ubuntu 18.04

Als u een Data Science Virtual Machine hebt waarop een oudere versie wordt uitgevoerd, zoals Ubuntu 16,04 of CentOS, moet u uw DSVM migreren naar Ubuntu 18,04. Door te migreren, zorgt u ervoor dat u beschikt over de meest recente patches voor het besturings systeem, stuur Programma's, vooraf geïnstalleerde software en bibliotheek versies. In dit document leest u hoe u kunt migreren van oudere versies van Ubuntu of van CentOS. 

## <a name="prerequisites"></a>Vereisten

- Vertrouwd met SSH en de Linux-opdracht regel

## <a name="overview"></a>Overzicht

Er zijn twee mogelijke manieren om te migreren:

- In-place migratie, ook wel dezelfde server migratie genoemd. Met deze migratie wordt de bestaande VM bijgewerkt zonder dat er een nieuwe virtuele machine wordt gemaakt. In-place migratie is de gemakkelijkste manier om te migreren van Ubuntu 16,04 naar Ubuntu 18,04.
- Gelijktijdige migratie, ook wel de migratie tussen servers genoemd. Deze migratie brengt gegevens over van de bestaande virtuele machine naar een nieuw gemaakte VM. Gelijktijdige migratie is de manier om te migreren van CentOS naar Ubuntu 18,04. U kunt de voor keur geven aan gelijktijdige migratie voor de upgrade tussen Ubuntu-versies als u denkt dat uw oude installatie is verouderd geworden.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>Moment opname maken van uw VM voor het geval u terugdraait

Gebruik in de Azure Portal de zoek balk om de functionaliteit van **moment opnamen** te vinden. 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Scherm opname met Azure Portal en zoek balk, met * * moment opnamen * * gemarkeerd":::

1. Selecteer **toevoegen**, waarmee u naar de pagina **moment opname maken** gaat. Selecteer het abonnement en de resource groep van de virtuele machine. Selecteer voor **regio** dezelfde regio waarin de doel opslag zich bevindt. Selecteer de DSVM-opslag schijf en aanvullende back-upopties. **Standard-HDD** is een geschikt opslag type voor dit back-upscenario.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Scherm opname met opties voor het maken van een moment opname":::

2. Als alle details zijn gevuld en de validatie is geslaagd, selecteert u **controleren + maken** om de moment opname te valideren en te maken. Wanneer de moment opname is voltooid, ziet u een bericht met de melding dat de implementatie is voltooid.

## <a name="in-place-migration"></a>In-place migratie

Als u een oudere Ubuntu-versie migreert, kunt u ervoor kiezen om een in-place migratie uit te voeren. Deze migratie maakt geen nieuwe virtuele machine en heeft minder stappen dan een gelijktijdige migratie.  Als u een gelijktijdige migratie wilt uitvoeren omdat u meer controle wilt hebben of omdat u migreert vanuit een andere distributie, zoals CentOS, gaat u naar de sectie [gelijktijdige migratie](#side-by-side-migration) . 

1. Start vanuit de Azure Portal uw DSVM en meld u aan met SSH. Hiertoe selecteert u **Connect** en **SSH** en volgt u de verbindings instructies. 

1. Nadat u verbinding hebt gemaakt met een terminal sessie op uw DSVM, voert u de volgende opdracht uit:

    ```bash
    sudo do-release-upgrade
    ```

Het duurt even voordat het upgrade proces is voltooid. Wanneer dit het geval is, vraagt het programma toestemming om de virtuele machine opnieuw op te starten. Antwoord **Ja**. De verbinding met de SSH-sessie wordt verbroken wanneer het systeem opnieuw wordt opgestart.

### <a name="if-necessary-regenerate-ssh-keys"></a>Genereer, indien nodig, SSH-sleutels opnieuw

> [!IMPORTANT] 
> Nadat u de upgrade hebt uitgevoerd en opnieuw hebt opgestart, moet u de SSH-sleutels mogelijk opnieuw genereren.

Nadat uw VM is bijgewerkt en opnieuw is opgestart, probeert u deze opnieuw via SSH. Het IP-adres is mogelijk gewijzigd tijdens het opnieuw opstarten, dus bevestig het voordat u probeert verbinding te maken.

Als u het fout bericht **externe host-id is gewijzigd**, moet u uw SSH-referenties opnieuw genereren.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="Power shell-scherm opname met externe host-id waarschuwing gewijzigd":::

Als u dit wilt doen, voert u de volgende opdracht uit op de lokale computer:

```bash
ssh-keygen -R "your server hostname or ip"
```

U kunt nu verbinding maken met SSH. Als u nog steeds problemen ondervindt, kunt u op de pagina **verbinding** de koppeling volgen om **problemen met ssh-verbindingen op te lossen**.

## <a name="side-by-side-migration"></a>Gelijktijdige migratie

Als u migreert van CentOS of een schone installatie van het besturings systeem wilt uitvoeren, kunt u een gelijktijdige migratie doen. Dit type migratie heeft meer stappen, maar geeft u de controle over precies welke bestanden worden overgedragen.

Migraties van andere systemen op basis van dezelfde set upstream-bron pakketten moeten relatief eenvoudig zijn, bijvoorbeeld [Veelgestelde vragen/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

U kunt ervoor kiezen om de onderdelen van het besturings systeem van het bestands systeem te upgraden en de mappen van de gebruiker te laten staan, zoals `/home` in plaats daarvan. Als u de oude basis mappen van de gebruiker op de juiste plaats verlaat, worden er enkele problemen verwacht met de menu's GNOME/KDE en andere bureau blad-items. Het kan handig zijn om nieuwe gebruikers accounts te maken en de oude directory's ergens anders in het bestands systeem te koppelen voor het verwijzen, kopiëren of koppelen van het materiaal van gebruikers na de migratie.

### <a name="migration-at-a-glance"></a>Migratie in één oogopslag

1.  Een moment opname van uw bestaande virtuele machine maken zoals eerder beschreven

1.  Een schijf maken op basis van die moment opname

1.  Een nieuwe Ubuntu-Data Science Virtual Machine maken

1.  Gebruikers account (s) opnieuw maken op de nieuwe virtuele machine

1.  Koppel de schijf van de moment opname-VM als een gegevens schijf op uw nieuwe Data Science Virtual Machine

1.  De gewenste gegevens hand matig kopiëren

### <a name="create-a-disk-from-your-vm-snapshot"></a>Een schijf maken op basis van uw VM-moment opname

Als u nog geen VM-moment opname hebt gemaakt zoals eerder beschreven, doet u dat nu. 

1. In de Azure Portal zoekt u naar **schijven** en selecteert u **toevoegen**, waarmee de **schijf** pagina wordt geopend.

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Scherm opname van Azure Portal weer geven van de pagina zoeken naar schijven en de knop toevoegen":::

2. Stel het **abonnement**, de **resource groep** en de **regio** in op de waarden van uw VM-moment opname. Kies een **naam** voor de schijf die u wilt maken.

3. Selecteer **bron type** als **moment opname** en selecteer de moment opname van de virtuele machine als **moment opname** van de bron. Controleer de schijf en maak deze. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Scherm opname van het dialoog venster schijf maken met opties":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Een nieuwe Ubuntu-Data Science Virtual Machine maken

Maak een nieuwe Ubuntu-Data Science Virtual Machine met behulp van de [Azure Portal](https://portal.azure.com) of een [arm-sjabloon](./dsvm-tutorial-resource-manager.md). 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Gebruikers account (s) opnieuw maken op uw nieuwe Data Science Virtual Machine

Omdat u alleen gegevens van uw oude computer kopieert, moet u de gebruikers accounts en software omgevingen die u wilt gebruiken op de nieuwe machine opnieuw maken.

Linux is flexibel genoeg om u in staat te stellen mappen en paden in uw nieuwe installatie aan te passen om uw oude machine te volgen. Over het algemeen is het eenvoudiger om de voorkeurs indeling van het moderne Ubuntu te gebruiken en uw gebruikers omgeving en-scripts te wijzigen en aan te passen.

Voor meer informatie raadpleegt [u Quick Start: de data Science Virtual Machine instellen voor Linux (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Koppel de schijf van de moment opname-VM als een gegevens schijf op uw nieuwe Data Science Virtual Machine

1. Zorg ervoor dat uw Data Science Virtual Machine wordt uitgevoerd in de Azure Portal.

2. Ga in het Azure Portal naar de pagina van uw Data Science Virtual Machine. Kies de Blade **schijven** op de rails. Kies **bestaande schijven koppelen**.

3. Selecteer in de vervolg keuzelijst **schijf naam** de schijf die u hebt gemaakt op basis van de moment opname van uw oude VM.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Scherm afbeelding van de pagina opties voor DSVM met opties voor de schijf bijlage":::

4. Selecteer **Opslaan** om uw virtuele machine bij te werken.

> [!Important]
> De virtuele machine moet worden uitgevoerd op het moment dat u de gegevens schijf koppelt. Als de virtuele machine niet wordt uitgevoerd, kunnen de schijven in een onjuiste volg orde worden toegevoegd, waardoor het systeem verwarrend en mogelijk niet kan worden opgestart. Als u de gegevens schijf aan de virtuele machine toevoegt, kiest u de **X** naast de gegevens schijf, start u de virtuele machine en koppelt u deze opnieuw.

### <a name="manually-copy-the-wanted-data"></a>De gewenste gegevens hand matig kopiëren 

1. Meld u aan bij uw actieve virtuele machine met SSH.

2. Bevestig dat u de schijf hebt gekoppeld die u hebt gemaakt op basis van de moment opname van uw oude VM door de volgende opdracht uit te voeren:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    De resultaten moeten er ongeveer uitzien als in de volgende afbeelding. In de installatie kopie wordt de schijf `sda1` aan de hoofdmap gekoppeld en `sdb2` is de `/mnt` Scratch schijf. De gegevens schijf die is gemaakt op basis van de moment opname van uw oude VM, wordt geïdentificeerd als `sdc1` , maar is nog niet beschikbaar, zoals wordt aangegeven door het ontbreken van een koppelings locatie. De resultaten kunnen verschillende id's hebben, maar u moet een vergelijkbaar patroon zien.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Scherm opname van lsblk uitvoer met niet-gekoppeld gegevens station":::
    
3. Voor toegang tot het gegevens station maakt u een locatie voor de schijf en koppelt u deze. Vervang door `/dev/sdc1` de juiste waarde die wordt geretourneerd door `lsblk` :

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Bevat nu `/datadrive` de mappen en bestanden van uw oude data Science virtual machine. Verplaats of kopieer de gewenste mappen of bestanden van het gegevens station naar de nieuwe VM naar wens.

Zie [de portal gebruiken om een gegevens schijf te koppelen aan een virtuele Linux-machine](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)voor meer informatie.

## <a name="connect-and-confirm-version-upgrade"></a>Verbinding maken en versie-upgrade bevestigen

Controleer of u een interne of gelijktijdige migratie hebt uitgevoerd en of u de upgrade hebt voltooid. Voer vanuit een terminal sessie het volgende uit: 

```bash
cat /etc/os-release
```

U ziet dat u Ubuntu 18,04 uitvoert.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Scherm afbeelding van Ubuntu Terminal met besturingssysteem versie gegevens":::

De wijziging van de versie wordt ook weer gegeven in de Azure Portal.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Scherm opname van de portal met DSVM-eigenschappen, waaronder de versie van het besturings systeem":::

## <a name="next-steps"></a>Volgende stappen

- [Data Wetenschappen met een Ubuntu data Science machine in azure](./linux-dsvm-walkthrough.md)
- [Welke hulpmiddelen worden meegeleverd met de Azure Data Science Virtual Machine?](./tools-included.md)