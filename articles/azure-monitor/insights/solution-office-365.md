---
title: Office 365-beheer oplossing in azure
description: Dit artikel bevat informatie over de configuratie en het gebruik van de Office 365-oplossing in Azure.  Het bevat een gedetailleerde beschrijving van de Office 365-records die zijn gemaakt in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: be5409f7130f0e79b77871a2657609e8ddb76e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728634"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365-beheer oplossing in azure (preview-versie)

![Office 365-logo](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Oplossings update
> Deze oplossing is vervangen door de oplossing voor algemene Beschik baarheid van [Office 365](../../sentinel/connect-office-365.md) in [Azure Sentinel](../../sentinel/overview.md) en de [Azure AD-oplossing voor rapportage en bewaking](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Ze bieden samen een bijgewerkte versie van de vorige Azure Monitor Office 365-oplossing met een verbeterde configuratie-ervaring. U kunt de bestaande oplossing blijven gebruiken tot 31 oktober 2020.
> 
> Azure Sentinel is een native Security Information and Event Management-oplossing in de cloud die logboeken opneemt en extra functionaliteit biedt voor SIEM, met inbegrip van detecties, onderzoeken, jacht en machine learning op basis van inzichten. Met Azure Sentinel kunt u nu opname maken van Office 365 share point-activiteiten en Exchange-beheer Logboeken.
> 
> Azure AD Reporting biedt een uitgebreidere weer gave van logboeken van Azure AD-activiteit in uw omgeving, waaronder aanmeldings gebeurtenissen, controle gebeurtenissen en wijzigingen in uw Directory. Als u Azure AD-logboeken wilt verbinden, kunt u de [Azure-Sentinel Azure AD-connector](../../sentinel/connect-azure-active-directory.md) gebruiken of [integratie met Azure AD-logboeken configureren met Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> Voor de verzameling van Azure AD-logboeken gelden Azure Monitor prijzen.  Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor meer informatie.
>
> De Azure Sentinel Office 365-oplossing gebruiken:
> 1. Het gebruik van Office 365 connector in azure Sentinel heeft gevolgen voor de prijzen voor uw werk ruimte. Zie [Azure Sentinel-prijzen](https://azure.microsoft.com/pricing/details/azure-sentinel/)voor meer informatie.
> 2. Als u al gebruikmaakt van de oplossing Azure Monitor Office 365, moet u deze eerst verwijderen met het script in de [onderstaande sectie verwijderen](#uninstall).
> 3. [De Azure-oplossing voor Sentinel inschakelen](../../sentinel/quickstart-onboard.md) in uw werk ruimte.
> 4. Ga naar de pagina **Data connectors** in azure Sentinel en schakel de **Office 365** -connector in.
>
> ## <a name="frequently-asked-questions"></a>Veelgestelde vragen
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>V: is het mogelijk om aan de slag te kunnen met Office 365 Azure Monitor oplossing van nu tot en met 31 oktober?
> Nee, de Azure Monitor Office 365-oplossing voor het onboarden van scripts zijn niet meer beschikbaar. De oplossing wordt op 31 oktober verwijderd.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>V: worden de tabellen en schema's gewijzigd?
> De naam en het schema van de **OfficeActivity** -tabel blijven hetzelfde als in de huidige oplossing. U kunt dezelfde query's blijven gebruiken in de nieuwe oplossing, met uitzonde ring van query's die verwijzen naar Azure AD-gegevens.
> 
> De nieuwe logboeken voor [Azure AD Reporting and monitoring-oplossingen](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) worden opgenomen in [de SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) -en [audit logs bevat](../../active-directory/reports-monitoring/concept-audit-logs.md) -tabellen in plaats van **OfficeActivity**. Zie [Azure AD-logboeken analyseren](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md)voor meer informatie. Dit is ook relevant voor Azure Sentinel-en Azure monitor-gebruikers.
> 
> Hieronder vindt u voor beelden voor het converteren van query's van **OfficeActivity** naar **SigninLogs**:
> 
> **Query's voor mislukte aanmeldingen door gebruiker:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Azure AD-bewerkingen weer geven:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>V: hoe kan ik een Azure-Sentinel op het moeder bord?
> Azure Sentinel is een oplossing die u kunt inschakelen op nieuwe of bestaande Log Analytics werk ruimte. Zie de [documentatie van Azure Sentinel on-boarding](../../sentinel/quickstart-onboard.md)voor meer informatie.
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>V: heb ik Azure Sentinel nodig om verbinding te maken met de Azure AD-logboeken?
> U kunt [integratie van Azure AD-logboeken met Azure monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)configureren. Dit is niet gerelateerd aan de Azure-Sentinel-oplossing. Azure Sentinel biedt een systeem eigen connector en out-of-the-Box-inhoud voor Azure AD-Logboeken. Zie voor meer informatie de onderstaande vraag over de out-of-the-box-beveiligings gerichte inhoud.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>V: wat zijn de verschillen bij het verbinden van Azure AD-logboeken vanuit Azure Sentinel en Azure Monitor?
> Azure Sentinel en Azure Monitor maken verbinding met Azure AD-logboeken op basis van dezelfde [Azure AD-oplossing voor rapportage en bewaking](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Azure Sentinel biedt een systeem eigen connector met één klik waarmee dezelfde gegevens worden verbonden en informatie wordt gecontroleerd.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>V: wat moet ik wijzigen wanneer ik overstapt op de nieuwe Azure AD-rapporten en-bewakings tabellen?
> Alle query's die gebruikmaken van Azure AD-gegevens, met inbegrip van query's in waarschuwingen, Dash boards en alle inhoud die u hebt gemaakt met behulp van Office 365 Azure AD-gegevens, moeten opnieuw worden gemaakt met behulp van de nieuwe tabellen.
>
> Azure Sentinel en Azure AD bieden ingebouwde inhoud die u kunt gebruiken wanneer u overstapt op de oplossing voor Azure AD-rapportage en-bewaking. Zie voor meer informatie de volgende vraag over out-of-the-box-beveiligings gerichte inhoud en [hoe u Azure monitor werkmappen gebruikt voor Azure Active Directory rapporten](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>V: hoe kan ik de Azure Sentinel out-of-Box-beveiligings gerichte inhoud gebruiken?
> Azure Sentinel biedt kant-en-klare, beveiligings gerichte Dash boards, aangepaste waarschuwings query's, jacht-query's, onderzoek en mogelijkheden voor automatische reacties op basis van de Office 365-en Azure AD-Logboeken. Bekijk de Azure Sentinel GitHub en de zelf studies voor meer informatie:
>
> - [Bedreigingen-out-of-Box detecteren](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Aangepaste analyseregels maken om verdachte bedreigingen te detecteren](../../sentinel/tutorial-detect-threats-custom.md)
> - [Uw gegevens bewaken](../../sentinel/tutorial-monitor-your-data.md)
> - [Incidenten onderzoeken met Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Azure Sentinel GitHub-Community](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>V: biedt Azure Sentinel extra connectors als onderdeel van de oplossing?
> Ja, Zie [Azure Sentinel Connect-gegevens bronnen](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>V: wat gebeurt er dan 31 oktober? Moet ik het vooraf niet meer vrijgeven?
> 
> - U kunt geen gegevens ontvangen van de **Office365** -oplossing. De oplossing wordt verwijderd uit uw werk ruimte en is niet meer beschikbaar in de Marketplace.
> - Voor Azure Sentinel-klanten wordt de Log Analytics werkruimte oplossing **Office365** opgenomen in de Azure Sentinel **SecurityInsights** -oplossing.
> - Als u uw oplossing niet hand matig niet meer vrijgeven op 31 oktober, worden uw gegevens automatisch losgekoppeld en wordt de **OfficeActivity** -tabel verwijderd. U kunt de tabel zelfs herstellen als u de Office 365-connector inschakelt in azure Sentinel, zoals hieronder wordt uitgelegd.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>V: zullen mijn gegevens worden overgedragen naar de nieuwe oplossing?
> Ja. Wanneer u de **Office 365** -oplossing uit uw werk ruimte verwijdert, worden de bijbehorende gegevens tijdelijk niet beschikbaar omdat het schema wordt verwijderd. Wanneer u de nieuwe **Office 365** -connector inschakelt in azure Sentinel, wordt het schema teruggezet naar de werk ruimte en worden alle gegevens die al zijn verzameld beschikbaar. 
 

Met de Office 365-beheer oplossing kunt u uw Office 365-omgeving in Azure Monitor bewaken.

- Bewaak gebruikers activiteiten in uw Office 365-accounts om gebruiks patronen te analyseren en gedrags trends te identificeren. U kunt bijvoorbeeld specifieke gebruiks scenario's extra heren, zoals bestanden die worden gedeeld buiten uw organisatie of de populairste share point-sites.
- Beheer activiteiten controleren om configuratie wijzigingen of bewerkingen met hoge bevoegdheden bij te houden.
- Detecteer en onderzoek ongewenste gebruikers gedrag dat kan worden aangepast aan de behoeften van uw organisatie.
- Demonstratie van controle en naleving. U kunt bijvoorbeeld bestands toegangs bewerkingen bewaken op vertrouwelijke bestanden, die u kunnen helpen bij het controle-en nalevings proces.
- Voer operationele problemen op te lossen met behulp van [logboek query's](../logs/log-query-overview.md) op Office 365-activiteit gegevens van uw organisatie.


## <a name="uninstall"></a>Verwijderen

U kunt de Office 365-beheer oplossing verwijderen met behulp van het proces in [een beheer oplossing verwijderen](solutions.md#remove-a-monitoring-solution). Hierdoor worden er geen gegevens meer verzameld van Office 365 in Azure Monitor. Volg de onderstaande procedure om u af te melden bij Office 365 en stop het verzamelen van gegevens.

1. Sla het volgende script op als *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Voer het script uit met de volgende opdracht:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Voorbeeld:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

U wordt gevraagd om referenties. Geef de referenties op voor uw Log Analytics-werk ruimte.

## <a name="data-collection"></a>Gegevens verzamelen

Het kan enkele uren duren voordat gegevens worden verzameld. Zodra het verzamelen begint, verzendt Office 365 een [webhook-melding](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) met gedetailleerde gegevens om Azure monitor telkens wanneer een record wordt gemaakt. Deze record is binnen een paar minuten na ontvangst beschikbaar in Azure Monitor.

## <a name="using-the-solution"></a>De oplossing gebruiken

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Wanneer u de Office 365-oplossing aan uw Log Analytics-werk ruimte toevoegt, wordt de tegel **office 365** toegevoegd aan uw dash board. Deze tegel toont het aantal computers in uw omgeving, evenals een grafische voorstelling hiervan, en de bijbehorende updatenaleving.<br><br>
![Office 365-overzichts tegel](media/solution-office-365/tile.png)  

Klik op de tegel **office 365** om het **Office 365** -dash board te openen.

![Office 365-dash board](media/solution-office-365/dashboard.png)  

Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat de bovenste tien waarschuwingen per aantal dat overeenkomt met de criteria van die kolom voor het opgegeven bereik en tijds bereik. U kunt een zoek opdracht in het logboek uitvoeren met de volledige lijst door te klikken op alles weer geven onder aan de kolom of door op de kolomkop te klikken.

| Kolom | Beschrijving |
|:--|:--|
| Operations | Bevat informatie over de actieve gebruikers uit uw alle bewaakte Office 365-abonnementen. U kunt ook het aantal activiteiten zien dat na verloop van tijd plaatsvindt.
| Exchange | Toont de uitsplitsing van Exchange Server-activiteiten, zoals Add-Mailbox machtiging of set-mailbox. |
| SharePoint | Toont de belangrijkste activiteiten die gebruikers uitvoeren op share point-documenten. Wanneer u inzoomt op deze tegel, worden in de zoek pagina de details van deze activiteiten weer gegeven, zoals het doel document en de locatie van deze activiteit. Voor een gebeurtenis die toegang heeft tot een bestand kunt u bijvoorbeeld het document zien dat wordt geopend, de bijbehorende account naam en het IP-adres. |
| Azure Active Directory | Bevat de belangrijkste gebruikers activiteiten, zoals het opnieuw instellen van gebruikers wachtwoorden en aanmeldings pogingen. Wanneer u inzoomt, kunt u de details van deze activiteiten zien, zoals de status van het resultaat. Dit is vooral nuttig als u verdachte activiteiten op uw Azure Active Directory wilt bewaken. |




## <a name="azure-monitor-log-records"></a>Azure Monitor logboek records

Alle records die zijn gemaakt in de Log Analytics-werk ruimte in Azure Monitor door de Office 365-oplossing, hebben een **type** **OfficeActivity**.  De eigenschap **OfficeWorkload** bepaalt welke Office 365-service de record verwijst naar-Exchange, AzureActiveDirectory, share point of OneDrive.  De eigenschap **record type** geeft het type bewerking aan.  De eigenschappen variëren per bewerkings type en worden weer gegeven in de onderstaande tabellen.

### <a name="common-properties"></a>Algemene eigenschappen

De volgende eigenschappen zijn gebruikelijk voor alle Office 365-records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | Het IP-adres van het apparaat waarmee de activiteit is uitgevoerd. Het IP-adres wordt weergegeven in de IPv4- of IPv6-adresindeling. |
| OfficeWorkload | Office 365-service waarnaar de record verwijst.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Bewerking | De naam van de activiteit door de gebruiker of beheerder.  |
| OrganizationId | De GUID voor de Office 365-Tenant van uw organisatie. Deze waarde is altijd hetzelfde voor uw organisatie, ongeacht de Office 365-service waarin deze zich voordoet. |
| RecordType | Het type bewerking dat is uitgevoerd. |
| ResultStatus | Geeft aan of de actie (opgegeven in de eigenschap Operation) al dan niet is gelukt. Mogelijke waarden zijn geslaagd, PartiallySucceeded of mislukt. Voor Exchange-beheer activiteiten is de waarde True of false. |
| UserId | De UPN (User Principal Name) van de gebruiker die de actie heeft uitgevoerd die heeft geresulteerd in het registreren van de record. bijvoorbeeld my_name@my_domain_name . Houd er rekening mee dat records voor activiteiten die worden uitgevoerd door systeem accounts (zoals SHAREPOINT\system of NTAUTHORITY\SYSTEM) ook worden opgenomen. | 
| UserKey | Een alternatieve ID voor de gebruiker die in de eigenschap UserId is geïdentificeerd.  Deze eigenschap wordt bijvoorbeeld gevuld met de unieke Pass Port-ID (PUID) voor gebeurtenissen die worden uitgevoerd door gebruikers in share point, OneDrive voor bedrijven en Exchange. Deze eigenschap kan ook dezelfde waarde opgeven als de eigenschap UserID voor gebeurtenissen die optreden in andere services en gebeurtenissen die worden uitgevoerd door systeem accounts|
| UserType | Het type gebruiker dat de bewerking heeft uitgevoerd.<br><br>Beheerder<br>Toepassing<br>DcAdmin<br>Regelmatig<br>Gereserveerd<br>ServicePrincipal<br>Systeem |


### <a name="azure-active-directory-base"></a>Azure Active Directory basis

De volgende eigenschappen zijn gebruikelijk voor alle Azure Active Directory records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Het type Azure AD-gebeurtenis. |
| ExtendedProperties | De uitgebreide eigenschappen van de Azure AD-gebeurtenis. |


### <a name="azure-active-directory-account-logon"></a>Aanmelding bij Azure Active Directory-account

Deze records worden gemaakt wanneer een Active Directorye gebruiker zich probeert aan te melden.

| Eigenschap | Beschrijving |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | De toepassing die de aanmeldings gebeurtenis van het account activeert, zoals Office 15. |
| `Client` | Details over het client apparaat, het besturings systeem van het apparaat en de browser van het apparaat dat is gebruikt voor de aanmeldings gebeurtenis van het account. |
| `LoginStatus` | Deze eigenschap is rechtstreeks van OrgIdLogon. LoginStatus. De toewijzing van verschillende interessante aanmeldings fouten kan worden uitgevoerd door middel van waarschuwings algoritmen. |
| `UserDomain` | De gegevens van de Tenant-id (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Deze records worden gemaakt wanneer er wijzigingen of toevoegingen worden aangebracht aan Azure Active Directory-objecten.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | De gebruiker die de actie (geïdentificeerd door de eigenschap Operation) is uitgevoerd. |
| Acteur | De gebruiker of service-principal die de actie heeft uitgevoerd. |
| ActorContextId | De GUID van de organisatie waartoe de actor behoort. |
| ActorIpAddress | Het IP-adres van de actor in IPV4-of IPV6-adres indeling. |
| InterSystemsId | De GUID waarmee de acties voor verschillende onderdelen in de Office 365-service worden bijgehouden. |
| IntraSystemId |     De GUID die wordt gegenereerd door Azure Active Directory om de actie bij te houden. |
| SupportTicketId | De ID van het klant ondersteunings ticket voor de actie in ' namen van ' Act-of-scenario's. |
| TargetContextId | De GUID van de organisatie waartoe de doel gebruiker behoort. |


### <a name="data-center-security"></a>Beveiliging van data centers

Deze records worden gemaakt op basis van de beveiligings controle gegevens van data centers.  

| Eigenschap | Beschrijving |
|:--- |:--- |
| EffectiveOrganization | De naam van de Tenant waarop de uitbrei ding/cmdlet is gericht. |
| ElevationApprovedTime | De tijds tempel voor het moment waarop de verhoging werd goedgekeurd. |
| ElevationApprover | De naam van een micro soft-Manager. |
| ElevationDuration | De duur waarvoor de verhoging actief was. |
| ElevationRequestId |     Een unieke id voor de aanvraag voor benodigde bevoegdheden. |
| ElevationRole | De rol waarvoor de uitbrei ding is aangevraagd. |
| ElevationTime | De begin tijd van de verhoging. |
| Start_Time | De begin tijd van de uitvoering van de cmdlet. |


### <a name="exchange-admin"></a>Exchange-beheerder

Deze records worden gemaakt wanneer er wijzigingen worden aangebracht in de Exchange-configuratie.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Hiermee geeft u op of de cmdlet is uitgevoerd door een gebruiker in uw organisatie, door het micro soft data center-personeel of een datacenter service-account of door een gedelegeerd beheerder. De waarde False geeft aan dat de cmdlet is uitgevoerd door iemand in uw organisatie. De waarde True geeft aan dat de cmdlet is uitgevoerd door een datacenter medewerker, een Data Center-service account of een gedelegeerd beheerder. |
| ModifiedObjectResolvedName |     Dit is de beschrijvende naam van de gebruiker van het object dat door de cmdlet is gewijzigd. Dit wordt alleen vastgelegd als de cmdlet het object wijzigt. |
| OrganizationName | De naam van de Tenant. |
| OriginatingServer | De naam van de server van waaruit de cmdlet is uitgevoerd. |
| Parameters | De naam en waarde voor alle para meters die zijn gebruikt met de cmdlet die wordt geïdentificeerd in de eigenschap Operations. |


### <a name="exchange-mailbox"></a>Exchange-postvak

Deze records worden gemaakt wanneer wijzigingen of toevoegingen worden doorgevoerd in Exchange-post vakken.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informatie over de e-mailclient die is gebruikt om de bewerking uit te voeren, zoals een browser versie, Outlook-versie en informatie over mobiele apparaten. |
| Client_IPAddress | Het IP-adres van het apparaat dat is gebruikt tijdens het registreren van de bewerking. Het IP-adres wordt weergegeven in de IPv4- of IPv6-adresindeling. |
| ClientMachineName | De naam van de computer die als host fungeert voor de Outlook-client. |
| ClientProcessName | De e-mailclient die is gebruikt voor toegang tot het postvak. |
| ClientVersion | De versie van de e-mailclient. |
| InternalLogonType | Gereserveerd voor intern gebruik. |
| Logon_Type | Hiermee wordt het type gebruiker aangegeven dat toegang heeft gekregen tot het postvak en de geregistreerde bewerking heeft uitgevoerd. |
| LogonUserDisplayName |     De beschrijvende naam van de gebruiker die de bewerking heeft uitgevoerd. |
| LogonUserSid | De SID van de gebruiker die de bewerking heeft uitgevoerd. |
| MailboxGuid | De Exchange-GUID van het postvak waartoe toegang is verkregen. |
| MailboxOwnerMasterAccountSid | De hoofd account-SID van het postvak eigenaar-account. |
| MailboxOwnerSid | De SID van de postbus eigenaar. |
| MailboxOwnerUPN | Het e-mail adres van de persoon die eigenaar is van het postvak dat is geopend. |


### <a name="exchange-mailbox-audit"></a>Exchange-postvak controle

Deze records worden gemaakt wanneer een postvak controle vermelding wordt gemaakt.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | Hiermee wordt het item aangeduid waarop de bewerking is uitgevoerd | 
| SendAsUserMailboxGuid | De Exchange-GUID van het postvak dat is gebruikt voor het verzenden van e-mail. |
| SendAsUserSmtp | Het SMTP-adres van de gebruiker die wordt geïmiteerd. |
| SendonBehalfOfUserMailboxGuid | De Exchange-GUID van het postvak dat is gebruikt voor het verzenden van e-mail namens. |
| SendOnBehalfOfUserSmtp | Het SMTP-adres van de gebruiker aan wie het e-mail bericht wordt verzonden. |


### <a name="exchange-mailbox-audit-group"></a>Audit groep voor Exchange-postvak

Deze records worden gemaakt wanneer wijzigingen of toevoegingen worden aangebracht aan Exchange-groepen.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informatie over elk item in de groep. |
| CrossMailboxOperations | Geeft aan of de bewerking meer dan een postvak heeft betrokken. |
| DestMailboxId | Stel dit in als de para meter CrossMailboxOperations is ingesteld op True. Hiermee geeft u de GUID van het doel postvak op. |
| DestMailboxOwnerMasterAccountSid | Stel dit in als de para meter CrossMailboxOperations is ingesteld op True. Hiermee geeft u de SID voor de SID van het hoofd account van de eigenaar van het doel postvak. |
| DestMailboxOwnerSid | Stel dit in als de para meter CrossMailboxOperations is ingesteld op True. Hiermee geeft u de SID van het doel postvak. |
| DestMailboxOwnerUPN | Stel dit in als de para meter CrossMailboxOperations is ingesteld op True. Hiermee geeft u de UPN van de eigenaar van het doel postvak. |
| DestFolder | De doelmap, voor bewerkingen zoals verplaatsen. |
| Map | De map waarin een groep items zich bevindt. |
| Mappen |     Informatie over de bron mappen die zijn betrokken bij een bewerking; Als bijvoorbeeld mappen zijn geselecteerd en vervolgens worden verwijderd. |


### <a name="sharepoint-base"></a>Share point-basis

Deze eigenschappen zijn gebruikelijk voor alle share point-records.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| Source | Hiermee wordt aangegeven dat er een gebeurtenis is opgetreden in share point. Mogelijke waarden zijn share point of ObjectModel. |
| ItemType | Het type object dat is geopend of gewijzigd. Zie de tabel item type voor meer informatie over de typen objecten. |
| MachineDomainInfo | Informatie over synchronisatie bewerkingen voor apparaten. Deze informatie wordt alleen gerapporteerd als deze aanwezig is in de aanvraag. |
| MachineId |     Informatie over synchronisatie bewerkingen voor apparaten. Deze informatie wordt alleen gerapporteerd als deze aanwezig is in de aanvraag. |
| Site_ | De GUID van de site waar het bestand of de map die door de gebruiker wordt gebruikt, zich bevindt. |
| Source_Name | De entiteit die de gecontroleerde bewerking heeft geactiveerd. Mogelijke waarden zijn share point of ObjectModel. |
| User agent | Informatie over de client of browser van de gebruiker. Deze informatie wordt verstrekt door de client of browser. |


### <a name="sharepoint-schema"></a>Share point-schema

Deze records worden gemaakt wanneer er configuratie wijzigingen worden aangebracht in share point.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Optionele teken reeks voor aangepaste gebeurtenissen. |
| Event_Data |     Optionele Payload voor aangepaste gebeurtenissen. |
| ModifiedProperties | De eigenschap is opgenomen voor beheer gebeurtenissen, zoals het toevoegen van een gebruiker als lid van een-site of een beheer groep voor een site verzameling. De eigenschap bevat de naam van de eigenschap die is gewijzigd (bijvoorbeeld de groep site beheerder), de nieuwe waarde van de gewijzigde eigenschap (bijvoorbeeld de gebruiker die is toegevoegd als een site beheerder) en de vorige waarde van het gewijzigde object. |


### <a name="sharepoint-file-operations"></a>Share point-Bestands bewerkingen

Deze records worden gemaakt als reactie op Bestands bewerkingen in share point.

| Eigenschap | Beschrijving |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | De bestands extensie van een bestand dat wordt gekopieerd of verplaatst. Deze eigenschap wordt alleen weer gegeven voor FileCopied-en FileMoved-gebeurtenissen. |
| DestinationFileName | De naam van het bestand dat wordt gekopieerd of verplaatst. Deze eigenschap wordt alleen weer gegeven voor FileCopied-en FileMoved-gebeurtenissen. |
| DestinationRelativeUrl | De URL van de doelmap waarnaar een bestand wordt gekopieerd of verplaatst. De combi natie van de waarden voor SiteURL-, DestinationRelativeURL-en DestinationFileName-para meters is hetzelfde als de waarde voor de eigenschap ObjectID, de volledige padnaam voor het bestand dat is gekopieerd. Deze eigenschap wordt alleen weer gegeven voor FileCopied-en FileMoved-gebeurtenissen. |
| SharingType | Het type machtigingen voor delen dat is toegewezen aan de gebruiker waarmee de resource is gedeeld. Deze gebruiker wordt aangeduid met de para meter UserSharedWith. |
| Site_Url | De URL van de site waar het bestand of de map die door de gebruiker wordt geopend, zich bevindt. |
| SourceFileExtension | De bestands extensie van het bestand dat door de gebruiker is geopend. Deze eigenschap is leeg als het object waartoe toegang is verkregen, een map is. |
| SourceFileName |     De naam van het bestand of de map die door de gebruiker wordt gebruikt. |
| SourceRelativeUrl | De URL van de map met het bestand dat door de gebruiker wordt gebruikt. De combi natie van de waarden voor de para meter SiteURL, SourceRelativeURL en SourceFileName is hetzelfde als de waarde voor de eigenschap ObjectID, de volledige padnaam voor het bestand dat door de gebruiker wordt gebruikt. |
| UserSharedWith |     De gebruiker waarmee een resource is gedeeld. |




## <a name="sample-log-queries"></a>Voorbeeld logboek query's

De volgende tabel bevat voorbeeld logboek query's voor update records die door deze oplossing zijn verzameld.

| Query | Beschrijving |
| --- | --- |
|Aantal bewerkingen voor uw Office 365-abonnement |Aantal OfficeActivity &#124;-overzicht () per bewerking |
|Gebruik van share point-sites|OfficeActivity &#124; waarbij OfficeWorkload = ~ ' share point ' &#124; samenvattings aantal () door de waarde voor SiteUrl \| sorteren op aantal ASC|
|Bestands toegangs bewerkingen op gebruikers type | OfficeActivity &#124; samenvattings aantal () door User type |
|Externe acties op Exchange controleren|OfficeActivity &#124; waarbij OfficeWorkload = ~ "Exchange" en ExternalAccess = = True|



## <a name="next-steps"></a>Volgende stappen

* Gebruik [logboek query's in azure monitor](../logs/log-query-overview.md) om gedetailleerde update gegevens weer te geven.
* [Maak uw eigen Dash boards](../visualize/tutorial-logs-dashboards.md) om uw favoriete Office 365-Zoek query's weer te geven.
* [Maak waarschuwingen](../alerts/alerts-overview.md) om proactief te worden geïnformeerd over belang rijke Office 365-activiteiten.  
