---
title: Een back-up maken van een share point-Farm op Azure Stack
description: Gebruik Azure Backup Server om een back-up te maken van uw share point-gegevens en deze te herstellen op Azure Stack. In dit artikel vindt u informatie over het configureren van uw share point-Farm zodat gewenste gegevens kunnen worden opgeslagen in Azure. U kunt beveiligde share point-gegevens herstellen vanaf schijf of Azure.
ms.topic: conceptual
ms.date: 06/07/2020
ms.openlocfilehash: 1e237e63b92468fafff4f8f8f525d1388840d162
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89378318"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Een back-up maken van een share point-Farm op Azure Stack

U maakt een back-up van een share point-Farm op Azure Stack naar Microsoft Azure met behulp van Microsoft Azure Backup-Server (MABS) op ongeveer dezelfde manier als u een back-up maakt van andere gegevens bronnen. Azure Backup biedt flexibiliteit in het back-upschema om dagelijkse, wekelijkse, maandelijkse of jaarlijkse back-uppunten te maken en biedt u Bewaar beleidsopties voor verschillende back-uppunten. Het biedt ook de mogelijkheid om lokale schijf kopieën op te slaan voor snelle herstel tijd (RTO) en om kopieën op te slaan in azure voor een voordelige, lange termijn retentie.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Ondersteunde versies van share point en gerelateerde beveiligings scenario's

Azure Backup voor MABS ondersteunt de volgende scenario's:

| Workload | Versie | Share point-implementatie | Beveiliging en herstel |
| --- | --- | --- | --- |
| SharePoint |Share point 2016, share point 2013, share point 2010 |Share point is geïmplementeerd als een Azure Stack virtuele machine <br> -------------- <br> SQL AlwaysOn | Opties voor herstel van share point-Farm beveiligen: herstel Farm, Data Base, en bestand of lijst item van schijf herstel punten.  Farm-en database herstel van Azure-herstel punten. |

## <a name="before-you-start"></a>Voordat u begint

Er zijn enkele dingen die u moet bevestigen voordat u een back-up van een share point-farm maakt in Azure.

### <a name="whats-not-supported"></a>Wat wordt er niet ondersteund

* MABS die een share point-Farm beveiligt, beveiligen geen zoek indexen of Application Service-data bases. U moet de beveiliging van deze data bases afzonderlijk configureren.

* MABS biedt geen back-up van share point-SQL Server data bases die worden gehost op scale-out Bestands server (SOFS)-shares.

### <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet u ervoor zorgen dat u voldoet aan alle [vereisten voor het gebruik van Microsoft Azure backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) voor het beveiligen van werk belastingen. Enkele taken voor vereisten zijn: Maak een back-upkluis, down load kluis referenties, installeer Azure Backup Agent en registreer de Azure Backup Server bij de kluis.

Aanvullende vereisten en beperkingen:

* Wanneer u share point beveiligt, worden alle inhouds databases (en de SharePoint_Config en SharePoint_AdminContent * data bases) standaard beveiligd. Als u aanpassingen wilt toevoegen, zoals zoekindexen, sjablonen, toepassingsservicedatabases of de gebruikersprofielservice, moet u deze afzonderlijk configureren voor de beveiliging. Zorg ervoor dat u de beveiliging voor alle mappen met deze typen functies of aanpassingsbestanden inschakelt.

* U kunt SharePoint-databases niet beveiligen als een SQL Server-gegevensbron. U kunt afzonderlijke databases herstellen van een back-up van de farm.

* Houd er rekening mee dat MABS wordt uitgevoerd als **lokaal systeem** en om back-ups te maken van SQL server-data bases die sysadmin-bevoegdheden nodig hebben voor dat account voor de SQL Server. Stel op de SQL Server waarvan u een back-up wilt maken, NT AUTHORITY\SYSTEM in op **sysadmin**.

