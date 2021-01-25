---
title: Problemen met een zelf-hostende Integration runtime in Azure Data Factory oplossen
description: Meer informatie over het oplossen van problemen met zelf-hostende Integration runtime in Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: e81a12f4c5d817670fe1f7968184bcc97e78a53c
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757675"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Problemen met zelf-hostende Integration runtime oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor zelf-hostende Integration runtime (IR) in Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Zelf-hostende IR-logboeken van Azure Data Factory verzamelen

Azure Data Factory ondersteunt het weer geven en uploaden van fouten logboeken voor mislukte activiteiten die worden uitgevoerd op een zelf-hostende IR of een gedeelde IR. Als u de ID van het fouten rapport wilt ophalen, volgt u de instructies hier en voert u de rapport-ID in om te zoeken naar gerelateerde bekende problemen.

1. Selecteer in Data Factory **pijplijn uitvoeringen**.

1. Selecteer onder **uitvoering van activiteit**, in de kolom **fout** , de gemarkeerde knop om de activiteiten logboeken weer te geven, zoals wordt weer gegeven in de volgende scherm afbeelding:

    ![Scherm afbeelding van de sectie ' uitgevoerde activiteiten ' in het deel venster alle pipeline-uitvoeringen.](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    De activiteiten logboeken worden weer gegeven voor het uitvoeren van mislukte activiteiten.

    ![Scherm afbeelding van de activiteiten logboeken voor de mislukte activiteit.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. Selecteer **Logboeken verzenden** voor meer informatie.
 
   Het **deel van de zelf-hostende Integration runtime-Logboeken (IR) met micro soft** venster wordt geopend.

    ![Scherm afbeelding van het venster ' zelf-hostende Integration runtime-Logboeken (IR) met micro soft ' delen.](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Selecteer de logboeken die u wilt verzenden. 
    * Voor een *zelf-hostende IR* kunt u Logboeken uploaden die betrekking hebben op de mislukte activiteit of alle logboeken op het zelf-hostende IR-knoop punt. 
    * Voor een *gedeelde IR* kunt u alleen logboeken uploaden die betrekking hebben op de mislukte activiteit.

1. Wanneer de logboeken zijn geüpload, moet u een record van de rapport-ID bijhouden voor later gebruik als u meer hulp nodig hebt om het probleem op te lossen.

    ![Scherm opname van de weer gegeven rapport-ID in het voortgangs venster voor het uploaden van de IR-Logboeken.](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Logboek weer geven en upload aanvragen worden uitgevoerd op alle online zelf-hostende IR-exemplaren. Als er logboeken ontbreken, moet u ervoor zorgen dat alle zelf-hostende IR-instanties online zijn. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Algemeen probleem of algemene fout met zelf-hostende IR

### <a name="out-of-memory-issue"></a>Probleem met onvoldoende geheugen

#### <a name="symptoms"></a>Symptomen

Er treedt een OutOfMemoryException (OOM)-fout op wanneer u probeert een opzoek activiteit uit te voeren met een gekoppelde IR of een zelf-hostende IR.

#### <a name="cause"></a>Oorzaak

Een nieuwe activiteit kan een OOM-fout veroorzaken als de IR-machine even veel geheugen gebruik ondervindt. Het probleem kan worden veroorzaakt door grote hoeveel heden gelijktijdige activiteiten en de fout is standaard.

#### <a name="resolution"></a>Oplossing

Controleer het resource gebruik en de uitvoering van gelijktijdige activiteiten op het IR-knoop punt. Pas de interne en activerings tijd van de uitvoering van de activiteit aan om te veel uitvoering op één IR-knoop punt te voor komen.

### <a name="concurrent-jobs-limit-issue"></a>Probleem met limiet voor gelijktijdige taken

#### <a name="symptoms"></a>Symptomen

Wanneer u probeert de limiet voor gelijktijdige taken te verhogen van de Azure Data Factory-interface, loopt het proces vast bij het *bijwerken* van de status.

Voorbeeld scenario: de waarde voor het maximum aantal gelijktijdige taken is momenteel ingesteld op 24 en u wilt het aantal verhogen zodat uw taken sneller kunnen worden uitgevoerd. De minimum waarde die u kunt invoeren is 3 en de maximum waarde is 32. Verhoog de waarde tussen 24 en 32 en selecteer vervolgens de knop **bijwerken** . Het proces krijgt de status *bijgewerkt* , zoals wordt weer gegeven in de volgende scherm afbeelding. U vernieuwt de pagina en de waarde wordt nog steeds weer gegeven als 24. Het is niet bijgewerkt naar 32 zoals verwacht.

![Scherm opname van het deel venster knoop punten van de Integration runtime, waarbij het proces wordt weer gegeven dat is vastgelopen in de status bijwerken.](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Oorzaak

De limiet voor het aantal gelijktijdige taken is afhankelijk van de Logic core en het geheugen van de computer. Probeer de waarde omlaag aan te passen aan een waarde zoals 24 en bekijk vervolgens het resultaat.

> [!TIP] 
> - Zie [vier manieren om het aantal kern geheugens in uw CPU te vinden in Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)voor meer informatie over het aantal Logic-kernen en het bepalen van het Logic core-aantal van uw machine.
> - Ga naar de [logaritmische reken machine](https://www.rapidtables.com/calc/math/Log_Calculator.html)voor meer informatie over het berekenen van Math. log.


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>Zelf-hostend SSL-certificaat voor hoge Beschik baarheid (HA) met IR

#### <a name="symptoms"></a>Symptomen

Het zelf-hostende IR-werk knooppunt heeft de volgende fout gerapporteerd:

Kan de gedeelde statussen van het primaire knoop punt net. TCP://abc.cloud.corp.Microsoft.com: 8060/ExternalService. SVC/niet ophalen. Activiteits-ID: XXXXX het X. 509-certificaat CN = ABC. Cloud. Corp. micro soft. com, OE = test, O = micro soft keten opbouwen is mislukt. Het certificaat dat is gebruikt, heeft een vertrouwens keten die niet kan worden geverifieerd. Vervang het certificaat of wijzig de certificateValidationMode. De intrekkings functie kan het intrekken niet controleren omdat de intrekkings server offline was. "

#### <a name="cause"></a>Oorzaak

Wanneer u cases verwerkt die zijn gerelateerd aan een SSL/TLS-Handshake, kunnen er problemen optreden met betrekking tot de verificatie van de certificaat keten. 

#### <a name="resolution"></a>Oplossing

- Hier volgt een snelle en intuïtieve manier om een fout op te lossen bij het opbouwen van een X. 509-certificaat keten:
 
    1. Exporteer het certificaat dat moet worden geverifieerd. U kunt dit als volgt doen:
    
       a. Selecteer in Windows **Start**, begin met het typen van **certificaten** en selecteer vervolgens **computer certificaten beheren**.
       
       b. Zoek in Verkenner in het linkerdeel venster naar het certificaat dat u wilt controleren, klik er met de rechter muisknop op en selecteer vervolgens **alle taken**  >  **exporteren**.
    
        ![Scherm opname van het besturings element "alle taken" > "exporteren" voor een certificaat in het deel venster computer certificaten beheren.](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Kopieer het geëxporteerde certificaat naar de client computer. 
    3. Voer de volgende opdracht uit in een opdracht prompt venster aan de client zijde. Zorg ervoor dat u *\<certificate path>* en vervangt door *\<output txt file path>* de daad werkelijke paden.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Bijvoorbeeld:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Controleer op fouten in het uitvoer-TXT-bestand. U vindt de samen vatting van de fout aan het einde van het TXT-bestand.

        Bijvoorbeeld: 

        ![Scherm afbeelding van een fout samenvatting aan het einde van het TXT-bestand.](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Als er aan het einde van het logboek bestand geen fout wordt weer gegeven, zoals in de volgende scherm afbeelding, kunt u overwegen dat de certificaat keten is gemaakt op de client computer.
        
        ![Scherm opname van een logboek bestand waarin geen fouten worden weer gegeven.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Als een AIA (toegang tot CA-gegevens), CDP (CRL-distributie punt) of OCSP (Online Certificate Status Protocol)-bestands extensie is geconfigureerd in het certificaat bestand, kunt u het op een meer intuïtieve manier controleren:
 
    1. U kunt deze informatie ophalen door de certificaat gegevens te controleren, zoals wordt weer gegeven in de volgende scherm afbeelding:
    
        ![Scherm opname van certificaat gegevens.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Voer de volgende opdracht uit. Zorg ervoor dat u vervangt door *\<certificate path>* het daad werkelijke pad van het certificaat.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        Het hulp programma voor het ophalen van URL'S wordt geopend. 
        
    1. Selecteer **ophalen** om certificaten met Aia-, CDP-en OCSP-bestandsnaam extensies te controleren.

        ![Scherm afbeelding van het hulp programma voor het ophalen van URL'S en de knop ophalen.](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        U hebt de certificaat keten gemaakt als de certificaat status van AIA is *geverifieerd* en de certificaat status van CDP of OCSP is *geverifieerd*.

        Als er een fout optreedt wanneer u AIA of CDP probeert op te halen, moet u samen werken met uw netwerk team om de client computer gereed te maken voor verbinding met de doel-URL. Het is voldoende als u het HTTP-pad of het LDAP-pad (Lightweight Directory Access Protocol) kunt controleren.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Zelf-hostende IR kan bestand of assembly niet laden

#### <a name="symptoms"></a>Symptomen

Het volgende fout bericht wordt weer gegeven:

' Kan bestand of Assembly ' XXXXXXXXXXXXXXXX, version = 4.0.2.0, Culture = neutrale, PublicKeyToken = XXXXXXXXX ' of een van de bijbehorende afhankelijkheden niet laden. Het systeem kan het opgegeven bestand niet vinden. Activiteits-ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "
 
Hier volgt een meer specifiek fout bericht: 

"Kan bestand of assembly System. ValueTuple, version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX of een van de bijbehorende afhankelijkheden niet laden. Het systeem kan het opgegeven bestand niet vinden. Activiteits-ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>Oorzaak

In Process Monitor kunt u het volgende resultaat bekijken:

[![Scherm afbeelding van de lijst paden in process monitor.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> In Process Monitor kunt u filters instellen, zoals wordt weer gegeven in de volgende scherm afbeelding.
>
> Het voor gaande fout bericht geeft aan dat het DLL-bestand System. ValueTuple zich niet bevindt in de map met de bijbehorende GAC ( *Global assembly cache* ), in de map *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* , of in de map *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* .
>
> Het proces laadt in principe het DLL-bestand eerst vanuit de map *GAC* , vervolgens uit de *gedeelde* map en ten slotte vanuit de map *Gateway* . Daarom kunt u het DLL-bestand laden vanuit elk pad dat nuttig is.

<br>

![Scherm afbeelding van de pagina monitor filter proces, waarin de filters voor de DLL worden weer gegeven.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Oplossing

U vindt het *System.ValueTuple.dll* -bestand in de map *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* . Om het probleem op te lossen, kopieert u het *System.ValueTuple.dll* bestand naar de map *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* .

U kunt dezelfde methode gebruiken om andere ontbrekende bestands-of assembly problemen op te lossen.

#### <a name="more-information-about-this-issue"></a>Meer informatie over dit probleem

De reden waarom u de *System.ValueTuple.dll* onder *%windir%\Microsoft.NET\assembly* en *%windir%\assembly* ziet, is dat dit een .net-gedrag is. 

In de volgende fout ziet u duidelijk dat de assembly *System. ValueTuple* ontbreekt. Dit probleem doet zich voor wanneer de toepassing probeert de *System.ValueTuple.dll* -assembly te controleren.
 
" \<LogProperties> \<ErrorInfo> [{" Code ": 0," bericht ":" het type initialisatie functie voor ' Npgsql. PoolManager ' heeft een uitzonde ring veroorzaakt. "," gebeurtenis code ": 0," categorie ": 5," gegevens ": {} ," MsgId ": null," ExceptionType ":" System. TypeInitializationException "," source ":" Npgsql "," StackTrace ":" "," InnerEventInfos ": [{" code ": 0," bericht ":" kan bestand of assembly System. ValueTuple, version = 4.0.2.0, Culture = neutral, PublicKeyToken = xxxxxxxxx of een van de afhankelijkheden ervan niet laden. Het systeem kan het opgegeven bestand niet vinden. "," Event type ": 0," Category ": 5," data ": {} ," MsgId":null,"ExceptionType":"System. io. FileNotFoundException "," source ":" Npgsql "," StackTrace ":" "," InnerEventInfos ": []}]}] \</ErrorInfo> \</LogProperties> "
 
Zie [Global assembly cache](https://docs.microsoft.com/dotnet/framework/app-domains/gac)(Engelstalig) voor meer informatie over GAC.


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>De verificatie sleutel voor de zelf-hostende Integration runtime ontbreekt

#### <a name="symptoms"></a>Symptomen

De zelf-hostende Integration runtime gaat plotseling offline zonder een verificatie sleutel en het gebeurtenis logboek geeft het volgende fout bericht weer: 

De verificatie sleutel is nog niet toegewezen

![Scherm afbeelding van het gebeurtenis venster voor Integration runtime dat aangeeft dat de verificatie sleutel nog niet is toegewezen.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Oorzaak

- Het zelf-hostende IR-knoop punt of de logische zelf-hostende IR in de Azure Portal is verwijderd.
- Er is een schone verwijdering uitgevoerd.

#### <a name="resolution"></a>Oplossing

Als geen van de voor gaande oorzaken van toepassing is, kunt u naar de map *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* kijken of het *configuratie* bestand is verwijderd. Als de app is verwijderd, volgt u de instructies in het NetWrix-artikel [detecteren wie een bestand heeft verwijderd van uw Windows-bestands servers](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Scherm opname van het detail venster van het gebeurtenis logboek voor het controleren van het configuratie bestand.](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Kan zelf-hostende IR niet gebruiken om twee on-premises gegevens opslag te bridgen

#### <a name="symptoms"></a>Symptomen

Nadat u een zelf-hostende IRs hebt gemaakt voor de bron-en doel gegevens opslag, wilt u de twee IRs koppelen om een Kopieer activiteit te volt ooien. Als de gegevens opslag in verschillende virtuele netwerken zijn geconfigureerd, of als het gateway mechanisme niet kan worden begrepen door de gegevens opslag, wordt een van de volgende fouten weer gegeven: 

* Het stuur programma van de bron is niet gevonden in de doel-IR
* "De bron is niet toegankelijk voor de doel-IR"
 
#### <a name="cause"></a>Oorzaak

De zelf-hostende IR is ontworpen als een centraal knoop punt van een Kopieer activiteit, niet een client agent die voor elke gegevens opslag moet worden geïnstalleerd.
 
In dit geval moet u de gekoppelde service voor elk gegevens archief met dezelfde IR maken en moet de IR toegang hebben tot beide gegevens opslag via het netwerk. Het maakt niet uit of de IR is geïnstalleerd op de bron gegevens opslag of in het doel gegevens archief of op een derde computer. Als twee gekoppelde services zijn gemaakt met een andere IRs, maar in dezelfde Kopieer activiteit worden gebruikt, wordt de doel-IR gebruikt en moet u de Stuur Programma's voor beide gegevens opslag op de doel-IR-computer installeren.

#### <a name="resolution"></a>Oplossing

Installeer Stuur Programma's voor de bron-en doel gegevens opslag in de doel-IR en zorg ervoor dat deze toegang heeft tot de bron gegevens opslag.
 
Als het verkeer niet via het netwerk kan worden door gegeven tussen twee gegevens opslag (bijvoorbeeld omdat deze zijn geconfigureerd in twee virtuele netwerken), is het mogelijk dat u niet in één activiteit kopieert, zelfs niet als de IR is geïnstalleerd. Als u het kopiëren in één activiteit niet kunt volt ooien, kunt u twee Kopieer activiteiten maken met twee IRs, elk in een ventilator: 
* Eén IR kopiëren van Data Store 1 naar Azure Blob Storage
* Kopieer een andere IR van Azure Blob Storage naar ddatastore 2. 

Deze oplossing kan de vereiste voor het gebruik van de IR simuleren om een brug te maken waarmee twee niet-verbonden gegevens opslag worden verbonden.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>Problemen met synchronisatie van referenties veroorzaken referentie verlies van HA

#### <a name="symptoms"></a>Symptomen

Als de gegevens bron referentie ' XXXXXXXXXX ' is verwijderd uit het huidige knoop punt voor Integration runtime met Payload, wordt het volgende fout bericht weer gegeven:

"Wanneer u de koppelings service op Azure Portal verwijdert of de taak de verkeerde Payload heeft, maakt u opnieuw een nieuwe koppelings service met uw referenties."

#### <a name="cause"></a>Oorzaak

Uw zelf-hostende IR is gebouwd in de modus HA met twee knoop punten, maar de knoop punten bevinden zich niet in de synchronisatie status van referenties. Dit betekent dat de referenties die zijn opgeslagen in het knoop punt dispatcher niet worden gesynchroniseerd met andere worker-knoop punten. Als een failover van het knoop punt dispatcher naar het worker-knoop punt wordt uitgevoerd en de referenties alleen voor komen in het vorige dispatcher-knoop punt, mislukt de taak wanneer u probeert toegang te krijgen tot referenties en wordt de voor gaande fout weer gegeven.

#### <a name="resolution"></a>Oplossing

De enige manier om dit probleem te voor komen, is om ervoor te zorgen dat de twee knoop punten de synchronisatie status van referenties hebben. Als ze niet zijn gesynchroniseerd, moet u de referenties voor de nieuwe dispatcher opnieuw opgeven.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>Kan het certificaat niet kiezen omdat de persoonlijke sleutel ontbreekt

#### <a name="symptoms"></a>Symptomen

* U hebt een PFX-bestand geïmporteerd in het certificaat archief.
* Wanneer u het certificaat via de UI van IR Configuration Manager hebt geselecteerd, hebt u het volgende fout bericht ontvangen:

   De versleutelings modus voor intranet communicatie kan niet worden gewijzigd. Waarschijnlijk heeft het certificaat \<*certificate name*> geen persoonlijke sleutel die kan worden gewisseld of kan het proces geen toegangs rechten voor de persoonlijke sleutel hebben. Zie de interne uitzonde ring voor details.

    ![Scherm opname van het deel venster Integration Runtime Configuration Manager instellingen, waarin het fout bericht ' persoonlijke sleutel ontbreekt ' wordt weer gegeven.](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Oorzaak

- Het gebruikers account heeft een laag privilege niveau en heeft geen toegang tot de persoonlijke sleutel.
- Het certificaat is gegenereerd als hand tekening, maar niet als sleutel uitwisseling.

#### <a name="resolution"></a>Oplossing

* Als u de gebruikers interface wilt uitvoeren, gebruikt u een account met de juiste bevoegdheden voor toegang tot de persoonlijke sleutel.  
* Importeer het certificaat door de volgende opdracht uit te voeren:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>Zelf-hostende IR-installatie

### <a name="integration-runtime-registration-error"></a>Fout bij de registratie van Integration runtime 

#### <a name="symptoms"></a>Symptomen

Het kan voor komen dat u een zelf-hostende IR wilt uitvoeren in een ander account om een van de volgende redenen:
- Het service account wordt niet toegestaan door het bedrijfs beleid.
- Sommige verificatie is vereist.

Nadat u het service account in het deel venster service hebt gewijzigd, is het mogelijk dat de Integration runtime niet meer werkt en het volgende fout bericht wordt weer gegeven:

' Tijdens de registratie van het knoop punt Integration Runtime (zelf-hostend) is een fout opgetreden. Kan geen verbinding maken met de host-service van Integration Runtime (zelf-hostend). "

![Scherm afbeelding van het venster Integration Runtime Configuration Manager met een IR-registratie fout.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Oorzaak

Veel resources worden alleen verleend aan het service account. Wanneer u het service account wijzigt in een ander account, blijven de machtigingen van alle afhankelijke resources ongewijzigd.

#### <a name="resolution"></a>Oplossing

Ga naar het gebeurtenis logboek van Integration runtime om de fout te controleren.

![Scherm opname van het IR-gebeurtenis logboek, waarin wordt aangegeven dat er een runtime-fout is opgetreden.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Als de fout in het gebeurtenis logboek ' UnauthorizedAccessException ' is, doet u het volgende:

    1. Controleer het *DIAHostService* -aanmeldings service account in het venster van de Windows-service.

        ![Scherm afbeelding van het eigenschappen venster van het aanmeldings service-account.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Controleer of het aanmeldings service account lees-en schrijf machtigingen heeft voor de map *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway* .

        - Als het account voor de service aanmelding niet is gewijzigd, moet dit standaard de machtiging lezen/schrijven hebben.

            ![Scherm opname van het deel venster Service machtigingen.](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Als u het account voor aanmelding bij de service hebt gewijzigd, kunt u het probleem als volgt oplossen:
 
            a. Voer een schone verwijdering uit van de huidige zelf-hostende IR.   
            b. Installeer de zelf-hostende IR-bits.  
            c. Wijzig het service account door de volgende handelingen uit te voeren:  

             i. Ga naar de zelf-hostende IR-installatiemap en schakel vervolgens over naar de map *micro soft Integration Runtime\4.0\Shared* .  
             ii. Open een opdracht prompt venster met verhoogde bevoegdheden. Vervang *\<user>* en *\<password>* door uw eigen gebruikers naam en wacht woord en voer de volgende opdracht uit:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. Als u wilt overschakelen naar het LocalSystem-account, moet u ervoor zorgen dat u de juiste indeling voor dit account gebruikt: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                Gebruik deze indeling *niet* : `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             4. Als het lokale systeem een hogere bevoegdheid heeft dan de beheerder, kunt u het ook rechtstreeks wijzigen in ' Services '.  
             v. U kunt een lokale/domein gebruiker voor het aanmeldings account van de IR-service gebruiken.            

            d. De Integration runtime registreren.

* Als de fout ' service ' Integration Runtime service ' (DIAHostService) niet kan worden gestart. Controleer als volgt of u voldoende bevoegdheden hebt om systeem services te starten:

    1. Controleer het *DIAHostService* -aanmeldings service account in het venster van de Windows-service.
    
        ![Scherm opname van het deel venster "aanmelden" voor het service account.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Controleer of het aanmeldings service account is **aangemeld als service** machtiging voor het starten van de Windows-service:

        ![Scherm afbeelding van het eigenschappen venster Aanmelden als service.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Meer informatie

Als geen van de voor gaande twee oplossings patronen in uw geval van toepassing is, kunt u proberen om de volgende Windows-gebeurtenis logboeken te verzamelen: 
- Logboeken van toepassingen en services > Integration Runtime
- Windows-logboeken > toepassing

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Kan de registratie knop niet vinden om een zelf-hostende IR te registreren    

#### <a name="symptoms"></a>Symptomen

Wanneer u een zelf-hostende IR registreert, wordt de knop **registreren** niet weer gegeven in het deel venster Configuration Manager.

![Scherm afbeelding van het deel venster Configuration Manager waarin een bericht wordt weer gegeven dat het knoop punt voor Integration runtime niet is geregistreerd.](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Oorzaak

Vanaf de release van Integration Runtime 3,0 is de knop **registreren** op bestaande Integration runtime-knoop punten verwijderd om een schone en veiligere omgeving mogelijk te maken. Als er een knoop punt is geregistreerd bij een Integration runtime, of het nu online is of niet, moet u het opnieuw registreren bij een andere Integration runtime door het vorige knoop punt te verwijderen en vervolgens het knoop punt te installeren en te registreren.

#### <a name="resolution"></a>Oplossing

1. Verwijder de bestaande Integration runtime in het configuratie scherm.

    > [!IMPORTANT] 
    > In het volgende proces selecteert u **Ja**. Bewaar geen gegevens tijdens het verwijderings proces.

    ![Scherm afbeelding van de knop Ja voor het verwijderen van alle gebruikers gegevens uit de Integration runtime.](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Als u het MSI-bestand voor Integration runtime Installer niet hebt, gaat u naar het [Download centrum](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) om de meest recente Integration runtime te downloaden.
1. Installeer het MSI-bestand en registreer de Integration runtime.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Kan de zelf-hostende IR niet registreren vanwege localhost    

#### <a name="symptoms"></a>Symptomen

U kunt de zelf-hostende IR niet registreren op een nieuwe machine wanneer u get_LoopbackIpOrName gebruikt.

**Fout opsporing:** Er is een runtime fout opgetreden.
Het type initializer voor Microsoft.DataTransfer.DIAgentHost.DataSourceCache heeft een uitzondering veroorzaakt.
Er is een onherstelbare fout opgetreden tijdens het zoeken in de data base.
 
**Uitzonderings Details:** System. TypeInitializationException: de type initialisatie functie voor micro soft. DataTransfer. DIAgentHost. DataSourceCache heeft een uitzonde ring veroorzaakt. ---> System .net. sockets. SocketException: er is een niet-herstel bare fout opgetreden tijdens het zoeken in de Data Base op het systeem .net. DNS. GetAddrInfo (String name).

#### <a name="cause"></a>Oorzaak

Het probleem treedt meestal op wanneer de localhost wordt opgelost.

#### <a name="resolution"></a>Oplossing

Gebruik localhost IP-adres 127.0.0.1 om het bestand te hosten en het probleem op te lossen.

### <a name="self-hosted-setup-failed"></a>Zelf-hostende installatie mislukt    

#### <a name="symptoms"></a>Symptomen

Het is niet mogelijk om een bestaande IR te verwijderen, een nieuwe IR te installeren of een bestaande IR te upgraden naar een nieuwe IR.

#### <a name="cause"></a>Oorzaak

De installatie van Integration runtime is afhankelijk van de Windows Installer-service. U kunt om de volgende redenen installatie problemen ondervinden:
- Onvoldoende schijf ruimte beschikbaar.
- Geen machtigingen.
- De Windows NT-service is vergrendeld.
- Het CPU-gebruik is te hoog.
- Het MSI-bestand wordt gehost op een trage netwerk locatie.
- Sommige systeem bestanden of registers zijn onbedoeld geraakt.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>Het IR-service account kan geen certificaat toegang ophalen

#### <a name="symptoms"></a>Symptomen

Wanneer u een zelf-hostende IR installeert via Microsoft Integration Runtime Configuration Manager, wordt er een certificaat met een vertrouwde certificerings instantie (CA) gegenereerd. Het certificaat kan niet worden toegepast voor het versleutelen van de communicatie tussen twee knoop punten en het volgende fout bericht wordt weer gegeven: 

Fout bij het wijzigen van de versleutelings modus voor intranet communicatie: kan Integration Runtime service account geen toegang verlenen aan het certificaat \<*certificate name*> . Fout code 103

![Scherm opname van het fout bericht '... Kan geen toegang verlenen tot het certificaat van de Integration Runtime-service account.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Oorzaak

Het certificaat maakt gebruik van SLEUTELARCHIEFPROVIDER-opslag (Key Storage Provider), wat nog niet wordt ondersteund. Tot nu toe heeft zelf-hostende IR alleen ondersteuning voor CSP-opslag (Cryptographic Service Provider).

#### <a name="resolution"></a>Oplossing

We raden u aan om in dit geval CSP-certificaten te gebruiken.

**Oplossing 1** 

Voer de volgende opdracht uit om het certificaat te importeren:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Scherm afbeelding van de certutil-opdracht voor het importeren van het certificaat.](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Oplossing 2** 

Voer de volgende opdrachten uit om het certificaat te converteren:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Voor en na de conversie:

![Scherm afbeelding van het resultaat voor de certificaat conversie.](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Scherm opname van het resultaat na de certificaat conversie.](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Zelf-hostende Integration runtime versie 5. x
Voor de upgrade naar versie 5. x van de Azure Data Factory zelf-hostende Integration runtime is **.NET Framework runtime 4.7.2** of hoger vereist. Op de pagina downloaden vindt u Download koppelingen voor de meest recente versie van 4. x en de laatste twee 5. x versies. 

Voor Azure Data Factory v2-klanten:
- Als automatische updates is ingeschakeld en u uw .NET Framework runtime al hebt bijgewerkt naar 4.7.2 of hoger, wordt de zelf-hostende Integration runtime automatisch bijgewerkt naar de meest recente versie van 5. x.
- Als automatische updates is ingeschakeld en u uw .NET Framework runtime niet hebt bijgewerkt naar 4.7.2 of hoger, wordt de zelf-hostende Integration runtime niet automatisch bijgewerkt naar de meest recente versie van 5. x. De zelf-hostende Integration runtime blijft in de huidige 4. x-versie. U kunt een waarschuwing zien voor een .NET Framework runtime-upgrade in de portal en de zelf-hostende Integration runtime-client.
- Als automatische updates is uitgeschakeld en u uw .NET Framework runtime al hebt bijgewerkt naar 4.7.2 of hoger, kunt u de meest recente versie van 5. x hand matig downloaden en installeren op uw computer.
- Als automatische updates is uitgeschakeld en u uw .NET Framework runtime niet hebt bijgewerkt naar 4.7.2 of hoger. Wanneer u zelf-hostende Integration runtime 5. x wilt installeren en de sleutel wilt registreren, moet u eerst een upgrade uitvoeren van uw .NET Framework runtime-versie.


Voor klanten met Azure Data Factory v1:
- Zelf-hostende Integration runtime 5. X biedt geen ondersteuning voor Azure Data Factory v1.
- De zelf-hostende Integration runtime wordt automatisch bijgewerkt naar de meest recente versie van 4. x. En de meest recente versie van 4. x verloopt niet. 
- Als u zelf-hostende Integration runtime 5. x wilt installeren en de sleutel wilt registreren, ontvangt u een melding dat zelf-hostende Integration runtime 5. x geen ondersteuning biedt voor Azure Data Factory v1.


## <a name="self-hosted-ir-connectivity-issues"></a>Zelf-hostende IR-verbindings problemen

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>Een zelf-hostende Integration runtime kan geen verbinding maken met de Cloud service

#### <a name="symptoms"></a>Symptomen

Wanneer u de zelf-hostende Integration runtime probeert te registreren, wordt in Configuration Manager het volgende fout bericht weer gegeven:

' Tijdens de registratie van het knoop punt Integration Runtime (zelf-hostend) is een fout opgetreden. '

![Scherm opname van het bericht ' het Integration Runtime (zelf-hostend)-knoop punt heeft een fout aangetroffen tijdens de registratie '.](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Oorzaak 

De zelf-hostende IR kan geen verbinding maken met de back-end van de Azure Data Factory-service. Dit probleem wordt meestal veroorzaakt door netwerk instellingen in de firewall.

#### <a name="resolution"></a>Oplossing

1. Controleer of de service Integration runtime actief is. Als dat het geval is, gaat u naar stap 2.
    
   ![Scherm opname die laat zien dat de zelf-hostende IR-service wordt uitgevoerd.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Als er geen proxy is geconfigureerd op de zelf-hostende IR, wat de standaard instelling is, voert u de volgende Power shell-opdracht uit op de computer waarop de zelf-hostende Integration runtime is geïnstalleerd:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > De service-URL kan variëren, afhankelijk van de locatie van uw data factory-exemplaar. Als u de service-URL wilt zoeken, selecteert u **ADF UI**  >  **Connections**  >  **Integration Runtimes**  >  **bewerken zelf-hostende IR**-  >  **knoop punten**  >  **weer geven service-url's**.
            
    Hier volgt de verwachte reactie:
            
    ![Scherm afbeelding van het Power shell-opdracht antwoord.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Als u het verwachte antwoord niet ontvangt, gebruikt u een van de volgende methoden, indien van toepassing:
            
    * Als er een bericht wordt weer gegeven dat de externe naam niet kan worden omgezet, is er een probleem met de Domain Name System (DNS). Neem contact op met uw netwerk team om het probleem op te lossen.
    * Als er een bericht wordt weer gegeven dat het SSL/TLS-certificaat niet wordt vertrouwd, [controleert u het certificaat](https://wu2.frontend.clouddatahub.net/) om te zien of het wordt vertrouwd op de computer en installeert u vervolgens het open bare certificaat met behulp van certificaat beheer. Deze actie moet het probleem verminderen.
    * Ga naar logboeken van  >  toepassingen en services van Windows **(Logboeken)**  >    >  **Integration runtime** en controleer op fouten die worden veroorzaakt door DNS, een firewall regel of instellingen van het bedrijfs netwerk. Als u een dergelijke fout vindt, sluit u de verbinding af. Omdat elk bedrijf zijn eigen aangepaste netwerk instellingen heeft, neemt u contact op met uw netwerk team om deze problemen op te lossen.

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
            
![Scherm opname van het verwachte Power shell-opdracht antwoord.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Overwegingen voor de proxy:
> * Controleer of de proxy server moet worden geplaatst in de lijst met veilige geadresseerden. Als dit het geval is, moet u ervoor zorgen dat [deze domeinen](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) zich in de lijst met veilige ontvangers bevinden.
> * Controleer of het SSL/TLS-certificaat "wu2.frontend.clouddatahub.net/" wordt vertrouwd op de proxy server.
> * Als u Active Directory verificatie gebruikt op de proxy, wijzigt u het service account in het gebruikers account dat toegang heeft tot de proxy als ' Integration Runtime-service.

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Fout bericht: zelf-hostende Integration runtime-knoop punt/logische zelf-hostende IR is in inactief/' wordt uitgevoerd (beperkt) '

#### <a name="cause"></a>Oorzaak 

Het zelf-hostende geïntegreerde runtime knooppunt heeft mogelijk de status **inactief**, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Scherm afbeelding van een zelf-hostend geïntegreerd runtime knooppunt met inactieve status](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Dit gedrag treedt op wanneer knoop punten niet met elkaar kunnen communiceren.

#### <a name="resolution"></a>Oplossing

1. Meld u aan bij de virtuele machine (VM) die wordt gehost op het knoop punt. Open in de **Logboeken toepassingen en services**  >  **Integration runtime** logboeken en filter de fouten Logboeken.

1. Controleer of een fouten logboek de volgende fout bevat: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Als u deze fout ziet, voert u de volgende opdracht uit in een opdracht prompt venster: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Neem contact op met uw IT-afdeling om dit probleem op te lossen als u de opdracht regel fout ' kan geen verbinding met de host openen ' wordt weer gegeven in de volgende scherm afbeelding. Wanneer u met succes kunt Telnet, neemt u contact op met Microsoft Ondersteuning als u nog steeds problemen ondervindt met de status van het Integration runtime-knoop punt.
        
   ![Scherm opname van de opdracht regel fout ' kan geen verbinding met de host openen '.](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Controleer of het fouten logboek de volgende vermelding bevat:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Probeer een of beide van de volgende methoden om het probleem op te lossen:
    - Plaats alle knoop punten in hetzelfde domein.
    - Voeg het IP-adres toe aan de host toewijzing in alle host-bestanden van de gehoste VM.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Connectiviteits probleem tussen de zelf-hostende IR en uw data factory-exemplaar of de zelf-hostende IR en de gegevens bron of sink

Als u problemen met het netwerk connectiviteits probleem wilt oplossen, moet u weten hoe u de netwerk tracering kunt verzamelen, inzicht kunt krijgen in hoe u deze moet gebruiken en hoe u [de Microsoft Network Monitor (netmon) moet analyseren](#analyze-the-netmon-trace) voordat u de hulpprogram Ma's voor netmon in real cases van de zelf-hostende IR toepast.

#### <a name="symptoms"></a>Symptomen

U kunt af en toe bepaalde verbindings problemen oplossen tussen de zelf-hostende IR en uw data factory-exemplaar, zoals wordt weer gegeven in de volgende scherm afbeelding of tussen de zelf-hostende IR en de gegevens bron of sink. 

![Scherm opname van bericht van de verwerkte HTTP-aanvraag is mislukt](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

In beide gevallen kunnen de volgende fouten optreden:

* Kopiëren is mislukt met de volgende fout: type = micro soft. DataTransfer. common. Shared. HybridDeliveryException, Message = kan geen verbinding maken met SQL Server: IP-adres

* "Er zijn een of meer fouten opgetreden. Er is een fout opgetreden tijdens het verzenden van de aanvraag. De onderliggende verbinding is gesloten: er is een onverwachte fout opgetreden bij het ontvangen. Kan geen gegevens lezen uit de transport verbinding: een bestaande verbinding is geforceerd gesloten door de externe host. Een bestaande verbinding is geforceerd gesloten door de activiteits-ID van de externe host. "

#### <a name="resolution"></a>Oplossing

Wanneer u de voor gaande fouten tegen komt, kunt u deze oplossen door de instructies in deze sectie te volgen.

- Een netmon-tracering verzamelen voor analyse: 

    1. U kunt het filter zo instellen dat de server weer opnieuw wordt ingesteld op de client. In de volgende voorbeeld scherm afbeelding ziet u dat de server kant de Data Factory-server is.

        ![Scherm afbeelding van de Data Factory-server.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. Wanneer u het pakket opnieuw instellen krijgt, kunt u de conversatie vinden door Transmission Control Protocol (TCP) te volgen.

        ![Scherm opname van de TCP-conversatie.](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Haal de conversatie tussen de client en de onderstaande Data Factory server op door het filter te verwijderen.

        ![Scherm afbeelding van gespreks Details.](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- Een analyse van de netmon-tracering die u hebt verzameld, laat zien dat het totaal van de Time to Live (TTL) is 64. Op basis van de waarden die worden vermeld in de [IP-time to Live (TTL) en het basis](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) hoofd artikel over limieten voor hops in de volgende lijst, kunt u zien dat het Linux-systeem is dat het pakket opnieuw instelt en de verbinding verbreken.

    De standaard waarden voor TTL en hops variëren tussen verschillende besturings systemen, zoals hier wordt weer gegeven:
    - Linux kernel 2,4 (circa 2001): 255 voor TCP, User Data gram Protocol (UDP) en Internet Control Message Protocol (ICMP)
    - Linux kernel 4,10 (2015): 64 voor TCP, UDP en ICMP
    - Windows XP (2001): 128 voor TCP, UDP en ICMP
    - Windows 10 (2015): 128 voor TCP, UDP en ICMP
    - Windows Server 2008:128 voor TCP, UDP en ICMP
    - Windows Server 2019 (2018): 128 voor TCP, UDP en ICMP
    - macOS (2001): 64 voor TCP, UDP en ICMP

    ![Scherm opname met een TTL-waarde van 61.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    In het vorige voor beeld wordt de TTL weer gegeven als 61 in plaats van 64, omdat wanneer het netwerk pakket de bestemming bereikt, de verschillende hops moeten worden gebruikt, zoals routers of netwerk apparaten. Het aantal routers of netwerk apparaten wordt in mindering gebracht voor het produceren van de laatste TTL.
    
    In dit geval kunt u zien dat een reset van het Linux-systeem met TTL 64 kan worden verzonden.

-  Om te bevestigen dat het apparaat opnieuw moet worden ingesteld, controleert u de vierde hop van zelf-hostende IR.
 
    *Netwerk pakket van Linux-systeem A met TTL 64-> B TTL 64 min 1 = 63-> C TTL 63 min 1 = 62-> TTL 62 min 1 = 61 zelf-hostende IR*

- In een ideale situatie is het TTL-hops nummer 128, wat betekent dat het Windows-besturings systeem uw data factory-exemplaar uitvoert. Zoals in het volgende voor beeld wordt weer gegeven, *128 min 107 = 21 hops*, wat betekent dat er 21 hops voor het pakket zijn verzonden vanuit het Data Factory-exemplaar naar de zelf-hostende IR tijdens de TCP 3-handshake.
 
    ![Scherm opname met een TTL-waarde van 107.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Daarom moet u het netwerk team laten controleren of u wilt zien wat de vierde hop is van de zelf-hostende IR. Als het de firewall is, zoals bij het Linux-systeem, controleert u de logboeken om te zien waarom dat apparaat het pakket opnieuw instelt na de TCP 3-handshake. 
    
    Als u niet zeker weet waar u onderzoek moet doen, probeert u de netmon-tracering van zowel de zelf-hostende IR als de firewall tijdens de problematische tijd. Met deze aanpak kunt u bepalen welk apparaat het pakket mogelijk opnieuw heeft ingesteld en de verbinding verbreken heeft veroorzaakt. In dit geval moet u ook uw netwerk team laten door lopen.

### <a name="analyze-the-netmon-trace"></a>De netmon-tracering analyseren

> [!NOTE] 
> De volgende instructies zijn van toepassing op de netmon-tracering. Omdat netmon trace momenteel niet wordt ondersteund, kunt u wireshark gebruiken voor dit doel einde.

Wanneer u probeert **8.8.8.8 888** te telneten met de verzamelde netmon-tracering, ziet u de tracering in de volgende scherm afbeeldingen:

![Fout bericht ' kan geen verbinding met de host openen op poort 888 '.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Scherm opname met een beschrijving van de netmon-tracering.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

In de voor gaande installatie kopieën ziet u dat er geen TCP-verbinding met de **8.8.8.8** -server op poort **888** kan worden gemaakt, zodat **er twee extra** pakketten worden weer gegeven. Omdat de bron **Self-HOST2** geen verbinding kan maken met **8.8.8.8** met het eerste pakket, blijft de verbinding proberen te maken.

> [!TIP]
> Als u deze verbinding wilt maken, probeert u de volgende oplossing:
> 1. Selecteer filter voor  >  **Standaard filter** laden  >    >  **IPv4-adressen**.
> 1. Als u het filter wilt Toep assen, voert u **IPv4. Address = = 8.8.8.8** in en selecteert u vervolgens **Toep assen**. Vervolgens ziet u de communicatie van de lokale machine naar de doel- **8.8.8.8**.

![Scherm opname met filter adressen.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Scherm opname met meer filter adressen.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Geslaagde scenario's worden weer gegeven in de volgende voor beelden: 

- Als u zonder problemen Telnet **8.8.8.8 53** kunt, is er een geslaagde TCP 3-Handshake en wordt de sessie voltooid met een TCP 4-handshake.

    ![Scherm opname met een geslaagd verbindings scenario.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Scherm afbeelding met details van een geslaagd verbindings scenario.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- De voor gaande TCP 3-Handshake produceert de volgende werk stroom:

    ![Diagram van een TCP 3 Handshake-werk stroom.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- De TCP 4-Handshake voor het volt ooien van de sessie wordt geïllustreerd door de volgende werk stromen:

    ![Scherm opname van Details van TCP 4-handshake.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagram van een TCP 4-Handshake-werk stroom.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>Micro soft-e-mail melding over het bijwerken van uw netwerk configuratie

U kunt de volgende e-mail melding ontvangen, waarmee wordt aanbevolen uw netwerk configuratie bij te werken om communicatie met nieuwe IP-adressen voor Azure Data Factory op 8 november 2020 mogelijk te maken:

   ![Scherm opname van micro soft-e-mail melding dat een update van de netwerk configuratie aanvragen.](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Bepalen of deze melding van invloed is op u

Deze melding is van toepassing op de volgende scenario's:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>Scenario 1: uitgaande communicatie van een zelf-hostende Integration runtime die on-premises wordt uitgevoerd achter een bedrijfs firewall

Controleren of u dit ondervindt:

- U *wordt niet* beïnvloed als u firewall regels definieert op basis van FQDN-namen (Fully Qualified Domain names) die gebruikmaken van de methode die wordt beschreven in [een firewall configuratie en acceptatie lijst instellen voor IP-adressen](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).

- Dit *wordt* beïnvloed als u de acceptatie lijst expliciet inschakelt voor uitgaande IP-adressen in uw bedrijfs firewall.

   Als u dit probleem ondervindt, voert u de volgende actie uit: op 8 november 2020 stelt u uw netwerk infrastructuur team in om uw netwerk configuratie bij te werken om de nieuwste data factory IP-adressen te gebruiken. Als u de meest recente IP-adressen wilt downloaden, gaat u naar [service Tags detecteren met behulp van Download bare json-bestanden](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>Scenario 2: uitgaande communicatie van een zelf-hostende Integration runtime die wordt uitgevoerd op een Azure-VM binnen een door de klant beheerd virtueel Azure-netwerk

Controleren of u dit ondervindt:

- Controleer of u een NSG-regels (uitgaand netwerk beveiligings groep) hebt in een particulier netwerk dat zelf-hostende Integration runtime bevat. Als er geen uitgaande beperkingen zijn, heeft dit geen invloed op dit probleem.

- Als u uitgaande regel beperkingen hebt, controleert u of u service tags gebruikt. Als u service tags gebruikt, hebt u geen last van dit probleem. U hoeft niets te wijzigen of toe te voegen, omdat het nieuwe IP-bereik zich onder uw bestaande service Tags bevindt. 

  ![Scherm opname van een doel controle met DataFactory als doel.](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- U kunt dit probleem verhelpen *Als u expliciet* de acceptatie lijst voor uitgaande IP-adressen in uw NSG-instellingen in het virtuele netwerk van Azure inschakelt.

   Als u dit probleem ondervindt, voert u de volgende actie uit: op 8 november 2020 stelt u uw netwerk infrastructuur team in om de NSG-regels in de configuratie van uw virtuele Azure-netwerk bij te werken om de nieuwste data factory IP-adressen te gebruiken. Als u de meest recente IP-adressen wilt downloaden, gaat u naar [service Tags detecteren met behulp van Download bare json-bestanden](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>Scenario 3: uitgaande communicatie van SSIS Integration Runtime in een door de klant beheerd virtueel Azure-netwerk

Controleren of u dit ondervindt:

- Controleer of u de regels voor uitgaande NSG hebt in een particulier netwerk dat SQL Server Integration Services (SSIS) bevat Integration Runtime. Als er geen uitgaande beperkingen zijn, heeft dit geen invloed op dit probleem.

- Als u uitgaande regel beperkingen hebt, controleert u of u service tags gebruikt. Als u service tags gebruikt, hebt u geen last van dit probleem. U hoeft niets te wijzigen of toe te voegen omdat het nieuwe IP-bereik zich onder uw bestaande service Tags bevindt.

- U kunt dit probleem verhelpen *Als u expliciet* de acceptatie lijst voor uitgaande IP-adressen in uw NSG-instellingen in het virtuele netwerk van Azure inschakelt.

  Als u dit probleem ondervindt, voert u de volgende actie uit: op 8 november 2020 stelt u uw netwerk infrastructuur team in om de NSG-regels in de configuratie van uw virtuele Azure-netwerk bij te werken om de nieuwste data factory IP-adressen te gebruiken. Als u de meest recente IP-adressen wilt downloaden, gaat u naar [service Tags detecteren met behulp van Download bare json-bestanden](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Kan geen vertrouwens relatie tot stand brengen voor het beveiligde SSL/TLS-kanaal 

#### <a name="symptoms"></a>Symptomen

De zelf-hostende IR kan geen verbinding maken met de Azure Data Factory-service.

Wanneer u het zelf-hostende IR-gebeurtenis logboek of de logboeken voor client meldingen in de CustomLogEvent-tabel controleert, wordt het volgende fout bericht weer gegeven:

"De onderliggende verbinding is gesloten: kan geen vertrouwens relatie tot stand brengen voor het beveiligde SSL/TLS-kanaal. Het externe certificaat is ongeldig volgens de validatie procedure.

De eenvoudigste manier om het server certificaat van de Data Factory-service te controleren is door de URL van de Data Factory-service in uw browser te openen. Open bijvoorbeeld de [koppeling server certificaat controleren](https://eu.frontend.clouddatahub.net/) op de computer waarop de zelf-hostende IR is geïnstalleerd en bekijk vervolgens de gegevens van het server certificaat.

  ![Scherm opname van het deel venster server certificaat controleren van de Azure Data Factory-service.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Scherm afbeelding van het venster voor het controleren van het server certificerings traject.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Oorzaak

Er zijn twee mogelijke redenen voor dit probleem:

- Reden 1: de basis-CA van het certificaat van de Data Factory-Service server wordt niet vertrouwd op de computer waarop de zelf-hostende IR is geïnstalleerd. 
- Reden 2: u gebruikt een proxy in uw omgeving, het server certificaat van de Data Factory-service wordt vervangen door de proxy en het vervangen server certificaat wordt niet vertrouwd door de computer waarop de zelf-hostende IR is geïnstalleerd.

#### <a name="resolution"></a>Oplossing

- Voor de reden 1: Zorg ervoor dat het Data Factory server-certificaat en de bijbehorende certificaat keten worden vertrouwd door de computer waarop de zelf-hostende IR is geïnstalleerd.
- Voor de reden 2: vertrouw de vervangen basis certificerings instantie op de zelf-hostende IR-computer of configureer de proxy niet om het Data Factory server certificaat te vervangen.

Zie [het vertrouwde basis certificaat installeren](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate)voor meer informatie over het vertrouwen van certificaten in Windows.

#### <a name="additional-information"></a>Aanvullende informatie
We hebben een nieuw SSL-certificaat geïmplementeerd dat is ondertekend vanuit DigiCert. Controleer of de DigiCert Global root G2 zich in de vertrouwde basis certificerings instantie bevindt.

  ![Scherm opname van de DigiCert Global root G2 in de map met vertrouwde basis certificerings instanties.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Als deze niet in de vertrouwde basis certificerings instantie voor komt, kunt [u deze hier downloaden](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Zelf-hostende IR-deling

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Het delen van een zelf-hostende IR vanuit een andere Tenant wordt niet ondersteund 

#### <a name="symptoms"></a>Symptomen

U kunt andere gegevens fabrieken (op verschillende tenants) zien als u probeert de zelf-hostende IR te delen vanuit de gebruikers interface van Azure Data Factory, maar u kunt deze niet delen via gegevens fabrieken die zich op verschillende tenants bevinden.

#### <a name="cause"></a>Oorzaak

De zelf-hostende IR kan niet worden gedeeld tussen tenants.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Micro soft Q&een pagina](/answers/topics/azure-data-factory.html)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatie gids gegevens stromen toewijzen](concepts-data-flow-performance.md)
