---
title: Problemen met de uitvoering van pakketten oplossen in SSIS Integration runtime
description: Dit artikel bevat richt lijnen voor probleem oplossing voor het uitvoeren van SSIS-pakketten in de SSIS Integration runtime
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 9609c382161514611ddc41af040e8fb438431fdf
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555998"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Problemen met de uitvoering van pakketten oplossen in SSIS Integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel vindt u de meest voorkomende fouten die u kunt tegen komen wanneer u SQL Server Integration Services (SSIS)-pakketten uitvoert in de SSIS Integration runtime. Hierin worden de mogelijke oorzaken en acties beschreven om de fouten op te lossen.

## <a name="where-to-find-logs-for-troubleshooting"></a>Waar vind ik logboeken voor het oplossen van problemen

Gebruik de Azure Data Factory Portal om de uitvoer van de SSIS-uitvoering van de pakketten te controleren. De uitvoer bevat het resultaat van de uitvoering, fout berichten en de bewerkings-ID. Zie [de pijp lijn controleren](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)voor meer informatie.

Gebruik de SSIS-catalogus (SSISDB) om de detail logboeken voor de uitvoering te controleren. Zie [monitoring Running packages and other Operations](/sql/integration-services/performance/monitor-running-packages-and-other-operations)(Engelstalig) voor meer informatie.

## <a name="common-errors-causes-and-solutions"></a>Veelvoorkomende fouten, oorzaken en oplossingen

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Fout bericht: de time-out voor de verbinding is verlopen of de service heeft een fout aangetroffen bij het verwerken van uw aanvraag. Probeer het opnieuw."

