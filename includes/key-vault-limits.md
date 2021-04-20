---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: 9ecfcff00e6f44f5c739513c063baaa3fa02a3db
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753290"
---
Azure Key Vault-service ondersteunt twee resourcetypen: kluizen en beheerde HMS's. In de volgende twee secties worden de servicelimieten voor elk van beide beschreven.

### <a name="resource-type-vault"></a>Resourcetype: kluis

In deze sectie worden servicelimieten voor resourcetype `vaults` beschreven.

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Belangrijkste transacties (maximum aantal transacties in 10 seconden per kluis per regio<sup>1</sup>):

|Type sleutel|HSM-sleutel<br>Sleutel maken|HSM-sleutel<br>Alle andere transacties|Softwaresleutel<br>Sleutel maken|Softwaresleutel<br>Alle andere transacties|
|:---|---:|---:|---:|---:|
|RSA 2048-bits|5|1000|10|2.000|
|RSA 3072-bits|5|250|10|500|
|RSA 4096-bits|5|125|10|250|
|ECC P-256|5|1000|10|2.000|
|ECC P-384|5|1000|10|2.000|
|ECC P-521|5|1000|10|2.000|
|ECC SECP256K1|5|1000|10|2.000|
||||||

> [!NOTE]
> In de vorige tabel ziet u dat RSA 2048-bits-softwaresleutels met 2000 GET-transacties per 10 seconden zijn toegestaan. Voor RSA 2048-bits HSM-sleutels zijn 1000 GET-transacties per 10 seconden toegestaan.
>
> De drempelwaarden voor beperking worden gewogen en afdwinging wordt bepaald op basis van de som. Wanneer u zoals aangeven in de vorige tabel GET-bewerkingen uitvoert op RSA HSM-sleutels, is het gebruik van 4096-bits sleutels achtmaal duurder in vergelijking met 2048-bits sleutels. En dat komt doordat 1000/125 = 8.
>
> In een gegeven interval van 10 seconden kan een Azure Key Vault-client *slechts één* van de volgende bewerkingen uitvoeren voordat er een `429`HTTP-statuscode voor beperking wordt aangetroffen:
> - 2000 RSA 2048-bits softwaresleutel voor GET-transacties
> - 1000 RSA 2048-bits HSM-sleutel voor GET-transacties
> - 125 RSA 4096-bits HSM-sleutel voor GET-transacties
> - 124 RSA 4096-bits HSM-sleutel voor GET-transacties en 8 RSA 2048-bits HSM-sleutels voor GET-transacties

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Geheimen, sleutels voor beheerde opslagaccounts en kluistransacties:

| Transactietype | Maximum aantal transacties in 10 seconden per kluis per regio<sup>1</sup> |
| --- | --- |
| Alle transacties |2.000 |

Zie de [Azure Key Vault-beperkingsrichtlijnen](../articles/key-vault/general/overview-throttling.md) voor informatie over het verwerken van beperkingen wanneer deze limieten worden overschreden.

<sup>1</sup> Een limiet voor het hele abonnement voor alle transactietypen is vijf keer per sleutelkluislimiet. Andere HSM-transacties per abonnement zijn bijvoorbeeld beperkt tot 5000 transacties binnen tien seconden per abonnement.

#### <a name="backup-keys-secrets-certificates"></a>Back-upsleutels, geheimen, certificaten

Wanneer u een back-up maakt van een object dat is opgeslagen in een sleutelkluis (zoals een geheim, sleutel of certificaat), wordt het object door de back-upbewerking gedownload als een versleutelde blob. Deze blob kan niet buiten Azure worden ontsleuteld. Als u gebruiksgegevens uit deze blob wilt halen, moet u de blob herstellen in een sleutelkluis binnen hetzelfde Azure-abonnement en dezelfde Azure-geografie

| Transactietype | Maximale toegestane versies van sleutelkluisobjecten |
| --- | --- |
| Back-up maken van afzonderlijke sleutel, geheim, certificaat |500 |

> [!NOTE]
> Als u probeert een back-up te maken van een sleutel, geheim of certificaatobject met meer versies dan de bovengrens, resulteert dit in een fout. Het is niet mogelijk om eerdere versies van een sleutel, geheim of certificaat te verwijderen. 

#### <a name="azure-private-link-integration"></a>Azure Private Link- integratie

