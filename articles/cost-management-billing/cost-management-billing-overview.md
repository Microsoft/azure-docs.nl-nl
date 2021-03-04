---
title: Overzicht van Azure Kostenbeheer en facturering
description: U gebruikt functies van Azure Kostenbeheer en facturering voor het uitvoeren van administratieve taken en het beheren van factureringstoegang tot kosten. Er zijn ook functies voor het bewaken en controleren van Azure-uitgaven en voor het optimaliseren van het resourcegebruik van Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/03/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9fe658a1755ce3731f220ec656845da1f861fa9b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050529"
---
# <a name="what-is-azure-cost-management--billing"></a>Wat is Azure Kostenbeheer en facturering?

Door de Microsoft-cloud te gebruiken, kunt u de technische prestaties van uw zakelijke workloads aanzienlijk verbeteren. Het kan ook uw kosten verlagen, evenals de overhead die nodig is om organisatie-assets te beheren. De zakelijke kans creëert echter een risico vanwege de potentiële verspilling en inefficiënties die in uw cloudimplementaties worden geïntroduceerd. Azure Kostenbeheer en facturering is een door Microsoft geleverde reeks hulpprogramma’s die u helpen de kosten van uw workloads te analyseren, beheren en optimaliseren. Door de reeks te gebruiken, helpt u te verzekeren dat uw organisatie profiteert van de voordelen die de cloud biedt.

U kunt uw Azure-workloads beschouwen als de lampen in uw woning. Wanneer u de deur uitgaat, laat u de lampen dan aan? Zou u andere lampen kunnen gebruiken die efficiënter zijn, om uw maandelijkse energierekening te helpen verlagen? Hebt u in een bepaalde kamer meer lampen dan nodig is? U kunt Azure Kostenbeheer en facturering gebruiken om een vergelijkbare gedachtegang toe te passen op de workloads die door uw organisatie worden gebruikt.

Bij producten en services van Azure is het zo dat u alleen betaalt voor wat u gebruikt. Als u Azure-resources maakt en gebruikt, worden er kosten in rekening gebracht voor de resources. Omdat nieuwe resources zo gemakkelijk kunnen worden geïmplementeerd, kunnen de kosten van uw workloads aanzienlijk oplopen zonder goede analyse en bewaking. U gebruikt functies van Azure Kostenbeheer en facturering voor:

- Het uitvoeren van administratieve taken zoals het betalen van uw rekening
- Het beheren van factureringstoegang tot kosten
- Het downloaden van kosten- en gebruiksgegevens die zijn gebruikt om uw maandelijkse factuur te genereren
- Het proactief toepassen van gegevensanalyse op uw kosten
- Het instellen van uitgavendrempels
- Het identificeren van kansen voor workloadwijzigingen die uw uitgaven kunnen optimaliseren

Lees het artikel [Azure Cost Management best practices](./costs/cost-mgt-best-practices.md) voor meer informatie over kostenbeheer in een bedrijf.

![Diagram van het optimalisatie proces voor Cost Management en facturering.](./media/cost-management-optimization-process.png)

## <a name="understand-azure-billing"></a>Informatie over Azure-facturering

De factureringsfuncties van Azure worden gebruikt om de gefactureerde kosten te controleren en de toegang tot factureringsgegevens te beheren. In grotere organisaties worden factureringstaken normaal gesproken uitgevoerd door speciale teams.

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om uw facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsaccounts. Stel dat u zich voor uw persoonlijke projecten hebt geregistreerd voor Azure. U hebt dus misschien een individueel Azure-abonnement met daaraan een factureringsaccount gekoppeld. U kunt ook toegang hebben via de Enterprise-overeenkomst van uw organisatie of de Microsoft-klantovereenkomst. Voor elk scenario hebt u een afzonderlijk factureringsaccount.

### <a name="billing-accounts"></a>Factureringsaccounts

De Azure-portal ondersteunt momenteel de volgende typen factureringsaccounts:

- **Microsoft Online Services-programma**: Er wordt een individueel factureringsaccount voor een Microsoft Online Services-programma gemaakt wanneer u zich bij de Azure-website aanmeldt om Azure te gebruiken. Als u zich bijvoorbeeld aanmeldt voor een [gratis Azure-account](./manage/create-free-services.md), account met betalen naar gebruik-tarieven of als een Visual Studio-abonnee.

- **Enterprise Overeenkomst**: Er wordt een factureringsaccount voor een Enterprise-overeenkomst gemaakt wanneer uw organisatie een Enterprise-overeenkomst (EA) ondertekent om Azure te gebruiken.

- **Microsoft-klantovereenkomst**: Er wordt een factureringsaccount voor een Microsoft-klantovereenkomst aangemaakt, wanneer uw organisatie samenwerkt met een Microsoft-vertegenwoordiger om een Microsoft-klantovereenkomst te ondertekenen. Sommige klanten in bepaalde regio's, die zich via de Azure-website aanmelden voor een account met tarieven op gebruiksbasis of die hun [gratis Azure-account](./manage/create-free-services.md) upgraden, hebben mogelijk ook een factureringsaccount voor een Microsoft-klantovereenkomst.

## <a name="understand-azure-cost-management"></a>Inzicht in het werken met Azure Kostenbeheer

Facturering is wel gerelateerd, maar verschilt van kostenbeheer. Facturering is het proces van facturen versturen naar klanten voor geleverde goederen of diensten en de commerciële relatie beheren.

