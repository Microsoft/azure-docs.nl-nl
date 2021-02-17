---
title: Azure Monitor op schaal implementeren met behulp van Azure Policy
description: Implementeer Azure Monitor-functies op schaal met behulp van Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: f06ed85e362f15e36e030cd11639d9d17348e938
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573618"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Azure Monitor op schaal implementeren met behulp van Azure Policy
Hoewel sommige Azure Monitor functies eenmaal of een beperkt aantal keren worden geconfigureerd, moeten andere worden herhaald voor elke resource die u wilt bewaken. In dit artikel worden methoden beschreven voor het gebruik van Azure Policy om Azure Monitor op schaal te implementeren om ervoor te zorgen dat de bewaking consistent en correct is geconfigureerd voor al uw Azure-resources.

U moet bijvoorbeeld een diagnostische instelling maken voor al uw bestaande Azure-resources en voor elke nieuwe resource die u maakt. U moet ook een agent hebben geïnstalleerd en geconfigureerd telkens wanneer u een virtuele machine maakt. U kunt methoden zoals Power shell of CLI gebruiken om deze acties uit te voeren, omdat deze methoden beschikbaar zijn voor alle functies van Azure Monitor. Met Azure Policy kunt u logica hebben die automatisch de juiste configuratie uitvoert, telkens wanneer u een resource maakt of wijzigt.


## <a name="azure-policy"></a>Azure Policy
In deze sectie vindt u een korte inleiding op [Azure Policy](../governance/policy/overview.md) waarmee u de organisatie standaarden in uw hele Azure-abonnement of-beheer groep kunt beoordelen en afdwingen met minimale inspanning. Raadpleeg de [documentatie van Azure Policy](../governance/policy/overview.md) voor volledige informatie.

Met Azure Policy kunt u configuratie vereisten opgeven voor alle resources die worden gemaakt en resources identificeren die niet voldoen aan het beleid, blok keren dat de resources worden gemaakt of de vereiste configuratie toevoegen. Het onderscheppen van aanroepen voor het maken van een nieuwe resource of het wijzigen van een bestaande resource werkt. Het kan worden gereageerd als de aanvraag wordt geweigerd als deze niet overeenkomt met de verwachte eigenschappen in een beleids definitie, het markeren voor niet-naleving of het implementeren van een gerelateerde bron. U kunt bestaande bronnen herstellen met een **deployIfNotExists** of een beleids definitie **wijzigen** .

