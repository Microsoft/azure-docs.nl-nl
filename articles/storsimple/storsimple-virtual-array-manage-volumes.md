---
title: Volumes op StorSimple virtuele matrix beheren | Microsoft Docs
description: Hierin wordt de StorSimple-Apparaatbeheer beschreven en wordt uitgelegd hoe u deze kunt gebruiken voor het beheren van volumes op uw StorSimple-virtuele matrix.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 79e3ce8c1605e5d68ff44901f53854d2f5f10abc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014377"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Gebruik de StorSimple-apparaatbeheerfunctie om volumes te beheren in de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In deze zelf studie wordt uitgelegd hoe u de StorSimple Apparaatbeheer-service gebruikt om volumes te maken en te beheren in uw StorSimple-virtuele matrix.

De StorSimple-Apparaatbeheer-service is een uitbrei ding in de Azure Portal waarmee u uw StorSimple-oplossing kunt beheren vanuit één webinterface. Naast het beheren van shares en volumes, kunt u de StorSimple Apparaatbeheer-service gebruiken om apparaten weer te geven en te beheren, waarschuwingen weer te geven en back-upbeleid en de back-upcatalogus te bekijken en te beheren.

## <a name="volume-types"></a>Volume typen

StorSimple volumes kunnen zijn:

* **Lokaal vastgemaakt**: gegevens in deze volumes blijven te allen tijde op de matrix en worden niet overgelopen naar de Cloud.
* **Gelaagd**: gegevens in deze volumes kunnen overlopen naar de Cloud. Wanneer u een gelaagd volume maakt, wordt ongeveer 10% van de ruimte ingericht op de lokale laag en wordt 90% van de ruimte ingericht in de Cloud. Als u bijvoorbeeld een volume van 1 TB hebt ingericht, bevindt 100 GB zich in de lokale ruimte en wordt 900 GB in de Cloud gebruikt wanneer de gegevens lagen. Dit houdt in dat als u alle lokale ruimte op het apparaat uitvoert, u geen gelaagd volume kunt inrichten (omdat de 10% vereiste voor de lokale laag niet beschikbaar is).

### <a name="provisioned-capacity"></a>Ingerichte capaciteit
Raadpleeg de volgende tabel voor de maximale ingerichte capaciteit voor elk volume type.

| **Limiet-id**                                       | **Limiet**     |
|------------------------------------------------------------|---------------|
| Minimale grootte van een gelaagd volume                            | 500 GB        |
| Maximale grootte van een gelaagd volume                            | 5 TB          |
| Minimum grootte van lokaal vastgemaakt volume                    | 50 GB         |
| Maximale grootte van lokaal vastgemaakt volume                    | 200 GB        |

## <a name="the-volumes-blade"></a>De Blade volumes
In het menu **volumes** op de Blade overzicht van de StorSimple-service wordt de lijst met opslag volumes op een bepaalde StorSimple-matrix weer gegeven en kunt u deze beheren.