Dit zijn mogelijke oorzaken en aanbevolen acties:
* De gegevens bron of het doel is overbelast. Controleer de belasting van de gegevens bron of het doel en kijk of het voldoende capaciteit heeft. Als u bijvoorbeeld Azure SQL Database hebt gebruikt, kunt u overwegen omhoog te schalen als de data base waarschijnlijk een time-out heeft.
* Het netwerk tussen de SSIS Integration runtime en de gegevens bron of het doel is Insta Biel, vooral wanneer de verbinding over meerdere regio's of tussen on-premises en Azure gaat. Pas het patroon voor opnieuw proberen in het SSIS-pakket toe door de volgende stappen uit te voeren:
  * Zorg ervoor dat uw SSIS-pakketten bij storingen zonder neven effecten (bijvoorbeeld gegevens verlies of gegevens duplicatie) opnieuw kunnen worden uitgevoerd.
  * Configureer het **interval** voor **opnieuw proberen** en de activiteit voor het **uitvoeren van SSIS-pakketten** op het tabblad **Algemeen** . ![ Eigenschappen instellen op het tabblad Algemeen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Stel voor een ADO.NET-en OLE DB bron-of doel onderdeel **ConnectRetryCount** en **ConnectRetryInterval** in in VERBINDINGS beheer in de SSIS-pakket-of-SSIS-activiteit.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Fout bericht: de ADO-netwerk bron kan de verbinding niet verkrijgen... met ' er is een netwerk-of instantie-specifieke fout opgetreden bij het tot stand brengen van een verbinding met SQL Server. De server is niet gevonden of niet toegankelijk. "

Dit probleem betekent meestal dat de gegevensbron of -bestemming niet toegankelijk is vanuit de SSIS-integratieruntime. De redenen kunnen variëren. Voer de volgende acties uit:
* Zorg ervoor dat u de gegevens bron of doel naam/IP juist doorgeeft.
* Controleer of de firewall juist is ingesteld.
* Zorg ervoor dat het virtuele netwerk correct is geconfigureerd als uw gegevens bron of doel locatie on-premises is:
  * U kunt controleren of het probleem afkomstig is van de configuratie van een virtueel netwerk door een Azure-VM in te richten in hetzelfde virtuele netwerk. Controleer vervolgens of de gegevens bron of het doel toegankelijk is vanaf de Azure-VM.
  * Meer informatie over het gebruik van een virtueel netwerk met SSIS Integration runtime vindt u in een [Azure-SSIS Integration runtime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Fout bericht: de ADO-netwerk bron kan de verbinding niet verkrijgen... met ' kan geen beheerd verbindings beheer maken '.

De mogelijke oorzaak is dat de ADO.NET-Provider die in het pakket wordt gebruikt, niet is geïnstalleerd in de SSIS Integration runtime. U kunt de provider installeren met behulp van een aangepaste installatie. Meer informatie over aangepaste Setup vindt u in [Customize Setup voor de Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Fout bericht: de verbinding... is niet gevonden "

Een bekend probleem in oudere versies van SQL Server Management Studio (SSMS) kan deze fout veroorzaken. Als het pakket een aangepast onderdeel bevat (bijvoorbeeld SSIS Azure Feature Pack of partneronderdelen) dat niet is geïnstalleerd op de computer waarop SSMS wordt gebruikt om de implementatie uit te voeren, wordt het onderdeel door SSMS verwijderd met de fout als gevolg. Upgrade [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) naar de nieuwste versie waarin het probleem is opgelost.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Fout bericht: de afsluit code van de SSIS-uitvoerder:-1073741819.

* Mogelijke oorzaak en aanbevolen actie:
  * Deze fout kan zijn veroorzaakt door de beperking van de Excel-bron en-bestemming wanneer meerdere Excel-bronnen of-bestemmingen parallel in meerdere threads worden uitgevoerd. U kunt deze beperking verhelpen door de Excel-onderdelen te wijzigen zodat deze in de juiste volg orde worden uitgevoerd, of ze in verschillende pakketten te scheiden en de trigger pakket taak uitvoeren te activeren, waarbij de eigenschap ExecuteOutOfProcess is ingesteld op True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Fout bericht: er is niet genoeg ruimte op de schijf

Deze fout geeft aan dat de lokale schijf wordt gebruikt in het SSIS Integration runtime-knoop punt. Controleer of uw pakket of aangepaste installatie veel schijf ruimte verbruikt:
* Als de schijf wordt gebruikt door uw pakket, wordt deze vrijgemaakt nadat de uitvoering van het pakket is voltooid.
* Als de schijf wordt gebruikt door uw aangepaste installatie, moet u de SSIS-integratie-runtime stoppen, uw script wijzigen en de Integration runtime opnieuw starten. De volledige Azure Blob-container die u hebt opgegeven voor aangepaste installatie, wordt gekopieerd naar het SSIS Integration runtime-knoop punt. Controleer daarom of er overbodige inhoud onder die container staat.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Fout bericht: ' kan de bron niet ophalen uit de hoofd server. Micro soft. SqlServer. IntegrationServices. scale. ScaleoutContract. common. MasterResponseFailedException: code: 300004. Beschrijving: het laden van het bestand * * * is mislukt.

* Mogelijke oorzaak en aanbevolen actie:
  * Als de SSIS-activiteit een pakket uit het bestands systeem (pakket bestand of project bestand) uitvoert, treedt deze fout op als het project, het pakket of het configuratie bestand niet toegankelijk is met de toegangs referentie voor het pakket dat u in de SSIS-activiteit hebt gegeven
    * Als u Azure-bestand gebruikt:
      * Het bestandspad moet beginnen met \\ \\ \<storage account name\> . file.core.Windows.net\\\<file share path\>
      * Het domein moet ' Azure ' zijn
      * De gebruikers naam moet \<storage account name\>
      * Het wacht woord moet \<storage access key\>
    * Als uw on-premises bestand wordt gebruikt, controleert u of VNet, toegangs referenties voor het pakket en de machtigingen juist zijn geconfigureerd, zodat uw Azure SSIS Integration runtime toegang heeft tot uw on-premises bestands share.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Fout bericht: ' de bestands naam '... ' opgegeven in de verbinding is ongeldig

* Mogelijke oorzaak en aanbevolen actie:
  * Er is een ongeldige bestands naam opgegeven
  * Zorg ervoor dat u FQDN (Fully Qualified Domain Name) gebruikt in plaats van korte tijd in uw verbindings beheer

### <a name="error-message-cannot-open-file-"></a>Fout bericht: kan bestand niet openen...

Deze fout treedt op wanneer de pakket uitvoering geen bestand kan vinden op de lokale schijf in de SSIS Integration runtime. Voer de volgende acties uit:
* Gebruik niet het absolute pad in het pakket dat wordt uitgevoerd in de SSIS Integration runtime. De huidige uitvoerings werkmap (.) of de map Temp (% TEMP%) gebruiken maar.
* Als u een aantal bestanden op SSIS Integration runtime-knoop punten wilt behouden, moet u de bestanden voorbereiden zoals beschreven in [Customize Setup](how-to-configure-azure-ssis-ir-custom-setup.md). Alle bestanden in de werkmap worden opgeruimd nadat de uitvoering is voltooid.
* Gebruik Azure Files in plaats van het bestand op te slaan in het knoop punt SSIS Integration runtime. Zie [Azure-bestands shares gebruiken](/sql/integration-services/lift-shift/ssis-azure-files-file-shares#use-azure-file-shares)voor meer informatie.

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Fout bericht: de grootte van de data base SSISDB is bereikt

Een mogelijke oorzaak is dat de SSISDB-data base die is gemaakt in Azure SQL Database of in het SQL Managed instance het quotum heeft bereikt. Voer de volgende acties uit:
* Overweeg de DTU van uw database te vergroten. U vindt meer informatie in [SQL database limieten voor een logische server](../azure-sql/database/resource-limits-logical-server.md).
* Controleer of uw pakket een groot aantal logboeken kan genereren. Als dat het geval is, kunt u een elastische taak configureren om deze logboeken op te schonen. Meer informatie kunt u vinden in [SSISDB-logboeken opschonen met taken voor Azure Elastic Database](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Fout bericht: de aanvraag limiet voor de data base is... en is bereikt. "

Als er een groot aantal pakketten parallel wordt uitgevoerd in de SSIS Integration runtime, kan deze fout optreden omdat SSISDB de aanvraag limiet heeft bereikt. Overweeg de DTC van SSISDB te verhogen om dit probleem op te lossen. U vindt meer informatie in [SQL database limieten voor een logische server](../azure-sql/database/resource-limits-logical-server.md).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Fout bericht: de SSIS-bewerking is mislukt met een onverwachte bewerkings status:...

De fout wordt meestal veroorzaakt door een tijdelijk probleem, dus probeer de uitvoering van het pakket opnieuw uit te voeren. Pas het patroon voor opnieuw proberen in het SSIS-pakket toe door de volgende stappen uit te voeren:

* Zorg ervoor dat uw SSIS-pakketten bij storingen zonder neven effecten (bijvoorbeeld gegevens verlies of gegevens duplicatie) opnieuw kunnen worden uitgevoerd.
* Configureer het **interval** voor **opnieuw proberen** en de activiteit voor het **uitvoeren van SSIS-pakketten** op het tabblad **Algemeen** . ![ Eigenschappen instellen op het tabblad Algemeen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Stel voor een ADO.NET-en OLE DB bron-of doel onderdeel **ConnectRetryCount** en **ConnectRetryInterval** in in VERBINDINGS beheer in de SSIS-pakket-of-SSIS-activiteit.

### <a name="error-message-there-is-no-active-worker"></a>Fout bericht: er is geen actieve werk nemer.

Deze fout betekent meestal dat de SSIS Integration runtime een slechte status heeft. Controleer de Azure Portal voor de status en gedetailleerde fouten. Zie [Azure-SSIS Integration runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime)voor meer informatie.

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Fout bericht: ' uw Integration runtime kan niet worden bijgewerkt en zal uiteindelijk niet meer werken omdat we geen toegang hebben tot de Azure Blob-container die u hebt ingevoerd voor aangepaste installatie. '

Deze fout treedt op wanneer de SSIS Integration runtime geen toegang kan krijgen tot de opslag die is geconfigureerd voor aangepaste installatie. Controleer of de SAS-URI (Shared Access Signature) die u hebt ingevoerd geldig is en niet is verlopen.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Fout bericht: micro soft OLE DB provider voor Analysis Services. HRESULT: 0x80004005 beschrijving: COM-fout: COM-fout: mscorlib; Er is een uitzonde ring opgetreden bij het doel van een aanroep.

Een mogelijke oorzaak is dat de gebruikers naam of het wacht woord waarvoor Azure AD Multi-Factor Authentication is ingeschakeld, is geconfigureerd voor Azure Analysis Services-verificatie. Deze authenticatie wordt niet ondersteund in de SSIS Integration runtime. Probeer een service-principal te gebruiken voor Azure Analysis Services authenticatie:

1. Bereid een service-principal voor zoals beschreven in [Automation met Service-principals](../analysis-services/analysis-services-service-principal.md).
2. Configureer in verbindings beheer **een specifieke gebruikers naam en wacht woord gebruiken**: Stel **AppID** in als de gebruikers naam en **clientSecret** als wacht woord.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Fout bericht: de ADONET-bron kan de verbinding {GUID} niet verkrijgen met het volgende fout bericht: de aanmelding is mislukt voor de gebruiker ' NT AUTHORITY\ANONYMOUS LOGON ' ' bij het gebruik van een beheerde identiteit

Zorg ervoor dat u de verificatie methode van verbindings beheer niet configureert als **Active Directory wachtwoord verificatie** wanneer de para meter *ConnectUsingManagedIdentity* is ingesteld op **True**. U kunt het configureren als **SQL-verificatie** in plaats daarvan, wat wordt genegeerd als *ConnectUsingManagedIdentity* is ingesteld.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Fout bericht: "0xC020801F at..., OData-bron [...]: kan geen beheerde verbinding verkrijgen vanuit het run-time verbindings beheer"

Een mogelijke oorzaak is dat de Transport Layer Security (TLS) niet is ingeschakeld in SSIS Integration runtime, wat vereist is voor uw OData-bron. U kunt TLS inschakelen in SSIS Integration runtime met behulp van Setup aanpassen. Meer informatie vindt u in [kan geen verbinding maken met project online Odata vanuit SSIS](/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) en [Setup aanpassen voor de Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Fout bericht: ' een taak voor het klaarzetten aanvragen met de bewerking-GUID... de volgende fout is opgetreden: kan de faserings bewerking niet verzenden met het fout bericht: micro soft. SqlServer. IntegrationServices. AisAgentCore. AisAgentException: kan de gegevens proxy niet laden. "

Zorg ervoor dat uw Azure SSIS Integration runtime is geconfigureerd met Self-Hosted Integration runtime. Meer informatie vindt [u in Self-Hosted IR configureren als proxy voor Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Fout bericht: ' status Faserings taak: mislukt. Fout in de staging-taak: error code: 2010, ErrorMessage: de zelf-hostende Integration Runtime... is offline '

Zorg ervoor dat uw Self-Hosted Integration runtime is geïnstalleerd en wordt gestart. Meer informatie vindt [u op het maken en configureren van een zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Fout bericht: "tijdelijke taak fout: error code: 2906, ErrorMessage: de uitvoering van het pakket is mislukt. uitvoer: {" OperationErrorMessages ":" fout: de gevraagde OLE DB provider... is niet geregistreerd. Als het 64-bits stuur programma niet is geïnstalleerd, voert u het pakket uit in 32-bits modus...

Zorg ervoor dat de overeenkomende provider die wordt gebruikt door uw OLE DB-connectors in uw pakket, op de juiste wijze is geïnstalleerd op Self-Hosted Integration runtime-computer. Meer informatie vindt [u in Self-Hosted IR configureren als proxy voor Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Fout bericht: "staging-taak fout: error code: 2906, ErrorMessage: de uitvoering van het pakket is mislukt. uitvoer: {" OperationErrorMessages ":" fout: System. IO. FileLoadException: kan bestand of assembly micro soft. WindowsAzure. Storage, versie =..., Culture = neutral, PublicKeyToken = 31bf3856ad364e35 of een van de afhankelijkheden ervan niet laden. De manifest definitie van de gevonden assembly komt niet overeen met de assembly-verwijzing. ..."

Een mogelijke oorzaak is dat uw Self-Hosted Integration runtime niet op de juiste wijze is geïnstalleerd of bijgewerkt. Suggesties voor het downloaden en opnieuw installeren van de nieuwste zelf-hostende Integration runtime. Meer informatie vindt [u op het maken en configureren van een zelf-hostende Integration runtime](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Fout bericht: er is een verbinding vereist bij het aanvragen van meta gegevens. Als u offline werkt, schakelt u offline werken in het SSIS-menu uit om de verbinding in te scha kelen "

* Mogelijke oorzaak en aanbevolen actie:
  * Als er ook een waarschuwings bericht ' het onderdeel biedt geen ondersteuning voor het gebruik van verbindings beheer met de waarde ' ConnectByProxy ' ingesteld op ' True ' in het uitvoerings logboek, betekent dit dat er een verbindings beheer wordt gebruikt voor een onderdeel dat nog geen ConnectByProxy heeft ondersteund. De ondersteunde onderdelen kunt u vinden op [Self-Hosted IR configureren als proxy voor Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Het uitvoerings logboek vindt u in het [rapport SSMS](/sql/integration-services/performance/monitor-running-packages-and-other-operations#reports) of in de logboekmap die u hebt opgegeven in de activiteit SSIS-pakket uitvoering.
  * vNet kan ook worden gebruikt om toegang te krijgen tot on-premises gegevens als alternatief. Meer informatie vindt u in een [Azure-SSIS Integration runtime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Fout bericht: ' status Faserings taak: mislukt. Fout in de staging-taak: error code: 2906, ErrorMessage: de uitvoering van het pakket is mislukt. uitvoer: {"OperationErrorMessages": "de afsluit code van de SSIS-uitvoerder:-1. \ n", "LogLocation": "... \\ SSISTelemetry \\ ExecutionLog \\ ... "," effectiveIntegrationRuntime ":"... "," executionDuration ":...," durationInQueue ": {" integrationRuntimeQueue ":...}}"

Zorg ervoor dat Visual C++ runtime is geïnstalleerd op Self-Hosted Integration runtime-computer. Meer informatie vindt [u in Self-Hosted IR configureren als proxy voor Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Meerdere pakket uitvoeringen worden onverwacht geactiveerd

* Mogelijke oorzaak en aanbevolen actie:
  * De opgeslagen procedure-activiteit van ADF of de opzoek activiteit wordt gebruikt voor het activeren van de uitvoering van SSIS-pakketten. De t-SQL-opdracht kan leiden tot een tijdelijk probleem en het opnieuw uitvoeren van het programma activeren waardoor meerdere pakket uitvoeringen kunnen worden uitgevoerd.
  * Gebruik in plaats daarvan ExecuteSSISPackage-activiteit die ervoor zorgt dat de pakket uitvoering niet opnieuw wordt uitgevoerd tenzij gebruikers het aantal pogingen in de activiteit instellen. Meer informatie vindt u op [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](./how-to-invoke-ssis-package-ssis-activity.md)
  * Verfijn uw t-SQL-opdracht om opnieuw te kunnen worden uitgevoerd door te controleren of er al een uitvoering is geactiveerd

### <a name="package-execution-takes-too-long"></a>Het uitvoeren van het pakket duurt te lang

Dit zijn mogelijke oorzaken en aanbevolen acties:

* Er zijn te veel pakket uitvoeringen gepland voor de SSIS Integration runtime. Al deze uitvoeringen worden in een wachtrij gewacht op hun beurt.
  * Bepaal het maximum door gebruik te maken van de volgende formule:

    Maximum aantal parallelle uitvoeringen per IR = aantal knoop punten * maximum aantal parallelle uitvoeringen per knoop punt
  * Zie [een Azure SSIS Integration runtime maken in azure Data Factory](create-azure-ssis-integration-runtime.md)voor meer informatie over het instellen van het aantal knoop punten en het maximum aantal parallelle uitvoeringen per knoop punt.
* De SSIS Integration runtime is gestopt of heeft een slechte status. Zie [Azure-SSIS Integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)voor meer informatie over het controleren van de runtime status en-fouten van de SSIS-integratie.

We raden u ook aan om een time-out op het tabblad **Algemeen** in te stellen: ![ Stel eigenschappen in op het tabblad algemeen ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) .

### <a name="poor-performance-in-package-execution"></a>Slechte prestaties bij het uitvoeren van een pakket

Voer de volgende acties uit:

* Zorg ervoor dat de SSIS Integration runtime zich in dezelfde regio bevindt als de gegevens bron en het doel.

* Stel het logboek registratie niveau van de uitvoering van het pakket in op **prestaties** om de duur informatie voor elk onderdeel in de uitvoering te verzamelen. Zie voor meer informatie [logboek registratie van Integration Services (SSIS)](/sql/integration-services/performance/integration-services-ssis-logging).

* Controleer de prestaties van het IR-knoop punt in de Azure Portal:
  * Zie [Azure-SSIS Integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)voor meer informatie over het bewaken van de SSIS Integration runtime.
  * U kunt de informatie over de CPU/het geheugen voor de SSIS Integration runtime vinden door de metrische gegevens van de data factory in het Azure Portal weer te geven.
    ![Metrische gegevens van de SSIS Integration runtime bewaken](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)