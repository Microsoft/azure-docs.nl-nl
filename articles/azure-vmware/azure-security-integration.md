---
title: Uw virtuele Azure VMware Solution beveiligen met Azure Security Center-integratie
description: Beveilig uw Azure VMware Solution-VM's met de eigen beveiligingshulpprogramma's van Azure via Azure Security Center dashboard.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: d04f0ac3e3934442ce5b6d5fbf4b53e18b3dff18
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877512"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Uw virtuele Azure VMware Solution beveiligen met Azure Security Center-integratie

Systeemeigen beveiligingshulpprogramma's van Azure bieden beveiliging voor een hybride omgeving van Azure, Azure VMware Solution en on-premises virtuele machines (VM's). In dit artikel wordt beschreven hoe u Azure-hulpprogramma's in kunt stellen voor de beveiliging van hybride omgevingen. U gebruikt deze hulpprogramma's om verschillende bedreigingen te identificeren en aan te pakken.

## <a name="azure-native-services"></a>Native Azure-services

Hier volgt een kort overzicht van de native Azure-services:

- **Log Analytics-werkruimte:** Log Analytics-werkruimte is een unieke omgeving voor het opslaan van logboekgegevens. Elke werkruimte heeft een eigen gegevensopslagplaats en configuratie. Gegevensbronnen en oplossingen worden geconfigureerd om hun gegevens in een specifieke werkruimte op te slaan.
- **Azure Security Center:** Azure Security Center is een geïntegreerd beveiligingsbeheersysteem voor infrastructuur. Het versterkt de beveiliging van datacenters en biedt geavanceerde beveiliging tegen bedreigingen voor hybride workloads in de cloud of on-premises.
- **Azure Sentinel:** Azure Sentinel is een cloudeigen SIEM-oplossing (Security Information Event Management). Het biedt beveiligingsanalyses, waarschuwingsdetectie en geautomatiseerd reageren op bedreigingen in een omgeving.

## <a name="topology"></a>Topologie

![Een diagram met de architectuur van geïntegreerde Azure-beveiliging.](media/azure-security-integration/azure-integrated-security-architecture.png)

Met de Log Analytics-agent kunt u logboekgegevens verzamelen uit Azure, Azure VMware Solution en on-premises VM's. De logboekgegevens worden verzonden naar Azure Monitor logboeken en worden opgeslagen in een Log Analytics-werkruimte. U kunt de Log Analytics-agent implementeren met ondersteuning voor VM-extensies voor servers met Arc voor nieuwe en bestaande [VM's.](../azure-arc/servers/manage-vm-extensions.md) 

Zodra de logboeken zijn verzameld door de Log Analytics-werkruimte, kunt u de Log Analytics-werkruimte configureren met Azure Security Center. Azure Security Center evalueert de beveiligingsstatus van de virtuele Azure VMware Solution en geeft een waarschuwing voor een kritiek beveiligingsprobleem. Er worden bijvoorbeeld ontbrekende besturingssysteempatches, onjuiste beveiligingsconfiguraties en [endpoint protection beoordeeld.](../security-center/security-center-services.md)

U kunt de Log Analytics-werkruimte configureren met Azure Sentinel voor detectie van waarschuwingen, zichtbaarheid van bedreigingen, opsporing en bedreigingsrespons. In het voorgaande diagram is Azure Security Center verbonden met Azure Sentinel met behulp Azure Security Center connector. Azure Security Center het beveiligingsprobleem in de omgeving doorsturen naar Azure Sentinel incident te maken en toe te kennen aan andere bedreigingen. U kunt ook de geplande regelsquery maken om ongewenste activiteit te detecteren en deze te converteren naar de incidenten.

## <a name="benefits"></a>Voordelen

- Systeemeigen Azure-services kunnen worden gebruikt voor de beveiliging van hybride omgevingen in Azure, Azure VMware Solution en on-premises services.
- Met behulp van een Log Analytics-werkruimte kunt u de gegevens of logboeken tot één punt verzamelen en dezelfde gegevens presenteren aan verschillende native Azure-services.
- Azure Security Center biedt veel functies, waaronder:
    - Bestandsintegriteit controleren
    - Detectie van bestandsloze aanvallen
    - Evaluatie van besturingssysteempatch 
    - Evaluatie van onjuiste beveiligingsconfiguratie
    - Evaluatie van eindpuntbeveiliging
- Azure Sentinel kunt u het volgende doen:
    - Verzamel gegevens op cloudschaal voor alle gebruikers, apparaten, toepassingen en infrastructuur, zowel on-premises als in meerdere clouds.
    - Detecteer eerder niet-gedetecteerde bedreigingen.
    - Onderzoek bedreigingen met kunstmatige intelligentie en zoek naar verdachte activiteiten op schaal.
    - Reageer snel op incidenten met ingebouwde indeling en automatisering van algemene taken.

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

