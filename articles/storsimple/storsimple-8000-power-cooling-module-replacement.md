---
title: Een PCM vervangen in uw StorSimple 8000-serie apparaat | Microsoft Docs
description: Hierin wordt uitgelegd hoe u de module voor voeding en koeling (PCM) op uw StorSimple-apparaat kunt verwijderen en vervangen
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 045cec85174a88d1d608a4adc679461008852768
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514596"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Stroom- en koelmodule van StorSimple-apparaat vervangen
## <a name="overview"></a>Overzicht
De module voor voeding en koeling (PCM) in uw Microsoft Azure StorSimple-apparaat bestaat uit een voeding en ventilatoren die worden beheerd via de primaire en EBOD behuizingen. Er is slechts één model van PCM dat is gecertificeerd voor elke behuizing. De primaire behuizing is gecertificeerd voor een 764 W PCM en de EBOD Enclosure is gecertificeerd voor een 580 W PCM. Hoewel de PCMs voor de primaire behuizing en de EBOD-behuizing verschillend zijn, is de vervangings procedure identiek.

In deze zelfstudie wordt het volgende uitgelegd:

* Een PCM verwijderen
* Een vervangende PCM installeren

> [!IMPORTANT]
> Voordat u een PCM verwijdert en vervangt, raadpleegt u de veiligheids informatie in [StorSimple vervanging](storsimple-8000-hardware-component-replacement.md)van het hardware-onderdeel.


## <a name="before-you-replace-a-pcm"></a>Voordat u een PCM vervangt
Houd rekening met de volgende belang rijke problemen voordat u uw PCM vervangt:

* Als de voeding van de PCM mislukt, laat u de defecte module geïnstalleerd, maar verwijdert u het netsnoer. De ventilator blijft energie van de behuizing ontvangen en blijft de juiste koeling hebben. Als de ventilator uitvalt, moet de PCM direct worden vervangen.
* Voordat u de PCM verwijdert, verbreekt u de verbinding met de stroom van de PCM door de hoofd schakelaar (indien aanwezig) uit te scha kelen of door de stroom kabel fysiek te verwijderen. Dit geeft een waarschuwing voor het systeem dat het afsluiten van de stroom is afgesloten.
* Zorg ervoor dat de andere PCM functioneel is voor een voortdurende systeem bewerking voordat de defecte PCM wordt vervangen. Een defecte PCM moet zo snel mogelijk worden vervangen door een volledig werkende PCM.
* Het verwijderen van PCM-modules duurt slechts enkele minuten, maar deze moet binnen 10 minuten worden voltooid als de defecte PCM wordt verwijderd om te voor komen dat de service wordt oververhit.
* De vervangende PCM-modules van 764 W bevatten geen module back-upbatterij. U moet de accu uit de defecte PCM verwijderen en vervolgens in de vervangings module plaatsen voordat u de vervanging uitvoert. Zie [een back-upbatterij module verwijderen en invoegen](storsimple-8000-battery-replacement.md)voor meer informatie.

## <a name="remove-a-pcm"></a>Een PCM verwijderen
Volg deze instructies wanneer u klaar bent voor het verwijderen van een voeding en koel module (PCM) van uw Microsoft Azure StorSimple-apparaat.

> [!NOTE]
> Voordat u uw PCM verwijdert, controleert u of u een juiste vervanging hebt (764 W voor de primaire behuizing of 580 W voor de EBOD Enclosure).

#### <a name="to-remove-a-pcm"></a>Een PCM verwijderen
1. Klik in de klassieke Azure-Portal op **instellingen > de status van > hardware te controleren**. Controleer de status van de PCM-onderdelen onder **gedeelde onderdelen** om te identificeren welke PCM is mislukt:
   
   * Als een voeding in PCM 0 is mislukt, wordt de status van de **voeding in PCM 0** rood weer geven.
   * Als een voeding in PCM 1 mislukt, wordt de status van de **voeding in PCM 1** rood weer geven.
   * Als de ventilator in PCM 1 is mislukt, wordt de status van **koeling 0 voor PCM 0** of **koeling 1 voor PCM 0** rood.
