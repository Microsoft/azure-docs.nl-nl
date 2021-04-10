---
title: Azure Defender voor SQL instellen
description: Meer informatie over het inschakelen van het optionele Azure Defender for SQL-abonnement van Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: a91329d3bd0247932614233ef5b1ec71bf4d2a6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465460"
---
# <a name="enable-azure-defender-for-sql-servers-on-machines"></a>Azure Defender voor SQL-servers op computers inschakelen 

Dit Azure Defender-abonnement detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te gebruiken.

U ziet waarschuwingen wanneer er verdachte database activiteiten, potentiële kwetsbaar heden of SQL-injectie aanvallen en afwijkende database toegang en query patronen zijn.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|**Azure Defender voor SQL-servers op computers** wordt gefactureerd zoals wordt weer gegeven op [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)|
|Beveiligde SQL-versies:|Azure SQL Server (alle versies die worden gedekt door micro soft support)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nee](./media/icons/no-icon.png) China Gov, Other Gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Azure Defender voor SQL-servers op computers instellen

Dit abonnement inschakelen:

[Stap 1. Richt de Log Analytics-agent in op de host van uw SQL-Server:](#step-1-provision-the-log-analytics-agent-on-your-sql-servers-host)

[Stap 2. Schakel het optionele abonnement in op de pagina prijzen en instellingen van Security Center:](#step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page)


### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Stap 1. Richt de Log Analytics-agent in op de host van uw SQL-Server:

- **SQL Server op de Azure-VM** : als uw SQL-machine wordt gehost op een Azure-VM, kunt u [het automatisch inrichten van <a name="auto-provision-mma"></a> de log Analytics agent inschakelen](security-center-enable-data-collection.md#auto-provision-mma). U kunt ook de hand matige procedure volgen voor [het Onboarden van uw Azure stack hub-vm's](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms).
- **SQL Server op Azure Arc** : als uw SQL Server wordt beheerd door [Azure Arc](../azure-arc/index.yml) -servers, kunt u de log Analytics agent implementeren met behulp van de Security Center aanbeveling ' Log Analytics agent moet worden geïnstalleerd op uw op Windows gebaseerde Azure-Arc-machines (preview) '. U kunt ook de installatie methoden volgen die worden beschreven in de [Azure Arc-documentatie](../azure-arc/servers/manage-vm-extensions.md).

- **SQL Server on-premises** : als uw SQL Server wordt gehost op een on-premises Windows-machine zonder Azure Arc, hebt u twee opties om deze te verbinden met Azure:
    
    - **Azure-Arc implementeren** : u kunt een Windows-machine verbinden met Security Center. Azure Arc biedt echter een diep gaande integratie in *al* uw Azure-omgeving. Als u Azure Arc hebt ingesteld, ziet u de pagina **SQL Server – Azure Arc** in de portal en worden uw beveiligings waarschuwingen weer gegeven op een specifiek tabblad **beveiliging** op die pagina. De eerste en aanbevolen optie is om [Azure Arc op de host in te stellen](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) en de instructies voor **SQL Server op Azure Arc** te volgen.
        
    - **De Windows-machine verbinden zonder Azure Arc** : als u ervoor kiest om verbinding te maken met een SQL Server die wordt uitgevoerd op een Windows-computer zonder Azure Arc te gebruiken, volgt u de instructies in [Windows-computers verbinden met Azure monitor](../azure-monitor/agents/agent-windows.md).


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Stap 2. Schakel het optionele abonnement in op de pagina prijzen en instellingen van Security Center:

1. Open in het menu van Security Center de pagina **& instellingen voor prijzen** .

    - Als u de **standaard werkruimte van Azure Security Center** gebruikt (met de naam ' defaultworkspace-[uw abonnement-id]-[regio] '), selecteert u het relevante **abonnement**.

    - Als u **een niet-standaard werk ruimte** gebruikt, selecteert u de relevante **werk ruimte** (Voer indien nodig de naam van de werk ruimte in het filter in):

        ![Uw niet-standaard werkruimte zoeken op titel](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Stel de optie voor **Azure Defender voor SQL-servers op machines** in **op aan**. 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="Security Center prijs pagina met optionele abonnementen":::

    Het plan wordt ingeschakeld op alle SQL-servers die zijn verbonden met de geselecteerde werk ruimte. De beveiliging is volledig actief na de eerste keer opnieuw opstarten van de SQL Server-instantie.

    >[!TIP] 
    > Als u een nieuwe werk ruimte wilt maken, volgt u de instructies in [een log Analytics-werk ruimte maken](../azure-monitor/logs/quick-create-workspace.md).


1. U kunt ook e-mail meldingen configureren voor beveiligings waarschuwingen. 
    U kunt een lijst met ontvangers instellen om een e-mail melding te ontvangen wanneer Security Center waarschuwingen worden gegenereerd. Het e-mail bericht bevat een directe koppeling naar de waarschuwing in Azure Security Center met alle relevante informatie. Zie [e-mail meldingen instellen voor beveiligings waarschuwingen](security-center-provide-security-contact-details.md)voor meer informatie.


## <a name="azure-defender-for-sql-alerts"></a>Azure Defender voor SQL-waarschuwingen
Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om SQL-machines te openen of misbruik te maken. Deze gebeurtenissen kunnen waarschuwingen activeren die worden weer gegeven op de [pagina met waarschuwingen](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Beveiligings waarschuwingen verkennen en onderzoeken

Azure Defender voor SQL-waarschuwingen zijn beschikbaar op de pagina waarschuwingen van Security Center, het tabblad Beveiliging van de resource, het [Azure Defender-dash board](azure-defender-dashboard.md)of via de directe koppeling in de e-mail waarschuwingen.

1. Als u waarschuwingen wilt weer geven, selecteert u **beveiligings waarschuwingen** in het menu van Security Center en selecteert u een waarschuwing.

1. Waarschuwingen zijn zodanig ontworpen dat ze zich op zichzelf bevinden, met gedetailleerde stappen voor herbemiddeling en informatie over onderzoek. U kunt verder onderzoeken door andere Azure Security Center en Azure-Sentinel-mogelijkheden te gebruiken voor een bredere weer gave:

    * Schakel de controle functie van SQL Server in voor verdere onderzoeken. Als u een Azure Sentinel-gebruiker bent, kunt u de SQL-controle logboeken uploaden van de gebeurtenissen van het Windows-beveiligings logboek naar Sentinel en een rijke onderzoek ervaring hebben. [Meer informatie over het controleren van SQL Server](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Gebruik de aanbevelingen van Security Center voor de hostmachine die in elke waarschuwing worden aangegeven om uw beveiligings postuur te verbeteren. Hierdoor worden de Risico's van toekomstige aanvallen verminderd. 

    Meer [informatie over het beheren van en reageren op waarschuwingen](security-center-managing-and-responding-alerts.md).


## <a name="next-steps"></a>Volgende stappen

Raadpleeg het volgende artikel voor gerelateerd materiaal:

- [Beveiligings waarschuwingen voor SQL Database en Azure Synapse Analytics](alerts-reference.md#alerts-sql-db-and-warehouse)
- [E-mailmeldingen voor beveiligingswaarschuwingen instellen](security-center-provide-security-contact-details.md)
- [Meer informatie over Azure Sentinel](../sentinel/index.yml)