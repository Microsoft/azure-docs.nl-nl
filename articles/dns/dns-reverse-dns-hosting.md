---
title: Reverse DNS lookup zones in Azure DNS
description: Meer informatie over het gebruik van Azure DNS voor het hosten van de reverse DNS lookup zones voor uw IP-bereiken
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 0c85049d6c8921432a753bf08989cab473b7c734
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525124"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Reverse DNS lookup zones in Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit artikel wordt uitgelegd hoe u de reverse DNS-lookup zones host voor uw toegewezen IP-bereiken in Azure DNS. De IP-bereiken die worden vertegenwoordigd door de zones voor reverse lookup moeten worden toegewezen aan uw organisatie, meestal door uw Internet provider.

Zie [Reverse DNS configureren voor services die worden gehost in azure](dns-reverse-dns-for-azure-services.md)voor meer informatie over het configureren van omgekeerde DNS voor een IP-adres dat eigendom is van Azure dat is toegewezen aan uw Azure-service.

Voordat u dit artikel leest, moet u bekend zijn met het [overzicht van omgekeerde DNS en ondersteuning in azure](dns-reverse-dns-overview.md).

Dit artikel begeleidt u stapsgewijs door de stappen voor het maken van uw eerste DNS-zone en-record voor reverse lookup met behulp van de Azure Portal, Azure PowerShell, Azure Classic CLI of Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Een DNS-zone voor reverse lookup maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer in het menu **hub** **nieuwe**  >  **netwerken** en selecteer vervolgens **DNS-zone**.

   ![De selectie van de DNS-zone](./media/dns-reverse-dns-hosting/figure1.png)

