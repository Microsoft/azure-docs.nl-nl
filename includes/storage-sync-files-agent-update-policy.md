---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "77123184"
---
De Azure File Sync-agent wordt regel matig bijgewerkt om nieuwe functionaliteit toe te voegen en om problemen op te lossen. We raden u aan Microsoft Update te configureren voor het ophalen van updates voor de Azure File Sync-agent wanneer deze beschikbaar zijn.

#### <a name="major-vs-minor-agent-versions"></a>Belangrijkste versus secundaire agent versies
* Belang rijke agent versies bevatten vaak nieuwe functies en hebben een verhoogd aantal als het eerste deel van het versie nummer. Bijvoorbeeld: \* 2. \* .\*\*
* Secundaire agent versies worden ook wel ' patches ' genoemd en zijn vaker vrijgegeven dan primaire versies. Ze bevatten vaak fout oplossingen en kleinere verbeteringen, maar geen nieuwe functies. Bijvoorbeeld: \* \* . 3.\*\*

#### <a name="upgrade-paths"></a>Upgrade paden
Er zijn vier goedgekeurde en geteste manieren om de Azure File Sync agent-updates te installeren. 
1. **Eigen Configureer Microsoft Update om updates van de agent automatisch te downloaden en te installeren.**  
    We raden altijd aan om elke Azure File Sync update uit te voeren om ervoor te zorgen dat u toegang hebt tot de meest recente oplossingen voor de Server Agent. Microsoft Update maakt dit proces naadloos, door automatisch updates te downloaden en te installeren.
2. **Gebruik AfsUpdater.exe om agent updates te downloaden en te installeren.**  
    Het AfsUpdater.exe bevindt zich in de installatiemap van de agent. Dubbel klik op het uitvoer bare bestand om agent updates te downloaden en te installeren. 
3. **Een bestaande Azure File Sync agent patchen met behulp van een Microsoft Update patch-bestand of een MSP-bestand. Het meest recente Azure File Sync Update pakket kan worden gedownload uit de [Microsoft Update catalogus](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Als u een MSP-bestand uitvoert, wordt uw Azure File Sync-installatie bijgewerkt met dezelfde methode die automatisch wordt gebruikt door Microsoft Update in het vorige upgradepad. Bij het Toep assen van een Microsoft Update patch wordt een in-place upgrade van een Azure File Sync-installatie uitgevoerd.
4. **Down load het nieuwste installatie programma voor Azure File Sync agent van het [micro soft Download centrum](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Als u een bestaande installatie van Azure File Sync agent wilt bijwerken, verwijdert u de oudere versie en installeert u de nieuwste versie van het gedownloade installatie programma. De registratie van de server, synchronisatie groepen en andere instellingen worden onderhouden door het Azure File Sync-installatie programma.

#### <a name="automatic-agent-lifecycle-management"></a>Beheer van automatische agent levenscyclus
Met Agent versie 6 heeft het bestand synchronisatie team een functie voor het automatisch bijwerken van de agent geïntroduceerd. U kunt een van de twee modi selecteren en een onderhouds venster opgeven waarin de upgrade wordt uitgevoerd op de server. Deze functie is ontworpen om u te helpen bij het beheer van de agent levenscyclus door ofwel een guardrail te bieden waardoor uw agent niet kan verlopen of geen problemen kan ondervinden. Blijf de huidige instelling.
1. Met de **standaard instelling** wordt geprobeerd te voor komen dat de agent verlopen. Binnen 21 dagen na de geboekte verval datum van een agent probeert de agent zelf een upgrade uit te voeren. Er wordt een poging gedaan om één keer per week een upgrade uit te voeren binnen 21 dagen vóór de verval datum en in het geselecteerde onderhouds venster. **Met deze optie wordt niet voor komen dat regel matige Microsoft Update patches nodig zijn.**
1. U kunt desgewenst selecteren dat de agent automatisch wordt bijgewerkt zodra er een nieuwe agent versie beschikbaar komt (momenteel niet van toepassing op geclusterde servers). Deze update vindt plaats tijdens het geselecteerde onderhouds venster en laat uw server van nieuwe functies en verbeteringen profiteren zodra deze algemeen beschikbaar is. Dit is de aanbevolen instelling voor het maken van een probleem, waarmee belang rijke agent versies en regel matige update patches naar uw server worden geleverd. Elke agent die wordt uitgebracht, is bij GA-kwaliteit. Als u deze optie selecteert, wordt de nieuwste versie van de agent naar u door micro soft geflighteerd. Geclusterde servers worden uitgesloten. Zodra de flighting is voltooid, wordt de agent ook beschikbaar gesteld in het [micro soft Download centrum](https://go.microsoft.com/fwlink/?linkid=858257) aka.MS/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>De instelling voor automatische upgrades wijzigen

In de volgende instructies wordt beschreven hoe u de instellingen wijzigt nadat u het installatie programma hebt voltooid, als u wijzigingen moet aanbrengen.

Open een Power shell-console en navigeer naar de map waarin u de synchronisatie agent hebt geïnstalleerd en importeer vervolgens de server-cmdlets. Dit ziet er standaard ongeveer als volgt uit:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

U kunt uitvoeren `Get-StorageSyncAgentAutoUpdatePolicy` om de huidige beleids instelling te controleren en te bepalen of u deze wilt wijzigen.

Als u de huidige beleids instelling wilt wijzigen in het vertraagde update spoor, kunt u het volgende gebruiken:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Als u de huidige beleids instelling wilt wijzigen in de onmiddellijke update track, kunt u het volgende gebruiken:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garanties voor levens cyclus van agents en wijzigings beheer
Azure File Sync is een Cloud service waarmee voortdurend nieuwe functies en verbeteringen worden geïntroduceerd. Dit betekent dat een specifieke Azure File Sync agent versie alleen kan worden ondersteund voor een beperkte periode. Om uw implementatie te vergemakkelijken, worden de volgende regels gegarandeerd voldoende tijd en melding voor het uitvoeren van agent updates/upgrades in uw proces voor wijzigings beheer:

- Belang rijke agent versies worden ten minste zes maanden na de eerste release ondersteund.
- We garanderen dat er ten minste drie maanden bestaan tussen de ondersteuning van belang rijke agent versies. 
- Er worden waarschuwingen gegeven voor geregistreerde servers met behulp van een binnenkort verlopen agent van ten minste drie maanden vóór de verval datum. U kunt controleren of een geregistreerde server een oudere versie van de agent gebruikt onder het gedeelte geregistreerde servers van een opslag synchronisatie service.
- De levens duur van een secundaire agent versie is gebonden aan de bijbehorende primaire versie. Bijvoorbeeld, als agent versie 3,0 wordt uitgebracht, versie 2 van agent. \* worden allemaal ingesteld om te verlopen.

> [!Note]
> Als u een agent versie installeert met een verloop waarschuwing, wordt een waarschuwing weer gegeven, maar slaagt. Het installeren of koppelen met een verlopen agent versie wordt niet ondersteund en wordt geblokkeerd.
