---
title: StorSimple-volumes beheren (update 3)
description: Hierin wordt uitgelegd hoe u StorSimple-volumes toevoegt, wijzigt, bewaken en verwijderen, en hoe u deze indien nodig offline kunt brengen.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 12/09/2017
ms.author: alkohli
ms.openlocfilehash: 3d649b54b544c120198963f8094764e9590e20b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100547545"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>De StorSimple Apparaatbeheer-service gebruiken voor het beheren van volumes (update 3 of hoger)

## <a name="overview"></a>Overzicht

In deze zelf studie wordt uitgelegd hoe u de StorSimple Apparaatbeheer-service gebruikt voor het maken en beheren van volumes op de StorSimple 8000-serie apparaten met update 3 en hoger.

De StorSimple-Apparaatbeheer-service is een uitbrei ding in de Azure Portal waarmee u uw StorSimple-oplossing kunt beheren vanuit één webinterface. Gebruik de Azure Portal om volumes op al uw apparaten te beheren. U kunt ook StorSimple-Services maken en beheren, apparaten beheren, back-upbeleid, back-upcatalogussen en waarschuwingen weer geven.

## <a name="volume-types"></a>Volume typen

StorSimple volumes kunnen zijn:

* **Lokaal vastgemaakte volumes**: gegevens in deze volumes blijven te allen tijde op het lokale StorSimple-apparaat.
* **Gelaagde volumes**: gegevens in deze volumes kunnen overlopen naar de Cloud.

Een archief volume is een type gelaagd volume. Met de grotere segment grootte voor ontdubbeling die wordt gebruikt voor archief volumes kan het apparaat grotere gegevens segmenten naar de Cloud overdragen.

