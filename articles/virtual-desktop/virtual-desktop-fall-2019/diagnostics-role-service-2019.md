---
title: Problemen met Windows Virtual Desktop (klassiek) vaststellen-Azure
description: De Windows-functie voor virtueel bureau blad (klassiek) gebruiken om problemen te diagnosticeren.
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e23a1e9a2a0118402df0d9b8869f170762a52284
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444952"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>Problemen in Windows virtueel bureau blad (klassiek) identificeren en onderzoeken

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../diagnostics-role-service.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Virtueel bureau blad van Windows biedt een functie voor diagnostische gegevens waarmee de beheerder problemen kan identificeren via één interface. De Windows-functies voor virtuele Bureau bladen registreren een diagnostische activiteit wanneer een gebruiker met het systeem communiceert. Elk logboek bevat relevante informatie zoals de Windows-functies voor virtueel bureau blad die betrokken zijn bij de trans actie, fout berichten, Tenant gegevens en gebruikers informatie. Diagnostische activiteiten worden gemaakt door eind gebruikers en administratieve acties en kunnen worden gecategoriseerd in drie hoofd verzamelingen:

* Activiteiten voor feed-abonnementen: de eind gebruiker activeert deze activiteiten wanneer ze verbinding proberen te maken met hun feed via Microsoft Extern bureaublad toepassingen.
* Verbindings activiteiten: de eind gebruiker activeert deze activiteiten wanneer ze verbinding proberen te maken met een bureau blad of RemoteApp via Microsoft Extern bureaublad toepassingen.
* Beheer activiteiten: de beheerder activeert deze activiteiten wanneer ze beheer bewerkingen uitvoeren op het systeem, zoals het maken van hostgroepen, het toewijzen van gebruikers aan app-groepen en het maken van roltoewijzingen.

Verbindingen die zich niet in het virtuele bureau blad van Windows bevinden, worden niet weer gegeven in de resultaten van diagnostische gegevens omdat de functie Service diagnostiek zelf deel uitmaakt van Windows virtueel bureau blad. Er kunnen problemen met de Windows-verbinding met virtueel bureau blad optreden wanneer de eind gebruiker problemen met de netwerk verbinding ondervindt.

