---
title: Scripts uitvoeren in een Azure Windows-VM
description: In dit onderwerp wordt beschreven hoe u scripts kunt uitvoeren op een virtuele Windows-machine
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 24ff0d8089055f83c76156cf5fb163313faf9492
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043475"
---
# <a name="run-scripts-in-your-windows-vm"></a>Scripts uitvoeren in uw Windows-VM

Om taken te automatiseren of problemen op te lossen, moet u mogelijk opdrachten uitvoeren in een VM. Het volgende artikel bevat een kort overzicht van de functies die beschikbaar zijn voor het uitvoeren van scripts en opdrachten binnen uw Vm's.

## <a name="custom-script-extension"></a>Aangepaste scriptextensie

De [aangepaste script extensie](../extensions/custom-script-windows.md) wordt voornamelijk gebruikt voor configuratie na implementatie en software-installatie.

* Down load en voer scripts uit op virtuele machines van Azure.
* Kan worden uitgevoerd met Azure Resource Manager sjablonen, Azure CLI, REST API, Power shell of Azure Portal.
* Script bestanden kunnen worden gedownload uit Azure Storage of GitHub, of worden meegeleverd vanaf uw PC wanneer ze vanuit de Azure Portal worden uitgevoerd.
* Power shell-script uitvoeren in Windows-computers en bash-script in Linux-machines.
* Handig voor configuratie na de implementatie, software-installatie en andere configuratie-of beheer taken.

## <a name="run-command"></a>Opdracht uitvoeren

Met de functie voor het [uitvoeren van opdrachten](run-command.md) kunt u virtuele machines en toepassings beheer en probleem oplossing met behulp van scripts maken, ook als de computer niet bereikbaar is, bijvoorbeeld als de gast firewall geen RDP-of SSH-poort geopend heeft.

* Voer scripts uit op virtuele machines van Azure.
* Kan worden uitgevoerd met [Azure Portal](run-command.md), [rest API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure cli](/cli/azure/vm/run-command#az-vm-run-command-invoke)of [Power shell](/powershell/module/az.compute/invoke-azvmruncommand)
* Voer snel een script uit en Bekijk de uitvoer en herhaal dit als dat nodig is in de Azure Portal.
* Script kan rechtstreeks worden getypt of u kunt een van de ingebouwde scripts uitvoeren.
* Power shell-script uitvoeren in Windows-computers en bash-script in Linux-machines.
* Handig voor het beheer van virtuele machines en toepassingen en voor het uitvoeren van scripts op virtuele machines die onbereikbaar zijn.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

De [Hybrid Runbook worker](../../automation/automation-hybrid-runbook-worker.md) biedt algemene computer-, toepassings-en omgevings beheer met aangepaste scripts van gebruikers die zijn opgeslagen in een Automation-account.

* Voer scripts uit in Azure en niet-Azure-machines.
* Kan worden uitgevoerd met Azure Portal, Azure CLI, REST API, Power shell, webhook.
* Scripts die zijn opgeslagen en beheerd in een Automation-account.
* Power shell, Power shell-werk stroom, python of grafische runbooks uitvoeren
* Geen tijds limiet voor de uitvoerings tijd van het script.
* Meerdere scripts kunnen gelijktijdig worden uitgevoerd.
* Volledige script uitvoer wordt geretourneerd en opgeslagen.
* De taak geschiedenis is 90 dagen beschikbaar.
* Scripts kunnen worden uitgevoerd als lokaal systeem of met door de gebruiker opgegeven referenties.
* Vereist [hand matige installatie](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Seriële console

De [seriële console](/troubleshoot/azure/virtual-machines/serial-console-windows) biedt directe toegang tot een virtuele machine, vergelijkbaar met een toetsen bord dat is verbonden met de virtuele machine.

* Voer opdrachten uit op virtuele machines van Azure.
* Kan worden uitgevoerd met behulp van een op tekst gebaseerde console op de machine in de Azure Portal.
* Meld u aan bij de computer met een lokaal gebruikers account.
* Dit is handig wanneer de toegang tot de virtuele machine nodig is, ongeacht de status van het netwerk of het besturings systeem van de machine.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende functies die beschikbaar zijn voor het uitvoeren van scripts en opdrachten binnen uw Vm's.

* [Aangepaste scriptextensie](../extensions/custom-script-windows.md)
* [Run-opdracht](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Seriële console](/troubleshoot/azure/virtual-machines/serial-console-windows)