---
title: Servers voor Azure-Arc migreren tussen regio's
description: Meer informatie over het migreren van een Azure Arc-server van de ene regio naar een andere.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 251a347205d93af715add52db293d8000438df44
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650172"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Servers voor Azure-Arc migreren tussen regio's

Er zijn scenario's waarin u uw bestaande Azure-Arc-server wilt verplaatsen van de ene regio naar een andere. U hebt bijvoorbeeld gerealiseerd dat de machine is geregistreerd in de verkeerde regio, om de beheer baarheid te verbeteren of om te kunnen door gaan.

Als u een Azure Arc-server wilt migreren van de ene Azure-regio naar een andere, moet u de VM-extensies verwijderen, de resource in azure verwijderen en deze opnieuw maken in de andere regio. Voordat u deze stappen uitvoert, moet u de machine controleren om te controleren welke VM-extensies zijn geïnstalleerd.

> [!NOTE]
> De geïnstalleerde uitbrei dingen blijven actief en uitvoeren nadat deze procedure is voltooid, kunt u ze niet meer beheren. Als u de uitbrei dingen op de computer probeert te implementeren, kan dit onvoorspelbaar gedrag ondervinden.

## <a name="move-machine-to-other-region"></a>Machine naar een andere regio verplaatsen

> [!NOTE]
> Tijdens deze bewerking resulteert dit in uitval tijd tijdens de migratie.

1. VM-extensies verwijderen die zijn geïnstalleerd via de [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension), met behulp van de [Azure cli](manage-vm-extensions-cli.md#remove-an-installed-extension)of met behulp van [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

2. Gebruik het hulp programma **azcmagent** met de para meter [Disconnect](manage-agent.md#disconnect) om de verbinding van de machine met Azure Arc te verbreken en de machine resource uit Azure te verwijderen. Als de computer wordt losgekoppeld van de Arc-servers, wordt de verbonden machine agent niet verwijderd. u hoeft de agent niet te verwijderen als onderdeel van dit proces. U kunt dit hand matig uitvoeren terwijl u zich interactief aanmeldt, of u automatiseert met dezelfde service-principal die u hebt gebruikt om meerdere agents uit te voeren, of met een [toegangs token](../../active-directory/develop/access-tokens.md)van een micro soft-identiteits platform. Raadpleeg het volgende [artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) om een service-principal te maken als u geen Service-Principal hebt gebruikt om de machine te registreren met servers die geschikt zijn voor Azure Arc.

3. Registreer de verbonden machine agent opnieuw met servers met Arc-functionaliteit in de andere regio. Voer het `azcmagent` hulp programma uit met de para meter [Connect](manage-agent.md#connect) om deze stap te volt ooien.

4. Implementeer de VM-extensies die oorspronkelijk zijn geïmplementeerd op de machine van servers waarop Arc is ingeschakeld. Als u de Azure Monitor voor VM's-agent (Insights) of de Log Analytics agent hebt geïmplementeerd met behulp van een Azure-beleid, worden de agents na de volgende [evaluatie cyclus](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)opnieuw geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

* Informatie over probleem oplossing vindt u in de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

* Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), controleert u of de computer rapporteert aan de verwachte log Analytics-werk ruimte, de bewaking met [Azure monitor inschakelen met](../../azure-monitor/vm/vminsights-enable-policy.md) het beleid voor vm's en nog veel meer.