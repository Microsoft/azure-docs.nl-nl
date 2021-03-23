---
title: Gebruiks modellen van de HPC-cache van Azure
description: Hierin worden de verschillende modellen voor de cache gebruikt en wordt uitgelegd hoe u er een kunt kiezen voor het instellen van alleen-lezen of lezen/schrijven in cache en het beheren van andere cache-instellingen
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 3ad252520ca0cf7acdb3c84ef1da87c8076f3172
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775711"
---
# <a name="understand-cache-usage-models"></a>Informatie over de gebruiks modellen van de cache

Met cache gebruiks modellen kunt u aanpassen hoe uw Azure HPC-cache bestanden opslaat om uw werk stroom sneller te maken.

## <a name="basic-file-caching-concepts"></a>Basis concepten voor het opslaan van bestanden

Het opslaan van bestanden in cache is het versnellen van client aanvragen door Azure HPC cache. Deze basis procedures worden gebruikt:

* **Lees cache** -Azure HPC cache houdt een kopie bij van bestanden die door clients worden aangevraagd vanuit het opslag systeem. De volgende keer dat een client hetzelfde bestand aanvraagt, kan HPC-cache de versie in de cache opgeven, in plaats van deze opnieuw op te halen uit het back-end-opslag systeem.

* **Schrijf cache** -optioneel: Azure HPC cache kan een kopie opslaan van alle gewijzigde bestanden die worden verzonden vanaf de client computers. Als meerdere clients wijzigingen aanbrengen in hetzelfde bestand gedurende een korte periode, kan de cache alle wijzigingen in de cache verzamelen in plaats van elke wijziging afzonderlijk naar het back-end-opslag systeem te schrijven.

  Na een opgegeven tijds duur zonder wijzigingen, verplaatst de cache het bestand naar het systeem voor lange termijn opslag.

  Als de schrijf cache is uitgeschakeld, wordt het gewijzigde bestand niet opgeslagen in de cache en wordt het onmiddellijk naar het back-end-opslag systeem geschreven.

* **Write-back vertraging** : voor een cache waarvoor schrijf cache is ingeschakeld, is write-back vertraging de hoeveelheid tijd die de cache wacht op extra bestands wijzigingen voordat het bestand naar het back-end-opslag systeem wordt gekopieerd.

* **Back-end-verificatie** : de instelling voor de back-end-verificatie bepaalt hoe vaak de cache de lokale kopie van een bestand vergelijkt met de externe versie op het back-end-opslag systeem. Als de back-end nieuwer is dan de kopie in de cache, haalt de cache de externe kopie op en slaat deze op voor toekomstige aanvragen.

  De instelling voor de back-end-verificatie geeft aan wanneer de cache bestanden *automatisch* vergelijkt met de bron bestanden in de externe opslag. U kunt de Azure HPC-cache echter dwingen om bestanden te vergelijken door een directory bewerking uit te voeren die een READDIRPLUS-aanvraag bevat. READDIRPLUS is een standaard-NFS-API (ook wel uitgebreide Lees bewerking genoemd) die directory-meta gegevens retourneert, waardoor de cache bestanden vergelijkt en bijwerkt.

De gebruiks modellen die in de Azure HPC-cache zijn ingebouwd, hebben verschillende waarden voor deze instellingen, zodat u de beste combi natie voor uw situatie kunt kiezen.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Het juiste gebruiks model voor uw werk stroom kiezen

U moet een gebruiks model kiezen voor elk door NFS gekoppeld opslag doel dat u gebruikt. Azure Blob-opslag doelen hebben een ingebouwd gebruiks model dat niet kan worden aangepast.

Met de gebruiks modellen van HPC-cache kunt u kiezen hoe u snel antwoord kunt verdelen met het risico dat verouderde gegevens worden opgehaald. Als u de snelheid voor het lezen van bestanden wilt optimaliseren, kunt u er mogelijk niet voor zorgen dat de bestanden in de cache worden gecontroleerd op basis van de back-end-bestanden. Als u daarentegen wilt controleren of uw bestanden altijd up-to-date zijn met de externe opslag, kiest u een model dat regel matig wordt gecontroleerd.

Dit zijn de opties voor het gebruiks model:

* **Lees zware, incidentele schrijf bewerkingen** : gebruik deze optie als u lees toegang tot bestanden wilt versnellen die statisch zijn of zelden worden gewijzigd.

  Met deze optie worden client lees bewerkingen in de cache opgeslagen, maar niet opgeslagen in de cache. Er wordt direct een schrijf bewerking door gegeven naar de back-end-opslag.
  
  Bestanden die in de cache zijn opgeslagen, worden niet automatisch vergeleken met de bestanden op het NFS-opslag volume. (Lees de beschrijving van de back-end-verificatie hierboven voor meer informatie over hoe u deze hand matig kunt vergelijken.)

  Gebruik deze optie niet als er een risico bestaat dat een bestand rechtstreeks op het opslag systeem kan worden gewijzigd zonder het eerst naar de cache te schrijven. Als dat gebeurt, is de versie van het bestand in de cache niet synchroon met het back-end-bestand.

* **Meer dan 15% schrijf bewerkingen** : deze optie versnelt de lees-en schrijf prestaties. Wanneer u deze optie gebruikt, moeten alle clients toegang hebben tot bestanden via de Azure HPC-cache in plaats van de back-end-opslag rechtstreeks te koppelen. De bestanden in de cache hebben recente wijzigingen die nog niet zijn gekopieerd naar de back-end.

  In dit gebruiks model worden bestanden in de cache alleen gecontroleerd op basis van de bestanden in de back-end-opslag om de acht uur. Er wordt ervan uitgegaan dat de cache versie van het bestand meer actueel is. Een gewijzigd bestand in de cache wordt naar het back-end-opslag systeem geschreven nadat het 20 minuten in de cache is opgeslagen<!-- an hour --> zonder extra wijzigingen.

* **Clients schrijven naar het NFS-doel, waarbij de cache wordt omzeild** : Kies deze optie als clients in uw werk stroom gegevens rechtstreeks naar het opslag systeem schrijven zonder eerst naar de cache te schrijven of als u de consistentie van de gegevens wilt optimaliseren. Bestanden die door clients worden aangevraagd, worden opgeslagen in de cache (Lees bewerkingen), maar wijzigingen aan deze bestanden van de client (schrijven) worden niet in de cache opgeslagen. Ze worden rechtstreeks door gegeven aan het back-end-opslag systeem.

  Bij dit gebruiks model worden de bestanden in de cache veelvuldig gecontroleerd op basis van de back-end-versies voor updates-elke 30 seconden. Met deze verificatie kunnen bestanden buiten de cache worden gewijzigd terwijl de consistentie van gegevens wordt behouden.

  > [!TIP]
  > Deze eerste drie basis gebruiks modellen kunnen worden gebruikt voor het afhandelen van de meeste Azure HPC-cache-werk stromen. De volgende opties zijn voor minder veelvoorkomende scenario's.

* **Meer dan 15% schrijf bewerkingen, de back-upserver controleren op wijzigingen om de 30 seconden** en **meer dan 15% schrijf bewerkingen, waardoor de back-upserver elke 60 seconden wordt gecontroleerd.** deze opties zijn ontworpen voor werk stromen waarbij u zowel lees-als schrijf bewerkingen wilt versnellen, maar er is een kans dat een andere gebruiker rechtstreeks naar het back-end-opslag systeem schrijft. Als er bijvoorbeeld meerdere sets clients aan dezelfde bestanden op verschillende locaties werken, kan het zinvol zijn om snel toegang tot bestanden te krijgen met lage tolerantie voor verouderde inhoud van de bron.

* **Meer dan 15% schrijf bewerkingen, schrijf elke 30 seconden terug naar de server** . deze optie is ontworpen voor het scenario waarbij meerdere clients de bestanden actief wijzigen of als sommige clients rechtstreeks toegang hebben tot de back-end-opslag in plaats van de cache te koppelen.

  De frequente back-end-schrijf bewerkingen beïnvloeden de cache prestaties, dus u kunt het gebruik van het **groter-dan 15%** -gebruiks model voor schrijven gebruiken als er sprake is van een klein risico op bestands conflicten, bijvoorbeeld als u weet dat verschillende clients werken in verschillende gebieden van dezelfde bestandenset.

* **Lees de zware, Controleer de back-upserver om de 3 uur** . met deze optie wordt de prioriteit van snelle lees bewerkingen op de client geregeld, maar worden de bestanden in de cache van het back-end-opslag systeem regel matig vernieuwd, in tegens telling tot het **Lees** model.

Deze tabel bevat een overzicht van de verschillen in het gebruiks model:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Als u vragen hebt over het beste gebruiks model voor de werk stroom van uw Azure HPC-cache, neemt u contact op met uw Azure-vertegenwoordiger of opent u een ondersteunings aanvraag voor hulp.

## <a name="next-steps"></a>Volgende stappen

* [Opslag doelen toevoegen](hpc-cache-add-storage.md) aan uw Azure HPC-cache
