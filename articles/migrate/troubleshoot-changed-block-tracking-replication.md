---
title: Problemen met replicatie oplossen in VMware VM-migratie zonder agent
description: Hulp krijgen bij fouten in de replicatie cyclus
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 5487e51ad73ab903e7b61de266e2c28d282a56c1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568655"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Problemen met replicatie oplossen in VMware VM-migratie zonder agent

In dit artikel worden enkele veelvoorkomende problemen en specifieke fouten beschreven die u kunt tegen komen wanneer u on-premises virtuele VMware-machines repliceert met behulp van de Azure Migrate: Server-agentloze methode.

Wanneer u een virtuele VMware-machine repliceert met behulp van de replicatie methode zonder agent, worden de gegevens van de virtuele machine&#39;s-schijven (vmdk's) gerepliceerd naar replica Managed disks in uw Azure-abonnement. Wanneer replicatie voor een virtuele machine wordt gestart, treedt er een initiële replicatie cyclus op waarin volledige kopieën van de schijven worden gerepliceerd. Nadat de initiële replicatie is voltooid, worden incrementele replicatie cycli regel matig gepland om wijzigingen over te dragen die hebben plaatsgevonden sinds de vorige replicatie cyclus.

U kunt af en toe replicatie cycli zien die mislukken voor een virtuele machine. Deze fouten kunnen zich voordoen als gevolg van problemen met de on-premises netwerk configuratie voor problemen met de back-end van de Azure Migrate Cloud service. In dit artikel gaan we het volgende doen:

 - Laat zien hoe u de replicatie status kunt bewaken en fouten kan oplossen.
 - Een lijst met enkele van de veelvoorkomende replicatie fouten en suggesties voor aanvullende stappen voor het oplossen ervan.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Controleer de replicatie status met behulp van de Azure Portal