1. Geef in het deel venster **DNS-zone maken** een naam op voor uw DNS-zone. De naam van de zone is anders ontworpen voor IPv4-en IPv6-voor voegsels. Gebruik de instructies voor [IPv4](#ipv4) of [IPv6](#ipv6) om uw zone een naam te benoemen. Wanneer u klaar bent, selecteert u **maken** om de zone te maken.

### <a name="ipv4"></a>IPv4

De naam van een IPv4-zone voor reverse lookup is gebaseerd op het IP-bereik dat het vertegenwoordigt. Deze moet de volgende indeling hebben: `<IPv4 network prefix in reverse order>.in-addr.arpa` . Zie [overzicht van omgekeerde DNS en ondersteuning in azure](dns-reverse-dns-overview.md#ipv4)voor voor beelden.

> [!NOTE]
> Als u onverse DNS-lookup zones maakt in Azure DNS, moet u een koppel teken ( `-` ) in plaats van een slash () gebruiken `/` in de zone naam.
>
> Voor het IP-bereik 192.0.2.128/26 moet u bijvoorbeeld `128-26.2.0.192.in-addr.arpa` de zone naam gebruiken in plaats van `128/26.2.0.192.in-addr.arpa` .
>
> Hoewel de DNS-standaard beide methoden ondersteunen, ondersteunt Azure DNS geen DNS-zone namen die het slash- `/` teken () bevatten.

In het volgende voor beeld ziet u hoe u een omgekeerde DNS-zone voor Class C `2.0.192.in-addr.arpa` met de naam in azure DNS maakt via de Azure portal:

 ![Scherm afbeelding die laat zien hoe u een omgekeerde DNS-zone van klasse C met de naam 2.0.192.in-addr. arpa in Azure DNS maakt via de Azure Portal.](./media/dns-reverse-dns-hosting/figure2.png)

De locatie van de **resource groep** definieert de locatie voor de resource groep. Dit heeft geen invloed op de DNS-zone. De locatie van de DNS-zone is altijd 'global' en wordt niet weergegeven.

In de volgende voor beelden ziet u hoe u deze taak kunt uitvoeren met behulp van Azure PowerShell en Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

De naam van een IPv6-zone voor reverse lookup moet de volgende vorm hebben: `<IPv6 network prefix in reverse order>.ip6.arpa` .  Zie [overzicht van omgekeerde DNS en ondersteuning in azure](dns-reverse-dns-overview.md#ipv6)voor voor beelden.


In het volgende voor beeld ziet u hoe u een IPv6 reverse DNS-lookup-zone `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` met de naam in azure DNS maakt via de Azure portal:

 ![Het deel venster ' DNS-zone maken ', waarbij de vakken zijn ingevuld](./media/dns-reverse-dns-hosting/figure3.png)

De locatie van de **resource groep** definieert de locatie voor de resource groep. Dit heeft geen invloed op de DNS-zone. De locatie van de DNS-zone is altijd 'global' en wordt niet weergegeven.

In de volgende voor beelden ziet u hoe u deze taak kunt uitvoeren met behulp van Azure PowerShell en Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Een reverse DNS-opzoek zone delegeren

Nu u de reverse DNS lookup-zone hebt gemaakt, moet u ervoor zorgen dat de zone wordt overgedragen van de bovenliggende zone. DNS-delegering maakt het proces van de DNS-naam omzetting mogelijk om de naam servers te vinden die de zone voor omgekeerde DNS-lookup hosten. Deze naam servers kunnen vervolgens DNS-reverse query's voor de IP-adressen in uw adres bereik beantwoorden.

Voor zones voor forward lookup wordt het proces voor het delegeren van een DNS-zone beschreven in [uw domein delegeren naar Azure DNS](dns-delegate-domain-azure-dns.md). De overdracht voor zones voor reverse lookup werkt op dezelfde manier. Het enige verschil is dat u de naam servers moet configureren met de Internet provider die uw IP-bereik heeft verstrekt, in plaats van uw domein naam registratie.

## <a name="create-a-dns-ptr-record"></a>Een PTR-record van DNS maken

### <a name="ipv4"></a>IPv4

In het volgende voor beeld wordt u begeleid bij het proces van het maken van een PTR-record in een reverse DNS-zone in Azure DNS. Voor andere typen en voor het wijzigen van bestaande records, raadpleegt u [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (DNS-records en -recordsets beheren met behulp van Azure Portal).

1. Selecteer boven in het deel venster **DNS-zone** de optie **+ Recordset** om het deel venster **recordset toevoegen** te openen.

   ![Scherm opname van het deel venster DNS-zone met een pijl die verwijst naar de knop + record set.](./media/dns-reverse-dns-hosting/figure4.png)

1. De naam van de recordset voor een PTR-record moet de rest van het IPv4-adres in omgekeerde volg orde zijn. 

   In dit voor beeld zijn de eerste drie octetten al ingevuld als onderdeel van de zone naam (. 2.0.192). Daarom wordt alleen het laatste octet opgegeven in het vak **naam** . U kunt bijvoorbeeld een naam opgeven voor de Recordset **15** voor een resource waarvan het IP-adres 192.0.2.15 is.  
1. Selecteer voor **type** **PTR**.  
1. Voer bij **domein naam** de Fully QUALIFIED domain name (FQDN) in van de bron die gebruikmaakt van het IP-adres.
1. Selecteer **OK** onder aan het deel venster om de DNS-record te maken.

   ![Het deel venster recordset toevoegen, waarbij vakken zijn ingevuld](./media/dns-reverse-dns-hosting/figure5.png)

In de volgende voor beelden ziet u hoe u deze taak kunt uitvoeren met behulp van Power shell of Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

In het volgende voor beeld wordt het proces voor het maken van een nieuwe PTR-record door lopen. Voor andere typen en voor het wijzigen van bestaande records, raadpleegt u [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (DNS-records en -recordsets beheren met behulp van Azure Portal).

1. Selecteer boven in het deel venster **DNS-zone** de optie **+ Recordset** om het deel venster **recordset toevoegen** te openen.

   ![Knop voor het maken van een recordset](./media/dns-reverse-dns-hosting/figure6.png)

2. De naam van de recordset voor een PTR-record moet de rest van het IPv6-adres in omgekeerde volg orde zijn. Het mag geen nul compressie bevatten. 

   In dit voor beeld zijn de eerste 64 bits van de IPv6 al ingevuld als onderdeel van de zone naam (0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2. ip6. arpa). Daarom worden alleen de laatste 64 bits opgegeven in het vak **naam** . De laatste 64 bits van het IP-adres worden in omgekeerde volg orde ingevoerd, met een punt als scheidings teken tussen elk hexadecimaal getal. U kunt bijvoorbeeld uw recordset **e. 5.0.4.9. f. a. 1. c. b. 0.1.4.2.5. f** noemen voor een resource waarvan het IP-adres 2001 is: 0db8: abdc: 0000: f524:10BC: 1af9:405e.  
3. Selecteer voor **type** **PTR**.  
4. Voer bij **domein naam** de FQDN in van de bron die gebruikmaakt van het IP-adres.
5. Selecteer **OK** onder aan het deel venster om de DNS-record te maken.

![Scherm opname van het deel venster record sets toevoegen met een pijl die verwijst naar de waarde in het veld type.](./media/dns-reverse-dns-hosting/figure7.png)

In de volgende voor beelden ziet u hoe u deze taak kunt uitvoeren met behulp van Power shell of Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Records weergeven

Als u de records wilt weer geven die u hebt gemaakt, bladert u naar uw DNS-zone in de Azure Portal. In het onderste gedeelte van het deel venster **DNS-zone** ziet u de records voor de DNS-zone. U moet de standaard-NS-en SOA-records zien, plus alle nieuwe records die u hebt gemaakt. De NS-en SOA-records worden in elke zone gemaakt. 

### <a name="ipv4"></a>IPv4

In het deel venster **DNS-zone** worden de IPv4 PTR-records weer gegeven:

![Deel venster DNS-zone met IPv4-records](./media/dns-reverse-dns-hosting/figure8.png)

In de volgende voor beelden ziet u hoe u de PTR-records kunt weer geven met behulp van Power shell of Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

In het deel venster **DNS-zone** worden de IPv6 PTR-records weer gegeven:

![Deel venster DNS-zone met IPv6-records](./media/dns-reverse-dns-hosting/figure9.png)

In de volgende voor beelden ziet u hoe u de records kunt weer geven met behulp van Power shell of Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Veelgestelde vragen

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Kan ik reverse DNS-lookup zones hosten voor door mijn provider toegewezen IP-blokken op Azure DNS?

Ja. Het hosten van de zones voor reverse lookup (ARPA) voor uw eigen IP-bereiken in Azure DNS wordt volledig ondersteund.

Maak de zone voor reverse lookup in Azure DNS zoals beschreven in dit artikel, en werk vervolgens samen met uw provider om [de zone te delegeren](dns-domain-delegation.md). U kunt de PTR-records voor elke reverse lookup op dezelfde manier beheren als andere record typen.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Wat is het hosten van mijn reverse DNS lookup zone kosten?

Het hosten van de reverse DNS lookup-zone voor uw door de Internet provider toegewezen IP-blok in Azure DNS wordt in rekening gebracht tegen [standaard Azure DNS tarieven](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Kan ik reverse DNS-lookup zones hosten voor IPv4-en IPv6-adressen in Azure DNS?

Ja. In dit artikel wordt uitgelegd hoe u de reverse DNS-lookup zones voor IPv4 en IPv6 maakt in Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Kan ik een bestaande reverse DNS lookup-zone importeren?

Ja. U kunt Azure CLI gebruiken voor het importeren van bestaande DNS-zones in Azure DNS. Deze methode is geschikt voor zones voor forward lookup en zones voor reverse lookup.

Zie voor meer informatie [een DNS-zone bestand importeren en exporteren met behulp van Azure cli](dns-import-export.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Reverse DNS-zoek opdracht op Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)voor meer informatie over omgekeerde DNS.
<br>
Meer informatie over het [beheren van reverse DNS-records voor uw Azure-Services](dns-reverse-dns-for-azure-services.md).
