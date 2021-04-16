---
title: Problemen met Azure Backup Server oplossen
description: Problemen met de installatie, registratie van Azure Backup Server en back-up en herstel van toepassingsworkloads oplossen.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 644946ca90c2893ba3d87f9d2ff8bfd8325f4715
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514747"
---
# <a name="troubleshoot-azure-backup-server"></a>Problemen met Azure Backup Server oplossen

Gebruik de informatie in de volgende tabellen om fouten op te lossen die u tijdens het gebruik van Azure Backup Server.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

U wordt aangeraden de volgende validatie uit te voeren voordat u begint met het oplossen Microsoft Azure Backup Server (MABS):

- [Zorg Microsoft Azure Recovery Services-agent (MARS) up-to-date is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zorg ervoor dat er een netwerkverbinding is tussen de MARS-agent en Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Controleer of Microsoft Azure Recovery Services wordt uitgevoerd (in Service-console). Start de bewerking indien nodig opnieuw op en opnieuw
- [Zorg ervoor dat er 5-10% ruimte vrij is op de locatie van de scratch-map](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- Als de registratie mislukt, controleert u of de server waarop u de Azure Backup Server nog niet is geregistreerd bij een andere kluis
- Als een push-installatie mislukt, controleert u of de DPM-agent al aanwezig is. Zo ja, verwijdert u de agent en start u de installatie opnieuw
- [Controleer of er geen ander proces of antivirussoftware conflicten veroorzaakt met Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Zorg ervoor dat de SQL Agent-service wordt uitgevoerd en is ingesteld op automatisch op de MABS-server<br>

## <a name="configure-antivirus-for-mabs-server"></a>Antivirus configureren voor MABS-server

MABS is compatibel met de populairste antivirussoftwareproducten. We raden u aan de volgende stappen uit te voeren om conflicten te voorkomen:

1. **Realtime-bewaking uitschakelen:** schakel realtime bewaking door de antivirussoftware uit voor het volgende:
    - `C:\Program Files<MABS Installation path>\XSD` Map
    - `C:\Program Files<MABS Installation path>\Temp` Map
    - De letter van het Modern Backup Storage volume
    - Replica- en overdrachtslogboeken: om dit te doen, schakelt u realtime bewaking vandpmra.exe **,** die zich in de map `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` bevindt. Realtime-bewaking verslechtert de prestaties omdat de antivirussoftware de replica's elke keer scant wanneer MABS synchroniseert met de beveiligde server en alle betrokken bestanden scant telkens wanneer MABS wijzigingen op de replica's past.
    - Administrator-console: om te voorkomen dat dit invloed heeft op de prestaties, schakelt u realtime bewaking van het **csc.exe** uit. Het **csc.exe** proces is de C-compiler en realtime-bewaking kan de prestaties verslechteren omdat de antivirussoftware bestanden scant die hetcsc.exe-proces genereert wanneer het \# XML-berichten  genereert. **CSC.exe** bevindt zich in de volgende paden:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - Voor de MARS-agent die op de MABS-server is geïnstalleerd, raden we u aan de volgende bestanden en locaties uit te sluiten:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` als een proces
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Scratchlocatie (als u niet de standaardlocatie gebruikt)
2. **Realtime-bewaking op** de beveiligde server uitschakelen: schakel de realtime bewaking van **dpmra.exe** uit. Deze bevindt zich in de map , op de `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` beveiligde server.
3. Antivirussoftware configureren om de geïnfecteerde bestanden op beveiligde servers en de **MABS-server** te verwijderen: als u wilt voorkomen dat replica's en herstelpunten worden beschadiging van gegevens, configureert u de antivirussoftware om geïnfecteerde bestanden te verwijderen in plaats van ze automatisch op te ruimen of in te perken. Automatische opschoon- en quarantining kan ertoe leiden dat de antivirussoftware bestanden wijzigt, waardoor er wijzigingen worden aangebracht die MABS niet kan detecteren.

U moet een handmatige synchronisatie uitvoeren met een consistentie. Controleer de taak telkens wanneer de antivirussoftware een bestand verwijdert uit de replica, zelfs als de replica als inconsistent is gemarkeerd.

### <a name="mabs-installation-folders"></a>MABS-installatiemappen

De standaardinstallatiemappen voor DPM zijn als volgt:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

U kunt ook de volgende opdracht uitvoeren om het pad naar de installatiemap te vinden:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Er zijn ongeldige kluisreferenties ingevoerd

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Registreren bij een kluis | Er zijn ongeldige kluisreferenties opgegeven. Het bestand is beschadigd of heeft niet de meest recente referenties die zijn gekoppeld aan de herstelservice. | Aanbevolen actie: <br> <ul><li> Download het meest recente referentiebestand uit de kluis en probeer het opnieuw. <br>(OF)</li> <li> Als de vorige actie niet werkt, downloadt u de referenties naar een andere lokale map of maakt u een nieuwe kluis. <br>(OF)</li> <li> Werk de datum- en tijdinstellingen bij zoals beschreven in [dit artikel.](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided) <br>(OF)</li> <li> Controleer of c:\windows\temp meer dan 65000 bestanden heeft. Verplaats verouderde bestanden naar een andere locatie of verwijder de items in de map Temp. <br>(OF)</li> <li> Controleer de status van certificaten. <br> a. Open **Computercertificaten beheren** (in Configuratiescherm). <br> b. Vouw het **knooppunt** Persoonlijk en het onderliggende knooppunt **Certificaten uit.**<br> c.  Verwijder het certificaat **Windows Azure Tools.** <br> d. De registratie opnieuw proberen in de Azure Backup client. <br> (OF) </li> <li> Controleer of er groepsbeleid is. </li></ul> |

## <a name="replica-is-inconsistent"></a>De replica is inconsistent

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | De replica is inconsistent | Controleer of de optie voor automatische consistentiecontrole in de wizard Beveiligingsgroep is ingeschakeld. Zie dit artikel voor meer informatie over replicatieopties [en consistentiecontroles.](/system-center/dpm/create-dpm-protection-groups)<br> <ol><li> In het geval van systeemtoestand/BMR-back-up controleert u of Windows Server Back-up is geïnstalleerd op de beveiligde server.</li><li> Controleer op ruimtegerelateerde problemen in de DPM-opslaggroep op de DPM/Microsoft Azure Backup-server en wijs zo nodig opslag toe.</li><li> Controleer de status van de Volume Shadow Copy Service op de beveiligde server. Als de status uitgeschakeld is, stelt u deze in op handmatig starten. Start de service op de server. Ga vervolgens terug naar de DPM/Microsoft Azure Backup Server-console en start de synchronisatie met de consistentiecontrole.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Het maken van een online herstelpunt is mislukt

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Het maken van een online herstelpunt is mislukt | **Foutbericht:** Windows Azure Backup Agent kan geen momentopname van het geselecteerde volume maken. <br> **Tijdelijke oplossing:** probeer de ruimte in het replica- en herstelpuntvolume te vergroten.<br> <br> **Foutbericht:** De Windows Azure Backup-agent kan geen verbinding maken met de OBEngine-service <br> **Tijdelijke oplossing:** controleer of OBEngine bestaat in de lijst met services die op de computer worden uitgevoerd. Als de OBEngine-service niet wordt uitgevoerd, gebruikt u de opdracht 'net start OBEngine' om de OBEngine-service te starten. <br> <br> **Foutbericht:** De wachtwoordzin voor versleuteling voor deze server is niet ingesteld. Configureer een wachtwoordzin voor versleuteling. <br> **Tijdelijke oplossing:** probeer een wachtwoordzin voor versleuteling te configureren. Als dit mislukt, moet u de volgende stappen volgen: <br> <ol><li>Controleer of de scratchlocatie bestaat. Dit is de locatie die wordt vermeld in het register **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, met de naam **ScratchLocation** moet bestaan.</li><li> Als de scratchlocatie bestaat, probeert u het opnieuw te registreren met behulp van de oude wachtwoordzin. *Wanneer u een wachtwoordzin voor versleuteling configureert, moet u deze op een veilige locatie opslaan.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>De oorspronkelijke en externe DPM-servers moeten zijn geregistreerd bij dezelfde kluis

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Herstellen | **Foutcode:** CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Foutbericht:** De oorspronkelijke en externe DPM-servers moeten zijn geregistreerd bij dezelfde kluis | **Oorzaak:** Dit probleem treedt op wanneer u bestanden probeert te herstellen naar de alternatieve server vanaf de oorspronkelijke server met behulp van de optie Extern DPM-herstel, en als de server die wordt hersteld en de oorspronkelijke server van waar de gegevens worden geback-up, niet zijn gekoppeld aan dezelfde Recovery Services-kluis.<br/> <br/>**Tijdelijke oplossing** Zorg ervoor dat zowel de oorspronkelijke als de alternatieve server zijn geregistreerd bij dezelfde kluis om dit probleem op te lossen.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Taken voor het maken van online herstelpunt voor VMware-VM mislukken

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Taken voor het maken van online herstelpunt voor VMware-VM's mislukken. Er is in DPM een fout opgetreden van VMware tijdens het op halen van ChangeTracking-informatie. ErrorCode - FileFaultFault (ID 33621) |  <ol><li> Stel de CTK op VMware voor de betreffende VM's opnieuw in.</li> <li>Controleer of er geen onafhankelijke schijf is op VMware.</li> <li>Stop de beveiliging voor de betrokken VM's en beveilig opnieuw met de **knop** Vernieuwen. </li><li>Voer een CC uit voor de betrokken VM's.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>De agentbewerking is mislukt vanwege een communicatiefout met de DPM-agentcoördinatorservice op de server

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Agent(s) pushen naar beveiligde servers | De agentbewerking is mislukt vanwege een communicatiefout met de DPM Agent Coordinator service op \<ServerName> . | **Als de aanbevolen actie die wordt weergegeven in het product niet werkt, voert u de volgende stappen uit:** <ul><li> Als u een computer koppelt vanuit een niet-vertrouwd domein, volgt u [deze stappen.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br> (OF) </li><li> Als u een computer koppelt vanuit een vertrouwd domein, kunt u problemen oplossen met behulp van de stappen die in dit [blog worden beschreven.](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726) <br>(OF)</li><li> Probeer antivirus uit te proberen als een probleemoplossingsstap. Als het probleem wordt opgelost, wijzigt u de antivirusinstellingen zoals voorgesteld in [dit artikel.](/system-center/dpm/run-antivirus-server)</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>De metagegevens van het register kunnen niet worden bijgewerkt met Setup

| Bewerking | Foutdetails | Tijdelijke oplossing |
|-----------|---------------|------------|
|Installatie | Setup kan registermetagegevens niet bijwerken. Deze updatefout kan leiden tot overgebruik van het opslagverbruik. Om deze update te voorkomen, moet u de registerinvoer voor ReFS-inkorten bijwerken. | Pas de registersleutel **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTsystem aan.** Stel de waarde Dword in op 1. |
|Installatie | Setup kan registermetagegevens niet bijwerken. Deze updatefout kan leiden tot overgebruik van het opslagverbruik. Werk de registerinvoer Volume SnapOptimization bij om dit te voorkomen. | Maak de registersleutel **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** met een lege tekenreekswaarde. |

## <a name="registration-and-agent-related-issues"></a>Registratie- en agentgerelateerde problemen

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Agent(s) pushen naar beveiligde servers | De referenties die zijn opgegeven voor de server zijn ongeldig. | **Als de aanbevolen actie die wordt weergegeven in het product niet werkt, moet u de volgende stappen volgen:** <br> Probeer de beveiligingsagent handmatig te installeren op de productieserver, zoals is opgegeven in [dit artikel.](/system-center/dpm/deploy-dpm-protection-agent)|
| Azure Backup agent kan geen verbinding maken met de Azure Backup service (id: 100050) | De Azure Backup-agent kan geen verbinding maken met de Azure Backup service. | **Als de aanbevolen actie die wordt weergegeven in het product niet werkt, moet u de volgende stappen volgen:** <br>1. Voer de volgende opdracht uit vanaf een prompt met verhoogde **Explorer\iexplore.exe: psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe.** Hiermee opent u Internet Explorer venster. <br/> 2. Ga naar **Hulpprogramma's**  >  **Internetopties**  >  **LAN-instellingen**  >  **verbindingen.** <br/> 3. Wijzig de instellingen voor het gebruik van een proxyserver. Geef vervolgens de details van de proxyserver op.<br/> 4. Als uw computer beperkte internettoegang heeft, moet u ervoor zorgen dat de firewallinstellingen op de computer of proxy deze [URL's](install-mars-agent.md#verify-internet-access) en [het IP-adres toestaan.](install-mars-agent.md#verify-internet-access)|
| Azure Backup agent is niet geïnstalleerd | De Microsoft Azure Recovery Services is mislukt. Alle wijzigingen die zijn aangebracht in het systeem door de Microsoft Azure Recovery Services-installatie zijn teruggedraaid. (Id: 4024) | Installeer De Azure-agent handmatig.

## <a name="configuring-protection-group"></a>Beveiligingsgroep configureren

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Beveiligingsgroepen configureren | DPM kan het toepassingsonderdeel op de beveiligde computer (naam van beveiligde computer) niet opsnoemen. | Selecteer **Vernieuwen in** het scherm Gebruikersinterface voor beveiligingsgroep configureren op het relevante niveau van de gegevensbron/het betreffende onderdeel. |
| Beveiligingsgroepen configureren | Kan de beveiliging niet configureren | Als de beveiligde server een SQL-server is, controleert u of de rolmachtigingen voor sysadmin zijn opgegeven voor het systeemaccount (NTAuthority\System) op de beveiligde computer, zoals beschreven in dit [artikel](/system-center/dpm/back-up-sql-server).
| Beveiligingsgroepen configureren | Er is onvoldoende vrije ruimte in de opslaggroep voor deze beveiligingsgroep. | De schijven die aan de opslaggroep worden [toegevoegd, mogen geen partitie bevatten.](/system-center/dpm/create-dpm-protection-groups) Verwijder alle bestaande volumes op de schijven. Voeg ze vervolgens toe aan de opslaggroep.|
| Beleidswijziging |Het back-upbeleid kan niet worden gewijzigd. Fout: De huidige bewerking is mislukt vanwege een interne servicefout [0x29834]. Voer de bewerking opnieuw uit nadat enige tijd is verstreken. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning. | **Oorzaak:**<br/>Deze fout treedt op onder drie voorwaarden: wanneer beveiligingsinstellingen zijn ingeschakeld, wanneer u de bewaartermijn probeert te beperken tot onder de minimumwaarden die eerder zijn opgegeven en wanneer u een niet-ondersteunde versie gebruikt. (Niet-ondersteunde versies zijn lager dan Microsoft Azure Backup Server versie 2.0.9052 en Azure Backup Server update 1.) <br/>**Aanbevolen actie:**<br/> Als u wilt doorgaan met beleidsgerelateerde updates, stelt u de retentieperiode in boven de opgegeven minimale bewaarperiode. (De minimale bewaarperiode is zeven dagen voor dagelijks, vier weken voor wekelijks, drie weken voor maandelijks of één jaar voor jaarlijks.) <br><br>Een andere voorkeursbenadering is optioneel om de back-upagent bij te werken en Azure Backup Server gebruik te maken van alle beveiligingsupdates. |

## <a name="backup"></a>Backup

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Er is een onverwachte fout opgetreden tijdens het uitvoeren van de taak. Het apparaat is niet gereed. | **Als de aanbevolen actie die wordt weergegeven in het product niet werkt, moet u de volgende stappen volgen:** <br> <ul><li>Stel de Opslagruimte voor schaduwkopie in op onbeperkt voor de items in de beveiligingsgroep en voer vervolgens de consistentiecontrole uit.<br></li> (OF) <li>Probeer de bestaande beveiligingsgroep te verwijderen en meerdere nieuwe groepen te maken. Elke nieuwe beveiligingsgroep moet een afzonderlijk item hebben.</li></ul> |
| Backup | Als u alleen een back-up maakt van de systeemtoestand, controleert u of er voldoende vrije ruimte is op de beveiligde computer om de systeemtoestandback-up op te slaan. | <ol><li>Controleer of Windows Server Back-up is geïnstalleerd op de beveiligde computer.</li><li>Controleer of er voldoende ruimte is op de beveiligde computer voor de systeemtoestand. De eenvoudigste manier om dit te controleren, is door naar de beveiligde computer te gaan, Windows Server Back-up openen, door de selecties te klikken en BMR te selecteren. De gebruikersinterface geeft vervolgens aan hoeveel ruimte er nodig is. Open het lokale back-upschema van **WSB**  >    >    >  **Selecteer Back-upconfiguratie**  >  **Volledige server** (grootte wordt weergegeven). Gebruik deze grootte voor verificatie.</li></ol>
| Backup | Back-upfout voor BMR | Als de BMR-grootte groot is, verplaatst u enkele toepassingsbestanden naar het besturingssysteemstation en doet u het opnieuw. |
| Backup | De optie voor het opnieuw veiligen van een VMware-VM op een nieuwe Microsoft Azure Backup Server wordt niet als beschikbaar om toe te voegen. | VMware-eigenschappen worden gewezen op een oude, niet-Microsoft Azure Backup server. Ga als volgt te werk om het probleem op te lossen:<br><ol><li>Ga in VCenter (SC-VMM-equivalent) naar het **tabblad Samenvatting** en vervolgens **naar Aangepaste kenmerken.**</li>  <li>Verwijder de oude Microsoft Azure Backup servernaam uit de **DPMServer-waarde.**</li>  <li>Terug de nieuwe Microsoft Azure Backup Server en wijzig de PG.  Nadat u de knop **Vernieuwen hebt** geselecteerd, wordt de VM weergegeven met een selectievakje dat beschikbaar is om aan de beveiliging toe te voegen.</li></ol> |
| Backup | Fout bij het openen van bestanden/gedeelde mappen | Wijzig de antivirusinstellingen zoals voorgesteld in dit artikel [Antivirussoftware uitvoeren op de DPM-server.](/system-center/dpm/run-antivirus-server)|

## <a name="change-passphrase"></a>Wachtwoordzin wijzigen

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Wachtwoordzin wijzigen |De ingevoerde pincode voor beveiliging is onjuist. Geef de juiste beveiligingspin pincode op om deze bewerking te voltooien. |**Oorzaak:**<br/> Deze fout treedt op wanneer u een ongeldige of verlopen beveiligingspincode op geeft tijdens het uitvoeren van een kritieke bewerking (zoals het wijzigen van een wachtwoordzin). <br/>**Aanbevolen actie:**<br/> Als u de bewerking wilt voltooien, moet u een geldige pincode voor beveiliging invoeren. Als u de pincode wilt op halen, meld u zich aan bij Azure Portal en gaat u naar de Recovery Services-kluis. Ga vervolgens naar  >  **InstellingenEigenschappen**  >  **Beveiligingspin pincode genereren.** Gebruik deze pincode om de wachtwoordzin te wijzigen. |
| Wachtwoordzin wijzigen |Bewerking is mislukt. Id: 120002 |**Oorzaak:**<br/>Deze fout treedt op wanneer beveiligingsinstellingen zijn ingeschakeld of wanneer u de wachtwoordzin probeert te wijzigen wanneer u een niet-ondersteunde versie gebruikt.<br/>**Aanbevolen actie:**<br/> Als u de wachtwoordzin wilt wijzigen, moet u eerst de back-upagent bijwerken naar de minimumversie, 2.0.9052. U moet ook een Azure Backup Server bijwerken naar minimaal update 1 en vervolgens een geldige beveiligingspin pincode invoeren. Als u de pincode wilt op halen, moet u zich Azure Portal en gaat u naar de Recovery Services-kluis. Ga vervolgens naar  >  **InstellingenEigenschappen**  >  **Beveiligingspin pincode genereren.** Gebruik deze pincode om de wachtwoordzin te wijzigen. |

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| E-mailmeldingen instellen met een werk- of schoolaccount |Fout-id: 2013| **Oorzaak:**<br> Werk- of schoolaccount gebruiken <br>**Aanbevolen actie:**<ol><li> Het eerste wat u moet controleren, is dat 'Anonieme relay toestaan op een ontvangstconnector' voor uw DPM-server is ingesteld op Exchange. Zie Allow Anonymous Relay on a Receive Connector (Anonieme relay toestaan op een ontvangstconnector) voor [meer informatie over het configureren van deze configuratie.](/exchange/mail-flow/connectors/allow-anonymous-relay)</li> <li> Als u geen interne SMTP-relay kunt gebruiken en deze moet instellen met behulp van uw Office 365-server, kunt u IIS instellen als een relay. Configureer de DPM-server om [de SMTP door te geven naar Office 365 met behulp van IIS.](/exchange/mail-flow/test-smtp-with-telnet)<br><br>  Zorg ervoor dat u de gebruikersindeling \@ domain.com en *niet domein\gebruiker.*<br><br><li>Wijs DPM toe om de naam van de lokale server te gebruiken als SMTP-server, poort 587. Wijs vervolgens het e-mailadres van de gebruiker aan waar de e-mailberichten vandaan moeten komen.<li> De gebruikersnaam en het wachtwoord op de DPM SMTP-installatiepagina moeten voor een domeinaccount in het domein zijn dat DPM gebruikt. </li><br> Wanneer u het ADRES van de SMTP-server wijzigt, wijzigt u de nieuwe instellingen, sluit u het instellingenvak en opent u het opnieuw om te controleren of de nieuwe waarde wordt weergegeven.  Door simpelweg te wijzigen en te testen, worden de nieuwe instellingen mogelijk niet altijd van kracht. Testen op deze manier is dus een best practice.<br><br>U kunt deze instellingen op elk moment tijdens dit proces wissen door de DPM-console te sluiten en de volgende registersleutels te bewerken: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Verwijder de sleutels SMTPPassword en SMTPUserName.** U kunt ze weer toevoegen aan de gebruikersinterface wanneer u deze opnieuw start.

## <a name="common-issues"></a>Algemene problemen

In deze sectie worden de veelvoorkomende fouten be behandelt die kunnen optreden tijdens het gebruik Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Foutbericht | Aanbevolen actie |
-- | --
Back-up is mislukt omdat de back-upreplica van de schijf ongeldig is of ontbreekt. | U kunt dit probleem oplossen door de onderstaande stappen te controleren en de bewerking opnieuw uit te voeren: <br/> 1. Een schijfherstelpunt maken<br/> 2. Consistentiecontrole uitvoeren op de gegevensbron <br/> 3. Stop de beveiliging van de gegevensbron en configureer vervolgens de beveiliging voor deze gegevensbron opnieuw

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Foutbericht | Aanbevolen actie |
-- | --
Momentopname van bronvolume is mislukt omdat metagegevens op de replica ongeldig zijn. | Maak een schijfherstelpunt van deze gegevensbron en maak opnieuw een online back-up

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Foutbericht | Aanbevolen actie |
-- | --
Momentopname van bronvolume is mislukt vanwege inconsistente gegevensbronreplica. | Voer een consistentiecontrole uit op deze gegevensbron en probeer het opnieuw

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Foutbericht | Aanbevolen actie |
-- | --
De back-up is mislukt omdat de back-upreplica van de schijf niet kan worden gekloond.| Zorg ervoor dat alle vorige replicabestanden voor schijfback-ups (.vhdx) zijn ontkoppeld en dat er geen back-up van schijf naar schijf wordt uitgevoerd tijdens online back-ups