Indien nodig kunt u het volume type wijzigen van lokaal in gelaagd of van laag naar lokaal. Ga naar [het volume type wijzigen](#change-the-volume-type)voor meer informatie.

### <a name="locally-pinned-volumes"></a>Lokaal vastgemaakte volumes

Lokaal vastgemaakte volumes zijn volledig ingerichte volumes die geen gegevens in de Cloud laagen, waardoor lokale garanties worden gegarandeerd voor primaire gegevens, onafhankelijk van Cloud connectiviteit. Gegevens op lokaal vastgemaakte volumes worden niet ontdubbeld en gecomprimeerd. moment opnamen van lokaal vastgemaakte volumes worden echter ontdubbeld. 

Lokaal vastgemaakte volumes zijn volledig ingericht. Daarom moet u voldoende ruimte op uw apparaat hebben wanneer u ze maakt. U kunt lokaal vastgemaakte volumes inrichten tot een maximale grootte van 8 TB op het StorSimple 8100-apparaat en 20 TB op het 8600-apparaat. StorSimple reserveert de resterende lokale ruimte op het apparaat voor moment opnamen, meta gegevens en gegevens verwerking. U kunt de grootte van een lokaal vastgemaakt volume verg Roten tot de Maxi maal beschik bare ruimte, maar u kunt de grootte van een volume niet kleiner maken.

Wanneer u een lokaal vastgemaakt volume maakt, wordt de beschik bare ruimte voor het maken van gelaagde volumes gereduceerd. Het omgekeerde is ook waar: als u bestaande gelaagde volumes hebt, is de beschik bare ruimte voor het maken van lokaal vastgemaakte volumes lager dan de hierboven vermelde maximum limieten. Raadpleeg de [Veelgestelde vragen over lokaal vastgemaakte volumes](storsimple-8000-local-volume-faq.md)voor meer informatie over lokale volumes.

### <a name="tiered-volumes"></a>Gelaagd volumes

Gelaagd gelaagde volumes zijn volumes met een Thin Provisioning waarbij de regel matig gebruikte gegevens lokaal op het apparaat blijven, en minder veelgebruikte gegevens worden automatisch aan de Cloud gelaagd. Thin Provisioning is een virtualisatiesoftware waarin beschik bare opslag ruimte groter is dan fysieke resources. In plaats van voldoende opslag vooraf te reserveren, gebruikt StorSimple Thin Provisioning om net zoveel ruimte toe te wijzen om te voldoen aan de huidige vereisten. De elastische aard van Cloud opslag vereenvoudigt deze benadering omdat StorSimple de Cloud opslag kan verg Roten of verkleinen om te voldoen aan veranderende vereisten.

Als u het gelaagde volume voor archiverings gegevens gebruikt, schakelt u het selectie vakje **dit volume gebruiken voor minder vaak gebruikte archiverings gegevens** in om de segment grootte van de ontdubbeling voor uw volume te wijzigen in 512 KB. Als u deze optie niet selecteert, wordt in het bijbehorende gelaagde volume een segment grootte van 64 KB gebruikt. Een grotere chunkgrootte voor de ontdubbeling zorgt ervoor dat het apparaat sneller grotere archiveringsgegevens naar de cloud kan overzetten.


### <a name="provisioned-capacity"></a>Ingerichte capaciteit

Raadpleeg de volgende tabel voor de maximale ingerichte capaciteit voor elk apparaat en elk volume type. (Houd er rekening mee dat lokaal vastgemaakte volumes niet beschikbaar zijn op een virtueel apparaat.)

| Type | Maximale grootte van het gelaagde volume | Maximum grootte van lokaal vastgemaakt volume |
| --- | --- | --- |
| **Fysieke apparaten** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuele apparaten** | | |
| 8010 |30 TB |N.v.t. |
| 8020 |64 TB |N.v.t. |

## <a name="the-volumes-blade"></a>De Blade volumes

Op de Blade **volumes** kunt u de opslag volumes beheren die zijn ingericht op het Microsoft Azure StorSimple apparaat voor uw initia tors (servers). De lijst met volumes op de StorSimple-apparaten die zijn verbonden met uw service wordt weer gegeven.

 ![Pagina volumes](./media/storsimple-8000-manage-volumes-u2/volumes-list.png)

Een volume bestaat uit een reeks kenmerken:

* **Volume naam** : een beschrijvende naam die uniek moet zijn en het volume kan identificeren. Deze naam wordt ook gebruikt in bewakings rapporten wanneer u filtert op een specifiek volume. Wanneer het volume is gemaakt, kan het niet worden hernoemd.
* **Status** : kan online of offline zijn. Als een volume offline is, is het niet zichtbaar voor initia tors (servers) die toegang hebben tot het gebruik van het volume.
* **Capaciteit** – Hiermee geeft u de totale hoeveelheid gegevens op die kan worden opgeslagen door de initiator (Server). Lokaal vastgemaakte volumes zijn volledig ingericht en bevinden zich op het StorSimple-apparaat. Gelaagde volumes worden dun ingericht en de gegevens worden ontdubbeld. Met thin provisioned volumes wijst uw apparaat geen fysieke opslag capaciteit intern of op basis van de geconfigureerde volume capaciteit toe aan de Cloud. De capaciteit van het volume wordt toegewezen en verbruikt op aanvraag.
* **Type** – geeft aan of het volume wordt **gelaagd** (de standaard instelling) of **lokaal is vastgemaakt**.

Volg de instructies in deze zelf studie om de volgende taken uit te voeren:

* Een volume toevoegen 
* Een volume wijzigen 
* Wijzig het volume type
* Een volume verwijderen 
* Een volume offline halen 
* Een volume bewaken 

## <a name="add-a-volume"></a>Een volume toevoegen

U hebt [een volume gemaakt](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) tijdens de implementatie van uw StorSimple 8000 Series-apparaat. Het toevoegen van een volume is een vergelijk bare procedure.

#### <a name="to-add-a-volume"></a>Een volume toevoegen

1. Selecteer en klik op uw apparaat in de lijst in tabelvorm met apparaten op de blade **Apparaten**. Klik op **+ Volume toevoegen**.

    ![Een nieuw volume toevoegen](./media/storsimple-8000-manage-volumes-u2/add-volume-01.png)

2. Doe het volgende op de blade **Volume**:
   
    1. In het veld **Apparaat selecteren** wordt automatisch uw huidige apparaat ingevuld.

    2. Selecteer in de vervolgkeuzelijst de volumecontainer waaraan u een volume wilt toevoegen.

    3.  een **naam** voor het volume. Wanneer het volume is gemaakt, kunt u de naam van het volume niet wijzigen.

    4. Selecteer in de vervolgkeuzelijst het **Type** voor het volume. Voor workloads waarvoor lokale garanties, lage latenties en betere prestaties vereist zijn, selecteert u een **lokaal vastgemaakt** volume. Voor alle overige gegevens selecteert u een **gelaagd** volume. Als u dit volume gebruikt voor de archivering van gegevens, schakelt u **Dit volume gebruiken voor minder frequent gebruikte gearchiveerde gegevens** in.
      
       Een gelaagd volume is dun ingericht (thin provisioning) en kan zeer snel worden gemaakt. Als u voor een gelaagd volume dat is bedoeld voor gegevens archivering de optie **Dit volume gebruiken voor minder vaak gebruikte gegevensarchivering** selecteert, verandert de chunkgrootte voor de ontdubbeling voor het volume in 512 KB. Als dit veld niet is ingeschakeld, gebruikt het betreffende gelaagde volume een chunkgrootte van 64 KB. Een grotere chunkgrootte voor de ontdubbeling zorgt ervoor dat het apparaat sneller grotere archiveringsgegevens naar de cloud kan overzetten.
       
       Een lokaal vastgemaakt volume is compact ingericht en zorgt ervoor dat de primaire gegevens op het volume lokaal op het apparaat blijven en niet naar de cloud worden gelekt.  Als u een lokaal vastgemaakt volume maakt, controleert het apparaat de beschikbare ruimte op de lokale lagen om ervoor te zorgen dat het volume het juiste formaat heeft. Het maken van een lokaal vastgemaakt volume heeft als risico dat bestaande gegevens van het apparaat naar de cloud worden gelekt. Ook duurt het maken van het volume mogelijk lang. De totale tijd is afhankelijk van de grootte van het betreffende volume, de beschikbare netwerkbandbreedte en de gegevens op uw apparaat.

    5. Geef de **ingerichte capaciteit** voor het volume op. Noteer de beschikbare capaciteit op basis van het volumetype dat is geselecteerd. De grootte van het opgegeven volume mag niet groter zijn dan de beschikbare ruimte.
      
       U kunt lokaal vastgemaakte volumes inrichten tot maximaal 8,5 TB, of gelaagde volumes op het 8100-apparaat tot 200 TB. Op een groter 8600-apparaat kunt u lokaal vastgemaakte volumes inrichten tot 22,5 TB en gelaagde volumes tot maximaal 500 TB. Aangezien lokale ruimte op het apparaat is vereist om de werkset van gelaagde volumes te hosten, heeft het maken van lokaal vastgemaakte volumes invloed op de beschikbare schijfruimte voor het inrichten van gelaagde volumes. Als u dus een lokaal vastgemaakt volume maakt, wordt de beschikbare schijfruimte voor het maken van gelaagde volumes verminderd. En andersom: als een gelaagd volume wordt gemaakt, wordt de beschikbare ruimte voor het maken van lokaal vastgemaakte volumes verminderd.
      
       Als u een lokaal vastgemaakt volume van 8,5 TB (maximaal toegestane grootte) op uw 8100-apparaat inricht, hebt u de lokale ruimte die beschikbaar is op het apparaat volledig gebruikt. Vanaf dat moment kunt u geen gelaagd volume maken, omdat er geen lokale ruimte op het apparaat is voor het hosten van de werkset van het gelaagde volume. Bestaande gelaagde volumes zijn ook van invloed op de beschikbare ruimte. Als u bijvoorbeeld een 8100-apparaat hebt met reeds gelaagde volumes van circa 106 TB, is er nog maar 4 TB ruimte beschikbaar voor lokaal vastgemaakte volumes.

    6. Klik in het veld **verbonden hosts** op de pijl en selecteer vervolgens elke ACR die u wilt verbinden. Kies op de Blade **verbonden hosts** een bestaand ACR of Voeg een nieuwe ACR toe. Als u een nieuwe ACR kiest, geeft u een **naam** op voor uw ACR. geef de **iSCSI qualified name** (IQN) van uw Windows-host op. Als u het IQN niet hebt, gaat u naar Het IQN van een Windows Server-host ophalen.<!--Please verify: Is new ACR capability still available? Are the labels and controls the same?--> 

        ![Een verbonden host toevoegen](./media/storsimple-8000-manage-volumes-u2/add-volume-02.png)<!--New graphic. Source: add-volume-connected host-->

   7. Wanneer u klaar bent met de instellingen, klikt u op **maken**. 

      Er wordt een volume gemaakt met de opgegeven instellingen. Het nieuwe volume is klaar voor gebruik.

> [!NOTE]
> Als u een lokaal vastgemaakt volume maakt en vervolgens nog een lokaal vastgemaakt volume maakt, worden de taken voor het maken van het volume sequentieel uitgevoerd. De eerste taak voor het maken van het volume moet worden voltooid voordat de volgende taak voor het maken van het volume kan beginnen.

## <a name="modify-a-volume"></a>Een volume wijzigen

Wijzig een volume wanneer u het wilt uitbreiden of wijzig de hosts die toegang hebben tot het volume.

> [!IMPORTANT]
> * Als u de grootte van het volume op het apparaat wijzigt, moet de grootte van het volume ook worden gewijzigd op de host.
> * De stappen voor de host-side die hier worden beschreven, zijn voor Windows Server 2012 (2012R2). De procedures voor Linux of andere besturings systemen van de host verschillen. Raadpleeg de instructies van uw hostbesturingssysteem wanneer u het volume wijzigt op een host met een ander besturings systeem.

#### <a name="to-modify-a-volume"></a>Een volume wijzigen

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de lijst in tabel vorm van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik op **instellingen > volumes**.

    ![Ga naar de Blade volumes](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

2. Selecteer in de lijst in tabel vorm van volumes het volume en klik met de rechter muisknop om het context menu aan te roepen. Selecteer **offline halen** om het volume te maken dat u offline wilt wijzigen.

    ![Volume selecteren en offline halen](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. Controleer op de Blade **offline nemen** de gevolgen van het offline halen van het volume en schakel het bijbehorende selectie vakje in. Controleer eerst of het bijbehorende volume op de host offline is. Raadpleeg specifieke instructies voor het besturings systeem voor meer informatie over het offline halen van een volume op de hostserver die is verbonden met StorSimple. Klik op **offline zetten**.

    ![De impact van het offline halen van het volume controleren](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)

4. Wanneer het volume offline is (zoals wordt weer gegeven door de volume status), selecteert u het volume en klikt u met de rechter muisknop om het context menu aan te roepen. Selecteer **volume wijzigen**.

    ![Selecteer volume wijzigen](./media/storsimple-8000-manage-volumes-u2/modify-volume-09.png)


5. Op de Blade **volume wijzigen** kunt u de volgende wijzigingen aanbrengen:
   
   1. De volume **naam** kan niet worden bewerkt.
   2. Converteer het **type** van lokaal vastgemaakt naar gelaagd of van gelaagd naar lokaal vastgemaakt (Zie [het volume type wijzigen](#change-the-volume-type) voor meer informatie).
   3. Verhoog de **ingerichte capaciteit**. De **ingerichte capaciteit** kan alleen worden verhoogd. U kunt een volume niet verkleinen nadat het is gemaakt.
   4. Onder **verbonden hosts** kunt u de ACR wijzigen. Als u een ACR wilt wijzigen, moet het volume offline zijn.

       ![Gevolgen van het offline halen van het volume controleren 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)<!--Legacy screen doesn't match step. New graphic needed?-->

6. Klik op **Opslaan** om uw wijzigingen op te slaan. Klik op **Ja** als u om bevestiging wordt gevraagd. Het Azure Portal een update van het volume bericht wordt weer gegeven. Er wordt een bericht weer gegeven wanneer het volume is bijgewerkt.

    ![Invloed van het nemen van het volume offline 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)<!--Updated graphic. Source: modify-volume-save-->

7. Als u een volume uitbreidt, voert u de volgende stappen uit op de Windows-hostcomputer:
   
   1. Ga naar   -> **schijf beheer** van computer beheer.
   2. Klik met de rechter muisknop op **schijf beheer** en selecteer **schijven opnieuw scannen**.
   3. Selecteer in de lijst met schijven het volume dat u hebt bijgewerkt, klik met de rechter muisknop en selecteer vervolgens **volume uitbreiden**. De wizard Volume uitbreiden wordt gestart. Klik op **Volgende**.
   4. Voltooi de wizard en accepteer de standaard waarden. Nadat de wizard is voltooid, moet het volume de verhoogde grootte weer geven.
      
      > [!NOTE]
      > Als u een lokaal vastgemaakt volume uitbreidt en vervolgens nogmaals een lokaal vastgemaakt volume uitbreidt, worden de volume-uitbreidings taken sequentieel uitgevoerd. De eerste volume uitbreiding taak moet worden voltooid voordat de volgende volume uitbreiding kan worden gestart.
      

## <a name="change-the-volume-type"></a>Wijzig het volume type

U kunt het volume type wijzigen van gelaagd naar lokaal vastgemaakt of lokaal vastgemaakt naar gelaagd. Deze conversie zou echter niet vaak moeten plaatsvinden.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Aandachtspunten voor de conversie van lokale volume-volumes

Enkele redenen voor het converteren van een volume van gelaagd naar lokaal vastgemaakte zijn:

* Lokale garanties met betrekking tot de beschik baarheid en prestaties van gegevens
* Eliminatie van Cloud latenties en Cloud verbindings problemen.

Normaal gesp roken zijn dit kleine bestaande volumes die u regel matig wilt benaderen. Een lokaal vastgemaakt volume is volledig ingericht wanneer het wordt gemaakt. 

Als u een gelaagd volume converteert naar een lokaal vastgemaakt volume, controleert StorSimple of u voldoende ruimte op uw apparaat hebt voordat de conversie wordt gestart. Als er onvoldoende ruimte beschikbaar is, ontvangt u een fout melding en wordt de bewerking geannuleerd. 

> [!NOTE]
> Voordat u een conversie van gelaagd naar lokaal vastgemaakt, moet u rekening houden met de ruimte vereisten van uw andere workloads. 

De conversie van een laag naar een lokaal vastgemaakt volume kan een negatieve invloed hebben op de prestaties van het apparaat. Daarnaast kunnen de volgende factoren de tijd verg Roten die nodig is om de conversie te volt ooien:

* Er is onvoldoende band breedte.
* Er is geen huidige back-up.

U kunt als volgt de gevolgen van deze factoren beperken:

* Controleer uw beleid voor bandbreedte beperking en zorg ervoor dat er een toegewezen band breedte van 40 Mbps beschikbaar is.
* De conversie plannen voor daluren.
* Maak een Cloud momentopname voordat u de conversie start.

Als u meerdere volumes converteert (die ondersteuning bieden voor verschillende werk belastingen), moet u de volume conversie priori teren zodat de volumes met hogere prioriteit eerst worden geconverteerd. U moet bijvoorbeeld volumes die virtuele machines (Vm's) hosten of volumes met SQL-workloads, converteren voordat u volumes converteert met bestands share-workloads.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Overwegingen voor de conversie van lokale naar gelaagde volumes

U kunt een lokaal vastgemaakt volume wijzigen in een gelaagd volume als u meer ruimte nodig hebt om andere volumes in te richten. Wanneer u het lokaal vastgemaakte volume converteert naar gelaagd, neemt de beschik bare capaciteit op het apparaat toe met de grootte van de vrijgegeven capaciteit. Als verbindings problemen verhinderen dat een volume van het lokale type naar het gelaagde type wordt geconverteerd, geeft het lokale volume eigenschappen van een gelaagd volume tot de conversie is voltooid. De reden hiervoor is dat sommige gegevens in de Cloud zijn overgelopen. Deze gemorste gegevens blijven lokale ruimte op het apparaat innemen. Dit kan pas worden vrijgemaakt als de bewerking opnieuw is gestart en voltooid.

> [!NOTE]
> Het converteren van een volume kan enige tijd duren en u kunt een conversie niet annuleren nadat deze is gestart. Het volume blijft online tijdens de conversie en u kunt back-ups maken, maar het volume kan niet worden uitgevouwen of hersteld terwijl de conversie plaatsvindt.


#### <a name="to-change-the-volume-type"></a>Het volume type wijzigen

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de lijst in tabel vorm van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik op **instellingen > volumes**.

    ![Ga naar de Blade volumes 2](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Selecteer in de lijst in tabel vorm van volumes het volume en klik met de rechter muisknop om het context menu aan te roepen. Selecteer **wijzigen**.

    ![Selecteer in het snelmenu wijzigen](./media/storsimple-8000-manage-volumes-u2/change-volume-type-02.png)

4. Wijzig op de Blade **volume wijzigen** het volume type door het nieuwe type te selecteren in de vervolg keuzelijst **type** .
   
   * Als u het type wijzigt in **lokaal vastgemaakt**, wordt door StorSimple gecontroleerd of er voldoende capaciteit is.
   * Als u het type wilt wijzigen in **gelaagd** en dit volume wordt gebruikt voor het archiveren van gegevens, schakelt u het selectie vakje **dit volume gebruiken voor minder vaak gebruikte gegevens archivering** in.
   * Als u een lokaal vastgemaakt volume configureert als gelaagd of _vice versa_, wordt het volgende bericht weer gegeven.
   
     ![Bericht van volume type wijzigen](./media/storsimple-8000-manage-volumes-u2/change-volume-type-03.png)

7. Klik op **Opslaan** om de wijzigingen op te slaan. Wanneer u om bevestiging wordt gevraagd, klikt u op **Ja** om het conversie proces te starten. 

    ![Opslaan en bevestigen](./media/storsimple-8000-manage-volumes-u2/modify-volume-11.png)

8. In het Azure Portal wordt een melding weer gegeven voor het maken van de taak waarmee het volume wordt bijgewerkt. Klik op de melding om de status van de volume conversie taak te controleren.

    ![Taak voor volume conversie](./media/storsimple-8000-manage-volumes-u2/change-volume-type-05.png)

## <a name="take-a-volume-offline"></a>Een volume offline halen

Mogelijk moet u een volume offline halen wanneer u van plan bent om het volume te wijzigen of te verwijderen. Wanneer een volume offline is, is het niet beschikbaar voor lezen/schrijven-toegang. U moet het volume offline halen op de host en op het apparaat.

#### <a name="to-take-a-volume-offline"></a>Een volume offline halen

1. Zorg ervoor dat het betreffende volume niet wordt gebruikt voordat u het offline zet.
2. Zet het volume eerst offline op de host. Dit elimineert mogelijke Risico's van beschadigde gegevens op het volume. Raadpleeg de instructies voor het besturings systeem van de host voor specifieke stappen.
3. Nadat de host offline is, neemt u het volume op het apparaat offline door de volgende stappen uit te voeren:
   
    1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de lijst in tabel vorm van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik op **instellingen > volumes**.

        ![Ga naar de Blade volumes 3](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

    2. Selecteer in de lijst in tabel vorm van volumes het volume en klik met de rechter muisknop om het context menu aan te roepen. Selecteer **offline halen** om het volume te maken dat u offline wilt wijzigen.

        ![Volume selecteren en offline halen](./media/storsimple-8000-manage-volumes-u2/modify-volume-04.png)

3. Controleer op de Blade **offline nemen** de gevolgen van het offline halen van het volume en schakel het bijbehorende selectie vakje in. Klik op **offline zetten**. 

    ![Bekijk de gevolgen van het offline halen van het volume 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-05.png)
      
      U wordt gewaarschuwd wanneer het volume offline is. De volume status wordt ook bijgewerkt naar offline.
      
4. Als u het volume hebt geselecteerd en met de rechter muisknop op een volume hebt geklikt, wordt de optie **online** beschikbaar in het context menu weer geven.

> [!NOTE]
> De opdracht **offline nemen** verzendt een aanvraag naar het apparaat om het volume offline te halen. Als hosts nog steeds het volume gebruiken, resulteert dit in verbroken verbindingen, maar neemt het volume offline.

## <a name="delete-a-volume"></a>Een volume verwijderen

> [!IMPORTANT]
> U kunt een volume alleen verwijderen als deze offline is.

Voer de volgende stappen uit om een volume te verwijderen.

#### <a name="to-delete-a-volume"></a>Een volume verwijderen

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de lijst in tabel vorm van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik op **instellingen > volumes**.

    ![Ga naar de Blade volumes 4](./media/storsimple-8000-manage-volumes-u2/modify-volume-02.png)

3. Controleer de status van het volume dat u wilt verwijderen. Als het volume dat u wilt verwijderen niet offline is, moet u het eerst offline zetten. Volg de stappen in [een volume offline halen](#take-a-volume-offline).
4. Nadat het volume offline is, selecteert u het volume, klikt u met de rechter muisknop om het context menu aan te roepen en selecteert u vervolgens **verwijderen**.

    ![Selecteer verwijderen in het context menu](./media/storsimple-8000-manage-volumes-u2/delete-volume-01.png)

5. Bekijk en selecteer op de Blade **verwijderen** het selectie vakje voor de impact van het verwijderen van een volume. Wanneer u een volume verwijdert, gaan alle gegevens die zich op het volume bevinden verloren. 

    ![Wijzigingen opslaan en bevestigen](./media/storsimple-8000-manage-volumes-u2/delete-volume-02.png)

6. Nadat het volume is verwijderd, wordt de lijst in tabel vorm met volumes bijgewerkt om het verwijderen aan te geven.

    ![Bijgewerkte volume lijst](./media/storsimple-8000-manage-volumes-u2/delete-volume-03.png)
   
   > [!NOTE]
   > Als u een lokaal vastgemaakt volume verwijdert, wordt de beschik bare ruimte voor nieuwe volumes mogelijk niet onmiddellijk bijgewerkt. De StorSimple-Apparaatbeheer service werkt de lokale beschik bare ruimte regel matig bij. We raden u aan een paar minuten te wachten voordat u het nieuwe volume probeert te maken.
   >
   > Als u een lokaal vastgemaakt volume verwijdert en vervolgens een ander lokaal vastgemaakt volume verwijdert onmiddellijk daarna, worden de taken voor het verwijderen van het volume sequentieel uitgevoerd. De eerste taak voor het verwijderen van het volume moet worden voltooid voordat de volgende taak voor het verwijderen van het volume kan worden gestart.

## <a name="monitor-a-volume"></a>Een volume bewaken

Met volume bewaking kunt u I/O-gerelateerde statistieken voor een volume verzamelen. Bewaking is standaard ingeschakeld voor de eerste 32 volumes die u maakt. Het controleren van extra volumes is standaard uitgeschakeld. 

> [!NOTE]
> Bewaking van gekloonde volumes is standaard uitgeschakeld.


Voer de volgende stappen uit om de bewaking van een volume in of uit te scha kelen.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Volume bewaking in-of uitschakelen

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de lijst in tabel vorm van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik op **instellingen > volumes**.
2. Selecteer in de lijst in tabel vorm van volumes het volume en klik met de rechter muisknop om het context menu aan te roepen. Selecteer **wijzigen**.
3. Selecteer in de Blade **volume wijzigen** voor **controle** de optie **inschakelen** of **uitschakelen** om bewaking in of uit te scha kelen.

    ! [Controle uitschakelen] (./Media/storsimple-8000-Manage-volumes-U2/monitor-volume-0 1.png) 

4. Klik op **Opslaan** en klik op **Ja** om de bevestiging te bevestigen. In het Azure Portal wordt een melding weer gegeven voor het bijwerken van het volume en vervolgens een geslaagd bericht nadat het volume is bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [klonen van een StorSimple-volume](storsimple-8000-clone-volume-u2.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

