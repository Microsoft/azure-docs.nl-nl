---
title: Bescherm uw Azure VMware-oplossing-Vm's met Azure Security Center-integratie
description: Meer informatie over het beveiligen van uw Azure VMware-oplossings-Vm's met de systeem eigen beveiligings tools van Azure vanaf één dash board in Azure Security Center.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 58cfa1e8e7faa56675d966f86d3b390e52acec27
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584954"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Bescherm uw Azure VMware-oplossing-Vm's met Azure Security Center-integratie

Azure native Security-hulpprogram ma's bieden een veilige infra structuur voor een hybride omgeving van Azure, Azure VMware-oplossing en on-premises virtuele machines (Vm's). In dit artikel wordt beschreven hoe u Azure-hulpprogram ma's instelt voor de beveiliging van hybride omgevingen. U gebruikt verschillende hulp middelen om verschillende soorten bedreigingen te identificeren en op te lossen.

## <a name="azure-native-services"></a>Systeem eigen Azure-Services

Hier volgt een korte samen vatting van elke Azure native-service:

- **Log Analytics-werk ruimte:** Log Analytics werk ruimte is een unieke omgeving voor het opslaan van logboek gegevens. Elke werkruimte heeft een eigen gegevensopslagplaats en configuratie. Gegevens bronnen en oplossingen zijn geconfigureerd om hun gegevens op te slaan in een specifieke werk ruimte.
- **Azure Security Center:** Azure Security Center is een systeem voor geïntegreerde infrastructuur beveiliging. Het verbetert de beveiligings postuur van de data centers en biedt geavanceerde beveiliging tegen bedreigingen in de hybride werk belastingen in de Cloud of on-premises.
- **Azure-Sentinel:** Azure Sentinel is een Cloud-native, Security Information Event Management (SIEM) en een via-oplossing (Security Orchestration Automated Response). Het biedt intelligente beveiligings analyses en bedreigings informatie in een omgeving. Het is één oplossing voor waarschuwings detectie, zicht baarheid van bedreigingen, proactieve jacht en bedreigings reacties.

## <a name="topology"></a>Topologie

![Een diagram van de architectuur van de geïntegreerde beveiliging van Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

Met de Log Analytics-agent kunt u logboek gegevens verzamelen van Azure, Azure VMware-oplossing en on-premises Vm's. De logboek gegevens worden verzonden naar Azure Monitor logboeken en worden opgeslagen in een Log Analytics-werk ruimte. U kunt de Log Analytics-agent implementeren met behulp van Arc ingeschakelde servers [ondersteuning voor VM-extensies](../azure-arc/servers/manage-vm-extensions.md) voor nieuwe en bestaande vm's. 

Zodra de logboeken zijn verzameld door de Log Analytics-werk ruimte, kunt u de Log Analytics-werk ruimte configureren met Azure Security Center. Azure Security Center kunt de beveiligings status van virtuele machines van Azure VMware-oplossingen evalueren en een waarschuwing genereren voor elk kritiek beveiligings probleem. Zo worden de ontbrekende patches voor het besturings systeem, de configuratie van de beveiliging en [Endpoint Protection](../security-center/security-center-services.md)geëvalueerd.

U kunt de Log Analytics-werk ruimte configureren met Azure Sentinel voor waarschuwings detectie, zicht baarheid van bedreigingen, proactieve jacht en bedreigings reacties. In het voor gaande diagram is Azure Security Center verbonden met Azure Sentinel met behulp van Azure Security Center-connector. Azure Security Center stuurt het probleem van de omgeving door naar Azure Sentinel om een incident te maken en te koppelen aan andere bedreigingen. U kunt ook de geplande regel query maken om ongewenste activiteiten te detecteren en deze naar de incidenten te converteren.

## <a name="benefits"></a>Voordelen

- Systeem eigen services van Azure kunnen worden gebruikt voor de beveiliging van hybride omgevingen in azure, Azure VMware-oplossing en on-premises Services.
- Met een Log Analytics-werk ruimte kunt u de gegevens of de logboeken op één punt verzamelen en dezelfde gegevens presen teren aan verschillende Azure native-Services.
- Azure Security Center biedt een aantal functies, waaronder:
    - Bestandsintegriteit controleren
    - Detectie van bestanden met een aanval
    - Evaluatie van patch voor besturings systeem 
    - Evaluatie van onjuiste beveiligingsconfiguratie
    - Evaluatie van eindpuntbeveiliging
- Met Azure Sentinel kunt u:
    - Verzamel gegevens op Cloud schaal voor alle gebruikers, apparaten, toepassingen en infra structuur, zowel on-premises als in meerdere clouds.
    - Detecteer eerder niet-gedetecteerde bedreigingen.
    - Onderzoek bedreigingen met kunst matige intelligentie en zoek naar verdachte activiteiten op schaal.
    - Reageer snel op incidenten met ingebouwde indeling en automatisering van algemene taken.

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

U hebt een Log Analytics-werk ruimte nodig voor het verzamelen van gegevens uit verschillende bronnen. Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/learn/quick-create-workspace.md)voor meer informatie. 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Security Center implementeren en virtuele machines van Azure VMware-oplossingen configureren

