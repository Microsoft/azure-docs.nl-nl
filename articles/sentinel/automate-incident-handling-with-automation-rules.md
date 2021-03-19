---
title: Incident verwerking automatiseren in azure Sentinel | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u met automatiserings regels de verwerking van incidenten kunt automatiseren, zodat u de efficiëntie en effectiviteit van uw SOC kunt maximaliseren in reactie op beveiligings Risico's.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609362"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Incident verwerking automatiseren in azure Sentinel met Automation-regels

In dit artikel wordt uitgelegd wat Azure Sentinel Automation-regels zijn en hoe u deze kunt gebruiken voor het implementeren van uw via-bewerkingen (Security Orchestration, Automation and Response), het verhogen van de effectiviteit van uw SOC en het besparen van tijd en resources.

> [!IMPORTANT]
>
> - De functie voor het **automatiseren van regels** is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

## <a name="what-are-automation-rules"></a>Wat zijn Automation-regels?

Automatiserings regels zijn een nieuw concept in azure Sentinel. Met deze functie kunnen gebruikers de automatisering van incident verwerking centraal beheren. Naast de mogelijkheid om playbooks toe te wijzen aan incidenten (niet alleen op waarschuwingen zoals voorheen), kunt u met automatiserings regels ook de reacties voor meerdere analyse regels tegelijk automatiseren, automatisch tags toewijzen, toekennen of sluiten van incidenten zonder playbooks, en de volg orde bepalen van de acties die worden uitgevoerd. Met Automation-regels worden automatiserings gebruik in azure Sentinel gestroomlijnd en kunt u complexe werk stromen vereenvoudigen voor de indelings processen van uw incident.

## <a name="components"></a>Onderdelen

Automatiserings regels bestaan uit verschillende onderdelen:

### <a name="trigger"></a>Trigger

Automation-regels worden geactiveerd door het maken van een incident. 

Voor beoordeling: incidenten worden gemaakt op basis van waarschuwingen door analyse regels, waarvan er vier typen zijn, zoals wordt uitgelegd in de zelf studie [bedreigingen detecteren met ingebouwde analyse regels in azure Sentinel](tutorial-detect-threats-built-in.md).

### <a name="conditions"></a>Voorwaarden

Er kunnen complexe sets voor waarden worden gedefinieerd om te bepalen wanneer acties (zie hieronder) moeten worden uitgevoerd. Deze voor waarden zijn doorgaans gebaseerd op de statussen of waarden van kenmerken van incidenten en hun entiteiten, en ze kunnen `AND` / `OR` / `NOT` / `CONTAINS` Opera tors bevatten.

### <a name="actions"></a>Acties

Acties kunnen worden gedefinieerd om te worden uitgevoerd wanneer aan de voor waarden (zie hierboven) wordt voldaan. U kunt veel acties in een regel definiëren en u kunt de volg orde kiezen waarin ze worden uitgevoerd (zie hieronder). De volgende acties kunnen worden gedefinieerd met behulp van automatiserings regels, zonder [dat de geavanceerde functionaliteit van een Playbook](automate-responses-with-playbooks.md)nodig is:

