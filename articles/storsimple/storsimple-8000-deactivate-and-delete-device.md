---
title: Een StorSimple 8000 Series-apparaat deactiveren en verwijderen | Microsoft Docs
description: Meer informatie over het deactiveren en verwijderen van een StorSimple-apparaat dat is verbonden met een StorSimple Apparaatbeheer service.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 07d108306fdca9bfe8f793b61660550e43151d71
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017182"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>Een StorSimple-apparaat deactiveren en verwijderen

## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u een StorSimple-apparaat deactiveert en verwijdert dat is verbonden met een StorSimple Apparaatbeheer service. De richt lijnen in dit artikel zijn alleen van toepassing op apparaten uit de StorSimple 8000-serie, met inbegrip van de StorSimple-Cloud apparaten. Als u een virtuele StorSimple-matrix gebruikt, gaat u naar [deactiveren en verwijdert u een StorSimple-virtuele matrix](storsimple-virtual-array-deactivate-and-delete-device.md).

Bij het deactiveren wordt de verbinding tussen het apparaat en de bijbehorende StorSimple-Apparaatbeheer service. U kunt een StorSimple-apparaat buiten gebruik stellen (bijvoorbeeld als u uw apparaat vervangt of bijwerkt of als u geen gebruik meer maakt van StorSimple). Als dat het geval is, moet u het apparaat deactiveren voordat u het kunt verwijderen.

Wanneer u een apparaat deactiveert, zijn alle gegevens die lokaal op het apparaat zijn opgeslagen, niet meer toegankelijk. Alleen de gegevens die zijn gekoppeld aan het apparaat dat is opgeslagen in de Cloud, kunnen worden hersteld.

> [!WARNING]
> Deactivering is een permanente bewerking en kan niet ongedaan worden gemaakt. Een gedeactiveerd apparaat kan niet worden geregistreerd bij de StorSimple-Apparaatbeheer service, tenzij de fabrieks instellingen worden hersteld.
>
> Met het proces Factory reset worden alle gegevens verwijderd die lokaal op uw apparaat zijn opgeslagen. Daarom moet u een Cloud momentopname van al uw gegevens maken voordat u een apparaat deactiveert. Met deze Cloud momentopname kunt u alle gegevens in een later stadium herstellen.

> [!NOTE]
>
> - Zorg ervoor dat de gegevens van de verwijderde volume container daad werkelijk van het apparaat worden verwijderd voordat u een fysiek StorSimple-apparaat of-Cloud toestel deactiveert. U kunt de diagrammen voor de Cloud verbruik bewaken en wanneer u de drop van Cloud gebruik ziet door de back-ups die u hebt verwijderd, kunt u door gaan met het deactiveren van het apparaat. Als u het apparaat uitschakelt voordat deze verwijdering plaatsvindt, worden de gegevens in het opslag account gelaagd en worden de kosten in rekening gebracht.
>
> - Voordat u een fysiek StorSimpleapparaat of Cloud apparaat deactiveert, moet u clients en hosts die afhankelijk zijn van dat apparaat, stoppen of verwijderen.
>
> - Als de opslag account (s) of de containers in het opslag account dat is gekoppeld aan de volume containers al zijn verwijderd voordat u de gegevens van het apparaat verwijdert, ontvangt u een fout melding en kunnen de gegevens mogelijk niet worden verwijderd. We raden u aan de gegevens op het apparaat te verwijderen voordat u het opslag account of de containers erin verwijdert. In dit geval moet u echter door gaan met het deactiveren van het apparaat en de verwijdering ervan uitgaande dat de gegevens al zijn verwijderd uit het opslag account.

Na het lezen van deze zelf studie kunt u het volgende doen:

- Een apparaat deactiveren en de gegevens verwijderen.
- Een apparaat deactiveren en de gegevens behouden.

## <a name="deactivate-and-delete-data"></a>Gegevens deactiveren en verwijderen

Voer de volgende stappen uit als u wilt dat het apparaat volledig wordt verwijderd en u de gegevens op het apparaat niet wilt behouden.

### <a name="to-deactivate-the-device-and-delete-the-data"></a>Het apparaat deactiveren en de gegevens verwijderen

1. Voordat u een apparaat deactiveert, moet u alle volume containers (en de volumes) verwijderen die zijn gekoppeld aan het apparaat. U kunt alleen volume containers verwijderen nadat u de gekoppelde back-ups hebt verwijderd. Raadpleeg de opmerking in het bovenstaande overzicht voordat u een fysiek StorSimple-apparaat of-Cloud toestel deactiveert.