* Voor elke 10.000.000-items in de farm moet er ten minste 2 GB aan ruimte zijn op het volume waar de MABS-map zich bevindt. Deze ruimte is vereist voor het genereren van de catalogus. Om u in staat te stellen MABS te gebruiken voor het uitvoeren van een specifiek herstel van items (site verzamelingen, sites, lijsten, document bibliotheken, mappen, afzonderlijke documenten en lijst items), maakt catalogus genereren een lijst van de Url's die zijn opgenomen in elke inhouds database. U kunt de lijst met Url's bekijken in het deel venster herstelbaar item in het taak gebied herstel van de MABS Administrator-console.

* Als u in de share point-Farm SQL Server data bases hebt die zijn geconfigureerd met SQL Server aliassen, installeert u de SQL Server-client onderdelen op de front-endwebserver die MABS zal beveiligen.

* De beveiliging van toepassingsarchiefitems wordt niet ondersteund door SharePoint 2013.

* MABS biedt geen ondersteuning voor de beveiliging van externe FILESTREAM. De FILESTREAM moet deel uitmaken van de database.

## <a name="configure-backup"></a>Back-up configureren

Als u een back-up wilt maken van de share point-Farm, configureert u beveiliging voor share point met behulp van ConfigureSharePoint.exe en maakt u een beveiligings groep in MABS.

