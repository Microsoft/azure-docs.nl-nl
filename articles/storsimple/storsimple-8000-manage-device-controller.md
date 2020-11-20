---
title: StorSimple 8000 Series-controllers beheren | Microsoft Docs
description: Meer informatie over het stoppen, opnieuw opstarten, afsluiten of opnieuw instellen van uw StorSimple-apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956990"
---
# <a name="manage-your-storsimple-device-controllers"></a>Uw StorSimple-Apparaatbeheer beheren

## <a name="overview"></a>Overzicht

In deze zelf studie worden de verschillende bewerkingen beschreven die kunnen worden uitgevoerd op uw StorSimple-apparaat. De controllers in uw StorSimple-apparaat zijn redundante (peer) controllers in een actief-passieve configuratie. Op een bepaald moment is slechts één controller actief en worden alle schijf-en netwerk bewerkingen verwerkt. De andere controller bevindt zich in een passieve modus. Als de actieve controller mislukt, wordt de passieve controller automatisch actief.

Deze zelf studie bevat stapsgewijze instructies voor het beheren van de apparaats controllers met behulp van:

* De Blade **controllers** voor uw apparaat in de StorSimple-Apparaatbeheer service.
* Windows PowerShell voor StorSimple.

U wordt aangeraden de apparaats controllers te beheren via de StorSimple Apparaatbeheer-service. Als een actie alleen kan worden uitgevoerd met behulp van Windows PowerShell voor StorSimple, maakt de zelf studie hiervan een notitie.

Na het lezen van deze zelf studie kunt u het volgende doen:

* Een StorSimple apparaat-controller opnieuw opstarten of afsluiten
* Een StorSimple-apparaat afsluiten
* De fabrieks instellingen van uw StorSimple-apparaat herstellen

## <a name="restart-or-shut-down-a-single-controller"></a>Een enkele controller opnieuw opstarten of afsluiten
Het opnieuw opstarten of afsluiten van de controller is niet vereist als onderdeel van de normale systeem bewerking. Afsluit bewerkingen voor één apparaat-controller zijn alleen gebruikelijk in gevallen waarin het hardwareapparaat van een defecte apparaat vervanging vereist. Het kan ook zijn dat het opnieuw opstarten van de controller vereist is in een situatie waarin de prestaties worden beïnvloed door overmatig gebruik van het geheugen of een defecte controller. Mogelijk moet u ook een controller opnieuw opstarten nadat de vervanging van de controller is geslaagd, als u de vervangen controller wilt inschakelen en testen.

Het opnieuw starten van een apparaat leidt niet tot uitval voor verbonden initiators, ervan uitgaande dat de passieve controller beschikbaar is. Als een passieve controller niet beschikbaar of uitgeschakeld is, kan het opnieuw opstarten van de actieve controller leiden tot onderbrekingen in de service en tot downtime.

> [!IMPORTANT]
> * **Een actieve controller mag nooit fysiek worden verwijderd, omdat dit zou leiden tot verlies van redundantie en een verhoogd risico op uitval tijd.**
> * De volgende procedure is alleen van toepassing op het fysieke StorSimple-apparaat. Zie [werken met het Cloud apparaat](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance)voor meer informatie over het starten, stoppen en opnieuw opstarten van de StorSimple Cloud Appliance.

U kunt één apparaat-controller opnieuw opstarten of afsluiten via de Azure Portal van de StorSimple Apparaatbeheer-service of Windows PowerShell voor StorSimple.

