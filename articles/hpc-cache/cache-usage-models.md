---
title: Azure HPC Cache-gebruiksmodellen
description: Beschrijft de verschillende cachegebruiksmodellen en hoe u ervoor kunt kiezen om alleen-lezen of lezen/schrijven-caching in te stellen en andere cache-instellingen te beheren
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 7e1b11fd15cca9b11fc627222318f08d31743336
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719183"
---
# <a name="understand-cache-usage-models"></a>Inzicht in cachegebruiksmodellen

Met cachegebruiksmodellen kunt u aanpassen hoe uw Azure HPC Cache opgeslagen om uw werkstroom te versnellen.

## <a name="basic-file-caching-concepts"></a>Basisconcepten voor bestands caching

Bestands-caching is hoe Azure HPC Cache clientaanvragen versnelt. Deze basisprocedures worden gebruikt:

* **Lees-caching:** Azure HPC Cache een kopie van bestanden die clients aanvragen bij het opslagsysteem. De volgende keer dat een client hetzelfde bestand aanvraagt, HPC Cache de versie in de cache leveren in plaats van dat deze opnieuw moet worden opgehaald uit het back-endopslagsysteem.

* **Schrijven in de caching:** u Azure HPC Cache een kopie opslaan van alle gewijzigde bestanden die vanaf de clientmachines worden verzonden. Als meerdere clients gedurende een korte periode wijzigingen aanbrengen in hetzelfde bestand, kan de cache alle wijzigingen in de cache verzamelen in plaats van elke wijziging afzonderlijk naar het back-endopslagsysteem te schrijven.

  Na een opgegeven hoeveelheid tijd zonder wijzigingen, verplaatst de cache het bestand naar het langetermijnopslagsysteem.

  Als schrijfcache is uitgeschakeld, wordt het gewijzigde bestand niet opgeslagen in de cache en wordt het onmiddellijk naar het back-endopslagsysteem geschreven.

* **Vertraging bij** terugschrijven: voor een cache met schrijfcache ingeschakeld, is write-back vertraging de hoeveelheid tijd die de cache wacht op aanvullende bestandswijzigingen voordat het bestand naar het back-endopslagsysteem wordt kopieert.

* **Back-endverificatie:** de instelling voor back-endverificatie bepaalt hoe vaak de cache de lokale kopie van een bestand vergelijkt met de externe versie op het back-endopslagsysteem. Als de back-endkopie nieuwer is dan de kopie in de cache, haalt de cache de externe kopie op en slaat deze op voor toekomstige aanvragen.

  De instelling voor back-endverificatie geeft aan wanneer de cache *de* bestanden automatisch vergelijkt met bronbestanden in externe opslag. U kunt echter wel Azure HPC Cache bestanden te vergelijken door een mapbewerking uit te voeren die een readdirplus-aanvraag bevat. Readdirplus is een standaard NFS-API (ook wel uitgebreid lezen genoemd) die mapmetagegevens retourneert, waardoor de cache bestanden kan vergelijken en bijwerken.

De gebruiksmodellen die zijn ingebouwd Azure HPC Cache hebben verschillende waarden voor deze instellingen, zodat u de beste combinatie voor uw situatie kunt kiezen.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Het juiste gebruiksmodel kiezen voor uw werkstroom

U moet een gebruiksmodel kiezen voor elk NFS-protocolopslagdoel dat u gebruikt. Azure Blob Storage-doelen hebben een ingebouwd gebruiksmodel dat niet kan worden aangepast.

HPC Cache gebruiksmodellen kunt u kiezen hoe u een snelle reactie wilt in balans brengen met het risico op het verkrijgen van verouderde gegevens. Als u de snelheid voor het lezen van bestanden wilt optimaliseren, is het wellicht niet belangrijk of de bestanden in de cache worden gecontroleerd op de back-endbestanden. Als u daarentegen wilt controleren of uw bestanden altijd up-to-date zijn met de externe opslag, kiest u een model dat regelmatig wordt gecontroleerd.