U hebt een Log Analytics-werkruimte nodig om gegevens uit verschillende bronnen te verzamelen. Zie Een Log Analytics-werkruimte maken op [basis van de Azure Portal.](../azure-monitor/logs/quick-create-workspace.md) 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Virtuele Security Center implementeren en Azure VMware Solution configureren

Azure Security Center is een vooraf geconfigureerd hulpprogramma waarvoor geen implementatie is vereist. Zoek in Azure Portal naar **Security Center** selecteer deze optie.

### <a name="enable-azure-defender"></a>Azure Defender inschakelen

Azure Defender breidt Azure Security Center geavanceerde beveiliging tegen bedreigingen uit voor uw hybride workloads, zowel on-premises als in de cloud. Als u uw virtuele Azure VMware Solution wilt beveiligen, moet u deze inschakelen Azure Defender. 

1. Selecteer Security Center de optie **Aan de slag.**

2. Selecteer het **tabblad Upgraden** en selecteer vervolgens uw abonnement of werkruimte. 

3. Selecteer **Upgrade** om Azure Defender in te schakelen.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Virtuele Azure VMware Solution toevoegen aan Security Center

1. Zoek in Azure Portal op **Azure Arc** selecteer deze optie.

2. Selecteer onder Resources de **optie Servers** en vervolgens **+Toevoegen.**

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Een schermopname van Azure Arc servers voor het toevoegen van een Azure VMware Solution-VM aan Azure.":::

3. Selecteer **Script genereren.**
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Een schermopname van Azure Arc met de optie voor het toevoegen van een server met behulp van een interactief script."::: 
 
4. Selecteer op **het tabblad** Vereisten de optie **Volgende.**

5. Vul op **het tabblad Resourcedetails** de volgende gegevens in: 
    - Abonnement
    - Resourcegroep
    - Region 
    - Besturingssysteem
    - Proxyserverdetails
    
    Selecteer vervolgens **Volgende: Tags**.

6. Selecteer op **het** tabblad Tags de optie **Volgende.**

7. Selecteer downloaden **op het tabblad Script downloaden** en **uitvoeren.**

8. Geef uw besturingssysteem op en voer het script uit op Azure VMware Solution VM.

## <a name="view-recommendations-and-passed-assessments"></a>Aanbevelingen en doorgegeven evaluaties weergeven

1. Selecteer Azure Security Center in het **linkerdeelvenster** inventaris.

2. Bij Resourcetype selecteert **u Servers - Azure Arc.**
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Een schermopname van de pagina Azure Security Center met Servers - Azure Arc geselecteerd onder Resourcetype.":::

3. Selecteer de naam van uw resource. Er wordt een pagina geopend met de beveiligingsgegevens van uw resource.

4. Selecteer **onder Aanbevelingslijst** de **tabbladen Aanbevelingen,** Doorgegeven **evaluaties** en Niet-beschikbare **evaluaties** om deze details weer te geven.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Een schermopname van Azure Security Center met beveiligingsaanbevelingen en -evaluaties.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Een werkruimte Azure Sentinel implementeren

Azure Sentinel is gebaseerd op een Log Analytics-werkruimte. Uw eerste stap bij het onboarden Azure Sentinel is het selecteren van de Log Analytics-werkruimte die u voor dat doel wilt gebruiken.

1. Zoek in Azure Portal naar **Azure Sentinel** en selecteer deze.

2. Selecteer op Azure Sentinel pagina werkruimten **+ Toevoegen.**

3. Selecteer de Log Analytics-werkruimte en selecteer **Toevoegen.**

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Gegevensverzamelaar inschakelen voor beveiligingsgebeurtenissen op Azure VMware Solution-VM's

U bent nu klaar om verbinding te Azure Sentinel met uw gegevensbronnen, in dit geval beveiligingsgebeurtenissen.

1. Selecteer op Azure Sentinel pagina werkruimten de geconfigureerde werkruimte.

2. Selecteer **gegevensconnectoren onder Configuratie.**

3. Selecteer in de kolom Connectornaam de **optie Beveiligingsgebeurtenissen** in de lijst en selecteer **vervolgens Connectorpagina openen.**

4. Selecteer op de connectorpagina de gebeurtenissen die u wilt streamen en selecteer **vervolgens Wijzigingen toepassen.**

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Schermopname van de pagina Beveiligingsgebeurtenissen in Azure Sentinel waar u kunt selecteren welke gebeurtenissen u wilt streamen.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Verbinding Azure Sentinel met Azure Security Center  

1. Selecteer op Azure Sentinel werkruimtepagina de geconfigureerde werkruimte.

2. Selecteer **gegevensconnectoren onder Configuratie.**