1. **ConfigureSharePoint.exe uitvoeren**: met dit hulpprogramma configureert u de SharePoint VSS Writer-service \(WSS\) en voorziet u de beveiligingsagent van referenties voor de SharePoint-farm. Nadat u de beveiligings agent hebt geïmplementeerd, bevindt het ConfigureSharePoint.exe-bestand zich in de `<MABS Installation Path\>\bin` map op de front- \- endwebserver.  Als u meerdere WFE-servers hebt, hoeft u deze alleen maar op een van beide te installeren. Voer het bestand als volgt uit:

    * Ga op de WFE-server naar de opdracht prompt `\<MABS installation location\>\\bin\\` en voer deze uit `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` , waarbij:

        * **EnableSharePointProtection** maakt beveiliging mogelijk van de share point-Farm, schakelt de VSS Writer in en registreert de identiteit van de DCOM-toepassing wsscmdletswrapper om om uit te voeren als gebruiker van wie de referenties met deze optie worden ingevoerd. Dit account moet een farmbeheerder en ook een lokale beheerder op de front\-endwebserver zijn.

        * Met **EnableSPSearchProtection** wordt de beveiliging van WSS 3.0 SP Search ingeschakeld met de registersleutel SharePointSearchEnumerationEnabled onder HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ on the front\-endwebserver en wordt de identiteit van de DCOM-toepassing WssCmdletsWrapper geregistreerd om te worden uitgevoerd als een gebruiker van wie de referenties met deze optie zijn ingevoerd. Dit account moet een farmbeheerder en ook een lokale beheerder op de front\-endwebserver zijn.

        * **ResolveAllSQLAliases**: hiermee worden alle aliassen weergegeven die zijn gerapporteerd door de SharePoint VSS Writer en worden deze omgezet naar de overeenkomstige SQL-server. De omgezette exemplaarnamen worden ook weergegeven. Als de servers zijn gespiegeld, worden de gespiegelde servers ook weergegeven. Alle aliassen die niet worden omgezet in een SQL Server, worden gerapporteerd.

        * Met **SetTempPath** stelt u de omgevingsvariabelen TEMP en TMP in voor het opgegeven pad. Herstel op item niveau mislukt als er een grote site verzameling, site, lijst of item wordt hersteld en er onvoldoende ruimte is in de tijdelijke map van de farm beheerder. Met deze optie kunt u het mappad wijzigen van de tijdelijke bestanden naar een volume met voldoende ruimte om de siteverzameling of de site die wordt hersteld, op te slaan.

    * Geef de referenties van de farmbeheerder op. Deze account moet een lid zijn van een lokale beheerdersgroep op de WFE-server. Als de farm beheerder geen lokale beheerder is, wijst u de volgende machtigingen toe op de WFE-server:

        * Ken de **WSS_Admin_WPG** groep volledig beheer toe aan de map MABS ( `%Program Files%\Data Protection Manager\DPM\` ).

        * Verleen de **WSS_Admin_WPG** -groep lees toegang tot de register sleutel MABS ( `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager` ).

        Nadat ConfigureSharePoint.exe is uitgevoerd, moet u het opnieuw uitvoeren als er een wijziging is in de referenties van de share point-farm beheerder.

1. Als u een beveiligings groep wilt maken, selecteert u **beveiligings**  >  **acties**  >  **beveiligings groep maken** om de wizard **nieuwe beveiligings groep maken** in de MABS-console te openen.

1. In **type beveiligings groep** selecteren selecteert u **servers**.

1. Vouw in **groeps leden selecteren** de server uit die de functie WFE bevat. Als er meer dan één WFE-server is, selecteert u de versie waarop u ConfigureSharePoint.exe hebt geïnstalleerd.

    Wanneer u de MABS query's van share Point server uitvouwt, kunt u zien welke gegevens MABS kunnen beveiligen.  Als de share point-data base extern is, wordt er met MABS verbinding gemaakt. Als share point-gegevens bronnen niet worden weer gegeven, controleert u of de VSS Writer wordt uitgevoerd op de share Point-server en eventuele externe SQL Server en of de MABS-agent is geïnstalleerd op zowel de share Point-server als de externe SQL Server. Zorg er ook voor dat share point-data bases niet elders worden beveiligd als SQL Server data bases.

1. Geef in **methode voor gegevens beveiliging selecteren** op hoe u \- back-ups voor de korte en de lange termijn wilt verwerken. Back-ups op korte \- termijn zijn altijd eerst op schijf, met de optie om een back-up te maken van de schijf naar de Azure-Cloud met Azure backup \( voor korte of lange \- termijn \) .

1. Geef in **\- doel stelling van de korte term** op hoe u back-ups wilt maken naar de \- opslag op korte termijn op schijf.   Geef in **Bewaar termijn** op hoe lang u de gegevens op de schijf wilt bewaren. In **synchronisatie frequentie** geeft u op hoe vaak u een incrementele back-up naar schijf wilt uitvoeren. Als u geen back-upinterval wilt instellen, kunt u net voor een herstel punt controleren, zodat MABS een snelle volledige back-up uitvoert, net voordat elk herstel punt wordt gepland.

1. Controleer op de pagina schijf toewijzing controleren de schijf ruimte van de opslag groep die is toegewezen aan de beveiligings groep.

    **Totale gegevens grootte** is de grootte van de gegevens waarvan u een back-up wilt maken en **schijf ruimte die moet worden ingericht op MABS** is de ruimte die MABS raadt aan de beveiligings groep. MABS kiest het ideale back-upvolume, op basis van de instellingen. U kunt echter de keuzes voor het back-upvolume bewerken in **Schijftoewijzingsdetails**. Selecteer voor de werkbelastingen de gewenste opslag in het vervolgkeuzemenu. Met uw bewerkingen worden de waarden voor **Totale opslag** en **Vrije opslagruimte** in het deelvenster **Beschikbare schijfopslag** gewijzigd. Onderingerichte ruimte is de hoeveelheid opslag MABS die u toevoegt aan het volume, zodat u in de toekomst probleemloos kunt door gaan met het maken van back-ups.

1. In **methode voor maken van replica** selecteren selecteert u hoe u de eerste volledige gegevens replicatie wilt verwerken.  Als u ervoor kiest om via het netwerk te repliceren, raden we u aan een rustige tijd te kiezen. Bij grote hoeveelheden gegevens of minder dan optimale netwerkomstandigheden, kunt u de gegevens offline repliceren met verwijderbare media.

1. Selecteer in **Opties voor consistentiecontrole selecteren** hoe u consistentiecontroles wilt automatiseren. U kunt instellen dat er alleen een controle wordt uitgevoerd als de gerepliceerde gegevens inconsistent worden, of volgens een planning. Als u geen automatische consistentie controle wilt configureren, kunt u op elk gewenst moment een hand matige controle uitvoeren door met de rechter muisknop op de beveiligings groep in het gebied **beveiliging** van de MABS-console te klikken en **consistentie controle uitvoeren** te selecteren.

1. Als u hebt gekozen voor het maken van een back-up naar de cloud met Azure Backup, moet u ervoor zorgen dat u op de pagina **Gegevens voor onlinebeveiliging opgeven** de werkbelastingen selecteert waarvan u een back-up wilt maken naar Azure.

1. Geef in **schema voor online back-ups opgeven** op hoe vaak incrementele back-ups naar Azure moeten worden uitgevoerd. U kunt back-ups dagelijks/wekelijks/maandelijks/jaarlijks uitvoeren en de tijd en datum opgeven waarop u deze wilt uitvoeren. U kunt maximaal twee keer per dag back-ups uitvoeren. Telkens wanneer een back-up wordt uitgevoerd, wordt er een gegevens herstel punt gemaakt in azure van de kopie van de back-upgegevens die zijn opgeslagen op de MABS-schijf.

1. In **online Bewaar beleid opgeven** kunt u opgeven hoe de herstel punten die zijn gemaakt op basis van de dagelijkse/wekelijkse/maandelijkse/jaarlijkse back-ups, worden bewaard in Azure.

1. In **Kies online replicatie**, geeft u op hoe de eerste volledige replicatie van gegevens wordt uitgevoerd. U kunt repliceren via het netwerk of een offlineback-up uitvoeren (offline-seeding). Voor offlineback-ups wordt gebruikgemaakt van de functie Azure Import. [Meer informatie](./backup-azure-backup-import-export.md).

1. Controleer uw instellingen op de pagina  **samen vatting** . Nadat u **groep maken** hebt geselecteerd, vindt de initiële replicatie van de gegevens plaats. Wanneer deze is voltooid, wordt de status van de beveiligings groep weer gegeven op **OK** op de pagina **status** . Back-up vindt plaats in overeenstemming met de beveiligingsgroepsinstellingen.

## <a name="monitoring"></a>Bewaking

Nadat de beveiligings groep is gemaakt, vindt de eerste replicatie plaats en MABS wordt een back-up gemaakt en worden de share point-gegevens gesynchroniseerd. MABS controleert de initiële synchronisatie en de volgende back-ups.  U kunt de SharePoint-gegevens op verschillende manieren controleren:

* Met de standaard MABS-controle kunt u meldingen instellen voor proactieve controle door waarschuwingen te publiceren en meldingen te configureren. U kunt meldingen verzenden via e-mail voor kritieke of informerende meldingen of waarschuwingen en voor de status van geïnstantieerde herstelbewerkingen.

* Als u Operations Manager gebruikt, kunt u waarschuwingen centraal publiceren.

### <a name="set-up-monitoring-notifications"></a>Controlemeldingen instellen

1. Selecteer in de MABS Administrator-console **bewaking**  >  **actie**  >  **Opties**.

2. Selecteer **SMTP-server**, typ de server naam, de poort en het e-mail adres waarvan de meldingen worden verzonden. Het adres moet geldig zijn.

3. In **geverifieerde SMTP-server** typt u een gebruikers naam en wacht woord. De gebruikers naam en het wacht woord moeten de domein accountnaam zijn van de persoon waarvan het ' van '-adres wordt beschreven in de vorige stap. Anders mislukt de levering van de melding.

4. Als u de SMTP-server instellingen wilt testen, selecteert u **test bericht verzenden**, typt u het e-mail adres waarnaar u het test bericht wilt verzenden en selecteert u vervolgens **OK**. Selecteer **Opties**  >  **meldingen** en selecteer het type waarschuwingen waarover ontvangers meldingen willen ontvangen. In **ontvangers** typt u het e-mail adres van elke geadresseerde aan wie u wilt dat MABS kopieën van de meldingen verzendt.

### <a name="publish-operations-manager-alerts"></a>Waarschuwingen voor Operations Manager publiceren

1. Selecteer in de MABS Administrator-console **bewakings**  >  **actie**  >  **Opties**  >  **waarschuwing** publiceren  >  **publiceren actieve waarschuwingen**

2. Nadat u **waarschuwingen publiceren** hebt ingeschakeld, worden alle bestaande MABS-waarschuwingen waarvoor een gebruikers actie is vereist, gepubliceerd naar het gebeurtenis logboek van de **MABS-waarschuwingen** . De Operations Manager-agent die op de MABS-server is geïnstalleerd, publiceert deze waarschuwingen vervolgens naar de Operations Manager en blijft de console bijwerken wanneer er nieuwe waarschuwingen worden gegenereerd.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Een share point-item herstellen vanaf schijf met behulp van MABS

In het volgende voor beeld is het herstel van het *share point-item* per ongeluk verwijderd en moet het worden hersteld.
![MABS share point Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Open de **MABS-beheer console**. Alle share point-Farms die worden beveiligd door MABS, worden weer gegeven op het tabblad **beveiliging** .

    ![MABS share point Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Selecteer het tabblad **herstel** om te beginnen met het herstellen van het item.

    ![MABS share point Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. U kunt in share Point zoeken naar het herstellen van een *share point-item* met behulp van een zoek opdracht op basis van joker tekens binnen een herstel punt bereik.

    ![MABS share point Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecteer het juiste herstel punt in de zoek resultaten, klik met de rechter muisknop op het item en selecteer vervolgens **herstellen**.
5. U kunt ook bladeren door verschillende herstel punten en een Data Base of een item selecteren dat u wilt herstellen. Selecteer **datum > herstel tijd** en selecteer vervolgens de juiste **Data Base > share point-Farm > herstel punt > item**.

    ![MABS share point Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klik met de rechter muisknop op het item en selecteer vervolgens **herstellen** om de **wizard herstellen** te openen. Selecteer **Next**.

    ![Selectie voor herstel controleren](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecteer het type herstel dat u wilt uitvoeren en selecteer vervolgens **volgende**.

    ![Herstel type](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > De selectie van **herstellen naar origineel** in het voor beeld herstelt het item naar de oorspronkelijke share point-site.
   >
   >
8. Selecteer het **herstel proces** dat u wilt gebruiken.

   * Selecteer **herstellen zonder een herstel Farm te gebruiken** als de share point-Farm niet is gewijzigd en gelijk is aan het herstel punt dat wordt hersteld.
   * Selecteer **herstellen met een herstel Farm** als de share point-Farm is gewijzigd sinds het herstel punt is gemaakt.

     ![Herstelproces](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Geef een staging SQL Server locatie van het exemplaar op om de data base tijdelijk te herstellen en geef een staging-bestands share op MABS en de server waarop share point wordt uitgevoerd om het item te herstellen.

    ![Staging-Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS koppelt de inhouds database die als host fungeert voor het share point-item aan het tijdelijke SQL Server-exemplaar. Vanuit de inhouds database wordt het item hersteld en geplaatst op de locatie van het staging-bestand op MABS. Het herstelde item op de faserings locatie moet nu worden geëxporteerd naar de faserings locatie in de share point-farm.

    ![Staging-Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecteer **herstel opties opgeven** en pas beveiligings instellingen toe op de share point-Farm of pas de beveiligings instellingen van het herstel punt toe. Selecteer **Next**.

    ![Herstel opties](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > U kunt ervoor kiezen het netwerk bandbreedte gebruik te beperken. Dit beperkt de gevolgen voor de productie server tijdens productie-uren.
    >
    >
11. Controleer de samenvattings informatie en selecteer vervolgens **herstellen** om te beginnen met het herstellen van het bestand.

    ![Samen vatting van herstel](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selecteer nu het tabblad **controle** in de **MABS-beheer console** om de **status** van het herstel weer te geven.

    ![Herstel status](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Het bestand wordt nu teruggezet. U kunt de share point-site vernieuwen om het herstelde bestand te controleren.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Een share point-data base terugzetten vanuit Azure met behulp van MABS

1. Als u een share point-inhouds database wilt herstellen, bladert u door verschillende herstel punten (zoals eerder weer gegeven) en selecteert u het herstel punt dat u wilt herstellen.

    ![MABS share point Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbel klik op het share point-herstel punt om de beschik bare share point-catalogus gegevens weer te geven.

   > [!NOTE]
   > Omdat de share point-Farm is beveiligd voor lange termijn retentie in azure, is er geen catalogus informatie (meta gegevens) beschikbaar op de MABS-server. Wanneer een tijdgebonden share point-inhouds database moet worden hersteld, moet u de share point-Farm opnieuw catalogiseren.
   >
   >
3. Selecteer **opnieuw catalogiseren**.

    ![MABS share point Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Het venster status van **Cloud opnieuw catalogiseren** wordt geopend.

    ![MABS share point Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Nadat het catalogiseren is voltooid, verandert de status in *geslaagd*. Selecteer **Sluiten**.

    ![MABS share point Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Selecteer het share point-object dat wordt weer gegeven op het tabblad MABS- **herstel** om de structuur van de inhouds database op te halen. Klik met de rechter muisknop op het item en selecteer vervolgens **herstellen**.

    ![MABS share point Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Volg op dit punt de herstel stappen eerder in dit artikel om een share point-inhouds database van schijf te herstellen.

## <a name="switching-the-front-end-web-server"></a>Scha kelen tussen de Front-End webserver

Als u meer dan één front-endwebserver hebt en u wilt overschakelen van de server die MABS gebruikt om de farm te beveiligen, volgt u de instructies:

De volgende procedure maakt gebruik van het voor beeld van een server farm met twee front-end-webservers, *Server1* en *server2*. MABS maakt gebruik van *Server1* om de farm te beveiligen. Wijzig de front-end-webserver die MABS gebruikt voor *server2* zodat u *Server1* uit de farm kunt verwijderen.

> [!NOTE]
> Als de front-endwebserver die MABS gebruikt om de farm te beveiligen, niet beschikbaar is, gebruikt u de volgende procedure om de front-end-webserver te wijzigen door te beginnen bij stap 4.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>De front-end-webserver die MABS gebruikt om de farm te beveiligen, wijzigen

1. Stop de share point VSS Writer-service op *Server1* door de volgende opdracht uit te voeren vanaf een opdracht prompt:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. Open de REGI ster-editor op *Server1* en ga naar de volgende sleutel:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Controleer alle waarden die worden weer gegeven in de subsleutel VssAccessControl. Als een vermelding een waarde heeft van 0 en een andere VSS Writer wordt uitgevoerd onder de referenties van het gekoppelde account, wijzigt u de waarde in 1.

1. Installeer een beveiligings agent op *server2*.

   > [!WARNING]
   > U kunt alleen web-front-endservers overschakelen als beide servers zich in hetzelfde domein bevinden.

1. Wijzig op *server2* bij een opdracht prompt de Directory in `_MABS installation location_\bin\` en voer **ConfigureSharepoint** uit. Zie [Configure Backup](#configure-backup)(Engelstalig) voor meer informatie over ConfigureSharePoint.

1. Selecteer de beveiligings groep waartoe de server farm behoort en selecteer vervolgens **beveiligings groep wijzigen**.

1. Vouw in de wizard groep wijzigen op de pagina **groeps leden selecteren** *server2* uit en selecteer de server farm en voltooi vervolgens de wizard.

   Er wordt een consistentie controle gestart.

1. Als u stap 6 hebt uitgevoerd, kunt u nu het volume uit de beveiligings groep verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [back-upbestanden en het toepassings](backup-mabs-files-applications-azure-stack.md) artikel.
* Zie het artikel [back-upSQL Server op Azure stack](backup-mabs-sql-azure-stack.md) .
