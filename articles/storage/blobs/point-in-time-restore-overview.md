---
title: Herstel naar een bepaald tijdstip voor blok-blobs
titleSuffix: Azure Storage
description: Herstel naar een bepaald tijdstip voor blok-blobs biedt beveiliging tegen onbedoeld verwijderen of beschadiging door u in te scha kelen om een opslag account terug te zetten naar de vorige status op dat moment.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 518df665db0ba3770bee757f45d02b6ccd303a00
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803864"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Herstel naar een bepaald tijdstip voor blok-blobs

Herstel naar een bepaald tijdstip biedt beveiliging tegen onbedoeld verwijderen of beschadiging door u in staat te stellen de blok-BLOB-gegevens terug te zetten naar een eerdere status. Herstel naar een bepaald tijdstip is handig in scenario's waarbij een gebruiker of toepassing per ongeluk gegevens verwijdert of een toepassings fout beschadigde gegevens verstuurt. Herstel naar een bepaald tijdstip biedt ook ondersteuning voor test scenario's waarbij een gegevensset naar een bekende status moet worden teruggedraaid voordat verdere tests worden uitgevoerd.

Herstel naar een bepaald tijdstip wordt alleen ondersteund voor v2-opslag accounts voor algemeen gebruik. Alleen gegevens in de warme en coolbar-toegangs lagen kunnen worden hersteld met herstel naar een bepaald tijdstip.

Zie [een herstel bewerking op een bepaald tijdstip uitvoeren op blok-BLOB-gegevens](point-in-time-restore-manage.md)voor meer informatie over het inschakelen van een herstel punt voor een opslag account.

## <a name="how-point-in-time-restore-works"></a>Hoe herstel naar een bepaald tijdstip werkt

Als u herstel naar een bepaald tijdstip wilt inschakelen, maakt u een beheer beleid voor het opslag account en geeft u een Bewaar periode op. Tijdens de retentie periode kunt u blok-blobs van de huidige status herstellen naar een status op een eerder tijdstip.

Als u een herstel tijdstip voor een bepaald tijdstip wilt initiëren, roept u de bewerking [BLOB Ranges herstellen](/rest/api/storagerp/storageaccounts/restoreblobranges) aan en geeft u een herstel punt op in UTC-tijd. U kunt lexicographical-bereiken van container-en BLOB-namen opgeven om te herstellen, of het bereik weglaten om alle containers in het opslag account te herstellen. Er worden Maxi maal 10 lexicographical-bereiken ondersteund per herstel bewerking.

Azure Storage analyseert alle wijzigingen die zijn aangebracht in de opgegeven blobs tussen het aangevraagde herstel punt, opgegeven in UTC-tijd en het huidige moment. De herstel bewerking is atomisch, waardoor alle wijzigingen volledig worden hersteld of niet kan worden uitgevoerd. Als er blobs zijn die niet kunnen worden hersteld, mislukt de bewerking en worden lees-en schrijf bewerkingen naar de betrokken containers hervat.

Er kan slechts één herstel bewerking tegelijk worden uitgevoerd op een opslag account. Een herstel bewerking kan niet worden geannuleerd zodra deze wordt uitgevoerd, maar er kan wel een tweede herstel bewerking worden uitgevoerd om de eerste bewerking ongedaan te maken.

De bewerking **BLOB-bereiken herstellen** retourneert een Restore-id waarmee de bewerking op unieke wijze wordt geïdentificeerd. Als u de status van een herstel tijdstip wilt controleren, roept u de bewerking **herstel status ophalen** aan met de herstel-id die wordt geretourneerd door de bewerking **BLOB-bereiken herstellen** .

> [!IMPORTANT]
> Wanneer u een herstel bewerking uitvoert, blokkeert Azure Storage gegevens bewerkingen op de blobs in de bereiken die worden teruggezet voor de duur van de bewerking. Lees-, schrijf-en verwijder bewerkingen worden geblokkeerd op de primaire locatie. Daarom kunnen bewerkingen, zoals het weer geven van containers in het Azure Portal, niet worden uitgevoerd zoals verwacht tijdens het terugzetten.
>
> Lees bewerkingen van de secundaire locatie kunnen door gaan tijdens de herstel bewerking als het opslag account geo-gerepliceerd is.

