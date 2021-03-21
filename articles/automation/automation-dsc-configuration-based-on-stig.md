---
title: Gegevens configureren op basis van STIG voor de configuratie van de Azure Automation status
description: In dit artikel leest u hoe u gegevens configureert op basis van DoD STIG voor de configuratie van de Azure Automation-status.
keywords: DSC, Power shell, configuratie, installatie
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dab0cd7f7d660808b4ed7a91318baad55f80928c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015133"
---
# <a name="configure-data-based-on-security-technical-information-guide-stig"></a>Gegevens configureren op basis van de hand leiding voor technische informatie over beveiliging (STIG)

> Van toepassing op: Windows Power shell 5,1

Het maken van configuratie-inhoud voor de eerste keer kan lastig zijn.
In veel gevallen is het doel om de configuratie van servers te automatiseren volgens een ' basis lijn ' die in het ideale geval wordt afgestemd op een branche aanbeveling.

> [!NOTE]
> In dit artikel wordt verwezen naar een oplossing die wordt onderhouden door de open source-community.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samen werking, niet van micro soft.

## <a name="community-project-powerstig"></a>Community-Project: PowerSTIG

Een community-project met de naam [PowerSTIG](https://github.com/microsoft/powerstig) is bedoeld om dit probleem op te lossen door DSC-inhoud te genereren op basis van de [open bare informatie](https://public.cyber.mil/stigs/) over Stig (technische implementatie handleiding voor beveiliging),

Het verwerken van basis lijnen is ingewik kelder dan het klinkt.
Veel organisaties moeten [uitzonde ringen documenteren](https://github.com/microsoft/powerstig#powerstigdata) voor regels en deze gegevens op schaal beheren.
PowerSTIG lost het probleem op door [samengestelde resources](https://github.com/microsoft/powerstig#powerstigdsc) op te geven voor elk gebied van de configuratie, in plaats van te proberen het hele bereik met instellingen in één groot bestand op te lossen.

Zodra de configuraties zijn gegenereerd, kunt u de DSC- [configuratie scripts](/powershell/scripting/dsc/configurations/configurations) gebruiken om MOF-bestanden te genereren en [de MOF-bestanden uploaden naar Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Registreer uw servers vervolgens [on-premises](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) of [in azure](./automation-dsc-onboarding.md#enable-azure-vms) om configuraties te halen.

Als u PowerSTIG wilt uitproberen, gaat u naar de [PowerShell Gallery](https://www.powershellgallery.com) en downloadt u de oplossing of klikt u op project site om de [documentatie](https://github.com/microsoft/powerstig)weer te geven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview)(Engelstalig) voor meer informatie over Power shell DSC.
- Meer informatie over Power shell DSC-resources in [DSC-resources](/powershell/scripting/dsc/resources/resources).
- Zie [Configuring the local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)(Engelstalig) voor meer informatie over de configuratie van de lokale Configuration Manager.