2. Deactiveer het apparaat als volgt:

   1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer op de Blade **apparaten** het apparaat dat u wilt deactiveren, klik met de rechter muisknop en klik vervolgens op **deactiveren**.

        ![StorSimple-apparaat deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Typ op de Blade **deactiveren** de naam van het apparaat om te bevestigen en klik vervolgens op **deactiveren**. Het deactiveren proces wordt gestart en duurt enkele minuten.

        ![StorSimple-apparaat 2 deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Na het deactiveren kunt u het apparaat volledig verwijderen. Als u een apparaat verwijdert, wordt dit verwijderd uit de lijst met apparaten die zijn verbonden met de service. De service kan het verwijderde apparaat vervolgens niet meer beheren. Gebruik de volgende stappen om het apparaat te verwijderen:
   
   1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer op de Blade **apparaten** het gedeactiveerde apparaat dat u wilt verwijderen, klik met de rechter muisknop en klik vervolgens op **verwijderen**.

        ![StorSimple-apparaat 3 deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Typ op de Blade **verwijderen** de naam van het apparaat dat u wilt bevestigen en klik vervolgens op **verwijderen**. Het duurt enkele minuten om het verwijderen te volt ooien.

        ![StorSimple-apparaat 4 deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Zodra het verwijderen is voltooid, wordt u hiervan op de hoogte gebracht. De lijst met apparaten wordt ook bijgewerkt om de verwijdering weer te geven.

## <a name="deactivate-and-retain-data"></a>Gegevens deactiveren en bewaren

Als u geïnteresseerd bent in het verwijderen van het apparaat, maar de gegevens wilt behouden, voert u de volgende stappen uit:

### <a name="to-deactivate-a-device-and-retain-the-data"></a>Een apparaat deactiveren en de gegevens behouden

1. Het apparaat deactiveren. Alle volume containers en de moment opnamen van het apparaat blijven behouden.
   
   1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer op de Blade **apparaten** het apparaat dat u wilt deactiveren, klik met de rechter muisknop en klik vervolgens op **deactiveren**.

         ![StorSimple-apparaat 5 deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Typ op de Blade **deactiveren** de naam van het apparaat om te bevestigen en klik vervolgens op **deactiveren**. Het deactiveren proces wordt gestart en duurt enkele minuten.

         ![StorSimple-apparaat 6 deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. U kunt nu een failover uitvoeren voor de volume containers en de bijbehorende moment opnamen. Voor procedures gaat u naar [failover en nood herstel voor uw StorSimple-apparaat](storsimple-8000-device-failover-disaster-recovery.md).
3. Na het deactiveren en de failover kunt u het apparaat volledig verwijderen. Als u een apparaat verwijdert, wordt dit verwijderd uit de lijst met apparaten die zijn verbonden met de service. De service kan het verwijderde apparaat vervolgens niet meer beheren. Voer de volgende stappen uit om het apparaat te verwijderen:
   
   1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer op de Blade **apparaten** het gedeactiveerde apparaat dat u wilt verwijderen, klik met de rechter muisknop en klik vervolgens op **verwijderen**.

       ![StorSimple-apparaat 7 deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Typ op de Blade **verwijderen** de naam van het apparaat dat u wilt bevestigen en klik vervolgens op **verwijderen**. Het duurt enkele minuten om het verwijderen te volt ooien.

       ![StorSimple-apparaat 8 deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Zodra het verwijderen is voltooid, wordt u hiervan op de hoogte gebracht. De lijst met apparaten wordt ook bijgewerkt om de verwijdering weer te geven.

## <a name="deactivate-and-delete-a-cloud-appliance"></a>Een cloudapparaat deactiveren en verwijderen

Voor een StorSimple Cloud Appliance wordt de toewijzing van de virtuele machine ongedaan gemaakt en verwijderd uit de portal en worden de resources die zijn aangemaakt toen deze werd ingericht. Wanneer het cloudapparaat is gedeactiveerd, kan het niet meer worden hersteld naar de oorspronkelijke staat.

![StorSimple Cloud Appliance deactiveren](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Deactivering resulteert in de volgende acties:

* De StorSimple Cloud Appliance wordt verwijderd uit de service.
* De virtuele machine voor de StorSimple Cloud Appliance wordt verwijderd.
* De besturingssysteem schijf en gegevens schijven die zijn gemaakt voor de StorSimple Cloud Appliance, blijven behouden. Als u deze entiteiten niet gebruikt, moet u ze hand matig verwijderen.
* De gehoste service en Virtual Network die tijdens het inrichten zijn gemaakt, blijven behouden. Als u deze entiteiten niet gebruikt, moet u ze hand matig verwijderen.
* Cloud momentopnamen die zijn gemaakt door de StorSimple Cloud Appliance, blijven behouden.

Nadat het Cloud apparaat is gedeactiveerd, kunt u het verwijderen uit de lijst met apparaten. Selecteer het gedeactiveerde apparaat, klik met de rechter muisknop en klik vervolgens op **verwijderen**. StorSimple waarschuwt u wanneer het apparaat is verwijderd en de lijst met apparaten wordt bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

* Als u het gedeactiveerde apparaat naar de fabrieks instellingen wilt herstellen, gaat u naar [de fabrieks instellingen van het apparaat herstellen](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* [Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md)voor technische ondersteuning.
* Ga voor meer informatie over het gebruik van de StorSimple Apparaatbeheer-service naar [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).

