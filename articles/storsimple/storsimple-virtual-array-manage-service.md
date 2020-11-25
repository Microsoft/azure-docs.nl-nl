---
title: StorSimple Apparaatbeheer-service implementeren | Microsoft Docs
description: Hierin wordt uitgelegd hoe u de StorSimple-Apparaatbeheer-service in de Azure Portal maakt en verwijdert, en wordt beschreven hoe u de service registratie sleutel beheert.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 92041be4d3d5ef4d980c17fa0a5870b740c1784a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995000"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>De StorSimple Apparaatbeheer-service voor de virtuele StorSimple-matrix implementeren

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

De StorSimple-Apparaatbeheer-service wordt uitgevoerd in Microsoft Azure en er wordt verbinding gemaakt met meerdere StorSimple-apparaten. Nadat u de service hebt gemaakt, kunt u deze gebruiken om de apparaten te beheren vanuit de Microsoft Azure-portal die in een browser wordt uitgevoerd. Hiermee kunt u alle apparaten bewaken die zijn verbonden met de StorSimple-Apparaatbeheer service vanaf één centrale locatie, waardoor de administratieve belasting wordt geminimaliseerd.

De algemene taken die betrekking hebben op een StorSimple-Apparaatbeheer service zijn:

* Een service maken
* Een service verwijderen
* De serviceregistratiesleutel ophalen
* De service registratie sleutel opnieuw genereren

In deze zelf studie wordt beschreven hoe u elk van de voor gaande taken uitvoert. De informatie in dit artikel is alleen van toepassing op virtuele StorSimple-matrices. Ga voor meer informatie over de StorSimple 8000-serie naar [Deploy a StorSimple Manager service](./storsimple-8000-manage-service.md).

## <a name="create-a-service"></a>Een service maken

Als u een service wilt maken, moet u het volgende hebben:

* Een abonnement met een Enterprise Agreement
* Een actief Microsoft Azure Storage-account
* De facturerings gegevens die worden gebruikt voor toegangs beheer

U kunt er ook voor kiezen om een opslag account te genereren wanneer u de service maakt.

Eén service kan meerdere apparaten beheren. Een apparaat kan echter niet meerdere services omvatten. Een grote onderneming kan meerdere service-exemplaren gebruiken om te werken met verschillende abonnementen, organisaties of zelfs implementatie locaties.

> [!NOTE]
> U hebt afzonderlijke instanties van StorSimple Apparaatbeheer service nodig voor het beheren van StorSimple 8000 Series-apparaten en StorSimple virtuele matrices.


Voer de volgende stappen uit om een service te maken.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Een service verwijderen

Voordat u een service verwijdert, moet u ervoor zorgen dat er geen aangesloten apparaten worden gebruikt. Als de service wordt gebruikt, deactiveert u de verbonden apparaten. Met de bewerking deactiveren wordt de verbinding tussen het apparaat en de service verbroken, maar blijven de apparaatgegevens in de Cloud behouden.

> [!IMPORTANT]
> De bewerking kan niet ongedaan worden gemaakt nadat een service is verwijderd. Elk apparaat dat de service gebruikt, moet de fabrieks instellingen herstellen voordat het kan worden gebruikt met een andere service. In dit scenario gaan de lokale gegevens op het apparaat, evenals de configuratie, verloren.
 

Voer de volgende stappen uit om een service te verwijderen.

#### <a name="to-delete-a-service"></a>Een service verwijderen