3. Selecteer **Azure Security Center** in de lijst en selecteer vervolgens **Connectorpagina openen.**

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Schermopname van de pagina Gegevensconnectoren in Azure Sentinel met de selectie om verbinding te Azure Security Center met Azure Sentinel.":::

4. Selecteer **Verbinding maken** om verbinding te Azure Security Center met Azure Sentinel.

5. Schakel **Incident maken in** om een incident te genereren voor Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Regels maken om beveiligingsrisico's te identificeren

Nadat u gegevensbronnen hebt verbonden met Azure Sentinel, kunt u regels maken om waarschuwingen te genereren voor gedetecteerde bedreigingen. In het volgende voorbeeld maken we een regel voor pogingen om aan te melden bij Windows Server met het verkeerde wachtwoord.

1. Selecteer op Azure Sentinel overzichtspagina onder Configuraties de optie **Analyse.**

2. Selecteer onder Configuraties de optie **Analyse.**

3. Selecteer **+Maken** en selecteer geplande **queryregel in de vervolgkeuzekeuze.**

4. Voer op **het** tabblad Algemeen de vereiste gegevens in.

    - Naam
    - Beschrijving
    - Tactieken
    - Severity
    - Status

    Selecteer **Volgende: Regellogica instellen >.**

5. Voer op **het tabblad Regellogica** instellen de vereiste gegevens in.

    - Regelquery (hier wordt onze voorbeeldquery weergegeven)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Entiteiten toewijzen
    - Queryplanning
    - Waarschuwingsdrempel
    - Gebeurtenissen groeperen
    - Onderdrukking

    Selecteer **Next**.

6. Schakel op **het tabblad Incidentinstellingen** De optie Incidenten maken op basis van waarschuwingen die worden geactiveerd door deze **analyseregel** in en selecteer **Volgende: Automatisch antwoord >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Schermopname van de wizard Analyseregel voor het maken van een nieuwe regel in Azure Sentinel. Toont Create incidents from alerts triggered by this rule as enabled.":::

7. Selecteer **Volgende: Controleer >.**

8. Controleer de **informatie op het** tabblad Controleren en maken en selecteer **Maken.**

Na de derde mislukte poging om u aan te melden bij Windows Server, activeert de gemaakte regel een incident voor elke mislukte poging.

## <a name="view-alerts"></a>Waarschuwingen weergeven

U kunt gegenereerde incidenten weergeven met Azure Sentinel. U kunt incidenten ook toewijzen en sluiten zodra ze zijn opgelost, allemaal vanuit Azure Sentinel.

1. Ga naar de Azure Sentinel overzichtspagina.

2. Selecteer incidenten onder **Bedreigingsbeheer.**

3. Selecteer een incident. U kunt het incident vervolgens toewijzen aan een team om het op te lossen.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Schermopname van Azure Sentinel incidentenpagina met incident geselecteerd en optie om het incident toe te wijzen voor oplossing.":::

    Nadat het probleem is opgelost, kunt u het sluiten.

## <a name="hunt-security-threats-with-queries"></a>Beveiligingsrisico's opzoeken met query's

U kunt query's maken of de beschikbare vooraf gedefinieerde query gebruiken in Azure Sentinel bedreigingen in uw omgeving te identificeren. Met de volgende stappen wordt een vooraf gedefinieerde query uitgevoerd.

1. Ga naar de Azure Sentinel overzichtspagina.

2. Selecteer onder Bedreigingsbeheer de optie **Hunting**. Er wordt een lijst met vooraf gedefinieerde query's weergegeven.

3. Selecteer een query en selecteer vervolgens **Query uitvoeren.**

4. Selecteer **Resultaten weergeven om** de resultaten te controleren.

### <a name="create-a-new-query"></a>Een nieuwe query maken

1.  Selecteer onder Bedreigingsbeheer **de optie Hunting** en vervolgens **+New Query**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Schermopname van Azure Sentinel pagina Hunting met + New Query gemarkeerd.":::

2. Vul de volgende gegevens in om een aangepaste query te maken.

    - Naam
    - Beschrijving
    - Aangepaste query
    - Toewijzing invoeren
    - Tactieken
    
3. Selecteer **Maken**. Vervolgens kunt u de gemaakte query, **Query uitvoeren** en Resultaten **weergeven selecteren.**

## <a name="next-steps"></a>Volgende stappen

Nu u hebt behandeld hoe u uw virtuele Azure VMware Solution kunt beveiligen, kunt u het volgende leren:

- Het dashboard [Azure Defender gebruiken](../security-center/azure-defender-dashboard.md)
- [Geavanceerde detectie van aanvallen met meerdere fases in Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md)
- [Levenscyclusbeheer van Azure VMware Solution-VM's](lifecycle-management-of-azure-vmware-solution-vms.md)
