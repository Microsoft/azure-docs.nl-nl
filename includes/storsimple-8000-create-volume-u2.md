---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 400c5f0b2281679c3ec913e94643ffec6082d9e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100551947"
---
#### <a name="to-create-a-volume"></a>Een volume maken
1. Selecteer en klik op uw apparaat in de lijst in tabelvorm met apparaten op de blade **Apparaten**. Klik op **+ Volume toevoegen**.<!--Note to reviewer. No screen updates. Had to rename screens to be able to correct a formatting error.-->

    ![Een nieuw volume toevoegen](./media/storsimple-8000-create-volume-u2/create-volume-01.png)

2. Doe het volgende op de blade **Volume**:
   
   1. In het veld **Apparaat selecteren** wordt automatisch uw huidige apparaat ingevuld.

   2. Selecteer in de vervolgkeuzelijst de volumecontainer waaraan u een volume wilt toevoegen. 

   3. een **naam** voor het volume. U kunt de naam van een volume niet wijzigen wanneer het volume is gemaakt.

   4. Selecteer in de vervolgkeuzelijst het **Type** voor het volume. Voor workloads waarvoor lokale garanties, lage latenties en betere prestaties vereist zijn, selecteert u een **lokaal vastgemaakt** volume. Voor alle overige gegevens selecteert u een **gelaagd** volume. Als u dit volume gebruikt voor de archivering van gegevens, schakelt u **Dit volume gebruiken voor minder frequent gebruikte gearchiveerde gegevens** in.
      
       Een gelaagd volume is dun ingericht (thin provisioning) en kan zeer snel worden gemaakt. Als u voor een gelaagd volume dat is bedoeld voor gegevens archivering de optie **Dit volume gebruiken voor minder vaak gebruikte gegevensarchivering** selecteert, verandert de chunkgrootte voor de ontdubbeling voor het volume in 512 KB. Als dit veld niet is ingeschakeld, gebruikt het betreffende gelaagde volume een chunkgrootte van 64 KB. Een grotere chunkgrootte voor de ontdubbeling zorgt ervoor dat het apparaat sneller grotere archiveringsgegevens naar de cloud kan overzetten.
       
       Een lokaal vastgemaakt volume is compact ingericht en zorgt ervoor dat de primaire gegevens op het volume lokaal op het apparaat blijven en niet naar de cloud worden gelekt.  Als u een lokaal vastgemaakt volume maakt, controleert het apparaat de beschikbare ruimte op de lokale lagen om ervoor te zorgen dat het volume het juiste formaat heeft. Het maken van een lokaal vastgemaakt volume heeft als risico dat bestaande gegevens van het apparaat naar de cloud worden gelekt. Ook duurt het maken van het volume mogelijk lang. De totale tijd is afhankelijk van de grootte van het betreffende volume, de beschikbare netwerkbandbreedte en de gegevens op uw apparaat.

   5. Geef de **ingerichte capaciteit** voor het volume op. Noteer de beschikbare capaciteit op basis van het volumetype dat is geselecteerd. De grootte van het opgegeven volume mag niet groter zijn dan de beschikbare ruimte.
      
       U kunt lokaal vastgemaakte volumes inrichten tot maximaal 8,5 TB, of gelaagde volumes op het 8100-apparaat tot 200 TB. Op een groter 8600-apparaat kunt u lokaal vastgemaakte volumes inrichten tot 22,5 TB en gelaagde volumes tot maximaal 500 TB. Aangezien lokale ruimte op het apparaat is vereist om de werkset van gelaagde volumes te hosten, heeft het maken van lokaal vastgemaakte volumes invloed op de beschikbare schijfruimte voor het inrichten van gelaagde volumes. Als u dus een lokaal vastgemaakt volume maakt, wordt de beschikbare schijfruimte voor het maken van gelaagde volumes verminderd. En andersom: als een gelaagd volume wordt gemaakt, wordt de beschikbare ruimte voor het maken van lokaal vastgemaakte volumes verminderd.
      
       Als u een lokaal vastgemaakt volume van 8,5 TB (maximaal toegestane grootte) op uw 8100-apparaat inricht, hebt u de lokale ruimte die beschikbaar is op het apparaat volledig gebruikt. Vanaf dat moment kunt u geen gelaagd volume maken, omdat er geen lokale ruimte op het apparaat is voor het hosten van de werkset van het gelaagde volume. Bestaande gelaagde volumes zijn ook van invloed op de beschikbare ruimte. Als u bijvoorbeeld een 8100-apparaat hebt met reeds gelaagde volumes van circa 106 TB, is er nog maar 4 TB ruimte beschikbaar voor lokaal vastgemaakte volumes.
 
      1. Klik in het veld **Verbonden hosts** op de pijl. 

         ![Verbonden hosts](./media/storsimple-8000-create-volume-u2/create-volume-02.png)

      1. Kies op de blade **Verbonden hosts** een bestaande ACR of voeg een nieuwe ACR toe door de volgende stappen uit te voeren:

         1. Geef een **naam** voor uw ACR op.
         2. Geef onder **iSCSI-initiatornaam** het IQN (iSCI Qualified Name) van uw Windows-host op. Als u het IQN niet hebt, gaat u naar [Het IQN van een Windows Server-host ophalen](#get-the-iqn-of-a-windows-server-host).

      1. Klik op **Create**. Er wordt een volume gemaakt met de opgegeven instellingen.

         ![Klik op Maken](./media/storsimple-8000-create-volume-u2/create-volume-03.png)

         > [!NOTE]
         > Let erop dat het volume dat u hier hebt gemaakt niet wordt beveiligd. U moet back-upbeleid maken en koppelen aan dit volume om geplande back-ups uit te voeren. 

