---
title: DNS-records in Azure DNS beheren met de Azure CLI
description: DNS-record sets en-records beheren op Azure DNS wanneer uw domein wordt gehost op Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 2d3989b3c477a35d602f1ccf3e45d6f597f5d78d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011559"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>DNS-records en-record sets beheren in Azure DNS met behulp van de Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure-CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

In dit artikel wordt beschreven hoe u DNS-records voor uw DNS-zone beheert met behulp van de platformoverschrijdende Azure CLI, die beschikbaar is voor Windows, Mac en Linux. U kunt ook uw DNS-records beheren met behulp van [Azure PowerShell](dns-operations-recordsets.md) of de [Azure Portal](dns-operations-recordsets-portal.md).

In de voor beelden in dit artikel wordt ervan uitgegaan dat u [de Azure cli al hebt geïnstalleerd en dat u bent aangemeld, en een DNS-zone hebt gemaakt](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Inleiding

Voordat u DNS-records in DNS Azure maakt, leest u eerst hoe Azure DNS DNS-records organiseert in DNS-recordsets.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Zie [DNS-zones en -records](dns-zones-records.md) voor meer informatie over DNS-records in Azure DNS.

## <a name="create-a-dns-record"></a>Een DNS-record maken

Als u een DNS-record wilt maken, gebruikt u de `az network dns record-set <record-type> add-record` opdracht (waarbij `<record-type>` het type record is, dat wil zeggen een, SRV, txt, enz.) Zie voor meer informatie `az network dns record-set --help` .

Bij het maken van een record, dient u de naam van de resourcegroep, de zonenaam, de naam van de recordset, het recordtype en de details van de record die wordt gemaakt op te geven. De opgegeven naam van de recordset moet een *relatieve* naam zijn, wat betekent dat de naam van de zone moet worden uitgesloten.

Als de recordset nog niet bestaat, maakt u er met deze opdracht een. Als de recordset al bestaat, voegt u met deze opdracht de opgegeven record toe aan de bestaande recordset.

Als er een nieuwe recordset wordt gemaakt, wordt een standaard time-to-live (TTL) van 3600 gebruikt. Zie [een DNS-recordset maken](#create-a-dns-record-set)voor instructies over het gebruik van verschillende TTLS.

In het volgende voorbeeld maakt u een A-record met de naam *www* in de zone *contoso.com* in de resourcegroep *MyResourceGroup*. Het IP-adres van de A-record is *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Als u een recordset wilt maken in de Apex van de zone (in dit geval ' contoso.com '), gebruikt u de record naam ' \@ ', inclusief de aanhalings tekens:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Een DNS-recordset maken

In de bovenstaande voor beelden is de DNS-record toegevoegd aan een bestaande recordset of is de Recordset *impliciet* gemaakt. U kunt ook de Recordset *expliciet* maken voordat u records toevoegt. Azure DNS ondersteunt ' empty ' record sets, die als tijdelijke aanduiding kunnen fungeren om een DNS-naam te reserveren voordat u DNS-records maakt. Lege record sets zijn zichtbaar in het Azure DNS besturings vlak, maar worden niet weer gegeven op de Azure DNS naam servers.

Record sets worden gemaakt met behulp van de `az network dns record-set <record-type> create` opdracht. Zie `az network dns record-set <record-type> create --help` voor help.

Als u de recordset expliciet maakt, kunt u de eigenschappen van de recordset opgeven, zoals [TTL (time-to-Live)](dns-zones-records.md#time-to-live) en meta gegevens. [META](dns-zones-records.md#tags-and-metadata) gegevens van de recordset kunnen worden gebruikt om toepassingsspecifieke informatie te koppelen aan elke recordset, als sleutel-waardeparen.

In het volgende voor beeld wordt een lege recordset van het type ' A ' met een TTL van 60-Second gemaakt met behulp van de `--ttl` para meter (kort formulier `-l` ):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

In het volgende voor beeld wordt een recordset gemaakt met twee meta gegevens items, afd = Finance en Environment = Production, met behulp van de `--metadata` para meter:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Als u een lege recordset hebt gemaakt, kunnen records worden toegevoegd, `azure network dns record-set <record-type> add-record` zoals beschreven in [een DNS-record maken](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Records van andere typen maken

In detail hoe u ' A '-records maakt, ziet u in de volgende voor beelden hoe u een record maakt van andere record typen die door Azure DNS worden ondersteund.

De parameters die u gebruikt om de gegevens van de record op te geven, variëren afhankelijk van het type record. Voor een record van het type 'A' geeft u bijvoorbeeld het IPv4-adres met de parameter `--ipv4-address <IPv4 address>` op. De para meters voor elk record type kunnen worden weer gegeven met `az network dns record-set <record-type> add-record --help` .

In elk geval laten we zien hoe u één record maakt. De record wordt toegevoegd aan de bestaande recordset of een recordset die impliciet is gemaakt. Zie [een DNS-recordset maken](#create-a-dns-record-set)voor meer informatie over het maken van record sets en het definiëren van een recordset-para meter.

We geven geen voor beeld van het maken van een SOA-Recordset, omdat SOAs zijn gemaakt en verwijderd met elke DNS-zone en niet afzonderlijk kunnen worden gemaakt of verwijderd. [De SOA kan echter worden gewijzigd, zoals wordt weer gegeven in een later voor beeld](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Een AAAA-record maken

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Een CAA-record maken

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Een CNAME-record maken

> [!NOTE]
> De DNS-standaarden staan geen CNAME-records toe aan de Apex van een zone ( `--Name "@"` ) en kunnen ook geen record sets met meer dan één record toestaan.
> 
> Zie [CNAME records](dns-zones-records.md#cname-records)voor meer informatie.

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Een MX-record maken

In dit voor beeld gebruiken we de naam van de Recordset \@ om de MX-record te maken op de zone Apex (in dit geval ' contoso.com ').

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Een NS-record maken

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Een PTR-record maken

In dit geval vertegenwoordigt ' my-arpa-zone.com ' de ARPA-zone die uw IP-bereik voor stelt. Elke PTR-recordset die is ingesteld in deze zone komt overeen met een IP-adres in dit IP-bereik.  De record naam ' 10 ' is het laatste octet van het IP-adres binnen dit IP-bereik dat door deze record wordt weer gegeven.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Een SRV-record maken

Wanneer u een [SRV-recordset](dns-zones-records.md#srv-records)maakt, geeft u de *\_ service* en het *\_ protocol* op in de naam van de recordset. Het is niet nodig om " \@ " in de naam van de recordset op te nemen bij het maken van een SRV-recordset op de zone Apex.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Een TXT-record maken

In het volgende voor beeld ziet u hoe u een TXT-record maakt. Zie [TXT records](dns-zones-records.md#txt-records)(Engelstalig) voor meer informatie over de maximale teken reeks lengte die wordt ondersteund in TXT-records.

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Een recordset ophalen

Als u een bestaande recordset wilt ophalen, gebruikt u `az network dns record-set <record-type> show` . Zie `az network dns record-set <record-type> show --help` voor help.

Als bij het maken van een record of recordset moet de opgegeven naam van de recordset een *relatieve* naam zijn, wat betekent dat de naam van de zone moet worden uitgesloten. U moet ook het record type opgeven, de zone met de recordset en de resource groep die de zone bevat.

In het volgende voor beeld wordt de record- *www* van het type A uit zone *contoso.com* in de resource groep *MyResourceGroup* opgehaald:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Record sets weer geven

U kunt alle records in een DNS-zone weer geven met behulp van de `az network dns record-set list` opdracht. Zie `az network dns record-set list --help` voor help.

In dit voor beeld worden alle record sets in de zone *contoso.com* als resultaat gegeven in de *MyResourceGroup* van de resource groep, ongeacht de naam of het record type:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

In dit voor beeld worden alle record sets geretourneerd die overeenkomen met het gegeven record type (in dit geval ' A ' records):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Een record toevoegen aan een bestaande recordset

U kunt `az network dns record-set <record-type> add-record` beide gebruiken om een record in een nieuwe recordset te maken of om een record toe te voegen aan een bestaande recordset.

Zie voor meer informatie [een DNS-record maken](#create-a-dns-record) en [records van andere typen maken](#create-records-of-other-types) .

## <a name="remove-a-record-from-an-existing-record-set"></a>Een record verwijderen uit een bestaande recordset.

Als u een DNS-record uit een bestaande recordset wilt verwijderen, gebruikt u `az network dns record-set <record-type> remove-record` . Zie `az network dns record-set <record-type> remove-record -h` voor help.

Met deze opdracht wordt een DNS-record uit een recordset verwijderd. Als de laatste record in een recordset wordt verwijderd, wordt de recordset zelf ook verwijderd. Gebruik de optie om in plaats daarvan de lege recordset te blijven instellen `--keep-empty-record-set` .

U moet de record opgeven die moet worden verwijderd en de zone waarvan deze moet worden verwijderd, met dezelfde para meters als bij het maken van een record met `az network dns record-set <record-type> add-record` . Deze para meters worden beschreven in [een DNS-record maken](#create-a-dns-record) en [records van andere typen maken](#create-records-of-other-types) .

In het volgende voor beeld wordt de A-record met de waarde 1.2.3.4 verwijderd uit de recordset met de naam ' *www* ' in de zone *contoso.com* in de resource groep *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Een bestaande recordset wijzigen

Elke recordset bevat een [time-to-Live (TTL)](dns-zones-records.md#time-to-live)-, [meta data](dns-zones-records.md#tags-and-metadata)-en DNS-records. In de volgende secties wordt uitgelegd hoe u elk van deze eigenschappen kunt wijzigen.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Een A, AAAA, CAA, MX, NS, PTR, SRV of TXT-record wijzigen

Als u een bestaande record van het type A, AAAA, CAA, MX,, PTR, SRV of TXT, wilt wijzigen, moet u eerst een nieuwe record toevoegen en vervolgens de bestaande record verwijderen. Zie de eerdere secties in dit artikel voor meer informatie over het verwijderen en toevoegen van records.

In het volgende voor beeld ziet u hoe u een A-record wijzigt van IP-adres 1.2.3.4 naar IP-adres 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

U kunt geen records toevoegen, verwijderen of wijzigen in de automatisch gemaakte NS-recordset die is ingesteld op de zone Apex ( `--Name "@"` , inclusief de aanhalings tekens). De enige toegestane wijzigingen zijn alleen van invloed op de ingestelde TTL en meta gegevens van de recordset.

### <a name="to-modify-a-cname-record"></a>Een CNAME-record wijzigen

In tegens telling tot de meeste andere record types kan een CNAME-recordset alleen één record bevatten.  Daarom kunt u de huidige waarde niet vervangen door een nieuwe record toe te voegen en de bestaande record te verwijderen, net als bij andere record typen.

In plaats daarvan kunt u een CNAME-record wijzigen met `az network dns record-set cname set-record` . Meer informatie vindt u in `az network dns record-set cname set-record --help`

In het voor beeld wordt de CNAME-recordset set *www* in de zone *contoso.com* in de resource groep *MyResourceGroup* gewijzigd in ' www.fabrikam.net ' in plaats van de bestaande waarde:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Een SOA-record wijzigen

In tegens telling tot de meeste andere record types kan een CNAME-recordset alleen één record bevatten.  Daarom kunt u de huidige waarde niet vervangen door een nieuwe record toe te voegen en de bestaande record te verwijderen, net als bij andere record typen.

In plaats daarvan kunt u het SOA-record wijzigen `az network dns record-set soa update` . Zie `az network dns record-set soa update --help` voor help.

In het volgende voor beeld ziet u hoe u de eigenschap Email van de SOA-record voor de zone *contoso.com* in de resource groep *MyResourceGroup* kunt instellen:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>NS-records wijzigen op de zone Apex

De NS-record die is ingesteld op de zone Apex wordt automatisch gemaakt met elke DNS-zone. Het bevat de namen van de Azure DNS naam servers die zijn toegewezen aan de zone.

U kunt aanvullende naam servers toevoegen aan deze NS-Recordset, ter ondersteuning van co-hosting domeinen met meer dan één DNS-provider. U kunt ook de TTL en meta gegevens voor deze recordset wijzigen. U kunt de vooraf ingevulde Azure DNS naam servers echter niet verwijderen of wijzigen.

Houd er rekening mee dat dit alleen geldt voor de NS-recordset die is ingesteld op de zone Apex. Andere NS-record sets in uw zone (zoals gebruikt voor het delegeren van onderliggende zones) kunnen zonder beperking worden gewijzigd.

In het volgende voor beeld ziet u hoe u een extra naam server toevoegt aan de NS-record die is ingesteld op de zone Apex:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>De TTL van een bestaande recordset wijzigen

Als u de TTL van een bestaande recordset wilt wijzigen, gebruikt u `azure network dns record-set <record-type> update` . Zie `azure network dns record-set <record-type> update --help` voor help.

In het volgende voor beeld ziet u hoe u een set-TTL voor een record wijzigt, in dit geval 60 seconden:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>De meta gegevens van een bestaande recordset wijzigen

[META](dns-zones-records.md#tags-and-metadata) gegevens van de recordset kunnen worden gebruikt om toepassingsspecifieke informatie te koppelen aan elke recordset, als sleutel-waardeparen. Gebruik om de meta gegevens van een bestaande recordset te wijzigen `az network dns record-set <record-type> update` . Zie `az network dns record-set <record-type> update --help` voor help.

In het volgende voor beeld ziet u hoe u een recordset wijzigt met twee meta gegevens, "afd = Finance" en "Environment = Production". Houd er rekening mee dat bestaande meta gegevens worden *vervangen* door de gegeven waarden.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Een recordset verwijderen

Record sets kunnen worden verwijderd met behulp van de `az network dns record-set <record-type> delete` opdracht. Zie `azure network dns record-set <record-type> delete --help` voor help. Als u een recordset verwijdert, worden ook alle records in de recordset verwijderd.

> [!NOTE]
> U kunt de SOA-en NS-record sets niet verwijderen uit de zone Apex ( `--name "@"` ).  Deze worden automatisch gemaakt wanneer de zone werd gemaakt en worden automatisch verwijderd wanneer de zone wordt verwijderd.

In het volgende voor beeld wordt de recordset met de naam *www* van type A uit de zone *contoso.com* in de resource groep *MyResourceGroup* verwijderd:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

U wordt gevraagd om de Verwijder bewerking te bevestigen. Gebruik de schakel optie om deze prompt te onderdrukken `--yes` .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [zones en records in azure DNS](dns-zones-records.md).
<br>
Meer informatie over het [beveiligen van uw zones en records](dns-protect-zones-recordsets.md) bij het gebruik van Azure DNS.