- De status van een incident wijzigen, zodat uw werk stroom up-to-date blijft.

  - Bij het wijzigen van ' gesloten ', het opgeven van de reden van het [sluiten](tutorial-investigate-cases.md#closing-an-incident) en het toevoegen van een opmerking. Dit helpt u bij het bijhouden van uw prestaties en effectiviteit, en verfijnen om fout-positieven te verminderen.

- Het wijzigen van de ernst van een incident: u kunt opnieuw evalueren en prioriteiten opnieuw instellen op basis van de aanwezigheid, verzuim, waarden of kenmerken van entiteiten die betrokken zijn bij het incident.

- Een incident toewijzen aan een eigenaar: dit helpt u bij het oplossen van typen incidenten aan het personeel dat het meest geschikt is voor het verwerken ervan, of voor het meest beschik bare personeel.

- Een label toevoegen aan een incident: dit is handig voor het classificeren van incidenten per onderwerp, aanvaller of andere gemeen schappelijke noemer.

U kunt ook een actie definiëren voor het uitvoeren van een [Playbook](tutorial-respond-threats-playbook.md), om complexere reactie acties uit te voeren, inclusief alle die externe systemen vereisen. Alleen playbooks geactiveerd door de [incident trigger](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) zijn beschikbaar voor gebruik in Automation-regels. U kunt een actie definiëren voor het toevoegen van meerdere playbooks, of combi Naties van playbooks en andere acties, en de volg orde waarin ze worden uitgevoerd.

### <a name="expiration-date"></a>Vervaldatum

U kunt een verval datum definiëren voor een Automation-regel. De regel wordt na die datum uitgeschakeld. Dit is handig voor het afhandelen van incidenten die worden veroorzaakt door geplande, tijdgebonden activiteiten zoals het testen van indringing.

### <a name="order"></a>Volgorde

U kunt de volg orde definiëren waarin de automatiserings regels worden uitgevoerd. Later worden de voor waarden van het incident geëvalueerd aan de hand van de status van de voor gaande automatiserings regels.

Als bijvoorbeeld ' eerste automatiserings regel ' de ernst van een incident is gewijzigd van gemiddeld in laag en ' tweede automatiserings regel ' is gedefinieerd om alleen te worden uitgevoerd op incidenten met een gemiddelde of een hogere Ernst, wordt dit niet uitgevoerd op het incident.

## <a name="common-use-cases-and-scenarios"></a>Veelvoorkomende use cases en scenario's

### <a name="incident-triggered-automation"></a>Door incident geactiveerde automatisering

Tot nu toe kunnen alleen waarschuwingen een geautomatiseerd antwoord activeren door het gebruik van playbooks. Met Automation-regels kunnen incidenten automatisch antwoord ketens activeren, die nieuwe incident-geactiveerde playbooks kunnen bevatten ([speciale machtigingen zijn vereist](#permissions-for-automation-rules-to-run-playbooks)) wanneer een incident wordt gemaakt. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Playbooks voor micro soft-providers activeren

Automation-regels bieden een manier om de verwerking van beveiligings waarschuwingen van micro soft te automatiseren door deze regels toe te passen op incidenten die zijn gemaakt op basis van de waarschuwingen. De automatiserings regels kunnen playbooks aanroepen ([speciale machtigingen zijn vereist](#permissions-for-automation-rules-to-run-playbooks)) en de incidenten eraan door geven met al hun details, inclusief waarschuwingen en entiteiten. Over het algemeen kunt u best practices van Azure Sentinel gebruiken als het brandpuntsafstand van beveiligings bewerkingen.

Micro soft-beveiligings waarschuwingen omvatten het volgende:

- Microsoft Cloud App Security (MCAS)
- Azure AD-identiteitsbeveiliging
- Azure Defender (ASC)
- Defender voor IoT (voorheen ASC voor IoT)
- Microsoft Defender voor Office 365 (voorheen Office 365 ATP)
- Microsoft Defender voor Eindpunten (voorheen MDATP)
- Microsoft Defender voor Identiteit (voorheen Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Meerdere geordende playbooks/acties in één regel

U kunt nu vrijwel volledige controle hebben over de volg orde waarin acties en playbooks in één automatiserings regel worden uitgevoerd. U kunt ook de volg orde van de uitvoering van de automatiserings regels zelf bepalen. Zo kunt u uw playbooks aanzienlijk vereenvoudigen, deze beperken tot één taak of een kleine, eenvoudige reeks taken en deze kleine playbooks combi neren in verschillende combi Naties in verschillende automatiserings regels.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Een Playbook aan meerdere analyse regels tegelijk toewijzen

Als u een taak die u wilt automatiseren wilt uitvoeren op al uw analyse regels, kunt u het maken van een ondersteunings ticket in een extern ticket systeem, maar één Playbook Toep assen op een of meer van uw analyse regels, inclusief toekomstige regels, in één afbeelding. Dit maakt eenvoudig maar herhaaldelijk onderhoud en housekeeping taken veel minder van een klus.

### <a name="automatic-assignment-of-incidents"></a>Automatische toewijzing van incidenten

U kunt incidenten automatisch toewijzen aan de juiste eigenaar. Als uw SOC een analist heeft die in een bepaald platform is gespecialiseerd, kunnen incidenten met betrekking tot dat platform automatisch worden toegewezen aan die analist.

### <a name="incident-suppression"></a>Incident onderdrukking

U kunt regels gebruiken om automatisch incidenten op te lossen die bekend zijn met valse of onschadelijke positieven zonder gebruik te maken van playbooks. Wanneer u bijvoorbeeld indringings tests uitvoert, geplande onderhoud of upgrades uitvoert of automatiserings procedures test, worden er veel fout-positieve incidenten gemaakt die de SOC wil negeren. Met een time-Limited Automation-regel kan deze incidenten automatisch worden gesloten wanneer ze worden gemaakt, terwijl ze worden gelabeld met een descriptor van de oorzaak van de generatie.

### <a name="time-limited-automation"></a>Tijd beperkte automatisering

U kunt verval datums voor uw automatiserings regels toevoegen. Er zijn mogelijk andere gevallen dan het onderdrukken van incidenten die een tijdrovende automatisering rechtvaardigen. Mogelijk wilt u een bepaald type incident toewijzen aan een bepaalde gebruiker (bijvoorbeeld een leerling of consultant) voor een specifiek tijds bestek. Als het tijds bestek vooraf bekend is, kunt u de regel effectief uitschakelen aan het einde van de relevancy, zonder dat u hiervoor moet onthouden.

### <a name="automatically-tag-incidents"></a>Incidenten automatisch labelen

U kunt automatisch tags voor vrije tekst toevoegen aan incidenten om ze te groeperen of te classificeren op basis van de criteria van uw keuze.

## <a name="automation-rules-execution"></a>Uitvoering van Automation-regels

Automation-regels worden opeenvolgend uitgevoerd, volgens de volg orde die u hebt vastgesteld. Elke Automation-regel wordt uitgevoerd nadat de uitvoering is voltooid. Binnen een Automation-regel worden alle acties opeenvolgend uitgevoerd in de volg orde waarin ze zijn gedefinieerd.

Voor Playbook-acties is er een vertraging van twee minuten tussen het begin van de actie Playbook en de volgende actie in de lijst.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Machtigingen voor Automation-regels voor het uitvoeren van playbooks

Wanneer een Azure Sentinel Automation-regel een Playbook uitvoert, wordt er een speciaal voor deze actie geautoriseerde Azure Sentinel service-account gebruikt. Het gebruik van dit account (in plaats van uw gebruikers account) verhoogt het beveiligings niveau van de service.

Voor een Automation-regel om een Playbook uit te voeren, moet aan dit account expliciete machtigingen worden verleend voor de resource groep waar de Playbook zich bevindt. Op dat moment kan elke automatiserings regel elk Playbook in die resource groep uitvoeren.

Wanneer u een automatiserings regel configureert en een actie **Run Playbook** toevoegt, wordt een vervolg keuzelijst met playbooks weer gegeven. Playbooks waaraan Azure Sentinel geen machtigingen heeft, worden weer gegeven als niet-beschikbaar (' lichter gekleurd '). U kunt de Azure Sentinel-machtiging verlenen aan de playbooks-resource groepen op de plaats door de koppeling **Playbook machtigingen beheren** te selecteren.

> [!NOTE]
> **Machtigingen in een architectuur met meerdere tenants**
>
> Automatische regels bieden ondersteuning voor meerdere werk ruimten en implementaties met meerdere tenants (in het geval van multi tenants met behulp van [Azure Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)).
>
> Als uw Azure Sentinel-implementatie gebruikmaakt van een architectuur met meerdere tenants (bijvoorbeeld als u een MSSP hebt), kunt u een automatiserings regel in één Tenant hebben om een Playbook uit te voeren dat in een andere Tenant zit, maar machtigingen voor Sentinel voor het uitvoeren van de playbooks moeten worden gedefinieerd in de Tenant waar de playbooks zich bevinden, niet in de Tenant waar de automatiserings regels zijn gedefinieerd

## <a name="creating-and-managing-automation-rules"></a>Automation-regels maken en beheren

U kunt Automation-regels van verschillende punten maken en beheren in de Azure-Sentinel-ervaring, afhankelijk van uw specifieke behoeften en use-cases.

- **Automation-Blade**

    Automatiserings regels kunnen centraal worden beheerd in de nieuwe **Automation** -Blade (waarmee de Blade **Playbooks** wordt vervangen), onder het tabblad **automatiserings regels** . (u kunt nu ook Playbooks in deze Blade beheren op het tabblad **Playbooks** .) Van daaruit kunt u nieuwe Automation-regels maken en de bestaande wijzigen. U kunt ook Automation-regels slepen om de volg orde van de uitvoering te wijzigen en ze in of uit te scha kelen.

    In de Blade **Automation** ziet u alle regels die zijn gedefinieerd in de werk ruimte, samen met hun status (ingeschakeld/uitgeschakeld) en de analyse regels waarop deze worden toegepast.

    Wanneer u een Automation-regel nodig hebt die van toepassing is op veel Analytics-regels, maakt u deze rechtstreeks op de Blade **Automation** . Klik in het bovenste menu op **maken** en **nieuwe regel toevoegen**. Hiermee opent u het paneel **nieuwe Automation-regel maken** . Hier hebt u volledige flexibiliteit bij het configureren van de regel: u kunt deze Toep assen op alle analyse regels (met inbegrip van toekomstige sjablonen) en het breedste scala aan voor waarden en acties definiëren.

- **Wizard analyse regel**

    Op het tabblad **automatisch antwoord** van de wizard Analytics-regel kunt u regels voor automatisering bekijken, beheren en maken die van toepassing zijn op de specifieke analyse regel die in de wizard wordt gemaakt of bewerkt.

    Wanneer u op **maken** klikt en een van de regel typen (regel voor **geplande query** of **micro soft incident is gemaakt**) in het bovenste menu op de Blade **Analytics** , of als u een bestaande analyse regel selecteert en op **bewerken** klikt, wordt de wizard regel geopend. Wanneer u het tabblad **geautomatiseerd antwoord** selecteert, ziet u een sectie met de naam **incident automatisering**, waaronder de regels voor automatisering die momenteel op deze regel van toepassing zijn, worden weer gegeven. U kunt een bestaande Automation-regel selecteren om deze te bewerken of op **Nieuw toevoegen** om een nieuwe te maken.

    Wanneer u hier de Automation-regel maakt, wordt in het deel venster **nieuwe automatiserings regel maken** de voor waarde voor de **analyse regel** weer gegeven als niet-beschikbaar, omdat deze regel al is ingesteld op alleen van toepassing op de analyse regel die u in de wizard bewerkt. Alle andere configuratie opties zijn nog steeds beschikbaar voor u.

- **Blade incidenten**

    U kunt ook een Automation-regel maken op de Blade **incidenten** , om te reageren op één, terugkerend incident. Dit is handig bij het maken van een [onderdrukkings regel](#incident-suppression) voor het automatisch sluiten van incidenten met ' ruis '. Selecteer een incident in de wachtrij en klik op **Automation-regel maken** in het bovenste menu.

    U ziet dat het paneel **nieuwe Automation-regel maken** alle velden met waarden van het incident heeft gevuld. De regel heeft dezelfde naam als het incident, past deze toe op de analyse regel die het incident heeft gegenereerd, en gebruikt alle beschik bare entiteiten in het incident als voor waarden van de regel. Ook wordt er standaard een onderdrukkings actie (sluiten) voorgesteld en wordt een verval datum voor de regel voorgesteld. U kunt voor waarden en acties toevoegen of verwijderen en de verval datum wijzigen, zoals u dat wilt.

## <a name="auditing-automation-rule-activity"></a>Activiteit regel voor Automation controleren

Mogelijk wilt u weten wat er is gebeurd met een bepaald incident en wat een bepaalde automatiserings regel wel of niet heeft gedaan. U hebt een volledige record met incident Chronicles die voor u beschikbaar is in de tabel *SecurityIncident* op de Blade **logs** . Gebruik de volgende query om alle activiteiten van uw Automation-regel weer te geven:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Automation-regels gebruikt om uw Azure-Sentinel-incidenten wachtrij te beheren en een eenvoudige automatisering voor incident verwerking te implementeren.

- Zie [bedreigings reacties automatiseren met playbooks in azure Sentinel](automate-responses-with-playbooks.md)voor meer informatie over geavanceerde opties voor automatisering.
- Voor hulp bij het implementeren van Automation-regels en playbooks raadpleegt u [zelf studie: Playbooks gebruiken voor het automatiseren van bedreigings reacties in azure Sentinel](tutorial-respond-threats-playbook.md).
