---
title: Scripts uitvoeren in een Azure Windows-VM
description: In dit onderwerp wordt beschreven hoe u scripts in een virtuele Windows-machine kunt uitvoeren
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 7bf62eb2ab8d2ce82ce73e3e8ae26cf303b8ba67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765877"
---
# <a name="run-scripts-in-your-windows-vm"></a>Scripts uitvoeren in uw Windows-VM

Als u taken wilt automatiseren of problemen wilt oplossen, moet u mogelijk opdrachten uitvoeren in een VM. In het volgende artikel wordt een kort overzicht gegeven van de functies die beschikbaar zijn voor het uitvoeren van scripts en opdrachten binnen uw VM's.

## <a name="custom-script-extension"></a>Aangepaste scriptextensie

De [aangepaste scriptextensie](../extensions/custom-script-windows.md) wordt voornamelijk gebruikt voor configuratie na de implementatie en software-installatie.

* Scripts downloaden en uitvoeren in virtuele Azure-machines.
* Kan worden uitgevoerd met Azure Resource Manager sjablonen, Azure CLI, REST API, PowerShell of Azure Portal.
* Scriptbestanden kunnen worden gedownload uit Azure Storage of GitHub, of van uw pc worden geleverd wanneer ze worden uitgevoerd vanuit de Azure Portal.
* PowerShell-script uitvoeren op Windows-machines en Bash-script op Linux-machines.
* Handig voor configuratie na implementatie, software-installatie en andere configuratie- of beheertaken.

## <a name="run-command"></a>Voer de opdracht  uit

Met [de Uitvoeropdracht-functie](run-command.md) kunt u virtuele machines en toepassingen beheren en problemen oplossen met behulp van scripts. Deze functie is zelfs beschikbaar wanneer de computer niet bereikbaar is, bijvoorbeeld als de RDP- of SSH-poort niet is geopend voor de gastfirewall.

* Scripts uitvoeren in virtuele Azure-machines.
* Kan worden uitgevoerd [met Azure Portal,](run-command.md) [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) [Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke)of [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand)
* Voer snel een script uit en bekijk uitvoer en herhaal dit naar behoefte in Azure Portal.
* Script kan rechtstreeks worden getypt of u kunt een van de ingebouwde scripts uitvoeren.
* PowerShell-script uitvoeren op Windows-machines en Bash-script op Linux-machines.
* Handig voor het beheer van virtuele machines en toepassingen en voor het uitvoeren van scripts op virtuele machines die onbereikbaar zijn.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

De [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) biedt algemeen machine-, toepassings- en omgevingsbeheer met aangepaste scripts van gebruikers die zijn opgeslagen in een Automation-account.

* Scripts uitvoeren in Azure- en niet-Azure-machines.
* Kan worden uitgevoerd met Azure Portal, Azure CLI, REST API, PowerShell, webhook.
* Scripts die zijn opgeslagen en beheerd in een Automation-account.
* PowerShell, PowerShell Workflow, Python of grafische runbooks uitvoeren
* Er is geen tijdslimiet voor de scriptrun time.
* Meerdere scripts kunnen gelijktijdig worden uitgevoerd.
* Volledige scriptuitvoer wordt geretourneerd en opgeslagen.
* Taakgeschiedenis beschikbaar voor 90 dagen.
* Scripts kunnen worden uitgevoerd als lokaal systeem of met door de gebruiker opgegeven referenties.
* Handmatige [installatie vereist](../../automation/automation-windows-hrw-install.md)

## <a name="serial-console"></a>Seriële console

De [Seriële console](/troubleshoot/azure/virtual-machines/serial-console-windows) biedt directe toegang tot een VM, vergelijkbaar met een toetsenbord dat is verbonden met de VM.

* Opdrachten uitvoeren in virtuele Azure-machines.
* Kan worden uitgevoerd met behulp van een console op basis van tekst op de computer in Azure Portal.
* Meld u aan bij de computer met een lokaal gebruikersaccount.
* Dit is handig wanneer toegang tot de virtuele machine nodig is, ongeacht het netwerk of de status van het besturingssysteem van de machine.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende functies die beschikbaar zijn voor het uitvoeren van scripts en opdrachten binnen uw VM's.

* [Aangepaste scriptextensie](../extensions/custom-script-windows.md)
* [Uitvoeropdracht](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [Seriële console](/troubleshoot/azure/virtual-machines/serial-console-windows)