Als u aan de slag wilt gaan, [downloadt en importeert u de Windows Virtual Desktop Power shell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan. Voer hierna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Problemen vaststellen met Power shell

De diagnostische gegevens van Windows virtueel bureau blad maakt gebruik van slechts één Power shell-cmdlet, maar bevat veel optionele para meters om problemen te beperken en te isoleren. De volgende secties bevatten een lijst met de cmdlets die u kunt uitvoeren om problemen vast te stellen. De meeste filters kunnen samen worden toegepast. Waarden die worden weer gegeven tussen vier Kante haken, zoals `<tenantName>` , moeten worden vervangen door de waarden die van toepassing zijn op uw situatie.

>[!IMPORTANT]
>De functie diagnostische gegevens is voor het oplossen van problemen met één gebruiker. Alle query's die gebruikmaken van Power shell moeten de para meters *-username* of *-ActivityID* bevatten. Gebruik Log Analytics voor bewakings mogelijkheden. Zie [log Analytics voor de diagnostische functie gebruiken](diagnostics-log-analytics-2019.md) voor meer informatie over het verzenden van diagnostische gegevens naar uw werk ruimte.

### <a name="filter-diagnostic-activities-by-user"></a>Diagnostische activiteiten filteren op gebruiker

De para meter **-username** retourneert een lijst met diagnostische activiteiten die door de opgegeven gebruiker zijn gestart, zoals wordt weer gegeven in de volgende voor beeld-cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

De para meter **-username** kan ook worden gecombineerd met andere optionele filter parameters.

### <a name="filter-diagnostic-activities-by-time"></a>Diagnostische activiteiten filteren op tijd

U kunt de geretourneerde lijst met diagnostische activiteiten filteren met de para meter **-StartTime** en **-EndTime** . De para meter **-StartTime** retourneert een lijst met diagnostische activiteiten vanaf een specifieke datum, zoals wordt weer gegeven in het volgende voor beeld.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

De para meter **-EndTime** kan worden toegevoegd aan een cmdlet met de para meter **-StartTime** om een specifieke periode op te geven waarvoor u resultaten wilt ontvangen. De volgende voor beeld-cmdlet retourneert een lijst met diagnostische activiteiten van 1 augustus tot en met 10 augustus.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

De para meters **-StartTime** en **-EndTime** kunnen ook worden gecombineerd met andere optionele filter parameters.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Diagnostische activiteiten filteren op activiteitstype

U kunt ook diagnostische activiteiten filteren op activiteitstype met de para meter **-Activity** type. Met de volgende cmdlet wordt een lijst met eind gebruikers verbindingen geretourneerd:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Met de volgende cmdlet wordt een lijst met beheerders beheer taken geretourneerd:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

De cmdlet **Get-RdsDiagnosticActivities** biedt momenteel geen ondersteuning voor het opgeven van feeds als activity type.

### <a name="filter-diagnostic-activities-by-outcome"></a>Diagnostische activiteiten filteren op resultaat

U kunt de geretourneerde lijst met diagnostische activiteiten filteren op uitkomst met de para meter **-Result** . Met de volgende voorbeeld cmdlet wordt een lijst met geslaagde diagnostische activiteiten geretourneerd.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

Met de volgende voor beeld-cmdlet wordt een lijst met mislukte diagnostische activiteiten geretourneerd.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

De para meter **-resultaat** kan ook worden gecombineerd met andere optionele filter parameters.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Een specifieke diagnostische activiteit ophalen op basis van de activiteits-ID

De para meter **-ActivityId** retourneert een specifieke diagnostische activiteit als deze bestaat, zoals wordt weer gegeven in de volgende voor beeld-cmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Fout berichten weer geven voor een mislukte activiteit op activiteits-ID

Als u de fout berichten voor een mislukte activiteit wilt weer geven, moet u de cmdlet uitvoeren met de para meter **-detail** . U kunt de lijst met fouten weer geven door de **Select-object-** cmdlet uit te voeren.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Gedetailleerde diagnostische activiteiten ophalen

De para meter **-detail** bevat aanvullende informatie voor elke geretourneerde diagnostische activiteit. De notatie voor elke activiteit is afhankelijk van het type activiteit. De para meter **-detail** kan worden toegevoegd aan een **Get-RdsDiagnosticActivities-** query, zoals wordt weer gegeven in het volgende voor beeld.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Veelvoorkomende foutscenario's

Fout scenario's worden intern gecategoriseerd voor de service en extern naar virtueel bureau blad van Windows.

* Intern probleem: Hiermee geeft u scenario's op die niet kunnen worden verholpen door de Tenant beheerder en moeten worden omgezet als ondersteunings probleem. Wanneer u feedback geeft via de [technische community van het Windows-bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), neemt u de activiteits-id en het geschatte tijds bestek op wanneer het probleem is opgetreden.
* Extern probleem: gerelateerd aan scenario's die door de systeem beheerder kunnen worden verholpen. Dit zijn externe virtuele Bureau bladen van Windows.

De volgende tabel bevat algemene fouten die door uw beheerders kunnen worden uitgevoerd.

>[!NOTE]
>Deze lijst bevat de meest voorkomende fouten en wordt bijgewerkt op een gewone uitgebracht. Om ervoor te zorgen dat u de meest recente informatie hebt, moet u dit artikel ten minste één keer per maand opnieuw controleren.

### <a name="external-management-error-codes"></a>Externe beheer fout codes

|Numerieke code|Foutcode|Voorgestelde oplossing|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|De gebruiker is geen lid van Azure Active Directory. Volg de instructies in [Active Directory-beheercentrum](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) om ze toe te voegen.|
|3|UnauthorizedAccess|De gebruiker die de Power shell-cmdlet voor beheer heeft geprobeerd uit te voeren, heeft geen machtigingen om dit te doen of heeft een niet-getypte gebruikers naam.|
|1000|TenantNotFound|De naam van de Tenant die u hebt ingevoerd, komt niet overeen met bestaande tenants. Controleer de naam van de Tenant voor type fouten en probeer het opnieuw.|
|1006|TenantCannotBeRemovedHasSessionHostPools|U kunt een Tenant niet verwijderen zolang deze objecten bevat. Verwijder eerst de Session Host-Pools en probeer het opnieuw.|
|2000|HostPoolNotFound|De naam van de hostgroep die u hebt opgegeven, komt niet overeen met de bestaande hostgroepen. Controleer de naam van de hostgroep op typfouten en probeer het opnieuw.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|U kunt een hostgroep niet verwijderen zolang deze objecten bevat. Verwijder eerst alle app-groepen in de hostgroep.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Verwijder eerst alle sessies hosts voordat u de groep Session Host verwijdert.|
|5001|SessionHostNotFound|De sessiemap die u hebt opgevraagd, is mogelijk offline. Controleer de status van de hostgroep.|
|5008|SessionHostUserSessionsExist |U moet alle gebruikers op de sessiehost afmelden voordat u de beoogde beheer activiteit uitvoert.|
|6000|AppGroupNotFound|De naam van de app-groep die u hebt ingevoerd, komt niet overeen met de bestaande app-groepen. Controleer de naam van de app-groep op type fouten en probeer het opnieuw.|
|6022|RemoteAppNotFound|De opgegeven RemoteApp-naam komt niet overeen met de RemoteApps. Controleer de RemoteApp-naam voor type fouten en probeer het opnieuw.|
|6010|PublishedItemsExist|De naam van de resource die u wilt publiceren, is hetzelfde als een resource die al bestaat. Wijzig de resource naam en probeer het opnieuw.|
|7002|NameNotValidWhiteSpace|Gebruik geen spaties in de naam.|
|8000|InvalidAuthorizationRoleScope|De naam van de rol die u hebt ingevoerd, komt niet overeen met de namen van bestaande rollen. Controleer de rolnaam voor type fouten en probeer het opnieuw. |
|8001|UserNotFound |De gebruikers naam die u hebt ingevoerd, komt niet overeen met bestaande gebruikers namen. Controleer de naam voor type fouten en probeer het opnieuw.|
|8005|UserNotFoundInAAD |De gebruikers naam die u hebt ingevoerd, komt niet overeen met bestaande gebruikers namen. Controleer de naam voor type fouten en probeer het opnieuw.|
|8008|TenantConsentRequired|Volg de instructies [hier](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) om toestemming te geven voor uw Tenant.|

### <a name="external-connection-error-codes"></a>Fout codes voor externe verbindingen

|Numerieke code|Foutcode|Voorgestelde oplossing|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|De gebruiker is geen lid van Active Directory. Volg de instructies in [Active Directory-beheercentrum](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) om ze toe te voegen.|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|De sessiehost is niet juist gekoppeld aan de Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|De verbindingen zijn mislukt omdat de sessiehost niet beschikbaar is. Controleer de status van de sessie-host.|
|-2146233088|ConnectionFailedClientDisconnect|Als deze fout regel matig wordt weer geven, controleert u of de computer van de gebruiker is verbonden met het netwerk.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|De sessie waarmee de host-gebruiker probeerde verbinding te maken, is niet in orde. Fouten opsporen in de virtuele machine.|
|-2146233088|ConnectionFailedUserNotAuthorized|De gebruiker is niet gemachtigd om toegang te krijgen tot de gepubliceerde app of het bureau blad. De fout kan worden weer gegeven nadat de beheerder gepubliceerde resources heeft verwijderd. Vraag de gebruiker om de feed in de Extern bureaublad toepassing te vernieuwen.|
|2|FileNotFound|De toepassing waartoe de gebruiker toegang probeerde te krijgen, is onjuist geïnstalleerd of ingesteld op een onjuist pad.|
|3|InvalidCredentials|De gebruikers naam of het wacht woord die de gebruiker heeft ingevoerd, komt niet overeen met de bestaande gebruikers namen of wacht woorden. Controleer de referenties voor type fouten en probeer het opnieuw.|
|8|ConnectionBroken|De verbinding tussen de client en de gateway of server is verbroken. Er is geen actie vereist, tenzij deze onverwacht plaatsvindt.|
|14|UnexpectedNetworkDisconnect|De verbinding met het netwerk is verbroken. Vraag de gebruiker om opnieuw verbinding te maken.|
|24|ReverseConnectFailed|De virtuele machine van de host heeft geen directe regel van het gezichts vermogen om te RD-gateway. Zorg ervoor dat het IP-adres van de gateway kan worden omgezet.|
|1322|ConnectionFailedNoMappingOfSIDinAD|De gebruiker is geen lid van Active Directory. Volg de instructies in [Active Directory-beheercentrum](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center) om ze toe te voegen.|

## <a name="next-steps"></a>Volgende stappen

Zie [Windows Virtual Desktop Environment](environment-setup-2019.md)(Engelstalig) voor meer informatie over de functies van virtuele Bureau bladen van Windows.

Zie de [Power shell-referentie](/powershell/windows-virtual-desktop/overview)voor een lijst met beschik bare Power shell-cmdlets voor virtuele Windows-Bureau bladen.
