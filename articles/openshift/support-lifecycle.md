---
title: Azure Red Hat open Shift-ondersteunings levenscyclus
description: Meer informatie over de ondersteunings levenscyclus en ondersteunde versies voor Azure Red Hat open Shift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: fca01c77a1ff47cbeee167eb408ed9f29a1307bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634325"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Levenscyclusondersteuning voor Azure Red Hat OpenShift 4

Red Hat geeft in ongeveer elke drie maanden een secundaire versie van het Red Hat open Shift container platform (OCP). Deze releases bevatten nieuwe functies en verbeteringen. Patch releases zijn vaker (meestal wekelijks) en zijn alleen bedoeld voor essentiële probleem oplossingen binnen een secundaire versie. Deze patches kunnen oplossingen bevatten voor beveiligings problemen of grote fouten.

Azure Red Hat open Shift is gebouwd op basis van specifieke versies van OCP. In dit artikel worden de versies van OCP beschreven die worden ondersteund voor Azure Red Hat open Shift en informatie over upgrades, afschaffing en ondersteunings beleid.

## <a name="red-hat-openshift-versions"></a>Versies van Red Hat open Shift

Red Hat open Shift container platform gebruikt semantische versie beheer. Bij semantische versie beheer worden verschillende niveaus van versie nummers gebruikt voor het opgeven van verschillende versies van versie beheer. In de volgende tabel ziet u de verschillende onderdelen van een semantisch versie nummer, in dit geval met behulp van het voor beeld-versie nummer 4.4.11.

|Primaire versie (x)|Secundaire versie (y)|Patch (z)|
|-|-|-|
|4|4|11|

Elk nummer in de versie geeft algemene compatibiliteit met de vorige versie aan:

* **Primaire versie**: er zijn op dit moment geen primaire versie versies gepland. Primaire versies worden gewijzigd wanneer incompatibele API-wijzigingen of achterwaartse compatibiliteit mogelijk wordt verbroken.
* **Secundaire versie**: ongeveer elke drie maanden vrijgegeven. Upgrades van secundaire versies kunnen bestaan uit onderdeel toevoegingen, verbeteringen, afschaffing, verwijderingen, oplossingen voor oplossingen, verbeteringen in de beveiliging en andere verbeteringen.
* **Patches**: doorgaans elke week, of indien nodig, vrijgegeven. Upgrades van patch-versies kunnen oplossingen voor problemen, verbeteringen van de beveiliging en andere verbeteringen omvatten.

Klanten moeten zich richten op het uitvoeren van de meest recente kleine release van de primaire versie die ze uitvoeren. Als uw productie cluster bijvoorbeeld op 4,4 is en 4,5 de meest recente, algemeen beschik bare secundaire versie voor de 4-serie is, moet u zo snel mogelijk een upgrade naar 4,5 uitvoeren.

### <a name="upgrade-channels"></a>Upgrade kanalen

Upgrade kanalen zijn gekoppeld aan een secundaire versie van het Red Hat open Shift container platform (OCP). Zo bevat de upgrade kanalen voor OCP 4,4 nooit een upgrade naar een 4,5-versie. Besturings elementen voor upgrade kanalen alleen vrijgeven selecteren en zijn niet van invloed op de versie van het cluster.

Azure Red Hat open Shift 4 ondersteunt alleen stabiele kanalen. Bijvoorbeeld: stabiel-4,4.

U kunt het stabiele 4,5-kanaal gebruiken om bij te werken van een eerdere secundaire versie van Azure Red Hat open SHIFT. Clusters die zijn bijgewerkt met behulp van snelle, Prerelease-en kandidaat-kanalen, worden niet ondersteund.

Als u overschakelt naar een kanaal dat uw huidige versie niet bevat, wordt er een waarschuwing weer gegeven en kunnen er geen updates worden aanbevolen, maar u kunt uw oorspronkelijke kanaal op elk gewenst moment veilig weer wijzigen.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Ondersteunings beleid voor Red Hat open Shift container platform versie

Azure Red Hat open Shift ondersteunt twee algemeen beschik bare versies van Red Hat open Shift container platform:
* De meest recente GA secundaire versie die wordt uitgebracht in azure Red Hat open Shift (waarnaar wordt verwezen als N)
* Een vorige secundaire versie (N-1)

Als dit beschikbaar is in een stabiel upgrade kanaal, kunnen nieuwere secundaire releases (N + 1, N + 2) die beschikbaar zijn in upstream-OCP ook worden ondersteund.

Essentiële patch updates worden automatisch toegepast op clusters door Azure Red Hat open Shift site betrouwbaarheids Engineers (SRE). Klanten die patch-updates vooraf willen installeren, zijn gratis.

Als Azure Red Hat open SHIFT nu bijvoorbeeld 4,5. z introduceert, wordt ondersteuning geboden voor de volgende versies:

|Nieuwe secundaire versie|Lijst met ondersteunde versies|
|-|-|
|4.5. z|4.5. z, 4.4. z|

'. z ' is representatief voor patch versies. Als er een stabiel upgrade kanaal beschikbaar is, kunnen klanten ook upgraden naar 4.6. z.

Wanneer een nieuwe secundaire versie wordt geïntroduceerd, wordt de oudste secundaire versie afgeschaft en verwijderd. Stel bijvoorbeeld dat de huidige lijst met ondersteunde versies 4,5. z en 4.4. z is. Wanneer Azure Red Hat open Shift versie 4.6. z loslaat, wordt de release van 4.4. z verwijderd en wordt deze binnen 30 dagen niet meer ondersteund.