Azure Policy bestaat uit de objecten in de volgende tabel. Zie [Azure Policy objecten](../governance/policy/overview.md#azure-policy-objects) voor een gedetailleerde beschrijving van elk.

| Item | Beschrijving |
|:---|:---|
| Beleidsdefinitie | Beschrijft de nalevings voorwaarden van de resource en het effect dat moet worden genomen als aan een voor waarde wordt voldaan. Dit kan alle resources van een bepaald type zijn of alleen resources die overeenkomen met bepaalde eigenschappen. Het gevolg kan zijn dat u de resource wilt markeren voor naleving of een gerelateerde resource wilt implementeren. Beleids definities worden geschreven met behulp van JSON zoals beschreven in [Azure Policy definitie structuur](../governance/policy/concepts/definition-structure.md). Effecten worden beschreven in [inzicht Azure Policy effecten](../governance/policy/concepts/effects.md).
| Beleids initiatief | Een groep beleids definities die samen moeten worden toegepast. U kunt bijvoorbeeld een beleids definitie hebben om bron logboeken te verzenden naar een Log Analytics-werk ruimte en een andere om bron logboeken te verzenden naar Event hubs. Maak een initiatief dat beide beleids definities bevat en pas het initiatief toe op resources in plaats van de afzonderlijke beleids definities. Initiatieven worden geschreven met behulp van JSON zoals beschreven in [Azure Policy Initiative-structuur](../governance/policy/concepts/initiative-definition-structure.md). |
| Toewijzing | Een beleids definitie of-initiatief wordt pas van kracht nadat het is toegewezen aan een bereik. U kunt bijvoorbeeld een beleid toewijzen aan een resource groep om dit toe te passen op alle resources die zijn gemaakt in die resource, of dit Toep assen op een abonnement om het toe te passen op alle resources in dat abonnement.  Zie [Azure Policy toewijzings structuur](../governance/policy/concepts/assignment-structure.md)voor meer informatie. |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Ingebouwde beleidsdefinities voor Azure Monitor
Azure Policy bevat verschillende vooraf gedefinieerde definities die betrekking hebben op Azure Monitor. U kunt deze beleids definities toewijzen aan uw bestaande abonnement of ze gebruiken als basis voor het maken van uw eigen aangepaste definities. Zie [Azure Policy ingebouwde beleids definities voor Azure monitor](.//policy-reference.md)voor een volledige lijst van de ingebouwde politiek in de categorie **bewaking** .

Als u de ingebouwde beleids definities wilt weer geven die betrekking hebben op bewaking, voert u de volgende handelingen uit:

1. Ga naar **Azure Policy** in het Azure Portal.
2. Selecteer **Definities**.
3. Selecteer bij **type** de optie *ingebouwd* en selecteer bij **categorie** de optie *bewaking*.

  ![Scherm afbeelding van de pagina met Azure Policy definities in Azure Portal een lijst met beleids definities voor de categorie bewaking en het ingebouwde type weer gegeven.](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>Diagnostische instellingen
[Diagnostische instellingen](essentials/diagnostic-settings.md) verzamelen resource logboeken en metrische gegevens van Azure-resources op meerdere locaties, meestal naar een log Analytics-werk ruimte, waarmee u de gegevens kunt analyseren met [logboek query's](logs/log-query-overview.md) en [logboek waarschuwingen](alerts/alerts-log.md). Gebruik beleid om automatisch een diagnostische instelling te maken telkens wanneer u een resource maakt.

Elk Azure-resource type heeft een unieke set categorieën die moeten worden weer gegeven in de diagnostische instelling. Daarom vereist elk resource type een afzonderlijke beleids definitie. Sommige resource typen hebben ingebouwde beleids definities die u zonder wijzigingen kunt toewijzen. Voor andere resource typen moet u een aangepaste definitie maken.

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Ingebouwde beleidsdefinities voor Azure Monitor
Er zijn twee ingebouwde beleids definities voor elk resource type, een om te verzenden naar Log Analytics werk ruimte en een andere naar Event hub. Als u slechts één locatie nodig hebt, wijst u dat beleid toe voor het bron type. Als u beide nodig hebt, wijst u beide beleids definities toe voor de resource.

De volgende afbeelding toont bijvoorbeeld de ingebouwde beleids definities voor Diagnostische instellingen voor Data Lake Analytics.

  ![Gedeeltelijke scherm opname van de pagina met Azure Policy definities met twee ingebouwde beleids definities voor Diagnostische instellingen voor Data Lake Analytics.](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Aangepaste beleids definities
Voor bron typen die geen ingebouwd beleid hebben, moet u een aangepaste beleids definitie maken. U kunt dit hand matig doen in het Azure Portal door een bestaand ingebouwd beleid te kopiëren en vervolgens te wijzigen voor uw resource type. Het is efficiënter om het beleid te maken met behulp van een script in de PowerShell Gallery.

De script [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) maakt beleids bestanden voor een bepaald bron type dat u kunt installeren met behulp van Power shell of cli. Gebruik de volgende procedure om een aangepaste beleids definitie te maken voor Diagnostische instellingen.


1. Zorg ervoor dat [Azure PowerShell](/powershell/azure/install-az-ps) is geïnstalleerd.
2. Installeer het script met de volgende opdracht:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Voer het script uit met de para meters om op te geven waarnaar de logboeken moeten worden verzonden. U wordt gevraagd om een abonnement en een resource type op te geven. Als u bijvoorbeeld een beleids definitie wilt maken die naar Log Analytics werk ruimte en Event hub verzendt, gebruikt u de volgende opdracht.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. U kunt ook een abonnement en een resource type opgeven in de opdracht. Gebruik bijvoorbeeld de volgende opdracht om een beleids definitie te maken die wordt verzonden naar Log Analytics werk ruimte en Event hub voor Azure SQL Server-data bases.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. Het script maakt afzonderlijke mappen voor elke beleids definitie, die elk drie bestanden bevat met de naam azurepolicy.jsop, azurepolicy.rules.jsop, azurepolicy.parameters.jsop. Als u het beleid hand matig wilt maken in de Azure Portal, kunt u de inhoud van azurepolicy.jskopiëren en plakken omdat het de volledige beleids definitie bevat. Gebruik de andere twee bestanden met Power shell of CLI om de beleids definitie te maken op basis van een opdracht regel.

    In de volgende voor beelden ziet u hoe u de beleids definitie kunt installeren vanuit Power shell en CLI. Elk bevat meta gegevens om een **bewakings** categorie op te geven voor het groeperen van de nieuwe beleids definitie met de ingebouwde beleids definities.

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>Initiatieven
In plaats van een toewijzing voor elke beleids definitie te maken, is een algemene strategie een initiatief te maken dat de beleids definities bevat voor het maken van diagnostische instellingen voor elke Azure-service. Maak een toewijzing tussen het initiatief en een beheer groep, abonnement of resource groep, afhankelijk van hoe u uw omgeving beheert. Deze strategie biedt de volgende voor delen:

- Maak een enkele toewijzing voor het initiatief in plaats van meerdere toewijzingen voor elk resource type. Gebruik hetzelfde initiatief voor meerdere bewakings groepen, abonnementen of resource groepen.
- Wijzig het initiatief wanneer u een nieuw resource type of doel wilt toevoegen. Uw eerste vereisten zijn bijvoorbeeld het verzenden van gegevens alleen naar een Log Analytics-werk ruimte, maar u kunt later ook Event hub toevoegen. Wijzig het initiatief in plaats van nieuwe toewijzingen te maken.

Zie [een initiatief definitie maken en toewijzen](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition) voor meer informatie over het maken van een initiatief. Houd rekening met de volgende aanbevelingen:

- Stel de **categorie** in op **bewaking** om deze te groeperen met gerelateerde ingebouwde en aangepaste beleids definities.
- In plaats van de gegevens op te geven voor de Log Analytics-werk ruimte en de Event hub voor beleids definities die zijn opgenomen in het initiatief, gebruikt u een para meter common Initiative. Zo kunt u eenvoudig een algemene waarde voor alle beleids definities opgeven en die waarde zo nodig wijzigen.

![Initiatiefdefinitie](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>Toewijzing 
Wijs het initiatief toe aan een Azure-beheer groep,-abonnement of-resource groep, afhankelijk van het bereik van uw resources om te bewaken. Een [beheer groep](../governance/management-groups/overview.md) is met name handig voor het bereik beleid, met name als uw organisatie meerdere abonnementen heeft.

![Scherm afbeelding van de instellingen voor het tabblad basis beginselen in het gedeelte Assign toewijzen van de diagnostische instellingen in Log Analytics werk ruimte in Azure Portal.](media/deploy-scale/initiative-assignment.png)

Door initiatief parameters te gebruiken, kunt u de werk ruimte of enige andere details één keer opgeven voor alle beleids definities in het initiatief. 

![Initiatiefparameters](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Herstel
Het initiatief wordt toegepast op elke virtuele machine wanneer deze wordt gemaakt. Met een [herstel taak](../governance/policy/how-to/remediate-resources.md) worden de beleids definities in het initiatief voor bestaande resources geïmplementeerd. Hierdoor kunt u Diagnostische instellingen maken voor alle resources die al zijn gemaakt. Wanneer u de toewijzing maakt met behulp van de Azure Portal, hebt u de mogelijkheid om op hetzelfde moment een herstel taak te maken. Zie [niet-compatibele resources herstellen met Azure Policy](../governance/policy/how-to/remediate-resources.md) voor meer informatie over het herstel.

![Initiatief herstellen](media/deploy-scale/initiative-remediation.png)


## <a name="azure-monitor-for-vms"></a>Azure Monitor voor virtuele machines
[Azure monitor voor VM's](vm/vminsights-overview.md) is het primaire hulp programma in azure monitor voor het bewaken van virtuele machines. Als u Azure Monitor voor VM's inschakelt, worden zowel de Log Analytics agent als de afhankelijkheids agent geïnstalleerd. In plaats van deze taken hand matig uit te voeren, gebruikt u Azure Policy om ervoor te zorgen dat elke virtuele machine is geconfigureerd terwijl u deze maakt.

> [!NOTE]
> Azure Monitor voor VM's bevat een functie met de naam **Azure monitor voor VM's-beleids dekking** waarmee niet-compatibele virtuele machines in uw omgeving kunnen worden gedetecteerd en hersteld. U kunt deze functie gebruiken in plaats van rechtstreeks te werken met Azure Policy voor Azure-Vm's en voor hybride virtuele machines die zijn verbonden met Azure Arc. Voor virtuele-machine schaal sets van Azure moet u de toewijzing maken met behulp van Azure Policy.
 

Azure Monitor voor VM's bevat de volgende ingebouwde initiatieven waarmee beide agents worden geïnstalleerd om volledige controle mogelijk te maken. 

|Naam |Beschrijving |
|:---|:---|
|Azure Monitor voor VM's inschakelen | Installeert de Log Analytics agent en de afhankelijkheids agent op virtuele Azure-machines en hybride Vm's die zijn verbonden met Azure Arc. |
|Azure Monitor inschakelen voor schaal sets voor virtuele machines | Hiermee worden de Log Analytics agent en de afhankelijkheids agent geïnstalleerd op de virtuele-machine schaalset van Azure. |


### <a name="virtual-machines"></a>Virtuele machines
In plaats van toewijzingen voor deze initiatieven te maken met behulp van de Azure Policy-interface, bevat Azure Monitor voor VM's een functie waarmee u het aantal virtuele machines in elk bereik kunt controleren om te bepalen of het initiatief is toegepast. U kunt vervolgens de werk ruimte configureren en de vereiste toewijzingen maken met deze interface.

Zie [Azure monitor voor VM's inschakelen met behulp van Azure Policy](./vm/vminsights-enable-policy.md)voor meer informatie over dit proces.

![Azure Monitor voor VM's beleid](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets
Als u Azure Policy wilt gebruiken om controle in te scha kelen voor schaal sets voor virtuele machines, wijst u het initiatief **Azure monitor voor Virtual Machine Scale sets inschakelen** toe aan een Azure-beheer groep,-abonnement of-resource groep, afhankelijk van het bereik van uw resources om te bewaken. Een [beheer groep](../governance/management-groups/overview.md) is met name handig voor het bereik beleid, met name als uw organisatie meerdere abonnementen heeft.

![Scherm afbeelding van de pagina Assign initiatief in Azure Portal. De initiatief definitie is ingesteld om Azure Monitor in te scha kelen voor schaal sets voor virtuele machines.](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

Selecteer de werk ruimte waarnaar de gegevens worden verzonden. Voor deze werk ruimte moet de *VMInsights* -oplossing zijn geïnstalleerd, zoals beschreven in [log Analytics werk ruimte configureren voor Azure monitor voor VM's](vm/vminsights-configure-workspace.md).

![Werkruimte selecteren](media/deploy-scale/virtual-machine-scale-set-workspace.png)

Maak een herstel taak als u een bestaande schaalset voor virtuele machines hebt waaraan dit beleid moet worden toegewezen.

![Hersteltaak](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Log Analytics-agent
Mogelijk hebt u scenario's waarin u de Log Analytics-agent wilt installeren, maar niet de afhankelijkheids agent. Er is geen ingebouwd initiatief voor de agent, maar u kunt uw eigen voor deel maken op basis van de ingebouwde beleids definities van Azure Monitor voor VM's.

> [!NOTE]
> Er is geen reden om de afhankelijke agent zelf te implementeren, omdat de Log Analytics-agent vereist is om de bijbehorende gegevens te leveren aan Azure Monitor.


|Naam |Beschrijving |
|-----|------------|
|Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert Vm's als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |
|Log Analytics-agent voor Linux-VM’s implementeren |Implementeer Log Analytics-agent voor Linux-Vm's als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |
|Log Analytics-agent voor Windows-VM's implementeren |Log Analytics-agent voor Windows-Vm's implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |
| [Preview-versie]: Log Analytics-agent moet worden geïnstalleerd op uw Linux Azure Arc-machines |Rapporteert hybride Azure Arc-machines als niet-compatibel voor Linux-Vm's als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |
| [Preview-versie]: Log Analytics-agent moet worden geïnstalleerd op uw Windows Azure-Arc-machines |Rapporteert hybride Azure Arc-machines als niet-compatibel voor Windows-Vm's als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |
| [Preview-versie]: Log Analytics-agent implementeren op Linux Azure Arc-machines |Implementeer Log Analytics agent voor Linux Hybrid Azure Arc machines als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |
| [Preview-versie]: Log Analytics agent implementeren op Windows Azure Arc-machines |Log Analytics-agent voor Windows Hybrid Azure Arc-machines implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |
|Implementatie van afhankelijkheids agent in virtuele-machine schaal sets controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert de schaalset voor virtuele machines als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |
|Implementatie van Log Analytics agent in virtuele-machine schaal sets controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert de schaalset voor virtuele machines als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |
|Log Analytics-agent implementeren voor virtuele-machineschaalsets van Linux |Implementeer Log Analytics agent voor virtuele Linux-machine schaal sets als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |
|Log Analytics-agent implementeren voor virtuele-machineschaalsets van Windows |Implementeer Log Analytics agent voor virtuele-machine schaal sets van Windows als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |


## <a name="next-steps"></a>Volgende stappen

- Lees meer over [Azure Policy](../governance/policy/overview.md).
- Meer informatie over [Diagnostische instellingen](essentials/diagnostic-settings.md).