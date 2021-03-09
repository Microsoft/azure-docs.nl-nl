---
title: Verouderde Azure DNS Private Zones migreren naar een nieuw resource model
titleSuffix: Azure DNS
description: Deze hand leiding bevat stapsgewijze instructies voor het migreren van verouderde privé-DNS-zones naar het meest recente bron model
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 72d046cde70d1224eb1fd47f527c9e49c6b002f6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500458"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Verouderde Azure DNS particuliere zones migreren naar een nieuw resource model

Tijdens de open bare preview zijn privé-DNS-zones gemaakt met behulp van de ' dnszones-bron waarvan de eigenschap ' para ' is ingesteld op ' persoonlijk '. Dergelijke zones worden na 31 december 2019 niet ondersteund en moeten worden gemigreerd naar een GA Resource-model dat gebruikmaakt van het resource type ' privateDnsZones ' in plaats van ' dnszones '. Het migratie proces is eenvoudig en er is een Power shell-script gegeven om dit proces te automatiseren. Deze hand leiding bevat stapsgewijze instructies voor het migreren van uw Azure DNS persoonlijke zones naar het nieuwe resource model.

Om erachter te komen welke dnszones-bronnen moeten worden gemigreerd. Voer de onderstaande opdracht uit in azure CLI.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de nieuwste versie van Azure PowerShell hebt geïnstalleerd. Voor meer informatie over Azure PowerShell (AZ) en hoe u deze kunt installeren, gaat u naar https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Zorg ervoor dat u de module AZ. PrivateDns hebt voor de Azure PowerShell geïnstalleerd. Als u deze module wilt installeren, opent u een Power shell-venster met verhoogde bevoegdheden (beheer modus) en voert u de volgende opdracht in

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Het migratie proces is volledig geautomatiseerd en verwacht geen uitval tijd. Als u echter Azure DNS particuliere zones (preview) in een kritieke productie omgeving gebruikt, moet u het volgende migratie proces uitvoeren tijdens het geplande onderhouds tijd venster. Zorg ervoor dat u de configuratie of record sets van een privé-DNS-zone niet wijzigt tijdens het uitvoeren van het migratie script.

## <a name="installing-the-script"></a>Het script installeren

Open een Power shell-venster met verhoogde bevoegdheden (beheer modus) en voer de volgende opdracht uit

```powershell
install-script PrivateDnsMigrationScript
```

Voer ' A ' in wanneer u wordt gevraagd het script te installeren

![Het script installeren](./media/private-dns-migration-guide/install-migration-script.png)

U kunt ook hand matig de meest recente versie van Power shell-script verkrijgen op https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Het migratie script mag niet worden uitgevoerd in azure Cloud shell en moet worden uitgevoerd op een virtuele machine of lokale computer die is verbonden met internet.

## <a name="running-the-script"></a>Het script uitvoeren

Voer de volgende opdracht uit om het script uit te voeren

```powershell
PrivateDnsMigrationScript.ps1
```

