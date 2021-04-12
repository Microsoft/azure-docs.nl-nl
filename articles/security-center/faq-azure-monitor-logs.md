---
title: 'Azure Security Center Veelgestelde vragen: vragen over bestaande Log Analytics agents'
description: Deze veelgestelde vragen beantwoordt vragen aan klanten die al gebruikmaken van de Log Analytics agent en overwegen Azure Security Center, een product dat u helpt bij het voor komen, detecteren en reageren op bedreigingen.
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
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701957"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Veelgestelde vragen voor klanten die Azure Monitor-logboeken al gebruiken<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Worden bestaande verbindingen tussen Vm's en werk ruimten Security Center overschreven?

Als de Log Analytics-agent al is geïnstalleerd op een virtuele machine als Azure-extensie, wordt de bestaande werkruimte verbinding niet overschreven door Security Center. In plaats daarvan maakt Security Center gebruik van de bestaande werk ruimte. De virtuele machine wordt beveiligd, op voor waarde dat de oplossing ' Security ' of ' SecurityCenterFree ' is geïnstalleerd op de werk ruimte waarmee deze wordt gerapporteerd. 

Er wordt een Security Center oplossing geïnstalleerd op de werk ruimte die is geselecteerd in het scherm voor het verzamelen van gegevens, als deze nog niet aanwezig is, en de oplossing wordt alleen toegepast op de relevante Vm's. Wanneer u een oplossing toevoegt, wordt deze automatisch geïmplementeerd op alle Windows-en Linux-agents die zijn verbonden met uw Log Analytics-werk ruimte. Met [doel items van oplossingen](../azure-monitor/insights/solution-targeting.md) kunt u een bereik Toep assen op uw oplossingen.

> [!TIP]
> Als de Log Analytics-agent rechtstreeks op de virtuele machine is geïnstalleerd (niet als een Azure-extensie), installeert Security Center de Log Analytics agent niet en is de beveiligings controle beperkt.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Installeert Security Center oplossingen op mijn bestaande Log Analytics-werk ruimten? Wat zijn de gevolgen voor facturering?
Als Security Center identificeert dat een virtuele machine al is verbonden met een werk ruimte die u hebt gemaakt, Security Center de oplossingen in deze werk ruimte op basis van uw prijs configuratie. De oplossingen worden alleen toegepast op de relevante Azure-Vm's, via [oplossings doelen](../azure-monitor/insights/solution-targeting.md), zodat de facturering op dezelfde manier blijft.

- **Azure Defender uit** : Security Center installeert de oplossing ' SecurityCenterFree ' in de werk ruimte. Er worden geen kosten in rekening gebracht.
- **Met Azure Defender on** – Security Center installeert u de oplossing beveiliging in de werk ruimte.

   ![Oplossingen in de standaardwerk ruimte](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Ik heb al werk ruimten in mijn omgeving. kan ik deze gebruiken voor het verzamelen van beveiligings gegevens?
Als de Log Analytics-agent al is geïnstalleerd op een virtuele machine als Azure-extensie, Security Center gebruikt de bestaande verbonden werk ruimte. Er wordt een Security Center oplossing geïnstalleerd op de werk ruimte als deze nog niet aanwezig is, en de oplossing wordt alleen toegepast op de relevante Vm's via de [doel stellingen](../azure-monitor/insights/solution-targeting.md)van de oplossing.

Als Security Center de Log Analytics-agent op Vm's installeert, worden de standaardwerk ruimte (n) gebruikt die door Security Center is gemaakt als Security Center niet naar een bestaande werk ruimte wijst.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Ik heb al een beveiligings oplossing voor mijn werk ruimten. Wat zijn de gevolgen voor facturering?
De oplossing beveiligings & controle wordt gebruikt om **Azure Defender voor servers** in te scha kelen. Als de oplossing beveiligings & controle al is geïnstalleerd op een werk ruimte, gebruikt Security Center de bestaande oplossing. Er is geen wijziging in de facturering.