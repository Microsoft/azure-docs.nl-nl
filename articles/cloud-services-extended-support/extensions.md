---
title: Uitbrei dingen voor Cloud Services (uitgebreide ondersteuning)
description: Uitbrei dingen voor Cloud Services (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6db43acda679c6c1c1edd6336f693cc4757b6d45
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220914"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Uitbrei dingen voor Cloud Services (uitgebreide ondersteuning)

Uitbrei dingen zijn kleine toepassingen die configuratie van na de implementatie en automatiserings taken op rollen bieden. U kunt bijvoorbeeld een Extern bureaublad verbinding in uw rol inschakelen tijdens de implementatie van de Cloud service (uitgebreide ondersteuning) met behulp van Extern bureaublad-extensie.  

## <a name="remote-desktop-extension"></a>Extern bureaublad extensie

Met Extern bureaublad kunt u toegang krijgen tot het bureau blad van een rol die wordt uitgevoerd in Azure. U kunt een verbinding met een extern bureau blad gebruiken om problemen met uw toepassing op te lossen en te diagnosticeren tijdens de uitvoering.

U kunt een verbinding met een extern bureau blad in uw rol tijdens de ontwikkeling inschakelen door de extern bureau blad-modules in uw service definitie of via de extern bureau blad-extensie op te nemen. 

Zie [extern bureau blad configureren vanaf de Azure Portal](enable-rdp.md) voor meer informatie

## <a name="windows-azure-diagnostics-extension"></a>Extensie voor Windows-Azure Diagnostics

U kunt belang rijke prestatie gegevens voor elke Cloud service bewaken. Elke Cloud service functie verzamelt minimale gegevens: CPU-gebruik, netwerk gebruik en schijf gebruik. Als de Cloud service de micro soft. Azure. Diagnostics-extensie op een rol heeft toegepast, kan die rol aanvullende gegevens punten verzamelen. 

Met de basis controle worden gegevens van de prestatie meter items uit rolinstanties gesampled en verzameld met een interval van 3 minuten. Deze basis bewakings gegevens worden niet opgeslagen in uw opslag account en er zijn geen extra kosten aan verbonden. 

Met geavanceerde controle worden extra metrische gegevens bemonsterd en verzameld met intervallen van vijf minuten, 1 uur en 12 uur. De geaggregeerde gegevens worden opgeslagen in een opslag account, in tabellen, en worden na 10 dagen verwijderd. Het gebruikte opslag account wordt geconfigureerd door de rol; u kunt verschillende opslag accounts voor verschillende rollen gebruiken. 

Zie [de Windows Azure Diagnostics-extensie Toep assen in Cloud Services (uitgebreide ondersteuning)](enable-wad.md) voor meer informatie

## <a name="anti-malware-extension"></a>Anti-malware-extensie
Een Azure-toepassing of-service kan micro soft antimalware voor Azure Cloud Services inschakelen en configureren met behulp van Power shell-cmdlets. Houd er rekening mee dat micro soft antimalware is geïnstalleerd met een uitgeschakelde status in het Cloud Services-platform met Windows Server 2012 R2 en ouder waarvoor een actie moet worden uitgevoerd door een Azure-toepassing om deze functie in te scha kelen. Voor Windows Server 2016 en hoger is Windows Defender standaard ingeschakeld, daarom kunnen deze cmdlets worden gebruikt voor het configureren van antimalware.

Zie voor meer informatie [micro soft antimalware toevoegen aan Azure-Cloud service met behulp van uitgebreide ondersteuning (CS-es)](https://docs.microsoft.com/azure/security/fundamentals/antimalware-code-samples#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support)

Als u meer wilt weten over Azure antimalware, kunt u [hier](https://docs.microsoft.com/azure/security/fundamentals/antimalware) terecht



## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