![Het script uitvoeren](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Voer de abonnements-ID in en meld u aan bij Azure

U wordt gevraagd om een abonnements-ID op te geven met de privé-DNS-zones die u wilt migreren. U wordt gevraagd om u aan te melden bij uw Azure-account. Voltooi de aanmelding zodat het script toegang kan krijgen tot de privé-DNS-zone bronnen in het abonnement.

![Aanmelden bij Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Selecteer de DNS-zones die u wilt migreren

Het script met de lijst met alle privé-DNS-zones in het abonnement ophalen en u vragen om te bevestigen welke u wilt migreren. Voer ' A ' in om alle privé-DNS-zones te migreren. Wanneer u deze stap hebt uitgevoerd, worden met het script nieuwe privé-DNS-zones gemaakt met behulp van nieuw resource model en worden de gegevens gekopieerd naar de nieuwe DSN-zone. Met deze stap worden uw bestaande privé-DNS-zones toch niet gewijzigd.

![DNS-zones selecteren](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>DNS-omzetting overschakelen naar de nieuwe DNS-zones

Zodra de zones en records naar het nieuwe bron model zijn gekopieerd, wordt u door het script gevraagd de DNS-omzetting over te scha kelen naar nieuwe DNS-zones. Met deze stap wordt de koppeling tussen verouderde privé-DNS-zones en uw virtuele netwerken verwijderd. Wanneer de verouderde zone wordt ontkoppeld van de virtuele netwerken, nemen de nieuwe DNS-zones die in de bovenstaande stap worden gemaakt, automatisch de DNS-omzetting over die virtuele netwerken.

Selecteer ' A ' om de DNS-omzetting voor alle virtuele netwerken te wijzigen.

![Naam omzetting overschakelen](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>De DNS-omzetting verifiëren

Controleer voordat u doorgaat of de DNS-omzetting op uw DNS-zones werkt zoals verwacht. U kunt zich aanmelden bij uw Azure-Vm's en nslookup-query's voor de gemigreerde zones geven om te controleren of de DNS-omzetting werkt.

![Naam omzetting controleren](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Als u vindt dat DNS-query's niet worden opgelost, wacht u enkele minuten en voert u de query's opnieuw uit. Als DNS-query's werken zoals verwacht, voert u ' Y ' in wanneer u wordt gevraagd om het virtuele netwerk te verwijderen uit de privé-DNS-zone.

![Naam omzetting bevestigen](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Als de DNS-omzetting voor de gemigreerde zones niet werkt zoals verwacht, voert u ' N ' in de bovenstaande stap en het script in, wordt de DNS-omzetting terug naar verouderde zones. Maak een ondersteunings ticket en wij kunnen u helpen bij de migratie van uw DNS-zones.

## <a name="cleanup"></a>Opschonen

Met deze stap worden de verouderde DNS-zones verwijderd en moeten deze pas worden uitgevoerd nadat u hebt gecontroleerd of de DNS-omzetting naar verwachting werkt. U wordt gevraagd elke privé-DNS-zone te verwijderen. Voer bij elke prompt ' Y ' in nadat u hebt gecontroleerd of de DNS-omzetting voor die zones goed werkt.

![Opschonen](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Uw automatisering bijwerken

Als u Automation gebruikt, inclusief sjablonen, Power shell-scripts of aangepaste code die is ontwikkeld met behulp van SDK, moet u uw automatisering bijwerken om het nieuwe resource model voor de privé-DNS-zones te gebruiken. Hieronder vindt u de koppelingen naar nieuwe persoonlijke DNS CLI/PS/SDK-documentatie.
* [Azure DNS particuliere zones REST API](/rest/api/dns/privatedns/privatezones)
* [Azure DNS CLI voor persoonlijke zones](/cli/azure/ext/privatedns/network/private-dns)
* [Azure DNS persoonlijke zones Power shell](/powershell/module/az.privatedns/)
* [Azure DNS-SDK voor particuliere zones](/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Meer hulp nodig

Maak een ondersteunings ticket als u meer hulp nodig hebt bij het migratie proces of om welke reden dan ook de hierboven vermelde stappen niet voor u werken. Neem het transcript bestand op dat door het Power shell-script wordt gegenereerd met uw ondersteunings ticket.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privé zone in Azure DNS met behulp van [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure cli](./private-dns-getstarted-cli.md).

* Lees meer over een aantal veelvoorkomende [scenario's voor privézones](./private-dns-scenarios.md) die u kunt realiseren met privézones in Azure DNS.

* Zie [Veelgestelde vragen over Privé-DNS](./dns-faq-private.md) voor veelgestelde vragen en antwoorden over privézones in Azure DNS, waaronder specifiek gedrag dat u kunt verwachten voor bepaalde soorten bewerkingen.

* Ga naar [Overzicht van DNS-zones en -records](dns-zones-records.md) voor meer informatie over DNS-zones en records.

* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.