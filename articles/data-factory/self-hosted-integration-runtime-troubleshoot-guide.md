---
title: Problemen met een zelf-hostende Integration runtime in Azure Data Factory oplossen
description: Meer informatie over het oplossen van problemen met zelf-hostende Integration runtime in Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
ms.openlocfilehash: 635178999398287649d8630fc5262a385afc48b2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341766"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Problemen met zelf-hostende Integration runtime oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor zelf-hostende Integration runtime in Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Zelf-hostende IR-logboeken van Azure Data Factory verzamelen

Azure Data Factory ondersteunt het weer geven en uploaden van fouten logboeken voor mislukte activiteiten die worden uitgevoerd op zelf-hostende IR/gedeelde IR. U kunt de onderstaande stappen volgen om de fout rapport-ID op te halen en vervolgens de rapport-ID in te voeren om verwante bekende problemen op te sporen.

1. Ga naar de pagina **activiteiten uitvoeren** .

1. Klik onder de kolom **fout** op de knop onder.

    ![Pagina uitvoeringen van activiteit](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. U ziet gerelateerde logboeken voor de uitvoering van de mislukte activiteit. Klik op de knop **Logboeken verzenden** voor verdere ondersteuning.

    ![Logboeken verzenden](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. U kunt Logboeken kiezen die u wilt verzenden. Voor *zelf-hostende IR* kunt u Logboeken uploaden die betrekking hebben op mislukte activiteiten of voor alle logboeken op zelf-hostend IR-knoop punt. Voor *gedeelde IR* kunt u alleen logboeken uploaden die betrekking hebben op mislukte activiteiten.

    ![Logboeken kiezen](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Wanneer de logboeken worden geüpload, kunt u een record van de rapport-ID bijhouden als u meer hulp nodig hebt om het probleem op te lossen.

    ![Logboeken uploaden](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Logboek weer geven en upload aanvragen worden uitgevoerd op alle online zelf-hostende IR-exemplaren. Zorg ervoor dat alle zelf-hostende IR-instanties online zijn in het geval van eventuele ontbrekende Logboeken. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Algemeen probleem of algemene fout met zelf-hostende IR

### <a name="out-of-memory-issue"></a>Probleem met onvoldoende geheugen

#### <a name="symptoms"></a>Symptomen

Het probleem ' OutOfMemoryException ' treedt op tijdens het uitvoeren van opzoek activiteit met gekoppelde IR of zelf-hostende IR.

#### <a name="cause"></a>Oorzaak

Nieuwe activiteiten kunnen voldoen aan het probleem met OOM (OutOfMemory) als de IR-computer op het moment een hoog geheugen gebruik heeft. Het probleem kan worden veroorzaakt door een grote schaal van de uitvoering van gelijktijdige activiteiten en de fout is standaard.

#### <a name="resolution"></a>Oplossing

Controleer het resource gebruik en de uitvoering van gelijktijdige activiteiten op het IR-knoop punt. Pas de interne en activerings tijd van de uitvoering van de activiteit aan om te veel uitvoering op hetzelfde IR-knoop punt te voor komen.


### <a name="tlsssl-certificate-issue"></a>Probleem met TLS/SSL-certificaat

#### <a name="symptoms"></a>Symptomen

Wanneer u TLS/SSL-certificaat probeert in te schakelen (geavanceerd) vanuit **de Configuration Manager van de zelf-hostende IR** -> **Externe toegang via een intranet**, wordt de volgende fout weergegeven nadat u TLS/SSL-certificaat hebt geselecteerd:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

In bovenstaand geval gebruikt de gebruiker certificaat met 'microsoft.com' als laatste item.

#### <a name="cause"></a>Oorzaak

Dit is een bekend probleem in WCF: Met WCF TLS/SSL-validatie wordt alleen de laatste DNSName in SAN gecontroleerd. 

#### <a name="resolution"></a>Oplossing

Een certificaat met jokertekens wordt ondersteund in zelf-hostende IR van Azure Data Factory v2. Dit probleem treedt normaal op omdat het SSL-certificaat niet correct is. De laatste DNSName in SAN moet geldig zijn. Volg de onderstaande stappen om dit te controleren. 
1.  Open de beheer console, dubbel Controleer of het *onderwerp* en de *alternatieve naam* van het onderwerp in de certificaat gegevens. In het bovenstaande geval is het laatste item in *alternatieve naam voor onderwerp*, dat ' DNS-naam = Microsoft.com.com ' is, niet legitiem.
2.  Neem contact op met het certificaat van het probleem met het bedrijf om de onjuiste DNS-naam te verwijderen.

### <a name="concurrent-jobs-limit-issue"></a>Probleem met limiet voor gelijktijdige taken

#### <a name="symptoms"></a>Symptomen

Wanneer u probeert de limiet voor gelijktijdige taken probeert te verhogen vanuit de interface van Azure Data Factory, blijft deze hangen in de fase *bijwerken*.
Het maximum aantal gelijktijdige taken was ingesteld op 24 en u wilt het aantal verhogen zodat taken sneller kunnen worden uitgevoerd. De minimumwaarde die u kunt invoeren is 3 en de maximumwaarde is 32. U hebt de waarde van 24 tot en met 32 verhoogd en op de knop *bijwerken* geklikt, in de gebruikers interface die de update heeft *ontvangen zoals hieronder* wordt weer gegeven. Na het vernieuwen bleef de klant de waarde 24 zien; deze werd nooit bijgewerkt naar 32.

![Status bijwerken](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Oorzaak

Er is een beperking voor de instelling omdat de waarde afhankelijk is van de logicCore en het geheugen van de computer. U kunt deze gewoon wijzigen in een lagere waarde, bijvoorbeeld 24, en het resultaat bekijken.

> [!TIP] 
> - Raadpleeg [dit artikel](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)voor meer informatie over wat het Logic core-aantal is en hoe u het Logic core-aantal van de computer kunt vinden.
> - Zie [dit artikel](https://www.rapidtables.com/calc/math/Log_Calculator.html)voor meer informatie over het berekenen van Math. log.


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Probleem met SSL-certificaat voor zelf-hostende IR Hoge beschikbaarheid

#### <a name="symptoms"></a>Symptomen

Het werkknooppunt van zelf-hostende IR heeft de volgende fout gerapporteerd:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Oorzaak

Bij de afhandeling van problemen met de SSL/TLS-handshake kunnen er problemen optreden met betrekking tot verificatie van de certificaatketen. 

#### <a name="resolution"></a>Oplossing

- Hier volgt een snelle en intuïtieve manier om problemen met de X. 509-certificaat keten op te lossen.
 
    1. Exporteer het certificaat dat moet worden geverifieerd. Ga naar Computercertificaat beheren en zoek het certificaat dat u wilt controleren. Klik met de rechtermuisknop op **Alle taken** -> **Exporteren**.
    
        ![Taken exporteren](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Kopieer het geëxporteerde certificaat naar de client computer. 
    3. Voer aan de clientzijde onderstaande opdracht uit in CMD. Zorg ervoor dat u hieronder *\<certificate path>* en *\<output txt file path>* tijdelijke aanduidingen met gerelateerde paden hebt vervangen.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Bijvoorbeeld:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Controleer of het uitvoer-txt-bestand fouten bevat. U vindt het overzicht van de fouten aan het einde van het txt-bestand.

        Bijvoorbeeld: 

        ![Fout samenvatting](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Als u geen fout ziet aan het einde van het logboek bestand, zoals hieronder wordt weer gegeven, kunt u overwegen de certificaat keten op de client computer op te bouwen.
        
        ![Er is geen fout in het logboek bestand](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Als er AIA, CDP en OCSP zijn geconfigureerd in het certificaat bestand. We kunnen deze op een intuïtieverere manier controleren.
 
    1. U kunt deze informatie opvragen door de details van een certificaat te controleren.
    
        ![Details van certificaat](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Voer onderstaande opdracht uit. Zorg ervoor dat u *\<certificate path>* de tijdelijke aanduiding vervangen door het bijbehorende pad van het certificaat.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Vervolgens wordt het hulpprogramma **voor het ophalen van de URL** geopend. U kunt certificaten van AIA, CDP en OCSP controleren door te klikken op de knop **Ophalen**.

        ![Ophaal knop](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        De certificaatketen kan worden opgebouwd als het certificaat van AIA 'Geverifieerd' is en het certificaat van CDP of OCSP 'Geverifieerd' is.

        Als er een fout optreedt bij het ophalen van AIA, CDP, neem dan contact op met het netwerkteam om de clientcomputer gereed te maken voor verbinding met de doel-URL. Het is voldoende als het HTTP-pad of het LDAP-pad kan worden geverifieerd.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Zelf-hostende IR kan bestand of assembly niet laden

#### <a name="symptoms"></a>Symptomen

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Bijvoorbeeld: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Oorzaak

Als u proces monitor neemt, ziet u het volgende resultaat:

[![Proces monitor](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> U kunt het filter instellen, zoals in de onderstaande scherm afbeelding wordt weer gegeven.
> Hiermee wordt aangegeven dat het dll **System. ValueTuple** zich niet bevindt in de map die is gerelateerd aan de GAC of in *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway*, of in de map *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* .
> In principe wordt eerst geprobeerd om het dll-bestand te laden uit de map *GAC* en vervolgens uit *Shared* en ten slotte uit de map *Gateway*. Daarom kunt u het dll-bestand in een willekeurig pad plaatsen, wat het handigst is.

![Filters instellen](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Oplossing

U kunt vinden dat de **System.ValueTuple.dll** zich bevindt in de map *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* . Kopieer het **System.ValueTuple.dll** naar de map *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* om het probleem op te lossen.

U kunt dezelfde methode gebruiken om problemen met andere ontbrekende bestanden of assembly's op te lossen.

#### <a name="more-information"></a>Meer informatie

De reden waarom u de System.ValueTuple.dll onder *%windir%\Microsoft.NET\assembly* en *%windir%\assembly* ziet, is dat het een .net-gedrag is. 

In de onderstaande fout ziet u duidelijk het assemblage *systeem. ValueTuple* is niet aanwezig. Dit probleem treedt dus op wanneer de toepassing probeert de assembly *System.ValueTuple.dll* te controleren.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Zie [dit artikel](/dotnet/framework/app-domains/gac)voor meer informatie over GAC.


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Controleren op ontbrekende sleutels voor zelf-hostende IR

#### <a name="symptoms"></a>Symptomen

De zelf-hostende integratieruntime gaat plotseling offline zonder sleutel, en onderstaand foutbericht wordt weergegeven in het gebeurtenislogboek: `Authentication Key is not assigned yet`

![De verificatie sleutel ontbreekt](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Oorzaak

- Het knooppunt van de zelf-hostende IR of de logische zelf-hostende IR in de portal is verwijderd.
- Er is een schone verwijdering uitgevoerd.

#### <a name="resolution"></a>Oplossing

Als geen van de bovenstaande oorzaken van toepassing is, kunt u naar de map gaan: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* en controleren of het bestand met de naam **configuraties** is verwijderd. Als deze is verwijderd, volgt u [deze instructies](https://www.netwrix.com/how_to_detect_who_deleted_file.html) om te controleren wie het bestand heeft verwijderd.

![Controleer het configuratie bestand](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Kan zelf-hostende IR niet gebruiken om twee on-premises gegevensarchieven te verbinden

#### <a name="symptoms"></a>Symptomen

Na het maken van zelf-hostende IR's voor zowel bron- als doelgegevensarchieven, wilt u de twee IR's verbinden om een kopie te verkrijgen. Als de gegevens archieven zijn geconfigureerd in verschillende VNETs of als ze het gateway mechanisme niet begrijpen, raakt u fouten als: *het stuur programma van de bron is niet gevonden in de doel-IR*. *de bron is niet toegankelijk voor de doel-IR*.
 
#### <a name="cause"></a>Oorzaak

De zelf-hostende IR is ontworpen als een centraal knooppunt van een kopieeractiviteit, niet als een clientagent die voor elk gegevensarchief moet worden geïnstalleerd.
 
In bovenstaand geval moet de gekoppelde service voor elk gegevensarchief met dezelfde IR worden gemaakt en moet de IR via het netwerk toegang hebben tot beide gegevensarchieven. Ongeacht of de IR is geïnstalleerd met brongegevensarchief, doelgegevensarchief of op een derde computer, als twee gekoppelde services worden gemaakt met verschillende IR's, maar worden gebruikt in dezelfde kopieeractiviteit, wordt de doel-IR gebruikt en moeten de stuurprogramma's voor beide gegevensarchieven op de doel-IR-computer worden geïnstalleerd.

#### <a name="resolution"></a>Oplossing

Installeer stuurprogramma's voor zowel de bron als de bestemming op de doel-IR en zorg ervoor dat deze toegang heeft tot het brongegevensarchief.
 
Als het verkeer het netwerk tussen twee gegevensarchieven niet kan passeren (bijvoorbeeld omdat ze zijn geconfigureerd in twee VNET's), kunt u de kopie niet in één activiteit voltooien, zelfs niet als IR is geïnstalleerd. In dat geval kunt u twee kopieeractiviteiten maken met twee IRs, elk in een VENT: 1 IR om te kopiëren uit gegevensarchief 1 naar Azure Blob Storage, een andere om te kopiëren van Azure Blob Storage naar gegevensarchief 2. Dit kan de vereiste simuleren om de IR te gebruiken voor het maken van een brug tussen twee niet-verbonden gegevensarchieven.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Probleem met de synchronisatie van referenties heeft tot gevolg dat referenties uit Hoge beschikbaarheid verloren gaan

#### <a name="symptoms"></a>Symptomen

De referentie van de gegevensbron 'XXXXXXXXXX' is verwijderd van het huidige knooppunt voor Integration Runtime met nettolading "Wanneer u de koppelingsservice op de Azure-portal verwijdert, of wanneer de taak de verkeerde nettolading heeft, moet u opnieuw een nieuwe koppelingsservice maken met uw referentie".

#### <a name="cause"></a>Oorzaak

Uw zelf-hostende IR is in de modus Hoge beschikbaarheid gebouwd met twee knooppunten, maar deze hebben niet de status voor synchronisatie van referenties. Dat betekent dat de referenties die zijn opgeslagen in het dispatcherknooppunt niet worden gesynchroniseerd met andere werkknooppunten. Als er een failover optreedt van het dispatcherknooppunt naar het werkknooppunt, maar de referenties zich alleen in het vorige dispatcherknooppunt bevinden, mislukt de taak wanneer er wordt geprobeerd om toegang te krijgen tot referenties.

#### <a name="resolution"></a>Oplossing

De enige manier om dit probleem te voorkomen, is door ervoor te zorgen dat de twee knooppunten de status voor synchronisatie van referenties hebben. Anders moet u de referenties voor de nieuwe dispatcher opnieuw invoeren.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Kan het certificaat niet kiezen vanwege een ontbrekende persoonlijke sleutel

#### <a name="symptoms"></a>Symptomen

1.  Importeer een PFX-bestand in het certificaatarchief.
2.  Wanneer u het certificaat selecteert via de Configuration Manager van de IR, wordt de volgende foutmelding gegenereerd:

    ![Persoonlijke sleutel ontbreekt](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Oorzaak

- Het gebruikersaccount heeft een beperkte bevoegdheid en heeft geen toegang tot de persoonlijke sleutel.
- Het certificaat is gegenereerd als handtekening, maar niet als sleuteluitwisseling.

#### <a name="resolution"></a>Oplossing

1.  Gebruik een account met uitgebreide bevoegdheden dat toegang heeft tot de persoonlijke sleutel om de gebruikersinterface te kunnen gebruiken.
2.  Voer de onderstaande opdracht uit om het certificaat te importeren:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Zelf-hostende IR-installatie

### <a name="the-integration-runtime-registration-error"></a>De Integration Runtime registratie fout 

#### <a name="symptoms"></a>Symptomen

Soms willen we zelf-hostende IR uitvoeren in een ander account om de volgende redenen:
- Het service account wordt niet toegestaan door het bedrijfs beleid.
- Sommige verificatie is vereist.

Nadat u het service account in het deel venster service hebt gewijzigd, kunt u zien dat de Integration Runtime niet meer werkt.

![IR-registratie fout](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Oorzaak

Er zijn veel resources die alleen aan het service account worden verleend. Wanneer het service account wordt gewijzigd in een ander account, blijft de machtiging van alle afhankelijke resources hetzelfde.

#### <a name="resolution"></a>Oplossing

Ga naar het gebeurtenis logboek van Integration Runtime om de fout te controleren.

![IR-gebeurtenis logboek](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Als de fout wordt weer gegeven zoals boven *UnauthorizedAccessException*, volgt u de onderstaande instructies:


1. Controleer *DIAHostService* Logon service-account in het venster van de Windows-service.

    ![Aanmeldings service account](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Controleer of het aanmeldings service account de machtiging R/W heeft via de map: *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway*.

    - Als het account voor de service aanmelding niet is gewijzigd, moet dit standaard de machtiging R/W hebben.

        ![Service machtiging](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Als u het account voor aanmelding bij de service hebt gewijzigd, volgt u de onderstaande stappen om het probleem te verhelpen:
        1. Verwijder de huidige zelf-hostende IR.
        1. Installeer de zelf-hostende IR-bits.
        1. Volg de onderstaande instructies om het service account te wijzigen: 
            1. Ga naar de installatiemap van de zelf-hostende IR. Ga naar de map: *micro soft Integration Runtime\4.0\Shared*.
            1. Start een opdracht regel met verhoogde bevoegdheden. Vervang *\<user>* en *\<password>* door uw eigen gebruikers naam en wacht woord en voer de volgende opdracht uit:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Als u wilt overschakelen naar de LocalSystem-account, moet u ervoor zorgen dat u de juiste indeling voor dit account gebruikt. Hieronder ziet u een voor beeld van de juiste indeling:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                Gebruik **niet** de indeling zoals hieronder wordt weer gegeven:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. Omdat het lokale systeem een hogere bevoegdheid heeft dan de beheerder, kunt u dit ook rechtstreeks wijzigen in ' Services '.
            1. U kunt de lokale/domein-gebruiker voor het aanmeldings account van de IR-service gebruiken.            
        1. Registreer de Integration Runtime.

Als de fout er als volgt uitziet: de *service ' Integration runtime service ' (DIAHostService) is niet gestart. Controleer of u voldoende rechten hebt om systeem services te starten*. Volg de onderstaande instructies:

1. Controleer *DIAHostService* Logon service-account in het venster van de Windows-service.
   
    ![Aanmeldings service account](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Controleer of het aanmeldings service account de machtiging **Aanmelden als service** heeft om Windows-service te starten:

    ![Aanmelden als service](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Meer informatie

Als er in uw geval geen van beide twee patronen in de oplossing van toepassing zijn, probeert u de onderstaande Windows-gebeurtenis logboeken te verzamelen: 
- Logboeken toepassingen en services-> Integration Runtime
- Windows-logboeken-> toepassing

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Kan de registratie knop niet vinden om een zelf-hostende IR te registreren    

#### <a name="symptoms"></a>Symptomen

De **registratie** knop is niet gevonden in de Configuration Manager gebruikers interface bij het registreren van een zelf-hostende IR.

![Geen registratie knop](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Oorzaak

Sinds de release van de *Integration Runtime 3,0* is de knop **registreren** op een bestaand Integration runtime knooppunt verwijderd om een schone en veiligere omgeving mogelijk te maken. Als er een knooppunt is geregistreerd voor een exemplaar van Integration Runtime (of dit nu online is of niet), moet u het vorige knoop punt verwijderen en vervolgens het knooppunt installeren en registreren om het opnieuw te registreren bij een ander exemplaar van Integration Runtime.

#### <a name="resolution"></a>Oplossing

1. Ga naar het configuratie scherm om de bestaande Integration Runtime te verwijderen.

    > [!IMPORTANT] 
    > Selecteer in het onderstaande proces de optie Ja. Bewaar geen gegevens tijdens het verwijderen van het proces.

    ![Gegevens verwijderen](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Als u het MSI-installatie programma voor Integration runtime niet hebt, gaat u naar het [Download centrum](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) om de nieuwste Integration runtime te downloaden.
1. Installeer het MSI-bestand en registreer de Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Kan de zelf-hostende IR niet registreren vanwege localhost    

#### <a name="symptoms"></a>Symptomen

Kan de zelf-hostende IR niet registreren op een nieuwe machine wanneer get_LoopbackIpOrName.

**Fout opsporing:** Er is een runtime fout opgetreden.
De type-initialisatie functie voor micro soft. DataTransfer. DIAgentHost. DataSourceCache heeft een uitzonde ring veroorzaakt.
Er is een onherstelbare fout opgetreden tijdens het zoeken in de data base.
 
**Uitzonderings Details:** System. TypeInitializationException: de type initialisatie functie voor micro soft. DataTransfer. DIAgentHost. DataSourceCache heeft een uitzonde ring veroorzaakt. ---> System .net. sockets. SocketException: er is een niet-herstel bare fout opgetreden tijdens het zoeken in de Data Base op het systeem .net. DNS. GetAddrInfo (String name).

#### <a name="cause"></a>Oorzaak

Het probleem treedt meestal op bij het oplossen van localhost.

#### <a name="resolution"></a>Oplossing

Gebruik localhost 127.0.0.1 om het bestand te hosten en los het probleem op.


### <a name="self-hosted-setup-failed"></a>Zelf-hostende installatie mislukt    

#### <a name="symptoms"></a>Symptomen

Kan geen bestaande IR verwijderen of een nieuwe IR installeren of een bestaande IR upgraden naar een nieuwe IR.

#### <a name="cause"></a>Oorzaak

De installatie is afhankelijk van de Windows Installer-service. Er zijn varianten die een installatie probleem kunnen veroorzaken:
- Onvoldoende schijf ruimte
- Geen machtigingen
- De NT-service is om een of andere reden vergrendeld
- Het CPU-gebruik is te hoog
- MSI-bestand wordt gehost op een trage netwerk locatie
- Sommige systeem bestanden of registers zijn onbedoeld gerakend


### <a name="ir-service-account-failed-to-fetch-certificate-access"></a>Het IR-service account kan geen certificaat toegang ophalen

#### <a name="symptoms"></a>Symptomen

Wanneer u zelf-hostende IR installeert via de Microsoft Integration Runtime Configuration Manager, wordt een certificaat met een vertrouwde certificerings instantie gegenereerd. Het certificaat kan niet worden toegepast om de communicatie tussen twee knoop punten te versleutelen. 

De fout informatie ziet er als volgt uit: 

`Failed to change Intranet communication encryption mode: Failed to grant Integration Runtime service account the access of to the certificate 'XXXXXXXXXX'. Error code 103`

![Kan geen certificaat toegang verlenen voor het IR-service account](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Oorzaak

Het certificaat gebruikt de SLEUTELARCHIEFPROVIDER (Key Storage Provider), die nog niet wordt ondersteund. SHIR ondersteunt tot nu toe alleen CSP-certificaten (Cryptographic Service Provider).

#### <a name="resolution"></a>Oplossing

CSP-certificaat wordt aanbevolen voor deze aanvraag.

**Oplossing 1:** Gebruik de onderstaande opdracht om het certificaat te importeren:

```
Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx 
```

![Certutil gebruiken](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Oplossing 2:** Conversie van certificaten:

openssl pkcs12/pfx-profiel-in .\xxxx.pfx-out. \ xxxx_new. pem-wachtwoord doorgifte:*\<EnterPassword>*

openssl pkcs12/pfx-Profiel-export-in. \ xxxx_new. pem-out xxxx_new. pfx

Voor en na de conversie:

![Vóór wijziging van certificaat](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Nadat het certificaat is gewijzigd](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Zelf-hostende Integration Runtime versie 5. x
Voor de upgrade naar versie 5. x van Azure Data Factory zelf-hostende Integration runtime vereisen we **.NET Framework runtime 4.7.2** of hoger. De download pagina bevat koppelingen naar de nieuwste versie van 4. x en de nieuwste twee 5. x-versies. 


Voor klanten van ADF v2:
- Als automatische updates is ingeschakeld en u uw .NET Framework-runtime al hebt bijgewerkt naar 4.7.2 of hoger, wordt de zelf-hostende Integration runtime automatisch bijgewerkt naar de nieuwste versie van 5. x.
- Als automatische updates is ingeschakeld en u uw .NET Framework-runtime niet hebt bijgewerkt naar 4.7.2 of hoger, wordt de zelf-hostende Integration runtime niet automatisch bijgewerkt naar de nieuwste versie van 5. x. De zelf-hostende Integration runtime blijft aanwezig in de huidige 4. x-versie. U kunt een waarschuwing voor de upgrade van .NET Framework runtime bekijken in de portal en zelf-hostende Integration runtime-client.
- Als automatische updates is uitgeschakeld en u uw .NET Framework-runtime al hebt bijgewerkt naar 4.7.2 of hoger, kunt u de nieuwste 5. x hand matig downloaden en installeren op uw computer.
- Als automatische updates is uitgeschakeld en u uw .NET Framework-runtime niet hebt bijgewerkt naar 4.7.2 of hoger. Wanneer u probeert SHIR 5. x hand matig te installeren en de sleutel te registreren, moet u eerst uw .NET Framework-runtime bijwerken.


Voor klanten van ADF v1:
- Zelf-hostende Integration runtime 5. X biedt geen ondersteuning voor ADF v1.
- Voor de zelf-hostende Integration runtime wordt automatisch een upgrade uitgevoerd naar de nieuwste versie van 4. x. En de laatste versie van 4. x zal niet verlopen. 
- Als u de zelf-hostende Integration runtime 5. x hand matig wilt installeren en de sleutel wilt registreren, geeft u aan dat zelf-hostende Integration runtime 5. x geen ondersteuning biedt voor v1.


## <a name="self-hosted-ir-connectivity-issues"></a>Zelf-hostende IR-verbindings problemen

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Zelf-hostende Integration runtime kan geen verbinding maken met de Cloud service

#### <a name="symptoms"></a>Symptomen

![Probleem met de zelf-hosted IR-verbinding](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Oorzaak 

De zelf-hostende Integration runtime kan geen verbinding maken met de Data Factory Service (back-end). Dit probleem wordt meestal veroorzaakt door netwerk instellingen in de firewall.

#### <a name="resolution"></a>Oplossing

1. Controleer of de service Integration runtime actief is.
    
   ![Zelf-hostende status van de IR-service](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Als de service wordt uitgevoerd, gaat u naar stap 3.

1. Als er geen proxy is geconfigureerd voor de zelf-hostende Integration runtime (dit is de standaard instelling), voert u de volgende Power shell-opdracht uit op de computer waarop de zelf-hostende Integration runtime is geïnstalleerd:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > De service-URL kan variëren, afhankelijk van de locatie van uw Data Factory. U kunt de service-URL vinden onder **ADF UI**  >  **Connections**  >  **Integration Runtimes**  >  **bewerken zelf-hostende IR**-  >  **knoop punten**  >  **bekijken service-url's**.
            
    Hier volgt de verwachte reactie:
            
    ![Power shell-opdracht antwoord](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Als u het verwachte antwoord niet ontvangt, gebruikt u een van de volgende methoden, afhankelijk van uw situatie:
            
    * Als er een bericht wordt weer gegeven dat de externe naam niet kan worden omgezet, is er een probleem met de Domain Name System (DNS). Neem contact op met uw netwerk team om dit probleem op te lossen.
    * Als er een bericht wordt weer gegeven dat het SSL/TLS-certificaat niet wordt vertrouwd, controleert u of het certificaat voor https://wu2.frontend.clouddatahub.net/ wordt vertrouwd op de computer en installeert u vervolgens het open bare certificaat met behulp van certificaat beheer. Deze actie moet het probleem verminderen.
    * Ga naar **Windows** logboeken  >  toepassingen en services van Windows **(Logboeken)**  >  **Applications and Services Logs**  >  **Integration runtime** en controleer op fouten die worden veroorzaakt door DNS, een firewall regel of instellingen van het bedrijfs netwerk. (Als u een dergelijke fout vindt, sluit u de verbinding af.) Omdat elk bedrijf aangepaste netwerk instellingen heeft, neemt u contact op met uw netwerk team om deze problemen op te lossen.

1. Als "proxy" is geconfigureerd op de zelf-hostende Integration runtime, controleert u of de proxy server toegang heeft tot het service-eind punt. Zie [Power shell, webaanvragen en proxy's](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)voor een voor beeld van een opdracht.    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Hier volgt de verwachte reactie:
            
![Power shell-opdracht antwoord 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Overwegingen voor de proxy:
> *    Controleer of de proxy server moet worden geplaatst in de lijst met veilige geadresseerden. Als dit het geval is, moet u ervoor zorgen dat [deze domeinen](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) zich in de lijst met veilige ontvangers bevinden.
> *    Controleer of het TLS/SSL-certificaat "wu2.frontend.clouddatahub.net/" wordt vertrouwd op de proxy server.
> *    Als u Active Directory verificatie gebruikt op de proxy, wijzigt u het service account in het gebruikers account dat toegang heeft tot de proxy als ' Integration Runtime-service.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Fout bericht: zelf-hostende Integration runtime-knoop punt/logische SHIR is in inactief/' wordt uitgevoerd (beperkt) '

#### <a name="cause"></a>Oorzaak 

Het zelf-hostende geïntegreerde runtime knooppunt heeft mogelijk een **inactieve** status, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Inactief Self-Hosted IR-knoop punt](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Dit gedrag treedt op wanneer knoop punten niet met elkaar kunnen communiceren.

#### <a name="resolution"></a>Oplossing

1. Meld u aan bij de door het knoop punt gehoste VM. Open in de **Logboeken toepassingen en services**  >  **Integration runtime** logboeken en filter alle fouten Logboeken.

1. Controleer of een fouten logboek de volgende fout bevat: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Als u deze fout ziet, voert u het volgende uit op de opdracht regel: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Als het volgende fout bericht wordt weer gegeven, neemt u contact op met uw IT-afdeling voor hulp bij het oplossen van dit probleem. Wanneer u met succes kunt Telnet, neemt u contact op met Microsoft Ondersteuning als u nog steeds problemen ondervindt met de status van het integrale runtime-knoop punt.
        
   ![Fout met opdracht regel](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Controleer of het fouten logboek het volgende bevat:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Probeer een of beide van de volgende methoden om het probleem op te lossen:
    - Plaats alle knoop punten in hetzelfde domein.
    - Voeg het IP-adres toe aan de host toewijzing in alle host-bestanden van de gehoste VM.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Connectiviteits probleem tussen zelf-hostende IR en Data Factory of zelf-hostende IR en gegevens bron/Sink

Als u het probleem met de netwerk verbinding wilt oplossen, moet u weten hoe u de netwerk tracering kunt verzamelen, hoe u deze kunt gebruiken en hoe u [de netmon-tracering moet analyseren](#how-to-analyze-netmon-trace) voordat u de hulpprogram Ma's voor netmon in real cases van zelf-hostende IR toepast.

#### <a name="symptoms"></a>Symptomen

Soms kunnen de verbindings problemen, zoals hieronder, worden opgelost tussen een zelf-hostende IR en Data Factory: 

![HTTP-aanvraag is mislukt](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Of de ene tussen zelf-hostende IR en gegevens bron/sink, zal de volgende fouten tegen komen:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Oplossing:

Raadpleeg de volgende instructies als u meer problemen ondervindt:

Neem de netmon-tracering en analyseer verder.
- Ten eerste kunt u het filter zo instellen dat er een opnieuw instellen wordt weer geven van de server naar de client zijde. In het onderstaande voor beeld ziet u dat de server is Data Factory server.

    ![Data Factory-server](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Wanneer u het pakket opnieuw instellen krijgt, kunt u het gesprek vinden door TCP te volgen.

    ![Gesprek zoeken](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Daarna kunt u de conversie tussen client en Data Factory server hieronder ophalen door het filter te verwijderen.

    ![Gesprek ontvangen](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Op basis van de verzamelde netmon-tracering kunnen we zien dat het TTL-totaal (TimeToLive) 64 is. Volgens de **standaard waarden voor TTL en hops** die in [dit artikel](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) worden genoemd (zoals hieronder geëxtraheerd), kunnen we zien dat het het Linux-systeem is dat het pakket opnieuw instelt, waardoor de verbinding wordt verbroken.

    De waarden voor de standaard-TTL en-hop variëren tussen de verschillende besturings systemen, en dit zijn de standaard instellingen voor enkele:
    - Linux kernel 2,4 (circa 2001): 255 voor TCP, UDP en ICMP
    - Linux kernel 4,10 (2015): 64 voor TCP, UDP en ICMP
    - Windows XP (2001): 128 voor TCP, UDP en ICMP
    - Windows 10 (2015): 128 voor TCP, UDP en ICMP
    - Windows Server 2008:128 voor TCP, UDP en ICMP
    - Windows Server 2019 (2018): 128 voor TCP, UDP en ICMP
    - macOS (2001): 64 voor TCP, UDP en ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Het wordt echter weer gegeven als 61 in plaats van 64 in het bovenstaande voor beeld, omdat wanneer het netwerk pakket naar de bestemming bereikt, de verschillende hops moeten door lopen, zoals routers/netwerk apparaten. Het aantal routers/netwerk apparaten wordt in mindering gebracht in de laatste TTL.
    In dit geval kunnen we zien dat opnieuw instellen kan worden verzonden vanuit Linux-systeem met TTL 64.

- We moeten de vierde hop van de zelf-hostende IR controleren om te bevestigen dat het apparaat opnieuw wordt ingesteld.
 
    *Netwerk pakket van Linux-systeem A met TTL 64-> B TTL 64 min 1 = 63-> C TTL 63 min 1 = 62-> TTL 62 min 1 = 61 zelf-hostende IR*

- In ideale situatie is de TTL 128, wat betekent dat Windows System de Data Factory uitvoert. Zoals u in het onderstaande voor beeld ziet, *128 – 107 = 21 hops*, wat betekent dat er 21 hops voor het pakket zijn verzonden van Data Factory naar een zelf-hostende IR tijdens de TCP 3-handshake.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Daarom moet u het netwerk team om te controleren wat de vierde hop is van zelf-hostende IR. Als dit de firewall is als Linux-systeem, controleert u de logboeken op de reden dat het apparaat het pakket opnieuw instelt na de TCP 3-handshake. Als u echter niet zeker weet waar u moet onderzoeken, probeert u de netmon-tracering van de zelf-hostende IR en firewall samen te halen tijdens de problematische tijd om te achterhalen op welk apparaat dit pakket kan worden teruggezet en wordt de verbinding verbroken. In dit geval moet u ook uw netwerk team laten door lopen.

### <a name="how-to-analyze-netmon-trace"></a>Netmon-tracering analyseren

> [!NOTE] 
> De onderstaande instructie is van toepassing op netmon-tracering. Omdat netmon-tracering momenteel niet wordt ondersteund, kunt u wireshark gebruiken.

Wanneer u probeert te Telnet **8.8.8.8 888** met netmon-tracering, moet u de onderstaande tracering zien:

![netmon-tracering 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon-tracering 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Dit betekent dat u geen TCP-verbinding met de **8.8.8.8** -server kunt maken op basis van de poort **888**, zodat u daar twee extra pakketten van **SynReTransmit** ziet. Omdat de **Self-HOST2** van de bron geen verbinding kan maken met **8.8.8.8** bij het eerste pakket, blijft deze verbinding maken.

> [!TIP]
> - U kunt klikken op standaard filter voor **Load filter**  ->  **Standard Filter**  ->  **adressen**  ->  **IPv4-adressen**.
> - Voer **IPv4. Address = = 8.8.8.8** in als filter en klik op **Toep assen**. Daarna ziet u alleen de communicatie van de lokale computer naar de doel- **8.8.8.8**.

![filter adressen 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filter adressen 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

In het onderstaande voor beeld ziet u hoe een geschikt scenario eruit zou zien. 

- Als Telnet **8.8.8.8 53** zonder problemen werkt, kunt u de TCP 3-Handshake bekijken en vervolgens de sessie met TCP 4-Handshake volt ooien.

    ![goed scenario-voor beeld 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![goed scenario-voor beeld 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Op basis van de bovenstaande TCP 3-Handshake kunt u de onderstaande werk stroom zien:

    ![TCP 3 Handshake-werk stroom](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- De TCP 4-Handshake voor het volt ooien van de sessie en de werk stroom worden als volgt weer gegeven:

    ![TCP 4-Handshake](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4-Handshake-werk stroom](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>E-mail ontvangen om de netwerk configuratie bij te werken om communicatie met nieuwe IP-adressen toe te staan

#### <a name="email-notification-from-microsoft"></a>E-mail melding van micro soft

U kunt de volgende e-mail melding ontvangen, waarmee u wordt geadviseerd om de netwerk configuratie bij te werken om communicatie met nieuwe IP-adressen voor Azure Data Factory op 8 november 2020 mogelijk te maken:

   ![E-mailmelding](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>Vaststellen of dit van invloed is op deze melding

Deze melding is van invloed op de volgende scenario's:
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>Scenario 1: uitgaande communicatie van zelf-hostende Integration Runtime op locatie achter de bedrijfs firewall uitgevoerd
Bepalen of u de gevolgen hebt:
- U hebt geen invloed op de manier waarop u firewall regels definieert op basis van FQDN-namen met behulp van de methode die in dit document wordt beschreven: [firewall configuratie en lijst met toegestane instellingen voor IP-adres](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).
- U hebt echter wel last van dit probleem als u de acceptatie lijst expliciet inschakelt voor uitgaande Ip's op uw bedrijfs firewall.

Te ondernemen actie als dit van invloed is: Waarschuw uw netwerk infrastructuur team om uw netwerk configuratie bij te werken om de nieuwste Data Factory IP-adressen te gebruiken voor 8 november 2020.  Als u de meest recente IP-adressen wilt downloaden, gaat u naar de [Download koppeling service Tags IP-adres bereik](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>Scenario 2: uitgaande communicatie van zelf-hostende Integration Runtime die wordt uitgevoerd op een Azure-VM binnen een door de klant beheerd Azure Virtual Network
Bepalen of u de gevolgen hebt:
- Controleer of er regels voor uitgaande NSG aanwezig zijn in uw particuliere netwerk dat zelf-hostende Integration Runtime bevat. Als er geen uitgaande beperkingen zijn, heeft dit geen invloed.
- Als u uitgaande regel beperkingen hebt, controleert u of u een service label gebruikt of niet. Als u service tags gebruikt, hoeft u niets te wijzigen of toe te voegen omdat de nieuwe IP-bereiken onder bestaande service tags vallen. 
 ![Doel controle](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- U hebt echter last van dit probleem als u expliciet de acceptatie lijst voor uitgaande IP-adressen in uw NSG-instellingen in het virtuele Azure-netwerk inschakelt.

Te ondernemen actie als dit van invloed is: Waarschuw uw netwerk infrastructuur team om de NSG-regels in de configuratie van uw virtuele Azure-netwerk bij te werken om de nieuwste Data Factory IP-adressen te gebruiken voor 8 november 2020.  Als u de meest recente IP-adressen wilt downloaden, gaat u naar de [Download koppeling service Tags IP-adres bereik](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>Scenario 3: uitgaande communicatie van SSIS-Integration Runtime in door klanten beheerd virtueel netwerk van Azure
- Controleer of u uitgaande NSG-regels hebt in uw particuliere netwerk dat SSIS Integration Runtime bevat. Als er geen uitgaande beperkingen zijn, heeft dit geen invloed.
- Als u uitgaande regel beperkingen hebt, controleert u of u een service label gebruikt of niet. Als u service tags gebruikt, hoeft u niets te wijzigen of toe te voegen omdat de nieuwe IP-bereiken onder bestaande service tags vallen.
- U hebt echter wel last van het inschakelen van de acceptatie lijst voor uitgaand IP-adres in uw NSG-instellingen in het virtuele Azure-netwerk.

Te ondernemen actie als dit van invloed is: Waarschuw uw netwerk infrastructuur team om de NSG-regels in de configuratie van uw virtuele Azure-netwerk bij te werken om de nieuwste Data Factory IP-adressen te gebruiken voor 8 november 2020.  Als u de meest recente IP-adressen wilt downloaden, gaat u naar de [Download koppeling service Tags IP-adres bereik](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Kan geen vertrouwens relatie tot stand brengen voor het beveiligde kanaal SSLTLS 

#### <a name="symptoms"></a>Symptomen

De zelf-hostende IR kan geen verbinding maken met de ADF-service.

Als u SHIR-gebeurtenis logboek of client meldings Logboeken in CustomLogEvent-tabel controleert, wordt het volgende fout bericht weer gegeven:

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

Controleren van het server certificaat van de ADF-service:

De eenvoudigste manier is om de URL van de ADF-service in de browser te openen, bijvoorbeeld open https://eu.frontend.clouddatahub.net/ op de computer waarop SHIR is geïnstalleerd, en vervolgens de gegevens van het server certificaat te bekijken:

  ![Het server certificaat van de ADF-service controleren](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Het pad van het server certificaat controleren](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Oorzaak

Twee mogelijke redenen voor dit probleem:

- De basis-CA van het ADF-service Server certificaat wordt niet vertrouwd op de computer waarop de SHIR is geïnstalleerd. 
- U gebruikt proxy in uw-omgeving en het server certificaat van de ADF-service wordt vervangen door de proxy, terwijl het vervangen server certificaat niet wordt vertrouwd door de computer waarop de SHIR is geïnstalleerd.

#### <a name="resolution"></a>Oplossing

- Zorg er daarom voor dat het ADF-server certificaat en de bijbehorende certificaat keten worden vertrouwd door de computer waarop de SHIR is geïnstalleerd.
- Voor de reden 2 vertrouwt u de vervangen basis-CA op de SHIR-machine of configureert u de proxy niet om het ADF server-certificaat te vervangen.

Raadpleeg [dit artikel](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate) voor meer informatie over het vertrouwen van een certificaat in Windows.

#### <a name="additional-info"></a>Aanvullende informatie
We implementeren een nieuw SSL-certificaat dat is ondertekend vanuit DigiCert. Controleer of de globale hoofdmap G2 van DigiCert zich in de vertrouwde basis certificerings instantie bevindt.

  ![DigiCert Global Root G2](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Als dat niet het geval is, kunt u dit [hier](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt )downloaden. 


## <a name="self-hosted-ir-sharing"></a>Zelf-hostende IR-deling

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Zelf-hostende IR van een andere Tenant delen wordt niet ondersteund 

#### <a name="symptoms"></a>Symptomen

U kunt andere gegevens fabrieken (op verschillende tenants) zien tijdens het delen van de zelf-hostende IR vanuit Azure Data Factory gebruikers interface, maar u kunt de zelf-hostende IR niet delen voor gegevens fabrieken die zich op verschillende tenants bevinden.

#### <a name="cause"></a>Oorzaak

De zelf-hostende IR kan geen gedeelde cross-tenants zijn.



## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-vragenpagina](/answers/topics/azure-data-factory.html)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatie gids gegevens stromen toewijzen](concepts-data-flow-performance.md)