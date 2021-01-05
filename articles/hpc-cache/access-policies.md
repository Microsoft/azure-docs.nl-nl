---
title: Toegangs beleid gebruiken in de Azure HPC-cache
description: Aangepaste toegangs beleid maken en Toep assen om de toegang van clients tot opslag doelen in een Azure HPC-cache te beperken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/28/2020
ms.author: v-erkel
ms.openlocfilehash: 795b194eb7cd31e633128c22ddffe808b32e07da
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802406"
---
# <a name="use-client-access-policies"></a>Beleid voor client toegang gebruiken

In dit artikel wordt uitgelegd hoe u aangepaste beleids regels voor client toegang maakt en toepast voor uw opslag doelen.

Beleids regels voor client toegang bepalen hoe clients verbinding kunnen maken met de opslag doel-exports. U kunt items zoals hoofdmap-squash en lezen/schrijven-toegang op de client-host of het netwerk niveau beheren.

Toegangs beleid wordt toegepast op een pad naar een naam ruimte, wat betekent dat u verschillende toegangs beleid voor twee verschillende export bewerkingen kunt gebruiken op een NFS-opslag systeem.

Deze functie is voor werk stromen waarvoor u wilt bepalen hoe verschillende groepen clients toegang hebben tot de opslag doelen.

Als u geen nauw keurige controle over de toegang tot opslag doelen nodig hebt, kunt u het standaard beleid gebruiken of u kunt het standaard beleid aanpassen met extra regels.

## <a name="create-a-client-access-policy"></a>Een beleid voor client toegang maken

Gebruik de pagina **client toegangs beleid** in de Azure Portal om beleid te maken en te beheren. <!-- is there AZ CLI for this? -->

[![scherm afbeelding van de pagina met client toegangs beleid. Er zijn verschillende beleids regels gedefinieerd, en sommige zijn uitgevouwen om hun regels weer te geven](media/policies-overview.png)](media/policies-overview.png#lightbox)

Elk beleid bestaat uit regels. De regels worden op hosts toegepast in de volg orde van de kleinste scope (host) tot de grootste (standaard). De eerste regel die overeenkomt wordt toegepast en latere regels worden genegeerd.

Als u een nieuw toegangs beleid wilt maken, klikt u op de knop **+ toegangs beleid toevoegen** boven aan de lijst. Geef het nieuwe toegangs beleid een naam en voer ten minste één regel in.

![scherm afbeelding van toegangs beleid Blade bewerken met meerdere regels ingevuld. Klik op OK om de regel op te slaan.](media/add-policy.png)

In de rest van deze sectie worden de waarden uitgelegd die u in regels kunt gebruiken.

### <a name="scope"></a>Bereik

De scope periode en het adres filter werken samen om te definiëren welke clients door de regel worden beïnvloed.

Gebruik deze om op te geven of de regel van toepassing is op een afzonderlijke client (host), een bereik van IP-adressen (netwerk) of alle clients (standaard).

Selecteer de juiste **bereik** waarde voor de regel:

* **Host** : de regel is van toepassing op een afzonderlijke client
* **Netwerk** : de regel is van toepassing op clients in een bereik van IP-adressen
* **Standaard** : de regel is van toepassing op alle clients.

Regels in een beleid worden in die volg orde geëvalueerd. Nadat een aanvraag voor een client koppeling overeenkomt met één regel, worden de andere regels genegeerd.

### <a name="address-filter"></a>Adres filter

De **adres filter** waarde geeft aan welke clients overeenkomen met de regel.

Als u het bereik instelt op **host**, kunt u slechts één IP-adres opgeven in het filter. Voor de scope-instelling **standaard** kunt u geen IP-adressen invoeren in het veld **adres filter** , omdat het standaard bereik overeenkomt met alle clients.

Geef het IP-adres of het bereik van adressen op voor deze regel. Gebruik CIDR-notatie (bijvoorbeeld: 0.1.0.0/16) om een adres bereik op te geven.

### <a name="access-level"></a>Toegangsniveau

Stel in welke bevoegdheden de clients moeten verlenen die overeenkomen met het bereik en het filter.

Opties zijn **lezen/schrijven**, **alleen-lezen** of **geen toegang**.

### <a name="suid"></a>SUID

Schakel het selectie vakje **suid** in om bestanden in de opslag toe te staan om gebruikers-id's in te stellen bij toegang.

SUID wordt doorgaans gebruikt om de bevoegdheden van een gebruiker tijdelijk te verhogen, zodat de gebruiker een taak kan uitvoeren die gerelateerd is aan dat bestand.

### <a name="submount-access"></a>Toegang voor subkoppeling

Schakel dit selectie vakje in om de opgegeven clients toe te staan om de submappen van deze export rechtstreeks te koppelen.

### <a name="root-squash"></a>Hoofdmap Squash

Kies of u de hoofdmap Squash wilt instellen voor clients die overeenkomen met deze regel.

Met deze waarde kunt u de basis-squash op het opslag niveau van de uitvoer toestaan. U kunt [de root-squash ook instellen op het niveau van de cache](configuration.md#configure-root-squash).

Als u root Squash inschakelt, moet u ook de waarde voor de anonieme ID-gebruiker instellen op een van de volgende opties:

* **-2** (niemand)
* **65534** (niemand)
* **-1** (geen toegang)
* **65535** (geen toegang)
* **0** (niet-gemachtigd hoofd niveau)

## <a name="update-access-policies"></a>Toegangs beleid bijwerken

U kunt toegangs beleid bewerken of verwijderen uit de tabel op de pagina **client access policies** .

Klik op de naam van het beleid om het te openen en te bewerken.

Als u een beleid wilt verwijderen, schakelt u het selectie vakje naast de naam in de lijst in en klikt u op de knop **verwijderen** boven aan de lijst. U kunt het beleid met de naam ' default ' niet verwijderen.

> [!NOTE]
> U kunt geen toegangs beleid verwijderen dat in gebruik is. Verwijder het beleid uit alle naam ruimte paden die het bevat voordat u het verwijdert.

## <a name="next-steps"></a>Volgende stappen

* Pas toegangs beleid toe in de naam ruimte paden voor uw opslag doelen. Lees [de geaggregeerde naam ruimte instellen](add-namespace-paths.md) voor meer informatie.
