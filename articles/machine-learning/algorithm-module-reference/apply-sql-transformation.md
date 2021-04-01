---
title: SQL-transformatie toepassen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module SQL-trans formatie Toep assen in Azure Machine Learning om een SQLite-query uit te voeren op invoer gegevens sets om de gegevens te transformeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/12/2020
ms.openlocfilehash: c66fbe59fd5b2660d02bfca285f78666d64569fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94555597"
---
# <a name="apply-sql-transformation"></a>SQL-transformatie toepassen

In dit artikel wordt een module van Azure Machine Learning Designer beschreven.

Met de module voor het Toep assen van SQL-trans formatie kunt u het volgende doen:
  
-   Maak tabellen voor resultaten en sla de gegevens sets op in een draag bare data base.  
  
-   Aangepaste trans formaties uitvoeren op gegevens typen of aggregaties maken.  
  
-   Voer SQL-query-instructies uit om gegevens te filteren of te wijzigen en de query resultaten te retour neren als een gegevens tabel.  

> [!IMPORTANT]
> De SQL-engine die in deze module wordt gebruikt, is **sqlite**. Voor meer informatie over de syntaxis van SQLite raadpleegt u [SQL zoals begrepen door sqlite](https://www.sqlite.org/index.html).
> Met deze module worden gegevens in SQLite gebump, die zich in de geheugen-DB bevinden. Daarom vergt het uitvoeren van de module veel meer geheugen en kan er een `Out of memory` fout optreden. Zorg ervoor dat uw computer voldoende RAM heeft.

## <a name="how-to-configure-apply-sql-transformation"></a>SQL-trans formatie Toep assen configureren  

De module kan Maxi maal drie gegevens sets als invoer hebben. Wanneer u verwijst naar de gegevens sets die zijn verbonden met elke invoer poort, moet u de namen `t1` , `t2` en gebruiken `t3` . Het tabel nummer geeft de index van de invoer poort aan.  

Hieronder ziet u voorbeeld code om te laten zien hoe u twee tabellen samenvoegt. T1 en T2 zijn twee gegevens sets die zijn verbonden met de linker-en middelste invoer poorten van **SQL-trans formatie Toep assen**:

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
De resterende para meter is een SQL-query die gebruikmaakt van de SQLite-syntaxis. Wanneer u meerdere regels in het tekstvak **SQL script** typt, gebruikt u een punt komma om elke instructie te beëindigen. Anders worden regel einden geconverteerd naar spaties.  

Deze module biedt ondersteuning voor alle standaard instructies van de SQLite-syntaxis. Zie de sectie [technische opmerkingen](#technical-notes) voor een lijst met niet-ondersteunde instructies.

##  <a name="technical-notes"></a>Technische opmerkingen  

Deze sectie bevat implementatie details, tips en antwoorden op veelgestelde vragen.

-   Er is altijd een invoer vereist op poort 1.  
  
-   Voor kolom-id's die spaties of andere speciale tekens bevatten, plaatst u de kolom-id altijd tussen vier Kante haken of dubbele aanhalings teken bij verwijzing naar de kolom in de `SELECT` or- `WHERE` componenten.  
  
### <a name="unsupported-statements"></a>Niet-ondersteunde instructies  

Hoewel SQLite veel van de ANSI SQL-standaard ondersteunt, bevat het niet veel functies die worden ondersteund door commerciële relationele database systemen. Zie [SQL as begrepen door sqlite](http://www.sqlite.org/lang.html)voor meer informatie. Houd ook rekening met de volgende beperkingen bij het maken van SQL-instructies:  
  
- SQLite gebruikt dynamische typen voor waarden, in plaats van een type toe te wijzen aan een kolom, zoals in de meeste relationele database systemen. Het is zwak getypt en maakt impliciete type conversie mogelijk.  
  
- `LEFT OUTER JOIN` is geïmplementeerd, maar niet `RIGHT OUTER JOIN` of `FULL OUTER JOIN` .  

- U kunt `RENAME TABLE` en- `ADD COLUMN` instructies gebruiken met de `ALTER TABLE` opdracht, maar andere componenten worden niet ondersteund, waaronder `DROP COLUMN` , `ALTER COLUMN` en `ADD CONSTRAINT` .  
  
- U kunt een weer gave maken in SQLite, maar daarna zijn de weer gaven alleen-lezen. U kunt een `DELETE` -, `INSERT` -of-instructie niet uitvoeren `UPDATE` op een weer gave. U kunt echter een trigger maken die wordt geactiveerd bij een poging naar `DELETE` , `INSERT` , of `UPDATE` op een weer gave en andere bewerkingen uitvoeren in de hoofd tekst van de trigger.  
  

Naast de lijst met niet-ondersteunde functies die beschikbaar zijn op de officiële SQLite-site, biedt de volgende wiki een lijst met andere niet-ondersteunde functies: [sqlite-niet-ondersteunde SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
