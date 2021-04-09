---
title: StorSimple Virtual array implementeren voor het Cloud Solution Provider-programma
description: Meer informatie over hoe een CSP-partner een klant of een nieuw abonnement aan een bestaande klant kan toevoegen en vervolgens een service kan maken voor het implementeren van een virtuele StorSimple-matrix in CSP.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021857"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>StorSimple Virtual array implementeren voor het Cloud Solution Provider-programma

## <a name="overview"></a>Overzicht

De virtuele StorSimple-matrix kan worden geïmplementeerd door de CSP-partners (Cloud Solution Provider) voor hun klanten. Een CSP-partner kan een StorSimple-Apparaatbeheer service maken. Deze service kan vervolgens worden gebruikt voor het implementeren en beheren van de virtuele StorSimple-matrix en de bijbehorende shares, volumes en back-ups.

In dit artikel wordt beschreven hoe een CSP-partner een klant of een nieuw abonnement aan een bestaande klant kan toevoegen en vervolgens een service kan maken voor het implementeren van een virtuele StorSimple-matrix in CSP.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat:

- U bent Inge schreven onder het CSP-programma.
- U hebt geldige aanmeldings referenties voor het [partner centrum](https://partnercenter.microsoft.com/) . Met de referenties kunt u zich aanmelden bij de Partner Portal om nieuwe klanten toe te voegen, te zoeken naar klanten of naar een klant account te navigeren vanuit het dash board van de partner. De CSP kan worden gebruikt als een StorSimple-beheerder namens de klant in de Azure Portal.
                             
## <a name="add-a-customer"></a>Een klant toevoegen

Als u een klant toevoegt, wordt er automatisch een abonnement gemaakt. Als u een klant wilt toevoegen (en automatisch een abonnement wilt maken), voert u de volgende stappen uit in de partner portal.

1. Ga naar het [partner centrum](https://partnercenter.microsoft.com/) en meld u aan met uw CSP-referenties. Klik op **dash board**.

     ![Dash board in partner centrum](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Klik in het linkerdeel venster op **klanten**. Klik in het rechterdeel venster op **klanten toevoegen**. Voer de details van de klant in. Klik op **volgende: abonnementen** om een klant abonnement te maken.

    ![Klant toevoegen](./media/storsimple-partner-csp-deploy/image2.png)

3.  Selecteer **Microsoft Azure** aanbieding. Ga naar de onderkant van de pagina en klik op **controleren**.

    ![Abonnements gegevens controleren](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Lees de informatie en klik op **verzenden**.

    ![Abonnement verzenden](./media/storsimple-partner-csp-deploy/image4.png)

5. Sla de bevestigings gegevens op voor toekomstig naslag doeleinden.

    ![Bevestiging opslaan](./media/storsimple-partner-csp-deploy/image5.png)

6. Zoek of navigeer naar de klant die u zojuist hebt toegevoegd. Klik op de naam van het **bedrijf** om in te zoomen op de details.

    ![Zoeken naar de klant](./media/storsimple-partner-csp-deploy/image6.png)  

7. Selecteer **Service beheer** in het linkerdeel venster. Klik in het rechterdeel venster onder **Services beheren** op **Microsoft Azure Management Portal** om u aan te melden als een Azure-beheerder voor uw klant.

    ![Aanmelden bij Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Als u een StorSimple-Apparaatbeheer wilt maken, klikt u op **+ Nieuw** en zoekt of navigeert u naar **StorSimple virtuele-Device Series**. Ga naar [Deploy a StorSimple Apparaatbeheer service](storsimple-virtual-array-manage-service.md)voor meer informatie.

    ![StorSimple Apparaatbeheer-service maken](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Een abonnement toevoegen

In sommige gevallen hebt u mogelijk een bestaande klant en moet u een abonnement toevoegen. Als u een abonnement wilt toevoegen aan een bestaande klant, voert u de volgende stappen uit in de partner portal.

1. Ga naar het [partner centrum](https://partnercenter.microsoft.com/) en meld u aan met uw CSP-referenties. Klik op **dash board**.

     ![Dash board in partner centrum](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Klik in het linkerdeel venster op **klanten**. Zoek of navigeer naar de klant aan wie u een abonnement wilt toevoegen. Klik op het pictogram ![ Selectie uitvouwen ](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) om de rij voor de bedrijfs naam voor uw klant uit te vouwen. Klik in de details op **abonnementen toevoegen**.

    ![Customers](./media/storsimple-partner-csp-deploy/image10.png)

3. Controleer **Microsoft Azure** voor de **beste aanbiedingen** in het abonnement en klik op **verzenden**. Hiermee maakt u een nieuw abonnement.

    ![Nieuw abonnement toevoegen](./media/storsimple-partner-csp-deploy/image11.png)

6. Wanneer een nieuw abonnement is gemaakt, klikt u op **<--klanten** in het linkerdeel venster om terug te gaan naar de pagina **klanten** . Zoek naar de klant voor wie u zojuist een abonnement hebt gemaakt. Klik op de naam van het **bedrijf** om in te zoomen op de details.

    ![Zoeken naar de klant](./media/storsimple-partner-csp-deploy/image6.png)  

7. Selecteer **Service beheer** in het linkerdeel venster. Klik in het rechterdeel venster onder **Services beheren** op **Microsoft Azure Management Portal** om u aan te melden als een Azure-beheerder voor uw klant.

    ![Aanmelden bij Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Als u een StorSimple-Apparaatbeheer wilt maken, klikt u op **+ Nieuw** en zoekt of navigeert u naar **StorSimple virtuele-Device Series**. Ga naar [Deploy a StorSimple Apparaatbeheer service](storsimple-virtual-array-manage-service.md)voor meer informatie.

    ![StorSimple Apparaatbeheer-service maken](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Volgende stappen

- Als u meer vragen hebt over de StorSimple in CSP, gaat u naar [StorSimple in CSP: veelgestelde vragen](storsimple-partner-csp-faq.md).
- Als u klaar bent om uw StorSimple te implementeren, gaat u naar [uw StorSimple implementeren in CSP](storsimple-partner-csp-deploy.md).