Cost Management toont organisatorische kosten en gebruikspatronen aan met geavanceerde analyses. Rapporten in Cost Management tonen de verbruikskosten die worden toegerekend aan Azure-services en aanbiedingen op Marketplace van derden. De kosten zijn gebaseerd op de overeengekomen prijzen en er wordt rekening gehouden met kortingen voor reserveringen en Azure Hybrid Benefit. Gezamenlijk tonen de rapporten uw intern en externe kosten voor gebruik en kosten voor Azure Marketplace. Andere kosten, zoals reserveringskosten, ondersteuning en belasting worden nog niet in rapporten getoond. De rapporten helpen u grip te krijgen op uw uitgaven en resource-gebruik en kunnen helpen afwijkingen te vinden in uitgaven. Voorspellende analyses zijn ook beschikbaar. Cost Management gebruikt Azure managementgroepen, budgetten en aanbevelingen om duidelijk aan te tonen hoe uw uitgaven zijn ingedeeld en hoe u kosten kunt reduceren.

U kunt de Azure Portal of verschillende API’s gebruiken voor geautomatiseerde export om kostengegevens te integreren met externe systemen en processen. Geautomatiseerde factureringsgegevensexport en geplande rapporten zijn ook beschikbaar.

Bekijk de video met een overzicht van Azure Cost Management voor een beknopt overzicht van hoe Azure Cost Management u kan helpen geld te besparen in Azure. Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>Uitgaven plannen en beheren

Verschillende manieren waarop Cost Management u kan helpen bij het plannen en beheren van uw kosten: Kosten analyseren, budgetten, aanbevelingen en kostenbeheergegevens exporteren.

U gebruikt kostenanalyse om de kosten van Azure voor uw bedrijf te verkennen en te analyseren. U kunt de totale kosten per bedrijf weergeven, zodat u begrijpt waar de kosten worden opgebouwd en uitgavenpatronen kunt identificeren. En u kunt de totale kosten bekijken om geschatte kostentrends per maand, per kwartaal en zelfs per jaar naast een budget te leggen.

Budgetten helpen u met plannen voor en voldoen aan financiële aansprakelijkheid in uw bedrijf. Ze helpen voorkomen dat kostendrempels of -limieten worden overschreden. Budgetten kunnen u ook helpen anderen te informeren over hun uitgaven om proactief kosten te beheren. En u kunt ermee zien hoe kosten zich ontwikkelen in de loop der tijd.

Aanbevelingen tonen hoe u de efficiëntie kunt optimaliseren en verbeteren door inactieve en onderbenutte resources te identificeren. Of ze kunnen goedkopere resource-opties tonen. Als u de aanbevelingen volgt, wijzigt u de manier waarop u uw resources gebruikt om geld te besparen. Om dit te doen, bekijkt u eerst de optimalisatieaanbevelingen om mogelijk inefficiënt gebruik te zien. Daarna volgt u de aanbeveling om uw Azure-resourcegebruik te wijzigen in een meer kosteneffectieve optie. Dan verifieert u de actie om ervoor te zorgen dat de wijziging die u hebt aangebracht ook is geslaagd.

Als u externe systemen gebruikt om kostenbeheergegevens te zien of te controleren, kunt u de gegevens van Azure eenvoudig exporteren. En u kunt een dagelijks geplande export in CSV-indeling instellen en de gegevensbestanden opslaan in Azure-opslag. Zo kunt u de gegevens vanaf uw externe systeem benaderen.

### <a name="cloudyn-deprecation"></a>Afschaffing van Cloudyn

Cloudyn is een Azure-service met betrekking tot Cost Management, die eind 2020 wordt afgeschaft. Waar mogelijk worden bestaande Cloudyn-functies rechtstreeks geïntegreerd in de Azure-portal. Er wordt op dit moment geen onboarding uitgevoerd voor nieuwe klanten, maar de ondersteuning voor het product blijft bestaan totdat dit volledig is afgeschaft.
 
### <a name="additional-azure-tools"></a>Aanvullende Azure-hulpprogramma’s

Azure heeft andere hulpprogramma’s die geen onderdeel uitmaken van de functieset van Azure Kostenbeheer en facturering. Ze spelen echter een belangrijke rol in het kostenbeheerproces. Zie de volgende koppelingen voor meer informatie over deze hulpprogramma’s.

- [Azure Prijscalculator](https://azure.microsoft.com/pricing/calculator/), gebruik dit hulpprogramma om de initiële cloudkosten in te schatten.
- [Azure Migrate](../migrate/migrate-services-overview.md), beoordeel de werkbelasting van uw huidige gegevenscentrum voor inzicht in wat er nodig is van een Azure-vervangingsoplossing.
- [Azure Advisor](../advisor/advisor-overview.md), identificeer ongebruikte VM’s en ontvang aanbevelingen over aankopen van Azure gereserveerde instanties.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), gebruik uw huidige on-premises Windows Server- of SQL Server-licenties voor VM’s in Azure om te besparen.

## <a name="next-steps"></a>Volgende stappen

Nu u weet wat Kostenbeheer en facturering is, is de volgende stap beginnen met het gebruik ervan.

- Begin Azure Cost Management te gebruiken om [kosten te analyseren](./costs/quick-acm-cost-analysis.md).
- U kunt ook meer informatie lezen over [Best practices voor Azure Cost Management](./costs/cost-mgt-best-practices.md).