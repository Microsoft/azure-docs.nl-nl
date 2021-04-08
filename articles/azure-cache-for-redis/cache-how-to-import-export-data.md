---
title: Gegevens importeren en exporteren in azure cache voor redis
description: Meer informatie over het importeren en exporteren van gegevens van en naar Blob Storage met uw Premium Azure-cache voor redis-instanties
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 9ee3b447b2b5f6dfa8972749c3c46ae01f79bfdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327505"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Gegevens importeren en exporteren in azure cache voor redis
Import/export is een Azure cache voor redis-gegevens beheer bewerking, waarmee u gegevens kunt importeren in azure-cache voor redis of gegevens uit Azure-cache exporteren voor redis door het importeren en exporteren van een Azure-cache voor de redis-data base (RDB) van een Premium-cache naar een BLOB in een Azure Storage-account.

- **Exporteren** : u kunt uw Azure-cache exporteren voor redis RDB-moment opnamen naar een pagina-blob.
- **Importeren** : u kunt uw Azure-cache voor redis RDB-moment opnamen importeren uit een pagina-BLOB of een blok-blob.

Met importeren/exporteren kunt u tussen verschillende Azure-caches migreren voor redis-exemplaren of de cache vullen met gegevens voor gebruik.

In dit artikel vindt u een hand leiding voor het importeren en exporteren van gegevens met Azure cache voor redis en vindt u de antwoorden op veelgestelde vragen.