![Blade volumes](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Een volume bestaat uit een reeks kenmerken:

* **Volume naam** : een beschrijvende naam die uniek moet zijn en het volume kan identificeren.
* **Status** : kan online of offline zijn. Als een volume offline is, is het niet zichtbaar voor initia tors (servers) die toegang hebben tot het gebruik van het volume.
* **Type** – geeft aan of het volume wordt **gelaagd** (de standaard instelling) of **lokaal is vastgemaakt**.
* **Capaciteit** – Hiermee geeft u de hoeveelheid gegevens die wordt gebruikt in vergelijking met de totale hoeveelheid gegevens die kan worden opgeslagen door de initiator (Server).
* **Back-up** : in het geval van de virtuele matrix StorSimple worden alle volumes automatisch ingeschakeld voor back-up.
* **Verbonden hosts** : Hiermee geeft u de initia tors (servers) op die toegang tot dit volume hebben.

![Details van volumes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Volg de instructies in deze zelf studie om de volgende taken uit te voeren:

* Een volume toevoegen
* Een volume wijzigen
* Een volume offline halen
* Een volume verwijderen

## <a name="add-a-volume"></a>Een volume toevoegen

1. Klik op de Blade samen vatting van de StorSimple-service op **+ volume toevoegen** op de opdracht balk. Hiermee opent u de Blade **volume toevoegen** .
   
    ![Scherm afbeelding toont de knop volume toevoegen en het deel venster volume toevoegen.](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Ga als volgt te werk op de Blade **volume toevoegen** :
   
   * Voer in het veld **volume naam** een unieke naam in voor het volume. De naam moet een teken reeks zijn die tussen de 3 en 127 tekens bevat.
   * Geef in de vervolg keuzelijst **type** de keuze aan of u een **gelaagd** of **lokaal vastgemaakt** volume wilt maken. Voor werk belastingen waarvoor lokale garanties, lage latenties en hogere prestaties zijn vereist, selecteert u **lokaal vastgemaakt volume**. Voor alle andere gegevens selecteert u **gelaagd** volume.
   * Geef in het veld **capaciteit** de grootte van het volume op. Een gelaagd volume moet tussen 500 GB en 5 TB liggen en een lokaal vastgemaakt volume moet tussen 50 GB en 500 GB liggen.
   * * Klik op **verbonden hosts**, selecteer een Access Control record (ACR) dat overeenkomt met de iSCSI-initiator die u wilt verbinden met dit volume en klik vervolgens op **selecteren**.
3. Als u een nieuwe verbonden host wilt toevoegen, klikt u op **Nieuw toevoegen**, voert u een naam in voor de host en de iSCSI QUALIFIED name (IQN), en klikt u vervolgens op **toevoegen**.
   
    ![Scherm afbeelding toont het deel venster verbonden hosts waar u nieuwe kunt toevoegen.](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Wanneer u klaar bent met het configureren van het volume, klikt u op **maken**. Er wordt een volume gemaakt met de opgegeven instellingen en er wordt een melding weer gegeven over het maken van dezelfde. Standaard wordt back-up ingeschakeld voor het volume.
5. Ga naar de Blade **volumes** om te controleren of het volume is gemaakt. Het volume wordt weer gegeven.
   
    ![Volume maken is voltooid](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Een volume wijzigen

Wijzig een volume wanneer u de hosts wilt wijzigen die toegang hebben tot het volume. De andere kenmerken van een volume kunnen niet worden gewijzigd nadat het volume is gemaakt.

#### <a name="to-modify-a-volume"></a>Een volume wijzigen

1. Selecteer in de **volume** -instelling op de Blade samen vatting van de StorSimple-service de virtuele matrix waarop het volume dat u wilt wijzigen, zich bevindt.
2. **Selecteer** het volume en klik op **verbonden hosts** om de momenteel verbonden host weer te geven en te wijzigen in een andere server.
   
    ![Volume bewerken](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Sla uw wijzigingen op door te klikken op de opdracht balk **Opslaan** . De opgegeven instellingen worden toegepast en er wordt een melding weer gegeven.

## <a name="take-a-volume-offline"></a>Een volume offline halen

Mogelijk moet u een volume offline halen wanneer u van plan bent om het te wijzigen of te verwijderen. Wanneer een volume offline is, is het niet beschikbaar voor lezen/schrijven-toegang. U moet het volume offline halen op de host en op het apparaat.

#### <a name="to-take-a-volume-offline"></a>Een volume offline halen

1. Zorg ervoor dat het betreffende volume niet wordt gebruikt voordat u het offline zet.
2. Zet het volume eerst offline op de host. Dit elimineert mogelijke Risico's van beschadigde gegevens op het volume. Raadpleeg de instructies voor het besturings systeem van de host voor specifieke stappen.
3. Nadat het volume op de host offline is, neemt u het volume op de matrix offline door de volgende stappen uit te voeren:
   
   * Selecteer in de **volume** -instelling op de Blade samen vatting van de StorSimple-service de virtuele matrix waarop het volume dat u wilt offline nemen, zich bevindt.
   * **Selecteer** het volume en klik op **...** (u kunt ook met de rechter muisknop op deze rij klikken) en in het context menu de optie **offline halen** selecteren.
     
        ![Offline volume](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Controleer de informatie op de Blade **offline nemen** en bevestig uw acceptatie van de bewerking. Klik op **offline zetten** om het volume offline te zetten. U ziet een melding dat de bewerking wordt uitgevoerd.
   * Ga naar de Blade **volumes** om te controleren of het volume is offline gezet. Als het goed is, ziet u de status van het volume als offline.
     
       ![Bevestiging van offline volume](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Een volume verwijderen

> [!IMPORTANT]
> U kunt een volume alleen verwijderen als deze offline is.
> 
> 

Voer de volgende stappen uit om een volume te verwijderen.

#### <a name="to-delete-a-volume"></a>Een volume verwijderen

1. Selecteer in de **volume** -instelling op de Blade overzicht van de StorSimple-service de virtuele matrix waarop het volume dat u wilt verwijderen, zich bevindt.
2. **Selecteer** het volume en klik op **...** (u kunt ook met de rechter muisknop op deze rij klikken) en selecteer **verwijderen** in het context menu.
   
    ![Volume verwijderen](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Controleer de status van het volume dat u wilt verwijderen. Als het volume dat u wilt verwijderen niet offline is, moet u het eerst offline halen. Volg hiervoor de stappen in [een volume offline halen](#take-a-volume-offline).
4. Wanneer u wordt gevraagd om bevestiging op de Blade **verwijderen** , accepteert u de bevestiging en klikt u op **verwijderen**. Het volume wordt nu verwijderd en op de Blade **volumes** wordt de bijgewerkte lijst met volumes binnen de virtuele matrix weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [klonen van een StorSimple-volume](storsimple-virtual-array-clone.md).