Dit zijn de opties voor het gebruiksmodel:

* **Leeszware, weinig** lees- of schrijf schrijfsnelheden: gebruik deze optie als u leestoegang wilt versnellen tot bestanden die statisch zijn of zelden worden gewijzigd.

  Met deze optie worden clientlezen in de cache opgeslagen, maar worden schrijf schrijfingen niet in de cache opgeslagen. Schrijf-naar-de-back-endopslag wordt onmiddellijk door naar de back-endopslag.
  
  Bestanden die zijn opgeslagen in de cache worden niet automatisch vergeleken met de bestanden op het NFS-opslagvolume. (Lees de beschrijving van back-endverificatie hierboven voor meer informatie over het handmatig vergelijken ervan.)

  Gebruik deze optie niet als er een risico bestaat dat een bestand rechtstreeks op het opslagsysteem wordt gewijzigd zonder het eerst naar de cache te schrijven. Als dat gebeurt, is de in cache opgeslagen versie van het bestand niet meer gesynchroniseerd met het back-endbestand.

* **Meer dan 15% schrijfprestaties:** deze optie versnelt zowel de lees- als schrijfprestaties. Wanneer u deze optie gebruikt, moeten alle clients toegang hebben tot bestanden via de Azure HPC Cache in plaats van de back-endopslag rechtstreeks te kunnen mounten. De bestanden in de cache bevatten recente wijzigingen die nog niet naar de back-end zijn gekopieerd.

  In dit gebruiksmodel worden bestanden in de cache slechts om de acht uur gecontroleerd op basis van de bestanden in de back-endopslag. Er wordt van uitgegaan dat de versie van het bestand in de cache actueler is. Een gewijzigd bestand in de cache wordt naar het back-endopslagsysteem geschreven nadat het 20 minuten in de cache is geweest<!-- an hour --> zonder aanvullende wijzigingen.

* Clients schrijven naar het **NFS-doel en** omzeilen de cache: kies deze optie als clients in uw werkstroom gegevens rechtstreeks naar het opslagsysteem schrijven zonder eerst naar de cache te schrijven of als u de consistentie van gegevens wilt optimaliseren. Bestanden die clients aanvragen, worden in de cache opgeslagen (lees leest), maar eventuele wijzigingen in deze bestanden van de client (schrijfbestanden) worden niet in de cache opgeslagen. Ze worden rechtstreeks doorgegeven aan het back-endopslagsysteem.

  Met dit gebruiksmodel worden de bestanden in de cache regelmatig om de 30 seconden gecontroleerd op updates in de back-endversies. Met deze verificatie kunnen bestanden buiten de cache worden gewijzigd met behoud van gegevensconsistentie.

  > [!TIP]
  > Deze eerste drie basisgebruiksmodellen kunnen worden gebruikt om het merendeel van de Azure HPC Cache te verwerken. De volgende opties zijn voor minder voorkomende scenario's.

* Meer dan 15% schrijfwerk, de **back-upserver elke 30** seconden controleren op wijzigingen en meer dan **15% schrijftaken,** de back-upserver elke 60 seconden controleren op wijzigingen: deze opties zijn ontworpen voor werkstromen waar u zowel lees- als schrijftaken wilt versnellen, maar er is een kans dat een andere gebruiker rechtstreeks naar het back-endopslagsysteem schrijft. Als bijvoorbeeld meerdere sets clients op dezelfde bestanden van verschillende locaties werken, kunnen deze gebruiksmodellen zinvol zijn om de behoefte aan snelle toegang tot bestanden te balanceren met lage tolerantie voor verouderde inhoud van de bron.