Voer de volgende stappen uit om uw Apparaatbeheer te beheren vanuit de Azure Portal.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Een controller in Azure Portal opnieuw opstarten of afsluiten
1. Ga in de StorSimple-Apparaatbeheer service naar **apparaten**. Selecteer uw apparaat in de lijst met apparaten. 

    ![Kies een apparaat](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Ga naar **instellingen > controllers**.
   
    ![Controleren of StorSimple-apparaten in orde zijn](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Controleer op de Blade **controllers** of de status van beide controllers op het apparaat in **orde** is. Selecteer een controller, klik met de rechter muisknop en selecteer **opnieuw opstarten** of **Afsluiten**.

    ![Selecteer opnieuw opstarten of StorSimple-hostcontrollers afsluiten](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Er wordt een taak gemaakt om de controller opnieuw op te starten of af te sluiten en er worden toepasselijke waarschuwingen weer gegeven, indien van toepassing. Als u het opnieuw opstarten of afsluiten wilt controleren, gaat u naar **service logboeken voor activiteiten >** en filtert u op para meters die specifiek zijn voor uw service. Als een controller is afgesloten, moet u de aan/uit-knop pushen om de controller in te scha kelen om deze in te scha kelen.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Een controller in Windows PowerShell voor StorSimple opnieuw opstarten of afsluiten
Voer de volgende stappen uit om een enkele controller op uw StorSimple-apparaat af te sluiten of opnieuw op te starten via de Windows PowerShell voor StorSimple.

1. Toegang tot het apparaat via de seriële console of een Telnet-sessie vanaf een externe computer. Als u verbinding wilt maken met controller 0 of controller 1, volgt u de stappen in [putty gebruiken om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang**.
3. In het banner bericht noteert u de controller waarmee u verbinding hebt (controller 0 of controller 1) en of dit de actieve of de passieve controller (standby) is.
   
   * Als u één controller wilt afsluiten, typt u het volgende bij de prompt:
     
       `Stop-HcsController`
     
       Hiermee wordt de controller die u hebt verbonden, afgesloten. Als u de actieve controller stopt, voert het apparaat een failover uit naar de passieve controller.

   * Als u een controller opnieuw wilt opstarten, typt u het volgende bij de opdracht prompt:
     
       `Restart-HcsController`
     
       Hiermee wordt de controller waarmee u verbinding hebt gemaakt, opnieuw opgestart. Als u de actieve controller opnieuw opstart, wordt een failover naar de passieve controller uitgevoerd voordat de computer opnieuw wordt opgestart.

## <a name="shut-down-a-storsimple-device"></a>Een StorSimple-apparaat afsluiten

In deze sectie wordt uitgelegd hoe u een actief of een mislukt StorSimple-apparaat afsluit vanaf een externe computer. Een apparaat wordt uitgeschakeld nadat beide controllers zijn afgesloten. Een apparaat wordt afgesloten wanneer het apparaat fysiek wordt verplaatst of buiten gebruik wordt gemaakt.

> [!IMPORTANT]
> Controleer voordat u het apparaat uitschakelt de status van de onderdelen van het apparaat. Navigeer naar uw apparaat en klik vervolgens op **instellingen > hardware-status**. Controleer op de Blade **status en hardware Health** of de LED-status van alle onderdelen groen is. Alleen een goed apparaat heeft een groene status. Als uw apparaat wordt afgesloten om een defect onderdeel te vervangen, ziet u een fout (rood) of een gedegradeerde (gele) status voor de respectievelijke onderdelen.


#### <a name="to-shut-down-a-storsimple-device"></a>Een StorSimple-apparaat afsluiten

1. Gebruik de procedure voor het [opnieuw opstarten of afsluiten van een controller](#restart-or-shut-down-a-single-controller) om de passieve controller op het apparaat te identificeren en af te sluiten. U kunt deze bewerking uitvoeren in de Azure Portal of in Windows PowerShell voor StorSimple.
2. Herhaal de bovenstaande stap om de actieve controller af te sluiten.
3. U moet nu kijken naar het achtergrond vlak van het apparaat. Nadat de twee controllers volledig zijn afgesloten, moeten de status-Led's op beide controllers rood knip peren. Als u het apparaat op dit moment volledig wilt uitschakelen, spiegelt u de voedings switches van zowel de voeding als de koel modules (PCMs) in de stand-by. Hiermee schakelt u het apparaat uit.

## <a name="reset-the-device-to-factory-default-settings"></a>Het apparaat terugzetten naar de fabrieksinstellingen

> [!IMPORTANT]
> Als u de standaard instellingen van uw apparaat wilt herstellen, neemt u contact op met Microsoft Ondersteuning. De hieronder beschreven procedure mag alleen worden gebruikt in combi natie met Microsoft Ondersteuning.

In deze procedure wordt beschreven hoe u uw Microsoft Azure StorSimple apparaat opnieuw instelt op de standaard fabrieks instellingen met behulp van Windows PowerShell voor StorSimple.
Als u een apparaat opnieuw instelt, worden alle gegevens en instellingen van het hele cluster standaard verwijderd.

Voer de volgende stappen uit om de fabrieks instellingen van uw Microsoft Azure StorSimple apparaat opnieuw in te stellen:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Het apparaat opnieuw instellen op de standaard instellingen in Windows PowerShell voor StorSimple
1. Toegang tot het apparaat via de seriële console. Controleer het banner bericht om er zeker van te zijn dat u verbonden bent met de **actieve** controller.
2. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang**.
3. Typ bij de prompt de volgende opdracht om het hele cluster opnieuw in te stellen, waarbij alle gegevens, meta data en controller instellingen worden verwijderd:
   
    `Reset-HcsFactoryDefault`
   
    Als u in plaats daarvan één controller opnieuw wilt instellen, gebruikt u de cmdlet  [Reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) met de `-scope` para meter.)
   
    Het systeem wordt meerdere keren opnieuw opgestart. U ontvangt een melding wanneer de herstel bewerking is voltooid. Afhankelijk van het systeem model kan het 45-60 minuten duren voor een 8100-apparaat en 60-90 minuten voor een 8600 om dit proces te volt ooien.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Vragen en antwoorden over het beheren van Apparaatbeheer
In deze sectie hebben we enkele van de veelgestelde vragen over het beheer van StorSimple-apparaten samenvatten.

**Nils.** Wat gebeurt er als beide controllers op mijn apparaat in orde zijn en zijn ingeschakeld en de actieve controller opnieuw opstarten of afsluiten?

**Één.** Als beide controllers op het apparaat in orde zijn en zijn ingeschakeld, wordt u gevraagd om bevestiging. U kunt kiezen uit:

* **De actieve controller opnieuw starten** : u wordt gewaarschuwd dat het apparaat een failover naar de passieve controller heeft veroorzaakt door het opnieuw opstarten van een actieve controller. De controller wordt opnieuw opgestart.
* **Een actieve controller afsluiten** : u wordt gewaarschuwd dat het afsluiten van een actieve controller resulteert in uitval tijd. U moet ook de aan/uit-knop op het apparaat pushen om de controller in te scha kelen.

**Nils.** Wat gebeurt er als de passieve controller op mijn apparaat niet beschikbaar is of is uitgeschakeld en de actieve controller opnieuw wordt opgestart of afgesloten?

**Één.** Als de passieve controller op het apparaat niet beschikbaar is of is uitgeschakeld, en u ervoor kiest om het volgende te doen:

* **De actieve controller opnieuw starten** : u wordt gewaarschuwd dat het voortzetten van de bewerking resulteert in een tijdelijke onderbreking van de service en u wordt gevraagd om bevestiging.
* **Een actieve controller afsluiten** : u ontvangt een melding dat de bewerking wordt voortgezet en de uitval tijd wordt teruggebracht. U moet ook de aan/uit-knop op een of beide controllers pushen om het apparaat in te scha kelen. U wordt gevraagd om bevestiging.

**Nils.** Wanneer wordt het opnieuw opstarten of afsluiten van de controller niet uitgevoerd?

**Één.** Het opnieuw opstarten of afsluiten van een controller kan mislukken als:

* Er wordt een update van het apparaat uitgevoerd.
* Het opnieuw opstarten van de controller wordt al uitgevoerd.
* Er wordt al een speld afgesloten.

**Nils.** Hoe kunt u achterhalen of een controller opnieuw is opgestart of afgesloten?

**Één.** U kunt de status van de controller controleren op de Blade Controller. De controller status geeft aan of een controller bezig is met opnieuw opstarten of afsluiten. Daarnaast bevat de Blade **waarschuwingen** een informatieve waarschuwing als de controller opnieuw wordt opgestart of wordt afgesloten. De bewerkingen voor het opnieuw opstarten en afsluiten van de controller worden ook vastgelegd in de activiteiten Logboeken. Ga voor meer informatie over activiteiten logboeken naar [de activiteiten logboeken weer geven](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**Nils.** Zijn er gevolgen voor de I/O als gevolg van een failover van een controller?

**Één.** De TCP-verbindingen tussen initia tors en de actieve controller worden opnieuw ingesteld als gevolg van een failover van de controller, maar worden opnieuw ingesteld wanneer de passieve controller de bewerking afneemt. Er is mogelijk een tijdelijke (minder dan 30 seconden) pauze in de I/O-activiteit tussen initia tors en het apparaat tijdens de uitvoering van deze bewerking.

**Nils.** Hoe kan ik retour neren van mijn controller naar service nadat deze is afgesloten en verwijderd?

**Één.** Als u een controller naar de service wilt retour neren, moet u deze in het chassis invoegen zoals beschreven in [een controller module vervangen op uw StorSimple-apparaat](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Volgende stappen
* Als u problemen ondervindt met de StorSimple-apparaten die u niet kunt oplossen met behulp van de procedures in deze zelf studie, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).
* Ga voor meer informatie over het gebruik van de StorSimple Apparaatbeheer-service naar [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).