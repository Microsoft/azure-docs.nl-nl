---
title: Azure CDN inhoud beperken per land/regio | Microsoft Docs
description: Meer informatie over het beperken van toegang tot uw Azure CDN inhoud met behulp van de functie voor geo-filtering.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: ed82adcc1432bde27042d5775c454bfabcdb96ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358131"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Azure CDN inhoud beperken per land/regio

## <a name="overview"></a>Overzicht
Wanneer een gebruiker uw inhoud opvraagt, wordt de inhoud standaard geleverd, ongeacht de locatie van de gebruiker die de aanvraag heeft gedaan. In sommige gevallen wilt u mogelijk de toegang tot uw inhoud beperken per land/regio. Met de functie voor *geo-filtering* kunt u regels maken voor specifieke paden op uw CDN-eind punt om inhoud in geselecteerde landen/regio's toe te staan of te blok keren.

> [!IMPORTANT]
> **Azure CDN standaard van micro soft** -profielen biedt geen ondersteuning voor geografische filtering op basis van een pad.
> 

## <a name="standard-profiles"></a>Standaard profielen
De procedures in deze sectie zijn alleen voor **Azure CDN standaard van Akamai** en **Azure CDN standaard van Verizon** -profielen. 

Voor **Azure CDN Premium van Verizon** -profielen moet u de portal **beheren** gebruiken om geo-filtering te activeren. Zie [Azure CDN Premium van Verizon-profielen](#azure-cdn-premium-from-verizon-profiles)voor meer informatie.

### <a name="define-the-directory-path"></a>Het mappad definiëren
Als u de functie voor het filteren van geo wilt gebruiken, selecteert u uw CDN-eind punt in de portal en selecteert u vervolgens **geo-filtering** onder instellingen in het menu aan de linkerkant. 

![Scherm opname van het selecteren van geo-filtering in het menu voor een eind punt.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Geef in het vak **pad** het relatieve pad op naar de locatie waar gebruikers toegang worden verleend of geweigerd. 

U kunt geo-filtering Toep assen voor alle bestanden met een slash (/) of specifieke mappen selecteren door mappaden op te geven (bijvoorbeeld */pictures/*). U kunt ook geo-filtering Toep assen op één bestand (bijvoorbeeld */pictures/city.png*). Meerdere regels zijn toegestaan. Nadat u een regel hebt opgegeven, wordt er een lege rij weer gegeven waarin u de volgende regel kunt invoeren.

Zo zijn alle volgende paden voor mappad geldig:   
*/*                                 
*Originele*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Het type actie definiëren

Selecteer in de lijst **actie** de optie **toestaan** of **blok keren**: 

- **Toestaan**: alleen gebruikers uit de opgegeven landen/regio's hebben toegang tot assets die zijn aangevraagd vanuit het recursieve pad.

- **Blok keren**: gebruikers uit de opgegeven landen/regio's hebben geen toegang tot de assets die zijn aangevraagd bij het recursieve pad. Als er geen andere opties voor het filteren van land/regio zijn geconfigureerd voor die locatie, worden alle andere gebruikers toegang verleend.

Een geo-filter regel voor het blok keren van het pad */Photos/Strasbourg/* filtert bijvoorbeeld de volgende bestanden:     
*http:// \<endpoint> . azureedge.net/Photos/Strasbourg/1000.jpg* 
 *http:// \<endpoint> . azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>De landen/regio's definiëren
Selecteer in de lijst **land codes** de landen/regio's die u wilt blok keren of toestaan van het pad. 

Wanneer u klaar bent met het selecteren van de landen/regio's, selecteert u **Opslaan** om de nieuwe geo-filter regel te activeren. 

![Scherm opname toont de land CODES die moeten worden gebruikt om landen of regio's te blok keren of toe te staan.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Resources opschonen
Als u een regel wilt verwijderen, selecteert u deze in de lijst op de pagina voor **geografische filtering** en kiest u **verwijderen**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium van Verizon-profielen
Voor **Azure CDN Premium van Verizon** -profielen is de gebruikers interface voor het maken van een geo-filter regel anders:

1. Selecteer **beheren**in het bovenste menu van uw Azure CDN profiel.

2. Selecteer in de Verizon-Portal de optie **http large**en selecteer vervolgens **land filtering**.

    ![In de scherm afbeelding ziet u hoe u land filters selecteert in azure C D N.](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Selecteer **land filter toevoegen**.

    De **stap een:** pagina wordt weer gegeven.

4. Voer het mappad in, selecteer **blok keren** of **toevoegen**en selecteer **volgende**.

    De **stap twee:** pagina wordt weer gegeven. 

5. Selecteer een of meer landen/regio's in de lijst en selecteer vervolgens **volt ooien** om de regel te activeren. 
    
    De nieuwe regel wordt weer gegeven in de tabel op de pagina **land filtering** .

    ![Scherm afbeelding laat zien waar de regel wordt weer gegeven in het filteren van landen.](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Resources opschonen
Selecteer in de tabel filteren op land/regio het Verwijder pictogram naast een regel om deze te verwijderen of bewerk het pictogram bewerken om het te wijzigen.

## <a name="considerations"></a>Overwegingen
* Wijzigingen in de configuratie van de geo-filtering worden niet onmiddellijk doorgevoerd:
   * Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
   * Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
   * Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven. 
 
* Deze functie biedt geen ondersteuning voor joker tekens (bijvoorbeeld *).

* De geo-filter configuratie die is gekoppeld aan het relatieve pad, wordt recursief toegepast op dat pad.

* Er kan slechts één regel worden toegepast op hetzelfde relatieve pad. Dat wil zeggen dat u geen meerdere land-of regio filters kunt maken die naar hetzelfde relatieve pad wijzen. Maar omdat de land-of regio filters recursief zijn, kan een map meerdere land-of regio filters hebben. Met andere woorden, een submap van een eerder geconfigureerde map kan worden toegewezen aan een ander filter voor land/regio.

* De functie voor geo-filtering gebruikt land codes om de landen/regio's te definiëren van waaruit een aanvraag wordt toegestaan of geblokkeerd voor een beveiligde Directory. Hoewel de Akamai-en Verizon-profielen de meeste van dezelfde land codes ondersteunen, zijn er enkele verschillen. Zie [Azure CDN land codes](/previous-versions/azure/mt761717(v=azure.100))voor meer informatie. 

