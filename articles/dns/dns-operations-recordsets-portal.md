---
title: DNS-record sets en-records beheren met Azure DNS
description: Azure DNS biedt de mogelijkheid om DNS-record sets en-records te beheren bij het hosten van uw domein.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 22ceba69ecf865d906021068a39a9d273b842ca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965728"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>DNS-records en-record sets beheren met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u record sets en records voor uw DNS-zone beheert met behulp van de Azure Portal.

Het is belang rijk dat u begrijpt wat het verschil is tussen DNS-record sets en afzonderlijke DNS-records. Een recordset is een verzameling records in een zone die dezelfde naam hebben en van hetzelfde type zijn. Zie voor meer informatie [DNS-record sets en-records maken met behulp van de Azure Portal](./dns-getstarted-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Een nieuwe recordset en record maken

Zie [DNS-records maken met behulp van de Azure Portal](./dns-getstarted-portal.md)voor het maken van een recordset in de Azure Portal.

## <a name="view-a-record-set"></a>Een recordset weer geven

1. Ga in het Azure Portal naar de Blade **DNS-zone** .
2. Zoek de recordset en selecteer deze. Hiermee opent u de eigenschappen van de recordset.

    ![Zoeken naar een recordset](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Een nieuwe record toevoegen aan een recordset

U kunt Maxi maal twintig records toevoegen aan een recordset. Een recordset kan niet twee identieke records bevatten. Lege record sets (met nul records) kunnen worden gemaakt, maar worden niet weer gegeven op de Azure DNS naam servers. Record sets van het type CNAME kunnen Maxi maal één record bevatten.

1. Klik op de Blade **Eigenschappen van recordset** voor uw DNS-zone op de record sets waaraan u een record wilt toevoegen.

    ![Een recordset selecteren](./media/dns-operations-recordsets-portal/selectset500.png)

2. Geef de eigenschappen van de recordset op door de velden in te vullen.

    ![Een record toevoegen](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Klik boven aan de Blade op **Opslaan** om uw instellingen op te slaan. Sluit de Blade.
4. In de hoek ziet u dat de record wordt opgeslagen.

    ![Recordset opslaan](./media/dns-operations-recordsets-portal/saving150.png)

Nadat de record is opgeslagen, worden de waarden op de Blade van de **DNS-zone** weer gegeven met de nieuwe record.

## <a name="update-a-record"></a>Een record bijwerken

Wanneer u een record in een bestaande recordset bijwerkt, zijn de velden die u kunt bijwerken, afhankelijk van het type record waarmee u werkt.

1. Zoek op de Blade **Eigenschappen van recordset** voor de recordset de record op.
2. Wijzig de record. Wanneer u een record wijzigt, kunt u de beschik bare instellingen voor de record wijzigen. In het volgende voor beeld is het veld **IP-adres** geselecteerd en wordt het IP-adres in het proces gewijzigd.

    ![Een record wijzigen](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Klik boven aan de Blade op **Opslaan** om uw instellingen op te slaan. In de rechter bovenhoek ziet u de melding dat de record is opgeslagen.

    ![Opgeslagen Recordset](./media/dns-operations-recordsets-portal/saved150.png)

Nadat de record is opgeslagen, worden de bijgewerkte records weer gegeven voor de record die is ingesteld op de Blade van de **DNS-zone** .

## <a name="remove-a-record-from-a-record-set"></a>Een record uit een recordset verwijderen

U kunt de Azure Portal gebruiken om records uit een recordset te verwijderen. Houd er rekening mee dat het verwijderen van de laatste record uit een recordset de recordset niet verwijdert.

1. Zoek op de Blade **Eigenschappen van recordset** voor de recordset de record op.
2. Klik op de record die u wilt verwijderen. Selecteer vervolgens **verwijderen**.

    ![Een record verwijderen](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Klik boven aan de Blade op **Opslaan** om uw instellingen op te slaan.
4. Nadat de record is verwijderd, worden de waarden voor de record op de Blade van de **DNS-zone** weer gegeven.

## <a name="delete-a-record-set"></a><a name="delete"></a>Een recordset verwijderen

1. Klik op de Blade **Eigenschappen van recordset** voor de recordset op **verwijderen**.

    ![Een recordset verwijderen](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. Er wordt een bericht weer gegeven waarin u wordt gevraagd of u de recordset wilt verwijderen.
3. Controleer of de naam overeenkomt met de recordset die u wilt verwijderen en klik vervolgens op **Ja**.
4. Controleer op de Blade **DNS-zone** of de recordset niet meer zichtbaar is.

## <a name="work-with-ns-and-soa-records"></a>Werken met NS-en SOA-records

NS-en SOA-records die automatisch worden gemaakt, worden anders beheerd dan andere record typen.

### <a name="modify-soa-records"></a>SOA-records wijzigen

U kunt geen records toevoegen aan of verwijderen uit de automatisch gemaakte SOA-record die is ingesteld op de zone Apex (naam = " \@ "). U kunt echter elk van de para meters in de SOA-record (met uitzonde ring van ' host ') en de set-TTL van de record wijzigen.

### <a name="modify-ns-records-at-the-zone-apex"></a>NS-records in de zone wijzigen Apex

De NS-record die is ingesteld op de zone Apex wordt automatisch gemaakt met elke DNS-zone. Het bevat de namen van de Azure DNS naam servers die zijn toegewezen aan de zone.

U kunt aanvullende naam servers toevoegen aan deze NS-Recordset, ter ondersteuning van co-hosting domeinen met meer dan één DNS-provider. U kunt ook de TTL en meta gegevens voor deze recordset wijzigen. U kunt de vooraf ingevulde Azure DNS naam servers echter niet verwijderen of wijzigen.

Houd er rekening mee dat dit alleen geldt voor de NS-recordset die is ingesteld op de zone Apex. Andere NS-record sets in uw zone (zoals gebruikt voor het delegeren van onderliggende zones) kunnen zonder beperking worden gewijzigd.

### <a name="delete-soa-or-ns-record-sets"></a>SOA-of NS-record sets verwijderen

U kunt de SOA-en NS-record sets niet verwijderen op de zone Apex (name = " \@ ") die automatisch worden gemaakt wanneer de zone wordt gemaakt. Ze worden automatisch verwijderd wanneer u de zone verwijdert.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure DNS-overzicht](dns-overview.md)voor meer informatie over Azure DNS.
* Zie [DNS-zones en-record sets maken met behulp van de .NET SDK](dns-sdk.md)voor meer informatie over het AUTOMATISEREN van DNS.
* Zie [overzicht van omgekeerde DNS en ondersteuning in azure](dns-reverse-dns-overview.md)voor meer informatie over reverse DNS-records.
* Zie [Azure DNS alias records Overview](dns-alias.md)(Engelstalig) voor meer informatie over Azure DNS alias records.