> [!NOTE]
> Houd er rekening mee dat als klanten een niet-ondersteunde versie van Red Hat open Shift uitvoeren, kan worden gevraagd om een upgrade uit te voeren bij het aanvragen van ondersteuning voor het cluster. Clusters met niet-ondersteunde Red Hat open Shift-releases vallen niet onder de SLA voor Azure Red Hat open SHIFT.

## <a name="release-and-deprecation-process"></a>Vrijgave-en afschaffing proces

U kunt naar aanstaande versie releases en afschaffing van de Azure Red Hat open Shift-Kalender raadplegen.

Voor nieuwe secundaire versies van Red Hat open Shift container platform:
* Het Azure Red Hat open Shift SRE-team publiceert een pre-aankondiging met de geplande datum van een nieuwe versie en de oorspronkelijke afschaffing van de oude versie van de [Azure Red Hat open Shift-release opmerkingen](https://github.com/Azure/OpenShift/releases) ten minste 30 dagen vóór het verwijderen.
* Het Azure Red Hat open Shift SRE-team publiceert een service status melding die beschikbaar is voor alle klanten met Azure Red Hat open Shift en portal toegang, en stuurt een e-mail naar de abonnements beheerders met de datums voor het verwijderen van de geplande versie.
* Klanten hebben 30 dagen van het verwijderen van de versie van de upgrade naar een ondersteunde versie van de secundaire release nodig om ondersteuning te blijven ontvangen.

Voor nieuwe patch versies van Red Hat open Shift container platform:
* Vanwege de dringende aard van patch versies kunnen deze in de service worden geïntroduceerd door Azure Red Hat open Shift SRE-team zodra deze beschikbaar komen.
* Over het algemeen voert het Azure Red Hat open Shift SRE-team geen brede communicatie uit voor de installatie van nieuwe patch versies. Het team bewaakt en valideert echter de beschik bare CVE-patches om hen op tijd te ondersteunen. Als de klant actie vereist is, zal het team klanten informeren over de upgrade.

## <a name="supported-versions-policy-exceptions"></a>Ondersteunde versies beleids uitzonderingen

Het Azure Red Hat open Shift SRE-team behoudt zich het recht voor om nieuwe/bestaande versies toe te voegen of te verwijderen, of om toekomstige kleine release versies te vertragen, die zijn geïdentificeerd om een of meer kritieke productie problemen op te lossen, zonder voorafgaande kennisgeving.

Er kunnen specifieke patch releases worden overgeslagen, of de implementatie kan worden versneld, afhankelijk van de ernst van het probleem of de beveiligings problemen.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal-en CLI-versies

Wanneer u een Azure Red Hat open Shift-cluster in de portal of met de Azure CLI implementeert, wordt het cluster standaard ingesteld op de laatste (N) secundaire versie en de meest recente essentiële patch. Als Azure Red Hat open Shift bijvoorbeeld 4,5. z en 4.4. z ondersteunt, is de standaard versie van de nieuwe installaties 4,5. z. Klanten die de nieuwste upstream OCP secundaire versie (N + 1, N + 2) willen gebruiken, kunnen hun cluster op elk gewenst moment upgraden naar elke versie die beschikbaar is in de stabiele upgrade kanalen.

## <a name="azure-red-hat-openshift-release-calendar"></a>Azure Red Hat open Shift-release agenda

Raadpleeg de volgende hand leiding voor de [release geschiedenis van de eerdere Red Hat open Shift container platform (upstream)](https://access.redhat.com/support/policy/updates/openshift/#dates).

|OCP-versie|Upstream-release|Algemene Beschik baarheid van Azure Red Hat open Shift|Einde van de levens duur|
|-|-|-|-|
|4.3|Januari 2020|April 2020| Augustus 2020|
|4.4|Mei 2020|Juli 2020|4,6 GA|
|4.5|Juli 2020| November 2020|4,7 GA
|4,6|Oktober 2020| Februari 2021|4,8 GA|

## <a name="faq"></a>Veelgestelde vragen

**Wat gebeurt er wanneer een gebruiker een open Shift-cluster bijwerkt met een secundaire versie die niet wordt ondersteund?**

Als u een N-2-versie of ouder hebt, betekent dit dat u buiten de ondersteuning bent en wordt u gevraagd om te upgraden. Wanneer de upgrade van versie N-2 naar N-1 slaagt, is u terug in het ondersteunings beleid. Bijvoorbeeld:
* Als de oudste Azure Red Hat open Shift-versie 4.4. z is en u zich op 4,3. z of ouder bevindt, bent u buiten ondersteuning.
* Wanneer de upgrade van 4.3. z naar 4.4. z of hoger is geslaagd, bent u terug in het ondersteunings beleid.

Het herstellen van uw cluster naar een eerdere versie of een terugdraai bewerking wordt niet ondersteund. Alleen upgraden naar een nieuwere versie wordt ondersteund.

**Wat betekent ' buiten het ondersteunings team '?**

"Buiten ondersteuning" betekent dat de versie die u uitvoert, zich buiten de lijst met ondersteunde versies bevindt en u wordt mogelijk gevraagd het cluster bij te werken naar een ondersteunde versie bij het aanvragen van ondersteuning, tenzij u binnen de respijt periode van 30 dagen na versie afschaffing. Bovendien maakt Azure Red Hat open Shift geen garanties voor de uitvoering van runtime of SLA voor clusters buiten de lijst met ondersteunde versies aan het einde van de respijt periode van 30 dagen.
