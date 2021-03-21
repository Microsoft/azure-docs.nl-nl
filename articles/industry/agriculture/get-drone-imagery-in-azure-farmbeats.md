---
title: Door drones verzamelde afbeeldingen ophalen
description: In dit artikel wordt beschreven hoe u drone installatie kopieën van partners kunt ophalen.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 18932d2ddb60242b4d7874dddf0349a62cd5c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177603"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Drone-installatie kopie van drone-partners ophalen

In dit artikel wordt beschreven hoe u orthomosaic-gegevens uit uw drone-installatie kopie partners naar Azure FarmBeats Datahub kunt brengen. Een orthomosaic is een foto met lucht afbeelding of afbeelding die geometrisch is gecorrigeerd en wordt afgenomen van gegevens die zijn verzameld door een drone.

Op dit moment worden de volgende afbeeldings partners ondersteund.

  ![FarmBeats drone imagey-partners](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Het integreren van drone-installatie kopie gegevens met Azure FarmBeats helpt u bij het ophalen van orthomosaic gegevens van de drone vluchten die u in uw farm uitvoert in de datahub. Wanneer de gegevens beschikbaar zijn, kunt u deze weer geven in de FarmBeats-Accelerator. De gegevens kunnen worden gebruikt voor gegevens fusie en kunst matige intelligentie en het bouwen van machine learning model.

## <a name="before-you-begin"></a>Voordat u begint

  - Zorg ervoor dat u Azure FarmBeats hebt geïnstalleerd. Zie [Azure FarmBeats installeren](install-azure-farmbeats.md)voor meer informatie over het installeren van FarmBeats.
  - Zorg ervoor dat u de farm hebt waarvoor u de drone-installatie kopie in uw FarmBeats-systeem wilt definiëren.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Drone image-integratie met FarmBeats inschakelen

Geef de volgende informatie op voor de provider van uw apparaat om integratie met FarmBeats in te scha kelen:
 - API-eindpunt
 - Tenant-id
 - Client-id
 - Clientgeheim

Volg deze stappen.

1. Down load dit [script](https://aka.ms/farmbeatspartnerscript)en pak het uit naar uw lokale station. Twee bestanden bevinden zich in het zip-bestand.
2. Meld u aan bij [Azure Portal](https://portal.azure.com/) en open Azure Cloud Shell. Deze optie is beschikbaar op de werk balk in de rechter bovenhoek van de portal.

    ![Azure Cloud Shell openen op de rechter balk van de portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Zorg ervoor dat de omgeving is ingesteld op **Power shell**.

    ![Power shell-instelling](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Upload de twee bestanden die u hebt gedownload uit stap 1 van uw Cloud Shell-exemplaar.

    ![Bestanden uploaden](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Ga naar de map waarin de bestanden zijn geüpload. Standaard worden deze geüpload naar de basismap onder de gebruikers naam.
6. Voer het volgende script uit:

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. Volg de instructies op het scherm voor het vastleggen van de waarden van API-eind punt, Tenant-ID, client-ID, client geheim en EventHub-verbindings reeks.

    Nadat u de vereiste referenties hebt opgegeven in het drone-software systeem van de partner, kunt u alle farms importeren uit het FarmBeats-systeem. Vervolgens kunt u de gegevens van de farm gebruiken om uw drone-installatie kopie verzameling te plannen.

    Nadat de onbewerkte installatie kopieën door de software van de drone-providers zijn verwerkt, laadt het drone-software systeem de niet-geplaatste orthomosaic en andere verwerkte installatie kopieën in de datahub.

## <a name="view-drone-imagery"></a>Drone-installatie kopie weer geven

Nadat de gegevens zijn verzonden naar de FarmBeats-datahub, kunt u een query uitvoeren op het scène archief met behulp van FarmBeats Datahub-Api's.

U kunt ook de meest recente drone-installatie kopie bekijken op de pagina met **Farm Details** . Volg de stappen om de installatie kopie weer te geven.

1. Selecteer de farm waarnaar uw installatie kopie is geüpload. De pagina Details van **Farm** wordt weer gegeven.
2. Schuif omlaag naar het gedeelte meest recente **precisie kaarten** .
3. Bekijk de afbeelding in het gedeelte **drone installatie kopie** .

    ![Sectie drone installatie kopie](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Drone-installatie kopie downloaden

Wanneer u de sectie drone installatie kopie selecteert, wordt er een pop-up geopend met een afbeelding met een hoge resolutie van de drone-orthomosaic.

![Orthomosaic met hoge resolutie](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Alle drone-kaarten weer geven

Bestanden en afbeeldingen die zijn geüpload door de drone-provider, worden weer gegeven in de sectie **kaarten** . Selecteer de sectie **Maps** , filter op **Farm** en selecteer de juiste bestanden om weer te geven en te downloaden.

  ![Sectie kaarten](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u FarmBeats Datahub- [api's](rest-api-in-azure-farmbeats.md) kunt gebruiken om uw drone-installatie kopie te downloaden.