Azure Security Center is een vooraf geconfigureerd hulp programma dat geen implementatie vereist. Zoek in het Azure Portal naar **Security Center** en selecteer dit.

### <a name="enable-azure-defender"></a>Azure Defender inschakelen

Azure Defender breidt de geavanceerde bedreigings beveiliging van Azure Security Center over uw hybride werk belastingen zowel on-premises als in de Cloud uit. Als u uw Azure VMware-oplossing-Vm's wilt beveiligen, moet u Azure Defender inschakelen. 

1. Selecteer in Security Center **aan** de slag.

2. Selecteer het tabblad **upgrade** en selecteer vervolgens uw abonnement of werk ruimte. 

3. Selecteer **Upgrade** om Azure Defender in te schakelen.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Virtuele machines van Azure VMware-oplossingen toevoegen aan Security Center

1. Zoek in de Azure Portal op **Azure Arc** en selecteer deze.

2. Onder resources selecteert u **servers** en vervolgens **+ toevoegen**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Een scherm opname van de pagina met Azure Arc-servers voor het toevoegen van een Azure VMware-oplossings-VM aan Azure.":::

3. Selecteer **script genereren**.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Een scherm opname van de Azure Arc-pagina met de optie voor het toevoegen van een server met een interactief script."::: 
 
4. Selecteer **volgende** op het tabblad **vereisten** .

5. Vul op het tabblad **Resource Details** de volgende gegevens in: 
    - Abonnement
    - Resourcegroep
    - Region 
    - Besturingssysteem
    - Details van de proxy server
    
    Selecteer vervolgens **volgende: Tags**.

6. Op het tabblad **labels** selecteert u **volgende**.

7. Klik op het tabblad **script downloaden en uitvoeren** op **downloaden**.

8. Geef uw besturings systeem op en voer het script uit op uw Azure VMware-oplossings-VM.

## <a name="view-recommendations-and-passed-assessments"></a>Aanbevelingen en geslaagde evaluaties weer geven

1. In Azure Security Center selecteert u **inventaris** in het linkerdeel venster.

2. Voor resource type selecteert u **servers-Azure Arc**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Een scherm afbeelding van de pagina met Azure Security Center-inventarisatie met servers-Azure-Arc geselecteerd onder resource type.":::

3. Selecteer de naam van uw resource. Er wordt een pagina geopend met de details van de beveiligings status van uw resource.

4. Onder **aanbeveling lijst**, selecteert u de tabbladen **aanbevelingen**, **door gegeven beoordelingen** en **niet-beschik bare beoordelingen** om deze details weer te geven.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Een scherm opname van Azure Security Center waarin aanbevelingen en evaluaties van de beveiliging worden weer gegeven.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Een Azure Sentinel-werk ruimte implementeren

Azure Sentinel is gebaseerd op een Log Analytics-werk ruimte. Uw eerste stap in het onboarding van Azure-Sentinel is het selecteren van de Log Analytics werk ruimte die u voor dat doel wilt gebruiken.

1. Zoek in het Azure Portal naar **Azure Sentinel** en selecteer deze.

2. Op de pagina Sentinel-werk ruimten van Azure selecteert u **+ toevoegen**.

3. Selecteer de werk ruimte Log Analytics en selecteer **toevoegen**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Gegevens verzamelaar inschakelen voor beveiligings gebeurtenissen op virtuele machines van Azure VMware-oplossing

Nu bent u klaar om Azure Sentinel te verbinden met uw gegevens bronnen, in dit geval beveiligings gebeurtenissen.

1. Op de pagina Sentinel-werk ruimten van Azure selecteert u de geconfigureerde werk ruimte.

2. Onder configuratie selecteert u **gegevens connectors**.

3. Selecteer in de kolom connector naam de optie **beveiligings gebeurtenissen** in de lijst en selecteer vervolgens **connector pagina openen**.

4. Selecteer op de pagina connector de gebeurtenissen die u wilt streamen en selecteer vervolgens **wijzigingen Toep assen**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Scherm opname van de pagina beveiligings gebeurtenissen in de Azure-Sentinel, waar u kunt selecteren welke gebeurtenissen u wilt streamen.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Azure-Sentinel verbinden met Azure Security Center  

1. Selecteer de geconfigureerde werk ruimte op de pagina voor de Azure-Sentinel-werk ruimte.

2. Onder configuratie selecteert u **gegevens connectors**.

