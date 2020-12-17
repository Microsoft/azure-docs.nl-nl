---
title: Problemen met de implementatie en detectie van Azure Migrate-apparaten oplossen
description: Hulp bij de implementatie van het apparaat en detectie van machines.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 647e07fe32beedbd54ca6c5bb95c1de9564df2fd
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654010"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Problemen met het Azure Migrate apparaat en de detectie oplossen

Dit artikel helpt u bij het oplossen van problemen bij het implementeren van het [Azure migrate](migrate-services-overview.md) apparaat en het gebruik van het apparaat om on-premises machines te detecteren.


## <a name="whats-supported"></a>Wat wordt ondersteund

[Bekijk](migrate-appliance.md) de vereisten voor het ondersteunings team voor apparaten.


## <a name="invalid-ovf-manifest-entry"></a>"Ongeldige vermelding van OVF-manifest"

Ga als volgt te werk als u de fout melding ' het opgegeven manifest bestand is ongeldig: ongeldige OVF-manifest vermelding ' wordt weer gegeven:

1. Controleer of het bestand van de Azure Migrate apparaat voor apparaten correct is gedownload door de hash-waarde te controleren. [Meer informatie](./tutorial-discover-vmware.md). Als de hash-waarde niet overeenkomt, downloadt u het bestand van de eicellen opnieuw en voert u de implementatie opnieuw uit.
2. Als de implementatie nog steeds mislukt, en u de VMware vSphere-client gebruikt om het OVF-bestand te implementeren, probeert u het te implementeren via de vSphere-webclient. Als de implementatie nog steeds mislukt, kunt u proberen een andere webbrowser te gebruiken.
3. Als u de vSphere-webclient gebruikt en deze probeert te implementeren op vCenter Server 6,5 of 6,7, probeert u de eicellen rechtstreeks op de ESXi-host te implementeren:
   - Maak rechtstreeks verbinding met de ESXi-host (in plaats van vCenter Server) met de webclient (https://<*host IP-adres*>/UI).
   - Selecteer in **huisraad**  >  **inventaris** **bestand**  >  **implementeren OVF sjabloon**. Blader naar de eicellen en voltooi de implementatie.
4. Als de implementatie nog steeds mislukt, neemt u contact op met Azure Migrate ondersteuning.

## <a name="cant-connect-to-the-internet"></a>Kan geen verbinding maken met Internet

Dit kan gebeuren als de apparaatapparaat zich achter een proxy bevindt.

- Zorg ervoor dat u de autorisatiegegevens opgeeft als de proxy deze nodig heeft.
- Als u een firewall proxy op basis van een URL gebruikt om de uitgaande connectiviteit te beheren, voegt u [deze url's](migrate-appliance.md#url-access) toe aan een acceptatie lijst.
- Als u een interceptie proxy gebruikt om verbinding te maken met internet, importeert u het proxy certificaat op de apparaat-VM met behulp van de [volgende stappen](./migrate-appliance.md).


## <a name="clicking-on-login-button-opens-a-new-tab-with-no-device-code"></a>Als u op de knop aanmelding klikt, wordt er een nieuw tabblad geopend zonder apparaatcode

Als u na het klikken op ' aanmelding in het configuratie beheer van het apparaat, een nieuw tabblad wordt geopend zonder code, gaat u terug naar het tabblad toestel Configuration Manager en kunt u de apparaatcode vet letter type vinden onder de knop aanmelding, zoals wordt weer gegeven in de onderstaande scherm afbeelding. Kopieer de code en plak deze op het tabblad Aanmelden om te verifiëren met Azure.

![Apparaatcode](./media/troubleshoot-appliance-discovery/code.png)

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Kan niet aanmelden bij Azure vanuit de Web-App van het apparaat

De fout ' er is een probleem opgetreden bij het aanmelden ' wordt weer gegeven als u het onjuiste Azure-account gebruikt om u aan te melden bij Azure. Deze fout treedt om een aantal redenen op:

- Als u zich aanmeldt bij de toestel webtoepassing voor de open bare Cloud, met behulp van de referenties van het gebruikers account voor de Government Cloud-Portal.
- Als u zich aanmeldt bij de toestel webtoepassing voor de Government-Cloud met behulp van de referenties van het gebruikers account voor de privécloud.

Zorg ervoor dat u de juiste referenties gebruikt.

##  <a name="datetime-synchronization-error"></a>Fout bij de synchronisatie van datum en tijd

Er is een fout opgetreden bij de synchronisatie van datum en tijd (802) geeft aan dat de klok van de server mogelijk niet meer is gesynchroniseerd met de huidige tijd van meer dan vijf minuten. Wijzig de klok tijd op de Collector-VM zodat deze overeenkomt met de huidige tijd:

1. Open een opdracht prompt met beheerders rechten op de VM.
2. Voer **w32tm/TZ** uit om de tijd zone te controleren.
3. Voer **w32tm/resync** uit om de tijd te synchroniseren.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Als deze verbindings fout optreedt, kunt u mogelijk geen verbinding maken met vCenter Server *servername*. com: 9443. De fout gegevens geven aan dat er geen eind punt luistert `https://\*servername*.com:9443/sdk` waarmee het bericht kan worden geaccepteerd.

- Controleer of u de nieuwste versie van het apparaat uitvoert. Als dat niet het geval is, moet u het apparaat bijwerken naar de [nieuwste versie](./migrate-appliance.md).
- Als het probleem zich nog steeds voordoet in de meest recente versie, is het mogelijk dat het apparaat de opgegeven vCenter Server naam niet kan omzetten of dat de opgegeven poort onjuist is. Als de poort niet is opgegeven, probeert de Collector standaard verbinding te maken met poort nummer 443.

    1. Ping *Server naam*. com van het apparaat.
    2. Als stap 1 mislukt, probeert u verbinding te maken met de vCenter-Server met behulp van het IP-adres.
    3. Bepaal het juiste poort nummer om verbinding te maken met vCenter Server.
    4. Controleer of vCenter Server actief is.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Fout 60052/60039: het apparaat is mogelijk niet geregistreerd

- Fout 60052 ' het apparaat is mogelijk niet geregistreerd voor het Azure Migrate-project ' treedt op als het Azure-account dat wordt gebruikt om het apparaat te registreren, onvoldoende machtigingen heeft.
    - Zorg ervoor dat het Azure-gebruikers account dat wordt gebruikt om het apparaat te registreren, ten minste Inzender machtigingen heeft voor het abonnement.
    - Meer [informatie](./migrate-appliance.md#appliance---vmware) over vereiste Azure-rollen en-machtigingen.
- Fout 60039 ' het apparaat is mogelijk niet geregistreerd voor het Azure Migrate-project ' kan zich voordoen als de registratie mislukt, omdat het Azure Migrate project dat is gebruikt voor de registratie van het apparaat, niet kan worden gevonden.
    - In de Azure Portal en controleer of het project bestaat in de resource groep.
    - Als het project niet bestaat, maakt u een nieuw Azure Migrate project in de resource groep en registreert u het apparaat opnieuw. [Meer informatie over het](./create-manage-projects.md#create-a-project-for-the-first-time) maken van een nieuw project.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Fout 60030/60031: Key Vault beheer bewerking is mislukt

Ga als volgt te werk als de fout 60030 of 60031 wordt weer gegeven: ' een Azure Key Vault beheer bewerking is mislukt '
- Zorg ervoor dat het Azure-gebruikers account dat wordt gebruikt om het apparaat te registreren, ten minste Inzender machtigingen heeft voor het abonnement.
- Zorg ervoor dat het account toegang heeft tot de sleutel kluis die is opgegeven in het fout bericht en voer de bewerking opnieuw uit.
- Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning.
- Meer [informatie](./migrate-appliance.md#appliance---vmware) over de vereiste Azure-rollen en-machtigingen.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Fout 60028: detectie kan niet worden gestart

Fout 60028: detectie kan niet worden gestart wegens een fout. De bewerking is mislukt voor de opgegeven lijst van hosts of clusters. Hiermee wordt aangegeven dat de detectie niet kan worden gestart op de hosts die in de fout worden vermeld vanwege een probleem bij het openen of ophalen van VM-gegevens. De rest van de hosts is toegevoegd.

- Voeg de hosts in de fout weer toe met behulp van de optie **host toevoegen** .
- Als er een validatie fout optreedt, raadpleegt u de richt lijnen voor herstel om de fouten op te lossen en probeert u het opnieuw met de optie **detectie opslaan en starten** .

## <a name="error-60025-azure-ad-operation-failed"></a>Fout 60025: de Azure AD-bewerking is mislukt 
Fout 60025: er is een Azure AD-bewerking mislukt. De fout is opgetreden tijdens het maken of bijwerken van de Azure AD-toepassing "treedt op wanneer het Azure-gebruikers account dat wordt gebruikt om de detectie te initiëren, afwijkt van het account dat wordt gebruikt om het apparaat te registreren. Voer een van de volgende handelingen uit:

- Zorg ervoor dat het gebruikers account dat de detectie initieert, hetzelfde is als dat waarmee het apparaat wordt geregistreerd.
- Geef Azure Active Directory machtigingen voor toegang tot de toepassing op voor het gebruikers account waarvoor de detectie bewerking is mislukt.
- Verwijder de resource groep die u eerder hebt gemaakt voor het Azure Migrate project. Maak een andere resource groep om opnieuw te starten.
- Meer [informatie](./migrate-appliance.md#appliance---vmware) over Azure Active Directory toepassings machtigingen.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Fout 50004: kan geen verbinding maken met de host of het cluster

Fout 50004: ' kan geen verbinding maken met een host of cluster omdat de server naam niet kan worden omgezet. WinRM-fout code: 0x803381B9 "kan optreden als de Azure DNS-service voor het apparaat het cluster of de hostnaam die u hebt ingevoerd, niet kan omzetten.

- Als u deze fout op het cluster ziet, wordt de cluster-FQDN.
- U ziet deze fout mogelijk ook voor hosts in een cluster. Dit geeft aan dat het apparaat verbinding kan maken met het cluster, maar dat het cluster hostnamen retourneert die geen FQDN-namen zijn. Om deze fout op te lossen, werkt u het hosts-bestand op het apparaat bij door een toewijzing van het IP-adres en de hostnamen toe te voegen:
    1. Open Klad blok als beheerder.
    2. Open het C:\Windows\System32\Drivers\etc\hosts-bestand.
    3. Voeg het IP-adres en de hostnaam toe aan een rij. Herhaal deze stap voor elke host of elk cluster waar u deze fout ziet.
    4. Sla het hosts-bestand op en sluit het bestand.
    5. Controleer of het apparaat verbinding kan maken met de hosts met behulp van de app voor het beheren van het apparaat. Na 30 minuten ziet u de meest recente informatie voor deze hosts in de Azure Portal.


## <a name="error-60001-unable-to-connect-to-server"></a>Fout 60001: kan geen verbinding maken met de server 

- Zorg ervoor dat er verbinding is tussen het apparaat en de server
- Als het een Linux-server is, moet u verificatie op basis van wacht woorden inschakelen met behulp van de volgende stappen:
    1. Meld u aan bij de Linux-computer en open het SSH-configuratie bestand met behulp van de opdracht ' vi/etc/ssh/sshd_config '
    2. Stel de optie PasswordAuthentication in op Ja. Sla het bestand op.
    3. De SSH-service opnieuw starten door ' service sshd restart ' uit te voeren
- Als het een Windows-Server is, moet u ervoor zorgen dat poort 5985 is geopend om externe WMI-aanroepen mogelijk te maken.
- Als u een GCP Linux-server detecteert en een hoofd gebruiker gebruikt, gebruikt u de volgende opdrachten om de standaard instelling voor basis aanmelding te wijzigen
    1. Meld u aan bij de Linux-computer en open het SSH-configuratie bestand met behulp van de opdracht ' vi/etc/ssh/sshd_config '
    2. Stel de optie PermitRootLogin in op Ja.
    3. De SSH-service opnieuw starten door ' service sshd restart ' uit te voeren

## <a name="error-no-suitable-authentication-method-found"></a>Fout: er is geen geschikte verificatie methode gevonden

Zorg ervoor dat verificatie op basis van wacht woorden is ingeschakeld op de Linux-server door de volgende stappen uit te voeren:
    1. Meld u aan bij de Linux-computer en open het SSH-configuratie bestand met behulp van de opdracht ' vi/etc/ssh/sshd_config '
    2. Stel de optie PasswordAuthentication in op Ja. Sla het bestand op.
    3. De SSH-service opnieuw starten door ' service sshd restart ' uit te voeren


## <a name="discovered-vms-not-in-portal"></a>Gedetecteerde Vm's niet in Portal

Als de detectie status ' detectie wordt uitgevoerd ' is, maar de virtuele machines in de portal nog niet worden weer geven, wacht u een paar minuten:
- Het duurt ongeveer 15 minuten voor een virtuele VMware-machine.
- Het duurt ongeveer twee minuten voor elke toegevoegde host voor de detectie van Hyper-V-VM'S.

Als u een ogen blik geduld en de status niet verandert, selecteert u **vernieuwen** op het tabblad **servers** . Hier ziet u het aantal gedetecteerde servers in Azure Migrate: Server evaluatie en Azure Migrate: Server migratie.

Als dit niet werkt en u de VMware-servers wilt detecteren:

- Controleer of de juiste machtigingen zijn ingesteld voor het vCenter-account dat u hebt opgegeven, met toegang tot ten minste één virtuele machine.
- Azure Migrate kan geen VMware-Vm's detecteren als het vCenter-account toegang heeft ingesteld op het niveau van de vCenter-VM-map. Meer [informatie](set-discovery-scope.md) over het detecteren van scopes.

## <a name="vm-data-not-in-portal"></a>VM-gegevens niet in Portal

Als gedetecteerde Vm's niet worden weer gegeven in de portal of als de VM-gegevens verouderd zijn, wacht u een paar minuten. Het duurt Maxi maal 30 minuten voordat wijzigingen in gedetecteerde VM-configuratie gegevens worden weer gegeven in de portal. Het kan enkele uren duren voordat wijzigingen in de toepassings gegevens worden weer gegeven. Als er na deze tijd geen gegevens zijn, kunt u als volgt vernieuwen

1. Selecteer in **servers**  >  **Azure migrate server-evaluatie** de optie **overzicht**.
2. Selecteer onder **beheren** de optie **status van agent**.
3. Selecteer **agent vernieuwen**.
4. Wacht tot de vernieuwings bewerking is voltooid. Nu worden actuele gegevens weer geven.

## <a name="deleted-vms-appear-in-portal"></a>Verwijderde Vm's worden weer gegeven in de portal

Als u Vm's verwijdert en deze nog steeds worden weer gegeven in de portal, wacht u 30 minuten. Als deze nog steeds worden weer gegeven, moet u vernieuwen zoals hierboven wordt beschreven.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Ik zie geen prestatie gegevens voor sommige netwerk adapters op mijn fysieke servers

Dit kan gebeuren als Hyper-V-virtualisatie is ingeschakeld op de fysieke server. Als gevolg van een product hiaat wordt de netwerk doorvoer vastgelegd op de gedetecteerde virtuele netwerk adapters.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Fout: Het geüploade bestand heeft niet de verwachte indeling
Sommige hulpprogramma's hebben regionale instellingen waardoor het CSV-bestand wordt gemaakt met een puntkomma als scheidingsteken. Wijzig de instellingen om het scheidingsteken in te stellen op een komma.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Ik heb een CSV geïmporteerd, maar ik zie 'Detectie wordt uitgevoerd'
Deze status wordt weer gegeven als het uploaden van CSV-bestanden is mislukt vanwege een validatie fout. Probeer het CSV-bestand opnieuw te importeren. U kunt het foutenrapport van de vorige upload downloaden en de hersteltips in het bestand volgen om de fouten op te lossen. Het foutenrapport kan worden gedownload uit de sectie Details importeren op de pagina Computers detecteren.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Details van toepassing niet zien, zelfs na het bijwerken van gast referenties
De detectie van de toepassing wordt elke 24 uur uitgevoerd. Als u de details onmiddellijk wilt zien, kunt u het volgende vernieuwen. Dit kan een paar minuten duren, afhankelijk van het aantal. van virtuele machines die zijn gedetecteerd.

1. Selecteer in **servers**  >  **Azure migrate server-evaluatie** de optie **overzicht**.
2. Selecteer onder **beheren** de optie **status van agent**.
3. Selecteer **agent vernieuwen**.
4. Wacht tot de vernieuwings bewerking is voltooid. Nu worden actuele gegevens weer geven.

## <a name="unable-to-export-application-inventory"></a>Kan de toepassings inventaris niet exporteren
Zorg ervoor dat de gebruiker die de inventaris van de portal downloadt, over Inzender bevoegdheden beschikt voor het abonnement.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Er is geen geschikte verificatie methode gevonden voor het volt ooien van de authenticatie (PUBLICKEY)
Verificatie op basis van sleutels werkt niet, wachtwoord verificatie gebruiken.

## <a name="common-app-discovery-errors"></a>Veelvoorkomende fouten bij app-detectie

Azure Migrate ondersteunt de detectie van toepassingen, functies en onderdelen met behulp van Azure Migrate: Server Assessment. App-detectie wordt momenteel alleen ondersteund voor VMware. Meer [informatie](how-to-discover-applications.md) over de vereisten en stappen voor het instellen van app-detectie.

Veelvoorkomende fouten bij het detecteren van apps worden in de tabel samenvatten. 

| **Fout** | **Oorzaak** | **Actie** |
|--|--|--|
| 9000: de status van het VMware-hulp programma kan niet worden gedetecteerd. | VMWare-hulpprogram ma's zijn mogelijk niet geïnstalleerd of beschadigd. | Zorg ervoor dat VMware-hulpprogram ma's zijn geïnstalleerd en worden uitgevoerd op de VM. |
| 9001: VMware-hulpprogram ma's zijn niet geïnstalleerd. | VMWare-hulpprogram ma's zijn mogelijk niet geïnstalleerd of beschadigd. | Zorg ervoor dat VMware-hulpprogram ma's zijn geïnstalleerd en worden uitgevoerd op de VM. |
| 9002: VMware-hulpprogram ma's worden niet uitgevoerd. | VMWare-hulpprogram ma's zijn mogelijk niet geïnstalleerd of beschadigd. | Zorg ervoor dat VMware-hulpprogram ma's zijn geïnstalleerd en worden uitgevoerd op de VM. |
| 9003: het type besturings systeem wordt niet ondersteund voor de detectie van gast-VM'S. | Het besturings systeem dat op de server wordt uitgevoerd, is geen Windows of Linux. | Ondersteunde typen besturings systemen zijn alleen Windows en Linux. Als de server inderdaad Windows of Linux is, controleert u het type besturings systeem dat is opgegeven in vCenter Server. |
| 9004: de VM wordt niet uitgevoerd. | De VM is uitgeschakeld. | Zorg ervoor dat de virtuele machine is ingeschakeld. |
| 9005: het type besturings systeem wordt niet ondersteund voor de detectie van gast-VM'S. | Het type besturings systeem wordt niet ondersteund voor de detectie van gast-VM'S. | Ondersteunde typen besturings systemen zijn alleen Windows en Linux. |
| 9006: de URL voor het downloaden van het meta gegevensbestand van de gast is leeg. | Dit kan gebeuren als de detectie agent niet naar behoren werkt. | Het probleem moet in24-uren automatisch oplossen. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| 9007: kan het proces dat de detectie taak in de gast-VM uitvoert, niet vinden. | Dit kan gebeuren als de detectie agent niet goed werkt. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| 9008: kan de status van het gast-VM-proces niet ophalen. | Het probleem kan optreden vanwege een interne fout. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| 9009: het uitvoeren van detectie taken op de server is door Windows UAC verhinderd. | Instellingen voor Windows Gebruikersaccountbeheer (UAC) op de server zijn beperkend en verhinderen de detectie van geïnstalleerde toepassingen. | Configureer in de instellingen voor Gebruikersaccountbeheer op de server de UAC-instelling op een van de twee lagere niveaus. |
| 9010: de VM is uitgeschakeld. | De VM is uitgeschakeld. | Zorg ervoor dat de virtuele machine is ingeschakeld. |
| 9011: gedetecteerd meta gegevensbestand niet gevonden in het bestands systeem van de gast-VM. | Het probleem kan optreden vanwege een interne fout. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| 9012: het bestand met gedetecteerde meta gegevens is leeg. | Het probleem kan optreden vanwege een interne fout. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| 9013: voor elke aanmelding wordt een nieuw tijdelijk profiel gemaakt. | Voor elke aanmelding bij de VMware-VM wordt een nieuw tijdelijk profiel gemaakt. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 9014: kan geen meta gegevens ophalen van het bestands systeem van de gast-VM. | Geen connectiviteit met ESXi-host | Zorg ervoor dat het apparaat verbinding kan maken met poort 443 op de ESXi-host waarop de VM wordt uitgevoerd |
| 9015: de rol gast bewerkingen is niet ingeschakeld op het vCenter-gebruikers account | De rol gast bewerkingen is niet ingeschakeld op het vCenter-gebruikers account. | Zorg ervoor dat de rol gast taken is ingeschakeld op het vCenter-gebruikers account. |
| 9016: kan niet detecteren omdat de gast Operations-agent verouderd is. | VMware-hulpprogram ma's zijn niet juist geïnstalleerd of zijn niet up-to-date. | Controleer of de VMware-hulpprogram ma's correct zijn geïnstalleerd en up-to-date zijn. |
| 9017: bestand met gedetecteerde meta gegevens is niet gevonden op de virtuele machine. | Het probleem kan optreden vanwege een interne fout. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 9018: Power shell is niet geïnstalleerd in de gast-Vm's. | Power shell is niet beschikbaar in de gast-VM. | Installeer Power shell in de gast-VM. |
| 9019: kan niet detecteren vanwege mislukte bewerkingen van de gast-VM. | VMware-gast bewerking is mislukt op de virtuele machine. | Zorg ervoor dat de referenties van de virtuele machine geldig zijn en dat de gebruikers naam in de gast-VM-referenties in de UPN-indeling wordt vermeld. |
| 9020: de machtiging voor het maken van een bestand is geweigerd. | De rol die is gekoppeld aan de gebruiker of het groeps beleid, beperkt de gebruiker het bestand in de map te maken | Controleer of de gast gebruiker beschikt over de machtiging maken voor het bestand in de map. Zie **meldingen** in Server beoordeling voor de naam van de map. |
| 9021: kan het bestand niet maken in het tijdelijke pad van het systeem. | Het hulp programma VMware rapporteert tijdelijk het pad van het systeem in plaats van het tijdelijke pad van gebruikers. | Voer een upgrade uit van de versie van uw VMware-hulp programma boven 10287 (NGC/VI-client indeling). |
| 9022: toegang tot WMI-object is geweigerd. | De rol die aan de gebruiker of het groeps beleid is gekoppeld, beperkt de toegang van de gebruiker tot het WMI-object. | Neem contact op met Microsoft Ondersteuning. |
| 9023: Power shell kan niet worden uitgevoerd als de waarde van de omgevings variabele System root is leeg. | De waarde van de System root-omgevings variabele is leeg voor de gast-VM. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 9024: kan niet detecteren als de waarde van de TEMP-omgevings variabele is leeg. | De waarde van de TEMP-omgevings variabele is leeg voor de gast-VM. | Neem contact op met Microsoft Ondersteuning. |
| 9025: Power shell is beschadigd in de gast-Vm's. | Power shell is beschadigd in de gast-VM. | Installeer Power shell opnieuw in de gast-VM en controleer of Power shell kan worden uitgevoerd op de gast-VM. |
| 9026: kan geen gast bewerkingen uitvoeren op de VM. | Met de VM-status mogen geen gast bewerkingen worden uitgevoerd op de VM. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 9027: Guest Operations agent wordt niet uitgevoerd in de virtuele machine. | Kan geen verbinding maken met de gast Operations-agent die wordt uitgevoerd in de virtuele machine. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 9028: het bestand kan niet worden gemaakt omdat er onvoldoende schijf ruimte is in de VM. | Er is onvoldoende ruimte op de schijf. | Zorg ervoor dat er voldoende ruimte beschikbaar is in de schijf opslag van de virtuele machine. |
| 9029: geen toegang tot Power shell op de gegeven gast-VM-referentie. | Toegang tot Power shell is niet beschikbaar voor de gebruiker. | Zorg ervoor dat de gebruiker die is toegevoegd op het apparaat toegang kan krijgen tot Power shell op de gast-VM. |
| 9030: kan geen gedetecteerde meta gegevens verzamelen omdat de verbinding van de ESXi-host is verbroken. | De ESXi-host heeft de status niet verbonden. | Zorg ervoor dat de ESXi-host met de virtuele machine is verbonden. |
| 9031: kan geen gedetecteerde meta gegevens verzamelen omdat de ESXi-host niet reageert. | De externe host heeft een ongeldige status. | Zorg ervoor dat de ESXi-host waarop de virtuele machine wordt uitgevoerd, actief is en is verbonden. |
| 9032: kan niet detecteren vanwege een interne fout. | Het probleem kan optreden vanwege een interne fout. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 9033: kan niet detecteren omdat de gebruikers naam van de virtuele machine ongeldige tekens bevat. | Er zijn ongeldige tekens aangetroffen in de gebruikers naam. | Geef de VM-referentie opnieuw op om ervoor te zorgen dat er geen ongeldige tekens zijn. |
| 9034: de gebruikers naam die is gegeven, heeft geen UPN-indeling. | De gebruikers naam heeft geen UPN-indeling. | Zorg ervoor dat de gebruikers naam de UPN-indeling (User Principal Name) heeft. |
| 9035: kan niet detecteren omdat de Power shell-taal modus niet is ingesteld op ' volledige taal '. | De taal modus voor Power shell in de gast-VM is niet ingesteld op de volledige taal. | Zorg ervoor dat de Power shell-taal modus is ingesteld op ' volledige taal '. |
| 9037: het verzamelen van gegevens is tijdelijk onderbroken omdat de VM-reactie tijd te hoog is. | Het duurt te lang voordat de gedetecteerde virtuele machine reageert | U hoeft geen actie te ondernemen. Er wordt binnen 24 uur een nieuwe poging gedaan voor toepassings detectie en 3 uur voor afhankelijkheids analyse (zonder agent). |
| 10000: het type besturings systeem wordt niet ondersteund. | Het besturings systeem dat op de server wordt uitgevoerd, is geen Windows of Linux. | Ondersteunde typen besturings systemen zijn alleen Windows en Linux. |
| 10001: script voor Server detectie is niet gevonden op het apparaat. | De detectie werkt niet zoals verwacht. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 10002: de detectie taak is niet in de tijd voltooid. | De detectie agent werkt niet zoals verwacht. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| 10003: het proces dat de detectie taak uitvoert, is afgesloten met een fout. | Het proces dat de detectie taak uitvoert, is afgesloten met een fout. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| 10004: er is geen referentie gegeven voor het type van het gast besturingssysteem. | Referenties voor toegang tot computers van dit type besturings systeem zijn niet opgenomen in het Azure Migrate apparaat. | Referenties toevoegen voor machines op het apparaat |
| 10005: de ingevoerde referenties zijn ongeldig. | De referenties voor toegang tot de server van het apparaat zijn onjuist. | Werk de referenties die zijn opgenomen in het apparaat bij en zorg ervoor dat de server toegankelijk is met behulp van de referenties. |
| 10006: het type gast besturingssysteem wordt niet ondersteund door het referentie archief. | Het besturings systeem dat op de server wordt uitgevoerd, is geen Windows of Linux. | Ondersteunde typen besturings systemen zijn alleen Windows en Linux. |
| 10007: kan de gedetecteerde meta gegevens niet verwerken. | Er is een fout opgetreden tijdens het deserialiseren van de JSON. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 10008: kan geen bestand maken op de server. | Het probleem kan optreden vanwege een interne fout. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |
| 10009: kan geen gedetecteerde meta gegevens schrijven naar een bestand op de server. | Het probleem kan optreden vanwege een interne fout. | Neem contact op met Microsoft Ondersteuning voor een oplossing. |

## <a name="next-steps"></a>Volgende stappen

Stel een apparaat in voor [VMware](how-to-set-up-appliance-vmware.md)-, [Hyper-V-](how-to-set-up-appliance-hyper-v.md)of [fysieke servers](how-to-set-up-appliance-physical.md).