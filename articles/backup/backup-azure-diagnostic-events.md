---
title: Diagnostische instellingen voor Recovery Services kluizen gebruiken
description: In dit artikel wordt beschreven hoe u de oude en nieuwe diagnostische gebeurtenissen voor Azure Backup gebruikt.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575423"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Diagnostische instellingen voor Recovery Services kluizen gebruiken

Azure Backup verzendt diagnostische gebeurtenissen die kunnen worden verzameld en gebruikt voor analyse, waarschuwingen en rapportage.

U kunt Diagnostische instellingen voor een Recovery Services kluis configureren via de Azure Portal door naar de kluis te gaan en **Diagnostische instellingen** te selecteren. Als u de **instelling diagnostische gegevens toevoegen** selecteert, kunt u een of meer diagnostische gebeurtenissen verzenden naar een opslag account, een event hub of een log Analytics-werk ruimte.

![Het deelvenster Diagnostische instellingen](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Diagnostische gebeurtenissen die beschikbaar zijn voor gebruikers van Azure Backup

Azure Backup biedt de volgende diagnostische gebeurtenissen. Elke gebeurtenis biedt gedetailleerde gegevens over een specifieke set back-upartefacten:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Als u nog steeds de [oude gebeurtenis](#legacy-event) AzureBackupReport gebruikt, kunt u het beste overschakelen naar het gebruik van bovenstaande gebeurtenissen.

Zie voor meer informatie [gegevens model voor Azure backup diagnostische gebeurtenissen](./backup-azure-reports-data-model.md).

Gegevens voor deze gebeurtenissen kunnen worden verzonden naar een opslag account, een Log Analytics-werk ruimte of een Event Hub. Als u deze gegevens naar een Log Analytics-werk ruimte verzendt, selecteert u de optie **resource-specifiek** in het scherm **Diagnostische instellingen** . Zie de volgende secties voor meer informatie.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Diagnostische instellingen gebruiken met Log Analytics

U kunt nu Azure Backup gebruiken om diagnostische gegevens over de kluis te verzenden naar toegewezen Log Analytics tabellen voor back-up. Deze tabellen worden [resource-specifieke tabellen](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)genoemd.

De diagnostische gegevens van uw kluis naar Log Analytics verzenden:

1. Ga naar uw kluis en selecteer **Diagnostische instellingen**. Selecteer **+ Diagnostische instelling toevoegen**.
1. Geef een naam op voor de diagnostische instelling.
1. Schakel het selectie vakje **verzenden naar log Analytics** in en selecteer een log Analytics werk ruimte.
1. Selecteer **resource specifiek** in de wissel knop en selecteer de volgende zes gebeurtenissen: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage** en **AddonAzureBackupProtectedInstance**.
1. Selecteer **Opslaan**.

   ![Resource-specifieke modus](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Nadat de gegevens naar de Log Analytics-werk ruimte zijn gestroomt, worden toegewezen tabellen voor elk van deze gebeurtenissen in uw werk ruimte gemaakt. U kunt elk van deze tabellen rechtstreeks opvragen. U kunt indien nodig ook samen voegingen of samen voegingen tussen deze tabellen uitvoeren.

> [!IMPORTANT]
> De zes gebeurtenissen, te weten, CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage en AddonAzureBackupProtectedInstance, worden *alleen* ondersteund in de resource-specifieke modus in [back-uprapporten](./configure-reports.md). *Als u gegevens voor deze zes gebeurtenissen in de diagnostische modus van Azure probeert te verzenden, worden er geen gegevens weer gegeven in de back-uprapporten.*

## <a name="legacy-event"></a>Verouderde gebeurtenis

Normaal gesp roken zijn alle diagnostische gegevens over back-ups voor een kluis opgenomen in één gebeurtenis genaamd AzureBackupReport. De zes gebeurtenissen die hier worden beschreven, zijn in essentie een ontleding van alle gegevens in AzureBackupReport.

Momenteel blijven we de AzureBackupReport-gebeurtenis voor achterwaartse compatibiliteit ondersteunen in gevallen waarin gebruikers bestaande aangepaste query's voor deze gebeurtenis hebben. Voor beelden zijn aangepaste logboek waarschuwingen en aangepaste visualisaties. *We raden u aan zo snel mogelijk naar de [nieuwe gebeurtenissen](#diagnostics-events-available-for-azure-backup-users) te gaan*. De nieuwe gebeurtenissen:

* Maak de gegevens veel gemakkelijker in logboek query's te werken.
* Zorg voor betere detectie van schema's en hun structuur.
* Verbeter de prestaties in zowel opname latentie als query tijden.

*De verouderde gebeurtenis in de diagnostische modus van Azure wordt uiteindelijk afgeschaft. Het kiezen van de nieuwe gebeurtenissen kan u helpen om complexe migraties op een later tijdstip te voor komen*. Onze [rapportage oplossing](./configure-reports.md) die gebruikmaakt van log Analytics, stopt ook met het ondersteunen van gegevens van de verouderde gebeurtenis.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Stappen voor het overstappen op nieuwe diagnostische instellingen voor een Log Analytics-werk ruimte

1. Identificeer welke kluizen gegevens verzenden naar de Log Analytics-werk ruimten met behulp van de verouderde gebeurtenis en de abonnementen waarvan ze deel uitmaken. Voer de volgende query uit in elk van uw werk ruimten om deze kluizen en abonnementen te identificeren.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    Hieronder ziet u een scherm afbeelding van de query die wordt uitgevoerd in een van de werk ruimten:

    ![Werkruimte query](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Gebruik de [ingebouwde Azure Policy definities](./azure-policy-configure-diagnostics.md) in azure backup om een nieuwe diagnostische instelling toe te voegen voor alle kluizen in een opgegeven bereik. Dit beleid voegt een nieuwe diagnostische instelling toe aan kluizen die geen diagnostische instelling hebben of alleen een instelling voor verouderde diagnostische gegevens hebben. Dit beleid kan worden toegewezen aan een volledig abonnement of resource groep. U moet een eigenaar hebben van de toegang tot elk abonnement waarvoor het beleid is toegewezen.

U kunt ervoor kiezen om afzonderlijke diagnostische instellingen voor AzureBackupReport en de zes nieuwe gebeurtenissen te hebben totdat u al uw aangepaste query's hebt gemigreerd om gegevens uit de nieuwe tabellen te gebruiken. In de volgende afbeelding ziet u een voor beeld van een kluis met twee Diagnostische instellingen. Met de eerste instelling, met de naam **Setting1**, worden gegevens van een AzureBackupReport-gebeurtenis verzonden naar een log Analytics-werk ruimte in de diagnostische modus van Azure. Met de tweede instelling, met de naam **Setting2**, worden gegevens van de zes nieuwe Azure backup gebeurtenissen naar een log Analytics werk ruimte in de resource-specifieke modus verzonden.

![Twee instellingen](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> De gebeurtenis AzureBackupReport wordt *alleen* ondersteund in de diagnostische modus van Azure. *Als u gegevens voor deze gebeurtenis wilt verzenden in de resource-specifieke modus, worden er geen gegevens naar de Log Analytics werk ruimte verzonden.*

> [!NOTE]
> De wissel knop voor **Azure Diagnostics** of **resource specific** wordt alleen weer gegeven als de gebruiker **Send to log Analytics** selecteert. Als u gegevens naar een opslag account of een Event Hub wilt verzenden, selecteert de gebruiker de vereiste bestemming en selecteert de selectie vakjes voor elk van de gewenste gebeurtenissen, zonder extra invoer. Het is ook raadzaam om de verouderde gebeurtenis AzureBackupReport niet te kiezen.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Azure Site Recovery gebeurtenissen verzenden naar Log Analytics

Azure Backup-en Azure Site Recovery-gebeurtenissen worden vanaf dezelfde Recovery Services kluis verzonden. Azure Site Recovery is momenteel niet beschikbaar voor resource-specifieke tabellen. Gebruikers die Azure Site Recovery gebeurtenissen naar Log Analytics willen verzenden, zijn *alleen bedoeld* voor het gebruik van de diagnostische modus van Azure, zoals wordt weer gegeven in de afbeelding. *Als u de resource-specifieke modus voor Azure site Recovery gebeurtenissen kiest, voor komt u dat de vereiste gegevens worden verzonden naar de log Analytics-werk ruimte*.

![Site Recovery gebeurtenissen](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Samenvatting:

* Als u al Log Analytics diagnostische gegevens hebt ingesteld met Azure Diagnostics en aangepaste query's bovenop deze hebt geschreven, houdt u die instelling *intact* totdat u uw query's migreert voor het gebruik van gegevens uit de nieuwe gebeurtenissen.
* Als u ook op nieuwe tabellen wilt worden geadviseerd, maakt u een **nieuwe** diagnostische instelling, selecteert u **resource specifiek** en selecteert u de zes nieuwe gebeurtenissen.
* Als u momenteel Azure Site Recovery gebeurtenissen verzendt naar Log Analytics, moet u de resource-specifieke modus voor deze gebeurtenissen *niet* kiezen. Als dat niet het geval is, worden de gegevens voor deze gebeurtenissen niet in uw Log Analytics-werk ruimte geplaatst. Maak in plaats daarvan een extra diagnostische instelling, selecteer **Azure Diagnostics** en selecteer de relevante Azure site Recovery gebeurtenissen.

In de volgende afbeelding ziet u een voor beeld van een gebruiker die drie Diagnostische instellingen voor een kluis heeft. Met de eerste instelling, met de naam **Setting1**, worden gegevens van een AzureBackupReport-gebeurtenis verzonden naar een log Analytics-werk ruimte in de diagnostische modus van Azure. Met de tweede instelling, met de naam **Setting2**, worden gegevens van de zes nieuwe Azure backup gebeurtenissen naar een log Analytics werk ruimte in de resource-specifieke modus verzonden. Met de derde instelling, met de naam **Setting3**, worden gegevens van de Azure site Recovery gebeurtenissen verzonden naar een log Analytics-werk ruimte in de Azure Diagnostics-modus.

![Drie instellingen](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het Log Analytics gegevens model voor de diagnostische gebeurtenissen](./backup-azure-reports-data-model.md)
