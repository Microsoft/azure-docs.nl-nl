---
title: Controle en logboek registratie-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Meer informatie over het beperken van controle en logboek registratie in de Threat Modeling Tool. Zie informatie over risico beperking en voor beelden van code weer geven.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 9d3f3ca7b5d4516c2ad5dc9cb19a2eaed0a8a4a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518274"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Beveiligings frame: controle en logboek registratie | Oplossingen 

| Product/service | Artikel |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Identificeer gevoelige entiteiten in uw oplossing en implementeer wijzigingen controleren](#sensitive-entities)</li></ul> |
| **Webtoepassing** | <ul><li>[Controleren of controle en logboek registratie wordt afgedwongen voor de toepassing](#auditing)</li><li>[Zorg ervoor dat de draai hoek van het logboek en de schei ding worden geplaatst](#log-rotation)</li><li>[Zorg ervoor dat de toepassing geen gevoelige gebruikers gegevens registreert](#log-sensitive-data)</li><li>[Zorg ervoor dat audit-en logboek bestanden beperkte toegang hebben](#log-restricted-access)</li><li>[Zorg ervoor dat gebruikers beheer gebeurtenissen worden geregistreerd](#user-management)</li><li>[Zorg ervoor dat het systeem ingebouwde verdedigings tegen misbruik heeft](#inbuilt-defenses)</li><li>[Diagnostische logboek registratie inschakelen voor web-apps in Azure App Service](#diagnostics-logging)</li></ul> |
| **Database** | <ul><li>[Zorg ervoor dat aanmeldings controle is ingeschakeld op SQL Server](#identify-sensitive-entities)</li><li>[Bedreigings detectie op Azure SQL inschakelen](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Azure Opslaganalyse gebruiken om de toegang van Azure Storage te controleren](#analytics)</li></ul> |
| **WCF** | <ul><li>[Voldoende logboek registratie implementeren](#sufficient-logging)</li><li>[Voldoende verwerking van de controle fout implementeren](#audit-failure-handling)</li></ul> |
| **Web-API** | <ul><li>[Controleren of controle en logboek registratie wordt afgedwongen voor web-API](#logging-web-api)</li></ul> |
| **IoT-veld Gateway** | <ul><li>[Zorg ervoor dat de juiste controle en logboek registratie wordt afgedwongen op de veld Gateway](#logging-field-gateway)</li></ul> |
| **IoT-Cloud gateway** | <ul><li>[Zorg ervoor dat de juiste controle en logboek registratie wordt afgedwongen op de Cloud gateway](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Identificeer gevoelige entiteiten in uw oplossing en implementeer wijzigingen controleren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Dynamics CRM | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen**                   | Entiteiten in uw oplossing identificeren die gevoelige gegevens bevatten en controle van wijzigingen voor deze entiteiten en velden implementeren |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Controleren of controle en logboek registratie wordt afgedwongen voor de toepassing

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen**                   | Schakel controle en logboek registratie in voor alle onderdelen. In de controle logboeken moet de gebruikers context worden vastgelegd. Alle belang rijke gebeurtenissen identificeren en deze gebeurtenissen registreren. Centrale logboek registratie implementeren |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Zorg ervoor dat de draai hoek van het logboek en de schei ding worden geplaatst

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen**                   | <p>De draai hoek van het logboek is een geautomatiseerd proces dat wordt gebruikt in systeem beheer waarin gedateerde logboek bestanden worden gearchiveerd. Servers die grote toepassingen uitvoeren, registreren vaak elke aanvraag: in het geval van bulksgewijs Logboeken is logboek rotatie een manier om de totale grootte van de logboeken te beperken terwijl er nog steeds analyse van recente gebeurtenissen wordt toegestaan. </p><p>Logboek scheiding in principe houdt in dat u uw logboek bestanden op een andere partitie moet opslaan als waar uw besturings systeem of toepassing wordt uitgevoerd om een DOS-aanval (Denial of service) te voor komen of de downgrade van de prestaties van uw toepassing</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Zorg ervoor dat de toepassing geen gevoelige gebruikers gegevens registreert

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen**                   | <p>Controleer of u geen gevoelige gegevens in het logboek registreert die een gebruiker naar uw site verzendt. Controleren op opzettelijke logboek registratie en neven effecten die zijn ontstaan door ontwerp problemen. Voor beelden van gevoelige gegevens zijn:</p><ul><li>Gebruikers referenties</li><li>Sociaal-fiscaal nummer of andere identificerende informatie</li><li>Creditcard nummers of andere financiële gegevens</li><li>Status informatie</li><li>Persoonlijke sleutels of andere gegevens die kunnen worden gebruikt voor het ontsleutelen van versleutelde informatie</li><li>Systeem-of toepassings informatie die kan worden gebruikt om de toepassing effectiever te aanvallen</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Zorg ervoor dat audit-en logboek bestanden beperkte toegang hebben

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen**                   | <p>Controleer of de toegangs rechten voor de logboek bestanden op de juiste wijze zijn ingesteld. Toepassings accounts moeten alleen-schrijven toegang hebben en Opera tors en ondersteunings personeel mogen alleen-lezen toegang hebben als dat nodig is.</p><p>Beheerders accounts zijn de enige accounts die volledige toegang moeten hebben. Controleer Windows ACL op logboek bestanden om te controleren of ze goed zijn beperkt:</p><ul><li>Toepassings accounts moeten alleen-schrijven toegang hebben</li><li>Opera tors en ondersteunings medewerkers mogen alleen-lezen toegang hebben als dat nodig is</li><li>Beheerders zijn de enige accounts die volledige toegang moeten hebben</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Zorg ervoor dat gebruikers beheer gebeurtenissen worden geregistreerd

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen**                   | <p>Zorg ervoor dat de toepassing gebruikers beheer gebeurtenissen bewaakt, zoals geslaagde en mislukte gebruikers aanmeldingen, wacht woorden opnieuw instellen, wachtwoord wijzigingen, account vergrendeling, gebruikers registratie. Dit helpt om mogelijk verdacht gedrag te detecteren en te reageren. Er kunnen ook operationele gegevens worden verzameld. bijvoorbeeld om bij te houden wie toegang heeft tot de toepassing</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Zorg ervoor dat het systeem ingebouwde verdedigings tegen misbruik heeft

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen**                   | <p>Er moeten besturings elementen aanwezig zijn die de beveiligings uitzondering veroorzaken in het geval van misbruik van toepassingen. Bijvoorbeeld, als de invoer validatie is ingesteld en een aanvaller probeert schadelijke code te injecteren die niet overeenkomt met de regex, kan er een beveiligings uitzondering worden gegenereerd die een indicatie van misbruik van het systeem kan zijn</p><p>U kunt bijvoorbeeld het beste beveiligings uitzonderingen hebben die zijn geregistreerd en acties die worden uitgevoerd voor de volgende problemen:</p><ul><li>Invoervalidatie</li><li>CSRF schendingen</li><li>Brute kracht (bovengrens voor het aantal aanvragen per gebruiker per resource)</li><li>Schendingen van bestand uploaden</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Diagnostische logboek registratie inschakelen voor web-apps in Azure App Service

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | EnvironmentType-Azure |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Azure bevat ingebouwde diagnose functies voor hulp bij het opsporen van fouten in een App Service web-app. Het is ook van toepassing op API apps en mobiele apps. App Service web-apps bieden diagnostische functionaliteit voor het vastleggen van gegevens van zowel de webserver als de webtoepassing.</p><p>Deze worden logisch gescheiden in webserver diagnostiek en toepassings diagnoses</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Zorg ervoor dat aanmeldings controle is ingeschakeld op SQL Server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Aanmeldingscontrole configureren](/sql/ssms/configure-login-auditing-sql-server-management-studio) |
| **Stappen** | <p>De controle van de database server aanmelding moet zijn ingeschakeld voor het detecteren/bevestigen van wacht woorden. Het is belang rijk om mislukte aanmeldings pogingen vast te leggen. Het vastleggen van zowel geslaagde als mislukte aanmeldings pogingen biedt een extra voor deel tijdens forensische onderzoek</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Bedreigings detectie op Azure SQL inschakelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | SQL Azure |
| **Kenmerken**              | SQL-versie-V12 |
| **Referenties**              | [Aan de slag met SQL Database detectie van bedreigingen](../../azure-sql/database/threat-detection-configure.md)|
| **Stappen** |<p>Met detectie van bedreigingen worden afwijkende database activiteiten gedetecteerd die potentiële beveiligings dreigingen voor de data base aangeven. Het biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop reageren zodra ze zich voordoen door beveiligings waarschuwingen te geven over afwijkende activiteiten.</p><p>Gebruikers kunnen de verdachte gebeurtenissen verkennen met behulp van Azure SQL Database controle om te bepalen of ze het resultaat zijn van een poging toegang te krijgen tot gegevens in de data base.</p><p>Met detectie van bedreigingen kunt u eenvoudig mogelijke dreigingen voor de data base aanpakken zonder dat u een beveiligings expert hoeft te zijn of om geavanceerde beveiligings bewakings systemen te beheren</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Azure Opslaganalyse gebruiken om de toegang van Azure Storage te controleren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Azure Storage | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t. |
| **Referenties**              | [Het verificatie type bewaken met behulp van Opslaganalyse](../../storage/blobs/security-recommendations.md#loggingmonitoring) |
| **Stappen** | <p>Voor elk opslag account kan een Azure Opslaganalyse in staat stellen om gegevens in het logboek in te voeren en op te slaan. De logboeken van de opslag analyse bevatten belang rijke informatie zoals de verificatie methode die door iemand wordt gebruikt bij het openen van opslag.</p><p>Dit kan erg nuttig zijn als u de toegang tot opslag nauw keurig kunt beveiligen. In Blob Storage kunt u bijvoorbeeld alle containers instellen op privé en het gebruik van een SAS-service in uw toepassingen implementeren. Vervolgens kunt u de logboeken regel matig controleren om te zien of de blobs toegankelijk zijn via de sleutels voor het opslag account, die kunnen wijzen op een schending van de beveiliging, of als de blobs openbaar zijn, maar ze niet moeten worden gebruikt.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Voldoende logboek registratie implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | .NET Framework |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Konink rijk](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Stappen** | <p>Het ontbreken van een juiste audittrail na een veiligheids incident kan forensischee inspanningen belemmeren. Windows Communication Foundation (WCF) biedt de mogelijkheid om geslaagde en/of mislukte verificatie pogingen te registreren.</p><p>Bij het vastleggen van mislukte verificatie pogingen kunnen beheerders worden gewaarschuwd voor mogelijke beveiligings aanvallen. Op dezelfde manier kan het vastleggen van geslaagde verificatie gebeurtenissen een nuttig controlepad bieden wanneer een rechtmatig account is aangetast. De beveiligings controle functie van de WCF-service inschakelen |

### <a name="example"></a>Voorbeeld
Hier volgt een voor beeld van een configuratie waarvoor controle is ingeschakeld
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Voldoende verwerking van de controle fout implementeren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | WCF | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | .NET Framework |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Konink rijk](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Stappen** | <p>De ontwikkelde oplossing is zo geconfigureerd dat er geen uitzonde ring wordt gegenereerd wanneer er niet naar een audit logboek kan worden geschreven. Als WCF zodanig is geconfigureerd dat er geen uitzonde ring optreedt wanneer het niet kan schrijven naar een audit logboek, wordt het programma niet op de hoogte gesteld van de fout en controle van kritieke beveiligings gebeurtenissen.</p>|

### <a name="example"></a>Voorbeeld
Het `<behavior/>` element van het WCF-configuratie bestand hieronder geeft aan dat WCF de toepassing niet kan waarschuwen wanneer WCF niet naar een audit logboek kan schrijven.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Configureer WCF om het programma op de hoogte te stellen wanneer het niet kan schrijven naar een audit logboek. Het programma moet een alternatief meldings schema hebben om de organisatie te waarschuwen dat de controle sporen niet worden onderhouden. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Controleren of controle en logboek registratie wordt afgedwongen voor web-API

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Web-API | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Schakel controle en logboek registratie in op Web-Api's. In de controle logboeken moet de gebruikers context worden vastgelegd. Alle belang rijke gebeurtenissen identificeren en deze gebeurtenissen registreren. Centrale logboek registratie implementeren |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Zorg ervoor dat de juiste controle en logboek registratie wordt afgedwongen op de veld Gateway

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-veld Gateway | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Wanneer meerdere apparaten verbinding maken met een veld Gateway, moet u ervoor zorgen dat verbindings pogingen en verificatie status (geslaagd of mislukt) voor afzonderlijke apparaten worden geregistreerd en onderhouden op de veld Gateway.</p><p>In gevallen waarin de veld Gateway de IoT Hub referenties voor afzonderlijke apparaten onderhoudt, moet u er ook voor zorgen dat de controle wordt uitgevoerd wanneer deze referenties worden opgehaald. Ontwikkel een proces om regel matig de logboeken te uploaden naar Azure IoT Hub/Storage voor lange termijn retentie.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Zorg ervoor dat de juiste controle en logboek registratie wordt afgedwongen op de Cloud gateway

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-Cloud gateway | 
| **SDL-fase**               | Build |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Inleiding tot de bewaking van IoT Hub bewerkingen](../../iot-hub/iot-hub-operations-monitoring.md) |
| **Stappen** | <p>Ontwerp voor het verzamelen en opslaan van controle gegevens die zijn verzameld via IoT Hub Operations-bewaking. De volgende bewakings categorieën inschakelen:</p><ul><li>Bewerkingen voor apparaat-id's</li><li>Communicatie tussen apparaat en Cloud</li><li>Communicatie tussen Cloud en apparaat</li><li>Verbindingen</li><li>Uploads van bestanden</li></ul>|