---
title: Problemen met het oplossen van Cloud synchronisatie Azure AD Connect
description: In dit artikel wordt beschreven hoe u problemen kunt oplossen die zich kunnen voordoen met de Cloud inrichtings agent.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 174ec8c42ea17ccae04769d7c0baaa91b8e7025b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517868"
---
# <a name="cloud-sync-troubleshooting"></a>Problemen met Cloud synchronisatie oplossen

Cloudsynchronisatie heeft veel verschillende aspecten en afhankelijkheden. Het brede bereik ervan kan leiden tot verschillende problemen. Dit artikel helpt u bij het oplossen van deze problemen. Het geeft aan op welke gebieden u zich het beste kunt richten, hoe u aanvullende informatie kunt verzamelen, en welke verschillende technieken u kunt gebruiken om problemen op te sporen.


## <a name="common-troubleshooting-areas"></a>Algemene probleemoplossings gebieden

|Naam|Beschrijving|
|-----|-----|
|[Agent problemen](#agent-problems)|Controleer of de agent juist is geïnstalleerd en of deze communiceert met Azure Active Directory (Azure AD).|
|[Problemen met object synchronisatie](#object-synchronization-problems)|Inrichtings Logboeken gebruiken om problemen met object synchronisatie op te lossen.|
|[In quarantaine geplaatste problemen inrichten](#provisioning-quarantined-problems)|Informatie over het inrichten van quarantaine problemen en hoe u deze kunt oplossen.|


## <a name="agent-problems"></a>Agent problemen
Enkele van de eerste dingen die u met de agent wilt verifiëren, zijn:

-  Is dit geïnstalleerd?
-  Wordt de agent lokaal uitgevoerd?
-  Bevindt de agent zich in de portal?
-  Is de agent gemarkeerd als in orde?

Deze items kunnen worden geverifieerd in de Azure Portal en op de lokale server waarop de-agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent

Voer de volgende stappen uit om te controleren of de agent door Azure wordt gezien en in orde is.

1. Meld u aan bij Azure Portal.
1. Selecteer aan de linkerkant **Azure Active Directory**  >  **Azure AD CONNECT**. In het midden selecteert u **synchronisatie beheren**.
1. Selecteer **Alle agents controleren** op het **Azure AD Connect scherm Cloud synchronisatie** .

   ![Alle agents controleren](media/how-to-install/install-7.png)</br>
 
1. Op het scherm **on-premises Provisioning agents** ziet u de agents die u hebt geïnstalleerd. Controleer of de agent in kwestie is en is gemarkeerd als *in orde*.

   ![Scherm on-premises ingerichte agents](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>De poort controleren

Controleer of Azure luistert op poort 443 en of uw agent ermee kan communiceren. 

Met deze test wordt gecontroleerd of uw agents kunnen communiceren met Azure via poort 443. Open een browser en ga naar de vorige URL van de server waarop de agent is geïnstalleerd.

![Verificatie van de bereik baarheid van de poort](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Op de lokale server

Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd.

1. Op de server waarop de agent is geïnstalleerd, opent u **Services** door **ernaar te navigeren of door te gaan** met het  >  **uitvoeren** van  >  **Services. msc**.
1. Zorg er bij **Services** voor dat **Microsoft Azure AD connect agent updater** en **Microsoft Azure AD Connect inrichtings agent** zijn, en de status wordt *uitgevoerd*.

   ![Scherm Services](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Veelvoorkomende problemen met de agent installatie

In de volgende secties worden enkele veelvoorkomende problemen met de agent installatie en typische oplossingen beschreven.

#### <a name="agent-failed-to-start"></a>De agent is niet gestart

Er wordt mogelijk een fout bericht met de volgende strekking weer gegeven:

**De service Microsoft Azure AD Connect inrichtings agent kan niet worden gestart. Controleer of u voldoende rechten hebt om de systeem services te starten.** 

Dit probleem wordt meestal veroorzaakt door een groeps beleid waarmee wordt voor komen dat machtigingen kunnen worden toegepast op het aanmeldings account van de lokale NT-service die is gemaakt door het installatie programma (NT SERVICE\AADConnectProvisioningAgent). Deze machtigingen zijn vereist voor het starten van de service.

Voer de volgende stappen uit om dit probleem op te lossen.

1. Meld u met een beheerders account aan bij de server.
1. Open **Services** door te navigeren naar de service of **door te gaan** met het  >  **uitvoeren** van  >  **Services. msc**.
1. Dubbel klik onder **Services** op **Microsoft Azure AD Connect inrichtings agent**.
1. Wijzig op het tabblad **Aanmelden** het **account** in een domein beheerder. Start de service vervolgens opnieuw. 

   ![Tabblad Aanmelden](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Er is een time-out van de agent of het certificaat is ongeldig

Mogelijk wordt het volgende fout bericht weer gegeven wanneer u probeert de agent te registreren.

![Fout bericht time-out](media/how-to-troubleshoot/troubleshoot-4.png)

Dit probleem wordt meestal veroorzaakt door de agent waardoor geen verbinding kan worden gemaakt met de Hybrid Identity-service, hiervoor moet u een HTTP-proxy configureren. U kunt dit probleem oplossen door een uitgaande proxy te configureren. 

De inrichtings agent ondersteunt het gebruik van een uitgaande proxy. U kunt deze configureren door het configuratie bestand van de agent te bewerken *C:\Program Files\Microsoft Azure AD Connect inrichting Agent\AADConnectProvisioningAgent.exe.config*. Voeg de volgende regels toe aan het einde van het bestand net vóór de eindtag `</configuration>` .
Vervang de variabelen `[proxy-server]` en `[proxy-port]` door de naam en poort waarden van de proxy server.

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

#### <a name="agent-registration-fails-with-security-error"></a>Registratie van agent mislukt vanwege beveiligings fout

Er wordt mogelijk een fout bericht weer gegeven wanneer u de Cloud inrichtings Agent installeert.

Dit probleem wordt meestal veroorzaakt door de agent waardoor de Power shell-registratie scripts niet kunnen worden uitgevoerd vanwege het lokale Power shell-uitvoerings beleid.

Om dit probleem op te lossen, wijzigt u het Power shell-uitvoerings beleid op de-server. U moet het beleid voor de computer en gebruiker instellen op niet- *gedefinieerde* of *RemoteSigned*. Als ze zijn ingesteld als *onbeperkt*, wordt deze fout weer geven. Zie [Power shell Execution policies](/powershell/module/microsoft.powershell.core/about/about_execution_policies)(Engelstalig) voor meer informatie. 

### <a name="log-files"></a>Logboekbestanden

De agent verzendt standaard minimale foutberichten en stack-traceringsgegevens. U vindt deze traceer Logboeken in de map **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect Provisioning Agent\Trace**.

Volg deze stappen om aanvullende informatie te verzamelen voor het oplossen van problemen met de agent.

1.  Installeer de AADCloudSyncTools Power shell-module, zoals [hier](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)wordt beschreven.
2. Gebruik de `Export-AADCloudSyncToolsLogs` Power shell-cmdlet om de gegevens vast te leggen.  U kunt de volgende Schakel opties gebruiken om uw gegevens verzameling te verfijnen.
      - SkipVerboseTrace alleen huidige logboeken exporteren zonder uitgebreide logboeken te vastleggen (standaard = onwaar)
      - TracingDurationMins om een andere vastleg duur op te geven (standaard = 3 minuten)
      - OutputPath om een ander uitvoerpad op te geven (standaard = documenten van de gebruiker)


## <a name="object-synchronization-problems"></a>Problemen met object synchronisatie

De volgende sectie bevat informatie over het oplossen van object synchronisatie.

### <a name="provisioning-logs"></a>Inrichtingslogboeken

In de Azure Portal kunnen inrichtings logboeken worden gebruikt om problemen met object synchronisatie op te sporen en op te lossen. Selecteer **Logboeken** om de logboeken weer te geven.

![Knop Logboeken](media/how-to-troubleshoot/log-1.png)

Inrichtings logboeken bieden een schat aan informatie over de status van de objecten die worden gesynchroniseerd tussen uw on-premises Active Directory omgeving en Azure.

![Scherm voor het inrichten van Logboeken](media/how-to-troubleshoot/log-2.png)

U kunt de vervolg keuzelijsten boven aan de pagina gebruiken om de weer gave te filteren op nul in op specifieke problemen, zoals datums. Dubbel klik op een afzonderlijke gebeurtenis om aanvullende informatie weer te geven.

![Informatie over de vervolg keuzelijst voor het inrichtings logboek](media/how-to-troubleshoot/log-3.png)

Deze informatie bevat gedetailleerde stappen en waar het synchronisatie probleem plaatsvindt. Op deze manier kunt u de exacte plaats van het probleem lokaliseren.


## <a name="provisioning-quarantined-problems"></a>In quarantaine geplaatste problemen inrichten

Met Cloud Sync wordt de status van uw configuratie gecontroleerd en worden de beschadigde objecten in een quarantaine status geplaatst. Als de meeste of alle aanroepen voor het doel systeem consistent mislukken vanwege een fout, bijvoorbeeld ongeldige beheerders referenties, wordt de synchronisatie taak gemarkeerd als in quarantaine.

![Quarantinestatus](media/how-to-troubleshoot/quarantine-1.png)

Als u de status selecteert, ziet u aanvullende informatie over de quarantaine. U kunt ook de fout code en het bericht ophalen.

![Scherm afbeelding met aanvullende informatie over de quarantaine.](media/how-to-troubleshoot/quarantine-2.png)

Met de rechter muisknop op de status krijgt u extra opties:
    
   - inrichtings logboeken weer geven
   - agent weer geven
   - quarantaine wissen

![Scherm afbeelding met de opties voor klikken met de rechter muisknop.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Een quarantaine omzetten
Er zijn twee verschillende manieren om een quarantaine op te lossen.  Dit zijn:

  - quarantaine wissen: Hiermee wist u het water merk en voert u een Delta synchronisatie uit
  - de inrichtings taak opnieuw starten: Hiermee wist u het water merk en voert u een initiële synchronisatie uit

#### <a name="clear-quarantine"></a>Quarantaine wissen
Als u het water merk wilt wissen en een Delta synchronisatie wilt uitvoeren voor de inrichtings taak nadat u deze hebt gecontroleerd, klikt u met de rechter muisknop op de status en selecteert u **quarantaine wissen**.

U ziet dat de quarantaine wordt gewist.

![Scherm afbeelding met de mede deling dat de quarantaine wordt gewist.](media/how-to-troubleshoot/quarantine-5.png)

Vervolgens ziet u de status van de agent als in orde.

![Status informatie voor quarantaine](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>De inrichtings taak opnieuw starten
Gebruik de Azure Portal om de inrichtings taak opnieuw te starten. Selecteer **inrichting opnieuw opstarten** op de pagina configuratie van agent.

  ![Inrichting opnieuw starten](media/how-to-troubleshoot/quarantine-3.png)

- Gebruik Microsoft Graph om [de inrichtings taak opnieuw te starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). U hebt volledige controle over wat u opnieuw opstart. U kunt het volgende wissen:
  - Borg, om de borg teller opnieuw op te starten die de quarantaine status toeneemt.
  - Quarantaine, om de toepassing uit quarantaine te verwijderen.
  - Water merken. 
  
  Gebruik de volgende aanvraag:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Het account voor Cloud Sync service herstellen
Als u het Cloud Sync Service-account moet herstellen, kunt u de gebruiken `Repair-AADCloudSyncToolsAccount` .  


   1.  Gebruik de installatie stappen die [hier](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) worden beschreven om te beginnen en vervolgens door te gaan met de resterende stappen.
   2.  Van een Windows Power shell-sessie met beheerders bevoegdheden, kunt u het volgende typen of kopiëren en plakken: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Voer uw Azure AD-referenties voor de globale beheerder in
   4. Typ of kopieer en plak het volgende: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Zodra dit is voltooid, moet u zeggen dat het account is hersteld.

## <a name="next-steps"></a>Volgende stappen 

- [Bekende beperkingen](how-to-prerequisites.md#known-limitations)
- [Fout codes](reference-error-codes.md)