> [!CAUTION]
> Herstel naar een bepaald tijdstip biedt alleen ondersteuning voor het herstellen van bewerkingen op blok-blobs. Bewerkingen op containers kunnen niet worden hersteld. Als u een container uit het opslag account verwijdert door de bewerking voor het verwijderen van een [container](/rest/api/storageservices/delete-container) aan te roepen, kan deze container niet worden hersteld met een herstel bewerking. In plaats van een volledige container te verwijderen, moet u afzonderlijke blobs verwijderen als u deze mogelijk later wilt herstellen.

### <a name="prerequisites-for-point-in-time-restore"></a>Vereisten voor herstel naar een bepaald tijdstip

Herstel naar een bepaald tijdstip vereist dat de volgende Azure Storage-functies worden ingeschakeld voordat u herstel naar een bepaald tijdstip kunt inschakelen:

- [Voorlopig verwijderen](./soft-delete-blob-overview.md)
- [Feed wijzigen](storage-blob-change-feed.md)
- [BLOB-versie beheer](versioning-overview.md)

### <a name="retention-period-for-point-in-time-restore"></a>Bewaar periode voor herstel naar een bepaald tijdstip

Wanneer u herstel naar een bepaald tijdstip voor een opslag account inschakelt, geeft u een Bewaar periode op. Blok-blobs in uw opslag account kunnen worden hersteld tijdens de Bewaar periode.

De Bewaar periode begint een paar minuten nadat u herstel naar een bepaald tijdstip hebt ingeschakeld. Denk eraan dat u geen blobs kunt herstellen naar een status vóór het begin van de Bewaar periode. Als u bijvoorbeeld punt-in-time herstel hebt ingeschakeld op 1 mei met een Bewaar periode van 30 dagen, kunt u op 15 mei Maxi maal vijf tien dagen herstellen. Op 1 juni kunt u gegevens terugzetten van tussen de en dertig dagen.

De Bewaar periode voor herstel naar een bepaald tijdstip moet ten minste één dag kleiner zijn dan de retentie periode die is opgegeven voor de tijdelijke verwijdering. Als de Bewaar periode voor zacht verwijderen bijvoorbeeld is ingesteld op 7 dagen, mag de Bewaar periode voor het herstel punt van de tijd tussen 1 en 6 dagen liggen.

> [!IMPORTANT]
> De tijd die nodig is voor het herstellen van een set gegevens is gebaseerd op het aantal schrijf-en verwijder bewerkingen tijdens de herstel periode. Bijvoorbeeld: een account met 1.000.000 objecten met 3.000 objecten per dag en 1.000 objecten die per dag worden verwijderd, duurt ongeveer twee uur om terug te gaan naar een punt 30 dagen in het verleden. Een Bewaar periode en herstel van meer dan 90 dagen in het verleden worden niet aanbevolen voor een account met deze wijzigings factor.

### <a name="permissions-for-point-in-time-restore"></a>Machtigingen voor herstel naar een bepaald tijdstip

Een client moet schrijf machtigingen hebben voor alle containers in het opslag account om een herstel bewerking te initiëren. Wijs de rol **Inzender voor opslag accounts** toe aan de beveiligingsprincipal op het niveau van het opslag account, de resource groep of het abonnement om machtigingen te verlenen voor het autoriseren van een herstel bewerking met Azure Active Directory (Azure AD).

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen

Herstel naar een bepaald tijdstip voor blok-blobs heeft de volgende beperkingen en bekende problemen:

- Alleen blok-blobs in een standaard v2-opslag account voor algemeen gebruik kunnen worden hersteld als onderdeel van een herstel bewerking op tijdstippen. Toevoeg-blobs, pagina-blobs en Premium-blok-blobs worden niet hersteld. 
- Als u een container tijdens de Bewaar periode hebt verwijderd, wordt deze container niet teruggezet met de herstel bewerking naar een bepaald tijdstip. Als u probeert een aantal blobs te herstellen dat blobs bevat in een verwijderde container, mislukt de herstel bewerking naar een bepaald tijdstip. Zie [voorlopig verwijderen voor containers (preview)](soft-delete-container-overview.md)voor meer informatie over het beveiligen van containers tegen verwijderen.
- Als een BLOB tussen de warme en koele lagen is verplaatst in de periode tussen het huidige moment en het herstel punt, wordt de BLOB teruggezet naar de vorige laag. Het herstellen van blok-blobs in de opslaglaag wordt niet ondersteund. Als een blob in de warme laag bijvoorbeeld twee dagen geleden is verplaatst naar de archieflaag en een herstelbewerking herstelt naar een bepaald punt drie dagen geleden, wordt de blob niet teruggezet naar de warme laag. Als u een gearchiveerde BLOB wilt herstellen, moet u deze eerst uit de laag archief verplaatsen. Zie BLOB-gegevens opnieuw [inbreken vanuit de laag archief](storage-blob-rehydration.md)voor meer informatie.
- Een blok dat is geüpload met behulp van de [put-blok kering](/rest/api/storageservices/put-block) of het [put-blok van de URL](/rest/api/storageservices/put-block-from-url), maar niet is doorgevoerd via een put- [blok lijst](/rest/api/storageservices/put-block-list), maakt geen deel uit van een BLOB en wordt dus niet hersteld als onderdeel van een herstel bewerking.
- Een blob met een actieve lease kan niet worden hersteld. Als een blob met een actieve lease is opgenomen in het bereik van blobs dat moet worden hersteld, mislukt de herstel bewerking Atomic. Verbreekt actieve leases voordat de herstel bewerking wordt gestart.
- Moment opnamen worden niet gemaakt of verwijderd als onderdeel van een herstel bewerking. Alleen de basis-BLOB wordt teruggezet naar de vorige status.
- Het herstellen van Azure Data Lake Storage Gen2 vlakke en hiërarchische naam ruimten wordt niet ondersteund.

> [!IMPORTANT]
> Als u blok-blobs herstelt naar een punt dat ouder is dan 22 september 2020, zijn de preview-beperkingen voor herstel naar een bepaald tijdstip van kracht. Micro soft raadt u aan om een herstel punt te kiezen dat gelijk is aan of hoger dan 22 september 2020 om te profiteren van de algemeen beschik bare functie voor het terugzetten van een bepaald tijdstip.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Facturering voor tijdstippen herstel is afhankelijk van de hoeveelheid gegevens die wordt verwerkt om de herstel bewerking uit te voeren. De hoeveelheid verwerkte gegevens is gebaseerd op het aantal wijzigingen dat is opgetreden tussen het herstel punt en het huidige moment. Als er bijvoorbeeld een relatief constant tempo van wijziging is voor het blok keren van BLOB-gegevens in een opslag account, zou een herstel bewerking die terugkeert in de tijd 1 dag kosten 1/tiende van een herstel datum van 10 dagen in beslag nemen.

Als u de kosten van een herstel bewerking wilt schatten, bekijkt u het logboek voor wijzigings invoer om de hoeveelheid gegevens te schatten die tijdens de herstel periode is gewijzigd. Als de retentie periode voor de wijziging 30 dagen is en de grootte van de wijzigings feed 10 MB is, zal het herstellen naar een punt dat 10 dagen eerder duurt, ongeveer een derde van de vermelde prijs voor een LRS-account in die regio kosten. Als u een punt van 27 dagen eerder herstelt, kost dit ongeveer negen tienden van de weer gegeven prijs.

Zie de [prijzen voor blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor herstel naar een bepaald tijdstip.

## <a name="next-steps"></a>Volgende stappen

- [Herstel naar een bepaald tijdstip uitvoeren op blok-BLOB-gegevens](point-in-time-restore-manage.md)
- [Ondersteuning voor feed wijzigen in Azure Blob Storage](storage-blob-change-feed.md)
- [Voorlopig verwijderen inschakelen voor blobs](./soft-delete-blob-enable.md)
- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)
