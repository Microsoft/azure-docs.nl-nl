---
title: Een StorSimple 8000 Series-controller vervangen | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u een of beide controller modules verwijdert en vervangt op het apparaat met de StorSimple 8000-serie.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 9d8b75c48da2bb13d843258ead378d3e849da951
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514072"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Een controller module op uw StorSimple-apparaat vervangen
## <a name="overview"></a>Overzicht
In deze zelf studie wordt uitgelegd hoe u een of beide controller modules in een StorSimple-apparaat kunt verwijderen en vervangen. Ook wordt de onderliggende logica beschreven voor de vervangings scenario's met één en dubbele controller.

> [!NOTE]
> Voordat de vervanging van een controller wordt uitgevoerd, raden we u aan uw controller firmware altijd bij te werken naar de nieuwste versie.
> 
> Om beschadiging van uw StorSimple-apparaat te voor komen, moet u de controller niet uitwerpen totdat de Led's als een van de volgende worden weer gegeven:
> 
> * Alle lichten zijn uitgeschakeld.
> * LED voor drie, ![ groen vinkje ](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) en ![ rood kruis pictogram ](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) zijn knipperend en de LED 7 is **ingeschakeld**.


In de volgende tabel worden de ondersteunde scenario's voor het vervangen van een controller weer gegeven.

