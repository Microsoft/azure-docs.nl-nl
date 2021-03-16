---
title: Configuraties converteren naar samengestelde resources voor Azure Automation status configuratie
description: In dit artikel wordt beschreven hoe u configuraties converteert naar samengestelde resources voor Azure Automation status configuratie.
keywords: DSC, Power shell, configuratie, installatie
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5833308f03d328d988ac818d83ff5ee618a4c79c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563569"
---
# <a name="convert-configurations-to-composite-resources"></a>Configuraties converteren naar samengestelde resources

> Van toepassing op: Windows Power shell 5,1

Zodra u aan de slag gaat met het ontwerpen van configuraties, kunt u snel scenario's maken waarmee groepen instellingen worden beheerd.
Voor beelden zijn:

- een webserver maken
- een DNS-server maken
- een share Point-server maken
- een SQL-cluster configureren
- Firewall-instellingen beheren
- wachtwoord instellingen beheren

Als u geïnteresseerd bent in het delen van dit werk met anderen, is de beste optie om de configuratie te verpakken als een [samengestelde resource](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Het maken van samengestelde resources voor de eerste keer kan overweldigend zijn.

> [!NOTE]
> In dit artikel wordt verwezen naar een oplossing die wordt onderhouden door de open source-community.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samen werking, niet van micro soft.

## <a name="community-project-compositeresource"></a>Community-Project: CompositeResource

Er is een door de Community onderhouden oplossing gemaakt met de naam [CompositeResource](https://github.com/microsoft/compositeresource) om deze uitdaging op te lossen.

CompositeResource automatiseert het proces van het maken van een nieuwe module op basis van uw configuratie.
U begint met [punt](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/) met het configuratie script op uw werk station (of build-server) zodat het in het geheugen wordt geladen.
In plaats van de configuratie uit te voeren voor het genereren van een MOF-bestand, gebruikt u de functie van de CompositeResource-module om een conversie te automatiseren.
De cmdlet laadt de inhoud van uw configuratie, haalt de lijst met para meters op en genereert een nieuwe module met alles wat u nodig hebt.

Zodra u een module hebt gegenereerd, kunt u de versie verhogen en release opmerkingen toevoegen telkens wanneer u wijzigingen aanbrengt en deze naar uw eigen [PowerShellGet-opslag plaats](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)publiceert.

Wanneer u een samengestelde resource module hebt gemaakt met uw configuratie (of meerdere configuraties), kunt u deze gebruiken in de [ontwerp ervaring](./compose-configurationwithcompositeresources.md) in azure, of deze toevoegen aan [DSC-configuratie scripts](/powershell/scripting/dsc/configurations/configurations) om MOF-bestanden te genereren en [de MOF-bestanden te uploaden naar Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Registreer uw servers vervolgens [on-premises](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) of [in azure](./automation-dsc-onboarding.md#enable-azure-vms) om configuraties te halen.
De meest recente update van het project heeft ook [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) gepubliceerd voor Azure Automation om het proces van het importeren van configuraties vanuit de PowerShell Gallery te automatiseren.

Als u het maken van samengestelde resources voor DSC wilt automatiseren, gaat u naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/compositeresource/) en downloadt u de oplossing of klikt u op project site om de [documentatie](https://github.com/microsoft/compositeresource)weer te geven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview)(Engelstalig) voor meer informatie over Power shell DSC.
- Meer informatie over Power shell DSC-resources in [DSC-resources](/powershell/scripting/dsc/resources/resources).
- Zie [Configuring the local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)(Engelstalig) voor meer informatie over de configuratie van de lokale Configuration Manager.
