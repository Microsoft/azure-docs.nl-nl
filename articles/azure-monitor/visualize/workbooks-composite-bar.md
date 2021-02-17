---
title: Renderer voor samengestelde balk van Azure Monitor werkmap
description: Meer informatie over de visualisatie voor het renderen van samengestelde balken van Azure Monitor werkmap.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 3bcbd2a58764003c174c02ec637050b57632048a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611631"
---
# <a name="composite-bar-renderer"></a>Renderer voor samengestelde balk

Met de werkmap kunt u gegevens weer geven met behulp van een samengestelde balk, een balk die uit meerdere staven bestaat.

De onderstaande afbeelding toont de samengestelde balk voor de database status van het aantal servers dat online, offline is en de herstel status heeft.

![Scherm opname van samengestelde balk voor database status.](./media/workbooks-composite-bar/database-status.png)

De renderer voor samengestelde balken wordt ondersteund voor rasters, tegels en grafiek visualisaties.

## <a name="adding-composite-bar-renderer"></a>Renderer voor samengestelde balk toevoegen

1. Schakel de werkmap over naar de bewerkings modus door de werk balk item *bewerken* te selecteren.
2. Selecteer *toevoegen* en vervolgens *query toevoegen*.
3. Stel de *gegevens bron* in op ' JSON ' en ' *visualisatie* ' op ' grid '.
4. Voeg de volgende JSON-gegevens toe.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Voer de query uit.
6. Selecteer **kolom instellingen** om de instellingen te openen.
7. Selecteer ' totaal ' in de *kolommen* en kies ' samengestelde balk ' voor de *kolom weergave*.
8. Stel de volgende instellingen in op de *samengestelde balk instellingen*.

| Kolom naam | Kleur        |
|-------------|--------------|
| online      | Green        |
| hersteld  | Geel       |
| off line     | Rood (helder) |

9. Label toevoegen:`["online"] of ["total"] are healthy`
10. In de kolom instellingen voor online, offline en herstel kunt u de kolom weergave instellen op verborgen (optioneel).
11. Selecteer *labels* bovenaan en voorzie het label van de kolom totaal als ' database status ' (optioneel).
12. Selecteren bij **Toep assen**

De instellingen van de samengestelde balk zien er als volgt uit:

![Scherm afbeelding van kolom instellingen voor samengestelde balken met instellingen die hierboven worden beschreven.](./media/workbooks-composite-bar/composite-bar-settings.png)

De samengestelde balk met de bovenstaande instellingen:

![Scherm afbeelding van samengestelde balk.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Samengestelde balk instellingen

Selecteer de kolom naam en de bijbehorende kleur om die kolom in die kleur weer te geven als onderdeel van de samengestelde balk. U kunt rijen omhoog en omlaag invoegen, verwijderen en verplaatsen.

### <a name="label"></a>Label

Het samengestelde balk label wordt boven aan de samengestelde balk weer gegeven. U kunt een combi natie van statische tekst, kolommen en para meters gebruiken.  Als label leeg is, wordt de waarde van de huidige kolommen weer gegeven als label. Als we in het vorige voor beeld het veld label zwart hebben gelaten, wordt de waarde van totaal aantal kolommen weer gegeven.

Raadpleeg kolommen met `["columnName"]` .

Raadpleeg para meters met `{paramName}` .

De naam van de kolom en de para meter zijn hoofdletter gevoelig. U kunt ook labels toevoegen aan een koppeling door ' dit item als koppeling maken ' te selecteren en vervolgens koppelings instellingen toe te voegen.

### <a name="aggregation"></a>Aggregatie

Aggregaties zijn handig voor structuur/groep op visualisaties. De gegevens voor een kolom voor de groeps rij worden bepaald door de aggregatie voor die kolom. Er zijn drie typen aggregaties die van toepassing zijn op samengestelde balken: geen, som en overnemen.

Groeps instellingen toevoegen:

1. Ga in kolom instellingen naar de kolom waaraan u instellingen wilt toevoegen.
2. In *structuur/groep op instellingen* onder *structuur type* selecteert u **groeperen op**
3. Selecteer het veld waarop u wilt groeperen.

![Scherm afbeelding van de instellingen voor groeperen op.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Geen

Geen aggregatie betekent dat er geen resultaten voor die kolom worden weer gegeven voor de groeps rijen.

![Scherm opname van samengestelde balk met geen aggregatie.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Sum

Als aggregatie is ingesteld als som, wordt in de kolom in de rij groep de samengestelde balk weer gegeven met de som van de kolommen die worden gebruikt om deze weer te geven. Het label gebruikt ook de som van de kolommen waarnaar wordt verwezen.

In het voor beeld hieronder ziet u de Maxi maal toegestane aggregatie en de aggregatie voor de totale kolom is som.

![Scherm opname van samengestelde balk met Sum-aggregatie.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Instellingen

Als aggregatie is ingesteld als inherit, wordt in de kolom in de rij groep de samengestelde balk weer gegeven met behulp van de aggregatie die door gebruikers is ingesteld voor de kolommen die worden gebruikt om deze weer te geven. De kolommen die in het label worden gebruikt, gebruiken ook de aggregatie die door de gebruiker is ingesteld. Als de huidige kolom renderer een samengestelde balk is en in het label wordt aangemerkt (zoals ' totaal ' in het bovenstaande voor beeld), wordt Sum gebruikt als de aggregatie voor die kolom.

In het onderstaande voor beeld zijn de Maxi maal toegestane aggregatie sets online, offline en herstellen en wordt de aggregatie voor de totale kolom overgenomen.

![Scherm opname van samengestelde balk met overname aggregatie.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Sorteren

Voor raster visualisaties werkt de sorteer volgorde van de rijen voor de kolom met de renderer voor samengestelde balken op basis van de waarde die de som is van de kolommen die worden gebruikt om de samengestelde-staaf computer dynamisch weer te geven. In de vorige voor beelden is de waarde die wordt gebruikt voor sorteren de som van de online-, herstel-en offline kolommen voor die specifieke rij.

## <a name="tiles-visualization"></a>Tegel visualisatie

1. Selecteer **toevoegen** en *query toevoegen*.
2. Wijzig de gegevens bron in JSON Voer de gegevens uit het [vorige voor beeld](#adding-composite-bar-renderer)in.
3. Wijzig de visualisatie in *tegels*.
4. Voer de query uit.
5. Selecteer **tegel instellingen**.
6. Selecteer *links* in de velden naast elkaar.
7. Voer de onderstaande instellingen onder *veld instellingen* in.
    1. Kolom: Server gebruiken.
    2. Kolom weergave: tekst.
8. Selecteer *onderaan* in tegel velden.
9. Voer de onderstaande instellingen onder *veld instellingen* in.
    1. Gebruik kolom: "totaal".
    2. Kolom weergave: "samengestelde balk".
    3. Geef de volgende instellingen op onder instellingen voor de samengestelde balk.

    | Kolom naam | Kleur        |
    |-------------|--------------|
    | online      | Green        |
    | hersteld  | Geel       |
    | off line     | Rood (helder) |

    4. Label toevoegen: `["online"] of ["total"] are healthy` .
10. Selecteer **Toepassen**.

Samengestelde balk instellingen voor tegels:

![Scherm afbeelding van de tegel instellingen voor samengestelde balken met instellingen die hierboven worden beschreven.](./media/workbooks-composite-bar/tiles-settings.png)

De samengestelde balk weergave voor tegels met de bovenstaande instellingen ziet er als volgt uit:

![Scherm opname van samengestelde balk tegels.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Grafiek visualisatie

Volg de onderstaande instructies om een samengestelde balk renderer te maken voor grafiek visualisaties (type Hive-clusters).

1. Selecteer **toevoegen** en *query toevoegen*.
2. Wijzig de gegevens bron in JSON Voer de gegevens uit het [vorige voor beeld](#adding-composite-bar-renderer)in.
3. Wijzig de visualisatie in *grafieken*.
4. Voer de query uit.
5. Selecteer de instellingen voor de **grafiek**.
6. Selecteer *Inhoud centreren* in instellingen voor knooppunt indeling.
7. Voer de onderstaande instellingen onder *veld instellingen* in.
    1. Gebruik kolom: "totaal".
    2. Kolom weergave: "samengestelde balk".
    3. Voer de volgende instellingen onder *instellingen van de samengestelde balk* in.

    |Kolom naam  |     Kleur    |
    |-------------|--------------|
    | online      | Green        |
    | hersteld  | Geel       |
    | off line     | Rood (helder) |

   4. Label toevoegen: `["online"] of ["total"] are healthy` .
9. Voer de instellingen hieronder onder *indelings instellingen* in.
    1. Grafiek type: **Hive-clusters**.
    2. Knoop punt-ID selecteren: "server".
    3. Groeperen op veld: ' geen '.
    4. Knooppunt grootte: 100.
    5. Marge tussen zeshoeken: 5.
    6. Type kleur type: **geen**.
1. Selecteer **Toepassen**.
    
Samengestelde balk instellingen voor grafieken:

![Scherm afbeelding van de instellingen voor de samengestelde staaf grafiek met instellingen die hierboven worden beschreven.](./media/workbooks-composite-bar/graphs-settings.png)

De samengestelde balk weergave voor de grafiek met de bovenstaande instellingen ziet er als volgt uit:

![Scherm afbeelding van samengestelde staaf grafieken met hive-clusters.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Volgende stappen

* [Implementeer](../visualize/workbooks-automate.md) werkmappen met Azure Resource Manager.
* De toegang tot uw werkmap resources [beheren](../platform/workbooks-access-control.md) en delen.
