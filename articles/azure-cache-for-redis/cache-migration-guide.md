---
title: Migreren naar Azure Cache voor Redis
description: Meer informatie over het migreren van uw bestaande cache naar Azure cache voor redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: d63cafb32dc1db0a901ed3e6004446b450db10c7
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451520"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Migreren naar Azure Cache voor Redis
In dit artikel wordt een aantal benaderingen beschreven voor het migreren van een bestaande redis-cache die on-premises wordt uitgevoerd of in een andere Cloud service naar Azure cache voor redis.

## <a name="migration-scenarios"></a>Migratiescenario's
Open-source redis kan in veel reken omgevingen worden uitgevoerd. Veelvoorkomende voor beelden zijn:

- **On-premises** redis-caches die worden uitgevoerd in particuliere data centers.
- **Cloud-gebaseerde vm's** : redis caches die worden uitgevoerd op virtuele machines van Azure, AWS EC2, enzovoort.
- **Hosting Services** : beheerde redis-Services, zoals AWS ElastiCache.
- **Verschillende regio's** -redis-caches in een andere Azure-regio.

Als u een dergelijke cache hebt, kunt u deze mogelijk verplaatsen naar Azure cache voor redis met een minimale onderbreking of downtime.

## <a name="migration-options"></a>Migratieopties

Er zijn verschillende manieren waarop u kunt overschakelen van de ene cache naar de andere. Afhankelijk van de locatie van uw cache en de manier waarop uw toepassing ermee samenwerkt, is één methode nuttiger dan de andere. Enkele van de veelgebruikte migratie strategieën worden hieronder beschreven.

   | Optie       | Voordelen | Nadelen |
   | ------------ | ---------- | ------------- |
   | Een nieuwe cache maken | Het eenvoudigste is om te implementeren. | Gegevens moeten opnieuw worden ingevuld in de nieuwe cache, wat mogelijk niet werkt bij veel toepassingen. |
   | Gegevens exporteren en importeren via een RDB-bestand | In het algemeen compatibel met een wille keurige redis-cache. | Sommige gegevens kunnen verloren gaan als ze naar de bestaande cache worden geschreven nadat het RDB-bestand is gegenereerd. | 
   | Dubbele gegevens naar twee caches schrijven | Geen gegevens verlies of downtime. Ongestoorde bewerkingen van de bestaande cache. Eenvoudiger testen van de nieuwe cache. | Heeft gedurende lange tijd twee caches nodig. | 
   | Gegevens migreren via een programma | Volledige controle over hoe gegevens worden verplaatst. | Aangepaste code vereist. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Een nieuwe Azure-cache maken voor redis

Deze benadering is technisch gezien geen migratie. Als gegevens verlies niet van belang is, is de eenvoudigste manier om naar Azure cache te gaan voor redis, het maken van een cache-exemplaar en het verbinden van uw toepassing. Als u bijvoorbeeld redis als een opzoek cache van database records gebruikt, kunt u de cache eenvoudig opnieuw samen stellen.

Algemene stappen voor het implementeren van deze optie zijn:

1. Maak een nieuwe Azure-cache voor redis-instantie.

2. Werk uw toepassing bij om het nieuwe exemplaar te gebruiken.

3. Verwijder het oude redis-exemplaar.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Gegevens exporteren naar een RDB-bestand en dit importeren in azure cache voor redis

