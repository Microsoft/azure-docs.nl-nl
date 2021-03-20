---
title: Azure CDN-eind punt met meerdere oorsprong (preview-versie)
description: Aan de slag met Azure CDN eind punt meerdere oorsprongen.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: 6e433950c04c4494201b090063b17a10e54a4822
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685768"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Meerdere begin punten Azure CDN

Ondersteuning voor meerdere oorsprong elimineert downtime en brengt wereld wijde redundantie tot stand. 

Door meerdere oorsprongen te kiezen binnen een Azure CDN-eind punt, wordt het risico gespreid door de redundantie die u hebt gekregen door de status van elke oorsprong te zoeken en zo nodig bij te werken.

Stel een of meer oorspronkelijke groepen in en kies een standaard oorspronkelijke groep. Elke oorsprongs groep is een verzameling van een of meer oorsprong die vergelijk bare workloads kan hebben.

> [!NOTE]
> Deze functie is momenteel alleen beschikbaar via Azure CDN van micro soft. 

## <a name="create-the-origin-group"></a>De oorspronkelijke groep maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com)

2. Selecteer uw Azure CDN profiel en selecteer vervolgens het eind punt dat moet worden geconfigureerd voor meerdere oorsprong.

3. Selecteer **oorsprong** onder **instellingen** in de eindpunt configuratie:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN-eindpunt" border="true":::

4. Als u meerdere Origins wilt inschakelen, moet u ten minste één oorsprongs groep hebben. Selecteer een **oorsprongs groep maken**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Oorspronkelijke instellingen" border="true":::

5. Typ of selecteer de volgende informatie in de configuratie van de groep voor het toevoegen van de **oorsprong** :

   | Instelling           | Waarde                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Naam van de oorspronkelijke groep | Voer een naam in voor de oorspronkelijke groep.                                   |
   | Test status      | Selecteer **Ingeschakeld**. </br> Azure CDN worden status tests uitgevoerd vanaf verschillende punten over de hele wereld om de status van de oorsprong te bepalen. Schakel niet in als de huidige oorspronkelijke groep niet actief is om extra kosten te voor komen.
   | Pad testen        | Het pad in de oorsprong dat wordt gebruikt om de status te bepalen. |
   | Test interval    | Selecteer een test interval van 1, 2 of 4 minuten.                        |
   | Test protocol    | Selecteer **http** of **https**.                                         |
   | Test methode      | Selecteer **Head** of **Get**.                                           |
   | Standaard groep oorsprong | Selecteer het vak om in te stellen als standaard groep van oorsprong.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Oorsprongs groep toevoegen" border="true":::

6. Selecteer **Toevoegen**.

## <a name="add-multiple-origins"></a>Meerdere oorsprongen toevoegen

1. Selecteer **+ oorsprong maken** in de oorspronkelijke instellingen voor het eind punt:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Oorsprong maken" border="true":::

2. Voer de volgende informatie in of Selecteer deze in de configuratie **oorsprong toevoegen** :

   | Instelling           | Waarde                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Naam        | Voer een naam in voor de oorsprong.        |
   | Type oorsprong | Selecteer **opslag**, **Cloud service**, **Web-app** of **aangepaste oorsprong**.                                   |
   | Hostnaam van oorsprong        | Selecteer of typ de hostnaam van de oorsprong.  De vervolg keuzelijst bevat alle beschik bare oorsprongen van het type dat u in de vorige instelling hebt opgegeven. Als u **aangepaste oorsprong** als uw oorsprongs type hebt geselecteerd, voert u het domein van de bron server van uw klant in. |
   | Host-header van oorsprong    | Voer de host-header in die u Azure CDN wilt verzenden met elke aanvraag, of wijzig de standaard waarde.                        |
   | HTTP-poort   | Voer de HTTP-poort in.                                         |
   | HTTPS-poort     | Voer de HTTPS-poort in.                                           |
   | Prioriteit    | Voer een getal tussen 1 en 5 in.       |
   | Gewicht      | Voer een getal in tussen 1 en 1000.   |

    > [!NOTE]
    > Wanneer een oorsprong in een oorspronkelijke groep wordt gemaakt, moet er een prioriteit en gewicht worden verleend. Als een oorspronkelijke groep slechts één oorsprong heeft, wordt de standaard prioriteit en het basis gewicht ingesteld op 1. Het verkeer wordt doorgestuurd naar de oorsprong van de hoogste prioriteit als de oorsprong in orde is. Als wordt vastgesteld dat een oorsprong een slechte status heeft, worden de verbindingen in de volg orde van prioriteit doorverkocht naar een andere oorsprong. Als twee oorsprongen dezelfde prioriteit hebben, wordt verkeer gedistribueerd als per gewicht dat is opgegeven voor de oorsprong 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="Aanvullende oorsprong toevoegen" border="true":::

