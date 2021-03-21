---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: d934d40cad5f4eec929cfd273b6e30ea291e48d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010950"
---
Azure Key Vault-service ondersteunt twee resource typen: kluizen en beheerde Hsm's. In de volgende twee secties worden de service limieten voor elk van beide beschreven.

### <a name="resource-type-vault"></a>Resource type: kluis

In deze sectie worden de service limieten voor het resource type beschreven `vaults` .

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

#### <a name="azure-private-link-integration"></a>Azure Private Link- integratie

> [!NOTE]
> Het aantal sleutelkluizen met ingeschakelde privé-eindpunten per abonnement is een aanpasbare limiet. De limiet die hieronder wordt weergegeven, is de standaardlimiet. Als u een hogere limiet wilt aanvragen voor uw service, kunt u een e-mail sturen naar akv-privatelink@microsoft.com. We zullen deze aanvragen per geval goedkeuren.

| Resource | Limiet |
| -------- | -----:|
| Privé-eindpunten per sleutelkluis | 64 |
| Sleutelkluizen met privé-eindpunten per abonnement | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Resource type: beheerde HSM (preview-versie)

In deze sectie worden de service limieten voor het resource type beschreven `managed HSM` .

#### <a name="object-limits"></a>Object limieten

|Item|Limieten|
|----|------:|
Aantal HSM-exemplaren per abonnement per regio|1 (tijdens de preview-versie)
Aantal sleutels per HSM-groep|5000
Aantal versies per sleutel|100
Aantal definities van aangepaste rollen per HSM|50
Aantal roltoewijzingen in het HSM-bereik|50
Aantal roltoewijzingen voor elk afzonderlijke sleutel bereik|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Transactie limieten voor beheer bewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)
|Bewerking |Aantal bewerkingen per seconde|
|----|------:|
Alle RBAC-bewerkingen<br/>(bevat alle ruwe bewerkingen voor roldefinities en roltoewijzingen)|5
Volledige HSM-back-up/herstellen<br/>(er wordt slechts één gelijktijdige back-up-of herstel bewerking per HSM-exemplaar ondersteund)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Transactie limieten voor cryptografische bewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)

- Elke beheerde HSM-instantie bestaat uit drie taak verdelingen HSM-partities. De doorvoer limieten zijn een functie van de onderliggende toegewezen hardware-capaciteit voor elke partitie. In de onderstaande tabellen wordt de maximale door Voer weer gegeven met ten minste één partitie beschikbaar. De werkelijke door Voer kan Maxi maal 3x hoger zijn als alle drie de partities beschikbaar zijn.
- Er wordt ervan uitgegaan dat er één sleutel wordt gebruikt om maximale door voer te krijgen. Als er bijvoorbeeld één RSA-2048-sleutel wordt gebruikt, is de maximale door Voer 1100 teken bewerkingen. Als u 1100 verschillende sleutels gebruikt met 1 trans actie per seconde, kunnen ze niet dezelfde door Voer uitvoeren.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA-sleutel bewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)

|Bewerking|2048-bits|3072-bits|4096-bits|
|--|--:|--:|--:|
Sleutel maken|1| 1| 1
Sleutel verwijderen (voorlopig verwijderen)|10|10|10 
Sleutel opschonen|10|10|10 
Back-upsleutel|10|10|10 
Sleutel herstellen|10|10|10 
Belang rijke gegevens ophalen|1100|1100|1100
Versleutelen|10.000|10.000|6000
Ontsleutelen|1100|360|160
Wrap|10.000|10.000|6000
Unwrap|1100|360|160
Teken|1100|360|160
Verifiëren|10.000|10.000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EC-sleutel bewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)

In deze tabel wordt het aantal bewerkingen per seconde voor elk type curve beschreven.

|Bewerking|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Sleutel maken| 1| 1| 1| 1
Sleutel verwijderen (voorlopig verwijderen)|10|10|10|10
Sleutel opschonen|10|10|10|10
Back-upsleutel|10|10|10|10
Sleutel herstellen|10|10|10|10
Belang rijke gegevens ophalen|1100|1100|1100|1100
Teken|260|260|165|56
Verifiëren|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES-sleutel bewerkingen (aantal bewerkingen per seconde per HSM-exemplaar)
- Voor versleutelings-en ontsleutel bewerkingen wordt uitgegaan van een 4KB-pakket grootte.
- Doorvoer limieten voor versleutelen/ontsleutelen zijn van toepassing op AES-CBC-en AES-GCM-algoritmen.
- Doorvoer limieten voor omloop/uitpakken zijn van toepassing op het AES-KW-algoritme.

|Bewerking|128-bits|192-bits|256-bits|
|--|--:|--:|--:|
Sleutel maken|1| 1| 1
Sleutel verwijderen (voorlopig verwijderen)|10|10|10
Sleutel opschonen|10|10|10
Back-upsleutel|10|10|10
Sleutel herstellen|10|10|10
Belang rijke gegevens ophalen|1100|1100|1100
Versleutelen|8000|8000 |8000 
Ontsleutelen|8000|8000|8000
Wrap|9000|9000|9000
Unwrap|9000|9000|9000

