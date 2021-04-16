---
title: Azure AD Connect cloudsynchronisatie oplossen
description: In dit artikel wordt beschreven hoe u problemen kunt oplossen die zich kunnen voordoen met de inrichtingsagent voor de cloud.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 65022d98c7ee7e90d8f1fe5b6854605c841ad05b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530306"
---
# <a name="cloud-sync-troubleshooting"></a>Problemen met cloudsynchronisatie oplossen

Cloudsynchronisatie heeft veel verschillende aspecten en afhankelijkheden. Het brede bereik ervan kan leiden tot verschillende problemen. Dit artikel helpt u bij het oplossen van deze problemen. Het geeft aan op welke gebieden u zich het beste kunt richten, hoe u aanvullende informatie kunt verzamelen, en welke verschillende technieken u kunt gebruiken om problemen op te sporen.


## <a name="common-troubleshooting-areas"></a>Veelvoorkomende gebieden voor probleemoplossing

|Naam|Beschrijving|
|-----|-----|
|[Problemen met agent](#agent-problems)|Controleer of de agent correct is geïnstalleerd en of deze communiceert met Azure Active Directory (Azure AD).|
|[Problemen met objectsynchronisatie](#object-synchronization-problems)|Gebruik inrichtingslogboeken om problemen met objectsynchronisatie op te lossen.|
|[Problemen met in quarantaine inrichten](#provisioning-quarantined-problems)|Informatie over het inrichten van quarantaineproblemen en hoe u deze kunt oplossen.|


## <a name="agent-problems"></a>Problemen met agent
Enkele van de eerste dingen die u met de agent wilt controleren, zijn:

-  Is deze geïnstalleerd?
-  Wordt de agent lokaal uitgevoerd?
-  Is de agent in de portal?
-  Is de agent gemarkeerd als in orde?

Deze items kunnen worden geverifieerd in de Azure Portal en op de lokale server met de agent.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent

Volg deze stappen om te controleren of de agent wordt gezien door Azure en in orde is.

1. Meld u aan bij Azure Portal.
1. Selecteer aan de linkerkant **Azure Active Directory**  >  **Azure AD Connect**. Selecteer synchronisatie beheren in **het midden.**
1. Selecteer in **Azure AD Connect cloudsynchronisatiescherm** **Alle agents controleren.**

   ![Alle agents controleren](media/how-to-install/install-7.png)</br>
 
1. In het **scherm On-premises inrichtingsagents** ziet u de agents die u hebt geïnstalleerd. Controleer of de agent in kwestie hier staat en als In orde *is gemarkeerd.*

   ![Scherm On-premises inrichtingsagents](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>De poort controleren

Controleer of Azure luistert op poort 443 en of uw agent mee kan communiceren. 

Deze test controleert of uw agents met Azure kunnen communiceren via poort 443. Open een browser en ga naar de vorige URL vanaf de server waarop de agent is geïnstalleerd.

![Verificatie van de bereikbaarheid van de poort](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Op de lokale server

Volg deze stappen om te controleren of de agent wordt uitgevoerd.

1. Open Services op de server  met de geïnstalleerde agent door er naar te navigeren of door **Naar Run**  >    >  **Services.msc** starten te gaan.
1. Zorg **ervoor dat** onder Services Microsoft Azure AD Connect Agent **Updater** en **Microsoft Azure AD Connect Provisioning Agent** zich daar staan en dat hun status Wordt uitgevoerd *is.*

   ![Scherm Services](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Veelvoorkomende problemen met de installatie van de agent

In de volgende secties worden enkele veelvoorkomende problemen met de installatie van de agent en typische oplossingen beschreven.

#### <a name="agent-failed-to-start"></a>Agent kan niet worden starten

Mogelijk wordt er een foutbericht weergegeven met de volgende tekst:

**Service 'Microsoft Azure AD Connect Provisioning Agent' kan niet worden starten. Controleer of u voldoende bevoegdheden hebt om de systeemservices te starten.** 

Dit probleem wordt meestal veroorzaakt door een groepsbeleid dat voorkomt dat machtigingen worden toegepast op het lokale NT Service-aanmeldaccount dat is gemaakt door het installatieprogramma (NT SERVICE\AADConnectProvisioningAgent). Deze machtigingen zijn vereist voor het starten van de service.

Volg deze stappen om dit probleem op te lossen.

1. Meld u aan bij de server met een beheerdersaccount.
1. Open **Services** door er naar te navigeren of door **Run**  >    >  **Services.msc te starten.**
1. Dubbelklik **onder Services** op Microsoft Azure AD **Connect Provisioning Agent**.
1. Op het **tabblad Aanmelden wijzigt** u **Dit account** in een domeinbeheerder. Start de service vervolgens opnieuw. 

   ![Tabblad Aanmelden](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Er t/m een agent uit of het certificaat is ongeldig

Mogelijk wordt het volgende foutbericht weergegeven wanneer u de agent probeert te registreren.

![Time-outfoutbericht](media/how-to-troubleshoot/troubleshoot-4.png)

Dit probleem wordt meestal veroorzaakt door de agent waardoor geen verbinding kan worden gemaakt met de Hybrid Identity-service, hiervoor moet u een HTTP-proxy configureren. U kunt dit probleem oplossen door een uitgaande proxy te configureren. 

De inrichtingsagent ondersteunt het gebruik van een uitgaande proxy. U kunt deze configureren door het configuratiebestand van de agent *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Voeg de volgende regels toe aan het einde van het bestand, net vóór de afsluitende `</configuration>` tag.
Vervang de variabelen en door de naam en poortwaarden `[proxy-server]` `[proxy-port]` van uw proxyserver.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Agentregistratie mislukt met beveiligingsfout

Mogelijk wordt er een foutbericht weergegeven wanneer u de inrichtingsagent voor de cloud installeert.

Dit probleem wordt meestal veroorzaakt doordat de agent de PowerShell-registratiescripts niet kan uitvoeren vanwege lokaal PowerShell-uitvoeringsbeleid.

U kunt dit probleem oplossen door het PowerShell-uitvoeringsbeleid op de server te wijzigen. U moet het machine- en gebruikersbeleid instellen op *Niet-gedefinieerd* of *Externtekend.* Als deze zijn ingesteld op *Onbeperkt,* wordt deze fout weergegeven. Zie [PowerShell-uitvoeringsbeleid voor meer informatie.](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 

### <a name="log-files"></a>Logboekbestanden

De agent verzendt standaard minimale foutberichten en stack-traceringsgegevens. U vindt deze traceerlogboeken in de map **C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.**

Volg deze stappen om aanvullende informatie te verzamelen voor het oplossen van problemen met betrekking tot de agent.

1.  Installeer de PowerShell-module AADCloudSyncTools zoals hier wordt [beschreven.](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)
2. Gebruik de `Export-AADCloudSyncToolsLogs` PowerShell-cmdlet om de informatie vast te leggen.  U kunt de volgende schakelopties gebruiken om uw gegevensverzameling af te stemmen.
      - SkipVerboseTrace om alleen huidige logboeken te exporteren zonder uitgebreide logboeken vast te leggen (standaard = false)
      - TracingDurationMins om een andere opnameduur op te geven (standaard = 3 minuten)
      - OutputPath om een ander uitvoerpad op te geven (standaard = gebruikersdocumenten)


## <a name="object-synchronization-problems"></a>Problemen met objectsynchronisatie

De volgende sectie bevat informatie over het oplossen van problemen met objectsynchronisatie.

### <a name="provisioning-logs"></a>Inrichtingslogboeken

In de Azure Portal kunnen inrichtingslogboeken worden gebruikt om problemen met objectsynchronisatie op te sporen en op te lossen. Als u de logboeken wilt weergeven, selecteert **u Logboeken.**

![De knop Logboeken](media/how-to-troubleshoot/log-1.png)

Inrichtingslogboeken bieden een schat aan informatie over de status van de objecten die worden gesynchroniseerd tussen uw on-premises Active Directory-omgeving en Azure.

![Scherm Inrichtingslogboeken](media/how-to-troubleshoot/log-2.png)

U kunt de vervolgkeuzevakken boven aan de pagina gebruiken om de weergave op nul te filteren op specifieke problemen, zoals datums. Dubbelklik op een afzonderlijke gebeurtenis om aanvullende informatie weer te geven.

![Informatie in de vervolgkeuzelijsten voor inrichtingslogboeken](media/how-to-troubleshoot/log-3.png)

Deze informatie bevat gedetailleerde stappen en waar het synchronisatieprobleem optreedt. Op deze manier kunt u de exacte locatie van het probleem aanwijzen.


## <a name="provisioning-quarantined-problems"></a>Problemen met in quarantaine inrichten

Cloudsynchronisatie bewaakt de status van uw configuratie en plaatst slechte objecten in een quarantainetoestand. Als de meeste of alle aanroepen op het doelsysteem consistent mislukken vanwege een fout, bijvoorbeeld ongeldige beheerdersreferenties, wordt de synchronisatie job gemarkeerd als in quarantaine.

![Quarantinestatus](media/how-to-troubleshoot/quarantine-1.png)

Als u de status selecteert, ziet u aanvullende informatie over de quarantaine. U kunt ook de foutcode en het bericht verkrijgen.

![Schermopname met aanvullende informatie over de quarantaine.](media/how-to-troubleshoot/quarantine-2.png)

Als u met de rechtermuisknop op de status klikt, worden er extra opties beschikbaar:
    
   - inrichtingslogboeken weergeven
   - agent weergeven
   - quarantaine leeg maken

![Schermopname van de opties in het menu met de rechtermuisknop.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Een quarantaine oplossen
Er zijn twee verschillende manieren om een quarantaine op te lossen.  Dit zijn:

  - quarantaine verwijderen: het watermerk wordt geweerd en er wordt een deltasynchronisatie uitgevoerd
  - start de inrichtings job opnieuw op: het watermerk wordt geweerd en er wordt een initiële synchronisatie uitgevoerd

#### <a name="clear-quarantine"></a>Quarantaine leeg maken
Als u het watermerk wilt verwijderen en een deltasynchronisatie wilt uitvoeren op de inrichtings job nadat u deze hebt geverifieerd, klikt u met de rechtermuisknop op de status en selecteert u **Quarantaine verwijderen.**

U ziet nu een melding dat de quarantaine wordt ge wissen.

![Schermopname van de melding dat de quarantaine wordt ge wissen.](media/how-to-troubleshoot/quarantine-5.png)

Vervolgens ziet u dat de status van uw agent in orde is.

![Statusinformatie over quarantaine](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>De inrichtings job opnieuw starten
Gebruik de Azure Portal om de inrichtings job opnieuw te starten. Selecteer op de configuratiepagina van de agent **de optie Inrichting opnieuw starten.**

  ![Inrichting opnieuw starten](media/how-to-troubleshoot/quarantine-3.png)

- Gebruik Microsoft Graph om de [inrichtings job opnieuw op te starten.](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) U hebt volledige controle over wat u opnieuw start. U kunt ervoor kiezen om het volgende te doen:
  - Escrows: om de escrow-teller die in de richting van de quarantainestatus komt, opnieuw op te starten.
  - Quarantaine om de toepassing uit quarantaine te verwijderen.
  - Watermerken. 
  
  Gebruik de volgende aanvraag:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Het cloudsynchronisatieserviceaccount herstellen
Als u het cloudsynchronisatieserviceaccount wilt herstellen, kunt u de `Repair-AADCloudSyncToolsAccount` gebruiken.  


   1.  Gebruik de installatiestappen die hier worden [beschreven](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) om te beginnen en vervolgens door te gaan met de resterende stappen.
   2.  Typ of kopieer Windows PowerShell een sessie met beheerdersbevoegdheden en plak het volgende: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Voer uw globale beheerdersreferenties voor Azure AD in
   4. Typ of kopieer en plak het volgende: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Zodra dit is voltooid, moet worden gezegd dat het account is hersteld.

## <a name="next-steps"></a>Volgende stappen 

- [Bekende beperkingen](how-to-prerequisites.md#known-limitations)
- [Foutcodes](reference-error-codes.md)