3. Selecteer **Toevoegen**.

4. Selecteer **oorsprong configureren** om het bronpad in te stellen voor alle oorsprongen:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Bronpad configureren" border="true":::

5. Selecteer **OK**.

## <a name="configure-origins-and-origin-group-settings"></a>Instellingen voor oorsprong en oorspronkelijke groep configureren

Zodra u verschillende oorsprongen en een oorspronkelijke groep hebt, kunt u de oorsprong toevoegen aan of verwijderen uit verschillende groepen. Oorsprongen binnen dezelfde groep moeten soort gelijke workloads hebben. Verkeer wordt naar deze oorsprongen gedistribueerd op basis van hun gezonde status, prioriteit en gewichts waarde. 

1. Selecteer in de oorspronkelijke instellingen van het Azure CDN-eind punt de naam van de oorspronkelijke groep die u wilt configureren:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Instellingen voor oorsprong en oorspronkelijke groep configureren" border="true":::

2. Selecteer in **oorspronkelijke groep bijwerken** de optie **+ oorsprong selecteren**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Oorspronkelijke groep bijwerken" border="true":::

4. Selecteer de oorsprong die u wilt toevoegen aan de groep in de vervolg keuzelijst en selecteer **OK**.

5. Controleer of de oorsprong is toegevoegd aan de groep en selecteer vervolgens **Opslaan**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="Bijkomende oorsprong die is toegevoegd aan de groep verifiëren" border="true":::

## <a name="remove-origin-from-origin-group"></a>Oorsprong uit de oorspronkelijke groep verwijderen

1. Selecteer in de oorspronkelijke instellingen van het eind punt van de Azure CDN de naam van de oorspronkelijke groep:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Oorsprong uit groep verwijderen" border="true":::

2. Als u een oorsprong uit de oorspronkelijke groep wilt verwijderen, selecteert u het prullenbak pictogram naast de oorsprong en selecteert u **Opslaan**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="Oorsprong van groep voor updates verwijderen" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>Bron groep overschrijven met regels-engine

Aanpassen hoe verkeer wordt gedistribueerd naar verschillende bron groepen met behulp van de standaard regels-engine.

Distribueer verkeer naar een andere groep op basis van de aanvraag-URL.

1. Selecteer in het CDN-eind punt **regel engine** onder **instellingen**:

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Engine voor regels" border="true":::

2. Selecteer **+ regel toevoegen**.

3. Voer een naam in voor de regel in de **naam**.

4. Selecteer **+ voor waarde** en selecteer vervolgens **URL-pad**.

5. Selecteer in het vervolg keuzemenu van de **operator** de optie **contains**.

6. Voer in **waarde** **/images** in.

7. Selecteer **+ actie toevoegen** en selecteer vervolgens **overschrijven van de oorspronkelijke groep**.

8. In **oorspronkelijke groep** selecteert u de oorspronkelijke groep in de vervolg keuzelijst.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Voor waarden van regel engine" border="true":::

Voor alle binnenkomende aanvragen als het URL-pad **/images** bevat, wordt de aanvraag toegewezen aan de oorspronkelijke groep in de sectie Action **(myorigingroup)**. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u Azure CDN-eind punt in meerdere oorsprong ingeschakeld.

Zie voor meer informatie over Azure CDN en de andere Azure-Services die in dit artikel worden genoemd:

* [Azure CDN](./cdn-overview.md)
* [Azure CDN product functie vergelijken](./cdn-features.md)
