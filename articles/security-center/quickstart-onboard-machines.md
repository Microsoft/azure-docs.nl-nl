---
title: Uw niet-Azure-machines verbinden met Azure Security Center
description: Leer hoe u uw niet-Azure-machines kunt verbinden met Security Center
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 68fcf8a8feb046fca2c26041d92264dd8b3a638e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465494"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Uw niet-Azure-machines verbinden met Security Center

Security Center kan de beveiligingsstatus van uw niet-Azure-computers controleren, maar u moet deze resources dan eerst verbinden met Azure.

U kunt uw niet-Azure-computers verbinden op een van de volgende manieren:

- Servers met Azure Arc gebruiken (**aanbevolen**)
- Via de pagina's van Azure Security Center in Azure Portal (**Aan de slag** en **Voorraad**)

Deze methoden worden beide op deze pagina beschreven.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Andere computers dan Azure-computers toevoegen met Azure Arc

Het gebruik van servers met Azure Arc is de aanbevolen manier om andere computers dan Azure-computers toe te voegen aan Azure Security Center.

Een computer met servers met Azure Arc, wordt een Azure-resource en wordt weergegeven in Azure Security Center met aanbevelingen zoals uw andere Azure-resources.

Bovendien bieden servers met Azure Arc uitgebreide mogelijkheden, zoals de optie voor het inschakelen van beleidsregels voor gastconfiguratie op de computer, het implementeren van de Log Analytics-agent als een uitbreiding en het vereenvoudigen van de implementatie met andere Azure-services. Zie [Ondersteunde scenario's](../azure-arc/servers/overview.md#supported-scenarios) voor een overzicht van de voordelen.

Meer informatie over [servers met Azure Arc](../azure-arc/servers/overview.md).

**U kunt als volgt Azure Arc implementeren:**

- Volg voor één computer de instructies in [Quick Start: een verbinding maken tussen hybride computers en Azure Arc-servers](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- Als u meerdere machines op schaal verbindt met servers met Azure Arc, raadpleegt u [Hybride machines op schaal verbinden met Azure](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> Als u computers in AWS in gebruik neemt, handelt de connector voor AWS van Azure Security Center de implementatie van Azure Arc op transparante wijze af. In [Uw AWS-accounts verbinden met Azure Security Center](quickstart-onboard-aws.md) vindt u hierover meer informatie.

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Niet-Azure-machines toevoegen vanuit de Azure-portal

1. Open in het menu van Security Center de pagina **Aan de slag**.
1. Selecteer het tabblad **Aan de slag**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Tabblad Aan de slag op de pagina Aan de slag" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Selecteer onder **Niet-Azure-servers toevoegen** de optie **Configureren**.

    > [!TIP]
    > U kunt Apparaten toevoegen ook openen met de knop **Niet-Azure-servers toevoegen** op de pagina **Voorraad**.
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Andere computers dan Azure-computers toevoegen vanaf de pagina Assetvoorraad":::

    Een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werkruimte of een andere werkruimte die u wilt gebruiken.

    U kunt computers aan een bestaande werkruimte toevoegen of een nieuwe werkruimte maken.

1. Selecteer eventueel  **Nieuwe werkruimte maken** om een nieuwe werkruimte te maken.

1. Selecteer in de lijst met werkruimten **Servers toevoegen** voor de relevante werkruimte.
    De pagina **Agentbeheer** wordt weergegeven.

    Kies hier de relevante procedure, afhankelijk van het type machines dat u aan het onboarden bent:

    - [Onboarding van uw Azure Stack hub-Vm's](#onboard-your-azure-stack-hub-vms)
    - [Uw Linux-machines onboarden](#onboard-your-linux-machines)
    - [Uw Windows-machines onboarden](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-hub-vms"></a>Onboarding van uw Azure Stack hub-Vm's

Om Azure Stack hub-Vm's toe te voegen, moet u de informatie op de pagina **agents beheren** en de uitbrei ding van de virtuele machine voor de **Azure monitor, update en configuratie beheer** configureren voor de virtuele machines die worden uitgevoerd op uw Azure stack hub-exemplaar.

1. Kopieer op de pagina **Agentbeheer** de **werkruimte-id** en **primaire sleutel** in Kladblok.
1. Meld u aan bij uw **Azure stack hub** -Portal en open de pagina **virtuele machines** .
1. Selecteer de virtuele machine die u met Security Center wilt beveiligen.
    >[!TIP]
    > Meer informatie over het maken van een virtuele machine op Azure Stack Hub vindt u in [deze Snelstartgids voor virtuele Windows-machines](/azure-stack/user/azure-stack-quick-windows-portal) of [op deze Quick start voor virtuele Linux-machines](/azure-stack/user/azure-stack-quick-linux-portal).
1. Selecteer **Extensies**. De lijst met virtuele machine-extensies die op deze virtuele machine is geïnstalleerd, wordt weergegeven.
1. Selecteer het tabblad **Toevoegen**. Het menu **Nieuwe resource** toont de lijst met beschikbare extensies voor virtuele machines.
1. Selecteer achtereenvolgens de extensie **Azure Monitor, update- en configuratiebeheer** en **Maken**. De configuratiepagina **Extensie installeren** wordt geopend.
    >[!NOTE]
    > Als u de **Azure monitor-, update-en configuratie beheer** uitbreiding die wordt vermeld in uw Marketplace niet ziet, kunt u contact opnemen met uw Azure stack hub-operator om deze beschikbaar te maken.
1. Op de configuratiepagina **Extensie installeren** plakt u de **werkruimte-id** en **werkruimtesleutel (primaire sleutel)** die u in de vorige stap naar Kladblok hebt gekopieerd.
1. Selecteer **OK** nadat u de configuratie hebt afgerond. De status van de extensie wordt weergegeven als **Inrichten geslaagd**. Het kan een uur duren voordat de virtuele machine wordt weergegeven in Security Center.

### <a name="onboard-your-linux-machines"></a>Uw Linux-machines onboarden

Om Linux-machines toe te voegen, hebt u de WGET-opdracht op de pagina **Agentbeheer** nodig.

1. Kopieer op de pagina **Agentbeheer** de opdracht **WGET** in Kladblok. Sla dit bestand op een locatie op die toegankelijk is vanaf uw Linux-computer.
1. Open op uw Linux-computer het bestand met de opdracht WGET. Selecteer de volledige inhoud en kopieer en plak deze in een terminalconsole.
1. Zodra de installatie is voltooid, kunt u controleren of de *omsagent* is geïnstalleerd door de opdracht *pgrep* uit te voeren. De opdracht retourneert de PID (proces-id) *omsagent*.
    De logboeken voor de agent zijn te vinden op: */var/opt/microsoft/omsagent/\<workspace id>/log/* Het kan tot 30 minuten duren voordat de nieuwe Linux-machine in Security Center verschijnt.

### <a name="onboard-your-windows-machines"></a>Uw Windows-machines onboarden

Om Windows-computers toe te voegen, hebt u de gegevens op de pagina **Agentbeheer** nodig en moet u het juiste agentbestand (32-/64-bits) downloaden.
1. Selecteer de koppeling **Windows-agent downloaden** die van toepassing is op het processortype van uw computer om het installatiebestand te downloaden.
1. Kopieer op de pagina **Agentbeheer** de **werkruimte-id** en **primaire sleutel** in Kladblok.
1. Kopieer het gedownloade installatiebestand naar de doelcomputer en voer het uit.
1. Volg de installatiewizard (**Volgende** **Ik ga akkoord**, **Volgende**, **Volgende**).
    1. Op de pagina **Azure Log Analytics** plakt u de **werkruimte-id** en **werkruimtesleutel (primaire sleutel)** die u in Kladblok hebt gekopieerd.
    1. Als u de computer wilt laten rapporteren bij een Log Analytics-werkruimte in de Azure Government-cloud, selecteert u **Azure US Government** in de vervolgkeuzelijst **Azure Cloud**.
    1. Als de computer met de Log Analytics-service moet communiceren via een proxyserver, selecteert u **Geavanceerd** en geeft u de URL en het poortnummer van de proxyserver op.
    1. Nadat u alle configuratie-instellingen hebt ingevoerd, selecteert u **Volgende**.
    1. Controleer op de pagina **Gereed om te installeren** de instellingen die moeten worden toegepast en selecteer **Installeren**.
    1. Selecteer op de pagina **Configuratie voltooid** de optie **Voltooien**.

Als u klaar bent wordt de **Microsoft Monitoring-agent** in het **Configuratiescherm** weergegeven. U kunt hier de configuratie controleren en verifiëren of de agent is verbonden.

Zie [Windows-computers verbinden](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard) voor meer informatie over het installeren en configureren van de agent.

::: zone-end

## <a name="verifying"></a>Controleren

Gefeliciteerd! Nu worden uw Azure- en niet-Azure-machines op één plek weergegeven. Open de pagina [Assetvoorraad](asset-inventory.md) en filter de relevante resourcetypen. Met de volgende pictogrammen worden de typen onderscheiden:

  ![ASC-pictogram voor andere computer dan Azure-computer](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Niet-Azure-machine

  ![ASC-pictogram voor Azure-computer](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

  ![ASC-pictogram voor Azure Arc-server](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Server met Azure Arc

## <a name="next-steps"></a>Volgende stappen

Op deze pagina werd uitgelegd hoe u uw niet-Azure-machines kunt toevoegen aan Azure Security Center. Om de status van deze niet-Azure-machines te controleren, gebruikt u de voorraad-hulpprogramma's zoals beschreven op de volgende pagina:

- [Uw resources verkennen en beheren met assetvoorraad](asset-inventory.md)