---
title: Een migratie plan bouwen met Azure Migrate
description: Biedt richt lijnen voor het bouwen van een migratie plan met Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 1c2be47060004d464003c00cbbddb3b58a136e3c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871143"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Migratieplan bouwen met Azure Migrate

Volg dit artikel om uw migratie plan te bouwen met Azure met [Azure migrate](migrate-services-overview.md). 

## <a name="define-cloud-migration-goals"></a>Doelen voor Cloud migratie definiëren

Voordat u begint, kunnen bijdragen aan een succesvol zakelijk resultaat worden bijzondering van de [motivatie](/azure/cloud-adoption-framework/strategy/motivations) van uw overstap naar de Cloud. Zoals uitgelegd in het [Cloud-acceptatie raamwerk](/azure/cloud-adoption-framework), zijn er een aantal triggers en resultaten.   

**Zakelijke gebeurtenis** | **Resultaat van de migratie**
--- | ---
Afsluiten van Data Center | Kosten 
Fusie, overname of divestiture | Reductie van leverancier/technische complexiteit
Vermindering van de kapitaal kosten | Optimalisatie van interne bewerkingen
Einde van ondersteuning voor essentiële technologieën | Toename in bedrijfs flexibiliteit
Reactie op wijzigingen van de naleving van de regelgeving | Voor bereiding op nieuwe technische mogelijkheden
Nieuwe vereisten voor gegevens soevereiniteit | Schalen om te voldoen aan de markt vraag
Minder onderbrekingen en verbeteringen in de stabiliteit | Schalen om te voldoen aan de geografische eisen

Het identificeren van uw motivatie helpt u bij het vastmaken van uw strategische migratie doelen. De volgende stap is het identificeren en plannen van een migratie traject dat is afgestemd op uw workloads. De [Azure migrate: hulp programma voor detectie en evaluatie](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) helpt u bij het beoordelen van on-premises workloads en biedt hulp en hulpprogram ma's waarmee u kunt migreren.

## <a name="understand-your-digital-estate"></a>Meer informatie over uw digitale onroerend goed

Begin door uw on-premises infra structuur, toepassingen en afhankelijkheden te identificeren. Dit helpt u bij het identificeren van werk belastingen voor migratie naar Azure en voor het verzamelen van geoptimaliseerde kosten projecties. Het hulp programma detectie en evaluatie helpt u bij het identificeren van de werk belastingen die u in gebruik hebt, afhankelijkheden tussen werk belastingen en optimalisatie van werk belastingen.

### <a name="workloads-in-use"></a>Werk belastingen die in gebruik zijn

Azure Migrate gebruikt een licht gewicht Azure Migrate-apparaat voor de detectie zonder agents van on-premises virtuele VMware-machines, Hyper-V-Vm's, andere gevirtualiseerde servers en fysieke servers. Doorlopende detectie verzamelt gegevens over de server configuratie en de prestatie gegevens van de toepassing, evenals toepassings gegevens. Wat het apparaat verzamelt van on-premises servers: 

- Server-, schijf-en NIC-meta gegevens.

- Geïnstalleerde toepassingen, functies en onderdelen.

- Prestatie gegevens, met inbegrip van CPU-en geheugen gebruik, schijf-IOPS en door voer.

Nadat u gegevens hebt verzameld, kunt u de lijst met toepassings inventarisatie exporteren om apps te zoeken en SQL Server exemplaren die op uw servers worden uitgevoerd. U kunt het hulp programma Azure Migrate: data base Assessment gebruiken om inzicht te krijgen in SQL Server gereedheid.

 ![Toepassings inventaris op Portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Export van toepassings voorraad](./media/concepts-migration-planning/application-inventory-export.png)

Samen met de gegevens die zijn gedetecteerd met het hulp programma detectie en evaluatie kunt u uw CMDB-gegevens (Configuration Management data base) gebruiken om een weer gave van uw server en Data Base te maken, en om te begrijpen hoe uw servers worden gedistribueerd over bedrijfs eenheden, toepassings eigenaren, geografi, enzovoort. Dit helpt bij het bepalen van de werk belastingen voor migratie. 

