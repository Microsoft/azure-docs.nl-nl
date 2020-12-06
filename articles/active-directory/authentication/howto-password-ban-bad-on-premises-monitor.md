---
title: On-premises Azure AD-wachtwoord beveiliging controleren
description: Meer informatie over het bewaken en controleren van Logboeken voor Azure AD-wachtwoord beveiliging voor een on-premises Active Directory Domain Services omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: edc246a414401c4c1c0248787eda0381fcd63037
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741759"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Logboeken controleren en bekijken voor on-premises Azure AD-wachtwoord beveiligings omgevingen

Na de implementatie van Azure AD-wachtwoord beveiliging, zijn bewaking en rapportage essentiële taken. In dit artikel vindt u meer informatie over verschillende bewakings technieken, waaronder waar elke service gegevens registreert en hoe u kunt rapporteren over het gebruik van Azure AD-wachtwoord beveiliging.

Bewaking en rapportage worden uitgevoerd door berichten in het gebeurtenis logboek of door Power shell-cmdlets uit te voeren. De DC-agent en proxy services registreren logboek berichten van gebeurtenissen. Alle Power shell-cmdlets die hieronder worden beschreven, zijn alleen beschikbaar op de proxy server (Zie de AzureADPasswordProtection Power shell-module). Met de DC-agent software wordt geen Power shell-module geïnstalleerd.

## <a name="dc-agent-event-logging"></a>Gebeurtenis registratie DC-agent

Op elke domein controller schrijft de DC-Agent service software de resultaten van elke afzonderlijke wachtwoord validatie bewerking (en andere status) naar een lokaal gebeurtenis logboek:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Het logboek van de DC-agent beheerder is de primaire bron van informatie voor de werking van de software.

Houd er rekening mee dat het tracerings logboek standaard uitgeschakeld is.

Gebeurtenissen die zijn vastgelegd door de verschillende onderdelen van de DC-agent vallen binnen de volgende bereiken:

|Onderdeel |Bereik gebeurtenis-ID|
| --- | --- |
|Dll voor wachtwoord filter van DC-agent| 10000-19999|
|Hosting proces van DC-Agent service| 20000-29999|
|Validatie logica van DC-Agent service beleid| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Gebeurtenis logboek van DC-agent beheerder

### <a name="password-validation-outcome-events"></a>Resultaat gebeurtenissen voor wachtwoord validatie

Op elke domein controller schrijft de DC Agent service-software de resultaten van elke afzonderlijke wachtwoord validatie naar het gebeurtenis logboek van de DC-agent beheerder.

Voor een geslaagde wachtwoord validatie is er meestal één gebeurtenis geregistreerd in het dll-bestand voor wachtwoord filter van de domein controller. Voor een mislukte wachtwoord validatie worden er in het algemeen twee gebeurtenissen geregistreerd, een van de DC-Agent service en een van de dll voor wachtwoord filtering van de DC-agent.

Discrete gebeurtenissen voor het vastleggen van deze situaties worden vastgelegd op basis van de volgende factoren:

* Hiermee wordt aangegeven of een bepaald wacht woord wordt ingesteld of gewijzigd.
* Hiermee wordt aangegeven of de validatie van een bepaald wacht woord is geslaagd of mislukt.
* Of de validatie is mislukt vanwege het globale beleid van micro soft, het organisatie beleid of een combi natie hiervan.
* Hiermee wordt aangegeven of de modus alleen controle momenteel is in-of uitgeschakeld voor het huidige wachtwoord beleid.

U hebt de volgende belang rijke gebeurtenissen met betrekking tot validatie op basis van wacht woorden:

| Gebeurtenis |Wachtwoord wijzigen |Wachtwoordset|
| --- | :---: | :---: |
|Geslaagd |10014 |10015|
|Mislukt (vanwege wachtwoord beleid van klant)| 10016, 30002| 10017, 30003|
|Mislukt (vanwege het wachtwoord beleid van micro soft)| 10016, 30004| 10017, 30005|
|Mislukt (door gecombineerde beleids regels van micro soft en het wacht woord van de klant)| 10016, 30026| 10017, 30027|
|Mislukt (vanwege gebruikers naam)| 10016, 30021| 10017, 30022|
|Alleen controle door gegeven (zou het beleid voor klant wachtwoord niet hebben kunnen uitvoeren)| 10024, 30008| 10025, 30007|
|Alleen controle door gegeven (zou het micro soft-wachtwoord beleid zouden hebben mislukt)| 10024, 30010| 10025, 30009|
|Alleen-controle door gegeven (zou zijn mislukt gecombineerd micro soft en het wachtwoord beleid van de klant)| 10024, 30028| 10025, 30029|
|Alleen controle door gegeven (zou zijn mislukt als gevolg van gebruikers naam)| 10016, 30024| 10017, 30023|

De gevallen in de tabel hierboven die verwijzen naar ' gecombineerd beleid ', verwijzen naar situaties waarbij het wacht woord van een gebruiker ten minste één token heeft gevonden uit de lijst met geblokkeerde wacht woorden van micro soft en de lijst met geblokkeerde wacht woorden van de klant.

De cases in de tabel hierboven die verwijzen naar ' gebruikers naam ', verwijzen naar situaties waarin het wacht woord van een gebruiker is gevonden en de account naam van de gebruiker en/of een van de beschrijvende namen van de gebruiker bevat. In beide gevallen wordt het wacht woord van de gebruiker geweigerd wanneer het beleid is ingesteld op afdwingen of door gegeven als het beleid zich in de controle modus bevindt.

Wanneer een paar gebeurtenissen samen worden geregistreerd, worden beide gebeurtenissen expliciet gekoppeld door dezelfde correlatie te hebben.

### <a name="password-validation-summary-reporting-via-powershell"></a>Overzicht van het rapporteren van wachtwoord validatie via Power shell

De `Get-AzureADPasswordProtectionSummaryReport` cmdlet kan worden gebruikt om een samen vatting van de activiteit voor wachtwoord validatie te maken. Een voor beeld van een uitvoer van deze cmdlet is als volgt:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Het bereik van de rapportage van de cmdlet kan worden beïnvloed met behulp van een van de para meters – forest,-Domain of – domain controller. Het opgeven van een para meter impliceert: forest.

De `Get-AzureADPasswordProtectionSummaryReport` cmdlet werkt met behulp van een query naar het gebeurtenis logboek van de DC-agent beheerder en telt vervolgens het totale aantal gebeurtenissen dat overeenkomt met elke weer gegeven resultaat categorie. De volgende tabel bevat de toewijzingen tussen de resultaten en de bijbehorende gebeurtenis-ID:

|Get-AzureADPasswordProtectionSummaryReport eigenschap |Bijbehorende gebeurtenis-ID|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Houd er rekening mee dat de `Get-AzureADPasswordProtectionSummaryReport` cmdlet wordt verzonden in Power shell-script vorm en dat indien nodig, rechtstreeks naar de volgende locatie kan worden verwezen:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Deze cmdlet werkt door een Power shell-sessie te openen voor elke domein controller. De ondersteuning voor externe Power shell-sessies moet zijn ingeschakeld op elke domein controller en de client moet voldoende bevoegdheden hebben om te kunnen slagen. Voor meer informatie over Power shell-vereisten voor externe sessies voert u Get-Help about_Remote_Troubleshooting in een Power shell-venster.