> [!IMPORTANT]
> Import/export is alleen beschikbaar voor [Premium-laag](cache-overview.md#service-tiers) caches.
>
>

## <a name="import"></a>Importeren
Importeren kan worden gebruikt om redis compatibele RDB-bestanden te halen van elke redis-server die in een wille keurige Cloud of omgeving wordt uitgevoerd, waaronder redis die wordt uitgevoerd op Linux, Windows of een Cloud provider zoals Amazon Web Services en anderen. Het importeren van gegevens is een eenvoudige manier om een cache met vooraf gevulde gegevens te maken. Tijdens het import proces laadt Azure cache voor redis de RDB-bestanden vanuit Azure Storage in het geheugen en voegt vervolgens de sleutels in de cache in.

> [!NOTE]
> Voordat u met de import bewerking begint, moet u ervoor zorgen dat uw redis-data base (RDB)-bestand of-bestanden worden geüpload naar pagina-of blok-blobs in azure Storage, in dezelfde regio en hetzelfde abonnement als uw Azure-cache voor redis-instantie. Zie [aan de slag met Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md)voor meer informatie. Als u uw RDB-bestand hebt geëxporteerd met de [Azure-cache voor de export functie redis](#export) , wordt uw RDB-bestand al opgeslagen in een pagina-Blob en kan het worden geïmporteerd.
>
>

1. Als u een of meer geëxporteerde cache-blobs wilt importeren, [bladert u naar uw cache](cache-configure.md#configure-azure-cache-for-redis-settings) in de Azure Portal en klikt u op **gegevens importeren** in het **menu resource**.

    ![Gegevens importeren](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Klik op **BLOB (s) kiezen** en selecteer het opslag account dat de gegevens bevat die u wilt importeren.

    ![Opslag account kiezen](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Klik op de container die de gegevens bevat die u wilt importeren.

    ![Container kiezen](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Selecteer een of meer blobs die u wilt importeren door te klikken op het gebied links van de naam van de BLOB en vervolgens op **selecteren**.

    ![Blobs kiezen](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Klik op **importeren** om het import proces te starten.

   > [!IMPORTANT]
   > De cache is niet toegankelijk voor cache-clients tijdens het import proces en eventuele bestaande gegevens in de cache worden verwijderd.
   >
   >

    ![Importeren](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    U kunt de voortgang van de import bewerking controleren door de meldingen van de Azure Portal te volgen of door de gebeurtenissen in het [controle logboek](../azure-resource-manager/management/view-activity-logs.md)te bekijken.

    ![Voortgang van importeren](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exporteren
Met exporteren kunt u de gegevens die zijn opgeslagen in azure cache exporteren voor redis naar redis-compatibele RDB-bestanden. U kunt deze functie gebruiken om gegevens van één Azure-cache te verplaatsen voor een redis-exemplaar naar een ander of een andere redis-server. Tijdens het export proces wordt er een tijdelijk bestand gemaakt op de virtuele machine die als host fungeert voor de Azure-cache voor redis server-exemplaar, en wordt het bestand geüpload naar het aangewezen opslag account. Wanneer de export bewerking is voltooid met de status geslaagd of mislukt, wordt het tijdelijke bestand verwijderd.

1. Als u de huidige inhoud van de cache naar de opslag ruimte wilt exporteren, [bladert u naar uw cache](cache-configure.md#configure-azure-cache-for-redis-settings) in de Azure Portal en klikt u op **gegevens exporteren** in het **menu resource**.

    ![In het navigatie deel venster voor contoso5premium is de optie gegevens exporteren in de beheer lijst gemarkeerd.](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Klik op **opslag container kiezen** en selecteer het gewenste opslag account. Het opslag account moet zich in hetzelfde abonnement en dezelfde regio bevinden als uw cache.

   > [!IMPORTANT]
   > Exporteren werkt met pagina-blobs, die worden ondersteund door de klassieke en Resource Manager-opslag accounts, maar die op dit moment niet worden ondersteund door Blob Storage-accounts. Zie [Overzicht van Azure-opslagaccount](../storage/common/storage-account-overview.md) voor meer informatie.
   >

    ![Storage-account](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Kies de gewenste BLOB-container en klik op **selecteren**. Als u een nieuwe container wilt gebruiken, klikt u op **container toevoegen** om deze eerst toe te voegen en selecteert u deze in de lijst.

    ![Op containers voor contoso55 is de optie + container gemarkeerd. Er bevindt zich één container in de lijst cachesaves en deze is geselecteerd en gemarkeerd. De optie selectie is geselecteerd en gemarkeerd.](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Typ een **voor voegsel voor de BLOB-naam** en klik op **exporteren** om het export proces te starten. Het voor voegsel van de blobnaam wordt gebruikt voor het voor voegsel van de namen van bestanden die door deze export bewerking worden gegenereerd.

    ![Exporteren](./media/cache-how-to-import-export-data/cache-export-data.png)

    U kunt de voortgang van de export bewerking controleren door de meldingen van de Azure Portal te volgen of door de gebeurtenissen in het [controle logboek](../azure-resource-manager/management/view-activity-logs.md)te bekijken.

    ![Exporteren van gegevens is voltooid](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Caches blijven beschikbaar voor gebruik tijdens het export proces.

## <a name="importexport-faq"></a>Veelgestelde vragen over importeren/exporteren
Deze sectie bevat veelgestelde vragen over de functie voor importeren/exporteren.

* [Welke prijs categorieën kunnen worden gebruikt voor importeren/exporteren?](#what-pricing-tiers-can-use-importexport)
* [Kan ik gegevens importeren uit een wille keurige redis-server?](#can-i-import-data-from-any-redis-server)
* [Welke RDB-versies kan ik importeren?](#what-rdb-versions-can-i-import)
* [Is mijn cache beschikbaar tijdens een import/export-bewerking?](#is-my-cache-available-during-an-importexport-operation)
* [Kan ik import/export met redis-cluster gebruiken?](#can-i-use-importexport-with-redis-cluster)
* [Hoe werkt importeren/exporteren met een aangepaste data base-instelling?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Hoe verschilt importeren/exporteren van redis persistentie?](#how-is-importexport-different-from-redis-persistence)
* [Kan ik import/export automatiseren met Power shell, CLI of andere management-clients?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Ik heb een time-outfout ontvangen tijdens de import/export-bewerking. Wat betekent dit?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Er is een fout opgetreden bij het exporteren van mijn gegevens naar Azure Blob Storage. Wat is er gebeurd?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Welke prijs categorieën kunnen worden gebruikt voor importeren/exporteren?
Import/export is alleen beschikbaar in de prijs categorie Premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Kan ik gegevens importeren uit een wille keurige redis-server?
Ja, naast het importeren van gegevens die zijn geëxporteerd uit Azure cache voor redis-instanties, kunt u RDB-bestanden importeren van elke redis-server die in een wille keurige Cloud of omgeving wordt uitgevoerd, zoals Linux, Windows of cloud providers zoals Amazon Web Services. Om dit te doen, uploadt u het RDB-bestand van de gewenste redis-server naar een pagina of een blok-Blob in een Azure Storage-account en importeert u het vervolgens in uw Premium Azure-cache voor redis-exemplaar. U kunt bijvoorbeeld de gegevens uit uw productie cache exporteren en deze importeren in een cache die wordt gebruikt als onderdeel van een faserings omgeving voor testen of migratie.

> [!IMPORTANT]
> Voor het importeren van gegevens die zijn geëxporteerd uit andere redis-servers dan Azure cache voor redis bij gebruik van een pagina-blob, moet de grootte van de pagina-BLOB worden uitgelijnd op een grens van 512 bytes. Zie voor beeld van de code voor het uitvoeren van een vereiste byte-celopvulling de voor beeld-upload van de [pagina-BLOB](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Welke RDB-versies kan ik importeren?

Azure cache voor redis ondersteunt RDB-import via RDB versie 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Is mijn cache beschikbaar tijdens een import/export-bewerking?
* **Export** -caches blijven beschikbaar en u kunt uw cache blijven gebruiken tijdens een export bewerking.
* **Import** -caches zijn niet meer beschikbaar wanneer een import bewerking wordt gestart, en is beschikbaar voor gebruik wanneer de import bewerking is voltooid.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Kan ik import/export met redis-cluster gebruiken?
Ja, en u kunt importeren/exporteren tussen een geclusterde cache en een niet-geclusterde cache. Omdat redis-cluster [alleen data base 0 ondersteunt](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), worden alle gegevens in andere data bases dan 0 niet geïmporteerd. Wanneer geclusterde cache gegevens worden geïmporteerd, worden de sleutels opnieuw verdeeld over de Shards van het cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hoe werkt importeren/exporteren met een aangepaste data base-instelling?
Sommige prijs categorieën hebben verschillende [database limieten](cache-configure.md#databases), dus er zijn enkele overwegingen bij het importeren als u een aangepaste waarde voor de instelling hebt geconfigureerd tijdens het maken van de `databases` cache.

* Bij het importeren in een prijs categorie met een lagere `databases` limiet dan de laag die u hebt geëxporteerd:
  * Als u het standaard aantal van `databases` 16 voor alle prijs categorieën gebruikt, gaan er geen gegevens verloren.
  * Als u een aangepast aantal gebruikt `databases` dat binnen de limieten voor de laag valt die u importeert, gaan er geen gegevens verloren.
  * Als de geëxporteerde gegevens gegevens bevatten in een Data Base die de limieten van de nieuwe laag overschrijdt, worden de gegevens van deze hogere data bases niet geïmporteerd.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Hoe verschilt importeren/exporteren van redis persistentie?
Met Azure cache voor redis-persistentie kunt u gegevens die zijn opgeslagen in redis, persistent maken Azure Storage. Wanneer persistentie is geconfigureerd, wordt in azure cache voor redis een moment opname van de Azure-cache voor redis in een redis binaire indeling op schijf opgeslagen op basis van een Configureer bare back-upfrequentie. Als er een onherstelbare gebeurtenis optreedt waarbij zowel de primaire als de replica cache wordt uitgeschakeld, worden de cache gegevens automatisch teruggezet met de meest recente moment opname. Zie [gegevens persistentie configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-persistence.md)voor meer informatie.

Met importeren/exporteren kunt u gegevens in de Azure-cache plaatsen of exporteren voor redis. Er wordt geen back-up-en herstel configuratie met behulp van redis-persistentie geconfigureerd.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Kan ik import/export automatiseren met Power shell, CLI of andere management-clients?
Ja, voor Power shell-instructies Zie [een Azure-cache importeren voor redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) en [een Azure-cache exporteren voor redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Ik heb een time-outfout ontvangen tijdens de import/export-bewerking. Wat betekent dit?
Als u de Blade **gegevens importeren** of **gegevens exporteren** langer dan 15 minuten overblijft voordat de bewerking wordt gestart, wordt een fout bericht met een fout melding weer gegeven die vergelijkbaar is met het volgende voor beeld:

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

U kunt dit oplossen door de import-of export bewerking te initiëren voordat 15 minuten is verstreken.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Er is een fout opgetreden bij het exporteren van mijn gegevens naar Azure Blob Storage. Wat is er gebeurd?
Exporteren werkt alleen met RDB-bestanden die zijn opgeslagen als pagina-blobs. Andere blob-typen worden momenteel niet ondersteund, met inbegrip van Blob Storage-accounts met warme en cool-lagen. Zie [Overzicht van Azure-opslagaccount](../storage/common/storage-account-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis-service lagen](cache-overview.md#service-tiers)