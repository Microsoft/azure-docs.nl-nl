---
title: DNS-records beheren in Azure DNS met behulp van Azure PowerShell | Microsoft Docs
description: DNS-record sets en-records beheren op Azure DNS wanneer uw domein wordt gehost op Azure DNS. Alle Power shell-opdrachten voor bewerkingen op record sets en records.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011588"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Beheer DNS-records en-record sets in Azure DNS met behulp van Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Klassieke Azure-CLI](./dns-operations-recordsets-cli.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

In dit artikel wordt beschreven hoe u DNS-records voor uw DNS-zone beheert met behulp van Azure PowerShell. DNS-records kunnen ook worden beheerd met behulp van de platformoverschrijdende [Azure cli](dns-operations-recordsets-cli.md) of de [Azure Portal](dns-operations-recordsets-portal.md).

In de voor beelden in dit artikel wordt ervan uitgegaan dat u Azure PowerShell al hebt [geïnstalleerd, aangemeld en een DNS-zone hebt gemaakt](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introductie

Voordat u DNS-records in DNS Azure maakt, leest u eerst hoe Azure DNS DNS-records organiseert in DNS-recordsets.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Zie [DNS-zones en -records](dns-zones-records.md) voor meer informatie over DNS-records in Azure DNS.


## <a name="create-a-new-dns-record"></a>Een nieuwe DNS-record maken

Als uw nieuwe record dezelfde naam en hetzelfde type als een bestaande record heeft, moet u [deze toevoegen aan de bestaande recordset](#add-a-record-to-an-existing-record-set). Als uw nieuwe record een andere naam heeft en een ander type voor alle bestaande records bevat, moet u een nieuwe recordset maken. 

### <a name="create-a-records-in-a-new-record-set"></a>A-records in een nieuwe recordset maken

U kunt recordsets maken met behulp van de cmdlet `New-AzDnsRecordSet`. Wanneer u een recordset maakt, moet u de naam van de recordset, de zone, de TTL (time to Live), het record type en de te maken records opgeven.

De parameters voor het toevoegen van records aan een recordset variëren afhankelijk van het type recordset. Als u bijvoorbeeld een recordset van het type ' A ' gebruikt, moet u het IP-adres opgeven met de para meter `-IPv4Address` . Andere para meters worden gebruikt voor andere record typen. Zie voor beelden van aanvullende record typen voor meer informatie.

In het volgende voor beeld wordt een recordset gemaakt met de relatieve naam ' www ' in de DNS-zone ' contoso.com '. De volledig gekwalificeerde naam van de recordset is ' www.contoso.com '. Het record type is ' A ' en de TTL is 3600 seconden. De recordset bevat één record met het IP-adres 1.2.3.4.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Als u een recordset wilt maken in de ' Apex ' van een zone (in dit geval ' contoso.com '), gebruikt u de naam van de Recordset \@ (exclusief aanhalings tekens):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Als u een recordset met meer dan één record wilt maken, maakt u eerst een lokale matrix en voegt u de records toe en geeft u de matrix `New-AzDnsRecordSet` als volgt door:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[META](dns-zones-records.md#tags-and-metadata) gegevens van de recordset kunnen worden gebruikt om toepassingsspecifieke informatie te koppelen aan elke recordset, als sleutel-waardeparen. In het volgende voor beeld ziet u hoe u een recordset maakt met twee meta gegevens items: ' afd = Finance ' en ' environment = production '.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS biedt ook ondersteuning voor lege record sets, die als tijdelijke aanduiding kunnen fungeren om een DNS-naam te reserveren voordat u DNS-records maakt. Lege record sets zijn zichtbaar in het Azure DNS besturings vlak, maar worden wel op de Azure DNS naam servers weer gegeven. In het volgende voor beeld wordt een lege recordset gemaakt:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Records van andere typen maken

In detail hoe u ' A '-records maakt, ziet u in de volgende voor beelden hoe u records maakt van andere record typen die door Azure DNS worden ondersteund.

In elk geval laten we zien hoe u een recordset met één record maakt. De eerdere voor beelden voor A-records kunnen worden aangepast om record sets te maken met andere typen die meerdere records bevatten, met meta gegevens of om lege record sets te maken.

We geven geen voor beeld van het maken van een SOA-Recordset, omdat SOAs zijn gemaakt en verwijderd met elke DNS-zone en niet afzonderlijk kunnen worden gemaakt of verwijderd. [De SOA kan echter worden gewijzigd, zoals wordt weer gegeven in een later voor beeld](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Een AAAA-recordset met één record maken

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Een CAA-recordset met één record maken

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Een CNAME-recordset met één record maken

> [!NOTE]
> De DNS-standaarden staan geen CNAME-records toe aan de Apex van een zone ( `-Name '@'` ) en kunnen ook geen record sets met meer dan één record toestaan.
> 
> Zie [CNAME records](dns-zones-records.md#cname-records)voor meer informatie.


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Een MX-recordset met één record maken

In dit voor beeld gebruiken we de naam van de Recordset \@ om een MX-record te maken op de zone Apex (in dit geval ' contoso.com ').


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Een NS-recordset met één record maken

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Een PTR-recordset met één record maken

In dit geval vertegenwoordigt ' my-arpa-zone.com ' de zone voor reverse lookup van de ARPA die uw IP-bereik vertegenwoordigt. Elke PTR-recordset die is ingesteld in deze zone komt overeen met een IP-adres in dit IP-bereik. De record naam ' 10 ' is het laatste octet van het IP-adres binnen dit IP-bereik dat door deze record wordt weer gegeven.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Een SRV-recordset met één record maken

Wanneer u een [SRV-recordset](dns-zones-records.md#srv-records)maakt, geeft u de *\_ service* en het *\_ protocol* op in de naam van de recordset. Het is niet nodig om ' \@ ' in de naam van de recordset op te nemen bij het maken van een SRV-recordset op de zone Apex.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Een TXT-recordset met één record maken

In het volgende voor beeld ziet u hoe u een TXT-record maakt. Zie [TXT records](dns-zones-records.md#txt-records)(Engelstalig) voor meer informatie over de maximale teken reeks lengte die wordt ondersteund in TXT-records.

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Een recordset ophalen

Als u een bestaande recordset wilt ophalen, gebruikt u `Get-AzDnsRecordSet` . Met deze cmdlet wordt een lokaal object geretourneerd dat de recordset vertegenwoordigt in Azure DNS.

Net als met moet `New-AzDnsRecordSet` de opgegeven naam van de recordset een *relatieve* naam zijn, wat betekent dat de naam van de zone moet worden uitgesloten. U moet ook het record type en de zone met de recordset opgeven.

In het volgende voor beeld ziet u hoe u een recordset ophaalt. In dit voor beeld wordt de zone opgegeven met behulp van de `-ZoneName` `-ResourceGroupName` para meters en.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

U kunt ook de zone opgeven met behulp van een zone-object, door gegeven met behulp van de `-Zone` para meter.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Record sets weer geven

U kunt ook gebruiken `Get-AzDnsZone` om record sets weer te geven in een zone, door de `-Name` para meters en/of te weglaten `-RecordType` .

In het volgende voor beeld worden alle record sets in de zone geretourneerd:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

In het volgende voor beeld ziet u hoe alle record sets van een bepaald type kunnen worden opgehaald door het record type op te geven, terwijl de naam van de recordset wordt wegge laten:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Als u alle record sets met een bepaalde naam wilt ophalen, moet u voor alle record typen alle record sets ophalen en vervolgens de resultaten filteren:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

In alle bovenstaande voor beelden kan de zone worden opgegeven met behulp van de `-ZoneName` `-ResourceGroupName` para meters (en) (zoals weer gegeven) of door een zone-object op te geven:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Een record toevoegen aan een bestaande recordset

Voer de volgende drie stappen uit om een record toe te voegen aan een bestaande recordset:

1. De bestaande recordset ophalen

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Voeg de nieuwe record toe aan de lokale Recordset. Dit is een off line bewerking.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Voer de wijziging door in de Azure DNS-service. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Met behulp van `Set-AzDnsRecordSet` wordt de bestaande recordset in azure DNS (en alle records erin bevat) *vervangen* door de opgegeven recordset. Er worden [ETAG-controles](dns-zones-records.md#etags) gebruikt om ervoor te zorgen dat gelijktijdige wijzigingen niet worden overschreven. U kunt de optionele `-Overwrite` Schakel optie gebruiken om deze controles te onderdrukken.

Deze volg orde van bewerkingen kan ook worden *gepiped*, wat betekent dat u het Recordset-object door geven met behulp van de sluis in plaats van het door te geven als een para meter:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

In de bovenstaande voor beelden ziet u hoe u een A-record toevoegt aan een bestaande recordset van het type A. Een vergelijk bare reeks bewerkingen wordt gebruikt om records toe te voegen aan record sets van andere typen, `-Ipv4Address` waarbij de para meter van wordt vervangen `Add-AzDnsRecordConfig` door andere para meters die specifiek zijn voor elk record type. De para meters voor elk record type zijn hetzelfde als voor de `New-AzDnsRecordConfig` cmdlet, zoals wordt weer gegeven in aanvullende voor beelden van record typen hierboven.

Record sets van het type ' CNAME ' of ' SOA ' mogen niet meer dan één record bevatten. Deze beperking doet zich voor op basis van de DNS-standaarden. Het is geen beperking van Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Een record uit een bestaande recordset verwijderen

Het proces om een record uit een recordset te verwijderen, is vergelijkbaar met het proces om een record toe te voegen aan een bestaande recordset:

1. De bestaande recordset ophalen

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Verwijder de record uit het lokale Recordset-object. Dit is een off line bewerking. De record die wordt verwijderd, moet exact overeenkomen met een bestaande record in alle para meters.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Voer de wijziging door in de Azure DNS-service. Gebruik de optionele `-Overwrite` Schakel optie om [ETAG-controles](dns-zones-records.md#etags) voor gelijktijdige wijzigingen te onderdrukken.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Als u de voor gaande reeks gebruikt om de laatste record uit een recordset te verwijderen, wordt de recordset niet verwijderd, maar blijft een lege recordset. Als u een recordset volledig wilt verwijderen, raadpleegt u [een recordset verwijderen](#delete-a-record-set).

Net als bij het toevoegen van records aan een Recordset, kan de volg orde van bewerkingen voor het verwijderen van een recordset ook worden gepiped:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Verschillende record typen worden ondersteund door de juiste typespecifieke para meters door te geven aan `Remove-AzDnsRecordSet` . De para meters voor elk record type zijn hetzelfde als voor de `New-AzDnsRecordConfig` cmdlet, zoals wordt weer gegeven in aanvullende voor beelden van record typen hierboven.


## <a name="modify-an-existing-record-set"></a>Een bestaande recordset wijzigen

De stappen voor het wijzigen van een bestaande recordset zijn vergelijkbaar met de stappen die u moet nemen bij het toevoegen of verwijderen van records uit een Recordset:

1. Haal de bestaande recordset op met behulp van `Get-AzDnsRecordSet` .
2. Wijzig het object lokale recordset door:
    * Records toevoegen of verwijderen
    * De para meters van bestaande records wijzigen
    * De meta gegevens van de recordset en time to Live (TTL) wijzigen
3. Voer uw wijzigingen door met behulp van de `Set-AzDnsRecordSet` cmdlet. Hiermee wordt de bestaande recordset in Azure DNS *vervangen* door de opgegeven recordset.

Bij gebruik `Set-AzDnsRecordSet` worden [ETAG-controles](dns-zones-records.md#etags) gebruikt om ervoor te zorgen dat gelijktijdige wijzigingen niet worden overschreven. U kunt de optionele `-Overwrite` Schakel optie gebruiken om deze controles te onderdrukken.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Een record in een bestaande recordset bijwerken

In dit voor beeld wijzigen we het IP-adres van een bestaande A-record:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Een SOA-record wijzigen

U kunt geen records toevoegen aan of verwijderen uit de automatisch gemaakte SOA-record die is ingesteld op de zone Apex ( `-Name "@"` , inclusief de aanhalings tekens). U kunt echter elk van de para meters in de SOA-record (met uitzonde ring van ' host ') en de set-TTL van de record wijzigen.

In het volgende voor beeld ziet u hoe u de *e-mail* eigenschap van de SOA-record wijzigt:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>NS-records wijzigen op de zone Apex

De NS-record die is ingesteld op de zone Apex wordt automatisch gemaakt met elke DNS-zone. Het bevat de namen van de Azure DNS naam servers die zijn toegewezen aan de zone.

U kunt aanvullende naam servers toevoegen aan deze NS-Recordset, ter ondersteuning van co-hosting domeinen met meer dan één DNS-provider. U kunt ook de TTL en meta gegevens voor deze recordset wijzigen. U kunt de vooraf ingevulde Azure DNS naam servers echter niet verwijderen of wijzigen.

Houd er rekening mee dat dit alleen geldt voor de NS-recordset die is ingesteld op de zone Apex. Andere NS-record sets in uw zone (zoals gebruikt voor het delegeren van onderliggende zones) kunnen zonder beperking worden gewijzigd.

In het volgende voor beeld ziet u hoe u een extra naam server toevoegt aan de NS-record die is ingesteld op de zone Apex:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Meta gegevens van record sets wijzigen

[META](dns-zones-records.md#tags-and-metadata) gegevens van de recordset kunnen worden gebruikt om toepassingsspecifieke informatie te koppelen aan elke recordset, als sleutel-waardeparen.

In het volgende voor beeld ziet u hoe u de meta gegevens van een bestaande recordset wijzigt:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Een recordset verwijderen

Record sets kunnen worden verwijderd met behulp van de- `Remove-AzDnsRecordSet` cmdlet. Als u een recordset verwijdert, worden ook alle records in de recordset verwijderd.

> [!NOTE]
> U kunt de SOA-en NS-record sets niet verwijderen uit de zone Apex ( `-Name '@'` ).  Azure DNS deze automatisch gemaakt wanneer de zone werd gemaakt en worden ze automatisch verwijderd wanneer de zone wordt verwijderd.

In het volgende voor beeld ziet u hoe u een recordset verwijdert. In dit voor beeld worden de naam van de recordset, het type record sets, de zone naam en de resource groep expliciet opgegeven.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

U kunt ook de recordset opgeven met de naam en het type en de zone die is opgegeven met behulp van een object:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Als derde optie kan de recordset zelf worden opgegeven met behulp van een Recordset-object:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Wanneer u de recordset opgeeft die moet worden verwijderd met behulp van een Recordset-object, worden [ETAG-controles](dns-zones-records.md#etags) gebruikt om ervoor te zorgen dat gelijktijdige wijzigingen niet worden verwijderd. U kunt de optionele `-Overwrite` Schakel optie gebruiken om deze controles te onderdrukken.

Het object Recordset kan ook worden gepiped in plaats van worden door gegeven als een para meter:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Bevestigingsprompts

De cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` en `Remove-AzDnsRecordSet` ondersteunen bevestigingspromts.

Elke cmdlet vraagt om bevestiging als de `$ConfirmPreference` Power shell-voorkeurs variabele een waarde van `Medium` of lager heeft. Aangezien de standaard waarde voor `$ConfirmPreference` is `High` , worden deze prompts niet gegeven wanneer de standaard Power shell-instellingen worden gebruikt.

U kunt de huidige instelling van `$ConfirmPreference` overschrijven met behulp van de parameter `-Confirm`. Als u `-Confirm` of `-Confirm:$True` opgeeft, vraagt de cmdlet u om bevestiging voordat deze wordt uitgevoerd. Als u `-Confirm:$False` opgeeft, wordt u niet om bevestiging gevraagd. 

Zie [over voorkeursvariabelen](/powershell/module/microsoft.powershell.core/about/about_preference_variables) voor meer informatie over `-Confirm` en `$ConfirmPreference`.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [zones en records in azure DNS](dns-zones-records.md).
<br>
Meer informatie over het [beveiligen van uw zones en records](dns-protect-zones-recordsets.md) bij het gebruik van Azure DNS.
<br>
Raadpleeg de [Azure DNS Power shell-referentie documentatie](/powershell/module/az.dns).