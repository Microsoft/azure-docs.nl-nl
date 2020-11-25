---
title: bestand opnemen
description: bestand opnemen
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 1be6a654962b513cfcf755d45e562b86067e7b25
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995001"
---
#### <a name="to-create-a-new-service"></a>Een nieuwe service maken

1.  Meld u met uw Microsoft-account-referenties aan bij de Azure Portal op deze URL: <https://portal.azure.com/> . Als u het apparaat in de overheids Portal implementeert, meldt u zich aan bij: <https://portal.azure.us/>

2.  Klik in het Azure Portal op **+ een resource** -StorSimple maken voor de &gt; **Storage** &gt; **virtuele** opslag.

    ![Nieuwe service maken](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Ga als volgt te werk op de Blade **StorSimple Apparaatbeheer** die wordt geopend:

    1.  Geef een unieke **resourcenaam** voor uw service op. De resource naam is een beschrijvende naam die kan worden gebruikt om de service te identificeren. De naam kan tussen 2 en 50 tekens bevatten (letters, cijfers en afbreekstreepjes). De naam moet beginnen en eindigen met een letter of cijfer.

    2.  Kies een **abonnement** in de vervolgkeuzelijst. Het abonnement is gekoppeld aan uw factureringsrekening. Dit veld wordt niet weergegeven als u slechts één abonnement hebt.

    3.  Voor **resource groep** selecteert u een bestaande of een nieuwe groep maken. Zie [Azure-resourcegroepen](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) voor meer informatie.

    4.  Geef een **locatie** voor uw service op. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services) voor meer informatie over welke services beschikbaar zijn in welke regio. Kies in het algemeen een **locatie** die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren. U kunt ook rekening houden met het volgende:

        -   Als u bestaande workloads in azure hebt die u ook wilt implementeren met uw StorSimple-apparaat, wordt u aangeraden dat Data Center te gebruiken.

        -   Uw StorSimple-Apparaatbeheer en Azure-opslag kunnen zich op twee verschillende locaties bevinden. In dat geval moet u de StorSimple--apparaatbeheerfunctie en het Azure Storage-account afzonderlijk maken. Als u een Azure-opslag account wilt maken, gaat u naar Azure Storage in het Azure Portal en volgt u de stappen in [een opslag account maken](../articles/storage/common/storage-account-create.md). Nadat u dit account hebt gemaakt, voegt u het toe aan de StorSimple-apparaatbeheerfunctie met de stappen in [Een nieuw opslagaccount voor de service maken](../articles/storsimple/storsimple-virtual-array-manage-storage-accounts.md#add-a-storage-account-credential).

        -   Als u het virtuele apparaat in de overheids Portal implementeert, is de StorSimple-Apparaatbeheer-service beschikbaar in Amerikaanse Iowa en Amerikaanse Virginia-locaties.

    5.  Selecteer **een nieuw Azure-opslag account maken** om automatisch een opslag account te maken met de service. Geef een naam op voor het **opslag account**. Als u een andere locatie voor uw gegevens wilt kiezen, schakelt u dit vakje uit.

    6.  Schakel **Aan dashboard vastmaken** in als u een snelkoppeling naar deze service op uw dashboard wilt.

    7.  Klik op **Maken** om de StorSimple-apparaatbeheerfunctie te maken.

        ![Nieuwe service maken 2](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

U wordt omgeleid naar de pagina **service** -lands. Het maken van de service duurt enkele minuten. Nadat de service is gemaakt, krijgt u hierover een melding en wordt de status van de service gewijzigd in **Actief**.