### <a name="dependencies-between-workloads"></a>Afhankelijkheden tussen werk belastingen

Na de server detectie kunt u [afhankelijkheden analyseren](concepts-dependency-visualization.md)om afhankelijkheden tussen servers te visualiseren en identificeren, en optimaliserings strategieën voor het verplaatsen van onafhankelijke servers naar Azure. De visualisatie helpt te begrijpen of bepaalde servers in gebruik zijn of dat ze uit bedrijf kunnen worden genomen, in plaats van worden gemigreerd.  Het analyseren van afhankelijkheden helpt ervoor te zorgen dat er niets achter is en dat er onverwachte storingen optreden tijdens de migratie. Met de inventarisatie van de toepassing en afhankelijkheids analyse kunt u groepen van servers met hoge betrouw baarheid maken en deze eerst beoordelen.

 ![Toewijzing van afhankelijkheden](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Optimalisatie en grootte

Azure biedt flexibiliteit om de grootte van uw Cloud capaciteit gedurende een periode te wijzigen en migratie biedt u de mogelijkheid om de CPU-en geheugen resources die aan uw servers zijn toegewezen, te optimaliseren. Het maken van een evaluatie van de servers die u hebt gemaakt, helpt u bij het begrijpen van de prestatie geschiedenis van uw werk belasting. Dit is essentieel voor het rechts aanpassen van de grootte van Azure VM-Sku's en schijf aanbevelingen in Azure.

## <a name="assess-migration-readiness"></a>Gereedheid voor migratie beoordelen


### <a name="readinesssuitability-analysis"></a>Gereedheids-en geschiktheids analyse

U kunt het evaluatie rapport exporteren en filteren op deze categorieën om inzicht te krijgen in azure-gereedheid:

- **Gereed voor Azure**: servers kunnen worden gemigreerd naar Azure, zonder dat u wijzigingen hoeft aan te brengen. 
- **Voorwaardelijk gereed voor Azure**: servers kunnen worden gemigreerd naar Azure, maar u hoeft alleen maar kleine wijzigingen aan te brengen in overeenstemming met de richt lijnen voor herstel van de evaluatie.
- **Niet gereed voor Azure**: servers kunnen niet worden gemigreerd naar Azure. Problemen moeten worden opgelost volgens de richt lijnen voor herstel, vóór de migratie. 
- **Gereedheid onbekend**: Azure migrate kunt de gereedheid van de server niet bepalen vanwege onvoldoende meta gegevens.

Met database evaluaties kunt u de gereedheid van uw SQL Server-Data voor migratie naar Azure SQL Database of Azure SQL Managed instances beoordelen. De beoordeling toont de gereedheids status van de migratie voor elk exemplaar van SQL Server. Daarnaast kunt u voor elk exemplaar het aanbevolen doel zien in azure, potentiële migratie blok keringen, een telling van de laatste wijzigingen, de gereedheid voor Azure SQL DB of Azure SQL-VM en een compatibiliteits niveau. U kunt dieper ingrijpen om inzicht te krijgen in de gevolgen van migratie blokken en aanbevelingen voor het oplossen ervan.

 ![Database evaluaties](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Aanbevelingen voor de grootte

Nadat een server is gemarkeerd als gereed voor Azure, worden in de detectie-en evaluatie vereisten voor de Azure VM-SKU en het schijf type voor uw servers aangegeven. U kunt aanbevelingen voor het aanpassen van de grootte verkrijgen op basis van de prestatie geschiedenis (voor het optimaliseren van resources tijdens het migreren) of op basis van on-premises server instellingen zonder prestatie geschiedenis. In een evaluatie van de Data Base kunt u aanbevelingen bekijken voor de data base-SKU, de prijs categorie en het reken niveau.  

### <a name="get-compute-costs"></a>Berekenings kosten ophalen

Met de optie voor de grootte van de prestaties op basis van Azure Migrate-evaluaties kunt u virtuele machines met een juiste grootte op maat maken en deze gebruiken als best practice voor het optimaliseren van werk belastingen in Azure. Naast de juiste grootte zijn er nog enkele andere opties voor het besparen van Azure-kosten: 

- **Gereserveerde instanties**: met [gereserveerde instanties (RI)](https://azure.microsoft.com/pricing/reserved-vm-instances/)kunt u de kosten aanzienlijk verlagen ten opzichte van de [prijzen voor betalen per gebruik](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- **Azure Hybrid Benefit**: met [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)kunt u on-premises Windows Server-licenties met actieve Software Assurance-of Linux-abonnementen naar Azure brengen en combi neren met opties voor gereserveerde instanties.
- **Enterprise overeenkomst**: Azure [Enter prise Agreements (EA)](../cost-management-billing/manage/ea-portal-agreements.md) kunnen besparingen bieden voor Azure-abonnementen en-services.
- **Aanbiedingen**: er zijn meerdere [Azure-aanbiedingen](https://azure.microsoft.com/support/legal/offer-details/). Bijvoorbeeld [pay-as-you-go dev/test](https://azure.microsoft.com/pricing/dev-test/), of [Enterprise dev/test aanbieding](https://azure.microsoft.com/offers/ms-azr-0148p/), om lagere tarieven te bieden voor ontwikkel-en test-vm's
- **VM-uptime**: u kunt dagen per maand en uur per dag bekijken waarin Azure-vm's worden uitgevoerd. Als u servers afsluit wanneer deze niet in gebruik zijn, kunt u uw kosten verlagen (niet van toepassing op RIs).
- **Doel regio**: u kunt evaluaties maken in verschillende regio's, om erachter te komen of het migreren naar een bepaalde regio kosten effectief kan zijn. 

### <a name="visualize-data"></a>Gegevens visualiseren

U kunt detectie-en evaluatie rapporten (met Azure-gereedheids gegevens en maandelijkse kosten distributie) bekijken in de portal. U kunt ook evaluatie exporteren en uw migratie plan verrijken met extra visualisaties. U kunt meerdere beoordelingen maken, met verschillende combi Naties van eigenschappen en de set eigenschappen kiezen die het meest geschikt is voor uw bedrijf.  

 ![Overzicht van evaluaties](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Hiaten/blok keringen evalueren

Als u de apps en werk belastingen die u wilt migreren, opstelt, identificeert u uitval beperkingen voor deze en zoekt u naar operationele afhankelijkheden tussen uw apps en de onderliggende infra structuur. Deze analyse helpt u bij het plannen van migraties die voldoen aan uw beoogde herstel tijd (RTO), en zorgen ervoor dat er mini maal nul gegevens verloren gaan. Voordat u migreert, wordt u aangeraden eventuele compatibiliteits problemen of niet-ondersteunde functies te controleren en te beperken, waardoor server/SQL database migratie mogelijk wordt geblokkeerd. Het Azure Migrate detectie-en evaluatie rapport en Azure Migrate evaluatie van de data base kunnen u hierbij helpen. 

### <a name="prioritize-workloads"></a>Werk belastingen priori teren

Nadat u informatie over uw inventaris hebt verzameld, kunt u bepalen welke apps en workloads het eerst moeten worden gemigreerd. Ontwikkel een ' toep assen en leren ' benadering om apps op een systematische en instel bare manier te migreren, zodat u eventuele fouten kunt voor komen voordat u een volledige migratie start.

Als u de prioriteit van de migratie order wilt wijzigen, kunt u gebruikmaken van strategische factoren zoals complexiteit, time-to-migrate, zakelijke urgentie, productie/niet-productie overwegingen, naleving, beveiligings vereisten, toepassings kennis, enzovoort. 

Enkele aanbevelingen:

- **Prioriteit van snelle WINS** instellen: gebruik de evaluatie rapporten om te identificeren dat er weinig hangende vruchten zijn, met inbegrip van servers en data bases die volledig klaar zijn, en vereist een minimale inspanning om naar Azure te migreren. De tabel geeft een overzicht van een aantal manieren waarop u dit kunt doen.

    **Status** | **Actie**
    --- | ---
    **Azure Ready-Vm's** | Exporteer het beoordelings rapport en filter alle servers met de status *gereed voor Azure*. Dit kan de eerste groep servers zijn die u gaat verplaatsen naar Azure, met behulp van het Azure Migrate: hulp programma voor [server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) .
    **End-of-support-besturings systemen** | Exporteer het beoordelings rapport en filter alle servers met Windows Server 2008 R2/Windows Server 2008. Deze besturings systemen zijn aan het einde van de ondersteuning en alleen Azure biedt een gratis periode van drie jaar beveiligings updates wanneer u ze naar Azure migreert. Als u Azure Hybrid Benefit combineert en RIs gebruikt, kunnen de besparingen veel hoger zijn.
    **SQL Server migratie** | Gebruik de aanbevelingen voor de evaluatie van de data base om data bases te migreren die gereed zijn voor Azure SQL Database, met behulp van de Azure Migrate: data base Migration Tool. Migreer de data bases die klaar zijn voor Azure SQL VM met behulp van het Azure Migrate: hulp programma voor server migratie.
    **End-of-support-software** | Exporteer uw toepassings voorraad en filter op alle software/uitbrei dingen die mogelijk de volledige ondersteuning bereiken. Volg prioriteiten voor deze toepassingen voor migratie.
    **Onder provisioned servers** | Het evaluatie rapport exporteren en filteren op servers met weinig CPU-gebruik (%) en geheugen gebruik (%).  Migreer naar een Azure-VM met een juiste grootte en bespaar op kosten voor gelaagde resources.
    **Servers die worden ingericht** | Het beoordelings rapport exporteren en filteren op servers met hoog CPU-gebruik (%) en geheugen gebruik (%).  Oplossing van capaciteits beperkingen, voor komen dat overgebonden servers worden verbroken en verbeter de prestaties door deze servers naar Azure te migreren. Gebruik in azure opties voor automatisch schalen om aan de vraag te voldoen.<br/><br/> Analyseer beoordelings rapporten om opslag beperkingen te onderzoeken. Analyseer schijf-IOPS en door Voer en het aanbevolen schijf type.

- **Begin klein en vervolgens** op de slag: begin door apps en workloads te verplaatsen die minimale Risico's en complexiteit presen teren om vertrouwen in uw migratie strategie te bouwen. Analyseer Azure Migrate beoordelings aanbevelingen in combi natie met uw CMDB-opslag plaats om ontwikkel-en test werkbelastingen te zoeken en te migreren die kandidaten kunnen zijn voor pilot migraties. Feedback en informatie van pilot migraties kan handig zijn wanneer u begint met de migratie van productie werkbelastingen.  
- **Naleven**: Azure onderhoudt de grootste portefeuille voor naleving in de branche, op basis van de breedte en diepte van de aanbiedingen. Gebruik nalevings vereisten om de prioriteit van migraties te bepalen, zodat apps en workloads voldoen aan uw nationale, regionale en branchespecifieke normen en wetten. Dit geldt met name voor organisaties die met een bedrijfskritische procedure handelen, gevoelige informatie bevatten of zich in zwaar gereglementeerde branches bevinden. In deze typen organisaties, standaarden en voor Schriften Abound en kunnen vaak veranderen, waardoor het lastig is om bij te houden.  

## <a name="finalize-the-migration-plan"></a>Het migratie plan volt ooien

Voordat u uw migratie plan voltooit, moet u ervoor zorgen dat u andere mogelijke blok keringen als volgt beschouwt en verhelpt: 

- **Netwerk vereisten**: Evalueer netwerk bandbreedte en latentie beperkingen. Dit kan leiden tot onverwachte vertragingen en onderbrekingen van de migratie replicatie snelheid.
- Voor **keuren voor testen/na migratie**: een tijd buffer toestaan voor het uitvoeren van prestaties en acceptatie tests van gebruikers voor gemigreerde apps, of voor het configureren/aanpassen van apps na de migratie, zoals het bijwerken van database verbindings reeksen, het configureren van webservers, het uitvoeren van knippen/opschonen, enzovoort.
- **Machtigingen**: Bekijk de aanbevolen Azure-machtigingen en de Server/database toegangs rollen en-machtigingen die nodig zijn voor de migratie.
- **Training**: bereid uw organisatie voor op de digitale trans formatie. Een solide trainings basis is belang rijk voor een succes volle wijziging van de organisatie. Bekijk de gratis training over [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), inclusief cursussen over de basis principes van Azure, oplossings architecturen en beveiliging. Stimuleer uw team om [Azure-certificeringen](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)te verkennen.  
- **Implementatie ondersteuning**: krijg ondersteuning voor uw implementatie als u deze nodig hebt. Veel organisaties kiezen buiten de hulp om hun Cloud migratie te ondersteunen. Als u snel wilt overschakelen naar Azure en wilt vertrouwen met persoonlijke hulp, overweeg dan een [Azure expert managed service provider](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)of [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  


Maak een effectief migratie plan voor de cloud dat gedetailleerde informatie bevat over de apps die u wilt migreren, de beschik baarheid van apps en data bases, uitvaltijd beperkingen en migratie mijlpaals. In het plan wordt gekeken hoe lang het duurt voordat de gegevens worden gekopieerd, en een realistische buffer voor testen na de migratie en uitwissel activiteiten. 

Een test plan na de migratie moet functionele, integratie-, beveiligings-en prestatie tests en gebruiks voorbeelden omvatten, om ervoor te zorgen dat gemigreerde apps naar verwachting werken en dat alle database objecten en gegevens relaties worden overgebracht naar de Cloud.  

Bouw een migratie plan en Declareer een onderhouds venster voor het migreren van uw apps en data bases met een minimale downtime van nul, en beperk het mogelijke operationele en zakelijke effect tijdens de migratie.  

## <a name="migrate"></a>Migrate

U wordt aangeraden een test migratie uit te voeren in Azure Migrate voordat u een migratie op basis van een volledige schaal uitvoert. Een test migratie helpt u bij het schatten van de tijd die nodig is om uw migratie plan te verfijnen. Het biedt de mogelijkheid om potentiële problemen te detecteren en deze te corrigeren vóór de volledige migratie.

Wanneer u klaar bent voor migratie, gebruikt u de Azure Migrate: hulp programma voor server migratie en de Azure Data Migration service (DMS), voor een naadloze en geïntegreerde migratie, met end-to-end-tracering.

- Met het hulp programma voor server migratie kunt u on-premises Vm's en servers of Vm's die zich in een andere persoonlijke of open bare cloud bevinden (inclusief AWS, GCP) migreren met ongeveer nul uitval tijd.
- Azure DMS biedt een volledig beheerde service die is ontworpen om naadloze migraties van meerdere database bronnen naar Azure-gegevens platformen mogelijk te maken, met minimale downtime.  

## <a name="next-steps"></a>Volgende stappen

- Onderzoek de [reis voor Cloud migratie](/azure/architecture/cloud-adoption/getting-started/migrate)   in het Azure Cloud-acceptatie Framework.
- Lees een [kort overzicht](migrate-services-overview.md) van Azure migrate en Bekijk een [video aan](https://youtu.be/wFfq3YPxYHE)de slag.
- Meer informatie over het beoordelen van Vm's voor migratie naar [Azure-vm's](concepts-assessment-calculation.md).
