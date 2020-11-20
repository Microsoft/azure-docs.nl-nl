---
title: StorSimple 8000-serie als back-updoel met Veeam | Microsoft Docs
description: Meer informatie over de configuratie van de StorSimple-back-updoel met Veeam en aanbevolen procedures voor het integreren van beide oplossingen.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: matd
ms.openlocfilehash: bf28265de2b297dade545695c9369b8074eeb72c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962549"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple als een back-updoel met Veeam

## <a name="overview"></a>Overzicht

Azure StorSimple is een Hybrid Cloud Storage oplossing van micro soft. StorSimple is van toepassing op de complexiteit van exponentiële gegevens groei door gebruik te maken van een Azure Storage-account als een uitbrei ding van de on-premises oplossing en het automatisch belagen van gegevens in on-premises opslag en Cloud opslag.

In dit artikel bespreken we de StorSimple-integratie met Veeam en aanbevolen procedures voor het integreren van beide oplossingen. We maken ook aanbevelingen voor het instellen van Veeam om optimaal te kunnen integreren met StorSimple. We stellen de aanbevolen procedures, back-uparchitects en beheerders uit voor de beste manier om Veeam in te stellen om te voldoen aan de afzonderlijke vereisten voor back-ups en Service Level Agreements (Sla's).

Hoewel we de configuratie stappen en belang rijke concepten illustreren, is dit artikel in geen stapsgewijze configuratie of installatie handleiding. We gaan ervan uit dat de basis onderdelen en infra structuur in werk orde zijn en klaar zijn voor de ondersteuning van de concepten die we beschrijven.

### <a name="who-should-read-this"></a>Wie moet dit lezen?

De informatie in dit artikel is het nuttigst voor back-upbeheerders, opslag beheerders en opslag architecten met kennis van opslag, Windows Server 2012 R2, Ethernet, Cloud Services en Veeam.

### <a name="supported-versions"></a>Ondersteunde versies

-   Veeam 9 en hoger
-   [StorSimple update 3 en hoger](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Waarom StorSimple als een back-updoel?

StorSimple is een goede keuze voor een back-updoel omdat:

-   Het biedt standaard lokale opslag voor back-uptoepassingen die als snelle back-upbestemming kunnen worden gebruikt, zonder dat u wijzigingen hoeft aan te brengen. U kunt StorSimple ook gebruiken voor het snel herstellen van recente back-ups.
-   De Cloud Tiering is naadloos geïntegreerd met een Azure Cloud Storage-account voor het gebruik van rendabele Azure Storage.
-   Het biedt automatisch externe opslag voor herstel na nood gevallen.


## <a name="key-concepts"></a>Belangrijkste concepten

Net als bij elke opslag oplossing is een zorgvuldige evaluatie van de opslag prestaties, de Sla's, de wijzigings snelheid en capaciteits groei vereist van essentieel belang. Het belangrijkste is dat door een Cloud-laag te introduceren, uw toegangs tijden en door Voer voor de Cloud een fundamentele rol spelen in de mogelijkheid van StorSimple om de taak uit te voeren.

StorSimple is ontworpen om opslag te bieden aan toepassingen die worden uitgevoerd op een goed gedefinieerde werkset gegevens (warme gegevens). In dit model wordt de werkset gegevens opgeslagen op de lokale lagen en wordt de resterende vrije/gekoelde/gearchiveerde set gegevens gelaagd voor de Cloud. Dit model wordt weer gegeven in de volgende afbeelding. De bijna vlakke groene lijn staat voor de gegevens die zijn opgeslagen in de lokale lagen van het StorSimple-apparaat. De rode lijn staat voor de totale hoeveelheid gegevens die is opgeslagen in de StorSimple-oplossing in alle lagen. De ruimte tussen de platte groene lijn en de exponentiële rode curve vertegenwoordigt de totale hoeveelheid gegevens die in de Cloud is opgeslagen.

**StorSimple lagen** 
 ![ Diagram van StorSimple-lagen](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Met deze architectuur zult u merken dat StorSimple ideaal is voor gebruik als back-updoel. U kunt StorSimple gebruiken voor het volgende:

-   Voer de meest frequente herstel bewerkingen uit vanuit de lokale werkset gegevens.
-   Gebruik de Cloud voor externe herstel na nood gevallen en oudere gegevens, waarbij herstel bewerkingen minder vaak worden gebruikt.

## <a name="storsimple-benefits"></a>StorSimple-voor delen

StorSimple biedt een on-premises oplossing die naadloos kan worden geïntegreerd met Microsoft Azure, door gebruik te maken van naadloze toegang tot on-premises en Cloud opslag.

StorSimple maakt gebruik van automatische lagen tussen het on-premises apparaat, dat SSD-en Serial Attached SCSI (SAS)-opslag heeft en Azure Storage. Met automatische lagen blijven de lokale gegevens lokaal geopend, op de SSD-en SAS-laag. Er worden zelden gebruikte gegevens naar Azure Storage verplaatst.

StorSimple biedt de volgende voor delen:

-   Unieke ontdubbeling-en compressie algoritmen die gebruikmaken van de cloud om ongekende ontdubbelings niveaus te creëren
-   Hoge beschikbaarheid
-   Geo-replicatie door gebruik te maken van geo-replicatie van Azure
-   Azure-integratie
-   Gegevens versleuteling in de Cloud
-   Verbeterd herstel na nood gevallen en naleving

Hoewel StorSimple twee belang rijke implementatie scenario's (primair back-updoel en secundair back-updoel) biedt, is het een gewoon, blok opslag apparaat. StorSimple alle compressie en ontdubbeling. Er worden naadloos gegevens verzonden en opgehaald tussen de Cloud en de toepassing en het bestands systeem.

Voor meer informatie over StorSimple raadpleegt u [StorSimple 8000 Series: hybride Cloud Storage-oplossing](storsimple-overview.md). U kunt ook de specificaties van de [technische StorSimple 8000-serie](./storsimple-8000-technical-specifications-and-compliance.md)bekijken.

> [!IMPORTANT]
> Het gebruik van een StorSimple-apparaat als back-updoel wordt alleen ondersteund voor StorSimple 8000-update 3 en hoger.

## <a name="architecture-overview"></a>Overzicht van de architectuur

In de volgende tabellen ziet u de richt lijnen voor het model naar architectuur van het apparaat.

**StorSimple-capaciteit voor lokale en Cloud opslag**

| Opslagcapaciteit | 8100 | 8600 |
|---|---|---|
| Lokale opslagcapaciteit | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capaciteit van de Cloud opslag | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Bij de opslag grootte wordt ervan uitgegaan dat er geen ontdubbeling of compressie wordt toegepast.

**StorSimple-capaciteit voor primaire en secundaire back-ups**

| Back-upscenario  | Lokale opslagcapaciteit  | Capaciteit van de Cloud opslag  |
|---|---|---|
| Primaire back-up  | Recente back-ups die zijn opgeslagen op de lokale opslag voor snel herstel om te voldoen aan Recovery Point Objective (RPO) | Back-upgeschiedenis (RPO) past in de Cloud capaciteit |
| Secundaire back-up | Secundaire kopie van back-upgegevens kan worden opgeslagen in de Cloud capaciteit  | N.v.t.  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple als primair back-updoel

In dit scenario worden StorSimple-volumes weer gegeven aan de back-uptoepassing als de enige opslag plaats voor back-ups. In de volgende afbeelding ziet u een oplossings architectuur waarin alle back-ups StorSimple gelaagde volumes gebruiken voor back-ups en herstel bewerkingen.

![StorSimple als primair diagram van een logisch back-updoel](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logische doel-back-upstappen

1.  De back-upserver neemt contact op met de doel back-upagent en de back-upagent verzendt gegevens naar de back-upserver.
2.  De back-upserver schrijft gegevens naar de StorSimple gelaagde volumes.
3.  De back-upserver werkt de catalogus database bij en voltooit vervolgens de back-uptaak.
4.  Met een momentopname script wordt de StorSimple-Cloud momentopname beheerder geactiveerd (starten of verwijderen).
5.  De back-upserver verwijdert verlopen back-ups op basis van een Bewaar beleid.

### <a name="primary-target-restore-logical-steps"></a>Primaire doel herstellen logische stappen

1.  De back-upserver begint met het terugzetten van de juiste gegevens uit de opslag opslagplaats.
2.  De back-upagent ontvangt de gegevens van de back-upserver.
3.  De back-upserver voltooit de herstel taak.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple als een secundair back-updoel

In dit scenario worden StorSimple-volumes voornamelijk gebruikt voor lange termijn retentie of archivering.

In de volgende afbeelding ziet u een architectuur waarin de eerste back-ups worden gemaakt en gericht op een volume met hoge prestaties. Deze back-ups worden gekopieerd en gearchiveerd naar een StorSimple gelaagd volume volgens een ingesteld schema.

Het is belang rijk om uw volume met hoge prestaties te verg Roten, zodat het de capaciteit en prestaties van uw Bewaar beleid kan afhandelen.

![StorSimple als een logisch diagram van een secundair back-updoel](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logische back-upprocedure voor secundaire doel

1.  De back-upserver neemt contact op met de doel back-upagent en de back-upagent verzendt gegevens naar de back-upserver.
2.  De back-upserver schrijft gegevens naar opslag met hoge prestaties.
3.  De back-upserver werkt de catalogus database bij en voltooit vervolgens de back-uptaak.
4.  De back-upserver kopieert back-ups naar StorSimple op basis van een Bewaar beleid.
5.  Met een momentopname script wordt de StorSimple-Cloud momentopname beheerder geactiveerd (starten of verwijderen).
6.  De back-upserver verwijdert verlopen back-ups op basis van een Bewaar beleid.

### <a name="secondary-target-restore-logical-steps"></a>Logische stappen voor het herstellen van secundaire doelen

1.  De back-upserver begint met het terugzetten van de juiste gegevens uit de opslag opslagplaats.
2.  De back-upagent ontvangt de gegevens van de back-upserver.
3.  De back-upserver voltooit de herstel taak.

## <a name="deploy-the-solution"></a>De oplossing implementeren

Voor het implementeren van de oplossing zijn drie stappen vereist:

1. Bereid de netwerk infrastructuur voor.
2. Implementeer uw StorSimple-apparaat als een back-updoel.
3. Veeam implementeren.

Elke stap wordt gedetailleerd beschreven in de volgende secties.

### <a name="set-up-the-network"></a>Het netwerk instellen

Omdat StorSimple is een oplossing die is geïntegreerd met de Azure-Cloud, is voor StorSimple een actieve en werkende verbinding met de Azure-Cloud vereist. Deze verbinding wordt gebruikt voor bewerkingen als Cloud momentopnamen, gegevens beheer en meta gegevens overdracht, en om oudere, minder toegankelijke gegevens te verlaagen naar Azure-Cloud opslag.

Als u de oplossing optimaal wilt uitvoeren, raden we u aan deze aanbevolen procedures voor netwerken uit te voeren:

-   De koppeling die uw StorSimple-laag aan Azure verbindt, moet voldoen aan uw bandbreedte vereisten. U kunt dit doen door het benodigde Quality of Service (QoS)-niveau toe te passen op de switches van uw infra structuur, zodat deze overeenkomen met uw RPO-en Recovery Time objectief (RTO) Sla's.
-   Maximum aantal toegangs latenties voor Azure Blob-opslag moet ongeveer 80 MS zijn.

### <a name="deploy-storsimple"></a>StorSimple implementeren

Zie [uw on-premises StorSimple-apparaat implementeren](./storsimple-8000-deployment-walkthrough-u2.md)voor stapsgewijze instructies voor de implementatie van StorSimple.

### <a name="deploy-veeam"></a>Veeam implementeren

Zie voor aanbevolen procedures voor de installatie van Veeam [Veeam back-up & aanbevolen procedures voor replicatie](https://bp.veeam.expert/)en lees de gebruikers handleiding op [Veeam Help Center (technische documentatie)](https://www.veeam.com/documentation-guides-datasheets.html).

## <a name="set-up-the-solution"></a>De oplossing instellen

In deze sectie ziet u enkele configuratie voorbeelden. De volgende voor beelden en aanbevelingen illustreren de meest eenvoudige en fundamentele implementatie. Deze implementatie is mogelijk niet rechtstreeks van toepassing op uw specifieke back-upvereisten.

### <a name="set-up-storsimple"></a>StorSimple instellen

| StorSimple-implementatie taken  | Aanvullende opmerkingen |
|---|---|
| Implementeer uw on-premises StorSimple-apparaat. | Ondersteunde versies: update 3 en hoger. |
| Schakel het doel van de back-up in. | Gebruik deze opdrachten om de doel modus voor back-up in of uit te scha kelen en de status op te halen. Zie [extern verbinding maken met een StorSimple-apparaat](./storsimple-8000-remote-connect.md)voor meer informatie.</br> De back-upmodus inschakelen: `Set-HCSBackupApplianceMode -enable` . </br> De back-upmodus uitschakelen: `Set-HCSBackupApplianceMode -disable` . </br> De huidige status van de instellingen van de back-upmodus ophalen: `Get-HCSBackupApplianceMode` . |
| Maak een algemene volume container voor uw volume waarin de back-upgegevens worden opgeslagen. Alle gegevens in een volume container worden ontdubbeld. | StorSimple volume containers definiëren ontdubbeling domeinen.  |
| Maak StorSimple-volumes. | Maak volumes met grootten zo dicht mogelijk bij het verwachte gebruik, omdat de volume grootte van invloed is op de duur van de Cloud momentopname. Meer informatie over het aanpassen van de grootte van een volume vindt u in het [Bewaar beleid](#retention-policies).</br> </br> Gebruik StorSimple gelaagde volumes en schakel het selectie vakje **dit volume gebruiken voor minder vaak gebruikte gegevens archivering** in. </br> Het gebruik van alleen lokaal vastgemaakte volumes wordt niet ondersteund. |
| Maak een uniek StorSimple-back-upbeleid voor alle back-updoel volumes. | Een StorSimple-back-upbeleid definieert de volume consistentie groep. |
| Het schema uitschakelen wanneer de moment opnamen verlopen. | Moment opnamen worden geactiveerd als een verwerkings bewerking. |

### <a name="set-up-the-host-backup-server-storage"></a>De opslag van de back-upserver van de host instellen

Stel de opslag van de back-upserver van de host in volgens deze richt lijnen:  

- Gebruik geen spanned volumes (gemaakt door Windows-schijf beheer). Spanned volumes worden niet ondersteund.
- Format teer uw volumes met NTFS met 64-KB Allocation Unit Size.
- Wijs de StorSimple-volumes rechtstreeks toe aan de Veeam-server.
    - Gebruik iSCSI voor fysieke servers.


## <a name="best-practices-for-storsimple-and-veeam"></a>Aanbevolen procedures voor StorSimple en Veeam

Stel uw oplossing in aan de hand van de richt lijnen in de volgende secties.

### <a name="operating-system-best-practices"></a>Aanbevolen procedures voor het besturings systeem

- Schakel Windows Server-versleuteling en ontdubbeling uit voor het NTFS-bestands systeem.
- Schakel Windows Server-defragmentatie uit op de StorSimple-volumes.
- Schakel Windows Server-indexering uit op de StorSimple-volumes.
- Voer een antivirus scan uit op de bronhost (niet op de StorSimple-volumes).
- Schakel het standaard [onderhoud van Windows Server](/windows/win32/w8cookbook/automatic-maintenance) uit in taak beheer. Doe dit op een van de volgende manieren:
  - De onderhouds Configurator uitschakelen in Windows taak planner.
  - Down load [PsExec](/sysinternals/downloads/psexec) van Windows Sysinternals. Nadat u PsExec hebt gedownload, voert u Windows Power shell als beheerder uit en typt u:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Best practices voor StorSimple

-   Zorg ervoor dat het StorSimple-apparaat is bijgewerkt naar [Update 3 of hoger](./index.yml).
-   Isoleer iSCSI-en Cloud verkeer. Gebruik toegewezen iSCSI-verbindingen voor verkeer tussen StorSimple en de back-upserver.
-   Zorg ervoor dat uw StorSimple-apparaat een specifiek back-updoel is. Gemengde werk belastingen worden niet ondersteund, omdat deze van invloed zijn op uw RTO en RPO.

### <a name="veeam-best-practices"></a>Best practices voor Veeam

-   De Veeam-data base moet lokaal zijn voor de server en zich niet bevinden op een StorSimple-volume.
-   Voor herstel na nood gevallen maakt u een back-up van de Veeam-Data Base op een StorSimple-volume.
-   Veeam volledige en incrementele back-ups voor deze oplossing worden ondersteund. U wordt aangeraden geen synthetische en differentiële back-ups te gebruiken.
-   Back-upgegevens bestanden mogen alleen de gegevens voor een specifieke taak bevatten. U kunt bijvoorbeeld geen media toevoegen tussen verschillende taken.
-   Schakel de taak verificatie uit. Indien nodig moet de verificatie worden gepland na de meest recente back-uptaak. Het is belang rijk om te begrijpen dat deze taak van invloed is op uw back-upvenster.
-   Vooraf toewijzen van media inschakelen.
-   Zorg ervoor dat parallelle verwerking is ingeschakeld.
-   Schakel compressie uit.
-   Schakel ontdubbeling uit voor de back-uptaak.
-   Stel optimalisatie in op **LAN-doel**.
-   Schakel de optie **actieve volledige back-up maken** (elke 2 weken) in.
-   Stel in de back-upopslagplaats **gebruik per VM-back-upbestanden** in.
-   Stel **meerdere upload stromen per taak gebruiken** in op **8** (Maxi maal 16 is toegestaan). Pas dit aantal aan of uit op basis van het CPU-gebruik op het StorSimple-apparaat.

## <a name="retention-policies"></a>Bewaarbeleid

Een van de meest voorkomende beleids typen voor het bewaren van back-ups is een beleid voor groot vader, vader en zoon (GFS). In een GFS-beleid wordt dagelijks een incrementele back-up uitgevoerd en worden volledige back-ups wekelijks en maandelijks uitgevoerd. Dit beleid is van toepassing op zes StorSimple gelaagde volumes: een volume bevat de wekelijkse, maandelijkse en jaarlijkse volledige back-ups. de andere vijf volumes slaan dagelijkse incrementele back-ups op.

In het volgende voor beeld wordt een GFS-rotatie gebruikt. In het voor beeld wordt uitgegaan van het volgende:

-   Niet-ontdubbelde of gecomprimeerde gegevens worden gebruikt.
-   Volledige back-ups zijn één TiB.
-   Dagelijkse incrementele back-ups zijn 500 GiB.
-   Vier wekelijkse back-ups worden gedurende een maand bewaard.
-   Twaalf maandelijkse back-ups worden gedurende een jaar bewaard.
-   Een jaarlijkse back-up wordt tien jaar bewaard.

Maak op basis van de voor gaande hypo theses een gelaagd volume van 26 TiB StorSimple voor de maandelijkse en jaarlijkse volledige back-ups. Maak een gelaagd volume van 5 TiB voor elk van de incrementele dagelijkse back-ups.

| Bewaar periode van het back-uptype | Grootte (TiB) | GFS vermenigvuldiger\* | Totale capaciteit (TiB)  |
|---|---|---|---|
| Wekelijks volledig | 1 | 4  | 4 |
| Dagelijks incrementeel | 0,5 | 20 (cyclussen van gelijk aantal weken per maand) | 12 (2 voor het extra quotum) |
| Volledig maandelijks | 1 | 12 | 12 |
| Jaarlijks volledig | 1  | 10 | 10 |
| GFS-vereiste |   | 38 |   |
| Extra quotum  | 4  |   | 42 totale GFS-vereiste  |

\* De GFS vermenigvuldiger is het aantal kopieën dat u moet beveiligen en bewaren om te voldoen aan de vereisten voor het back-upbeleid.

## <a name="set-up-veeam-storage"></a>Veeam-opslag instellen

### <a name="to-set-up-veeam-storage"></a>Veeam-opslag instellen

1.  Ga in de Veeam-back-up en de replicatie console naar **back-upinfrastructuur** in de **bibliotheek hulpprogram ma's**. Klik met de rechter muisknop op **back-Upopslagplaatsen** en selecteer vervolgens **back-upopslagplaats toevoegen**.

    ![Scherm opname van de Veeam-beheer console en markeert de optie back-upopslagplaats toevoegen.](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Voer in het dialoog venster **nieuwe back-upopslagplaats** een naam en een beschrijving in voor de opslag plaats. Selecteer **Next**.

    ![Veeam-beheer console, naam en beschrijvings pagina](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Selecteer **micro soft Windows Server** voor het type. Selecteer de Veeam-server. Selecteer **Next**.

    ![Veeam-beheer console, type back-upopslagplaats selecteren](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Als u een **locatie** wilt opgeven, bladert en selecteert u het volume. Selecteer het selectie vakje **maximum aantal gelijktijdige taken beperken tot:** en stel de waarde in op **4**. Dit zorgt ervoor dat er slechts vier virtuele schijven gelijktijdig worden verwerkt terwijl elke virtuele machine (VM) wordt verwerkt. Selecteer de knop **Geavanceerd** .

    ![Veeam-beheer console, volume selecteren](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Schakel in het dialoog venster **instellingen voor opslag compatibiliteit** het selectie vakje **back-upbestanden per VM gebruiken** in.

    ![Veeam-beheer console, instellingen voor opslag compatibiliteit](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Schakel in het dialoog venster **nieuwe back-upopslagplaats** het selectie vakje **vPower NFS-service inschakelen op het koppel server (aanbevolen)** in. Selecteer **Next**.

    ![Scherm opname van de Veeam-beheer console waar u een nieuwe back-upopslagplaats kunt toevoegen.](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Controleer de instellingen en selecteer vervolgens **volgende**.

    ![Veeam-beheer console, pagina Back-upopslagplaats](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Een opslag plaats wordt toegevoegd aan de Veeam-server.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>StorSimple als primair back-updoel instellen

> [!IMPORTANT]
> Het terugzetten van gegevens vanuit een back-up die is getiert in de Cloud, vindt plaats op Cloud snelheden.

In de volgende afbeelding ziet u de toewijzing van een typisch volume aan een back-uptaak. In dit geval worden alle wekelijkse back-ups met de volledige zaterdag-schijf en de incrementele back-ups toegewezen aan Monday-Friday incrementele schijven. Alle back-ups en herstel bewerkingen zijn afkomstig uit een StorSimple gelaagd volume.

![Logisch diagram van configuratie van primaire back-updoel](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Voor beeld van StorSimple als een primair GFS schema voor het maken van een doel

Hier volgt een voor beeld van een GFS-rotatie schema voor vier weken, maandelijks en jaarlijks:

| Type frequentie/back-up | Volledig | Incrementeel (dagen 1-5)  |   
|---|---|---|
| Wekelijks (weken 1-4) | Saturday | Monday-Friday |
| Maandelijks  | Saturday  |   |
| Jaar | Saturday  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple-volumes toewijzen aan een Veeam-back-uptaak

Maak voor primair scenario voor back-updoel een dagelijkse taak met uw primaire Veeam StorSimple-volume. Voor een secundair scenario voor het maken van een back-updoel maakt u een dagelijkse taak met behulp van DAS (Direct Attached Storage), NAS (Network Attached Storage) of een JBOD-opslag.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple-volumes toewijzen aan een Veeam-back-uptaak

1.  Selecteer **back-up & replicatie** in de Veeam-back-up en replicatie console. Klik met de rechter muisknop op **back-up** en selecteer vervolgens **VMware** of **Hyper-V**, afhankelijk van uw omgeving.

    ![Veeam-beheer console, nieuwe back-uptaak](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Voer in het dialoog venster **nieuwe back-uptaak** een naam en een beschrijving in voor de dagelijkse back-uptaak.

    ![Scherm opname van de Veeam-beheer console waar u de naam en beschrijving toevoegt.](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Selecteer een virtuele machine waarvan u een back-up wilt maken.

    ![Scherm opname van de Veeam-beheer console waar u de virtuele machine selecteert.](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Selecteer de gewenste waarden voor **back-upproxy** en **back-upopslagplaats**. Selecteer een waarde voor **herstel punten die op schijf** moet worden bewaard volgens de RPO-en RTO definities voor uw omgeving op lokaal gekoppelde opslag. Selecteer **Geavanceerd**.

    ![Veeam-beheer console, pagina nieuwe back-uptaak](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Selecteer in het dialoog venster **Geavanceerde instellingen** op het tabblad **back-up** de optie **Incrementeel**. Zorg ervoor dat het selectie vakje **synthetische volledige back-ups maken regel matig** wordt uitgeschakeld. Schakel het selectie vakje **periodiek actieve volledige back-ups maken** in. Schakel onder **actieve volledige back-up** het selectie vakje **wekelijks op geselecteerde dagen** in voor zaterdag.

    ![Scherm opname van de Veeam-beheer console, met name de nieuwe pagina Geavanceerde instellingen voor de back-uptaak](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Controleer op het tabblad **opslag** of het selectie vakje **inline gegevensontdubbeling inschakelen** is uitgeschakeld. Schakel het selectie vakje **wissel bestand blokken uitsluiten** in en schakel het selectie vakje **Verwijder verwijderde bestands blokken** in. Stel **compressie niveau** in op **geen**. Voor evenwichtige prestaties en ontdubbeling stelt u de **opslag optimalisatie** in op het **LAN-doel**. Selecteer **OK**.

    ![Veeam-beheer console, nieuwe pagina Geavanceerde instellingen voor back-uptaak](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Zie [gegevens compressie en ontdubbeling](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html)voor meer informatie over instellingen voor ontdubbeling en compressie van Veeam.

7.  In het dialoog venster **back-uptaak bewerken** kunt u het selectie vakje **toepassings bewuste verwerking inschakelen** selecteren (optioneel).

    ![Veeam-beheer console, nieuwe pagina Back-uptaak gast verwerking](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Stel in dat het schema één keer per dag wordt uitgevoerd, per keer dat u kunt opgeven.

    ![Veeam-beheer console, nieuwe plannings pagina voor back-uptaken](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>StorSimple instellen als een secundair back-updoel

> [!NOTE]
> Gegevens die worden teruggezet vanuit een back-up die is getiert in de Cloud, treden op in de Cloud.

In dit model moet u een opslag medium hebben (met uitzonde ring van StorSimple) om als tijdelijke cache te dienen. U kunt bijvoorbeeld een RAID-volume (Redundant Array of Independent Disks) gebruiken om ruimte, invoer/uitvoer (I/O) en band breedte aan te passen. U kunt het beste RAID 5, 50 en 10 gebruiken.

In de volgende afbeelding ziet u een typische lokale Bewaar periode voor retentie (naar de server) en volumes voor lange termijn retentie archieven. In dit scenario worden alle back-ups uitgevoerd op het lokale RAID-volume (naar de server). Deze back-ups worden periodiek gedupliceerd en gearchiveerd naar een archief volume. Het is belang rijk dat u de grootte van uw lokale (op het server) RAID-volume kunt aanpassen, zodat deze de capaciteit van de korte-termijn inhoud en prestatie vereisten kan verwerken.

![Logisch diagram van StorSimple als secundair back-updoel](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>GFS-voor beeld van StorSimple als secundaire back-updoel

In de volgende tabel ziet u hoe u back-ups kunt instellen om uit te voeren op de lokale en StorSimple-schijven. Het bevat afzonderlijke en totale capaciteits vereisten.

| Type back-up en retentie | Geconfigureerde opslag | Grootte (TiB) | GFS vermenigvuldiger | Totale capaciteit \* (Tib) |
|---|---|---|---|---|
| Week 1 (volledig en incrementeel) |Lokale schijf (korte termijn)| 1 | 1 | 1 |
| StorSimple weken 2-4 |StorSimple-schijf (lange termijn) | 1 | 4 | 4 |
| Volledig maandelijks |StorSimple-schijf (lange termijn) | 1 | 12 | 12 |
| Jaarlijks volledig |StorSimple-schijf (lange termijn) | 1 | 1 | 1 |
|Vereiste voor grootte van GFS-volumes |  |  |  | 18,0|

\* De totale capaciteit omvat 17 TiB van StorSimple schijven en 1 TiB van het lokale RAID-volume.


### <a name="gfs-example-schedule"></a>Voorbeeld schema voor GFS

GFS draaiing wekelijks, maandelijks en jaarlijks plannen

| Week | Volledig | Incrementele dag 1 | Incrementele dag 2 | Incrementele dag 3 | Incrementele dag 4 | Incrementele dag 5 |
|---|---|---|---|---|---|---|
| Week 1 | Lokaal RAID-volume  | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume |
| Week 2 | StorSimple weken 2-4 |   |   |   |   |   |
| Week 3 | StorSimple weken 2-4 |   |   |   |   |   |
| Week 4 | StorSimple weken 2-4 |   |   |   |   |   |
| Maandelijks | StorSimple maandelijks |   |   |   |   |   |
| Jaar | Jaarlijks StorSimple  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple-volumes toewijzen aan een Veeam-Kopieer taak

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple-volumes toewijzen aan een Veeam-Kopieer taak

1.  Selecteer **back-up & replicatie** in de Veeam-back-up en replicatie console. Klik met de rechter muisknop op **back-up** en selecteer vervolgens **VMware** of **Hyper-V**, afhankelijk van uw omgeving.

    ![Scherm opname van de Veeam-beheer console met de VMware-en Hyper-V-opties die u kunt selecteren.](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Voer in het dialoog venster **nieuwe back-upkopie taak** een naam en een beschrijving in voor de taak.

    ![Scherm opname van de Veeam-beheer console waarin u de naam en beschrijving voor de taak opgeeft.](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Selecteer de Vm's die u wilt verwerken. Selecteer een back-up en selecteer vervolgens de dagelijkse back-up die u eerder hebt gemaakt.

    ![Veeam-beheer console, nieuwe pagina back-upkopie taak](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Sluit zo nodig objecten uit van de back-uptaak.

5.  Selecteer uw back-upopslagplaats en stel een waarde **in voor de herstel punten die u wilt laten staan**. Zorg ervoor dat u het selectie vakje **de volgende herstel punten voor archiverings doeleinden gebruiken** inschakelt. Definieer de back-upfrequentie en selecteer vervolgens **Geavanceerd**.

    ![Scherm afbeelding die laat zien waar u de back-upfrequentie definieert.](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Geef de volgende geavanceerde instellingen op:

    * Schakel op het tabblad **onderhoud** de beveiliging tegen beschadiging van opslag niveau uit.

    ![Scherm opname van het tabblad Onderhoud in de Veeam-beheer console.](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Zorg ervoor dat ontdubbeling en compressie is uitgeschakeld op het tabblad **opslag** .

    ![Veeam-beheer console, nieuwe pagina Geavanceerde instellingen voor back-upkopie taak](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Geef op dat de gegevens overdracht direct is.

8.  Definieer het venster schema voor back-ups volgens uw behoeften en voltooi vervolgens de wizard.

Zie [back-upkopie taken maken](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html)voor meer informatie.

## <a name="storsimple-cloud-snapshots"></a>StorSimple-Cloud momentopnamen

Met StorSimple-Cloud momentopnamen worden de gegevens beschermd die zich op uw StorSimple-apparaat bevinden. Het maken van een Cloud momentopname is gelijk aan het verzenden van lokale back-uptapes naar een externe locatie. Als u geografisch redundante opslag van Azure gebruikt, is het maken van een moment opname van de Cloud gelijk aan het verzenden van back-uptapes naar meerdere sites. Als u een apparaat na een nood geval wilt herstellen, kunt u een ander StorSimple-apparaat online zetten en een failover uitvoeren. Na de failover hebt u toegang tot de gegevens (tegen Cloud snelheden) van de meest recente Cloud momentopname.

In de volgende sectie wordt beschreven hoe u een kort script maakt voor het starten en verwijderen van StorSimple-Cloud momentopnamen tijdens een back-up na de verwerking.

> [!NOTE]
> Moment opnamen die hand matig of via een programma worden gemaakt, volgen niet het verloop beleid van de StorSimple-moment opname. Deze moment opnamen moeten hand matig of programmatisch worden verwijderd.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Cloud momentopnamen starten en verwijderen met behulp van een script

> [!NOTE]
> Evalueer zorgvuldig de gevolgen voor naleving en gegevens retentie voordat u een StorSimple-moment opname verwijdert. Zie de Veeam-documentatie voor meer informatie over het uitvoeren van een script dat volgt op back-ups.


### <a name="backup-lifecycle"></a>Back-uplevenscyclus

![Diagram van back-uplevenscyclus](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Vereisten

-   De server waarop het script wordt uitgevoerd, moet toegang hebben tot Azure-cloud resources.
-   Het gebruikers account moet de juiste machtigingen hebben.
-   Een StorSimple-back-upbeleid met de gekoppelde StorSimple-volumes moet worden ingesteld, maar niet ingeschakeld.
-   U hebt de StorSimple-resource naam, de registratie sleutel, de apparaatnaam en het back-upbeleid nodig.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Een Cloud momentopname starten of verwijderen

1. [Installeer Azure PowerShell](/powershell/azure/).
2. Down load en Setup [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) Power shell-script.
3. Op de server waarop het script wordt uitgevoerd, voert u Power shell uit als beheerder. Zorg ervoor dat u het script uitvoert `-WhatIf $true` om te zien welke wijzigingen door het script worden aangebracht. Zodra de validatie is voltooid, geeft u door `-WhatIf $false` . Voer de onderstaande opdracht uit:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Als u het script wilt toevoegen aan uw back-uptaak, bewerkt u de geavanceerde opties van uw Veeam-taak.

    ![Tabblad scripts van geavanceerde instellingen voor Veeam back-up](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

U wordt aangeraden om uw back-upbeleid voor StorSimple-Cloud momentopname als een script aan het einde van uw dagelijkse back-uptaak uit te voeren. Voor meer informatie over het maken van een back-up en het herstellen van uw back-uptoepassingsproces om u te helpen bij uw RPO en RTO, kunt u contact opnemen met uw back-uparchitect.

## <a name="storsimple-as-a-restore-source"></a>StorSimple als een herstel bron

Herstellen vanaf een StorSimple-apparaat werkt zoals herstellen vanuit elk apparaat voor blok opslag. Het terugzetten van gegevens die in de cloud worden getierd, vindt plaats tegen Cloud snelheden. Voor lokale gegevens worden herstel bewerkingen uitgevoerd op de snelheid van de lokale schijf van het apparaat.

Met Veeam krijgt u snel en gedetailleerd herstel op bestands niveau via StorSimple via de ingebouwde Explorer-weer gaven in de Veeam-console. Gebruik de Veeam Explorers om afzonderlijke items, zoals e-mail berichten, Active Directory objecten en share point-items van back-ups, te herstellen. U kunt de herstel bewerking uitvoeren zonder on-premises VM-onderbreking. U kunt ook herstel naar een bepaald tijdstip voor Azure SQL Database en Oracle-data bases. Veeam en StorSimple maken het proces van herstel op item niveau van Azure snel en eenvoudig. Zie de Veeam-documentatie voor meer informatie over het uitvoeren van een herstel bewerking:

- Voor [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Voor [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Voor [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Voor [share point](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Voor [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple-failover en herstel na nood gevallen

> [!NOTE]
> StorSimple Cloud Appliance wordt niet ondersteund als doel voor het terugzetten van Back-updoelen.

Een nood geval kan worden veroorzaakt door diverse factoren. De volgende tabel geeft een lijst van veelvoorkomende scenario's voor herstel na nood gevallen.

| Scenario | Impact | Herstellen | Opmerkingen |
|---|---|---|---|
| StorSimple-apparaat is mislukt | Back-up-en herstel bewerkingen worden onderbroken. | Vervang het apparaat dat is mislukt en voer een [StorSimple-failover en nood herstel](./storsimple-8000-device-failover-disaster-recovery.md)uit. | Als u na het herstel van het apparaat een herstel bewerking moet uitvoeren, worden volledige gegevens verzamelingen opgehaald uit de Cloud naar het nieuwe apparaat. Alle bewerkingen bevinden zich in de Cloud snelheid. Het proces voor het opnieuw scannen van indexen en catalogussen kan ertoe leiden dat alle back-upsets worden gescand en opgehaald van de Cloud laag naar de laag van het lokale apparaat. Dit kan een tijdrovend proces zijn. |
| Veeam-server fout | Back-up-en herstel bewerkingen worden onderbroken. | Bouw de back-upserver opnieuw op en voer data base terugzetten uit zoals beschreven in [Veeam Help Center (technische documentatie)](https://www.veeam.com/documentation-guides-datasheets.html).  | U moet de Veeam-server opnieuw samen stellen of herstellen op de site voor nood herstel. Zet de data base terug naar het meest recente punt. Als de herstelde Veeam-data base niet is gesynchroniseerd met uw meest recente back-uptaken, is indexeren en catalogiseren vereist. Dit proces voor het opnieuw scannen van index en catalogus kan ertoe leiden dat alle back-upsets worden gescand en van de Cloud laag worden opgehaald naar de laag van het lokale apparaat. Hierdoor is het veel tijdrovender. |
| Site fout die leidt tot verlies van zowel de back-upserver als de StorSimple | Back-up-en herstel bewerkingen worden onderbroken. | Herstel StorSimple eerst en herstel vervolgens Veeam. | Herstel StorSimple eerst en herstel vervolgens Veeam. Als u na het herstel van het apparaat een herstel bewerking moet uitvoeren, worden de volledige gegevens sets van de Cloud naar het nieuwe apparaat opgehaald. Alle bewerkingen bevinden zich in de Cloud snelheid. |


## <a name="references"></a>Verwijzingen

In dit artikel wordt verwezen naar de volgende documenten:

- [StorSimple Multipath I/O instellen](./storsimple-8000-configure-mpio-windows-server.md)
- [Opslag scenario's: Thin Provisioning](/windows-hardware/drivers/storage/thin-provisioning)
- [GPT-stations gebruiken](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Schaduw kopieën voor gedeelde mappen instellen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [herstellen van een back-upset](./storsimple-8000-restore-from-backup-set-u2.md).
- Meer informatie over het uitvoeren van een failover van een [apparaat en nood herstel](./storsimple-8000-device-failover-disaster-recovery.md).