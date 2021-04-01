---
title: StorSimple 8000-serie als back-updoel met NetBackup | Microsoft Docs
description: Meer informatie over de configuratie van de StorSimple-back-updoel met VERITAS NetBackup en best practices voor het integreren van beide oplossingen.
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
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 4f71cf82b675222836a73eec12d68bd8f62a5538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967275"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple als back-updoel met NetBackup

## <a name="overview"></a>Overzicht

Azure StorSimple is een Hybrid Cloud Storage oplossing van micro soft. StorSimple is van toepassing op de complexiteit van exponentiële gegevens groei door gebruik te maken van een Azure-opslag account als uitbrei ding van de on-premises oplossing, en om gegevens automatisch te trapsgewijs te lagen in opslag-en Cloud opslag.

In dit artikel bespreken we StorSimple-integratie met VERITAS NetBackup en best practices voor het integreren van beide oplossingen. We maken ook aanbevelingen voor het instellen van VERITAS NetBackup voor het beste integreren met StorSimple. We stellen de aanbevolen procedures, back-uparchitects en beheerders uit voor de beste manier om VERITAS NetBackup in te stellen om te voldoen aan de afzonderlijke vereisten voor back-ups en Service Level Agreements (Sla's).

Hoewel we de configuratie stappen en belang rijke concepten illustreren, is dit artikel in geen stapsgewijze configuratie of installatie handleiding. We gaan ervan uit dat de basis onderdelen en infra structuur in werk orde zijn en klaar zijn voor de ondersteuning van de concepten die we beschrijven.

### <a name="who-should-read-this"></a>Wie moet dit lezen?

De informatie in dit artikel is het nuttigst voor back-upbeheerders, opslag beheerders en opslag architecten met kennis van opslag, Windows Server 2012 R2, Ethernet, Cloud Services en VERITAS NetBackup.

### <a name="supported-versions"></a>Ondersteunde versies

-   NetBackup 7,7. x en latere versies
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
 ![ Diagram van StorSimple-lagen](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

| Opslagcapaciteit       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![StorSimple als primair diagram van een logisch back-updoel](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logische doel-back-upstappen

1.  De back-upserver neemt contact op met de doel back-upagent en de back-upagent verzendt gegevens naar de back-upserver.
2.  De back-upserver schrijft gegevens naar de StorSimple gelaagde volumes.
3.  De back-upserver werkt de catalogus database bij en voltooit vervolgens de back-uptaak.
4.  Met een momentopname script wordt de StorSimple snap shot Manager (starten of verwijderen) geactiveerd.
5.  De back-upserver verwijdert verlopen back-ups op basis van een Bewaar beleid.

### <a name="primary-target-restore-logical-steps"></a>Primaire doel herstellen logische stappen

1.  De back-upserver begint met het terugzetten van de juiste gegevens uit de opslag opslagplaats.
2.  De back-upagent ontvangt de gegevens van de back-upserver.
3.  De back-upserver voltooit de herstel taak.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple als een secundair back-updoel

In dit scenario worden StorSimple-volumes voornamelijk gebruikt voor lange termijn retentie of archivering.

In de volgende afbeelding ziet u een architectuur waarin de eerste back-ups worden gemaakt en gericht op een volume met hoge prestaties. Deze back-ups worden gekopieerd en gearchiveerd naar een StorSimple gelaagd volume volgens een ingesteld schema.

Het is belang rijk om uw volume met hoge prestaties te verg Roten, zodat het de capaciteit en prestaties van uw Bewaar beleid kan afhandelen.

![Diagram waarin een architectuur wordt weer gegeven waarin eerste back-ups worden gemaakt en een volume met hoge prestaties wordt hersteld.](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logische back-upprocedure voor secundaire doel

1.  De back-upserver neemt contact op met de doel back-upagent en de back-upagent verzendt gegevens naar de back-upserver.
2.  De back-upserver schrijft gegevens naar opslag met hoge prestaties.
3.  De back-upserver werkt de catalogus database bij en voltooit vervolgens de back-uptaak.
4.  De back-upserver kopieert back-ups naar StorSimple op basis van een Bewaar beleid.
5.  Met een momentopname script wordt de StorSimple snap shot Manager (starten of verwijderen) geactiveerd.
6.  De back-upserver verwijdert de verlopen back-ups op basis van een Bewaar beleid.

### <a name="secondary-target-restore-logical-steps"></a>Logische stappen voor het herstellen van secundaire doelen

1.  De back-upserver begint met het terugzetten van de juiste gegevens uit de opslag opslagplaats.
2.  De back-upagent ontvangt de gegevens van de back-upserver.
3.  De back-upserver voltooit de herstel taak.

## <a name="deploy-the-solution"></a>De oplossing implementeren

Voor het implementeren van deze oplossing zijn drie stappen vereist:
1. Bereid de netwerk infrastructuur voor.
2. Implementeer uw StorSimple-apparaat als een back-updoel.
3. Implementeer VERITAS NetBackup.

Elke stap wordt gedetailleerd beschreven in de volgende secties.

### <a name="set-up-the-network"></a>Het netwerk instellen

Omdat StorSimple een oplossing is die is geïntegreerd met de Azure-Cloud, is voor StorSimple een actieve en werkende verbinding met de Azure-Cloud vereist. Deze verbinding wordt gebruikt voor bewerkingen als Cloud momentopnamen, gegevens beheer en meta gegevens overdracht, en om oudere, minder toegankelijke gegevens te verlaagen naar Azure-Cloud opslag.

Als u de oplossing optimaal wilt uitvoeren, raden we u aan deze aanbevolen procedures voor netwerken uit te voeren:

-   De koppeling die de StorSimple-laag aan Azure verbindt, moet voldoen aan uw bandbreedte vereisten. U kunt dit doen door het juiste Quality of Service (QoS)-niveau toe te passen op de switches van uw infra structuur, zodat deze overeenkomen met uw RPO-en herstel tijd (RTO) Sla's.

-   Maximum aantal toegangs latenties voor Azure Blob-opslag moet ongeveer 80 MS zijn.

### <a name="deploy-storsimple"></a>StorSimple implementeren

Zie [uw on-premises StorSimple-apparaat implementeren](./storsimple-8000-deployment-walkthrough-u2.md)voor stapsgewijze instructies voor de implementatie van StorSimple.

### <a name="deploy-netbackup"></a>NetBackup implementeren

Zie de documentatie van de sectie [NetBackup 7,7. x](http://www.veritas.com/docs/000094423)voor stapsgewijze instructies voor de implementatie van netback-ups. x.

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

- Gebruik geen spanned volumes (gemaakt door Windows-schijf beheer). spanned volumes worden niet ondersteund.
- Format teer uw volumes met NTFS met een toewijzings grootte van 64 KB.
- Wijs de StorSimple-volumes rechtstreeks toe aan de NetBackup-server.
    - Gebruik iSCSI voor fysieke servers.
    - Gebruik doorgangs schijven voor virtuele servers.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Aanbevolen procedures voor StorSimple en NetBackup

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

### <a name="netbackup-best-practices"></a>Best practices voor netback-ups

-   De NetBackup-data base moet lokaal zijn voor de server en zich niet bevinden op een StorSimple-volume.
-   Voor herstel na nood gevallen maakt u een back-up van de NetBackup-Data Base op een StorSimple-volume.
-   Voor deze oplossing worden volledige en incrementele back-ups voor netback-ups (ook wel differentiële incrementele back-ups genoemd in NetBackup) ondersteund. U wordt aangeraden geen synthetische en cumulatieve incrementele back-ups te gebruiken.
-   Back-upgegevens bestanden mogen alleen de gegevens voor een specifieke taak bevatten. U kunt bijvoorbeeld geen media toevoegen tussen verschillende taken.

Zie de documentatie voor netback-ups op [www.Veritas.com](https://www.veritas.com)voor de meest recente instellingen voor netback-ups en aanbevolen procedures voor het implementeren van deze vereisten.


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

## <a name="set-up-netbackup-storage"></a>Opslag voor netback-ups instellen

### <a name="to-set-up-netbackup-storage"></a>Opslag voor netback-ups instellen

1.  Selecteer in de beheer console voor netback-ups **media-en Apparaatbeheer**  >  **apparaten**  >  **schijf groepen**. Selecteer in de wizard schijf groep configureren het opslag server type **AdvancedDisk** en selecteer vervolgens **volgende**.

    ![Beheer console voor netback-ups, wizard Configuratie van schijf groep](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Selecteer uw server en selecteer vervolgens **volgende**.

    ![Beheer console voor netback-ups, selecteer de server](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Selecteer uw StorSimple-volume.

    ![Beheer console voor netback-ups, selecteer de StorSimple volume schijf](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Voer een naam in voor het doel van de back-up en selecteer **volgende**  >  **volgende** om de wizard te volt ooien.

5.  Controleer de instellingen en selecteer vervolgens **volt ooien**.

6.  Aan het einde van elke volume toewijzing wijzigt u de instellingen voor het opslag apparaat zodat deze overeenkomen met de aanbevolen [procedures voor StorSimple en NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Herhaal stap 1-6 tot u klaar bent met het toewijzen van uw StorSimple-volumes.

    ![Beheer console voor netback-ups, schijf configuratie](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>StorSimple als primair back-updoel instellen

> [!NOTE]
> Gegevens die worden teruggezet vanuit een back-up die is getiert in de Cloud, treden op in de Cloud.

In de volgende afbeelding ziet u de toewijzing van een typisch volume aan een back-uptaak. In dit geval worden alle wekelijkse back-ups met de volledige zaterdag-schijf en de incrementele back-ups toegewezen aan Monday-Friday incrementele schijven. Alle back-ups en herstel bewerkingen zijn afkomstig uit een StorSimple gelaagd volume.

![Logisch diagram van configuratie van primaire back-updoel](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Voor beeld van StorSimple als een primair GFS schema voor het maken van een doel

Hier volgt een voor beeld van een GFS-rotatie schema voor vier weken, maandelijks en jaarlijks:

| Type frequentie/back-up | Volledig | Incrementeel (dagen 1-5)  |   
|---|---|---|
| Wekelijks (weken 1-4) | Saturday | Monday-Friday |
| Maandelijks  | Saturday  |   |
| Jaar | Saturday  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple-volumes toewijzen aan een back-uptaak voor netback-ups

In de volgende procedure wordt ervan uitgegaan dat NetBackup en de doelhost zijn geconfigureerd in overeenstemming met de richt lijnen voor NetBackup-agents.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple-volumes toewijzen aan een back-uptaak voor netback-ups

1. In de beheer console voor netback-ups selecteert u **NetBackup-beheer**, klikt u met de rechter muisknop op **beleids regels** en selecteert u **Nieuw beleid**.

   ![Beheer console voor netback-ups, een nieuw beleid maken](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Voer in het dialoog venster **een nieuw beleid toevoegen** een naam in voor het beleid en schakel vervolgens het selectie vakje **beleids configuratie gebruiken** in. Selecteer **OK**.

   ![Beheer console voor netback-ups, het dialoog venster Nieuw beleid toevoegen](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. Kies in de wizard Configuratie van back-upbeleid het gewenste back-uptype en selecteer vervolgens **volgende**.

   ![Beheer console voor netback-ups, selecteer back-uptype](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Als u het beleids type wilt instellen, selecteert u **standaard** en selecteert u **volgende**.

   ![Beheer console voor netback-ups, selecteer beleid type](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Selecteer uw host, schakel het selectie vakje **client besturingssysteem detecteren** in en selecteer vervolgens **toevoegen**. Selecteer **Next**.

   ![Beheer console voor netback-ups, clients weer geven in een nieuw beleid](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Selecteer de stations waarvan u een back-up wilt maken.

   ![Beheer console voor netback-ups, back-ups selecteren voor een nieuw beleid](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Selecteer de frequentie en de retentie waarden die voldoen aan de vereisten voor het door draaien van back-ups.

   ![Beheer console voor netback-ups, back-upfrequentie en rotatie voor een nieuw beleid](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Selecteer **volgende**  >  **volgende**  >  **volt ooien**.  U kunt het schema wijzigen nadat het beleid is gemaakt.

9. Selecteer om het beleid dat u zojuist hebt gemaakt uit te vouwen en selecteer vervolgens **planningen**.

   ![Beheer console voor netback-ups, schema's voor een nieuw beleid](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Klik met de rechter muisknop op **differentieel-Inc**. Selecteer **kopiëren naar nieuw** en selecteer vervolgens **OK**.

    ![Beheer console voor netback-ups, schema kopiëren naar een nieuw beleid](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Klik met de rechter muisknop op het schema dat u zojuist hebt gemaakt en selecteer vervolgens **wijzigen**.

12. Schakel op het tabblad **kenmerken** het selectie vakje **selectie van beleids opslag overschrijven** in en selecteer vervolgens het volume waar incrementele maandag back-ups naartoe gaan.

    ![Beheer console voor netback-ups, planning wijzigen](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Selecteer op het tabblad **Start venster** het tijd venster voor uw back-ups.

    ![Beheer console voor netback-ups, het Start venster wijzigen](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Selecteer **OK**.

15. Herhaal stap 10-14 voor elke incrementele back-up. Selecteer het juiste volume en schema voor elke back-up die u maakt.

16. Klik met de rechter muisknop op het schema **differentieel-Inc** en verwijder het.

17. Wijzig uw volledige schema om te voldoen aan uw back-upbehoeften.

    ![Beheer console voor netback-ups, het volledige schema wijzigen](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Het Start venster wijzigen.

    ![Beheer console voor netback-ups, het Start venster wijzigen](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Het laatste schema ziet er als volgt uit:

    ![Beheer console voor netback-ups, laatste planning](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>StorSimple instellen als een secundair back-updoel

> [!NOTE]
>Gegevens die worden teruggezet vanuit een back-up die is getiert in de Cloud, treden op in de Cloud.

In dit model moet u een opslag medium hebben (met uitzonde ring van StorSimple) om als tijdelijke cache te dienen. U kunt bijvoorbeeld een RAID-volume (Redundant Array of Independent Disks) gebruiken om ruimte, invoer/uitvoer (I/O) en band breedte aan te passen. U kunt het beste RAID 5, 50 en 10 gebruiken.

In de volgende afbeelding ziet u een voor beeld van een lokale retentie op korte termijn (naar de server) volumes en volumes voor lange termijn retentie archieven. In dit scenario worden alle back-ups uitgevoerd op het lokale RAID-volume (naar de server). Deze back-ups worden periodiek gedupliceerd en gearchiveerd naar een archief volume. Het is belang rijk dat u de grootte van uw lokale (op het server) RAID-volume kunt aanpassen, zodat deze de capaciteit van de korte-termijn inhoud en prestatie vereisten kan verwerken.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>GFS-voor beeld van StorSimple als secundaire back-updoel

![StorSimple als een logisch diagram van een secundair back-updoel](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

In de volgende tabel ziet u hoe u back-ups kunt instellen om uit te voeren op de lokale en StorSimple-schijven. Het bevat afzonderlijke en totale capaciteits vereisten.

### <a name="backup-configuration-and-capacity-requirements"></a>Back-upconfiguratie en capaciteits vereisten

| Type back-up en retentie | Geconfigureerde opslag | Grootte (TiB) | GFS vermenigvuldiger | Totale capaciteit \* (Tib) |
|---|---|---|---|---|
| Week 1 (volledig en incrementeel) |Lokale schijf (korte termijn)| 1 | 1 | 1 |
| StorSimple weken 2-4 |StorSimple-schijf (lange termijn) | 1 | 4 | 4 |
| Volledig maandelijks |StorSimple-schijf (lange termijn) | 1 | 12 | 12 |
| Jaarlijks volledig |StorSimple-schijf (lange termijn) | 1 | 1 | 1 |
|Vereiste voor grootte van GFS-volumes |  |  |  | 18,0|

\* De totale capaciteit omvat 17 TiB van StorSimple schijven en 1 TiB van het lokale RAID-volume.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Voorbeeld schema voor GFS: GFS draai hoek per week, maandelijks en jaarlijks schema

| Week | Volledig | Incrementele dag 1 | Incrementele dag 2 | Incrementele dag 3 | Incrementele dag 4 | Incrementele dag 5 |
|---|---|---|---|---|---|---|
| Week 1 | Lokaal RAID-volume  | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume | Lokaal RAID-volume |
| Week 2 | StorSimple weken 2-4 |   |   |   |   |   |
| Week 3 | StorSimple weken 2-4 |   |   |   |   |   |
| Week 4 | StorSimple weken 2-4 |   |   |   |   |   |
| Maandelijks | StorSimple maandelijks |   |   |   |   |   |
| Jaar | Jaarlijks StorSimple  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple-volumes toewijzen aan een archief voor netback-ups en een duplicatie taak

Omdat NetBackup een breed scala aan opties biedt voor opslag en Media beheer, raden we u aan om te raadplegen met Veritas of de NetBackup architect om uw SLP-vereisten (Storage Lifecycle Policy) goed te beoordelen.

Nadat u de eerste schijf groepen hebt gedefinieerd, moet u drie extra opslag levenscyclus beleid definiëren, voor een totaal van vier beleids regels:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple-volumes toewijzen aan een NetBackup-archief en een duplicatie taak

1. Selecteer in de beheer console voor netback-ups **opslag** ruimte voor het levenscyclus beleid voor  >    >  **nieuwe opslag**.

   ![Beheer console voor netback-ups, nieuw beleid voor opslag levenscyclus](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Voer een naam in voor de moment opname en selecteer vervolgens **toevoegen**.

3. Selecteer in het dialoog venster **nieuwe bewerking** op het tabblad **Eigenschappen** voor **bewerking** de optie **back-up**. Selecteer de gewenste waarden voor de **doel opslag**, het **Bewaar type** en de **Bewaar periode**. Selecteer **OK**.

   ![De beheer console van netback-ups, het dialoog venster nieuwe bewerking](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Hiermee definieert u de eerste back-upbewerking en-opslag plaats.

4. Selecteer de vorige bewerking en selecteer vervolgens **toevoegen**. Selecteer in het dialoog venster **opslag bewerking wijzigen** de gewenste waarden voor de **doel opslag**, het **Bewaar type** en de **Bewaar periode**.

   ![Beheer console voor netback-ups, het dialoog venster opslag bewerking wijzigen](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Selecteer de vorige bewerking en selecteer vervolgens **toevoegen**. Voeg in het dialoog venster **Nieuw beleid voor opslag levenscyclus** maandelijks back-ups voor een jaar toe.

   ![Beheer console voor netback-ups, het dialoog venster Nieuw beleid voor opslag levenscyclus](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Herhaal stap 4-5 tot u het uitgebreide SLP-Bewaar beleid hebt gemaakt dat u nodig hebt.

   ![Beheer console voor netback-ups, beleids regels toevoegen in het dialoog venster nieuwe opslag levenscyclus beleid](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Wanneer u klaar bent met het definiëren van uw SLP-Bewaar beleid, definieert u onder **beleid** een back-upbeleid door de stappen te volgen die worden beschreven in het [toewijzen van StorSimple-volumes aan een back-uptaak voor netback](#assigning-storsimple-volumes-to-a-netbackup-backup-job)-ups.

8. Klik onder **schema's** in het dialoog venster **planning wijzigen** met de rechter muisknop op **volledig** en selecteer vervolgens **wijzigen**.

   ![Beheer console voor netback-ups, het dialoog venster planning wijzigen](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Schakel het selectie vakje **selectie van beleids opslag overschrijven** in en selecteer vervolgens het SLP-Bewaar beleid dat u hebt gemaakt in stap 1-6.

   ![Beheer console voor netback-ups, selectie van beleids opslag negeren](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Selecteer **OK** en herhaal dit voor het incrementele back-upschema.

    ![Beheer console voor netback-ups, het dialoog venster planning wijzigen voor incrementele back-ups](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Bewaar periode van het back-uptype | Grootte (TiB) | GFS vermenigvuldiger\* | Totale capaciteit (TiB)  |
|---|---|---|---|
| Wekelijks volledig |  1  |  4 | 4  |
| Dagelijks incrementeel  | 0,5  | 20 (cycli zijn gelijk aan het aantal weken per maand) | 12 (2 voor het extra quotum) |
| Volledig maandelijks  | 1 | 12 | 12 |
| Jaarlijks volledig | 1  | 10 | 10 |
| GFS-vereiste  |     |     | 38 |
| Extra quotum  | 4  |    | 42 totale GFS-vereiste |

\* De GFS vermenigvuldiger is het aantal kopieën dat u moet beveiligen en bewaren om te voldoen aan de vereisten voor het back-upbeleid.

## <a name="storsimple-cloud-snapshots"></a>StorSimple-Cloud momentopnamen

Met StorSimple-Cloud momentopnamen worden de gegevens beschermd die zich op uw StorSimple-apparaat bevinden. Het maken van een Cloud momentopname is gelijk aan het verzenden van lokale back-uptapes naar een externe locatie. Als u geografisch redundante opslag van Azure gebruikt, is het maken van een moment opname van de Cloud gelijk aan het verzenden van back-uptapes naar meerdere sites. Als u een apparaat na een nood geval wilt herstellen, kunt u een ander StorSimple-apparaat online zetten en een failover uitvoeren. Na de failover hebt u toegang tot de gegevens (tegen Cloud snelheden) van de meest recente Cloud momentopname.

In de volgende sectie wordt beschreven hoe u een kort script maakt voor het starten en verwijderen van StorSimple-Cloud momentopnamen tijdens een back-up na de verwerking.

> [!NOTE]
> Moment opnamen die hand matig of via een programma worden gemaakt, volgen niet het verloop beleid van de StorSimple-moment opname. Deze moment opnamen moeten hand matig of programmatisch worden verwijderd.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Cloud momentopnamen starten en verwijderen met behulp van een script

> [!NOTE]
> Evalueer zorgvuldig de gevolgen voor naleving en gegevens retentie voordat u een StorSimple-moment opname verwijdert. Zie de [documentatie voor netback](http://www.veritas.com/docs/000094423)-ups voor meer informatie over het uitvoeren van een script dat volgt op back-ups.

### <a name="backup-lifecycle"></a>Back-uplevenscyclus

![Diagram van back-uplevenscyclus](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

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
4. Voeg het script toe aan de back-uptaak in NetBackup. Om dit te doen, bewerkt u de opties voor de taak ' vóór verwerking en na verwerking van de opdracht.

> [!NOTE]
> U wordt aangeraden om uw back-upbeleid voor StorSimple-Cloud momentopname als een script aan het einde van uw dagelijkse back-uptaak uit te voeren. Voor meer informatie over het maken van een back-up en het herstellen van uw back-uptoepassingsproces om u te helpen bij uw RPO en RTO, kunt u contact opnemen met uw back-uparchitect.

## <a name="storsimple-as-a-restore-source"></a>StorSimple als een herstel bron

Herstellen vanaf een StorSimple-apparaat werkt zoals herstellen vanuit elk apparaat voor blok opslag. Het terugzetten van gegevens die in de cloud worden getierd, vindt plaats tegen Cloud snelheden. Voor lokale gegevens worden herstel bewerkingen uitgevoerd op de snelheid van de lokale schijf van het apparaat. Zie de [documentatie voor netback-ups](http://www.veritas.com/docs/000094423)voor meer informatie over het uitvoeren van een herstel bewerking. U wordt aangeraden te voldoen aan de aanbevolen procedures voor het herstellen van netback-ups.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple-failover en herstel na nood gevallen

> [!NOTE]
> StorSimple Cloud Appliance wordt niet ondersteund als doel voor het terugzetten van Back-updoelen.

Een nood geval kan worden veroorzaakt door diverse factoren. De volgende tabel geeft een lijst van veelvoorkomende scenario's voor herstel na nood gevallen.

| Scenario | Impact | Herstellen | Notities |
|---|---|---|---|
| StorSimple-apparaat is mislukt | Back-up-en herstel bewerkingen worden onderbroken. | Vervang het apparaat dat is mislukt en voer een [StorSimple-failover en nood herstel](./storsimple-8000-device-failover-disaster-recovery.md)uit. | Als u na het herstel van het apparaat een herstel bewerking moet uitvoeren, worden volledige gegevens verzamelingen opgehaald uit de Cloud naar het nieuwe apparaat. Alle bewerkingen bevinden zich in de Cloud snelheid. Het proces voor het opnieuw scannen van indexen en catalogussen kan ertoe leiden dat alle back-upsets worden gescand en opgehaald van de Cloud laag naar de laag van het lokale apparaat. Dit kan een tijdrovend proces zijn. |
| NetBackup-server fout | Back-up-en herstel bewerkingen worden onderbroken. | Maak de back-upserver opnieuw en herstel de data base. | U moet de NetBackup-server opnieuw bouwen of herstellen op de site voor nood herstel. Zet de data base terug naar het meest recente punt. Als de herstelde NetBackup-data base niet is gesynchroniseerd met uw meest recente back-uptaken, is indexeren en catalogiseren vereist. Dit proces voor het opnieuw scannen van index en catalogus kan ertoe leiden dat alle back-upsets worden gescand en van de Cloud laag worden opgehaald naar de laag van het lokale apparaat. Hierdoor is het veel tijdrovender. |
| Site fout die leidt tot verlies van zowel de back-upserver als de StorSimple | Back-up-en herstel bewerkingen worden onderbroken. | Herstel StorSimple eerst en herstel vervolgens NetBackup. | Herstel StorSimple eerst en herstel vervolgens NetBackup. Als u na het herstel van het apparaat een herstel bewerking moet uitvoeren, worden de volledige gegevens sets van de Cloud naar het nieuwe apparaat opgehaald. Alle bewerkingen bevinden zich in de Cloud snelheid. |

## <a name="references"></a>Referenties

In dit artikel wordt verwezen naar de volgende documenten:

- [StorSimple Multipath I/O instellen](./storsimple-8000-configure-mpio-windows-server.md)
- [Opslag scenario's: Thin Provisioning](/windows-hardware/drivers/storage/thin-provisioning)
- [GPT-stations gebruiken](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Schaduw kopieën voor gedeelde mappen instellen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [herstellen van een back-upset](./storsimple-8000-restore-from-backup-set-u2.md).
- Meer informatie over het uitvoeren van een failover van een [apparaat en nood herstel](./storsimple-8000-device-failover-disaster-recovery.md).