Open-source redis definieert een standaard mechanisme voor het maken van een moment opname van de gegevensset in het geheugen van de cache en het opslaan naar een bestand. Dit bestand, met de naam RDB, kan worden gelezen door een andere redis-cache. [Azure cache voor de Premium-laag van redis](cache-overview.md#service-tiers) biedt ondersteuning voor het importeren van gegevens in een cache-exemplaar via RDB-bestanden. U kunt een RDB-bestand gebruiken om gegevens van een bestaande cache over te brengen naar Azure cache voor redis.

> [!IMPORTANT]
> De indeling van het RDB-bestand kan worden gewijzigd tussen redis-versies en behoudt mogelijk niet achterwaartse compatibiliteit. De redis-versie van de cache die u wilt exporteren, moet gelijk zijn aan of kleiner zijn dan de versie van Azure cache voor redis.
>

Algemene stappen voor het implementeren van deze optie zijn:

1. Maak een nieuwe Azure-cache voor redis-instantie in de Premium-laag die even groot is als (of groter is dan) de bestaande cache.

2. Een moment opname van de bestaande redis-cache opslaan. U kunt [redis configureren om moment opnamen periodiek op te slaan](https://redis.io/topics/persistence) of het proces hand matig uitvoeren met de opdrachten [Opslaan](https://redis.io/commands/save) of [BGSAVE](https://redis.io/commands/bgsave) . Het RDB-bestand heet standaard ' dump. rdb ' en bevindt zich in het pad dat is opgegeven in het configuratie bestand *redis. conf* .

    > [!NOTE]
    > Als u gegevens in azure cache migreert voor redis, raadpleegt u [deze instructies voor het exporteren van een RDB-bestand](cache-how-to-import-export-data.md) of het gebruik van de [Power shell-cmdlet Export](/powershell/module/azurerm.rediscache/export-azurermrediscache) .
    >

3. Kopieer het RDB-bestand naar een Azure-opslag account in de regio waar uw nieuwe cache zich bevindt. U kunt AzCopy gebruiken voor deze taak.

4. Importeer het RDB-bestand in de nieuwe cache met behulp van deze [import instructies](cache-how-to-import-export-data.md) of de [Power shell-cmdlet Import](/powershell/module/azurerm.rediscache/import-azurermrediscache).

5. Werk uw toepassing bij om het nieuwe cache-exemplaar te gebruiken.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Tijdens de migratie periode naar twee redis-caches tegelijk schrijven

In plaats van gegevens rechtstreeks tussen caches te verplaatsen, kunt u uw toepassing gebruiken om gegevens te schrijven naar een bestaande cache en een nieuwe die u instelt. In de toepassing worden nog steeds gegevens uit de bestaande cache gelezen. Wanneer de nieuwe cache de benodigde gegevens bevat, zet u de toepassing om in de cache en buiten gebruik stellen van de oude. Stel dat u bijvoorbeeld redis als een sessie archief gebruikt en dat de toepassings sessies zeven dagen geldig zijn. Nadat u naar de twee caches voor een week hebt geschreven, is het zeker dat de nieuwe cache alle niet-verlopen sessie-informatie bevat. U kunt op een later tijdstip veilig gebruikmaken van dit moment zonder dat er gegevens verloren gaan.

Algemene stappen voor het implementeren van deze optie zijn:

1. Maak een nieuwe Azure-cache voor redis-instantie in de Premium-laag die even groot is als (of groter is dan) de bestaande cache.

2. Wijzig de toepassings code om naar zowel de nieuwe als de oorspronkelijke instanties te schrijven.

3. Ga door met het lezen van gegevens van het oorspronkelijke exemplaar totdat het nieuwe exemplaar voldoende is gevuld met gegevens.

4. Werk de toepassings code bij om alleen van het nieuwe exemplaar te lezen en te schrijven.

5. Verwijder het oorspronkelijke exemplaar.

### <a name="migrate-programmatically"></a>Programmatisch migreren

U kunt een aangepast migratie proces maken door programmatisch gegevens te lezen uit een bestaande cache en deze te schrijven naar Azure cache voor redis. Dit [open source-hulp programma](https://github.com/deepakverma/redis-copy) kan worden gebruikt om gegevens te kopiëren van een Azure-cache voor een redis-exemplaar naar een andere. Dit hulp programma is nuttig voor het verplaatsen van gegevens tussen cache-instanties in verschillende Azure-cache regio's. Er is ook een [gecompileerde versie](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) beschikbaar. Mogelijk vindt u de bron code ook een nuttige hand leiding voor het schrijven van uw eigen migratie programma.

> [!NOTE]
> Dit hulp programma wordt niet officieel ondersteund door micro soft. 
>

Algemene stappen voor het implementeren van deze optie zijn:

1. Maak een virtuele machine in de regio waarin de bestaande cache zich bevindt. Als uw gegevensset groot is, kiest u een relatief krachtige VM om de Kopieer tijd te verminderen.

2. Maak een nieuwe Azure-cache voor redis-instantie.

3. Gegevens uit de nieuwe cache leegmaken om ervoor te zorgen dat deze leeg zijn. Deze stap is vereist omdat het kopieer programma zelf geen bestaande sleutel in de doel cache overschrijft.

    > [!IMPORTANT]
    > Zorg ervoor dat u niets leegmaakt uit de bron cache.
    >

4. Gebruik een toepassing zoals het open source-hulp programma om het kopiëren van gegevens van de bron cache naar het doel te automatiseren. Houd er rekening mee dat het kopieer proces enige tijd kan duren, afhankelijk van de grootte van uw gegevensset.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis-service lagen](cache-overview.md#service-tiers)
* [Gegevens importeren](cache-how-to-import-export-data.md#import)