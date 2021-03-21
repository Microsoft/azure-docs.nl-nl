---
title: StorSimple 8000-serie als back-updoel met Backup Exec | Microsoft Docs
description: Beschrijft de configuratie van de StorSimple-back-updoel met Veritas Backup Exec.
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
ms.date: 12/05/2016
ms.author: matd
ms.openlocfilehash: 66a1e22282864d0425173504735d6beb42b76ad7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967258"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple als back-updoel met Backup Exec

## <a name="overview"></a>Overzicht

Azure StorSimple is een Hybrid Cloud Storage oplossing van micro soft. StorSimple is van toepassing op de complexiteit van exponentiële gegevens groei door gebruik te maken van een Azure-opslag account als uitbrei ding van de on-premises oplossing, en om gegevens automatisch te trapsgewijs te lagen in opslag-en Cloud opslag.

In dit artikel bespreken we de StorSimple-integratie met Veritas Backup Exec en best practices voor het integreren van beide oplossingen. We maken ook aanbevelingen voor het instellen van de back-upversie die het beste kan worden geïntegreerd met StorSimple. We stellen de aanbevolen procedures, back-uparchitects en beheerders uit voor de beste manier om Backup Exec in te stellen om te voldoen aan de afzonderlijke vereisten voor back-ups en Service Level Agreements (Sla's).

Hoewel we de configuratie stappen en belang rijke concepten illustreren, is dit artikel in geen stapsgewijze configuratie of installatie handleiding. We gaan ervan uit dat de basis onderdelen en infra structuur in werk orde zijn en klaar zijn voor de ondersteuning van de concepten die we beschrijven.

### <a name="who-should-read-this"></a>Wie moet dit lezen?

De informatie in dit artikel is het nuttigst voor back-upbeheerders, opslag beheerders en opslag architecten met kennis van opslag, Windows Server 2012 R2, Ethernet, Cloud Services en Backup Exec.

## <a name="supported-versions"></a>Ondersteunde versies

-   [Backup Exec 16 en latere versies](https://www.veritas.com/content/support/en_US/article.100040087)
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
 ![ Diagram van StorSimple-lagen](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

![StorSimple als primair diagram van een logisch back-updoel](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![StorSimple als een logisch diagram van een secundair back-updoel](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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
3. Backup Exec implementeren.

Elke stap wordt gedetailleerd beschreven in de volgende secties.

### <a name="set-up-the-network"></a>Het netwerk instellen

Omdat StorSimple een oplossing is die is geïntegreerd met de Azure-Cloud, is voor StorSimple een actieve en werkende verbinding met de Azure-Cloud vereist. Deze verbinding wordt gebruikt voor bewerkingen als Cloud momentopnamen, beheer en meta gegevens overdracht, en om oudere, minder toegankelijke gegevens te laag te maken naar Azure-Cloud opslag.

Als u de oplossing optimaal wilt uitvoeren, raden we u aan deze aanbevolen procedures voor netwerken uit te voeren:

-   De koppeling die uw StorSimple-laag aan Azure verbindt, moet voldoen aan uw bandbreedte vereisten. U kunt dit doen door het benodigde Quality of Service (QoS)-niveau toe te passen op de switches van uw infra structuur, zodat deze overeenkomen met uw RPO-en Recovery Time objectief (RTO) Sla's.
-   Maximum aantal toegangs latenties voor Azure Blob-opslag moet ongeveer 80 MS zijn.

### <a name="deploy-storsimple"></a>StorSimple implementeren

Zie [uw on-premises StorSimple-apparaat implementeren](./storsimple-8000-deployment-walkthrough-u2.md)voor een stapsgewijze instructies voor de implementatie van StorSimple.

### <a name="deploy-backup-exec"></a>Backup Exec implementeren

Zie [Aanbevolen procedures voor de installatie van Backup Exec](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464)voor de aanbevolen procedures voor back-upprocedures.

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

- Gebruik geen spanned volumes (gemaakt door Windows-schijf beheer). Spanned schijven worden niet ondersteund.
- Format teer uw volumes met NTFS met een toewijzings grootte van 64 KB.
- Wijs de StorSimple-volumes rechtstreeks toe aan de Backup Exec-server.
    - Gebruik iSCSI voor fysieke servers.
    - Gebruik doorgangs schijven voor virtuele servers.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Best practices voor StorSimple en Backup Exec

Stel uw oplossing in aan de hand van de richt lijnen in de volgende secties.

### <a name="operating-system-best-practices"></a>Aanbevolen procedures voor het besturings systeem

- Schakel Windows Server-versleuteling en ontdubbeling uit voor het NTFS-bestands systeem.
- Schakel Windows Server-defragmentatie uit op de StorSimple-volumes.
- Schakel Windows Server-indexering uit op de StorSimple-volumes.
- Voer een antivirus scan uit op de bronhost (niet op de StorSimple-volumes).
- Schakel het standaard [onderhoud van Windows Server](/windows/win32/w8cookbook/automatic-maintenance) uit in taak beheer. Doe dit op een van de volgende manieren:
  - De onderhouds Configurator uitschakelen in Windows taak planner.
  - Down load [PsExec](/sysinternals/downloads/psexec) van Windows Sysinternals. Nadat u PsExec hebt gedownload, voert u Azure PowerShell uit als beheerder en typt u:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Best practices voor StorSimple

  -   Zorg ervoor dat het StorSimple-apparaat is bijgewerkt naar [Update 3 of hoger](./index.yml).
  -   Isoleer iSCSI-en Cloud verkeer. Gebruik toegewezen iSCSI-verbindingen voor verkeer tussen StorSimple en de back-upserver.
  -   Zorg ervoor dat uw StorSimple-apparaat een specifiek back-updoel is. Gemengde werk belastingen worden niet ondersteund, omdat deze van invloed zijn op uw RTO en RPO.

### <a name="backup-exec-best-practices"></a>Best practices voor Backup Exec

-   Backup Exec moet worden geïnstalleerd op een lokale schijf van de server en niet op een StorSimple-volume.
-   Stel de back-up opslag **gelijktijdige schrijf bewerkingen** in op het toegestane maximum.
    -   Stel het opslag **blok en de buffer grootte** voor Backup Exec in op 512 KB.
    -   Back-up-opslag in **buffer inschakelen lezen en schrijven**.
-   StorSimple biedt ondersteuning voor de volledige en incrementele back-ups van Backup Exec. U wordt aangeraden geen synthetische en differentiële back-ups te gebruiken.
-   Back-upgegevens bestanden moeten alleen gegevens voor een specifieke taak bevatten. U kunt bijvoorbeeld geen media toevoegen tussen verschillende taken.
-   Taak verificatie uitschakelen. Indien nodig moet de verificatie worden gepland na de meest recente back-uptaak. Het is belang rijk om te begrijpen dat deze taak van invloed is op uw back-upvenster.
-   Selecteer **opslag**  >  Details van **de schijf**  >    >  **Eigenschappen**. Schakel **het vooraf toewijzen van schijf ruimte** uit.

Zie [de website van Veritas](https://www.veritas.com)voor de meest recente instellingen voor Backup Exec en aanbevolen procedures voor het implementeren van deze vereisten.

## <a name="retention-policies"></a>Bewaarbeleid

Een van de meest voorkomende beleids typen voor het bewaren van back-ups is een beleid voor groot vader, vader en zoon (GFS). In een GFS-beleid wordt dagelijks een incrementele back-up uitgevoerd en worden volledige back-ups wekelijks en maandelijks uitgevoerd. Dit beleid resulteert in gelaagde zes StorSimple-volumes. Eén volume bevat de wekelijkse, maandelijkse en jaarlijkse volledige back-ups. De andere vijf volumes slaan dagelijkse incrementele back-ups op.

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

## <a name="set-up-backup-exec-storage"></a>Backup Exec-opslag instellen

### <a name="to-set-up-backup-exec-storage"></a>Opslag voor Backup Exec instellen

1.  Selecteer in de beheer console backup **exec opslag**  >    >  **op opslag schijven gebaseerde opslag** configureren  >  .

    ![Backup Exec-beheer console, opslag pagina configureren](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Selecteer **Disk Storage** en selecteer vervolgens **volgende**.

    ![Backup Exec-beheer console, opslag pagina selecteren](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Voer een representatieve naam in, bijvoorbeeld **zaterdag vol** en een beschrijving. Selecteer **Next**.

    ![Pagina Backup Exec-beheer console, naam en beschrijving](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Selecteer de schijf waarop u het opslag apparaat voor schijven wilt maken en selecteer vervolgens **volgende**.

    ![Beheer console Backup Exec, selectie pagina opslag schijven](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Verhoog het aantal schrijf bewerkingen tot **16** en selecteer vervolgens **volgende**.

    ![De pagina Backup Exec-beheer console, gelijktijdige instellingen voor schrijf bewerkingen](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Controleer de instellingen en selecteer vervolgens **volt ooien**.

    ![Backup Exec-beheer console, pagina overzicht van opslag configuratie](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Aan het einde van elke volume toewijzing wijzigt u de instellingen voor het opslag apparaat zodat deze overeenkomen met de aanbevolen [procedures voor StorSimple en Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Beheer console Backup Exec, pagina instellingen voor opslag apparaten](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Herhaal stap 1-7 tot u klaar bent met het toewijzen van uw StorSimple-volumes aan Backup Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>StorSimple als primair back-updoel instellen

> [!NOTE]
> Het terugzetten van gegevens vanuit een back-up die is getiert in de Cloud, vindt plaats op Cloud snelheden.

In de volgende afbeelding ziet u de toewijzing van een typisch volume aan een back-uptaak. In dit geval worden alle wekelijkse back-ups met de volledige zaterdag-schijf en de incrementele back-ups toegewezen aan Monday-Friday incrementele schijven. Alle back-ups en herstel bewerkingen zijn afkomstig uit een StorSimple gelaagd volume.

![Logisch diagram van configuratie van primaire back-updoel](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Voor beeld van StorSimple als een primair GFS schema voor het maken van een doel

Hier volgt een voor beeld van een GFS-rotatie schema voor vier weken, maandelijks en jaarlijks:

| Type frequentie/back-up | Volledig | Incrementeel (dagen 1-5)  |   
|---|---|---|
| Wekelijks (weken 1-4) | Saturday | Monday-Friday |
| Maandelijks  | Saturday  |   |
| Jaar | Saturday  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-volumes toewijzen aan een Backup Exec-back-uptaak

In de volgende procedure wordt ervan uitgegaan dat Backup Exec en de doelhost zijn geconfigureerd in overeenstemming met de richt lijnen voor Backup Exec-agents.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-volumes toewijzen aan een Backup Exec-back-uptaak

1.  Selecteer back-up van back **-up**  >    >  **naar schijf** in de beheer console van Backup Exec.

    ![Backup Exec-beheer console, host, back-up en back-up naar schijf selecteren](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  Selecteer in het dialoog venster **Eigenschappen van back-updefinitie** onder **back-up** de optie **bewerken**.

    ![Backup Exec-beheer console, eigenschappen van definitie van back-up in het dialoog venster](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Stel uw volledige en incrementele back-ups in, zodat ze voldoen aan uw RPO-en RTO-vereisten en voldoen aan de aanbevolen procedures voor Veritas.

4.  Selecteer in het dialoog venster **Opties voor back-up** de optie **opslag**.

    ![Het dialoog venster Backup Exec-beheer console, back-upopties voor opslag](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Wijs de bijbehorende StorSimple-volumes toe aan uw back-upschema.

    > [!NOTE]
    > **Compressie** en **versleutelings type** zijn ingesteld op **geen**.

6.  Schakel onder **controleren** het selectie vakje **gegevens niet verifiëren voor deze taak** in. Het gebruik van deze optie kan van invloed zijn op de StorSimple-lagen.

    > [!NOTE]
    > Defragmentatie, indexering en verificatie op de achtergrond zijn een negatieve invloed op de StorSimple-lagen.

    ![Backup Exec-beheer console, back-upopties instellingen controleren](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Wanneer u de rest van de back-upopties hebt ingesteld om te voldoen aan uw vereisten, selecteert u **OK** om te volt ooien.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>StorSimple instellen als een secundair back-updoel

> [!NOTE]
>Gegevens die worden teruggezet vanuit een back-up die is getiert in de Cloud, treden op in de Cloud.

In dit model moet u een opslag medium hebben (met uitzonde ring van StorSimple) om als tijdelijke cache te dienen. U kunt bijvoorbeeld een RAID-volume (Redundant Array of Independent Disks) gebruiken om ruimte, invoer/uitvoer (I/O) en band breedte aan te passen. U kunt het beste RAID 5, 50 en 10 gebruiken.

In de volgende afbeelding ziet u een voor beeld van een lokale retentie op korte termijn (naar de server) volumes en volumes voor lange termijn retentie archieven. In dit scenario worden alle back-ups uitgevoerd op het lokale RAID-volume (naar de server). Deze back-ups worden periodiek gedupliceerd en gearchiveerd naar een archief volume. Het is belang rijk dat u de grootte van uw lokale (op het server) RAID-volume kunt aanpassen, zodat deze de capaciteit van de korte-termijn inhoud en prestatie vereisten kan verwerken.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>GFS-voor beeld van StorSimple als secundaire back-updoel

![Logisch diagram van StorSimple als secundair back-updoel](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

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


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>StorSimple-volumes toewijzen aan een taak Backup Exec Archive en ontdubbeling

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>StorSimple-volumes toewijzen aan een Backup Exec-archief en een duplicatie taak

1.  Klik in de beheer console van Backup Exec met de rechter muisknop op de taak die u wilt archiveren naar een StorSimple-volume en selecteer vervolgens **back-up van definitie-eigenschappen**  >  **bewerken**.

    ![Backup Exec-beheer console, tabblad Back-updefinitie eigenschappen](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Selecteer **fase**  >  **duplicaat toevoegen voor schijf**  >  **bewerking**.

    ![Backup Exec-beheer console, fase toevoegen](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Selecteer in het dialoog venster **Opties dupliceren** de waarden die u wilt gebruiken voor de **bron** en het **schema**.

    ![Backup Exec-beheer console, eigenschappen van back-updefinities en dubbele opties](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Selecteer in de vervolg keuzelijst **opslag** het StorSimple-volume waar u de gegevens wilt opslaan met de archief taak.

    ![Scherm opname van de lijst waarin u opslag wilt selecteren.](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Selecteer **verifiëren** en schakel vervolgens het selectie vakje de **gegevens niet controleren voor deze taak** in.

    ![Scherm afbeelding die laat zien waar u de optie geen gegevens controleren voor deze taak selecteert.](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Selecteer **OK**.

    ![Scherm opname van de eigenschappen van de back-updefinitie.](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Voeg in de kolom **back-up** een nieuwe fase toe. Gebruik **Incrementeel** voor de bron. Kies voor het doel het StorSimple-volume waarop de incrementele back-uptaak wordt gearchiveerd. Herhaal stap 1-6.

## <a name="storsimple-cloud-snapshots"></a>StorSimple-Cloud momentopnamen

Met StorSimple-Cloud momentopnamen worden de gegevens beschermd die zich op uw StorSimple-apparaat bevinden. Het maken van een Cloud momentopname is gelijk aan het verzenden van lokale back-uptapes naar een externe locatie. Als u geografisch redundante opslag van Azure gebruikt, is het maken van een moment opname van de Cloud gelijk aan het verzenden van back-uptapes naar meerdere sites. Als u een apparaat na een nood geval wilt herstellen, kunt u een ander StorSimple-apparaat online zetten en een failover uitvoeren. Na de failover hebt u toegang tot de gegevens (tegen Cloud snelheden) van de meest recente Cloud momentopname.

In de volgende sectie wordt beschreven hoe u een kort script maakt voor het starten en verwijderen van StorSimple-Cloud momentopnamen tijdens een back-up na de verwerking.

> [!NOTE]
> Moment opnamen die hand matig of via een programma worden gemaakt, volgen niet het verloop beleid van de StorSimple-moment opname. Deze moment opnamen moeten hand matig of programmatisch worden verwijderd.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Cloud momentopnamen starten en verwijderen met behulp van een script

> [!NOTE]
> Evalueer zorgvuldig de gevolgen voor naleving en gegevens retentie voordat u een StorSimple-moment opname verwijdert. Zie de [documentatie voor Backup Exec](https://www.veritas.com/support/en_US/article.100032497.html)voor meer informatie over het uitvoeren van een script dat volgt op back-ups.

### <a name="backup-lifecycle"></a>Back-uplevenscyclus

![Diagram van back-uplevenscyclus](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
4. Voeg het script toe aan uw back-uptaak in Backup Exec door de opdracht Opties pre-processing en naverwerking van de opdracht Backup Exec te bewerken.

   ![Het tabblad opdracht Backup Exec-console, back-upopties, pre-en verwerkings opdrachten](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> U wordt aangeraden om uw back-upbeleid voor StorSimple-Cloud momentopname als een script aan het einde van uw dagelijkse back-uptaak uit te voeren. Voor meer informatie over het maken van een back-up en het herstellen van uw back-uptoepassingsproces om u te helpen bij uw RPO en RTO, kunt u contact opnemen met uw back-uparchitect.

## <a name="storsimple-as-a-restore-source"></a>StorSimple als een herstel bron

Herstellen vanaf een StorSimple-apparaat werkt zoals herstellen vanuit elk apparaat voor blok opslag. Het terugzetten van gegevens die in de cloud worden getierd, vindt plaats tegen Cloud snelheden. Voor lokale gegevens worden herstel bewerkingen uitgevoerd op de snelheid van de lokale schijf van het apparaat. Zie de documentatie voor Backup Exec voor informatie over het uitvoeren van een herstel bewerking. U wordt aangeraden om te voldoen aan de aanbevolen procedures voor Backup Exec herstellen.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple-failover en herstel na nood gevallen

> [!NOTE]
> StorSimple Cloud Appliance wordt niet ondersteund als doel voor het terugzetten van Back-updoelen.

Een nood geval kan worden veroorzaakt door diverse factoren. De volgende tabel geeft een lijst van veelvoorkomende scenario's voor herstel na nood gevallen.

| Scenario | Impact | Herstellen | Notities |
|---|---|---|---|
| StorSimple-apparaat is mislukt | Back-up-en herstel bewerkingen worden onderbroken. | Vervang het apparaat dat is mislukt en voer een [StorSimple-failover en nood herstel](./storsimple-8000-device-failover-disaster-recovery.md)uit. | Als u na het herstel van het apparaat een herstel bewerking moet uitvoeren, worden volledige gegevens verzamelingen opgehaald uit de Cloud naar het nieuwe apparaat. Alle bewerkingen bevinden zich in de Cloud snelheid. Het proces voor het opnieuw scannen van indexeren en catalogiseren kan ertoe leiden dat alle back-upsets worden gescand en worden opgehaald uit de Cloud laag naar de laag van het lokale apparaat. Dit kan een tijdrovend proces zijn. |
| Backup Exec-server fout | Back-up-en herstel bewerkingen worden onderbroken. | Bouw de back-upserver opnieuw op en voer data base terugzetten uit, zoals beschreven in [een hand matige back-up en herstel van de data base Backup Exec (BEDB)](http://www.veritas.com/docs/000041083). | U moet de Backup Exec-server opnieuw samen stellen of herstellen op de site voor nood herstel. Zet de data base terug naar het meest recente punt. Als de herstelde data base van Backup Exec niet synchroon is met uw meest recente back-uptaken, is indexeren en catalogiseren vereist. Dit proces voor het opnieuw scannen van index en catalogus kan ertoe leiden dat alle back-upsets worden gescand en van de Cloud laag worden opgehaald naar de laag van het lokale apparaat. Hierdoor is het veel tijdrovender. |
| Site fout die leidt tot verlies van zowel de back-upserver als de StorSimple | Back-up-en herstel bewerkingen worden onderbroken. | Herstel eerst StorSimple en herstel vervolgens de functie voor het maken van back-ups. | Herstel eerst StorSimple en herstel vervolgens de functie voor het maken van back-ups. Als u na het herstel van het apparaat een herstel bewerking moet uitvoeren, worden de volledige gegevens sets van de Cloud naar het nieuwe apparaat opgehaald. Alle bewerkingen bevinden zich in de Cloud snelheid. |

## <a name="references"></a>Referenties

In dit artikel wordt verwezen naar de volgende documenten:

- [StorSimple Multipath I/O instellen](./storsimple-8000-configure-mpio-windows-server.md)
- [Opslag scenario's: Thin Provisioning](/windows-hardware/drivers/storage/thin-provisioning)
- [GPT-stations gebruiken](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Schaduw kopieën voor gedeelde mappen instellen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [herstellen van een back-upset](./storsimple-8000-restore-from-backup-set-u2.md).
- Meer informatie over het uitvoeren van een failover van een [apparaat en nood herstel](./storsimple-8000-device-failover-disaster-recovery.md).