3. Selecteer **Azure Security Center** in de lijst en selecteer vervolgens de **pagina connector openen**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Scherm afbeelding van de pagina Data connectors in azure Sentinel waarin de selectie wordt weer gegeven om Azure Security Center met Azure Sentinel te verbinden.":::

4. Selecteer **verbinding maken** om de Azure Security Center met Azure Sentinel te verbinden.

5. Schakel **incident maken** in om een incident te genereren voor Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Regels maken om beveiligings Risico's te identificeren

Nadat u gegevens bronnen hebt verbonden met Azure Sentinel, kunt u regels maken om waarschuwingen te genereren op basis van gedetecteerde bedreigingen. In het volgende voor beeld maken we een regel voor het identificeren van pogingen om zich aan te melden bij Windows Server met het verkeerde wacht woord.

1. Selecteer op de pagina overzicht van Azure-Sentinel, onder configuraties, **Analytics**.

2. Onder configuraties selecteert u **analyse**.

3. Selecteer **+ maken** en selecteer in de vervolg keuzelijst **geplande query regel**.

4. Voer de vereiste gegevens in op het tabblad **Algemeen** .

    - Naam
    - Beschrijving
    - Tactieken
    - Severity
    - Status

    Selecteer **volgende: regel logica instellen >**.

5. Voer op het tabblad **regel logica instellen** de vereiste gegevens in.

    - Regel query (hier wordt de voorbeeld query weer gegeven)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Entiteiten toewijzen
    - Query planning
    - Waarschuwingsdrempel
    - Gebeurtenissen groeperen
    - Onderdrukking

    Selecteer **Next**.

6. Schakel op het tabblad **incident instellingen** het selectie vakje **incidenten maken van waarschuwingen die door deze Analytics regel zijn geactiveerd** in en selecteer **volgende: automatisch antwoord >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Scherm afbeelding van de wizard analytische regel voor het maken van een nieuwe regel in azure Sentinel. Toont het maken van incidenten van waarschuwingen die door deze regel worden geactiveerd als ingeschakeld.":::

7. Selecteer **volgende: controleren >**.

8. Controleer de informatie op het tabblad **controleren en maken** en selecteer **maken**.

Nadat de derde heeft geprobeerd zich aan te melden bij Windows Server, activeert de gemaakte regel een incident voor elke mislukte poging.

## <a name="view-generated-alerts"></a>Gegenereerde waarschuwingen weer geven

U kunt gegenereerde incidenten weer geven met Azure Sentinel. U kunt ook incidenten toewijzen en deze sluiten zodra ze zijn opgelost, allemaal vanuit Azure Sentinel.

1. Ga naar de pagina overzicht van Azure-Sentinel.

2. Onder Threat Management selecteert u **incidenten**.

3. Selecteer een incident. U kunt het incident vervolgens toewijzen aan een team voor oplossing.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Scherm afbeelding van de pagina met incidenten voor Azure-incidenten met een geselecteerd voorval en de optie voor het toewijzen van het incident voor oplossing.":::

    Nadat u het probleem hebt opgelost, kunt u het sluiten.

## <a name="hunt-security-threats-with-queries"></a>Beveiligings dreigingen met query's

U kunt query's maken of de beschik bare vooraf gedefinieerde query gebruiken in azure Sentinel om bedreigingen in uw omgeving te identificeren. Voer de volgende stappen uit om een vooraf gedefinieerde query uit te voeren.

1. Ga naar de pagina overzicht van Azure-Sentinel.

2. Onder Threat Management selecteert u **jacht**. Er wordt een lijst met vooraf gedefinieerde query's weer gegeven.

3. Selecteer een query en selecteer vervolgens **query uitvoeren**.

4. Selecteer **resultaten weer geven** om de resultaten te controleren.

### <a name="create-a-new-query"></a>Een nieuwe query maken

1.  Selecteer onder Threat Management de optie **jacht** en vervolgens **+ nieuwe query**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Scherm afbeelding van de Azure Sentinel-jacht pagina met + nieuwe query gemarkeerd.":::

2. Vul de volgende gegevens in om een aangepaste query te maken.

    - Naam
    - Beschrijving
    - Aangepaste query
    - Toewijzing invoeren
    - Tactieken
    
3. Selecteer **Maken**. U kunt vervolgens de gemaakte query selecteren, **Query's uitvoeren** en de **resultaten weer geven**.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt besproken hoe u uw Azure VMware-oplossing-Vm's kunt beveiligen, wilt u mogelijk meer informatie over:

- Het [Azure Defender-dash board](../security-center/azure-defender-dashboard.md)gebruiken.
- [Geavanceerde detectie van bedreigingen in azure-Sentinel](../azure-monitor/learn/quick-create-workspace.md).
- [Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen](lifecycle-management-of-azure-vmware-solution-vms.md).