> [!NOTE]
> Met deze cmdlet wordt op afstand een query uitgevoerd voor elk beheer gebeurtenis logboek van de DC-Agent service. Als de gebeurtenis logboeken grote aantallen gebeurtenissen bevatten, kan het enige tijd duren voordat de cmdlet is voltooid. Bovendien kunnen bulksgewijze netwerk query's van grote gegevens sets van invloed zijn op de prestaties van de domein controller. Deze cmdlet moet daarom zorgvuldig worden gebruikt in productie omgevingen.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Voorbeeld bericht in gebeurtenis logboek voor gebeurtenis-ID 10014 (geslaagde wachtwoord wijziging)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Voorbeeld bericht in gebeurtenis logboek voor gebeurtenis-ID 10017 en 30003 (mislukte wachtwoord instelling)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Voorbeeld bericht van gebeurtenis logboek voor gebeurtenis-ID 30001 (wacht woord geaccepteerd omdat er geen beleid beschikbaar is)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Voorbeeld bericht in gebeurtenis logboek voor gebeurtenis-ID 30006 (nieuw beleid wordt afgedwongen)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Voorbeeld bericht van gebeurtenis logboek voor gebeurtenis-ID 30019 (Azure AD-wachtwoord beveiliging is uitgeschakeld)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Operationeel logboek van DC-agent

De DC-Agent service registreert ook operationele gerelateerde gebeurtenissen in het volgende logboek:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Traceer logboek DC-agent

De DC-Agent service kan ook uitgebreide gebeurtenissen voor fout opsporing op debugniveau vastleggen in het volgende logboek:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Traceer logboek registratie is standaard uitgeschakeld.

> [!WARNING]
> Wanneer deze functie is ingeschakeld, ontvangt het tracerings logboek een groot aantal gebeurtenissen en kan dit van invloed zijn op de prestaties van de domein controller. Daarom moet dit uitgebreide logboek alleen worden ingeschakeld als een probleem een grondig onderzoek vereist en vervolgens alleen gedurende een minimale hoeveelheid tijd.

## <a name="dc-agent-text-logging"></a>Tekst registratie van DC-agent

De DC-Agent service kan worden geconfigureerd om naar een tekst logboek te schrijven door de volgende register waarde in te stellen:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Tekst logboek registratie is standaard uitgeschakeld. De DC-Agent service moet opnieuw worden opgestart om wijzigingen in deze waarde van kracht te laten worden. Wanneer de DC-Agent service is ingeschakeld, wordt er naar een logboek bestand geschreven dat zich bevindt in:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Het tekst logboek ontvangt dezelfde vermeldingen voor fout opsporing die kunnen worden geregistreerd in het traceer logboek, maar is in het algemeen een eenvoudigere indeling om te controleren en analyseren.

> [!WARNING]
> Als deze functie is ingeschakeld, ontvangt u een groot aantal gebeurtenissen en kan dit van invloed zijn op de prestaties van de domein controller. Daarom moet dit uitgebreide logboek alleen worden ingeschakeld als een probleem een grondig onderzoek vereist en vervolgens alleen gedurende een minimale hoeveelheid tijd.

## <a name="dc-agent-performance-monitoring"></a>Bewaking van DC-agent prestaties

De DC Agent-service software installeert een prestatie meter object met de naam **Azure AD-wachtwoord beveiliging**. De volgende prestatie meter items zijn momenteel beschikbaar:

|Naam prestatie meter item | Beschrijving|
| --- | --- |
|Verwerkte wacht woorden |Met deze teller wordt het totale aantal verwerkte wacht woorden weer gegeven (geaccepteerd of afgewezen) sinds de laatste keer opnieuw opstarten.|
|Wacht woorden geaccepteerd |Met deze teller wordt het totale aantal wacht woorden weer gegeven dat sinds de laatste keer opnieuw is geaccepteerd.|
|Geweigerde wacht woorden |Met deze teller wordt het totale aantal wacht woorden weer gegeven dat is geweigerd sinds de laatste keer opnieuw is opgestart.|
|Wachtwoord filter aanvragen worden uitgevoerd |Met deze teller wordt het aantal aanvragen voor wachtwoord filter weer gegeven dat momenteel wordt uitgevoerd.|
|Piek aanvragen voor wachtwoord filter |Deze teller geeft het maximum aantal aanvragen voor het filteren van gelijktijdige wacht woorden weer sinds de laatste keer opnieuw opstarten.|
|Fouten in wachtwoord filter aanvragen |Met deze teller wordt het totale aantal aanvragen voor wachtwoord filter weer gegeven dat is mislukt vanwege een fout sinds de laatste keer opnieuw opstarten. Er kunnen fouten optreden wanneer de Azure AD-service voor wachtwoord beveiliging van de DC-agent niet wordt uitgevoerd.|
|Wachtwoord filter aanvragen per seconde |Deze teller geeft de snelheid weer waarmee wacht woorden worden verwerkt.|
|Verwerkings tijd wachtwoord filter aanvraag |Deze teller toont de gemiddelde tijd die nodig is voor het verwerken van een aanvraag voor wachtwoord filter.|
|Verwerkings tijd van aanvragen voor piek wachtwoord filter |Deze teller geeft de maximale verwerkings tijd voor wachtwoord filter aanvragen sinds de laatste keer opnieuw opstarten.|
|Wacht woorden die zijn geaccepteerd vanwege de controle modus |Met deze teller wordt het totale aantal wacht woorden weer gegeven dat normaal zou zijn afgewezen, maar wel werden geaccepteerd omdat het wachtwoord beleid is geconfigureerd voor de controle modus (sinds de laatste keer opnieuw opstarten).|

## <a name="dc-agent-discovery"></a>Detectie van DC-agent

De `Get-AzureADPasswordProtectionDCAgent` cmdlet kan worden gebruikt voor het weer geven van basis informatie over de verschillende DC-agents die worden uitgevoerd in een domein of forest. Deze informatie wordt opgehaald uit de serviceConnectionPoint-object (en) die zijn geregistreerd door de actieve DC-Agent service (s).

Een voor beeld van een uitvoer van deze cmdlet is als volgt:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

De verschillende eigenschappen worden op basis van elk uur bijgewerkt door elke DC-Agent service. Voor de gegevens is nog steeds Active Directory replicatie latentie van toepassing.

Het bereik van de query van de cmdlet kan worden beïnvloed door gebruik te maken van de para meters – forest of-domein.

Als de waarde van HeartbeatUTC verouderd is, kan dit een symptoom zijn dat de Azure AD-agent voor wachtwoord beveiliging op die domein controller niet wordt uitgevoerd, of verwijderd is, of dat de computer is gedegradeerd en niet langer een domein controller is.

Als de waarde van PasswordPolicyDateUTC verouderd is, kan dit een symptoom zijn dat de DC-agent voor de Azure AD-wachtwoord beveiliging op die computer niet goed werkt.

## <a name="dc-agent-newer-version-available"></a>Nieuwe versie van DC-agent beschikbaar

De DC-Agent service meldt een 30034-waarschuwings gebeurtenis aan het operationele logboek wanneer wordt gedetecteerd dat er een nieuwere versie van de DC-agent software beschikbaar is, bijvoorbeeld:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

De bovenstaande gebeurtenis geeft niet de versie van de nieuwere software op. Ga naar de koppeling in het gebeurtenis bericht voor deze informatie.

> [!NOTE]
> Ondanks de verwijzingen naar "autoupgrade" in het bovenstaande gebeurtenis bericht, biedt de DC-agent software momenteel geen ondersteuning voor deze functie.

## <a name="proxy-service-event-logging"></a>Logboek registratie van proxy service

De proxy service verzendt een minimale set gebeurtenissen naar de volgende gebeurtenis logboeken:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Houd er rekening mee dat het tracerings logboek standaard uitgeschakeld is.

> [!WARNING]
> Wanneer deze functie is ingeschakeld, ontvangt het tracerings logboek een groot aantal gebeurtenissen. Dit kan van invloed zijn op de prestaties van de proxy-host. Daarom moet dit logboek alleen worden ingeschakeld als een probleem een grondig onderzoek vereist en vervolgens alleen gedurende een minimale hoeveelheid tijd.

Gebeurtenissen worden door de verschillende proxy onderdelen geregistreerd met behulp van de volgende bereiken:

|Onderdeel |Bereik gebeurtenis-ID|
| --- | --- |
|Hosting proces van proxy service| 10000-19999|
|Bedrijfs logica voor de kern van proxy service| 20000-29999|
|PowerShell-cmdlets| 30000-39999|

## <a name="proxy-service-text-logging"></a>Logboek registratie van proxy service-tekst

De proxy service kan worden geconfigureerd om naar een tekst logboek te schrijven door de volgende register waarde in te stellen:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD waarde)

Tekst logboek registratie is standaard uitgeschakeld. De proxy service moet opnieuw worden opgestart om wijzigingen in deze waarde van kracht te laten worden. Wanneer de proxy service is ingeschakeld, wordt er naar een logboek bestand geschreven dat zich bevindt in:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Het tekst logboek ontvangt dezelfde vermeldingen voor fout opsporing die kunnen worden geregistreerd in het traceer logboek, maar is in het algemeen een eenvoudigere indeling om te controleren en analyseren.

> [!WARNING]
> Wanneer deze functie is ingeschakeld, ontvangt het logboek een groot aantal gebeurtenissen en kan dit van invloed zijn op de prestaties van de machine. Daarom moet dit uitgebreide logboek alleen worden ingeschakeld als een probleem een grondig onderzoek vereist en vervolgens alleen gedurende een minimale hoeveelheid tijd.

## <a name="powershell-cmdlet-logging"></a>Logboek registratie voor Power shell-cmdlets

Power shell-cmdlets die resulteren in een status wijziging (bijvoorbeeld REGI ster-AzureADPasswordProtectionProxy), registreren normaal gesp roken een resultaat gebeurtenis in het operationele logboek.

Daarnaast schrijft de meeste van de Azure AD-cmdlets voor wachtwoord beveiliging Power shell naar een tekst logboek dat zich bevindt in:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Als er een cmdlet-fout optreedt en de oorzaak van de en/of-oplossing niet direct zichtbaar is, kunnen deze tekst logboeken ook worden geraadpleegd.

## <a name="proxy-discovery"></a>Proxy detectie

De `Get-AzureADPasswordProtectionProxy` cmdlet kan worden gebruikt voor het weer geven van basis informatie over de verschillende Azure AD-Services voor wachtwoord beveiliging die worden uitgevoerd in een domein of forest. Deze informatie wordt opgehaald uit de serviceConnectionPoint-object (en) die zijn geregistreerd door de actieve proxy service (s).

Een voor beeld van een uitvoer van deze cmdlet is als volgt:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

De verschillende eigenschappen worden op basis van elk uur bijgewerkt door elke proxy service. Voor de gegevens is nog steeds Active Directory replicatie latentie van toepassing.

Het bereik van de query van de cmdlet kan worden beïnvloed door gebruik te maken van de para meters – forest of-domein.

Als de waarde van HeartbeatUTC verouderd is, kan dit een symptoom zijn dat de Azure AD-wachtwoord beveiligings proxy op die computer niet actief is of is verwijderd.

## <a name="proxy-agent-newer-version-available"></a>Nieuwere versie van Proxy agent beschikbaar

De proxy service meldt een 20002-waarschuwings gebeurtenis aan het operationele logboek op wanneer wordt gedetecteerd dat er een nieuwere versie van de proxy software beschikbaar is, bijvoorbeeld:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

De bovenstaande gebeurtenis geeft niet de versie van de nieuwere software op. Ga naar de koppeling in het gebeurtenis bericht voor deze informatie.

Deze gebeurtenis wordt ook verzonden als de proxy-agent is geconfigureerd met automatisch upgraden ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

[Probleem oplossing voor Azure AD-wachtwoord beveiliging](howto-password-ban-bad-on-premises-troubleshoot.md)

Voor meer informatie over de algemene en aangepaste lijst met verboden wacht woorden raadpleegt u het artikel [been](concept-password-ban-bad.md)