Gebruik de volgende stappen om de replicatie status voor uw virtuele machines te controleren:

  1. Ga naar de pagina servers in Azure Migrate op de Azure Portal.
  ![Afbeelding 1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. Ga naar de pagina "computers repliceren" door te klikken op "repliceren van servers" in de tegel server migratie.
  ![Afbeelding 2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. U ziet een lijst met replicatie servers samen met aanvullende informatie, zoals status, status, laatste synchronisatie tijd, enzovoort. In de kolom Status wordt de huidige replicatie status van de virtuele machine aangegeven. Een kritische of waarschuwings waarde in de kolom Status geeft meestal aan dat de vorige replicatie cyclus voor de virtuele machine is mislukt. Klik met de rechter muisknop op de virtuele machine en selecteer fout gegevens voor meer informatie. De pagina fout Details bevat informatie over de fout en aanvullende informatie over het oplossen van problemen. U ziet ook een koppeling recente gebeurtenissen die kan worden gebruikt om naar de pagina gebeurtenissen voor de virtuele machine te gaan.
  ![Afbeelding 3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. Klik op recente gebeurtenissen om de eerdere fouten in de replicatie cyclus voor de virtuele machine te bekijken. Zoek op de pagina gebeurtenissen naar de meest recente gebeurtenis van het type ' replicatie cyclus mislukt ' of ' de replicatie cyclus is mislukt voor de schijf ' voor de virtuele machine.
  ![Afbeelding 4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. Klik op de gebeurtenis om inzicht te krijgen in de mogelijke oorzaken van de fout en aanbevolen herstel stappen. Gebruik de verstrekte informatie om problemen op te lossen en de fout te herstellen.
 ![Afbeelding 5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>Veelvoorkomende replicatie fouten

In deze sectie worden enkele veelvoorkomende fouten beschreven en wordt uitgelegd hoe u deze problemen kunt oplossen.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>Key Vault mislukte bewerking bij het repliceren van Vm's

**Fout:** De bewerking Key Vault is mislukt. Bewerking: een beheerd opslag account configureren, Key Vault: sleutel-kluis-naam, opslag account: naam van opslag account is mislukt met de fout:

**Fout:** De bewerking Key Vault is mislukt. Bewerking: definitie van de hand tekening voor gedeelde toegang genereren, Key Vault: sleutel-kluis-naam, opslag account: naam van opslag account is mislukt met de fout:

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

Deze fout treedt doorgaans op omdat het beleid voor gebruikers toegang voor de Key Vault de gebruiker die momenteel is aangemeld, niet de benodigde machtigingen heeft om opslag accounts te configureren om te worden Key Vault beheerd. Als u het beleid voor gebruikers toegang op de sleutel kluis wilt controleren, gaat u naar de sleutel kluis pagina op de portal voor de sleutel kluis en selecteert u toegangs beleid 

Wanneer de-Portal de sleutel kluis maakt, wordt er ook een gebruikers toegangs beleid toegevoegd waarmee de momenteel aangemelde gebruikers machtigingen worden verleend om opslag accounts te configureren om te worden Key Vault beheerd. Dit kan twee oorzaken hebben

- De aangemelde gebruiker is een externe principal op de Azure-Tenant voor klanten (CSP-abonnement, en de aangemelde gebruiker is de partner beheerder). De tijdelijke oplossing in dit geval is om de sleutel kluis te verwijderen, u af te melden bij de portal en u vervolgens aan te melden met een gebruikers account van de Tenant klanten (geen externe principal) en de bewerking opnieuw uit te voeren. De CSP-partner heeft meestal een gebruikers account in de klanten Azure Active Directory Tenant die ze kunnen gebruiken. Als dat niet het geval is, kunt u zich bij de gebruikers Azure Active Directory Tenant aanmelden bij de portal en de gerepliceerde bewerking vervolgens opnieuw proberen. Voor het gebruikte account moet de machtigingen eigenaar of Inzender + gebruikers toegangs beheerder zijn verleend aan het account in de resource groep (project resource groep migreren)

- De andere gevallen waarin dit kan gebeuren, is wanneer een gebruiker (gebruiker1) in eerste instantie replicatie probeerde uit te voeren en een fout heeft aangetroffen, maar de sleutel kluis al is gemaakt (en het beleid voor gebruikers toegang op de juiste wijze is toegewezen aan deze gebruiker). Op een later tijdstip probeert een andere gebruiker (bijvoorbeeld) replicatie te installeren, maar de bewerking beheerd opslag account configureren of de SAS-definitie genereren mislukt omdat er geen gebruikers toegangs beleid is dat overeenkomt met de voor-en achterrichting in de sleutel kluis.

**Oplossing**: om dit probleem te verhelpen, maakt u een beleid voor gebruikers toegang voor de machtiging verlenen van de sleutel kluis die het beheerde opslag account moet configureren en het genereren van SAS-definities. Met de onderstaande cmdlets kunt u dit doen via Azure PowerShell:

$userPrincipalId = $ (Get-AzureRmADUser-UserPrincipalName "user2_email_address"). Id

Set-AzureRmKeyVaultAccessPolicy-Kluisnaam "sleutel kluisnaam"-ObjectId $userPrincipalId-PermissionsToStorage Get, List, Delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, herstellen, back-up, herstellen, opschonen


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**Fout-id:** 181008

**Fout bericht:** VM: VMName. Fout: time-outgebeurtenis ' DisposeArtefactsTimeout ' aangetroffen in de status & ' [' gateway. service. state machine. SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle ' (' WaitingForArtefactsDisposalPreCycle ')] '.

**Mogelijke oorzaken:**

Het onderdeel dat gegevens probeert te repliceren naar Azure, is niet actief of reageert niet. De mogelijke oorzaken zijn:

- De Gateway Service die wordt uitgevoerd op het Azure Migrate apparaat is niet beschikbaar.
- De Gateway Service ondervindt verbindings problemen met Service Bus/Event hub/storage-opslag account.

**Identificeren van de exacte oorzaak van DisposeArtefactsTimedOut en de bijbehorende oplossing:**

1. Zorg ervoor dat het Azure Migrate-apparaat actief en werkend is.
2. Controleer of de Gateway Service wordt uitgevoerd op het apparaat:
   1.  Meld u met extern bureau blad aan bij het Azure Migrate apparaat en Ga als volgt te werk.

   2.  Open de MMC-module van micro soft-Services (Voer > Services. msc) uit en controleer of de Microsoft Azure Gateway Service wordt uitgevoerd. Als de service is gestopt of niet wordt uitgevoerd, start u de service. U kunt ook een opdracht prompt of Power shell openen en: "net start asrgwy"

3. Controleer of er verbindings problemen zijn tussen Azure Migrate apparaat en het opslag account voor het apparaat: 

    Voer de volgende opdracht uit nadat u azcopy in het Azure Migrate apparaat hebt gedownload:
    
    _azcopy Bank https://[account]. blob. core. Windows. net/[container]? GEBASEERD_
    
    **Stappen voor het uitvoeren van de Bench Mark-test voor de prestaties:**
    
      1. Azcopy [downloaden](../storage/common/storage-use-azcopy-v10.md)
        
      2. Zoek naar het opslag account voor het apparaat in de resource groep. Het opslag account heeft een naam die lijkt op migrategwsa \* \* \* \* \* \* \* \* \* \* . Dit is de waarde van para meter [account] in de bovenstaande opdracht.
        
      3. Zoek uw opslag account in de Azure Portal. Zorg ervoor dat het abonnement dat u gebruikt om te zoeken, hetzelfde abonnement is (het doel abonnement) waarin het opslag account is gemaakt. Ga naar containers in de sectie BLOB-service. Klik op + container en maak een container. Geef het niveau van open bare toegang tot de standaard geselecteerde waarde.
        
      4. Ga naar Shared Access Signature onder instellingen. Selecteer container in toegestaan resource type. Klik op SAS en connection string genereren. Kopieer de SAS-waarde.
        
      5. Voer de bovenstaande opdracht uit in de opdracht prompt door de account, container en SA'S te vervangen door de waarden die zijn verkregen in de stappen 2, 3 en 4.
        
      U kunt ook de Azure Storage verkennen naar het apparaat [downloaden](https://go.microsoft.com/fwlink/?linkid=2138967) en proberen 10 blobs van ~ 64 MB te uploaden naar de opslag accounts. Als er geen probleem is, moet de upload slagen.
        
    **Oplossing:** Als deze test mislukt,&#39;een netwerk probleem. Stel uw lokale netwerk team in om verbindings problemen te controleren. Normaal gesp roken kunnen er Firewall-instellingen zijn die de fouten veroorzaken.
    
4.  Controleren op verbindings problemen tussen Azure Migrate apparaat en Service Bus:

    Met deze test wordt gecontroleerd of het Azure Migrate apparaat kan communiceren met de Azure Migrate back-end van de Cloud service. Het apparaat communiceert met de back-end van de service via Service Bus en Event hub-berichten wachtrijen. Als u de verbinding van het apparaat met de Service Bus wilt valideren, [downloadt](https://go.microsoft.com/fwlink/?linkid=2139104) u de service bus Explorer, probeert u verbinding te maken met het toestel service bus en het bericht bericht verzenden/ontvangen uit te voeren. Als er geen probleem is, moet dit lukken.

    **Stappen voor het uitvoeren van de test:**

    1. De connection string kopiëren van de Service Bus die is gemaakt in het migratie project
    2. Open de Service Bus Explorer
    3. Ga naar bestand en vervolgens verbinding maken
    4. Plak de connection string en klik op verbinden
    5. Hiermee wordt Service Bus naam ruimte geopend
    6. Selecteer Snapshot Manager in het onderwerp. Klik met de rechter muisknop op Snapshot Manager, selecteer ' berichten ontvangen ' > Selecteer ' Peek ' en klik op OK
    7. Als de verbinding is geslaagd, ziet u de melding "[x] berichten ontvangen" op de console-uitvoer. Als de verbinding niet slaagt, wordt er een bericht weer gegeven met de melding dat de verbinding is mislukt
    
    **Oplossing:** Als deze test mislukt, is er een netwerk probleem. Stel uw lokale netwerk team in om verbindings problemen te controleren. Normaal gesp roken kunnen er Firewall-instellingen zijn die de fouten veroorzaken.

5. Verbindings problemen tussen Azure Migrate apparaat en Azure Key Vault:

    Met deze test wordt gecontroleerd op verbindings problemen tussen het Azure Migrate apparaat en de Azure Key Vault. De Key Vault wordt gebruikt voor het beheren van toegang tot het opslag account dat wordt gebruikt voor replicatie.
    
    **Stappen voor het controleren van de connectiviteit:**
    
    1. Haal de Key Vault-URI op uit de lijst met resources in de resource groep die overeenkomt met Azure Migrate project.
    
    1. Open Power shell in het Azure Migrate apparaat en voer de volgende opdracht uit:
    
    _test-NetConnection Key Vault URI-P 443_
    
    Met deze opdracht wordt geprobeerd een TCP-verbinding uit te voeren en wordt een uitvoer geretourneerd.
    
     - Controleer in de uitvoer het veld "_TcpTestSucceeded_". Als de waarde '_True_' is, is er geen connectiviteits probleem tussen het Azure migrate apparaat en de Azure Key Vault. Als de waarde False is, is er een probleem met de verbinding.
    
    **Oplossing:** Als deze test mislukt, is er een connectiviteits probleem tussen het Azure Migrate apparaat en de Azure Key Vault. Stel uw lokale netwerk team in om verbindings problemen te controleren. Normaal gesp roken kunnen er Firewall-instellingen zijn die de fouten veroorzaken.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**Fout-id:** 1011

**Fout bericht:** Het uploaden van gegevens voor de schijf DiskPath, DiskId van de virtuele machine VMName; VMId is niet binnen de verwachte tijd voltooid.

Deze fout geeft meestal aan dat Azure Migrate het apparaat dat de replicatie uitvoert, geen verbinding kan maken met de Azure-Cloud Services, of dat de replicatie langzaam verloopt als gevolg van een time-out voor de replicatie cyclus.

De mogelijke oorzaken zijn:

- Het Azure Migrate apparaat is niet beschikbaar.
- De replicatie Gateway Service op het apparaat is niet actief.
- De replicatie Gateway Service ondervindt connectiviteits problemen met een van de volgende Azure-service onderdelen die worden gebruikt voor replicatie: Service Bus/Event hub/Azure cache Storage-account/Azure Key Vault.
- De Gateway Service wordt op het vCenter-niveau beperkt tijdens het lezen van de schijf.

**Het identificeren van de hoofd oorzaak en het oplossen van het probleem:**

1. Zorg ervoor dat het Azure Migrate-apparaat actief en werkend is.
2. Controleer of de Gateway Service wordt uitgevoerd op het apparaat:
   1.  Meld u met extern bureau blad aan bij het Azure Migrate apparaat en Ga als volgt te werk.

   2.  Open de MMC-module van micro soft-Services (Voer > Services. msc) uit en controleer of de Microsoft Azure Gateway Service wordt uitgevoerd. Als de service is gestopt of niet wordt uitgevoerd, start u de service. U kunt ook een opdracht prompt of Power shell openen en het volgende doen: "net start asrgwy".


3. **Controleren op verbindings problemen tussen Azure Migrate apparaat en cache-opslag account:** 

    Voer de volgende opdracht uit nadat u azcopy in het Azure Migrate apparaat hebt gedownload:
    
    _azcopy Bank https://[account]. blob. core. Windows. net/[container]? GEBASEERD_
    
    **Stappen voor het uitvoeren van de Bench Mark-test voor de prestaties:**
    
      1. Azcopy [downloaden](../storage/common/storage-use-azcopy-v10.md)
        
      2. Zoek naar het opslag account voor het apparaat in de resource groep. Het opslag account heeft een naam die lijkt op migratelsa \* \* \* \* \* \* \* \* \* \* . Dit is de waarde van para meter [account] in de bovenstaande opdracht.
        
      3. Zoek uw opslag account in de Azure Portal. Zorg ervoor dat het abonnement dat u gebruikt om te zoeken, hetzelfde abonnement is (het doel abonnement) waarin het opslag account is gemaakt. Ga naar containers in de sectie BLOB-service. Klik op + container en maak een container. Geef het niveau van open bare toegang tot de standaard geselecteerde waarde.
        
      4. Ga naar Shared Access Signature onder instellingen. Selecteer container in toegestaan resource type. Klik op SAS en connection string genereren. Kopieer de SAS-waarde.
        
      5. Voer de bovenstaande opdracht uit in de opdracht prompt door de account, container en SA'S te vervangen door de waarden die zijn verkregen in de stappen 2, 3 en 4.
        
      U kunt ook de Azure Storage verkennen naar het apparaat [downloaden](https://go.microsoft.com/fwlink/?linkid=2138967) en proberen 10 blobs van ~ 64 MB te uploaden naar de opslag accounts. Als er geen probleem is, moet de upload slagen.
        
    **Oplossing:** Als deze test mislukt,&#39;een netwerk probleem. Stel uw lokale netwerk team in om verbindings problemen te controleren. Normaal gesp roken kunnen er Firewall-instellingen zijn die de fouten veroorzaken.
                
4.  **Verbindings problemen tussen Azure Migrate apparaat en Azure Service Bus:**

    Met deze test wordt gecontroleerd of het Azure Migrate apparaat kan communiceren met de Azure Migrate back-end van de Cloud service. Het apparaat communiceert met de back-end van de service via Service Bus en Event hub-berichten wachtrijen. Als u de verbinding van het apparaat met de Service Bus wilt valideren, [downloadt](https://go.microsoft.com/fwlink/?linkid=2139104) u de service bus Explorer, probeert u verbinding te maken met het toestel service bus en het bericht bericht verzenden/ontvangen uit te voeren. Als er geen probleem is, moet dit lukken.

    **Stappen voor het uitvoeren van de test:**
    
    1. Kopieer de connection string van de Service Bus die is gemaakt in de resource groep die overeenkomt met Azure Migrate project
    
    1. Service Bus Explorer openen
    
    1. Ga naar bestands > verbinding maken
    
    1. Plak de connection string die u in stap 1 hebt gekopieerd en klik op verbinden
    
    1. Hiermee wordt Service Bus naam ruimte geopend.
    
    1. Selecteer Snapshot Manager in het onderwerp in de naam ruimte. Klik met de rechter muisknop op Snapshot Manager, selecteer ' berichten ontvangen ' > Selecteer ' Peek ' en klik op OK.
    
    Als de verbinding is geslaagd, ziet u de melding "[x] berichten ontvangen" op de console-uitvoer. Als de verbinding niet is gelukt, ziet u een bericht dat de verbinding is mislukt.
    
    **Oplossing:** Als deze test mislukt, is er een connectiviteits probleem tussen het Azure Migrate apparaat en Service Bus. Neem contact op met uw lokale netwerk team om deze verbindings problemen te controleren. Normaal gesp roken kunnen er Firewall-instellingen zijn die de fouten veroorzaken.
    
 5. **Verbindings problemen tussen Azure Migrate apparaat en Azure Key Vault:**

    Met deze test wordt gecontroleerd op verbindings problemen tussen het Azure Migrate apparaat en de Azure Key Vault. De Key Vault wordt gebruikt voor het beheren van toegang tot het opslag account dat wordt gebruikt voor replicatie.
    
    **Stappen voor het controleren van de connectiviteit:**
    
    1. Haal de Key Vault-URI op uit de lijst met resources in de resource groep die overeenkomt met Azure Migrate project.
    
    1. Open Power shell in het Azure Migrate apparaat en voer de volgende opdracht uit:
    
    _test-NetConnection Key Vault URI-P 443_
    
    Met deze opdracht wordt geprobeerd een TCP-verbinding uit te voeren en wordt een uitvoer geretourneerd.
    
    1. Controleer in de uitvoer het veld "_TcpTestSucceeded_". Als de waarde '_True_' is, is er geen connectiviteits probleem tussen het Azure migrate apparaat en de Azure Key Vault. Als de waarde False is, is er een probleem met de verbinding.
    
    **Oplossing:** Als deze test mislukt, is er een connectiviteits probleem tussen het Azure Migrate apparaat en de Azure Key Vault. Stel uw lokale netwerk team in om verbindings problemen te controleren. Normaal gesp roken kunnen er Firewall-instellingen zijn die de fouten veroorzaken.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Er is een fout opgetreden tijdens het ophalen van de gewijzigde blokken

Fout bericht: ' er is een fout opgetreden bij het ophalen van de wijzigings blokken '

De replicatie methode zonder agent maakt gebruik van de gewijzigde blokkerings technologie van VMware (CBT) om gegevens naar Azure te repliceren. Met CBT kan het hulp programma server migratie alleen de blokken volgen die zijn gewijzigd sinds de laatste replicatie cyclus. Deze fout treedt op als CBT voor een replicerende virtuele machine opnieuw wordt ingesteld of als het CBT-bestand beschadigd is.

Deze fout kan op twee manieren worden opgelost:

- Als u hebt gekozen voor ' automatisch herstel van replicatie ' door Ja te selecteren wanneer u de replicatie van de virtuele machine hebt geactiveerd, wordt het hulp programma geprobeerd het te herstellen. Klik met de rechter muisknop op de virtuele machine en selecteer replicatie herstellen.
- Als u niet hebt gekozen voor ' automatische herstel van replicatie ' of als de bovenstaande stap niet werkt, kunt u de replicatie voor de virtuele machine stoppen, de [gewijzigde blok tracering](https://go.microsoft.com/fwlink/?linkid=2139203) op de virtuele machine opnieuw instellen en de replicatie vervolgens opnieuw configureren.

Een dergelijk bekend probleem dat ertoe kan leiden dat een CBT opnieuw instellen van de virtuele machine op VMware vSphere 5,5 wordt beschreven in [VMware KB 2048201: de gewijzigde blok tracering](https://go.microsoft.com/fwlink/?linkid=2138888) wordt opnieuw ingesteld na een bewerking van de opslag-VMotion in vSphere 5. x. Als u werkt met VMware vSphere 5.5, zorg er dan voor dat u de updates toepast die worden beschreven in dit artikel.

U kunt ook VMware-gewijzigde blok tracering opnieuw instellen op een virtuele machine met behulp van VMware PowerCLI.

## <a name="an-internal-error-occurred"></a>Er is een interne fout opgetreden

Soms treedt er een fout op die optreedt als gevolg van problemen in de VMware-omgeving/API. We hebben de volgende set fouten geïdentificeerd als fouten met betrekking tot VMware-omgeving. Deze fouten hebben een vaste indeling.

_Fout bericht: er is een interne fout opgetreden. [Fout bericht]_

Bijvoorbeeld: fout bericht: er is een interne fout opgetreden. [Er is een ongeldige momentopname configuratie gedetecteerd].

In de volgende sectie vindt u een aantal veelvoorkomende VMware-fouten en hoe u deze kunt oplossen.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Fout bericht: er is een interne fout opgetreden. [Server heeft verbinding geweigerd]

Het probleem is een bekend VMware-probleem en treedt op in VDDK 6,7. U moet de Gateway-Service die wordt uitgevoerd in het Azure Migrate apparaat stoppen, [een update downloaden uit VMware KB](https://go.microsoft.com/fwlink/?linkid=2138889)en de Gateway Service opnieuw starten.

Stappen om de Gateway Service te stoppen:

1. Druk op Windows + R, Open Services. msc. Klik op Microsoft Azure Gateway Service en stop deze.
2. U kunt ook een opdracht prompt of Power shell openen en: net stop asrgwy. Zorg ervoor dat u wacht totdat het bericht wordt weer gegeven dat de service niet meer wordt uitgevoerd.

Stappen voor het starten van de Gateway Service:

1. Druk op Windows + R, Open Services. msc. Klik met de rechter muisknop op Microsoft Azure Gateway Service en start deze.
2. U kunt ook een opdracht prompt of Power shell openen en: net start asrgwy.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Fout bericht: er is een interne fout opgetreden. [' Er is een ongeldige momentopname configuratie gedetecteerd. ']

Als u een virtuele machine met meerdere schijven hebt, kan deze fout optreden als u een schijf van de virtuele machine verwijdert. Raadpleeg de stappen in [dit VMware-artikel](https://go.microsoft.com/fwlink/?linkid=2138890)om dit probleem te verhelpen.

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Fout bericht: er is een interne fout opgetreden. [Moment opname genereren is vastgelopen]

Dit probleem treedt op wanneer het genereren van moment opnamen niet meer reageert. Als dit probleem zich voordoet, kunt u de taak moment opname maken stoppen om 95% of 99%. Raadpleeg deze [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138969) om dit probleem op te lossen.

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Fout bericht: er is een interne fout opgetreden. [Kan de schijven niet consolideren op de VM _[redenen]_]

Wanneer we schijven aan het einde van de replicatie cyclus consolideren, mislukt de bewerking. Volg de instructies in [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970) door de juiste _reden_ te selecteren om het probleem op te lossen.

De volgende fouten treden op wanneer er VMware-momentopname bewerkingen worden uitgevoerd: maken, verwijderen of consolideren van schijven. Volg de instructies in de volgende sectie om de fouten te herstellen:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Fout bericht: er is een interne fout opgetreden. [Er wordt al een andere taak uitgevoerd]

Dit probleem treedt op wanneer er sprake is van conflicterende taken van virtuele machines die op de achtergrond worden uitgevoerd, of wanneer er een time-out optreedt voor een taak binnen het vCenter Server. Volg de oplossing die is opgenomen in de volgende [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138891).

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Fout bericht: er is een interne fout opgetreden. [De bewerking is niet toegestaan in de huidige status]

Dit probleem treedt op wanneer vCenter Server beheer agenten niet meer werken. Raadpleeg de oplossing in de volgende [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971)om dit probleem op te lossen.

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Fout bericht: er is een interne fout opgetreden. [Schijf grootte van moment opname is ongeldig]

Dit is een bekend VMware-probleem waarin de schijf grootte die wordt aangegeven door de moment opname nul wordt. Volg de oplossing die is opgegeven in [VMware KB](https://kb.vmware.com/s/).

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Fout bericht: er is een interne fout opgetreden. [Geheugen toewijzing is mislukt. Onvoldoende geheugen.]

Dit gebeurt wanneer de NFC-host buffer onvoldoende geheugen heeft. Om dit probleem op te lossen, moet u de virtuele machine (Compute vMotion) verplaatsen naar een andere host, die vrije bronnen heeft.

## <a name="replication-cycle-failed"></a>Replicatie cyclus is mislukt

**Fout-id:** 181008

**Fout bericht:** VM: ' VMName '. Fout: er zijn geen disksnapshots gevonden voor de momentopname replicatie met de moment opname-id: ' SnapshotID '.

**Mogelijke oorzaken:**

Mogelijke oorzaken zijn:
1. Het pad van een of meer inbegrepen schijven is gewijzigd door de opslag-VMotion.
2. Een of meer inbegrepen schijven zijn niet meer gekoppeld aan de VM.
      
**Advies**

De volgende aanbevelingen worden gegeven
1. Herstel de inbegrepen schijven in het oorspronkelijke pad met behulp van Storage vMotion en schakel vervolgens Storage VMotion uit.
2. Schakel Storage VMotion uit, indien ingeschakeld, stop de replicatie op de virtuele machine en repliceer de virtuele machine opnieuw. Neem contact op met de ondersteuning als het probleem zich blijft voordoen.

## <a name="next-steps"></a>Volgende stappen

Ga door met VM-replicatie en voer [test migratie](./tutorial-migrate-vmware.md#run-a-test-migration)uit.
