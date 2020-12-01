---
title: AADCloudSyncTools Power shell-module voor Azure AD Connect Cloud inrichting
description: In dit artikel wordt beschreven hoe u de Azure AD Connect Cloud-inrichtings Agent installeert.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba71d3eedbb75780c20499daa890556574494a35
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348134"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-provisioning"></a>AADCloudSyncTools Power shell-module voor Azure AD Connect Cloud inrichting

Met de release van open bare preview vernieuwen 2 heeft micro soft de AADCloudSyncTools Power shell-module geïntroduceerd.  Deze module biedt een aantal nuttige hulpprogram ma's die u kunt gebruiken om uw Azure AD Connect Cloud synchronisatie-implementaties te beheren.

## <a name="pre-requisites"></a>Vereisten
De volgende vereisten zijn vereist:
- Deze module maakt gebruik van MSAL-verificatie, zodat de module MSAL.PS is geïnstalleerd. Het is niet langer afhankelijk van de preview-versie van Azure AD of Azure AD.   Als u wilt controleren, voert u in een Power shell-venster voor beheerders uit `Get-module MSAL.PS` . Als de module correct is geïnstalleerd, ontvangt u een antwoord.  U kunt gebruiken `Install-AADCloudSyncToolsPrerequisites` voor het installeren van de meest recente versie van MSAL.PS
- De AzureAD Power shell-module.  Sommige van de cmdlets zijn afhankelijk van de onderdelen van de AzureAD Power shell-module om hun taken uit te voeren.  Als u wilt controleren, voert u in een Power shell-venster voor beheer uit `Get-module AzureAD` . U ontvangt een antwoord.  U kunt gebruiken `Install-AADCloudSyncToolsPrerequisites` om de meest recente versie van de AzureAD Power shell-module te installeren.
- Het installeren van modules vanuit Power shell kan afdwingen met behulp van TLS 1,2.  Stel het volgende in om ervoor te zorgen dat u modules kunt installeren: \
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 `

## <a name="install-the-aadcloudsynctools-powershell-module"></a>De AADCloudSyncTools Power shell-module installeren
Voer de volgende stappen uit om de AADCloudSyncTools-module te installeren en gebruiken:

1.  Open Windows Power shell met beheerders bevoegdheden
2.  Typ `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` en druk op ENTER.
3.  Typ of kopieer en plak het volgende: 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
    ```
3.  Druk op ENTER.
4.  Als u wilt controleren of de module is geïnstalleerd, voert u de volgende in of kopieert en plakt u deze:
    ```powershell
    Get-module AADCloudSyncTools
    ```
5.  U ziet nu informatie over de module.
6.  Volgende uitvoering
    ``` powershell
    Install-AADCloudSyncToolsPrerequisites
    ```
7.  Hiermee worden de Power shell-modules ophalen geïnstalleerd.  Sluit het Power shell-venster.
8.  Open Windows Power shell met beheerders bevoegdheden
9.  Importeer de module opnieuw met behulp van stap 3.
10. Uitvoeren `Install-AADCloudSyncToolsPrerequisites` om de MSAL-en AzureAD-modules te installeren
11. Alle pre-reqs moeten zijn geïnstalleerd ![ installatie module installeren](media/reference-powershell/install-1.png)

## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools-cmdlets
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Maakt gebruik van de AzureAD-module om verbinding te maken met Azure AD en de MSAL.PS-module om een token aan te vragen voor Microsoft Graph


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exporteert en verpakt alle gegevens voor het oplossen van problemen in een gecomprimeerd bestand, als volgt:
 1. Hiermee start u een uitgebreide tracering met start-AADCloudSyncToolsVerboseLogs.  U vindt deze traceerlogboeken in de map C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. Hiermee wordt drie minuten een traceer logboek verzameld.
   U kunt een andere tijd opgeven met-TracingDurationMins of uitgebreide tracering overs laan met-SkipVerboseTrace
 3. Hiermee wordt uitgebreide tracering met Stop-AADCloudSyncToolsVerboseLogs gestopt
 4. Verzamelt Logboeken Logboeken gedurende de afgelopen 24 uur
 5. Comprimeert alle Agent logboeken, uitgebreide logboeken en logboeken worden geregistreerd in een gecomprimeerd zip-bestand in de map documenten van de gebruiker. 
 </br>U kunt een andere uitvoermap met-OutputPath opgeven \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Status van Azure AD-Tenant en interne variabelen weer geven

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Maakt gebruik van Graph om AD2AAD-service-principals op te halen en de informatie over de synchronisatie taak te retour neren.
Kan ook worden aangeroepen met behulp van de specifieke ID van de synchronisatie taak als een para meter.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Maakt gebruik van Graph om AD2AAD-service-principals op te halen en de planning van de synchronisatie taak te retour neren.
Kan ook worden aangeroepen met behulp van de specifieke ID van de synchronisatie taak als een para meter.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Maakt gebruik van Graph om AD2AAD-service-principals op te halen en het schema van de synchronisatie taak te retour neren.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Maakt gebruik van een grafiek om het schema van de synchronisatie taak voor de gegeven synchronisatie taak-ID op te halen en de bereiken van alle filter groepen uit te voeren.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Maakt gebruik van Graph voor het ophalen van AD2AAD-service-principals en het retour neren van de instellingen van de synchronisatie taak.
Kan ook worden aangeroepen met behulp van de specifieke ID van de synchronisatie taak als een para meter.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Maakt gebruik van Graph om AD2AAD-service-principals op te halen en de status van de synchronisatie taak te retour neren.
Kan ook worden aangeroepen met behulp van de specifieke ID van de synchronisatie taak als een para meter.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Maakt gebruik van Graph om de Service-Principal (s) voor AD2AAD en/of SyncFabric te verkrijgen.
Zonder para meters wordt alleen AD2AAD Service-Principal (s) geretourneerd.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Controleert op de aanwezigheid van PowerShellGet v 2.2.4.1 of hoger en Azure AD en MSAL.PS modules en installeert deze als deze ontbreken.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Hiermee wordt een webaanvraag aangeroepen voor de URI, de methode en de hoofd tekst die zijn opgegeven als para meters

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Maakt gebruik van Azure AD Power shell om het huidige account (indien aanwezig) te verwijderen en de verificatie van het synchronisatie account opnieuw in te stelt met een nieuw synchronisatie account in azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Hiermee wordt een volledige synchronisatie opnieuw gestart.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
De synchronisatie van het vorige water merk voortzetten.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Hiermee wijzigt u de AADConnectProvisioningAgent.exe.config om uitgebreide tracering in te scha kelen en de AADConnectProvisioningAgent-service opnieuw te starten, kunt u-SkipServiceRestart gebruiken om te voor komen dat de service opnieuw wordt gestart, maar eventuele configuratie wijzigingen worden niet doorgevoerd.  U vindt deze traceerlogboeken in de map C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Hiermee wijzigt u de AADConnectProvisioningAgent.exe.config om uitgebreide tracering uit te scha kelen en de AADConnectProvisioningAgent-service opnieuw te starten. U kunt-SkipServiceRestart gebruiken om te voor komen dat de service opnieuw wordt gestart, maar eventuele configuratie wijzigingen worden niet doorgevoerd.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Hiermee wordt de synchronisatie onderbroken.

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

