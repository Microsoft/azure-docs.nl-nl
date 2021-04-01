---
title: Failover en nood herstel voor de virtuele StorSimple-matrix
description: Meer informatie over herstel na nood gevallen voor uw Microsoft Azure StorSimple virtuele matrix, inclusief de gedetailleerde stappen voor het uitvoeren van een failover naar een andere virtuele matrix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a321b2eb79ea01bbf94e1a413c189ac7f4614ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99096420"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Herstel na noodgevallen en failover van apparaat voor virtuele StorSimple-array via Azure-portal

## <a name="overview"></a>Overzicht
In dit artikel wordt het herstel na nood gevallen voor uw Microsoft Azure StorSimple virtuele matrix beschreven, inclusief de gedetailleerde stappen voor het uitvoeren van een failover naar een andere virtuele matrix. Met een failover kunt u uw gegevens van een *bron* apparaat in het Data Center naar een *doel* apparaat verplaatsen. Het doel apparaat bevindt zich mogelijk op dezelfde of een andere geografische locatie. De failover van het apparaat is voor het hele apparaat. Tijdens de failover wijzigt de Cloud gegevens voor het bron apparaat het eigendom van het doel apparaat.

Dit artikel is alleen van toepassing op virtuele StorSimple-matrices. Als u een failover wilt uitvoeren voor een apparaat met een 8000-serie, gaat u naar [failover van apparaat en nood herstel van uw StorSimple-apparaat](./storsimple-8000-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Wat is nood herstel en failover van het apparaat?

In een scenario voor nood herstel (DR) functioneert het primaire apparaat niet meer. In dit scenario kunt u de Cloud gegevens die zijn gekoppeld aan het apparaat, verplaatsen naar een ander apparaat. U kunt het primaire apparaat als *bron* gebruiken en een ander apparaat als *doel* opgeven. Dit proces wordt de *failover* genoemd. Tijdens de failover worden alle volumes of de shares van het bron apparaat gewijzigd in eigendom en worden ze overgedragen naar het doel apparaat. Het is niet toegestaan om de gegevens te filteren.

DR wordt gemodelleerd als een volledig herstel van het apparaat met behulp van de op warmte toewijzing gebaseerde lagen en tracering. Een heatmap wordt gedefinieerd door een hitte waarde toe te wijzen aan de gegevens op basis van de lees-en schrijf patronen. Met deze heatmap worden vervolgens de laagste gegevens segmenten in de Cloud gelaagd en worden de hoge hitte (meest gebruikte) gegevens segmenten in de lokale laag bewaard. Tijdens een DR gebruikt StorSimple de heatmap om de gegevens uit de Cloud terug te zetten en te herstellen. Het apparaat haalt alle volumes/shares op in de laatste recente back-up (zoals intern bepaald) en voert een herstel uit van de back-up. De virtuele matrix is het hele DR-proces.

> [!IMPORTANT]
> Het bron apparaat wordt aan het einde van de failover van het apparaat verwijderd en daarom wordt failback niet ondersteund.
> 
> 

Herstel na nood gevallen wordt georganiseerd via het failover-onderdeel van het apparaat en wordt gestart vanaf de Blade **apparaten** . In deze Blade worden alle StorSimple-apparaten die zijn verbonden met uw StorSimple Apparaatbeheer-service in een tabel. Voor elk apparaat ziet u de beschrijvende naam, status, ingericht en maximum capaciteit, het type en het model.

## <a name="prerequisites-for-device-failover"></a>Vereisten voor apparaatfailover

### <a name="prerequisites"></a>Vereisten

Controleer voor een failover van een apparaat of aan de volgende vereisten wordt voldaan:

* Het bron apparaat moet een **gedeactiveerde** status hebben.
* Het doel apparaat moet zo worden weer gegeven dat het **kan worden ingesteld** in de Azure Portal. Richt een virtuele doel matrix van dezelfde of een hogere capaciteit in. Gebruik de lokale web-UI om de doel-virtuele matrix te configureren en te registreren.
  
  > [!IMPORTANT]
  > Probeer het geregistreerde virtuele apparaat niet via de service te configureren. Er moet geen apparaatconfiguratie worden uitgevoerd via de service.
  > 
  > 
* Het doel apparaat kan niet dezelfde naam hebben als het bron apparaat.
* Het bron-en doel apparaat moeten van hetzelfde type zijn. U kunt alleen een failover uitvoeren van een virtuele matrix die is geconfigureerd als een bestands server op een andere bestands server. Hetzelfde geldt voor een iSCSI-server.
* Voor een bestands server DR wordt u aangeraden het doel apparaat toe te voegen aan hetzelfde domein als de bron. Deze configuratie zorgt ervoor dat de share machtigingen automatisch worden omgezet. Alleen de failover naar een doel apparaat in hetzelfde domein wordt ondersteund.
* De beschik bare doel apparaten voor DR zijn apparaten die dezelfde of grotere capaciteit hebben ten opzichte van het bron apparaat. De apparaten die zijn verbonden met uw service maar niet voldoen aan de criteria van voldoende ruimte zijn niet beschikbaar als doel apparaten.

### <a name="other-considerations"></a>Andere overwegingen

* Voor een geplande failover:
  
  * U wordt aangeraden alle volumes of shares op het bron apparaat offline te halen.
  * U wordt aangeraden een back-up van het apparaat te maken en vervolgens door te gaan met de failover om gegevens verlies te minimaliseren.
* Voor een ongeplande failover gebruikt het apparaat de meest recente back-up om de gegevens te herstellen.

### <a name="device-failover-prechecks"></a>Voor controle van failover van apparaat

Voordat DR wordt gestart, voert het apparaat voor controle uit. Deze controles helpen ervoor te zorgen dat er geen fouten optreden bij het begin van DR. De voor controles zijn onder andere:

* Het opslag account wordt gevalideerd.
* De Cloud connectiviteit met Azure controleren.
* Beschik bare ruimte op het doel apparaat controleren.
* Controleren of het volume van een iSCSI-Server bron apparaat is
  
  * geldige ACR-namen.
  * geldige IQN (niet langer is dan 220 tekens).
  * geldige CHAP-wacht woorden (12-16 tekens lang).

Als een van de voor gaande controles mislukt, kunt u niet door gaan met de DR. Los deze problemen op en voer DR opnieuw uit.

Nadat de DR is voltooid, wordt het eigendom van de Cloud gegevens op het bron apparaat overgebracht naar het doel apparaat. Het bron apparaat is vervolgens niet meer beschikbaar in de portal. De toegang tot alle volumes/shares op het bron apparaat wordt geblokkeerd en het doel apparaat wordt actief.

> [!IMPORTANT]
> Hoewel het apparaat niet meer beschikbaar is, neemt de virtuele machine die u op het hostsysteem hebt ingericht nog steeds resources in beslag. Zodra de DR is voltooid, kunt u deze virtuele machine verwijderen van uw hostsysteem.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Failover naar een virtuele matrix

U wordt aangeraden een andere virtuele StorSimple-matrix in te richten, configureren en registreren met uw StorSimple Apparaatbeheer-service voordat u deze procedure uitvoert.

> [!IMPORTANT]
> 
> * U kunt geen failover uitvoeren van een StorSimple 8000-serie apparaat naar een 1200 virtueel apparaat.
> * U kunt een failover van een virtueel Federal Information Processing Standard-apparaat (FIPS) inschakelen op een ander FIPS-apparaat of naar een niet-FIPS-apparaat dat is geïmplementeerd in de overheids Portal.


Voer de volgende stappen uit om het apparaat te herstellen naar een virtueel StorSimple-apparaat.

1. Een doel apparaat inrichten en configureren dat voldoet aan de [vereisten voor failover van het apparaat](#prerequisites). De apparaatconfiguratie volt ooien via de lokale webgebruikersinterface en deze registreren bij uw StorSimple Apparaatbeheer service. Als u een bestands server maakt, gaat u naar stap 1 van [instellen als bestands server](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Als u een iSCSI-server maakt, gaat u naar stap 1 van [instellen als iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Volumes/shares offline halen op de host. Raadpleeg het besturings systeem – specifieke instructies voor de host om de volumes/shares offline te halen. Als u dit nog niet hebt gedaan, moet u alle volumes/shares op het apparaat als volgt offline zetten.
   
    1. Ga naar de Blade **apparaten** en selecteer uw apparaat.
   
    2. Ga naar **instellingen > > shares te beheren** (of **instellingen > > volumes beheren**). 
   
    3. Selecteer een share/volume, klik met de rechter muisknop en selecteer **offline halen**. 
   
    4. Als u om bevestiging wordt gevraagd, moet u **weten wat de gevolgen zijn van het offline halen van deze share.** 
   
    5. Klik op **offline zetten**.

3. Ga in de StorSimple-Apparaatbeheer service naar **Management >-apparaten**. Selecteer op de Blade **apparaten** de optie en klik op uw bron apparaat.

4. Klik op de Blade van uw **apparaat-dash board** op **deactiveren**.

5. Op de Blade **deactiveren** wordt u gevraagd om bevestiging. Het deactiveren van het apparaat is een *permanent* proces dat niet ongedaan kan worden gemaakt. U wordt ook gevraagd om uw shares/volumes offline te halen op de host. Typ de naam van het apparaat dat u wilt bevestigen en klik op **deactiveren**.
   
    ![Scherm afbeelding van de Blade deactiveren. Het vak apparaatnaam is ingevuld en de knop deactiveren is gemarkeerd.](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Het deactiveren wordt gestart. U ontvangt een melding nadat de activering is voltooid.
   
    ![Scherm opname van een voortgangs balk die aangeeft dat het apparaat wordt gedeactiveerd.](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Op de pagina apparaten wordt de status van het apparaat nu gewijzigd in **gedeactiveerd**.
    ![Scherm afbeelding van de pagina apparaten. De eigenschappen van het gedeactiveerde apparaat worden weer gegeven, inclusief de status, die wordt weer gegeven als gedeactiveerd.](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Selecteer op de Blade **apparaten** het gedeactiveerde bron apparaat voor failover. 
9. Klik op de Blade van het **dash board apparaat** op **failover**. 
10. Ga als volgt te werk op de Blade **failover van apparaat** :
    
    1. Het veld Bron apparaat wordt automatisch ingevuld. Noteer de totale gegevens grootte voor het bron apparaat. De gegevens grootte moet kleiner zijn dan de beschik bare capaciteit op het doel apparaat. Bekijk de details die zijn gekoppeld aan het bron apparaat, zoals de apparaatnaam, de totale capaciteit en de namen van de shares waarvoor een failover is uitgevoerd.

    2. Kies een **doel apparaat** in de vervolg keuzelijst met beschik bare apparaten. Alleen apparaten met voldoende capaciteit worden weer gegeven in de vervolg keuzelijst.

    3. Controleer of **er een failover van de gegevens naar het doel apparaat in deze bewerking wordt uitgevoerd**. 

    4. Klik op **failover**.
    
        ![Scherm opname van de Blade failover van apparaat, waarbij het bron-en doel apparaat is ingevuld, de optie ingeschakeld en de knop failover is gemarkeerd.](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Een failover-taak wordt gestart en er wordt een melding weer gegeven. Ga naar **apparaten > taken** om de failover te controleren.
    
     ![Scherm opname van een voortgangs balk die aangeeft dat er een failover voor het apparaat wordt uitgevoerd.](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Op de Blade **taken** ziet u een failover-taak die is gemaakt voor het bron apparaat. Met deze taak worden de voor spellingen van DR uitgevoerd.
    
    ![Scherm afbeelding die laat zien dat een failover-taak is gestart.](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Nadat de DR-controle is geslaagd, worden met de failover-taak herstel taken voor elk share/volume dat op uw bron apparaat bestaat.
    
    ![Scherm opname met de details van de failover-taak, zoals de status, het apparaat en de duur.](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Nadat de failover is voltooid, gaat u naar de Blade **apparaten** .
    
    1. Selecteer en klik op het StorSimple-apparaat dat is gebruikt als doel apparaat voor het failoverproces.
    2. Ga naar **instellingen > beheer > shares** (of **volumes** als iSCSI-server). Op de Blade **shares** kunt u alle shares (volumes) van het oude apparaat weer geven.
        ![Scherm opname van de Blade apparaten. Het doel apparaat wordt weer gegeven met de status online.](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. U moet [een DNS-alias maken](https://web.archive.org/web/20150307000707/http://support.microsoft.com:80/kb/168322) zodat alle toepassingen die verbinding proberen te maken, kunnen worden omgeleid naar het nieuwe apparaat.

## <a name="errors-during-dr"></a>Fouten tijdens DR

**Storing in de Cloud verbinding tijdens DR**

Als de verbinding met de Cloud wordt verstoord nadat DR is gestart en voordat het apparaat is teruggezet, mislukt de DR. U ontvangt een melding over een fout. Het doel apparaat voor DR is gemarkeerd als *onbruikbaar.* U kunt niet hetzelfde doel apparaat gebruiken voor toekomstige DRs.

**Geen compatibele doel apparaten**

Als er onvoldoende ruimte beschikbaar is op de beschik bare doel apparaten, ziet u een fout bericht dat er geen compatibele doel apparaten zijn.

**Mislukte controles**

Als niet aan een van de voor spellingen wordt voldaan, worden er controle fouten weer geven.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Bedrijfs continuïteit nood herstel (BCDR)

Een scenario voor een herstel na nood geval in bedrijfs continuïteit (BCDR) treedt op wanneer het hele Azure-Data Center niet meer werkt. Dit kan van invloed zijn op uw StorSimple Apparaatbeheer-service en de bijbehorende StorSimple-apparaten.

Als er StorSimple-apparaten zijn die zijn geregistreerd net voordat een nood geval is opgetreden, moeten deze StorSimple-apparaten mogelijk worden verwijderd. Na de nood geval kunt u deze apparaten opnieuw maken en configureren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u [uw virtuele StorSimple-matrix beheert met behulp van de lokale webgebruikersinterface](storsimple-ova-web-ui-admin.md).