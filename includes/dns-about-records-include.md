---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 8ca054b3a3d5147b7d98a021ce1e26d02d5581b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011558"
---
### <a name="record-names"></a>Recordnamen

In Azure DNS worden records opgegeven met behulp van relatieve namen. Een FQDN-domeinnaam (*Fully Qualified Domain Name*) bevat de zonenaam, terwijl een *relatieve* naam deze niet bevat. De relatieve record naam `www` in de zone `contoso.com` geeft bijvoorbeeld de volledig gekwalificeerde record naam `www.contoso.com` .

Een *apexrecord* is een DNS-record in de hoofdmap (of *apex*) van een DNS-zone. In de DNS-zone `contoso.com` heeft een Apex-record bijvoorbeeld ook de volledig gekwalificeerde naam `contoso.com` (dit wordt soms een heet domein  genoemd).  Op conventies wordt de relatieve naam ' \@ ' gebruikt om Apex-records weer te geven.

### <a name="record-types"></a>Recordtypen

Elke DNS-record heeft een naam en een type. Records zijn ingedeeld in verschillende typen overeenkomstig de gegevens die ze bevatten. Het meest voorkomende type is een A-record, waarmee een naam aan een IPv4-adres wordt toegewezen. Een ander algemeen type is een MX-record, waarmee een naam aan een e-mailserver wordt toegewezen.

Azure DNS ondersteunt alle algemene DNS-record typen: A, AAAA, CAA, CNAME,, NS, PTR, SOA, SRV en TXT. Houd er rekening mee dat [SPF-records worden gerepresenteerd door TXT-records](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Recordsets

Soms moet u meer dan één DNS-record maken met een bepaalde naam en een bepaald type. Stel bijvoorbeeld dat de website www.contoso.com wordt gehost op twee verschillende IP-adressen. De website vereist twee verschillende A-records, één voor elk IP-adres. Dit is een voorbeeld van een recordset:

```dns
www.contoso.com.        3600    IN    A    134.170.185.46
www.contoso.com.        3600    IN    A    134.170.188.221
```

Azure DNS beheert alle DNS-records met *recordsets*. Een recordset (ook bekend als een *resource*-recordset) is een verzameling DNS-records in een zone die dezelfde naam hebben en van hetzelfde type zijn. De meeste recordsets bevatten één record. Voorbeelden zoals hierboven waarin een recordset meer dan één record bevat, zijn echter niet ongewoon.

Veronderstel bijvoorbeeld dat u al een A-record 'www' hebt gemaakt in de zone 'contoso.com' die naar het IP-adres '134.170.185.46' verwijst (de eerste record bovenaan).  Als u nu de tweede record wilt maken, moet u deze toevoegen aan de bestaande recordset. U maakt dus geen aanvullende recordset.

De recordtypen SOA en CNAME zijn uitzonderingen. De DNS-standaarden staan voor deze typen niet toe dat er meerdere records zijn met dezelfde naam. Daarom kunnen deze recordsets slechts één record bevatten.