---
title: Operations Manager verbinding maken met Azure Monitor | Microsoft Docs
description: Als u uw bestaande investering in System Center Operations Manager wilt behouden en combineren met de uitgebreide mogelijkheden van Log Analytics, kunt u Operations Manager integreren met uw werkruimte.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2020
ms.openlocfilehash: e429b87397b91de28f7fea14729b0d18187fa8ff
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031375"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Operations Manager verbinden met Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u uw bestaande investering in [System Center Operations Manager](/system-center/scom/key-concepts) wilt behouden en uitgebreide mogelijkheden met Azure monitor wilt gebruiken, kunt u Operations Manager integreren met uw log Analytics-werk ruimte. Zo kunt u gebruikmaken van de mogelijkheden van Logboeken in Azure Monitor terwijl u Operations Manager kunt gebruiken om het volgende te doen:

* Statuscontrole van uw IT-services met Operations Manager
* Onderhoud van de integratie met uw ITSM-beheeroplossingen voor incidenten en problemen
* Beheer van de levenscyclus van agents die lokaal zijn geïmplementeerd of op IaaS-VM's in de openbare cloud, en die u bewaakt met Operations Manager

Integreren met System Center Operations Manager voegt waarde toe aan uw strategie voor service bewerkingen door gebruik te maken van de snelheid en efficiëntie van Azure Monitor bij het verzamelen, opslaan en analyseren van logboek gegevens van Operations Manager. Met Azure Monitor logboek query's kunt u de fouten van problemen en halen-terugkeer patronen identificeren en gebruiken ter ondersteuning van uw bestaande probleem beheer proces. De flexibiliteit van de query-engine voor het onderzoeken van prestaties, gebeurtenis-en waarschuwings gegevens, met uitgebreide Dash boards en rapportage mogelijkheden om deze gegevens op Azure Monitor een zinvolle manier weer te geven, laat zien hoe belang rijk is Operations Manager.

De agents die rapporteren aan de Operations Manager beheer groep verzamelen gegevens van uw servers op basis van de [log Analytics gegevens bronnen](../agents/agent-data-sources.md) en oplossingen die u hebt ingeschakeld in uw werk ruimte. Afhankelijk van de ingeschakelde oplossingen worden de gegevens rechtstreeks van een Operations Manager-beheer server naar de service verzonden of door het volume van de gegevens die worden verzameld op het door de agent beheerde systeem, worden ze rechtstreeks van de agent naar een Log Analytics-werk ruimte verzonden. De beheerserver stuurt de gegevens rechtstreeks naar de service. Deze worden nooit naar de operationele database of datawarehouse-database geschreven. Wanneer een beheer server de verbinding met Azure Monitor kwijtraakt, worden de gegevens lokaal in de cache opgeslagen totdat de communicatie opnieuw tot stand is gebracht. Als de beheer server offline is vanwege gepland onderhoud of niet-geplande onderbreking, hervat een andere beheer server in de beheer groep de verbinding met Azure Monitor.  

In het volgende diagram ziet u de verbinding tussen de beheerser vers en agents in een System Center Operations Manager-beheer groep en Azure Monitor, met inbegrip van de richting en poorten.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Als uw IT-beveiligings beleid niet toestaat dat computers in uw netwerk verbinding maken met internet, kunnen beheerser vers worden geconfigureerd om verbinding te maken met de Log Analytics-gateway om configuratie-informatie te ontvangen en verzamelde gegevens te verzenden, afhankelijk van de ingeschakelde oplossingen. Voor meer informatie en stappen voor het configureren van uw Operations Manager-beheer groep voor communicatie via een Log Analytics gateway naar Azure Monitor raadpleegt u [computers verbinden om te Azure monitor met de log Analytics gateway](./gateway.md).  

## <a name="prerequisites"></a>Vereisten

Controleer de volgende vereisten voordat u begint.

