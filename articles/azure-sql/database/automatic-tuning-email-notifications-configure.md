---
title: Instructies voor het automatisch afstemmen van e-mail meldingen
description: Schakel e-mail meldingen in voor Azure SQL Database automatisch afstemmen van query's.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500951"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-mail meldingen voor automatisch afstemmen
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL Database afstemmings aanbevelingen worden gegenereerd door Azure SQL Database [automatisch afstemmen](automatic-tuning-overview.md). Deze oplossing bewaakt en analyseert voortdurend werk belastingen van data bases die aangepaste aanpassings aanbevelingen bieden voor elke afzonderlijke Data Base met betrekking tot het maken van indexen, het verwijderen van indexen en het optimaliseren van plannen voor het uitvoeren van query's.

Azure SQL Database automatische afstemmings aanbevelingen kunnen worden weer gegeven in de [Azure Portal](database-advisor-find-recommendations-portal.md), opgehaald met [rest API](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) -aanroepen of met behulp van [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) -en [Power shell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) -opdrachten. Dit artikel is gebaseerd op het gebruik van een Power shell-script om aanbevelingen voor automatische afstemming op te halen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>E-mail meldingen voor automatische afstemmings aanbevelingen automatiseren

Met de volgende oplossing wordt het verzenden van e-mail meldingen met aanbevelingen voor automatisch afstemmen geautomatiseerd. De oplossing die wordt beschreven, bestaat uit het automatiseren van de uitvoering van een Power shell-script voor het ophalen van afstemmings aanbevelingen met behulp van [Azure Automation](../../automation/automation-intro.md)en de automatisering van het plannen van e-mail met behulp van [Microsoft flow](https://flow.microsoft.com)

## <a name="create-azure-automation-account"></a>Azure Automation account maken

Als u Azure Automation wilt gebruiken, is de eerste stap het maken van een Automation-account en het configureren van Azure-resources voor het uitvoeren van het Power shell-script. Zie aan de slag [met Azure Automation](../../automation/index.yml)voor meer informatie over Azure Automation en de mogelijkheden ervan.

Volg deze stappen om een Azure Automation-account te maken via de methode voor het selecteren en configureren van een Automation-app vanuit Azure Marketplace:

1. Meld u aan bij de Azure-portal.
1. Klik in de linkerbovenhoek op '**+ een resource maken**'.
1. Zoek naar '**Automation**' (druk op ENTER).
1. Klik op de Automation-app in de zoek resultaten.

    ![Azure Automation toevoegen](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. Klik in het deel venster een Automation-account maken op **maken**.
1. Vul de vereiste gegevens in: Voer een naam in voor dit Automation-account, selecteer uw Azure-abonnements-ID en Azure-resources die moeten worden gebruikt voor het uitvoeren van het Power shell-script.
1. Voor de optie '**Azure uitvoeren als-account maken**' selecteert u **Ja** om het type account te configureren waarmee het Power shell-script wordt uitgevoerd met de Help van Azure Automation. Zie [Run as-account](../../automation/manage-runas-account.md)voor meer informatie over account typen.
1. Het maken van het Automation-account volt ooien door te klikken op **maken**.

> [!TIP]
> Noteer de naam van uw Azure Automation-account, abonnements-ID en resources (zoals kopiëren en plakken naar een Klad blok) precies zoals deze zijn ingevoerd tijdens het maken van de Automation-app. U hebt deze gegevens later nodig.

Als u meerdere Azure-abonnementen hebt waarvoor u dezelfde automatisering wilt maken, moet u dit proces herhalen voor uw andere abonnementen.

## <a name="update-azure-automation-modules"></a>Azure Automation-modules bijwerken

Het Power shell-script voor het ophalen van de aanbeveling voor automatisch afstemmen maakt gebruik van [Get-AzResource](/powershell/module/az.Resources/Get-azResource) en [Get-AzSqlDatabaseRecommendedAction-](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) opdrachten waarvoor Azure-module versie 4 en hoger is vereist.

- Als uw Azure-modules moeten worden bijgewerkt, raadpleegt u [AZ module support in azure Automation](../../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Azure Automation runbook maken

De volgende stap is het maken van een Runbook in Azure Automation waarin het Power shell-script voor het ophalen van afstemmings aanbevelingen zich bevindt.

Voer de volgende stappen uit om een nieuw Azure Automation runbook te maken:

1. Open het Azure Automation-account dat u in de vorige stap hebt gemaakt.
1. Klik in het deel venster Automation-account op de menu opdracht "**Runbooks**" aan de linkerkant om een nieuw Azure Automation runbook te maken met het Power shell-script. Zie [een nieuw Runbook maken](../../automation/manage-runbooks.md#create-a-runbook)voor meer informatie over het maken van Automation-runbooks.
1. Als u een nieuw runbook wilt toevoegen, klikt u op de menu optie **+ een Runbook toevoegen** en klikt u vervolgens op de **snelle invoer – een nieuw runbook maken**.
1. Typ in het deel venster Runbook de naam van uw Runbook (voor het doel van dit voor beeld wordt '**AutomaticTuningEmailAutomation**' gebruikt), selecteer het type Runbook als **Power shell** en schrijf een beschrijving van dit runbook om het doel ervan te beschrijven.
1. Klik op de knop **maken** om het maken van een nieuw runbook te volt ooien.

    ![Azure Automation-runbook toevoegen](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Volg deze stappen om een Power shell-script te laden in het runbook dat is gemaakt:

1. In het deel venster **Power shell-Runbook bewerken** selecteert u **RUNBOOKS** in de menu structuur en vouwt u de weer gave uit totdat u de naam van uw Runbook ziet (in dit voor beeld "**AutomaticTuningEmailAutomation**"). Selecteer dit runbook.
1. Kopieer de volgende Power shell-script code op de eerste regel van het Power shell-Runbook bewerken (te beginnen met het nummer 1). Dit Power shell-script wordt meegeleverd als-is om aan de slag te gaan. Wijzig het script in uw behoeften.

In de koptekst van het Power shell-script moet u vervangen `<SUBSCRIPTION_ID_WITH_DATABASES>` door de id van uw Azure-abonnement. Zie [de GUID van uw Azure-abonnement ophalen](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal)voor meer informatie over het ophalen van uw Azure-abonnements-id.

In het geval van verschillende abonnementen kunt u ze als door komma's gescheiden toevoegen aan de eigenschap $subscriptions in de header van het script.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Klik in de rechter bovenhoek op de knop **Opslaan** om het script op te slaan. Wanneer u tevreden bent met het script, klikt u op de knop **publiceren** om dit runbook te publiceren.

In het hoofd venster van runbook kunt u klikken op de knop **starten** om het script te **testen** . Klik op de **uitvoer** om de resultaten van het uitgevoerde script weer te geven. Deze uitvoer wordt de inhoud van uw e-mail bericht. De voorbeeld uitvoer van het script kan worden weer gegeven in de volgende scherm afbeelding.

![Aanbevelingen voor automatische afstemming uitvoeren met Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

Zorg ervoor dat u de inhoud aanpast door het Power shell-script aan uw behoeften aan te passen.

Met de bovenstaande stappen wordt het Power shell-script voor het ophalen van aanbevelingen voor automatisch afstemmen geladen in Azure Automation. De volgende stap is het automatiseren en plannen van de verzend taak voor e-mail.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>De e-mail taken met Microsoft Flow automatiseren

Voor het volt ooien van de oplossing kunt u als laatste stap een automatiserings stroom maken in Microsoft Flow die bestaat uit drie acties (taken):

- '**Azure Automation-taak maken**': wordt gebruikt om het Power shell-script uit te voeren voor het ophalen van aanbevelingen voor automatische afstemming binnen het Azure Automation runbook.
- '**Azure Automation taak uitvoer ophalen**': wordt gebruikt voor het ophalen van uitvoer van het uitgevoerde Power shell-script.
- "**Office 365 Outlook: een E-mail verzenden**": wordt gebruikt om e-mail te verzenden. E-mail berichten worden verzonden met behulp van het werk-of school account van de persoon die de stroom maakt.

Zie aan de slag [met Microsoft flow](/flow/getting-started)voor meer informatie over Microsoft flow mogelijkheden.

De vereiste voor deze stap is om u aan te melden voor een [Microsoft flow](https://flow.microsoft.com) -account en u aan te melden. Voer de volgende stappen uit om een **nieuwe stroom** in te stellen in de oplossing:

1. De menu opdracht '**mijn stromen**' openen.
1. Selecteer in mijn stromen de koppeling '**+ geen lege** pagina ' boven aan de pagina.
1. Klik onder aan de pagina op de koppeling **zoeken naar honderden connectors en triggers**.
1. Typ in het zoek veld '**recurrence**' en selecteer '**schema-recurrence**' in de zoek resultaten om de taak voor het uitvoeren van e-mail berichten te plannen.
1. Selecteer in het deel venster terugkeer patroon in het veld frequentie de plannings frequentie voor deze stroom die moet worden uitgevoerd, zoals automatische e-mail verzenden per minuut, uur, dag, week enzovoort.

De volgende stap bestaat uit het toevoegen van drie taken (maken, ophalen en verzenden van e-mail) naar de zojuist gemaakte terugkerende stroom. Voer de volgende stappen uit om de vereiste taken aan de stroom toe te voegen:

1. Maak een actie om het Power shell-script uit te voeren voor het ophalen van de aanbevelingen

   - Selecteer **+ nieuwe stap**, gevolgd door **een actie toevoegen** in het deel venster terugkeer patroon.
   - Typ in het zoek veld **Automation** en selecteer in de zoek resultaten **Azure Automation-taak maken**.
   - Configureer de taak eigenschappen in het deel venster taak maken. Voor deze configuratie hebt u details nodig van uw Azure-abonnements-ID, resource groep en Automation-account dat **eerder is vastgelegd** in het **deel venster Automation-account**. Zie voor meer informatie over de beschik bare opties in deze sectie [Azure Automation-taak maken](/connectors/azureautomation/#create-job).
   - U voltooit deze actie door op **stroom opslaan** te klikken.

2. Een actie maken om de uitvoer op te halen uit het uitgevoerde Power shell-script

   - Selecteer **+ nieuwe stap**, gevolgd door **een actie toevoegen** in het deel venster terugkeer patroon
   - Typ in het zoek veld **Automation** en selecteer in de zoek resultaten **Azure Automation-taak uitvoer ophalen**. Zie [Azure Automation-taak uitvoer ophalen](/connectors/azureautomation/#get-job-output)voor meer informatie over de beschik bare opties in deze sectie.
   - Velden invullen die vereist zijn (vergelijkbaar met het maken van de vorige taak): Vul uw Azure-abonnements-ID, resource groep en Automation-account in (zoals ingevoerd in het deel venster Automation-account).
   - Klik in het veld **taak-id** voor het menu **dynamische inhoud** om weer te geven. Selecteer in dit menu de optie **taak-id**.
   - U voltooit deze actie door op **stroom opslaan** te klikken.

3. Een actie maken om e-mail te verzenden met behulp van Office 365-integratie

   - Selecteer **+ nieuwe stap**, gevolgd door **een actie toevoegen** in het deel venster terugkeer patroon.
   - Typ in het zoek veld '**een e-mail verzenden**' en selecteer '**Office 365 Outlook – een e-mail verzenden**' uit de zoek resultaten.
   - Typ in het veld **aan** in het e-mail adres waarnaar u het e-mail bericht wilt verzenden.
   - Typ in het veld **onderwerp** het onderwerp van uw e-mail adres, bijvoorbeeld ' automatische afstemming aanbevelingen voor e-mail berichten '.
   - Klik in het veld **hoofd tekst** voor het menu **dynamische inhoud** om weer te geven. Selecteer in dit menu onder '**taak uitvoer ophalen**' de optie '**inhoud**'.
   - U voltooit deze actie door op **stroom opslaan** te klikken.

> [!TIP]
> Als u geautomatiseerd e-mail berichten wilt verzenden naar verschillende ontvangers, maakt u afzonderlijke stromen. In deze extra stromen wijzigt u het e-mail adres van de ontvanger in het veld aan en de onderwerpregel van het e-mail bericht in het veld onderwerp. Door nieuwe runbooks te maken in Azure Automation met aangepaste Power shell-scripts (zoals de wijziging van de Azure-abonnements-ID) kunt u geautomatiseerde scenario's verder aanpassen, zoals het verzenden van afzonderlijke ontvangers bij automatische afstemmings aanbevelingen voor afzonderlijke abonnementen.

In het bovenstaande worden de stappen voor het configureren van de werk stroom voor het verzenden van e-mail berichten beschreven. De volledige stroom die bestaat uit drie acties die zijn gebouwd, wordt weer gegeven in de volgende afbeelding.

![Berichten stroom voor automatisch afstemmen weer geven](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

Als u de stroom wilt testen, klikt u in de rechter bovenhoek van het deel venster stroom op **nu uitvoeren**.

De statistieken voor het uitvoeren van de geautomatiseerde taken, weer geven van geslaagde e-mail meldingen die worden verzonden, kunnen worden weer gegeven in het deel venster stroom analyse.

![Actieve stroom voor het automatisch afstemmen van e-mail meldingen](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

Het deel venster stroom analyse is handig voor het bewaken van het succes van taak uitvoeringen, en indien nodig voor het oplossen van problemen.  In het geval van het oplossen van problemen kunt u ook het uitvoerings logboek van Power shell-scripts bekijken dat toegankelijk is via de app Azure Automation.

De uiteindelijke uitvoer van het geautomatiseerde e-mail bericht ziet er ongeveer uit als het volgende e-mail bericht wordt ontvangen nadat u deze oplossing hebt gebouwd en uitgevoerd:

![Voorbeeld e-mail uitvoer van e-mail meldingen automatisch afstemmen](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

Door het Power shell-script aan te passen, kunt u de uitvoer en de opmaak van het geautomatiseerde e-mail bericht aanpassen aan uw behoeften.

U kunt de oplossing verder aanpassen om e-mail meldingen op te bouwen op basis van een specifieke afstemmings gebeurtenis en voor meerdere ontvangers, voor meerdere abonnementen of data bases, afhankelijk van uw aangepaste scenario's.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe automatisch afstemmen u kan helpen de prestaties van de data base te verbeteren, vindt u [in Azure SQL database automatisch afstemmen](automatic-tuning-overview.md).
- Als u het automatisch afstemmen van Azure SQL Database wilt inschakelen voor het beheren van uw werk belasting, raadpleegt u [automatisch afstemmen inschakelen](automatic-tuning-enable.md).
- Zie [aanbevelingen voor prestaties zoeken en](database-advisor-find-recommendations-portal.md)Toep assen om de aanbevelingen voor automatisch afstemmen hand matig te controleren en toe te passen.