---
title: Problemen met de implementatie van Linux-VM'S oplossen | Microsoft Docs
description: Problemen met de implementatie van Resource Manager oplossen wanneer u een nieuwe virtuele Linux-machine in azure maakt
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: daberry
ms.openlocfilehash: d94f7389ce96c2e3bda35413cbcc7b1e8a992683
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197537"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Problemen met de implementatie van Resource Manager oplossen met het maken van een nieuwe virtuele Linux-machine in azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Belangrijkste problemen
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Voor andere problemen met en vragen over de VM-implementatie raadpleegt u [Troubleshoot deploying Linux virtual machine issues in Azure](troubleshoot-deploy-vm-linux.md) (Problemen met de implementatie van Linux-VM's in Azure oplossen).

## <a name="collect-activity-logs"></a>Activiteiten logboeken verzamelen
Om te beginnen met het oplossen van problemen, verzamelt u de activiteiten Logboeken om de fout te identificeren die aan het probleem is gekoppeld. De volgende koppelingen bevatten gedetailleerde informatie over het proces dat moet worden gevolgd.

[Implementatiebewerkingen bekijken](../../azure-resource-manager/templates/deployment-history.md)

[Activiteiten logboeken weer geven om Azure-resources te beheren](../../azure-resource-manager/management/view-activity-logs.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Als het besturings systeem wordt gegeneraliseerd door Linux en het is geüpload en/of vastgelegd met de gegeneraliseerde instelling, zijn er geen fouten. Op dezelfde manier geldt dat als het besturings systeem Linux gespecialiseerd is en het is geüpload en/of vastgelegd met de gespecialiseerde instelling, er geen fouten zijn.

**Upload fouten:**

**N <sup>1</sup>:** Als het besturings systeem wordt gegeneraliseerd door Linux en het is geüpload als gespecialiseerd, ontvangt u een time-outfout voor inrichting omdat de virtuele machine zich in de inrichtings fase bevindt.

**N <sup>2</sup>:** Als het besturings systeem Linux gespecialiseerd is en het is geüpload als gegeneraliseerd, krijgt u een inrichtings fout, omdat de nieuwe virtuele machine wordt uitgevoerd met de oorspronkelijke computer naam, gebruikers naam en wacht woord.

**Oplossing:**

Als u beide fouten wilt oplossen, uploadt u de oorspronkelijke VHD, die beschikbaar is op locatie, met dezelfde instelling als die voor het besturings systeem (gegeneraliseerd/gespecialiseerd). Als u wilt uploaden als gegeneraliseerd, moet u eerst de uitvoering van het programma uitvoeren.

**Fouten vastleggen:**

**N <sup>3</sup>:** Als het besturings systeem Linux gegeneraliseerd is en het is vastgelegd als gespecialiseerd, krijgt u een time-outfout van inrichting omdat de oorspronkelijke virtuele machine niet bruikbaar is omdat deze is gemarkeerd als gegeneraliseerd.

**N <sup>4</sup>:** Als het besturings systeem Linux gespecialiseerd is en het is vastgelegd als gegeneraliseerd, krijgt u een inrichtings fout omdat de nieuwe virtuele machine wordt uitgevoerd met de oorspronkelijke computer naam, gebruikers naam en wacht woord. De oorspronkelijke virtuele machine kan ook niet worden gebruikt omdat deze is gemarkeerd als gespecialiseerd.

**Oplossing:**

Om beide fouten op te lossen, verwijdert u de huidige installatie kopie uit de portal en [legt u deze opnieuw vast vanaf de huidige vhd's](../linux/capture-image.md) met dezelfde instelling als voor het besturings systeem (gegeneraliseerd/gespecialiseerd).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Probleem: afbeelding voor aangepast/galerie/Marketplace; toewijzings fout
Deze fout treedt op wanneer de nieuwe VM-aanvraag is vastgemaakt aan een cluster dat de aangevraagde VM-grootte niet kan ondersteunen of waarvoor er geen beschik bare ruimte beschikbaar is om aan de aanvraag te voldoen.

**Oorzaak 1:** Het cluster kan de aangevraagde VM-grootte niet ondersteunen.

**Oplossing 1:**

* Voer de aanvraag opnieuw uit met een kleinere VM-grootte.
* Als de grootte van de aangevraagde virtuele machine niet kan worden gewijzigd:
  * Stop alle virtuele machines in de beschikbaarheidsset.
    Klik op **resource groepen**  >  *uw resource groep*  >  **resources**  >  *uw beschikbaarheidsset*  >  **virtual machines**  >  *de virtuele machine* wordt  >  **gestopt**.
  * Nadat alle Vm's zijn gestopt, maakt u de nieuwe virtuele machine op de gewenste grootte.
  * Start eerst de nieuwe VM en selecteer vervolgens elk van de gestopte Vm's en klik op **Start**.

**Oorzaak 2:** Het cluster heeft geen vrije resources.

**Oplossing 2:**

* Voer de aanvraag op een later tijdstip opnieuw uit.
* Als de nieuwe VM deel kan uitmaken van een andere beschikbaarheidsset
  * Maak een nieuwe virtuele machine in een andere beschikbaarheidsset (in dezelfde regio).
  * Voeg de nieuwe virtuele machine toe aan hetzelfde virtuele netwerk.

## <a name="next-steps"></a>Volgende stappen
Als u problemen ondervindt bij het starten van een gestopt Linux-VM of het formaat van een bestaande virtuele Linux-machine in azure wijzigt, raadpleegt u problemen met [de implementatie van Resource Manager oplossen bij het opnieuw starten of wijzigen van het formaat van een bestaande Linux-vm in azure](./troubleshoot-deploy-vm-linux.md?toc=/azure/virtual-machines/linux/toc.json).