1. Ga naar **Alle resources**. Zoek naar uw StorSimple-Apparaatbeheer service. Selecteer de service die u wilt verwijderen.
   
    ![Selecteer de service die u wilt verwijderen](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Ga naar uw service dashboard om te controleren of er geen apparaten zijn verbonden met de service. Als er geen apparaten zijn geregistreerd bij deze service, wordt er ook een banner bericht weer gegeven voor het effect. Klik op **Verwijderen**.
   
    ![Service verwijderen](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Wanneer u om bevestiging wordt gevraagd, klikt u op **Ja** in het bevestigings bericht. 
   
    ![Verwijderen van service bevestigen](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Het kan enkele minuten duren voordat de service is verwijderd. Zodra de service is verwijderd, wordt u hiervan op de hoogte gebracht.
   
    ![Het verwijderen van de service is voltooid](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

De lijst met Services wordt vernieuwd.

 ![Bijgewerkte lijst met Services](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>De serviceregistratiesleutel ophalen
Nadat u een service hebt gemaakt, moet u uw StorSimple-apparaat registreren bij de service. Als u uw eerste StorSimple-apparaat wilt registreren, hebt u de service registratie sleutel nodig. Als u extra apparaten wilt registreren met een bestaande StorSimple-service, hebt u zowel de registratie sleutel als de versleutelings sleutel voor service gegevens nodig (die tijdens de registratie wordt gegenereerd op het eerste apparaat). Zie [StorSimple Security](./storsimple-8000-security.md)(Engelstalig) voor meer informatie over de versleutelings sleutel voor service gegevens. U kunt de registratie sleutel ophalen door de Blade **sleutels** voor uw service te openen.

Voer de volgende stappen uit om de service registratie sleutel op te halen.

#### <a name="to-get-the-service-registration-key"></a>De service registratie sleutel ophalen
1. Ga op de Blade **StorSimple Apparaatbeheer** naar **beheer &gt;** **sleutels**.
   
   ![De blade Sleutels](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Op de Blade **sleutels** wordt een service registratie sleutel weer gegeven. Kopieer de registratie sleutel met behulp van het Kopieer pictogram. 

Behoud de service registratie sleutel op een veilige locatie. U hebt deze sleutel nodig, evenals de versleutelings sleutel voor service gegevens, om extra apparaten te registreren bij deze service. Nadat u de service registratie sleutel hebt verkregen, moet u uw apparaat configureren via de Windows PowerShell voor StorSimple-interface.

## <a name="regenerate-the-service-registration-key"></a>De service registratie sleutel opnieuw genereren
Als u een sleutel rotatie moet uitvoeren of als de lijst met service beheerders is gewijzigd, moet u een service registratie sleutel opnieuw genereren. Wanneer u de sleutel opnieuw genereert, wordt de nieuwe sleutel alleen gebruikt voor het registreren van volgende apparaten. De apparaten die al zijn geregistreerd, worden niet beïnvloed door dit proces.

Voer de volgende stappen uit om een service registratie sleutel opnieuw te genereren.

#### <a name="to-regenerate-the-service-registration-key"></a>De service registratie sleutel opnieuw genereren
1. Ga op de Blade **StorSimple Apparaatbeheer** naar **beheer &gt;** **sleutels**.
   
   ![Blade sleutels opnieuw](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Klik op de Blade **sleutels** op **opnieuw genereren**.
   
   ![Klik op opnieuw genereren](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Controleer op de Blade **service registratie sleutel opnieuw genereren** de actie die vereist is wanneer de sleutels opnieuw worden gegenereerd. Op alle volgende apparaten die zijn geregistreerd bij deze service wordt de nieuwe registratie sleutel gebruikt. Klik op **opnieuw genereren** om te bevestigen. U wordt gewaarschuwd nadat de registratie is voltooid.
   
   ![Sleutel opnieuw genereren bevestigen](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Er wordt een nieuwe service registratie sleutel weer gegeven.
   
    ![Extra sleutel voor opnieuw genereren bevestigen](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Kopieer deze sleutel en sla deze op voor het registreren van nieuwe apparaten bij deze service.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over hoe u aan de [slag kunt gaan](storsimple-virtual-array-deploy1-portal-prep.md) met een StorSimple-virtuele matrix.
* Meer informatie over [het beheren van uw StorSimple-apparaat](storsimple-ova-web-ui-admin.md).