* Azure Monitor ondersteunt alleen System Center Operations Manager 2016 of hoger, Operations Manager 2012 SP1 UR6 of hoger en Operations Manager 2012 R2 UR2 of hoger. Proxyondersteuning is toegevoegd aan Operations Manager 2012 SP1 UR7 en Operations Manager 2012 R2 UR3.
* Voor het integreren van System Center Operations Manager 2016 met de Amerikaanse Government-Cloud is een bijgewerkte Advisor-management pack opgenomen in update pakket 2 of hoger. System Center Operations Manager 2012 R2 vereist een bijgewerkte Advisor management pack die is opgenomen in update pakket 3 of hoger.
* Alle Operations Manager-agents moeten voldoen aan de minimale vereisten voor ondersteuning. Zorg ervoor dat de agents op de minimale update zijn, anders kan de communicatie met Windows-agent mislukken en fouten genereren in het Operations Manager-gebeurtenis logboek.
* Een Log Analytics-werkruimte. Zie Log Analytics overzicht van de [werk ruimte](../logs/design-logs-deployment.md)voor meer informatie.
* U bent geverifieerd bij Azure met een account dat lid is van de [rol log Analytics Inzender](../logs/manage-access.md#manage-access-using-azure-permissions).

* Ondersteunde regio's: alleen de volgende Azure-regio's worden ondersteund door System Center Operations Manager om verbinding te maken met een Log Analytics-werk ruimte:
    - VS - west-centraal
    - Australië - zuidoost
    - Europa -west
    - VS - oost
    - Azië - zuidoost
    - Japan East
    - Verenigd Koninkrijk Zuid
    - India - centraal
    - Canada - midden
    - VS - west 2

>[!NOTE]
>Recente wijzigingen aan Azure-Api's kunnen voor komen dat klanten de integratie tussen hun beheer groep en Azure Monitor voor de eerste keer configureren. Voor klanten die hun beheer groep al hebben geïntegreerd met de service, heeft dit geen invloed op de gebruikers, tenzij u uw bestaande verbinding opnieuw moet configureren.  
>Er is een nieuwe management pack uitgebracht voor de volgende versies van Operations Manager:
> - Voor System Center Operations Manager 2019 wordt deze management pack opgenomen in de-bron media en geïnstalleerd tijdens de installatie van een nieuwe beheer groep of tijdens een upgrade.
>- Operations Manager 1801 management pack is ook van toepassing op Operations Manager 1807.
>- Voor System Center Operations Manager 1801, moet u de management pack [hier](https://www.microsoft.com/download/details.aspx?id=57173)downloaden.
>- Voor System Center 2016-Operations Manager moet u de management pack [hier](https://www.microsoft.com/download/details.aspx?id=57172)downloaden.  
>- Voor System Center Operations Manager 2012 R2 kunt u de management pack [hier](https://www.microsoft.com/download/details.aspx?id=57171)downloaden.  


### <a name="network"></a>Netwerk

Hieronder vindt u de informatie over de proxy-en firewall configuratie die is vereist voor de Operations Manager agent, beheerser vers en Operations-console om te communiceren met Azure Monitor. Verkeer van elk onderdeel is uitgaand van uw netwerk naar Azure Monitor.

|Resource | Poortnummer| HTTPS-controle overslaan|  
|---------|------|-----------------------|  
|**Tussen**|||  
|\*.ods.opinsights.azure.com| 443 |Ja|  
|\*.oms.opinsights.azure.com| 443|Ja|  
|\*.blob.core.windows.net| 443|Ja|  
|\*.azure-automation.net| 443|Ja|  
|**Beheerserver**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Ja|  
|\*.ods.opinsights.azure.com| 443| Ja|  
|*.azure-automation.net | 443| Ja|  
|**Operations Manager-console naar Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 en 443||  
|\*.microsoft.com| 80 en 443||  
|\*.microsoftonline.com| 80 en 443||  
|\*.mms.microsoft.com| 80 en 443||  
|login.windows.net| 80 en 443||  
|portal.loganalytics.io| 80 en 443||
|api.loganalytics.io| 80 en 443||
|docs.loganalytics.io| 80 en 443||  

### <a name="tls-12-protocol"></a>TLS 1,2-protocol

Om ervoor te zorgen dat de beveiliging van gegevens die onderweg zijn Azure Monitor, kunt u het beste de agent en beheer groep configureren om ten minste Transport Layer Security (TLS) 1,2 te gebruiken. Er zijn oudere versies van TLS/Secure Sockets Layer (SSL) gevonden die kwetsbaar zijn en terwijl ze nog steeds werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen**. Raadpleeg voor meer informatie [veilig verzenden van gegevens met behulp van TLS 1,2](../logs/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Operations Manager verbinding maken met Azure Monitor

Voer de volgende stappen uit om uw Operations Manager-beheergroep te verbinden met een van uw Log Analytics-werkruimten.

Tijdens de eerste registratie van uw Operations Manager-beheer groep met een Log Analytics-werk ruimte is de optie om de proxy configuratie voor de beheer groep op te geven niet beschikbaar in de operations-console.  Deze optie is pas beschikbaar als de beheergroep bij de service is geregistreerd.  Als u dit wilt omzeilen, moet u de systeem proxy configuratie bijwerken met behulp van Netsh op het systeem waarop de operations-console wordt uitgevoerd om de integratie te configureren en alle beheerser vers in de beheer groep.  

1. Open een opdrachtprompt met verhoogde bevoegdheid.
   a. Ga naar **Start** en typ **cmd**.
   b. Klik met de rechter muisknop op **opdracht prompt** en selecteer als administrator uitvoeren * *.
1. Voer de volgende opdracht in en druk op **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Na het volt ooien van de volgende stappen voor de integratie met Azure Monitor, kunt u de configuratie verwijderen door uit te voeren `netsh winhttp reset proxy` en vervolgens de optie **proxy server configureren** in de operations-console gebruiken om de proxy-of log Analytics Gateway server op te geven.

1. Selecteer de werkruimte **Beheer** in de Operations Manager-console.
1. Vouw het knooppunt Operations Management Suite uit en klik op **Verbinding**.
1. Klik op de koppeling **Registreren bij Operations Management Suite**.
1. Voer op de pagina **Wizard Operations Management Suite voorbereiden: verificatie** het e-mailadres of telefoonnummer en het wachtwoord in van het beheerdersaccount dat is gekoppeld aan uw OMS-abonnement, en klik op **Aanmelden**.

   >[!NOTE]
   >De naam van de Operations Management Suite is buiten gebruik gesteld.

1. Nadat u bent geverifieerd, klikt u op de **wizard Operations Management Suite voorbereiden: pagina werk ruimte selecteren** , wordt u gevraagd om uw Azure-Tenant,-abonnement en log Analytics-werk ruimte te selecteren. Als u meerdere werkruimten hebt, selecteert u in de keuzelijst de werkruimte die u bij de Operations Manager-beheergroep wilt registreren, en klikt u op **Volgende**.

   > [!NOTE]
   > Operations Manager ondersteunt slechts één Log Analytics-werkruimte tegelijk. De verbinding en de computers die zijn geregistreerd voor Azure Monitor met de vorige werk ruimte worden verwijderd uit Azure Monitor.
   >
   >
1. Controleer de instellingen op de pagina **Wizard Operations Management Suite voorbereiden: samenvatting**. Als deze juist zijn, klikt u op **Maken**.
1. Op de pagina **Wizard Operations Management Suite voorbereiden: voltooien** klikt u op **Sluiten**.

### <a name="add-agent-managed-computers"></a>Door een agent beheerde computers toevoegen

Na het configureren van de integratie met uw Log Analytics-werk ruimte, wordt er alleen een verbinding met de service gemaakt, worden er geen gegevens verzameld van de agents die rapporteren aan uw beheer groep. Dit gebeurt pas nadat u hebt geconfigureerd welke specifieke door agents beheerde computers logboek gegevens voor Azure Monitor verzamelen. U kunt de computerobjecten afzonderlijk selecteren of een groep met Windows-computerobjecten selecteren. U kunt geen groep met objecten van een andere klasse selecteren, zoals logische schijven of SQL-databases.

1. Open de Operations Manager-console en selecteer de werkruimte **Beheer**.
1. Vouw het knooppunt Operations Management Suite uit en klik op **Verbinding**.
1. Klik op de koppeling **Computer/Groep toevoegen** onder de kop Acties rechts in het deelvenster.
1. In het dialoogvenster **Computer zoeken** kunt u zoeken naar computers of groepen die worden bewaakt door Operations Manager. Selecteer computers of groepen met de Operations Manager-beheer server voor onboarding naar Azure Monitor, klik op **toevoegen** en klik vervolgens op **OK**.

U kunt de computers en groepen die zijn geconfigureerd voor het verzamelen van gegevens bekijken in het knooppunt Beheerde computers onder Operations Management Suite in de werkruimte **Beheer** van de Operations-console. Hier kunt u zo nodig computers en groepen toevoegen of verwijderen.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Proxy-instellingen configureren in de Operations-console

Voer de volgende stappen uit als er een interne proxy server is tussen de beheer groep en Azure Monitor. Deze instellingen worden centraal beheerd vanuit de beheer groep en gedistribueerd naar door agents beheerde systemen die zijn opgenomen in het bereik voor het verzamelen van logboek gegevens voor Azure Monitor.  Dit is nuttig voor oplossingen die de beheerserver overslaan en gegevens rechtstreeks naar de service verzenden.

1. Open de Operations Manager-console en selecteer de werkruimte **Beheer**.
1. Vouw Operations Management Suite uit en klik op **Verbindingen**.
1. Klik in de weergave OMS-verbinding op **Proxyserver configureren**.
1. Selecteer op de pagina **Wizard Operations Management Suite: proxyserver** de optie **Een proxyserver gebruiken voor toegang tot Operations Management Suite** en typ de URL met het poortnummer, bijvoorbeeld http://corpproxy:80, en klik op **Voltooien**.

Als voor uw proxy server verificatie is vereist, voert u de volgende stappen uit om referenties en instellingen te configureren die moeten worden door gegeven aan beheerde computers die rapporteren aan Azure Monitor in de beheer groep.

1. Open de Operations Manager-console en selecteer de werkruimte **Beheer**.
1. Selecteer onder **RunAs-configuratie** de optie **Profielen**.
1. Open het profiel **System Center Advisor RunAs-profielproxy**.
1. Klik in de wizard Uitvoeren als-profiel op Toevoegen om een Uitvoeren als-account te gebruiken. U kunt een nieuw [Uitvoeren als-account](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) maken of een bestaand account gebruiken. Dit account moet over voldoende rechten beschikken voor het doorgeven van de proxyserver.
1. Als u het account voor beheer wilt instellen, kiest u **Een geselecteerde klasse, groep of object**, klikt u op **Selecteren...** en klikt u vervolgens op **Groep...** om het vak **Groep zoeken** te openen.
1. Zoek en selecteer de **Microsoft System Center Advisor Monitoring Server-groep**. Klik op **OK** na het selecteren van de groep om het vak **Groep zoeken** te sluiten.
1. Klik op **OK** om het vak **een uitvoeren als-account toevoegen** te sluiten.
1. Klik op **Opslaan** om de wizard te voltooien en de wijzigingen op te slaan.

Nadat de verbinding is gemaakt en u configureert welke agenten logboek gegevens verzamelen en rapporteren aan Azure Monitor, wordt de volgende configuratie toegepast in de beheer groep, niet noodzakelijkerwijs in de juiste volg orde:

* Het Uitvoeren als-account **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** wordt gemaakt. Het wordt gekoppeld aan het Uitvoeren als-profiel **Microsoft System Center Advisor Run As Profile Blob** voor twee klassen: **Collection Server** (server voor verzameling) en **Operations Manager Management Group** (Operations Manager-beheergroep).
* Er worden twee connectors gemaakt.  De eerste heeft de naam **Microsoft.SystemCenter. Advisor. DataConnector** en wordt automatisch geconfigureerd met een abonnement dat alle waarschuwingen stuurt die zijn gegenereerd op basis van exemplaren van alle klassen in de beheer groep naar Azure monitor. De tweede connector is **Advisor connector**, die verantwoordelijk is voor de communicatie met Azure monitor en het delen van gegevens.
* Agents en groepen die u hebt geselecteerd voor het verzamelen van gegevens in de beheergroep, worden toegevoegd aan de **Microsoft System Center Advisor Monitoring Server-groep**.

## <a name="management-pack-updates"></a>Management pack-updates

Nadat de configuratie is voltooid, brengt de Operations Manager-beheer groep een verbinding tot stand met Azure Monitor. De beheerserver wordt gesynchroniseerd met de webservice en ontvangt informatie over de bijgewerkte configuratie in de vorm van management packs voor de oplossingen die u hebt ingeschakeld en die zijn geïntegreerd met Operations Manager. Operations Manager controleert of er updates van deze Management Packs zijn en worden deze automatisch gedownload en geïmporteerd wanneer ze beschikbaar zijn. Dit gedrag wordt voornamelijk bepaald door twee regels:

* **Microsoft.SystemCenter. Advisor. MPUpdate** : Hiermee worden de Management Packs voor de basis Azure monitor bijgewerkt. Deze regel wordt standaard elke 12 uur uitgevoerd.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - Hiermee worden de management packs van ingeschakelde oplossingen in uw werkruimte bijgewerkt. Deze regel wordt standaard elke vijf (5) minuten uitgevoerd.

U kunt deze twee regels negeren om automatisch downloaden te voor komen door ze uit te scha kelen of de frequentie te wijzigen voor hoe vaak de beheer server synchroniseert met Azure Monitor om te bepalen of er een nieuwe management pack beschikbaar is en moet worden gedownload. Volg de stappen voor het [overschrijven van een regel of controle](/previous-versions/system-center/system-center-2012-R2/hh212869(v=sc.12)) als u de parameter **Frequency** (Frequentie) wilt bijwerken met een waarde in seconden om het synchronisatieschema te wijzigen. Wijzig de parameter **Enabled** (Ingeschakeld) als u de regels wilt uitschakelen. Configureer de overschrijvingen voor alle objecten van de klasse Operations Manager Management Group.

Als u wilt door gaan met het bestaande wijzigings besturings proces voor het beheren van management pack releases in uw productie beheer groep, kunt u de regels uitschakelen en inschakelen tijdens specifieke tijdstippen wanneer updates zijn toegestaan. Als u een ontwikkelings- of QA-beheergroep in uw omgeving hebt en deze met internet verbonden is, kunt u die beheergroep configureren met een Log Analytics-werkruimte ter ondersteuning van dit scenario. Hierdoor kunt u de iteratieve releases van de Azure Monitor Management Packs controleren en evalueren voordat u ze uitbrengt in uw productie beheer groep.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Een Operations Manager-groep overschakelen naar een nieuwe Log Analytics-werkruimte

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
1. Klik in Azure Portal op **Meer services** in de linkerbenedenhoek. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics** en maak vervolgens een werkruimte.  
1. Open de Operations Manager-console met een account dat lid is van de rol Administrators in Operations Manager en selecteer de werkruimte **Beheer**.
1. Vouw Log Analytics uit en selecteer **verbindingen**.
1. Selecteer de koppeling **Operations Management Suite opnieuw configureren** in het middelste gedeelte van het deelvenster.
1. Volg de **log Analytics wizard voor onboarding** en voer het e-mail adres of telefoon nummer en het wacht woord in van het beheerders account dat is gekoppeld aan uw nieuwe log Analytics-werk ruimte.

   > [!NOTE]
   > Op de pagina **Wizard Operations Management Suite voorbereiden: werkruimte selecteren** wordt de bestaande werkruimte weergegeven die momenteel wordt gebruikt.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Operations Manager integratie met Azure Monitor valideren

Gebruik de volgende query om de verbonden instanties van Operations Manager op te halen:

```azurepowershell
union *
| where isnotempty(MG)
| where not(ObjectName == 'Advisor Metrics' or ObjectName == 'ManagedSpace')
| summarize LastData = max(TimeGenerated) by lowerCasedComputerName=tolower(Computer), MG, ManagementGroupName
| sort by lowerCasedComputerName asc
```

## <a name="remove-integration-with-azure-monitor"></a>Integratie met Azure Monitor verwijderen

Als de integratie tussen uw Operations Manager-beheergroep en de Log Analytics-werkruimte niet meer nodig is, moet u verschillende stappen uitvoeren om de verbinding en de configuratie in de beheergroep correct te verwijderen. Met de volgende procedure kunt u uw Log Analytics-werk ruimte bijwerken door de verwijzing van de beheer groep te verwijderen, de Azure Monitor-connectors te verwijderen en vervolgens Management Packs te verwijderen die integratie met de service ondersteunen.  

Management Packs voor de oplossingen die u hebt ingeschakeld en die zijn geïntegreerd met Operations Manager en de Management Packs die vereist zijn voor de ondersteuning van integratie met Azure Monitor kunnen niet eenvoudig worden verwijderd uit de beheer groep. Dit komt omdat sommige van de Azure Monitor Management Packs afhankelijk zijn van andere gerelateerde Management Packs. Download het script [remove a management pack with dependencies](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) (een management pack met afhankelijkheden verwijderen) van TechNet Script Center als u management packs die afhankelijk zijn van andere management packs wilt verwijderen.  

1. Open de Operations Manager-opdrachtshell met een account dat lid is van de rol Administrators in Operations Manager.

    > [!WARNING]
    > Controleer voordat u doorgaat of er geen aangepaste management packs zijn met 'Advisor' of 'IntelligencePack' in de naam, anders worden ze uit de beheergroep verwijderd tijdens de volgende stappen.
    >

1. Typ vanaf de opdrachtshell-prompt `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Typ vervolgens `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Als u ook alle resterende management packs die afhankelijk zijn van andere management packs van System Center Advisor wilt verwijderen, gebruikt u het script *RecursiveRemove.ps1* dat u eerder van TechNet Script Center hebt gedownload.  

    > [!NOTE]
    > Met de stap voor het verwijderen van de Advisor Management Packs met Power shell worden de interne Management Packs voor micro soft System Center Advisor of micro soft System Center Advisor niet automatisch verwijderd.  Probeer ze niet te verwijderen.  
    >  

1. Open de Operations-console van Operations Manager met een account dat lid is van de rol Administrators in Operations Manager.
1. Selecteer onder **Beheer** het knooppunt **Management Packs**, typ in het vak **Look for:** (Zoeken naar) **Advisor** en controleer of de volgende management packs nog zijn geïmporteerd in de beheergroep:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Klik in de Azure Portal op de tegel **instellingen** .
1. Selecteer **verbonden bronnen**.
1. In de tabel onder de sectie System Center Operations Manager ziet u de naam van de beheer groep die u wilt verwijderen uit de werk ruimte. Klik onder de kolom **Laatste gegevens** op **Verwijderen**.  

    > [!NOTE]
    > De koppeling **Verwijderen** is pas beschikbaar als er 14 dagen geen activiteit van de verbonden beheergroep is gedetecteerd.  
    >

1. Er wordt een bevestigingsvenster weergegeven waarin u wordt gevraagd of u wilt doorgaan met het verwijderen.  Klik op **Ja** om door te gaan.

Als u de twee connectors Microsoft.SystemCenter.Advisor.DataConnector en Advisor Connector wilt verwijderen, slaat u het onderstaande PowerShell-script op uw computer op en voert u het script uit met behulp van de volgende voorbeelden:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Als de computer waarop u dit script uitvoert geen beheerserver is, moet mogelijk de Operations Manager-opdrachtshell erop zijn geïnstalleerd, afhankelijk van de versie van uw beheergroep.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

In de toekomst moet u het management pack bestand opnieuw importeren als u van plan bent om uw beheer groep opnieuw te verbinden met een Log Analytics-werk ruimte `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` . Afhankelijk van welke versie van System Center Operations Manager in uw omgeving is geïmplementeerd, bevindt dit bestand zich op de volgende locatie:

* Op het bronmedium in de map `\ManagementPacks` voor System Center 2016 - Operations Manager of hoger.
* Van het meest recente updatepakket dat op de beheergroep is toegepast. Voor Operations Manager 2012 is de bronmap `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` en voor 2012 R2 aanwezig in `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` .

## <a name="next-steps"></a>Volgende stappen

Zie [Azure monitor oplossingen toevoegen vanuit de Oplossingengalerie](../insights/solutions.md)om functionaliteit toe te voegen en gegevens te verzamelen.