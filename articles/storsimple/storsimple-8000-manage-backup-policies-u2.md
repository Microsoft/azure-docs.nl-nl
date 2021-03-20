---
title: StorSimple 8000 Series-back-upbeleid beheren | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u de StorSimple-Apparaatbeheer service kunt gebruiken voor het maken en beheren van hand matige back-ups, back-upscheman en het bewaren van back-ups op een StorSimple 8000 Series-apparaat.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9ec2bbef88ed185c3927676ad2596269783d1850
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85515631"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>De StorSimple Apparaatbeheer-service in Azure Portal gebruiken voor het beheren van het back-upbeleid


## <a name="overview"></a>Overzicht

In deze zelf studie wordt uitgelegd hoe u de Blade StorSimple Apparaatbeheer service **back-upbeleid** gebruikt voor het beheren van back-upprocessen en het bewaren van back-ups voor uw StorSimple-volumes. Ook wordt beschreven hoe u een hand matige back-up kunt volt ooien.

Wanneer u een back-up maakt van een volume, kunt u ervoor kiezen om een lokale moment opname of een Cloud momentopname te maken. Als u een back-up maakt van een lokaal vastgemaakt volume, raden we u aan een moment opname van de Cloud op te geven. Het nemen van een groot aantal lokale moment opnamen van een lokaal vastgemaakt volume dat is gekoppeld aan een gegevensset die veel verloop heeft, resulteert in een situatie waarin de lokale ruimte snel kan worden uitgevoerd. Als u ervoor kiest om lokale moment opnamen te maken, raden we u aan om een back-up van minder dagelijkse moment opnamen te maken, de meest recente status te herstellen, ze gedurende een dag te bewaren en deze vervolgens te verwijderen.

Wanneer u een Cloud momentopname van een lokaal vastgemaakt volume maakt, kopieert u alleen de gewijzigde gegevens naar de Cloud, waar deze wordt ontdubbeld en gecomprimeerd.

## <a name="the-backup-policy-blade"></a>De Blade back-upbeleid

Met de Blade **back-upbeleid** voor uw StorSimple-apparaat kunt u het back-upbeleid beheren en lokale en Cloud momentopnamen plannen. Back-upbeleid wordt gebruikt voor het configureren van back-upscheman en het bewaren van back-ups voor een verzameling van volumes. Met back-upbeleid kunt u een moment opname van meerdere volumes tegelijk maken. Dit betekent dat de back-ups die zijn gemaakt met een back-upbeleid, consistente kopieën zullen maken.

Met de lijst met back-upbeleid in tabel vorm kunt u ook het bestaande back-upbeleid filteren op een of meer van de volgende velden:

* **Beleids naam** : de naam die aan het beleid is gekoppeld. De verschillende typen beleids regels zijn onder andere:

  * Het geplande beleid dat expliciet door de gebruiker wordt gemaakt.
  * Geïmporteerd beleid, dat oorspronkelijk is gemaakt in de StorSimple-Snapshot Manager. Deze hebben een tag die de StorSimple-Snapshot Manager host beschrijft waaruit het beleid is geïmporteerd.

  > [!NOTE]
  > Automatisch of standaard back-upbeleid is niet meer ingeschakeld op het moment dat het volume wordt gemaakt.

* **Laatste geslaagde back-up** : de datum en tijd van de laatste geslaagde back-up die is gemaakt met dit beleid.

* **Volgende back-up** : de datum en tijd van de volgende geplande back-up die door dit beleid wordt gestart.

* **Volumes** : de volumes die zijn gekoppeld aan het beleid. Alle volumes die aan een back-upbeleid zijn gekoppeld, worden gegroepeerd wanneer er back-ups worden gemaakt.

* **Schema's** : het aantal schema's dat is gekoppeld aan het back-upbeleid.

De veelgebruikte bewerkingen die u kunt uitvoeren voor back-upbeleid zijn:

* Een back-upbeleid toevoegen
* Een planning toevoegen of wijzigen
* Een volume toevoegen of verwijderen
* Een back-upbeleid verwijderen
* Een hand matige back-up maken

## <a name="add-a-backup-policy"></a>Een back-upbeleid toevoegen

Voeg een back-upbeleid toe om uw back-ups automatisch te plannen. Wanneer u voor het eerst een volume maakt, is er geen standaard back-upbeleid gekoppeld aan uw volume. U moet een back-upbeleid toevoegen en toewijzen om volume gegevens te beveiligen.

Voer de volgende stappen uit in de Azure Portal om een back-upbeleid voor uw StorSimple-apparaat toe te voegen. Nadat u het beleid hebt toegevoegd, kunt u een planning definiëren (Zie [een planning toevoegen of wijzigen](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Een planning toevoegen of wijzigen

U kunt een schema toevoegen of wijzigen dat is gekoppeld aan een bestaand back-upbeleid op uw StorSimple-apparaat. Voer de volgende stappen uit in de Azure Portal om een schema toe te voegen of te wijzigen.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Een volume toevoegen of verwijderen

U kunt een volume toevoegen aan of verwijderen uit een back-upbeleid op uw StorSimple-apparaat. Voer de volgende stappen uit in de Azure Portal om een volume toe te voegen of te verwijderen.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Een back-upbeleid verwijderen

Voer de volgende stappen uit in de Azure Portal om een back-upbeleid op uw StorSimple-apparaat te verwijderen.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Een hand matige back-up maken

Voer de volgende stappen uit in de Azure Portal om een back-up op aanvraag (hand matig) te maken voor één volume.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

