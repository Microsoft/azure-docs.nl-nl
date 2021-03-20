---
title: Problemen met Azure Backup Server oplossen
description: Problemen met de installatie, registratie van Azure Backup Server en back-up en herstel van werk belastingen van toepassingen oplossen.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 09e5fe5da7e316257cbbdcb89074fe8a4bc692c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91403004"
---
# <a name="troubleshoot-azure-backup-server"></a>Problemen met Azure Backup Server oplossen

Gebruik de informatie in de volgende tabellen om fouten op te lossen die optreden tijdens het gebruik van Azure Backup Server.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

U wordt aangeraden de volgende validatie uit te voeren voordat u begint met het oplossen van problemen Microsoft Azure Backup Server (MABS):

- [Controleren of de MARS-agent (Microsoft Azure Recovery Services) up-to-date is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zorg ervoor dat er een netwerk verbinding is tussen MARS-agent en Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Controleer of Microsoft Azure Recovery Services wordt uitgevoerd (in Service-console). Start indien nodig opnieuw op en voer de bewerking opnieuw uit
- [Zorg ervoor dat er meer dan 5-10% vrije volume ruimte beschikbaar is op de locatie van de Scratch map](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- Als de registratie mislukt, controleert u of de server waarop u wilt installeren Azure Backup Server nog niet is geregistreerd bij een andere kluis
- Als een push-installatie mislukt, controleert u of de DPM-agent al aanwezig is. Zo ja, verwijdert u de agent en start u de installatie opnieuw
- [Controleer of er geen ander proces of antivirussoftware conflicten veroorzaakt met Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Zorg ervoor dat de SQL Agent-service wordt uitgevoerd en is ingesteld op automatisch in de MABS-server<br>

## <a name="configure-antivirus-for-mabs-server"></a>Anti virus voor MABS-server configureren

MABS is compatibel met de populairste antivirus software producten. We raden u aan de volgende stappen uit te voeren om conflicten te voor komen:

1. **Real-time bewaking uitschakelen** : Schakel realtime-bewaking door de antivirus software uit voor het volgende
    - `C:\Program Files<MABS Installation path>\XSD` map
    - `C:\Program Files<MABS Installation path>\Temp` map
    - Stationsletter van Modern Backup Storage volume
    - Replica-en overdrachts logboeken: Hiervoor schakelt u real-time bewaking van **dpmra.exe** uit, dat zich in de map bevindt `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . Real-time bewaking vermindert de prestaties omdat de antivirus software elke keer dat MABS synchroniseert met de beveiligde server scant en alle betrokken bestanden telkens scant wanneer MABS wijzigingen in de replica's toepast.
    - Administrator-console: Schakel real-time bewaking van het **csc.exe** proces uit om de prestaties te voor komen. Het **csc.exe** proces is de C- \# compiler en realtime-bewaking kan de prestaties verslechteren omdat de antivirus software bestanden scant die het **csc.exe** proces verzendt bij het genereren van XML-berichten. **CSC.exe** bevindt zich in de volgende paden:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - Voor de MARS-agent die is geïnstalleerd op de MABS-server, wordt u aangeraden de volgende bestanden en locaties uit te sluiten:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` Als een proces
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Scratch locatie (als u geen gebruik maakt van de standaard locatie)
2. **Realtime-bewaking uitschakelen op de beveiligde server**: Schakel de real-time bewaking van **dpmra.exe**, dat zich in de map bevindt `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` , uit op de beveiligde server.
3. Antivirus **software configureren om de geïnfecteerde bestanden op beveiligde servers en de MABS-server te verwijderen**: als u gegevens beschadiging van replica's en herstel punten wilt voor komen, configureert u de antivirus software zo dat geïnfecteerde bestanden worden verwijderd en niet automatisch worden gereinigd of in quarantaine. Automatisch opschonen en in quarantaine kan ervoor zorgen dat de antivirus software bestanden wijzigt, waardoor wijzigingen die MABS niet kunnen detecteren.

U moet een hand matige synchronisatie uitvoeren met een consistentie. Controleer de taak elke keer dat de antivirus software een bestand uit de replica verwijdert, zelfs als de replica is gemarkeerd als inconsistent.

### <a name="mabs-installation-folders"></a>MABS-installatie mappen

De standaard installatie mappen voor DPM zijn als volgt:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

U kunt ook de volgende opdracht uitvoeren om het pad naar de installatiemap te vinden:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Er zijn ongeldige kluisreferenties ingevoerd

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Registreren bij een kluis | Er zijn ongeldige kluisreferenties opgegeven. Het bestand is beschadigd of heeft niet de meest recente referenties die zijn gekoppeld aan de herstel service. | Aanbevolen actie: <br> <ul><li> Down load het meest recente referentie bestand van de kluis en probeer het opnieuw. <br>OF</li> <li> Als de vorige actie niet werkt, kunt u de referenties downloaden naar een andere lokale map of een nieuwe kluis maken. <br>OF</li> <li> Probeer de datum-en tijd instellingen bij te werken zoals beschreven in [dit artikel](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided). <br>OF</li> <li> Controleer of c:\Windows\Temp meer dan 65000 bestanden heeft. Verplaats verouderde bestanden naar een andere locatie of verwijder de items in de map Temp. <br>OF</li> <li> Controleer de status van certificaten. <br> a. Open **computer certificaten beheren** (in het configuratie scherm). <br> b. Vouw het **persoonlijke** knoop punt en de onderliggende knooppunt **certificaten** uit.<br> c.  Verwijder het certificaat **Windows Azure-Hulpprogram ma's**. <br> d. Voer de registratie opnieuw uit op de Azure Backup-client. <br> OF </li> <li> Controleer of er groeps beleid aanwezig is. </li></ul> |

## <a name="replica-is-inconsistent"></a>De replica is inconsistent

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | De replica is inconsistent | Controleer of de optie Automatische consistentie controle in de wizard beveiligings groep is ingeschakeld. Zie [dit artikel](/system-center/dpm/create-dpm-protection-groups) voor meer informatie over replicatie opties en consistentie controles.<br> <ol><li> Controleer in het geval van systeem status-BMR back-up of Windows Server Back-up is geïnstalleerd op de beveiligde server.</li><li> Controleer op ruimte-gerelateerde problemen in de DPM-opslag groep op de DPM/Microsoft Azure Backup-Server en wijs opslag toe als dat nodig is.</li><li> Controleer de status van de Volume Shadow Copy Service op de beveiligde server. Als de status is uitgeschakeld, stelt u deze in op hand matig starten. Start de service op de server. Ga vervolgens terug naar de DPM/Microsoft Azure Backup Server-console en start de synchronisatie met de consistentie controle taak.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Het maken van een onlineherstelpunt is mislukt

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Het maken van een onlineherstelpunt is mislukt | **Fout bericht**: er kan geen moment opname van het geselecteerde volume worden gemaakt met de Windows Azure backup-agent. <br> **Tijdelijke oplossing**: Probeer de ruimte in het replica-en herstel punt volume te verg Roten.<br> <br> **Fout bericht**: de Windows Azure backup-agent kan geen verbinding maken met de OBEngine-service <br> **Tijdelijke oplossing**: Controleer of de OBEngine bestaat in de lijst met actieve services op de computer. Als de OBEngine-service niet wordt uitgevoerd, gebruikt u de opdracht ' net start OBEngine ' om de OBEngine-service te starten. <br> <br> **Fout bericht**: de wachtwoordzin voor versleuteling voor deze server is niet ingesteld. Configureer een wachtwoordzin voor versleuteling. <br> **Tijdelijke oplossing**: Probeer een coderings wachtwoord code te configureren. Als dit mislukt, voert u de volgende stappen uit: <br> <ol><li>Controleer of de Scratch locatie bestaat. Dit is de locatie die wordt vermeld in het REGI ster **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, met de naam **ScratchLocation** moet bestaan.</li><li> Als de Scratch locatie bestaat, kunt u opnieuw registreren met de oude wachtwoordzin. *Wanneer u een wachtwoordzin voor versleuteling configureert, kunt u deze op een veilige locatie opslaan.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>De oorspronkelijke en externe DPM-servers moeten worden geregistreerd bij dezelfde kluis

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Herstellen | **Fout code**: CBPServerRegisteredVaultDontMatchWithCurrent/kluis referentie fout: 100110 <br/> <br/>**Fout bericht**: de oorspronkelijke en externe DPM-servers moeten zijn geregistreerd bij dezelfde kluis | **Oorzaak**: dit probleem treedt op wanneer u bestanden probeert te herstellen op de alternatieve server van de oorspronkelijke server met de externe DPM-herstel optie, en als de server die wordt hersteld en de oorspronkelijke server van waaruit de gegevens worden opgeslagen, niet zijn gekoppeld aan dezelfde Recovery Services kluis.<br/> <br/>**Tijdelijke oplossing** Om dit probleem op te lossen, moet u ervoor zorgen dat de oorspronkelijke en alternatieve server zijn geregistreerd bij dezelfde kluis.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Taken voor het maken van online herstel punten voor VMware VM mislukken

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Taken voor het maken van online herstel punten voor VMware VM mislukken. Er is in DPM een fout opgetreden in VMware tijdens het ophalen van change tracking-gegevens. Error code-FileFaultFault (ID 33621) |  <ol><li> Stel de CTK op VMware opnieuw in voor de betrokken Vm's.</li> <li>Controleer of de onafhankelijke schijf niet aanwezig is op VMware.</li> <li>Stop de beveiliging van de betrokken Vm's en beveilig de gegevens opnieuw met de knop **vernieuwen** . </li><li>Voer een CC uit voor de betrokken Vm's.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>De agent bewerking is mislukt vanwege een communicatie fout met de service DPM agent Coordinator op de server

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Agent (s) pushen naar beveiligde servers | De agent bewerking is mislukt vanwege een communicatie fout met de DPM Agent Coordinator-service op \<ServerName> . | **Als de aanbevolen actie die wordt weer gegeven in het product niet werkt, voert u de volgende stappen uit**: <ul><li> Als u een computer koppelt van een niet-vertrouwd domein, volgt u [deze stappen](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains). <br> OF </li><li> Als u een computer koppelt van een vertrouwd domein, kunt u het probleem oplossen met behulp van de stappen die worden beschreven in [deze blog](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>OF</li><li> Probeer anti virus uit te scha kelen als een stap voor het oplossen van problemen. Als het probleem is opgelost, wijzigt u de antivirus instellingen, zoals wordt voorgesteld in [dit artikel](/system-center/dpm/run-antivirus-server).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>De metagegevens van het register kunnen niet worden bijgewerkt met Setup

| Bewerking | Foutdetails | Tijdelijke oplossing |
|-----------|---------------|------------|
|Installatie | Setup kan de meta gegevens van het REGI ster niet bijwerken. Deze update fout kan leiden tot overbelasting van het opslag verbruik. Als u wilt voor komen dat deze update het register item ReFS bijsnijden. | Wijzig de register sleutel **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Stel de DWORD-waarde in op 1. |
|Installatie | Setup kan de meta gegevens van het REGI ster niet bijwerken. Deze update fout kan leiden tot overbelasting van het opslag verbruik. U kunt dit voor komen door de register vermelding volume SnapOptimization bij te werken. | Maak de register sleutel **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** met een lege teken reeks waarde. |

## <a name="registration-and-agent-related-issues"></a>Registratie en problemen met betrekking tot agents

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Agent (s) pushen naar beveiligde servers | De referenties die voor de server zijn opgegeven, zijn ongeldig. | **Als de aanbevolen actie die wordt weer gegeven in het product niet werkt, voert u de volgende stappen uit**: <br> Probeer de beveiligings agent hand matig te installeren op de productie server zoals opgegeven in [dit artikel](/system-center/dpm/deploy-dpm-protection-agent).|
| Azure Backup Agent kan geen verbinding maken met de Azure Backup-Service (ID: 100050) | De Azure Backup-Agent kan geen verbinding maken met de Azure Backup-service. | **Als de aanbevolen actie die wordt weer gegeven in het product niet werkt, voert u de volgende stappen uit**: <br>1. Voer de volgende opdracht uit vanaf een prompt met verhoogde bevoegdheid: **PsExec-i-s "C:\Program Files\Internet Explorer\iexplore.exe**. Hiermee opent u het venster Internet Explorer. <br/> 2. Ga naar **extra**  >  **Internet opties**  >  **verbindingen**  >  **LAN-instellingen**. <br/> 3. Wijzig de instellingen voor het gebruik van een proxy server. Geef vervolgens de proxyserver gegevens op.<br/> 4. als uw computer beperkte internet toegang heeft, moet u ervoor zorgen dat de firewall instellingen op de computer of de proxy deze [url's](install-mars-agent.md#verify-internet-access) en dit [IP-adres](install-mars-agent.md#verify-internet-access)toestaan.|
| Installatie van Azure Backup-agent mislukt | De installatie van Microsoft Azure Recovery Services is mislukt. Alle wijzigingen die zijn aangebracht in het systeem door de Microsoft Azure Recovery Services-installatie zijn teruggedraaid. (ID: 4024) | Installeer de Azure-agent hand matig.

## <a name="configuring-protection-group"></a>Beveiligings groep configureren

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Beveiligings groepen configureren | DPM kan het toepassings onderdeel op de beveiligde computer (beveiligde computer naam) niet opsommen. | Selecteer **vernieuwen** in het scherm gebruikers interface van beveiligings groep configureren op het relevante gegevens bron/onderdeel niveau. |
| Beveiligings groepen configureren | Kan de beveiliging niet configureren | Als de beveiligde server een SQL-Server is, controleert u of de sysadmin-rolmachtigingen zijn toegewezen aan het systeem account (NTAuthority\System) op de beveiligde computer, zoals beschreven in [dit artikel](/system-center/dpm/back-up-sql-server).
| Beveiligings groepen configureren | Er is onvoldoende vrije ruimte in de opslag groep voor deze beveiligings groep. | De schijven die worden toegevoegd aan de opslag groep [mogen geen partitie bevatten](/system-center/dpm/create-dpm-protection-groups). Verwijder alle bestaande volumes op de schijven. Voeg ze vervolgens toe aan de opslag groep.|
| Beleids wijziging |Het back-upbeleid kan niet worden gewijzigd. Fout: de huidige bewerking is mislukt vanwege een interne service fout [0x29834]. Voer de bewerking opnieuw uit nadat het is geslaagd. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning. | **Oorzaak:**<br/>Deze fout treedt op onder drie omstandigheden: wanneer de beveiligings instellingen zijn ingeschakeld, kunt u de Bewaar termijn verlagen met de minimum waarden die u eerder hebt opgegeven en wanneer u een niet-ondersteunde versie gebruikt. (Niet-ondersteunde versies zijn lager dan Microsoft Azure Backup Server versie 2.0.9052 en Azure Backup Server Update 1.) <br/>**Aanbevolen actie:**<br/> Als u wilt door gaan met beleids updates, stelt u de retentie periode in boven de opgegeven minimale Bewaar periode. (De minimale Bewaar periode is zeven dagen voor dagelijks, vier weken voor wekelijks, drie weken voor maandelijks of één jaar voor jaarlijks.) <br><br>Een andere voorkeurs benadering is het bijwerken van de back-upagent en Azure Backup Server om alle beveiligings updates te benutten. |

## <a name="backup"></a>Backup

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Backup | Er is een onverwachte fout opgetreden tijdens het uitvoeren van de taak. Het apparaat is niet gereed. | **Als de aanbevolen actie die wordt weer gegeven in het product niet werkt, voert u de volgende stappen uit:** <br> <ul><li>Stel de opslag ruimte voor schaduw kopieën in op onbeperkt op de items in de beveiligings groep en voer de consistentie controle uit.<br></li> OF <li>Probeer de bestaande beveiligings groep te verwijderen en meerdere nieuwe groepen te maken. Elke nieuwe beveiligings groep moet een afzonderlijk item bevatten.</li></ul> |
| Backup | Als u een back-up maakt van alleen een systeem status, controleert u of er voldoende beschik bare ruimte op de beveiligde computer is om de systeem status back-up op te slaan. | <ol><li>Controleer of Windows Server Back-up is geïnstalleerd op de beveiligde computer.</li><li>Controleer of er voldoende ruimte beschikbaar is op de beveiligde computer voor de systeem status. De eenvoudigste manier om dit te controleren is door naar de beveiligde computer te gaan, Windows Server Back-up te openen, door te klikken op de selecties en vervolgens BMR te selecteren. De gebruikers interface geeft vervolgens aan hoeveel ruimte er nodig is. **WSB** back-  >  upschema voor **lokale back**-up openen  >    >  **Selecteer**  >  **volledige server** voor backup-configuratie (grootte wordt weer gegeven). Gebruik deze grootte voor verificatie.</li></ol>
| Backup | Back-up van fout voor BMR | Als de grootte van de BMR groot is, verplaatst u enkele toepassings bestanden naar het station van het besturings systeem en probeert u het opnieuw. |
| Backup | De optie om een virtuele VMware-machine opnieuw te beveiligen op een nieuwe Microsoft Azure Backup-Server, wordt niet weer gegeven als beschikbaar om toe te voegen. | VMware-eigenschappen worden gepuntd op een oud, buiten gebruik gesteld exemplaar van Microsoft Azure Backup Server. Ga als volgt te werk om het probleem op te lossen:<br><ol><li>Ga in VCenter naar het tabblad **Summary** en klik vervolgens op **Custom Attributes**.</li>  <li>Verwijder de oude naam van de Microsoft Azure Backup-Server uit de waarde **DPMServer** .</li>  <li>Ga terug naar de nieuwe Microsoft Azure Backup-Server en wijzig de pagina.  Nadat u de knop **vernieuwen** hebt geselecteerd, wordt de VM weer gegeven met een selectie vakje dat kan worden toegevoegd aan de beveiliging.</li></ol> |
| Backup | Fout bij het openen van bestanden/gedeelde mappen | Wijzig de antivirus instellingen, zoals wordt voorgesteld in dit artikel, om [antivirus software op de DPM-server uit te voeren](/system-center/dpm/run-antivirus-server).|

## <a name="change-passphrase"></a>Wachtwoordzin wijzigen

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| Wachtwoordzin wijzigen |De ingevoerde beveiligings pincode is onjuist. Geef de juiste beveiligings pincode op om deze bewerking te volt ooien. |**Oorzaak:**<br/> Deze fout treedt op wanneer u een ongeldige of verlopen beveiligings pincode invoert tijdens het uitvoeren van een kritieke bewerking (zoals het wijzigen van een wachtwoordzin). <br/>**Aanbevolen actie:**<br/> Als u de bewerking wilt volt ooien, moet u een geldige beveiligings pincode opgeven. Als u de pincode wilt ontvangen, meldt u zich aan bij de Azure Portal en gaat u naar de Recovery Services kluis. Ga vervolgens naar **instellingen**  >  **Eigenschappen**  >  **beveiligings pincode genereren**. Gebruik deze pincode om de wachtwoordzin te wijzigen. |
| Wachtwoordzin wijzigen |De bewerking is mislukt. ID: 120002 |**Oorzaak:**<br/>Deze fout treedt op wanneer de beveiligings instellingen zijn ingeschakeld of wanneer u probeert de wachtwoordzin te wijzigen wanneer u een niet-ondersteunde versie gebruikt.<br/>**Aanbevolen actie:**<br/> Als u de wachtwoordzin wilt wijzigen, moet u de back-upagent eerst bijwerken naar de minimale versie, die 2.0.9052 is. U moet Azure Backup Server ook bijwerken naar het minimum van update 1 en vervolgens een geldige beveiligings pincode invoeren. Als u de pincode wilt ophalen, meldt u zich aan bij de Azure Portal en gaat u naar de Recovery Services kluis. Ga vervolgens naar **instellingen**  >  **Eigenschappen**  >  **beveiligings pincode genereren**. Gebruik deze pincode om de wachtwoordzin te wijzigen. |

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

| Bewerking | Foutdetails | Tijdelijke oplossing |
| --- | --- | --- |
| E-mail meldingen instellen met een werk-of school account |Fout-ID: 2013| **Oorzaak:**<br> Poging om een werk-of school account te gebruiken <br>**Aanbevolen actie:**<ol><li> Het eerste om te zorgen dat "anoniem door sturen toestaan op een receive-connector" voor uw DPM-server is ingesteld op Exchange. Zie voor meer informatie over hoe u dit kunt configureren [anonieme relay toestaan op een receive-connector](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> Als u een interne SMTP-relay niet kunt gebruiken en moet worden ingesteld met behulp van uw Office 365-server, kunt u IIS instellen als een relay. Configureer de DPM-server om [de SMTP door te sturen naar Office 365 met behulp van IIS](/exchange/mail-flow/test-smtp-with-telnet).<br><br>  Zorg ervoor dat u de \@ indeling user domain.com gebruikt en *niet* domein\gebruiker.<br><br><li>Wijs DPM toe om de naam van de lokale server te gebruiken als SMTP-server, poort 587. Ga vervolgens naar het e-mail adres van de gebruiker waarvan de e-mail berichten afkomstig moeten zijn.<li> De gebruikers naam en het wacht woord op de DPM SMTP-installatie pagina moeten voor een domein account in het domein waarop DPM is ingeschakeld. </li><br> Wanneer u het adres van de SMTP-server wijzigt, brengt u de wijziging aan in de nieuwe instellingen, sluit u het vak instellingen en opent u het opnieuw om er zeker van te zijn dat de nieuwe waarde wordt weer gegeven.  Door te wijzigen en te testen worden mogelijk niet altijd de nieuwe instellingen van kracht, dus als u deze wilt testen, is een best practice.<br><br>Op elk gewenst moment tijdens dit proces kunt u deze instellingen wissen door de DPM-console te sluiten en de volgende register sleutels te bewerken: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword en SMTPUserName Keys**. U kunt deze weer toevoegen aan de gebruikers interface wanneer u deze opnieuw start.

## <a name="common-issues"></a>Algemene problemen

In deze sectie worden de veelvoorkomende fouten besproken die kunnen optreden tijdens het gebruik van Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Foutbericht | Aanbevolen actie |
-- | --
Back-up is mislukt omdat de back-upreplica van de schijf ongeldig is of ontbreekt. | Om dit probleem op te lossen, controleert u de onderstaande stappen en voert u de bewerking opnieuw uit: <br/> 1. een schijf herstel punt maken<br/> 2. Voer een consistentie controle uit op de gegevens bron <br/> 3. Stop de beveiliging van de gegevens bron en configureer de beveiliging opnieuw

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Foutbericht | Aanbevolen actie |
-- | --
De moment opname van het bron volume is mislukt, omdat de meta gegevens voor de replica ongeldig zijn. | Maak een schijf herstel punt voor deze gegevens bron en probeer opnieuw online back-up uit te voeren

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Foutbericht | Aanbevolen actie |
-- | --
De moment opname van het bron volume is mislukt omdat de gegevens bron replica niet consistent is. | Voer een consistentie controle uit op deze gegevens bron en probeer het opnieuw

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Foutbericht | Aanbevolen actie |
-- | --
De back-up is mislukt omdat de back-upreplica van de schijf niet kan worden gekloond.| Zorg ervoor dat alle vorige schijf-back-up replica bestanden (. vhdx) niet zijn gekoppeld en dat er geen schijf back-up wordt gemaakt tijdens online back-ups