2. Zoek de defecte PCM op de achterkant van de primaire behuizing. Als u een 8600-model gebruikt, identificeert u de primaire behuizing door te kijken naar het id-nummer van de systeem eenheid die wordt weer gegeven op de LED voor het voor paneel. De standaard eenheid-ID die wordt weer gegeven op de primaire behuizing is **00**, terwijl de standaard eenheid-id die wordt weer gegeven in de EBOD Enclosure **01** is. In het volgende diagram en tabel wordt het voor paneel van de LED-weer gave uitgelegd.
   
    ![Systeem-ID op front OPS-paneel](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Afbeelding 1** Voor paneel van het apparaat  
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Knop Dempen |
   | 2 |Systeem energie |
   | 3 |Module fout |
   | 4 |Logische fout |
   | 5 |Eenheid-ID weer geven |
3. De bewakings indicator-Led's aan de achterkant van de primaire behuizing kunnen ook worden gebruikt om de defecte PCM te identificeren. Raadpleeg het volgende diagram en deze tabel om te begrijpen hoe u de Led's kunt gebruiken om de defecte PCM te vinden. Als de LED die overeenkomt met de **ventilator bijvoorbeeld niet** kan branden, is de ventilator mislukt. Ook als de LED die overeenkomt met **AC mislukt** , de voeding is mislukt. 
   
    ![Backplane van bewakings indicator-Led's van Device PCM](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Afbeelding 2** Achterkant van PCM met indicator-Led's
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Netstroom storing |
   | 2 |Fout met ventilator |
   | 3 |Accu fout |
   | 4 |PCM OK |
   | 5 |Stroom storing in gelijkstroom |
   | 6 |Accu in orde |
4. Raadpleeg het volgende diagram van de achterkant van het StorSimple-apparaat om de defecte PCM-module te vinden. PCM 0 bevindt zich aan de linkerkant en PCM 1 bevindt zich aan de rechter kant. In de volgende tabel worden de modules uitgelegd.
   
     ![Backplane van modules voor primaire behuizing van apparaten](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Afbeelding 3** Terug van apparaat met invoeg modules 
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
5. Schakel de defecte PCM uit en Verbreek de verbinding met de stroom kabel. U kunt nu de PCM verwijderen.
6. Begrijpt de vergren deling en de zijkant van de PCM-ingang tussen uw duim en forefinger en deel ze samen om de ingang te openen.
   
    ![PCM-ingang openen](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Afbeelding 4** De PCM-ingang openen
7. Greep de koppeling en verwijder de PCM.
   
    ![De PCM van het apparaat verwijderen](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Afbeelding 5** De PCM verwijderen

## <a name="install-a-replacement-pcm"></a>Een vervangende PCM installeren
Volg deze instructies voor het installeren van een PCM op uw StorSimple-apparaat. Zorg ervoor dat u de module back-upbatterij hebt ingevoegd voordat u de vervangende PCM installeert (alleen van toepassing op 764 W PCMs). Zie [een back-upbatterij module verwijderen en invoegen](storsimple-8000-battery-replacement.md)voor meer informatie.

#### <a name="to-install-a-pcm"></a>Een PCM installeren
1. Controleer of u de juiste vervangende PCM hebt voor deze behuizing. De primaire behuizing vereist een 764 W PCM en de EBOD Enclosure heeft een 580 W PCM nodig. U moet niet proberen om de 580 W PCM in de primaire behuizing te gebruiken of de PCM van 764 W in de EBOD-behuizing. De volgende afbeelding laat zien waar deze informatie wordt geïdentificeerd op het label dat op de PCM is aangebracht.
   
    ![Apparaat-PCM-label](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Afbeelding 6** PCM-label
2. Controleer op beschadiging van de behuizing en besteed bijzondere aandacht aan de connectors. 
   
   > [!NOTE]
   > **Installeer de module niet als een connector pincode is gebogen.**
   > 
   > 
3. Verplaats met de PCM-ingang in de open positie de module naar de behuizing.
   
    ![De PCM van het apparaat wordt geïnstalleerd](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Afbeelding 7** De PCM installeren
4. Sluit de PCM-ingang hand matig. U moet een klik horen als de hendel van de handle.
   
   > [!NOTE]
   > Om ervoor te zorgen dat de connector-pincodes zijn ingeschakeld, kunt u de Tug voorzichtig door nemen zonder de hendel vrij te brengen. Als de PCM-dia's uit het bereik valt, houdt dit in dat de vergren deling is gesloten voordat de connectors zijn ingeschakeld.
   
5. Verbind de stroom kabels met de voedings bron en de PCM.
6. De Bales van de stam verlichting beveiligen.
7. Schakel de PCM in.
8. Controleer of de vervanging is geslaagd: Ga in de Azure Portal van uw StorSimple Apparaatbeheer-service naar het apparaat en klik vervolgens op **instellingen > de status van > hardware te controleren**. Onder de **gedeelde onderdelen** moet de status van de PCM groen zijn.
   
   > [!NOTE]
   > Het kan enkele minuten duren voordat de vervangende PCM volledig is geïnitialiseerd.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [vervangen van StorSimple](storsimple-8000-hardware-component-replacement.md).