> [!NOTE]
> Het aantal sleutelkluizen met ingeschakelde privé-eindpunten per abonnement is een aanpasbare limiet. De limiet die hieronder wordt weergegeven, is de standaardlimiet. Als u een limietverhoging voor uw service wilt aanvragen, maakt u een ondersteuningsaanvraag en wordt deze per geval beoordeeld.

| Resource | Limiet |
| -------- | -----:|
| Privé-eindpunten per sleutelkluis | 64 |
| Sleutelkluizen met privé-eindpunten per abonnement | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Resourcetype: Beheerde HSM (preview)

In deze sectie worden servicelimieten voor resourcetype `managed HSM` beschreven.

#### <a name="object-limits"></a>Objectlimieten

|Item|Limieten|
|----|------:|
Aantal HSM-exemplaren per abonnement per regio|1 (tijdens de preview)
Aantal sleutels per HSM-pool|5000
Aantal versies per sleutel|100
Aantal aangepaste roldefinities per HSM|50
Aantal roltoewijzingen op HSM-bereik|50
Aantal roltoewijzingen voor elk afzonderlijk sleutelbereik|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Transactielimieten voor beheerbewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)
|Bewerking |Aantal bewerkingen per seconde|
|----|------:|
Alle RBAC-bewerkingen<br/>(bevat alle CRUD-bewerkingen voor roldefinities en roltoewijzingen)|5
Volledige back-up/herstel van HSM<br/>(slechts één gelijktijdige back-up- of herstelbewerking per ondersteund HSM-exemplaar)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Transactielimieten voor cryptografische bewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)

- Elk beheerd hsm-exemplaar bestaat uit drie HSM-partities met load balanced. De doorvoerlimieten zijn een functie van de onderliggende hardwarecapaciteit die voor elke partitie wordt toegewezen. In de onderstaande tabellen wordt de maximale doorvoer weergegeven met ten minste één partitie beschikbaar. De werkelijke doorvoer kan maximaal 3 x hoger zijn als alle 3 de partities beschikbaar zijn.
- Bij het genoteerde doorvoerlimieten wordt ervan uitgenomen dat er één sleutel wordt gebruikt om een maximale doorvoer te bereiken. Als bijvoorbeeld één RSA-2048-sleutel wordt gebruikt, is de maximale doorvoer 1100 aanmeldingsbewerkingen. Als u 1100 verschillende sleutels gebruikt met elk 1 transactie per seconde, kunnen ze niet dezelfde doorvoer bereiken.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA-sleutelbewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)

|Bewerking|2048-bits|3072-bits|4096-bits|
|--|--:|--:|--:|
Sleutel maken|1| 1| 1
Sleutel verwijderen (soft-delete)|10|10|10 
Sleutel ops purge|10|10|10 
Back-upsleutel|10|10|10 
Sleutel herstellen|10|10|10 
Sleutelgegevens op halen|1100|1100|1100
Versleutelen|10.000|10.000|6000
Ontsleutelen|1100|360|160
Wrap|10.000|10.000|6000
Unwrap|1100|360|160
Teken|1100|360|160
Verifiëren|10.000|10.000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EC-sleutelbewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)

In deze tabel wordt het aantal bewerkingen per seconde voor elk curvetype beschreven.

|Bewerking|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Sleutel maken| 1| 1| 1| 1
Sleutel verwijderen (soft-delete)|10|10|10|10
Sleutel ops purge|10|10|10|10
Back-upsleutel|10|10|10|10
Sleutel herstellen|10|10|10|10
Sleutelinformatie verkrijgen|1100|1100|1100|1100
Teken|260|260|165|56
Verifiëren|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES-sleutelbewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)
- Voor versleutelings- en ontsleutelingsbewerkingen wordt uit van een pakketgrootte van 4 KB uitgenomen.
- Doorvoerlimieten voor versleutelen/ontsleutelen zijn van toepassing op AES-CBC- en AES-GCM-algoritmen.
- Doorvoerlimieten voor Wrap/Unwrap zijn van toepassing op het AES-KW-algoritme.

|Bewerking|128-bits|192-bits|256-bits|
|--|--:|--:|--:|
Sleutel maken|1| 1| 1
Sleutel verwijderen (soft-delete)|10|10|10
Sleutel opskeren|10|10|10
Back-upsleutel|10|10|10
Sleutel herstellen|10|10|10
Sleutelgegevens op halen|1100|1100|1100
Versleutelen|8000|8000 |8000 
Ontsleutelen|8000|8000|8000
Wrap|9000|9000|9000
Unwrap|9000|9000|9000