| Case | Vervangings scenario | Toepasselijke procedure |
|:--- |:--- |:--- |
| 1 |Een controller heeft de status mislukt, de andere controller is in orde en actief. |[Vervanging van één controller](#replace-a-single-controller), waarmee de [logica achter één vervangende controller](#single-controller-replacement-logic)wordt beschreven, evenals de [vervangings stappen](#single-controller-replacement-steps). |
| 2 |Beide controllers zijn mislukt en moeten worden vervangen. Het chassis, de schijven en de schijf behuizing zijn in orde. |[Vervanging van dubbele controllers](#replace-both-controllers), waarmee de [logica achter een vervanging van dubbele controllers](#dual-controller-replacement-logic)wordt beschreven, evenals de [vervangings stappen](#dual-controller-replacement-steps). |
| 3 |Controllers van hetzelfde apparaat of van verschillende apparaten worden omgewisseld. Het chassis, de schijven en de schijf behuizingen zijn in orde. |Er wordt een waarschuwings bericht met een niet-overeenkomende sleuf weer gegeven. |
| 4 |Er ontbreekt een controller en de andere controller mislukt. |[Vervanging van dubbele controllers](#replace-both-controllers), waarmee de [logica achter een vervanging van dubbele controllers](#dual-controller-replacement-logic)wordt beschreven, evenals de [vervangings stappen](#dual-controller-replacement-steps). |
| 5 |Een of beide controllers zijn mislukt. U hebt geen toegang tot het apparaat via de seriële console of externe communicatie van Windows Power shell. |[Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor een hand matige vervanging van de controller. |
| 6 |De controllers hebben een andere build-versie, die kan worden veroorzaakt door:<ul><li>Controllers hebben een andere software versie.</li><li>Controllers hebben een andere firmware versie.</li></ul> |Als de versies van de controller software verschillend zijn, detecteert de vervangings logica dat en werkt de software versie op de vervangende controller.<br><br>Als de firmware versies van de controller verschillen en de oude firmware versie **niet** automatisch wordt geüpgraded, wordt er een waarschuwings bericht weer gegeven in de Azure Portal. U moet zoeken naar updates en de firmware-updates installeren.</br></br>Als de firmware versies van de controller verschillend zijn en de oude firmware versie automatisch wordt geüpgraded, detecteert de vervangings logica van de controller dit en nadat de controller is gestart, wordt de firmware automatisch bijgewerkt. |

U moet een controller module verwijderen als deze is mislukt. Een of beide van de controller modules kunnen mislukken, wat kan leiden tot een vervanging van één controller of het vervangen van dubbele controllers. Zie het volgende voor een vervangings procedure en de logica achter deze procedures:

* [Eén controller vervangen](#replace-a-single-controller)
* [Beide controllers vervangen](#replace-both-controllers)
* [Een controller verwijderen](#remove-a-controller)
* [Een controller invoegen](#insert-a-controller)
* [De actieve controller op het apparaat identificeren](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Voordat u een controller verwijdert en vervangt, raadpleegt u de beveiligings informatie in [StorSimple vervanging](storsimple-8000-hardware-component-replacement.md)van het hardware-onderdeel.
> 
> 

## <a name="replace-a-single-controller"></a>Eén controller vervangen
Wanneer een van de twee controllers op het Microsoft Azure StorSimple apparaat is mislukt, een storing heeft of ontbreekt, moet u één controller vervangen.

### <a name="single-controller-replacement-logic"></a>Vervangings logica voor één controller
In één vervangende controller moet u eerst de mislukte controller verwijderen. (De resterende controller in het apparaat is de actieve controller.) Wanneer u de vervangende controller invoegt, worden de volgende acties uitgevoerd:

1. De vervangende controller begint direct met communiceren met het StorSimple-apparaat.
2. Er wordt een moment opname van de virtuele harde schijf (VHD) voor de actieve controller gekopieerd op de vervangende controller.
3. De moment opname wordt gewijzigd, zodat de nieuwe controller wordt herkend als een stand-by-controller.
4. Wanneer de wijzigingen zijn voltooid, wordt de vervangende controller gestart als de stand-by-controller.
5. Wanneer beide controllers worden uitgevoerd, is het cluster online.

### <a name="single-controller-replacement-steps"></a>Vervangings stappen voor één controller
Voer de volgende stappen uit als een van de controllers in uw Microsoft Azure StorSimple-apparaat mislukt. (De andere controller moet actief en actief zijn. Als beide controllers mislukken of niet goed functioneren, gaat u naar [dubbele controller vervangings stappen](#dual-controller-replacement-steps).)

> [!NOTE]
> Het kan 30 tot 45 minuten duren voordat de controller opnieuw wordt opgestart en volledig is hersteld vanuit de vervangings procedure voor één controller. De totale tijd voor de volledige procedure, inclusief het koppelen van de kabels, is ongeveer 2 uur.


#### <a name="to-remove-a-single-failed-controller-module"></a>Eén defecte controller module verwijderen
1. Ga in het Azure Portal naar de StorSimple Apparaatbeheer-service, klik op **apparaten** en klik vervolgens op de naam van het apparaat dat u wilt bewaken.
2. Ga naar **Monitor > hardware Health**. De status van controller 0 of controller 1 moet rood zijn, wat duidt op een storing.
   
   > [!NOTE]
   > De mislukte controller in één controller vervanging is altijd een stand-by-controller.
   
3. Gebruik afbeelding 1 en de volgende tabel om de module mislukte controller te vinden.
   
    ![Backplane van modules voor primaire behuizing van apparaten](./media/storsimple-controller-replacement/IC740994.png)
   
    **Afbeelding 1** Terug van StorSimple-apparaat
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
4. Verwijder alle verbonden netwerk kabels van de gegevens poorten op de controller die is mislukt. Als u een 8600-model gebruikt, verwijdert u ook de SAS-kabels die de controller verbinden met de EBOD-controller.
5. Volg de stappen in [een controller verwijderen](#remove-a-controller) om de mislukte controller te verwijderen.
6. Installeer de fabrieks vervanging in dezelfde sleuf waarvan de mislukte controller is verwijderd. Hiermee wordt de vervangings logica van één controller geactiveerd. Zie voor meer informatie een [vervangings logica voor één controller](#single-controller-replacement-logic).
7. Wanneer de vervangings logica van één controller op de achtergrond wordt uitgevoerd, sluit u de kabels opnieuw aan. Zorg ervoor dat u alle kabels precies zo verbindt als voor de vervanging.
8. Nadat de controller opnieuw is opgestart, controleert u de status van de **controller** en de **cluster status** in de Azure Portal om te controleren of de controller weer de status in orde heeft en de modus stand-by heeft.

> [!NOTE]
> Als u het apparaat bewaakt via de seriële console, ziet u mogelijk meerdere opnieuw opstarten wanneer de controller wordt hersteld van de vervangings procedure. Wanneer het menu van de seriële console wordt weer gegeven, weet u zeker dat de vervanging is voltooid. Als het menu niet binnen twee uur na het starten van de vervanging van de controller wordt weer gegeven, neemt u [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).
>
> Als u update 4 Start, kunt u ook de cmdlet `Get-HCSControllerReplacementStatus` in de Windows Power shell-interface van het apparaat gebruiken om de status van het vervangings proces van de controller te controleren.
> 

## <a name="replace-both-controllers"></a>Beide controllers vervangen
Wanneer beide controllers op het Microsoft Azure StorSimple apparaat zijn mislukt, niet goed functioneren of ontbreken, moet u beide controllers vervangen. 

### <a name="dual-controller-replacement-logic"></a>Vervangings logica voor dubbele controller
Bij een vervanging van dubbele controllers verwijdert u eerst beide mislukte controllers en vervolgens plaatst u vervangingen. Wanneer de twee vervangende controllers worden ingevoegd, worden de volgende acties uitgevoerd:

1. De vervangende controller in sleuf 0 controleert het volgende:
   
   1. Gebruikt de huidige versie van de firmware en software?
   2. Maakt het deel uit van het cluster?
   3. Is de peer-controller actief en is deze geclusterd?
      
      Als geen van deze voor waarden waar is, zoekt de controller naar de laatste dagelijkse back-up (gevonden in de **nonDOMstorage** op station S). De controller kopieert de laatste moment opname van de VHD van de back-up.
2. De controller in sleuf 0 gebruikt de moment opname voor afbeelding zelf.
3. Ondertussen wacht de controller in sleuf 1 op controller 0 om de installatie kopie te volt ooien en te starten.
4. Nadat controller 0 is gestart, detecteert controller 1 het cluster dat is gemaakt door controller 0, dat de vervangings logica van één controller activeert. Zie voor meer informatie een [vervangings logica voor één controller](#single-controller-replacement-logic).
5. Daarna worden beide controllers uitgevoerd en wordt het cluster online gezet.

> [!IMPORTANT]
> Nadat het StorSimple-apparaat is geconfigureerd, is het essentieel dat u een hand matige back-up van het apparaat maakt nadat u een dubbele controller hebt vervangen. Dagelijkse configuratie back-ups van apparaten worden pas geactiveerd nadat er 24 uur zijn verstreken. Werk met [Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om een hand matige back-up van uw apparaat te maken.


### <a name="dual-controller-replacement-steps"></a>Vervangings stappen voor dubbele controllers
Deze werk stroom is vereist wanneer beide controllers in uw Microsoft Azure StorSimple-apparaat zijn mislukt. Dit kan gebeuren in een Data Center waarin het koel systeem niet meer werkt, en als gevolg hiervan mislukken beide controllers binnen een korte periode. Afhankelijk van of het StorSimple-apparaat is uitgeschakeld of ingeschakeld, en of u een 8600-of 8100-model gebruikt, is een andere set stappen vereist.

> [!IMPORTANT]
> Het kan 45 minuten tot 1 uur duren voordat de controller opnieuw wordt opgestart en volledig wordt hersteld van een vervangings procedure met dubbele controller. De totale tijd voor de volledige procedure, inclusief het koppelen van de kabels, is ongeveer 2,5 uur.

#### <a name="to-replace-both-controller-modules"></a>Beide controller modules vervangen
1. Als het apparaat is uitgeschakeld, slaat u deze stap over en gaat u verder met de volgende stap. Als het apparaat is ingeschakeld, schakelt u het apparaat uit.
   
   1. Als u een 8600-model gebruikt, schakelt u eerst de primaire behuizing uit en schakelt u de EBOD-behuizing uit.
   2. Wacht totdat het apparaat volledig is afgesloten. Alle Led's aan de achterkant van het apparaat worden uitgeschakeld.
2. Verwijder alle netwerk kabels die zijn verbonden met de gegevens poorten. Als u een 8600-model gebruikt, verwijdert u ook de SAS-kabels die de primaire behuizing verbinden met de EBOD-behuizing.
3. Verwijder beide controllers van het StorSimple-apparaat. Zie [een controller verwijderen](#remove-a-controller)voor meer informatie.
4. Plaats eerst de fabrieks vervanging voor controller 0 en plaats vervolgens controller 1. Zie [een controller invoegen](#insert-a-controller)voor meer informatie. Hiermee wordt de vervangings logica voor dubbele controllers geactiveerd. Zie [vervangings logica voor dubbele controllers](#dual-controller-replacement-logic)voor meer informatie.
5. Wanneer de vervangings logica van de controller op de achtergrond wordt uitgevoerd, sluit u de kabels opnieuw aan. Zorg ervoor dat u alle kabels precies zo verbindt als voor de vervanging. Raadpleeg de gedetailleerde instructies voor uw model in de sectie kabel uw apparaat van [uw StorSimple 8100-apparaat installeren](storsimple-8100-hardware-installation.md) of [installeer uw StorSimple 8600-apparaat](storsimple-8600-hardware-installation.md).
6. Schakel het StorSimple-apparaat in. Als u een 8600-model gebruikt:
   
   1. Zorg ervoor dat de EBOD-behuizing eerst is ingeschakeld.
   2. Wacht tot de EBOD-behuizing actief is.
   3. Schakel de primaire behuizing in.
   4. Nadat de eerste controller opnieuw is opgestart en de status in orde heeft, wordt het systeem uitgevoerd.
      
      > [!NOTE]
      > Als u het apparaat bewaakt via de seriële console, ziet u mogelijk meerdere opnieuw opstarten wanneer de controller wordt hersteld van de vervangings procedure. Wanneer het menu seriële console wordt weer gegeven, weet u dat de vervanging is voltooid. Als het menu niet binnen 2,5 uur na het starten van de vervanging van de controller wordt weer gegeven, neemt u [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Een controller verwijderen
Gebruik de volgende procedure om een defecte controller module te verwijderen van uw StorSimple-apparaat.

> [!NOTE]
> De volgende illustraties zijn voor controller 0. Voor controller 1 worden deze omgedraaid.


#### <a name="to-remove-a-controller-module"></a>Een controller module verwijderen
1. Begrijpt de module hendel tussen uw duim en forefinger.
2. Neem voorzichtig uw duim-en forefinger op om de vergren deling van de controller op te heffen.
   
    ![Vergren deling van controller opheffen](./media/storsimple-controller-replacement/IC741047.png)
   
    **Afbeelding 2** Vergren deling van controller opheffen
3. Gebruik de hendel als een ingang om de controller uit het chassis te verschuiven.
   
    ![Schuif regelaar buiten chassis](./media/storsimple-controller-replacement/IC741048.png)
   
    **Afbeelding 3** De controller buiten het chassis schuiven

## <a name="insert-a-controller"></a>Een controller invoegen
Gebruik de volgende procedure om een door de fabriek geleverde controller module te installeren nadat u een defecte module hebt verwijderd van uw StorSimple-apparaat.

#### <a name="to-install-a-controller-module"></a>Een controller module installeren
1. Controleer of er schade toebrengt aan de interface connectors. Installeer de module niet als een van de connector pincodes is beschadigd of gebogen is.
2. Schuif de controller module in het chassis terwijl de vergren deling volledig is losgelaten.
   
    ![Schuif regelaar naar chassis](./media/storsimple-controller-replacement/IC741053.png)
   
    **Afbeelding 4** Schuif regelaar in het chassis
3. Wanneer de controller module is ingevoegd, sluit u de vergren deling en gaat u door met het pushen van de controller module naar het chassis. De vergren deling neemt de controller in de hand.
   
    ![Vergren deling van controller sluiten](./media/storsimple-controller-replacement/IC741054.png)
   
    **Afbeelding 5** De vergren deling van de controller sluiten
4. U bent klaar wanneer de vergren deling op locatie wordt toegepast. De LED **OK** moet nu zijn ingeschakeld.
   
   > [!NOTE]
   > Het kan tot vijf minuten duren voor de controller en de LED om te activeren.
  
5. Als u wilt controleren of de vervanging is geslaagd, gaat u in het Azure Portal naar uw apparaat en vervolgens navigeert u naar de status van de hardware **controleren** en controleert u of de  >  controller 0 en controller 1 in orde zijn (status is groen).

## <a name="identify-the-active-controller-on-your-device"></a>De actieve controller op het apparaat identificeren
Er zijn veel situaties, zoals het voor de eerste keer registreren van apparaten of het vervangen van de controller, waarvoor u de actieve controller op een StorSimple-apparaat moet zoeken. De actieve controller verwerkt alle firmware-en netwerk bewerkingen van de schijf. U kunt een van de volgende methoden gebruiken om de actieve controller te identificeren:

* [De Azure Portal gebruiken om de actieve controller te identificeren](#use-the-azure-portal-to-identify-the-active-controller)
* [Windows PowerShell voor StorSimple gebruiken om de actieve controller te identificeren](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Controleer het fysieke apparaat om de actieve controller te identificeren](#check-the-physical-device-to-identify-the-active-controller)

Elk van deze procedures wordt hierna beschreven.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>De Azure Portal gebruiken om de actieve controller te identificeren
Ga in het Azure Portal naar het apparaat en **Controleer** vervolgens de status van de  >  **Hardware** en schuif naar het gedeelte **controllers** . Hier kunt u controleren welke controller actief is.

![Actieve controller in Azure Portal identificeren](./media/storsimple-controller-replacement/IC752072.png)

**Afbeelding 6** Azure Portal de actieve controller weer geven

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Windows PowerShell voor StorSimple gebruiken om de actieve controller te identificeren
Wanneer u uw apparaat opent via de seriële console, wordt een banner bericht weer gegeven. Het banner bericht bevat basis informatie over het apparaat, zoals het model, de naam, de geïnstalleerde software versie en de status van de controller die u wilt openen. In de volgende afbeelding ziet u een voor beeld van een banner bericht:

![Serie banner bericht](./media/storsimple-controller-replacement/IC741098.png)

**Afbeelding 7** Banner bericht met controller 0 als actief

U kunt het banner bericht gebruiken om te bepalen of de controller waarmee u verbonden bent, actief of passief is.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Controleer het fysieke apparaat om de actieve controller te identificeren
Als u de actieve controller op het apparaat wilt identificeren, zoekt u de blauwe LED boven de DATA 5-poort op de achterkant van de primaire behuizing.

Als deze LED knippert, is de controller actief en de andere controller in de modus stand-by. Gebruik het volgende diagram en de tabel als een hulp.

![Backplane voor primaire behuizing van apparaat met data poorten](./media/storsimple-controller-replacement/IC741055.png)

**Afbeelding 8** Terug van primaire behuizing met gegevens poorten en bewakings-Led's

| Label | Description |
|:--- |:--- |
| 1-6 |GEGEVENS 0 – 5 netwerk poorten |
| 7 |Blauwe LED |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [vervangen van StorSimple](storsimple-8000-hardware-component-replacement.md).