* Meer dan **15%** schrijf schrijft elke 30 seconden terug naar de server: deze optie is ontworpen voor het scenario waarin meerdere clients dezelfde bestanden actief wijzigen, of als sommige clients rechtstreeks toegang hebben tot de back-endopslag in plaats van de cache te installeren.

  De frequente back-end-schrijf schrijfprestaties zijn van invloed op de prestaties van de cache, dus u kunt overwegen het gebruiksmodel Groter dan **15%** schrijfgegevens te gebruiken als er een laag risico op bestandsconflicten is, bijvoorbeeld als u weet dat verschillende clients in verschillende gebieden van dezelfde bestandsset werken.

* **Intensief lezen,** de back-endserver elke drie uur controleren: met deze optie wordt prioriteit gegeven aan snelle lees- en schrijfgegevens aan de clientzijde, maar worden bestanden in de cache van het back-endopslagsysteem regelmatig vernieuwd, in tegenstelling tot het gebruiksmodel Intensief lezen **en** niet-vaak-schrijven.

Deze tabel bevat een overzicht van de verschillen in het gebruiksmodel:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Als u vragen hebt over het beste gebruiksmodel voor uw Azure HPC Cache werkstroom, kunt u met uw Azure-vertegenwoordiger praten of een ondersteuningsaanvraag voor hulp openen.

## <a name="know-when-to-remount-clients-for-nlm"></a>Weten wanneer clients opnieuw moeten worden ontkoppeld voor NLM

In sommige gevallen moet u mogelijk clients opnieuw ontkoppelen als u het gebruiksmodel van een opslagdoel wijzigt. Dit is nodig vanwege de manier waarop verschillende gebruiksmodellen NLM-aanvragen (Network Lock Manager) verwerken.

De HPC Cache bevindt zich tussen clients en het back-endopslagsysteem. Normaal gesproken geeft de cache NLM-aanvragen door aan het back-endopslagsysteem, maar in sommige gevallen bevestigt de cache zelf de NLM-aanvraag en retourneert deze een waarde naar de client. In Azure HPC Cache gebeurt dit alleen wanneer u gebruik  maakt van het gebruiksmodel Leeszware, niet-vaak schrijfbare schrijfgegevens (of met een standaarddoel voor blobopslag, dat geen configureerbare gebruiksmodellen heeft).

Er is een klein risico op bestandsconflicten als u wisselt tussen het **gebruiksmodel** Voor lezen intensief, weinig schrijfgebruik en een ander gebruiksmodel. Er is geen manier om de huidige NLM-status van de cache over te dragen naar het opslagsysteem of vice versa. De vergrendelingsstatus van de client is dus onjuist.

Ontkoppel de clients opnieuw om ervoor te zorgen dat ze een nauwkeurige NLM-status hebben met het nieuwe vergrendelingsbeheer.

Als uw clients een NLM-aanvraag verzenden wanneer het gebruiksmodel of de back-endopslag dit niet ondersteunt, krijgen ze een foutmelding.

### <a name="disable-nlm-at-client-mount-time"></a>NLM uitschakelen tijdens clientkoppeling

Het is niet altijd eenvoudig om te weten of uw clientsystemen NLM-aanvragen verzenden of niet.

U kunt NLM uitschakelen wanneer clients het cluster met behulp van de optie ``-o nolock`` in de ``mount`` opdracht.

Het exacte gedrag van de optie is afhankelijk van het besturingssysteem van de client. Raadpleeg daarom de documentatie voor het monteren ``nolock`` (man 5 nfs) voor uw clientbesturingssysteem. In de meeste gevallen wordt de vergrendeling lokaal naar de client verplaatst. Wees voorzichtig als uw toepassing bestanden vergrendelt op meerdere clients.

> [!NOTE]
> ADLS-NFS biedt geen ondersteuning voor NLM. Schakel NLM uit met de bovenstaande optie voor het plaatsen van een ADLS-NFS-opslagdoel.

## <a name="next-steps"></a>Volgende stappen

* [Opslagdoelen toevoegen](hpc-cache-add-storage.md) aan uw Azure HPC Cache
