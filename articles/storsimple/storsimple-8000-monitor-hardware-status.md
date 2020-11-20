---
title: StorSimple 8000 Series-hardware-onderdelen en-status | Microsoft Docs
description: Meer informatie over het bewaken van de hardwareonderdelen van uw StorSimple-apparaat via de StorSimple Apparaatbeheer service.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960356"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>De StorSimple Apparaatbeheer-service gebruiken voor het bewaken van hardware-onderdelen en-status

## <a name="overview"></a>Overzicht
In dit artikel worden de verschillende fysieke en logische onderdelen in uw on-premises StorSimple 8000 Series-apparaat beschreven. Ook wordt uitgelegd hoe u de status van de apparaatstatus bewaakt met behulp van de Blade **status en hardware** -status in de StorSimple-Apparaatbeheer service.

Op de Blade **status en hardware Health** wordt de hardware-status weer gegeven van alle onderdelen van de StorSimple.

Onder de lijst met onderdelen voor 8100 zijn drie secties te beschrijven:

* **Gedeelde onderdelen** : deze zijn geen onderdeel van de controllers, zoals schijf stations, behuizing-, voeding-en koel module (PCM) onderdelen en PCM-Tempe ratuur, lijn spanning en lijn stroom sensors.
* **Onderdelen van controller 0** : de onderdelen die zich bevinden op controller 0, zoals controller, SAS-uitbrei ding en connector, controller temperatuur Sens oren en de verschillende netwerk interfaces.
* **Controller 1-onderdelen** : de onderdelen die controller 1 vormen, vergelijkbaar met die van controller 0.

Een 8600-apparaat heeft extra onderdelen die overeenkomen met de EBOD-behuizing (Extendedity of disks). Onder de lijst met onderdelen bevinden zich vijf secties. Er zijn drie secties die de onderdelen in de primaire behuizing bevatten en identiek zijn aan die van 8100. Er zijn twee extra secties voor de EBOD behuizing waarin wordt beschreven:

* **EBOD controller 0 onderdelen** : de onderdelen die zich bevinden op EBOD Enclosure 0, zoals de EBOD-controller, SAS-uitbrei ding en connector, en controller temperatuur sensors.
* **EBOD controller 1-onderdelen** : de onderdelen die deel uitmaakten van EBOD Enclosure 1, vergelijkbaar met die van EBOD Enclosure 0.
* **Gedeelde onderdelen van EBOD Enclosure** : de onderdelen die aanwezig zijn in de EBOD-behuizing en PCM die geen deel uitmaken van de EBOD-controller.

> [!NOTE]
> **De hardware-status is niet beschikbaar voor een StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>De hardware-status controleren
Voer de volgende stappen uit om de hardware-status van een onderdeel van een apparaat weer te geven:

1. Navigeer naar **apparaten**, selecteer een specifiek StorSimple-apparaat. Ga naar **Monitor > hardware Health**.

    ![Scherm opname van de Blade instellingen van de Apparaatbeheer service. Onder monitor wordt de hardware-status geselecteerd.](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Ga naar de sectie **hardware-onderdelen** en kies uit de beschik bare onderdelen. Klik op het onderdeel label om de lijst uit te vouwen en de status van de verschillende onderdelen van het apparaat weer te geven. Zie de [gedetailleerde onderdelen lijst voor de primaire behuizing](#component-list-for-primary-enclosure-of-storsimple-device) en de [gedetailleerde onderdelen lijst voor de EBOD behuizing](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![Scherm opname van het ene onderdeel dat is geselecteerd in het gedeelte hardware-onderdelen. Een aangrenzende Blade bevat de naam en de status van de onderdelen van dat onderdeel.](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Gebruik het volgende schema voor kleur codering om de status van het onderdeel te interpreteren:
   
   * **Groen vinkje** : Hiermee wordt een gezonde component met de status **OK** aangeduid.
   * **Geel** : Hiermee wordt een gedegradeerd onderdeel met een **waarschuwings** status aangeduid.
   * **Rood uitroep teken** : Hiermee wordt een defect onderdeel aangeduid dat de **status mislukt** heeft.
   * **Wit met zwarte tekst** : Hiermee wordt een onderdeel aangeduid dat niet aanwezig is.
   
   In de volgende scherm afbeelding ziet u een apparaat met onderdelen in **OK**, **waarschuwing** en **fout** status.
       
   ![Scherm afbeelding met drie onderdelen in het gedeelte hardware-onderdelen: een in de status niet in orde, een in een mislukte status en een in een waarschuwings status.](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Als u de **lijst met gedeelde onderdelen** uitbreidt, kunnen we zien dat het NVRAM en het cluster gedegradeerd zijn.

   ![Scherm opname van het item gedeelde onderdelen dat is geselecteerd. Op de Blade ernaast bevinden de N V R A M en het cluster een waarschuwings status.](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Als u de lijst met **Controller 1-onderdelen** uitbreidt, kunnen we zien dat het cluster knooppunt is mislukt.  

   ![Scherm afbeelding van het item controller 1-onderdelen geselecteerd. Het cluster knooppunt bevindt zich in de aangrenzende Blade met de status mislukt.](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Neem contact op met Microsoft Ondersteuning als u een onderdeel tegen komt dat niet in **orde** is. Als waarschuwingen zijn ingeschakeld op uw apparaat, ontvangt u een e-mail waarschuwing. Als u een defect hardwareonderdeel moet vervangen, raadpleegt u [StorSimple hardware-onderdeel vervanging](./storsimple-8000-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Onderdelen lijst voor de primaire behuizing van het StorSimple-apparaat
De volgende tabel bevat een overzicht van de fysieke en logische onderdelen in de primaire behuizing (aanwezig in 8100 en 8600) van uw on-premises StorSimple-apparaat.

| Onderdeel | Module | Type | Locatie | De vervangable-eenheid (FRU) van het veld? | Beschrijving |
| --- | --- | --- | --- | --- | --- |
| Station in sleuf [0-11] |Schijf stations |Fysiek |Gedeeld |Ja |Er wordt één regel weer gegeven voor elk van de SSD-of HDD-schijven in de primaire behuizing. |
| Omgevings temperatuur sensor |Sluit |Fysiek |Gedeeld |Nee |Meet de Tempe ratuur binnen het chassis. |
| Mid-vlieg temperatuur sensor |Sluit |Fysiek |Gedeeld |Nee |Meet de Tempe ratuur van het middel vlak. |
| Hoorbaar alarm |Sluit |Fysiek |Gedeeld |Nee |Hiermee wordt aangegeven of het hoorbare alarm subsysteem binnen het chassis functioneel is. |
| Sluit |Sluit |Fysiek |Gedeeld |Ja |Hiermee wordt de aanwezigheid van een chassis aangegeven. |
| Instellingen voor de behuizing |Sluit |Fysiek |Gedeeld |Nee |Verwijst naar het voor paneel van het chassis. |
| Lijn spanning-Sens oren |PCM |Fysiek |Gedeeld |Nee |Voor talrijke spannings sensors wordt de status weer gegeven. Dit geeft aan of de gemeten spanning binnen de tolerantie valt. |
| Regel huidige Sens oren |PCM |Fysiek |Gedeeld |Nee |De status van een groot aantal regel huidige Sens oren wordt weer gegeven. Dit geeft aan of de gemeten huidige waarde binnen de tolerantie ligt. |
| Temperatuur sensors in PCM |PCM |Fysiek |Gedeeld |Nee |De status van een groot aantal temperatuur Sens oren zoals de inlaat-en hotspot Sens oren wordt weer gegeven. Dit geeft aan of de gemeten Tempe ratuur binnen de tolerantie ligt. |
| Voeding [0-1] |PCM |Fysiek |Gedeeld |Ja |Er wordt één regel weer gegeven voor elk van de energie-eenheden in de twee PCMs die zich op de achterkant van het apparaat bevinden. |
| Koeling [0-1] |PCM |Fysiek |Gedeeld |Ja |Er wordt één regel weer gegeven voor elk van de vier ventilatoren in de twee PCMs. |
| Batterij [0-1] |PCM |Fysiek |Gedeeld |Ja |Er wordt één regel weer gegeven voor elk van de back-upbatterij modules die zijn aangesloten op de PCM. |
| Metis |N.v.t. |Logisch |Gedeeld |N.v.t. |Hiermee wordt de status van de accu's weer gegeven: of ze moeten worden geloosd en de eind tijd nadert. |
| Cluster |N.v.t. |Logisch |Gedeeld |N.v.t. |Hier wordt de status weer gegeven van het cluster dat is gemaakt tussen de twee geïntegreerde controller modules. |
| Cluster knooppunt |N.v.t. |Logisch |Gedeeld |N.v.t. |Hiermee wordt de status van de controller aangegeven als onderdeel van het cluster. |
| Cluster quorum |N.v.t. |Logisch | |N.v.t. |Hiermee wordt de aanwezigheid van het grootste schijf lidmaatschap in de HDD-opslag groep aangegeven. |
| HDD-gegevens ruimte |N.v.t. |Logisch |Gedeeld |N.v.t. |De opslag ruimte die wordt gebruikt voor gegevens in de opslag groep van de harde schijf (HDD). |
| Ruimte voor HDD-beheer |N.v.t. |Logisch |Gedeeld |N.v.t. |De ruimte die is gereserveerd in de HDD-opslag groep voor beheer taken. |
| HDD-quorum ruimte |N.v.t. |Logisch |Gedeeld |N.v.t. |De ruimte die in de opslag groep voor de harde schijf is gereserveerd voor het cluster quorum. |
| Ruimte voor HDD-vervanging |N.v.t. |Logisch |Gedeeld |N.v.t. |De ruimte die in de opslag groep voor de HDD is gereserveerd voor het vervangen van de controller. |
| Gegevens ruimte SSD |N.v.t. |Logisch |Gedeeld |N.v.t. |De opslag ruimte die wordt gebruikt voor gegevens in de opslag groep voor de Solid-State Drive (SSD). |
| NVRAM-ruimte SSD |N.v.t. |Logisch |Gedeeld |N.v.t. |De opslag ruimte in de SSD-opslag groep die is toegewezen voor de NVRAM-logica. |
| HDD-opslag groep |N.v.t. |Logisch |Gedeeld |N.v.t. |Hier wordt de status weer gegeven van de logische opslag groep die is gemaakt op basis van de Hdd's van het apparaat. |
| SSD-opslag groep |N.v.t. |Logisch |Gedeeld |N.v.t. |Hier wordt de status weer gegeven van de logische opslag groep die is gemaakt op basis van de Ssd's van het apparaat. |
| Controller [0-1] [status] |I/O |Fysiek |Regelaar |Ja |Hiermee wordt de status van de controller weer gegeven en wordt aangegeven of deze zich in de modus actief of stand-by binnen het chassis bevindt. |
| Temperatuur sensors in controller |I/O |Fysiek |Regelaar |Nee |Voor talrijke temperatuur Sens oren, zoals I/O-module, CPU-Tempe ratuur, DIMM-en PCIe-Sens oren, wordt de status weer gegeven. Dit geeft aan of de gedetecteerde Tempe ratuur binnen de tolerantie ligt. |
| SAS-expansie |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de SAS-expansie (Serial Attached SCSI) aangegeven, die wordt gebruikt om de geïntegreerde opslag te verbinden met de controller. |
| SAS-connector [0-1] |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van elke SAS-connector aangegeven, die wordt gebruikt voor het verbinden van geïntegreerde opslag met de SAS-uitbrei ding. |
| SBB mid-plan Interconnect |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de mid-vlieg connector aangegeven, die wordt gebruikt voor het verbinden van elke controller met het middel vlak. |
| Processor kern |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de processor kernen binnen elke controller aangegeven. |
| Behuizing elektronica kracht |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van het energie systeem aangegeven dat wordt gebruikt door de behuizing. |
| Behuizing elektronica Diagnostics |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de diagnostische subsystemen aangegeven die worden verschaft door de controller. |
| Baseboard Management Controller (BMC) |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de Base Board management controller (BMC) aangegeven, een gespecialiseerde service processor die het hardwareapparaat bewaakt via Sens oren en met de systeem beheerder communiceert via een onafhankelijke verbinding. |
| Ethernet |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van elk van de netwerk interfaces aangegeven, dat wil zeggen, de beheer-en gegevens poorten die op de controller zijn opgenomen. |
| NVRAM |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van het NVRAM aangegeven, een niet-vluchtige wille keurig toegangs geheugen dat wordt gebruikt door de accu waarmee toepassings kritieke informatie wordt bewaard in het geval van een stroom storing. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lijst met onderdelen voor EBOD Enclosure van StorSimple-apparaat
De volgende tabel bevat een overzicht van de fysieke en logische onderdelen in de EBOD Enclosure (alleen aanwezig in het 8600-model) van uw on-premises StorSimple-apparaat.

| Onderdeel | Module | Type | Locatie | FRU? | Beschrijving |
| --- | --- | --- | --- | --- | --- |
| Station in sleuf [0-11] |Schijf stations |Fysiek |Gedeeld |Ja |Er wordt één regel weer gegeven voor elk van de harde schijven in de voor zijde van de EBOD-behuizing. |
| Omgevings temperatuur sensor |Sluit |Fysiek |Gedeeld |Nee |Meet de Tempe ratuur binnen het chassis. |
| Mid-vlieg temperatuur sensor |Sluit |Fysiek |Gedeeld |Nee |Meet de Tempe ratuur van het middel vlak. |
| Hoorbaar alarm |Sluit |Fysiek |Gedeeld |Nee |Hiermee wordt aangegeven of het hoorbare alarm subsysteem binnen het chassis functioneel is. |
| Sluit |Sluit |Fysiek |Gedeeld |Ja |Hiermee wordt de aanwezigheid van een chassis aangegeven. |
| Instellingen voor de behuizing |Sluit |Fysiek |Gedeeld |Nee |Verwijst naar het OPS-of het voor paneel van het chassis. |
| Lijn spanning-Sens oren |PCM |Fysiek |Gedeeld |Nee |Voor talrijke spannings sensors wordt de status weer gegeven. Dit geeft aan of de gemeten spanning binnen de tolerantie valt. |
| Regel huidige Sens oren |PCM |Fysiek |Gedeeld |Nee |De status van een groot aantal regel huidige Sens oren wordt weer gegeven. Dit geeft aan of de gemeten huidige waarde binnen de tolerantie ligt. |
| Temperatuur sensors in PCM |PCM |Fysiek |Gedeeld |Nee |De status van een groot aantal temperatuur Sens oren zoals de inlaat-en hotspot Sens oren wordt weer gegeven. Dit geeft aan of de gemeten Tempe ratuur binnen de tolerantie ligt. |
| Voeding [0-1] |PCM |Fysiek |Gedeeld |Ja |Er wordt één regel weer gegeven voor elk van de energie-eenheden in de twee PCMs die zich op de achterkant van het apparaat bevinden. |
| Koeling [0-1] |PCM |Fysiek |Gedeeld |Ja |Er wordt één regel weer gegeven voor elk van de vier ventilatoren in de twee PCMs. |
| Lokale opslag [HDD] |N.v.t. |Logisch |Gedeeld |N.v.t. |Hier wordt de status weer gegeven van de logische opslag groep die is gemaakt op basis van de Hdd's van het apparaat. |
| Controller [0-1] [status] |I/O |Fysiek |Regelaar |Ja |Hiermee wordt de status van de controllers in de EBOD-module weer gegeven. |
| Temperatuur sensors in EBOD |I/O |Fysiek |Regelaar |Nee |De status van een groot aantal temperatuur Sens oren van elke controller wordt weer gegeven. Dit geeft aan of de Tempe ratuur binnen de tolerantie ligt. |
| SAS-expansie |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de SAS-uitbreidings module aangegeven, die wordt gebruikt om de geïntegreerde opslag te verbinden met de controller. |
| SAS-connector [0-2] |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van elke SAS-connector aangegeven, die wordt gebruikt voor het verbinden van geïntegreerde opslag met de SAS-uitbrei ding. |
| SBB mid-plan Interconnect |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de mid-vlieg connector aangegeven, die wordt gebruikt voor het verbinden van elke controller met het middel vlak. |
| Behuizing elektronica kracht |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van het energie systeem aangegeven dat wordt gebruikt door de behuizing. |
| Behuizing elektronica Diagnostics |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de diagnostische subsystemen aangegeven die worden verschaft door de controller. |
| Verbinding met apparaat-controller |I/O |Fysiek |Regelaar |Nee |Hiermee wordt de status van de verbinding tussen de I/O-module EBOD en de apparaat-controller aangegeven. |

## <a name="next-steps"></a>Volgende stappen
* Als u de StorSimple Apparaatbeheer-service wilt gebruiken om uw apparaat te beheren, gaat u naar [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).
* Als u problemen moet oplossen met een onderdeel van een apparaat met een gedegradeerde of mislukte status, raadpleegt u [StorSimple-bewakings indicatoren](storsimple-monitoring-indicators.md).
* Zie [StorSimple hardware component Replacement](./storsimple-8000-hardware-component-replacement.md)(Engelstalig) als u een defect hardwareonderdeel wilt vervangen.
* Als er problemen blijven optreden, neemt u [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).