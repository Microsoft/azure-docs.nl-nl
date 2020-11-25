---
title: StorSimple virtuele matrix shares beheren | Microsoft Docs
description: Hierin wordt de StorSimple-Apparaatbeheer beschreven en wordt uitgelegd hoe u deze kunt gebruiken voor het beheren van shares op uw StorSimple-virtuele matrix.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 340b910319cabe3379bdb1bad1c09bc71c17f072
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994941"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Gebruik de StorSimple-apparaatbeheerfunctie om shares te beheren in de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In deze zelf studie wordt uitgelegd hoe u de StorSimple Apparaatbeheer-service gebruikt om shares te maken en te beheren op uw StorSimple-virtuele matrix.

De StorSimple-Apparaatbeheer-service is een uitbrei ding in de Azure Portal waarmee u uw StorSimple-oplossing kunt beheren vanuit één webinterface. Naast het beheren van shares en volumes kunt u de StorSimple Apparaatbeheer-service gebruiken om apparaten weer te geven en te beheren, waarschuwingen weer te geven, back-upbeleid te beheren en de back-upcatalogus te beheren.

## <a name="share-types"></a>Share typen

StorSimple-shares kunnen zijn:

* **Lokaal vastgemaakt**: gegevens in deze shares blijven steeds op de matrix en worden niet overgelopen naar de Cloud.
* **Gelaagd**: gegevens in deze shares kunnen overlopen naar de Cloud. Wanneer u een gelaagde share maakt, wordt ongeveer 10% van de ruimte ingericht op de lokale laag en wordt 90% van de ruimte ingericht in de Cloud. Als u bijvoorbeeld een share van 1 TB hebt ingericht, bevindt 100 GB zich in de lokale ruimte en wordt 900 GB in de Cloud gebruikt wanneer de gegevens lagen. Dit houdt in dat als u alle lokale ruimte op het apparaat uitvoert, u geen gelaagde shares kunt inrichten (omdat de 10% vereiste voor de lokale laag niet beschikbaar is).

### <a name="provisioned-capacity"></a>Ingerichte capaciteit

Raadpleeg de volgende tabel voor de maximale ingerichte capaciteit voor elk share type.

| **Limiet-id** | **Limiet** |
| --- | --- |
| Minimale grootte van een gelaagde share |500 GB |
| Maximale grootte van een gelaagde share |20 TB |
| Minimale grootte van een lokaal vastgemaakte share |50 GB |
| Maximale grootte van een lokaal vastgemaakte share |2 TB |

## <a name="the-shares-blade"></a>De Blade shares

In het menu **shares** op de Blade overzicht van de StorSimple-service wordt de lijst met opslag shares op een bepaalde StorSimple-matrix weer gegeven en kunt u ze beheren.

