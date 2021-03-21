---
title: 'Azure Security Center Veelgestelde vragen: vragen over virtuele machines'
description: Veelgestelde vragen over virtuele machines in Azure Security Center, een product dat u helpt bedreigingen te voor komen, te detecteren en erop te reageren
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 32023fca15de9d3f9258bc05166c3cff4184ea15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499846"
---
# <a name="faq---questions-about-virtual-machines"></a>Veelgestelde vragen: vragen over virtuele machines


## <a name="what-types-of-virtual-machines-are-supported"></a>Welke typen virtuele machines worden ondersteund?

Bewaking en aanbevelingen zijn beschikbaar voor virtuele machines (Vm's) die zijn gemaakt met behulp van de [klassieke en Resource Manager-implementatie modellen](../azure-resource-manager/management/deployment-models.md).

Zie de [ondersteunde platforms in azure Security Center](security-center-os-coverage.md) voor een lijst met ondersteunde platforms.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Waarom wordt de antimalware-oplossing die op mijn Azure-VM wordt uitgevoerd niet Azure Security Center herkend?

Azure Security Center heeft zicht baarheid van antimalware geïnstalleerd via Azure-extensies. Security Center is bijvoorbeeld niet in staat om antimalware te detecteren die vooraf is geïnstalleerd op een installatie kopie die u hebt ingevoerd of als u antimalware op uw virtuele machines hebt geïnstalleerd met behulp van uw eigen processen (zoals configuratie beheersystemen).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Waarom ontvang ik het bericht ' ontbrekende scan gegevens ' voor mijn VM?

Dit bericht wordt weergegeven wanneer er geen scangegevens zijn voor een VM. Het kan even duren (minder dan een uur) voordat scangegevens beschikbaar komen nadat de functie Gegevensverzameling is ingeschakeld in Azure Security Center. Nadat de eerste scangegevens zijn doorgegeven, kunt u dit bericht ontvangen omdat er helemaal geen scangegevens zijn of geen recente scangegevens. Er worden geen scangegevens aangeboden aan een VM die is gestopt. Dit bericht kan ook worden weer gegeven als de scan gegevens niet recent zijn ingevuld (in overeenstemming met het Bewaar beleid voor de Windows-agent, die een standaard waarde heeft van 30 dagen).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hoe vaak Security Center scant op beveiligings problemen met het besturings systeem, systeem updates en Endpoint Protection?

Hieronder vindt u de latentie tijden voor Security Center scans van beveiligings problemen, updates en problemen:

- Beveiligings configuraties van besturings systeem: gegevens worden binnen 48 uur bijgewerkt
- Systeem updates: gegevens worden binnen 24 uur bijgewerkt
- Endpoint Protection problemen: gegevens worden binnen 8 uur bijgewerkt

Security Center scant doorgaans elk uur op nieuwe gegevens en vernieuwt de aanbevelingen dienovereenkomstig. 

> [!NOTE]
> Security Center gebruikt de Log Analytics agent om gegevens te verzamelen en op te slaan. Zie [Azure Security Center platform Migration](./security-center-enable-data-collection.md)(Engelstalig) voor meer informatie.


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Waarom ontvang ik het bericht ' de VM-agent ontbreekt? '

De VM-agent moet op Vm's zijn geïnstalleerd om gegevens verzameling in te scha kelen. De VM-agent wordt standaard geïnstalleerd op VM's die zijn geïmplementeerd vanuit Azure Marketplace. Zie voor meer informatie over het installeren van de VM-agent op andere Vm's de blog post [VM-agent en-extensies](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).