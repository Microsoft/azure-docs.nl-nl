---
title: 'Quickstart: Interactief zoeken in kaarten met Azure Maps'
description: 'Quickstart: Leer hoe u interactieve, doorzoekbare kaarten maakt. Ontdek hoe u een Azure Maps-account maakt, een primaire sleutel ophaalt en de Web-SDK gebruikt om toewijzingstoepassingen in te stellen'
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 24a834c87fe34d90dec5961bb3f8d376c6e5e62d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373212"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Quickstart: Een interactief doorzoekbare kaart maken met Azure Maps

In dit artikel leert u hoe u een kaart maakt met Azure Maps om gebruikers een interactieve zoekervaring te bieden. U wordt door de volgende basisstappen geleid:

* Uw eigen Azure Maps-account maken.
* Haal uw primaire sleutel op om te gebruiken in de demo-webtoepassing.
* De demo-kaarttoepassing downloaden en openen.

In deze Snelstartgids wordt gebruikgemaakt van de Azure Maps Web-SDK, maar de Azure Maps Services kunnen worden gebruikt met elk kaart besturings element. [Hier](open-source-projects.md#third-part-map-control-plugins) vindt u een aantal populaire open-source kaart besturings elementen waarvoor het Azure Maps-team een invoeg toepassing heeft gemaakt.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Meld u aan bij de [Azure-portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken

Voer de volgende stappen uit om een nieuw Azure Maps-account te maken:

1. Klik in de linkerbovenhoek van [Azure Portal](https://portal.azure.com) op **Een resource maken**.
2. Typ **Azure Maps** in het vak *Marketplace doorzoeken*.
3. Selecteer **Azure Maps** in de *Resultaten*. Klik op de knop **Maken** die onder de kaart wordt weergegeven.
4. Voer de volgende waarden in op de pagina **Azure Kaarten-account maken**:
    * Het *Abonnement* dat u wilt gebruiken voor dit account.
    * De naam van de *Resourcegroep* voor dit account. U kunt kiezen om een *Nieuwe* of *Bestaande* resourcegroep te gebruiken.
    * De *Naam* van uw nieuwe account.
    * De *Prijscategorie* voor dit account.
    * Lees de *licentie* en de *privacyverklaring*, en schakel het selectievakje in om de voorwaarden te accepteren.
    * Klik op de knop **Maken**.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Maps-account maken in de portal":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>De primaire sleutel voor uw account ophalen

Als het Azure Kaarten-account is gemaakt, haalt u de primaire sleutel op waarmee u query's kunt uitvoeren op de API's van kaarten.

1. Open uw Maps-account in de portal.
2. Selecteer **Verificatie** in de sectie Instellingen.
3. Kopieer de **Primaire Sleutel** naar het Klembord. Sla de sleutel lokaal op voor gebruik verderop in deze zelfstudie.

>[!NOTE]
> Als u de abonnementssleutel gebruikt in plaats van de primaire sleutel, wordt uw kaart niet juist weergegeven. Bovendien wordt om veiligheidsredenen aanbevolen dat u roteert tussen de primaire en secundaire sleutel. Als u sleutels wilt roteren, werkt u de app bij om de secundaire sleutel te gebruiken. Vervolgens implementeert u en drukt u op de knop voor cyclus/vernieuwen naast de primaire sleutel om een nieuwe primaire sleutel te genereren. De oude primaire sleutel wordt uitgeschakeld. Zie [Azure Key Vault instellen met wisselen en controleren van sleutels](../key-vault/secrets/tutorial-rotation-dual.md) voor meer informatie over het roteren van sleutels

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Primaire sleutel voor Azure Maps ophalen in de Azure-portal":::

## <a name="download-the-demo-application"></a>Download de demo-toepassing

1. Ga naar [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Kopieer de inhoud van het bestand.
2. Sla de inhoud van dit bestand lokaal op als **AzureMapDemo.html**. Open het in een teksteditor.
3. Zoek naar de tekenreeks `<Your Azure Maps Key>`. Vervang deze door de waarde van de **primaire sleutel** uit de voorgaande sectie.

## <a name="open-the-demo-application"></a>De demo-toepassing openen

1. Open het bestand **AzureMapDemo.html** in een browser naar keuze.
2. Bekijk de kaart van Los Angeles. Zoom in en uit om te zien hoe de kaart automatisch met meer of minder informatie wordt weergegeven, afhankelijk van het zoomniveau.
3. Wijzig de standaardinstelling voor het midden van de kaart. Zoek in het bestand **AzureMapDemo.html** naar de variabele **center**. Vervang de lengte- en breedtegraad voor deze variabele door deze nieuwe waarden: **[-74.0060, 40.7128]** . Sla het bestand op en vernieuw de browser.
4. Probeer de interactieve zoekervaring uit. Zoek in het zoekvak linksboven in de demo-webtoepassing naar **restaurants**.
5. Beweeg de muis over de lijst met adressen en locaties die worden weergegeven onder het zoekvak. U ziet hoe bij de bijbehorende speld op de kaart een pop-upvenster met informatie over die locatie wordt weergegeven. Ten behoeve van de privacy van particuliere bedrijven worden er fictieve namen en adressen weergegeven.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Webtoepassing voor interactief zoeken in kaarten":::


## <a name="clean-up-resources"></a>Resources opschonen

>[!WARNING]
>In de zelfstudies in de sectie [Volgende stappen](#next-steps) vindt u gedetailleerde informatie over het gebruik en de configuratie van Azure Maps met uw account. Als u wilt doorgaan met de zelfstudies, verwijder de resources die u in deze quickstart hebt gemaakt dan niet.

Als u niet van plan bent om door te gaan naar de zelfstudies, voert u deze stappen uit voor het opschonen van de resources:

1. Sluit de browser waarin de **AzureMapDemo.html**-webtoepassing wordt uitgevoerd.
2. Navigeer naar de Azure-portalpagina. Selecteer **Alle resources** op de hoofdpagina van de portal. Of klik op het menupictogram in de linkerbovenhoek. Selecteer **Alle resources**.
3. Klik op uw Azure Maps-account. Klik bovenaan de pagina op **Verwijderen**.

Bekijk de volgende handleidingen voor meer codevoorbeelden en een interactieve coderingservaring:

[Een adres vinden met de Azure Maps-zoekservice](how-to-search-for-address.md)

[Azure Maps Map Control gebruiken](how-to-use-map-control.md)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Maps-account en een demo-toepassing gemaakt. Bekijk de volgende zelfstudies voor meer informatie over Azure Maps:

> [!div class="nextstepaction"]
> [Zoeken naar nuttige plaatsen in de buurt met Azure Maps](tutorial-search-location.md)