![Blade shares](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Een share bestaat uit een reeks kenmerken:

* **Share naam** : een beschrijvende naam die uniek moet zijn en de share kan identificeren.
* **Status** : kan online of offline zijn. Als een share offline is, hebben gebruikers van de share geen toegang meer.
* **Type** – geeft aan of de share is **gelaagd** (de standaard instelling) of **lokaal vastgemaakt**.
* **Capaciteit** – Hiermee geeft u de hoeveelheid gegevens die wordt gebruikt in vergelijking met de totale hoeveelheid gegevens die op de share kan worden opgeslagen.
* **Beschrijving** : een optionele instelling waarmee de share kan worden beschreven.
* **Machtigingen** : de NTFS-machtigingen voor de share die kunnen worden beheerd via Windows Verkenner.
* **Back-up** : in het geval van de virtuele matrix StorSimple worden alle shares automatisch ingeschakeld voor back-up.

![Details van shares](./media/storsimple-virtual-array-manage-shares/share-details.png)

Volg de instructies in deze zelf studie om de volgende taken uit te voeren:

* Een share toevoegen
* Een share wijzigen
* Een share offline halen
* Een share verwijderen

## <a name="add-a-share"></a>Een share toevoegen

1. Klik op de Blade overzicht van de StorSimple-service op **+ share toevoegen** op de opdracht balk. Hiermee opent u de Blade **share toevoegen** .

    ![Share toevoegen](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Ga als volgt te werk op de Blade **share toevoegen** :
   
   1. Voer in het veld **share naam** een unieke naam in voor uw share. De naam moet een teken reeks zijn die tussen de 3 en 127 tekens bevat.

   2. Een optionele **Beschrijving** voor de share. De beschrijving helpt bij het identificeren van de eigen aren van shares.

   3. Geef in de vervolg keuzelijst **type** de keuze aan of u een **gelaagde** of **lokaal vastgemaakte** share wilt maken. Voor werk belastingen waarvoor lokale garanties, lage latenties en hogere prestaties zijn vereist, selecteert u **lokaal vastgemaakte share**. Voor alle andere gegevens selecteert u **gelaagde** share.

   4. Geef in het veld **capaciteit** de grootte van de share op. Een gelaagde share moet tussen 500 GB en 20 TB liggen en een lokaal vastgemaakte share moet tussen 50 GB en 2 TB liggen.

   5. Wijs in het veld **standaard volledige machtigingen instellen op** de machtigingen toe aan de gebruiker of de groep die toegang tot deze share heeft. Geef de naam op van de gebruiker of de gebruikers groep in de _john@contoso.com_ indeling. We raden u aan om een gebruikers groep (in plaats van één gebruiker) te gebruiken om beheerders bevoegdheden voor toegang tot deze shares toe te staan. Als u de bevoegdheden hier hebt toegewezen, kunt u Verkenner gebruiken om de bevoegdheden te wijzigen.
3. Wanneer u klaar bent met het configureren van de share, klikt u op **maken**. Er wordt een share gemaakt met de opgegeven instellingen en er wordt een melding weer gegeven. Standaard wordt de back-up ingeschakeld voor de share.
4. Ga naar de Blade **shares** om te controleren of de share is gemaakt. De lijst wordt weer gegeven.
   
    ![Share maken is voltooid](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Een share wijzigen

Wijzig een share wanneer u de beschrijving van de share wilt wijzigen. Er kunnen geen andere share-eigenschappen worden gewijzigd wanneer de share is gemaakt.

#### <a name="to-modify-a-share"></a>Een share wijzigen

1. Selecteer in de instelling voor **shares** op de Blade samen vatting van StorSimple-service de virtuele matrix waarop de share die u wilt wijzigen, zich bevindt.
2. **Selecteer** de share om de huidige beschrijving te bekijken en te wijzigen.
3. Sla uw wijzigingen op door te klikken op de opdracht balk **Opslaan** . De opgegeven instellingen worden toegepast en er wordt een melding weer gegeven.
   
    ![ Share bewerken](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Een share offline halen

Mogelijk moet u een share offline halen wanneer u van plan bent om deze te wijzigen of te verwijderen. Wanneer een share offline is, is deze niet beschikbaar voor lezen/schrijven-toegang. U moet de share offline halen op de host en op het apparaat.

#### <a name="to-take-a-share-offline"></a>Een share offline halen

1. Zorg ervoor dat de betreffende share niet wordt gebruikt voordat u deze offline zet.
2. Volg de volgende stappen om de share offline te zetten in de matrix:
   
    1. Selecteer in de instelling voor **shares** op de Blade samen vatting van de StorSimple-service de virtuele matrix waarop de share die u wilt gebruiken offline moet worden opgeslagen.

    2. **Selecteer** de share en klik op **...** (Klik met de rechter muisknop in deze rij) en selecteer in het context menu de optie **offline halen**.
     
        ![Offline share](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Controleer de informatie op de Blade **offline nemen** en bevestig uw acceptatie van de bewerking. Klik op **offline zetten** om de share offline te zetten. U ziet een melding dat de bewerking wordt uitgevoerd.

    4. Ga naar de Blade **shares** om te controleren of de share offline is gezet. Als het goed is, ziet u de status van de share offline.

## <a name="delete-a-share"></a>Een share verwijderen

> [!IMPORTANT]
> U kunt een share alleen verwijderen als deze offline is.


Voer de volgende stappen uit om een share te verwijderen.

#### <a name="to-delete-a-share"></a>Een share verwijderen

1. Selecteer in de instelling voor **shares** op de Blade samen vatting van StorSimple-service de virtuele matrix waarop de share die u wilt verwijderen zich bevindt.
2. **Selecteer** de share en klik op **...** (Klik met de rechter muisknop in deze rij) en selecteer **verwijderen** in het context menu.
   
    ![Share verwijderen](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Controleer de status van de share die u wilt verwijderen. Als de share die u wilt verwijderen niet offline is, moet u deze eerst offline zetten. Volg de stappen in [een share offline halen](#take-a-share-offline).
4. Wanneer u wordt gevraagd om bevestiging op de Blade **verwijderen** , accepteert u de bevestiging en klikt u op **verwijderen**. De share wordt nu verwijderd en op de Blade **shares** ziet u de bijgewerkte lijst met shares in de virtuele matrix.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [klonen van een StorSimple-share](storsimple-virtual-array-clone.md).

