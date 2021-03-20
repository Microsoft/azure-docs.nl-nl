---
title: Video van SaaS SQL-app
description: Dit artikel indexeert diverse tijd punten in onze 81 minuten video over het ontwerp van de SaaS-data base van de Ignite-conferentie van 11 oktober 2017. U kunt verder gaan met het onderdeel dat u interesseert. Er zijn ten minste drie patronen beschreven. Azure-functies die de ontwikkeling en het beheer vereenvoudigen, worden beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.custom: sqldbrb=1
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 8cceeef7e41e8fc5c48b4e67cdb1adf3aa4f02a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89483861"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Video die is geïndexeerd en annotaties voor een SaaS-app met meerdere tenants met behulp van Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dit artikel is een aantekeningen index in de tijd locaties van een video van 81 minuten over SaaS pacht-modellen of-patronen. In dit artikel kunt u achterwaarts of voorwaarts overs laan in de video waarop u een deel van uw interesse hebt. In de video worden de belangrijkste ontwerp opties voor een toepassing met meerdere tenants op Azure SQL Database beschreven. De video bevat demo's, scenario's met beheer code en op momenten dat er meer details worden geïnformeerd over de ervaring dan in onze schriftelijke documentatie.

De video versterkt de informatie in onze schriftelijke documentatie, gevonden op: 
- *Conceptueel:* [multitenancy-patronen voor de multi tenant SaaS-data base][saas-concept-design-patterns-563e]
- *Zelf studies:* [de SaaS-toepassing Wingtip tickets][saas-how-welcome-wingtip-app-679t]

In de video en de artikelen worden de vele fasen beschreven van het maken van een multi tenant-toepassing op Azure SQL Database in de Cloud. Speciale functies van Azure SQL Database maken het gemakkelijker om apps met meerdere tenants te ontwikkelen en te implementeren die eenvoudiger te beheren zijn en veilig kunnen worden uitgevoerd.

Onze schriftelijke documentatie wordt regel matig bijgewerkt. De video wordt niet bewerkt of bijgewerkt, dus hoe langer het detail kan worden verouderd.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Reeks van 38 tijd-geïndexeerde scherm afbeeldingen

In deze sectie wordt de tijd locatie van 38-discussies in de video van 81 minuten geïndexeerd. Telkens wanneer de index wordt voorzien van een scherm opname van de video, en soms aanvullende informatie.

Elke tijd index heeft de indeling *uu: SS*. Zo begint de tweede geïndexeerde tijd locatie, de gelabelde **sessie doelstellingen**, te beginnen bij de geschatte tijd op de locatie **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Compacte koppelingen naar geïndexeerde tijd locaties voor video

De volgende titels bevatten een koppeling naar de bijbehorende secties verderop in dit artikel:

- [1. **(start)** Welkom dia, 0:00:03](#anchor-image-wtip-min00001)
- [2. sessie doelstellingen, 0:03:11](#anchor-image-wtip-min00311)
- [3. Agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. webtoepassing met meerdere tenants, 0:05:05](#anchor-image-wtip-min00505)
- [5. app Web Form in actie, 0:05:55](#anchor-image-wtip-min00555)
- [6. kosten per Tenant (schaal, isolatie, herstel), 0:09:31](#anchor-image-wtip-min00931)
- [7. database modellen voor multi tenant: voor-en nadelen, 0:11:59](#anchor-image-wtip-min01159)
- [8. hybride model mengsels van MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. single-tenant versus multi tenant: voor-en nadelen, 0:16:44](#anchor-image-wtip-min01644)
- [10. Pools zijn kosten effectief voor onvoorspelbare workloads, 0:19:36](#anchor-image-wtip-min01936)
- [11. demo van data base-per-Tenant en hybride ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Live-App-formulier met Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB en geen DBA in het gezichts vermogen, 0:28:54](#anchor-image-wtip-min02854)
- [14. voor beeld van gebruik van elastische Pools in MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. Learning van MYOB en andere Isv's, 0:31:36](#anchor-image-wtip-min03136)
- [16. patronen vormen een E2E SaaS-scenario, 0:43:15](#anchor-image-wtip-min04315)
- [17. canonieke hybride multi tenant SaaS-app, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS-voor beeld-app, 0:48:10](#anchor-image-wtip-min04810)
- [19. scenario's en patronen die in de zelf studies worden besproken, 0:49:10](#anchor-image-wtip-min04910)
- [20. demo van zelf studies en GitHub-opslag plaats, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub opslag plaats micro soft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. het patronen verkennen, 0:56:20](#anchor-image-wtip-min05620)
- [23. tenants inrichten en onboarding, 0:57:44](#anchor-image-wtip-min05744)
- [24. tenants en toepassings verbinding inrichten, 0:58:58](#anchor-image-wtip-min05858)
- [25. demo van beheer scripts die één Tenant inrichten, 0:59:43](#anchor-image-wtip-min05943)
- [26. Power shell voor het inrichten en catalogiseren, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. onvoorspelbare Tenant werkbelastingen beheren, 1:04:36](#anchor-image-wtip-min10436)
- [29. elastische pool bewaking, 1:06:39](#anchor-image-wtip-min10639)
- [30. generatie van belasting en prestatie bewaking, 1:09:42](#anchor-image-wtip-min10942)
- [31. schema beheer op schaal, 1:10:33](#anchor-image-wtip-min11033)
- [32. gedistribueerde query in de Tenant-data bases, 1:12:21](#anchor-image-wtip-min11221)
- [33. demo van het genereren van tickets, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS AD-analyse, 1:12:46](#anchor-image-wtip-min11246)
- [35. Haal Tenant gegevens op in azure Synapse Analytics, 1:16:32](#anchor-image-wtip-min11632)
- [36. grafiek van dagelijkse verkoop verdeling, 1:16:48](#anchor-image-wtip-min11648)
- [37. inpakken en aanroepen naar actie, 1:19:52](#anchor-image-wtip-min11952)
- [38. resources voor meer informatie, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>De geïndexeerde index tijd locaties in de video

Als u op een scherm afbeelding klikt, gaat u naar de exacte tijd locatie in de video.


&nbsp; <a name="anchor-image-wtip-min00001"></a>
#### <a name="1-start-welcome-slide-00001"></a>1. *(start)* Welkom dia, 0:00:01

*Leren van MYOB: ontwerp patronen voor SaaS-toepassingen op Azure SQL Database-BRK3120*

[![Welkomst-dia][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Titel: leren van MYOB: ontwerp patronen voor SaaS-toepassingen op Azure SQL Database
- Bill.Gibson@microsoft.com
- Principal Program Manager, Azure SQL Database
- Micro soft Ignite Session BRK3120, Orlando, FL USA, oktober/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"></a>
#### <a name="2-session-objectives-00153"></a>2. sessie doelstellingen, 0:01:53
[![Sessie doelstellingen][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatieve modellen voor apps met meerdere tenants, met voor-en nadelen.
- SaaS-patronen om de ontwikkelings-, beheer-en resource kosten te reduceren.
- Een voor beeld-app en scripts.
- PaaS-functies en SaaS-patronen maken SQL Database een uiterst schaalbaar, rendabel gegevens platform voor SaaS met meerdere tenants.


&nbsp; <a name="anchor-image-wtip-min00417"></a>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Vergaderingen][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"></a>
#### <a name="4-multi-tenant-web-app-00500"></a>4. webtoepassing met meerdere tenants, 0:05:00
[![Wingtip SaaS-app: multi tenant web-app][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"></a>
#### <a name="5-app-web-form-in-action-00539"></a>5. app Web Form in actie, 0:05:39
[![App Web Form in actie][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"></a>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. kosten per Tenant (schaal, isolatie, herstel), 0:06:58
[![Kosten per Tenant, schalen, isolatie, herstel][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"></a>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. database modellen voor multi tenant: voor-en nadelen, 0:09:52
[![Database modellen voor multi tenants: voor-en nadelen][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"></a>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. hybride model mengsels van MT/ST, 0:12:29
[![Voor delen van het hybride model mengsels van MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"></a>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. single-tenant versus multi tenant: voor-en nadelen, 0:13:11
[![Single-tenant versus multi tenant: voor-en nadelen][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"></a>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Pools zijn kosten effectief voor onvoorspelbare workloads, 0:17:49
[![Pools zijn rendabel voor onvoorspelbare workloads][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"></a>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. demo van data base-per-Tenant en hybride ST/MT, 0:19:59
[![Demo van data base-per-Tenant en hybride ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"></a>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live-App-formulier met Dojo, 0:20:10
[![Live App-formulier met Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"></a>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB en geen DBA in het gezichts vermogen, 0:25:06
[![MYOB en geen DBA in het gezichts vermogen][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"></a>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. voor beeld van gebruik van elastische Pools in MYOB, 0:29:30
[![Voor beeld van gebruik van elastische Pools in MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"></a>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Learning van MYOB en andere Isv's, 0:31:25
[![Leren van MYOB en andere Isv's][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"></a>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. patronen vormen een E2E SaaS-scenario, 0:31:42
[![Patronen voor E2E SaaS-scenario][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"></a>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. canonieke hybride multi tenant SaaS-app, 0:46:04
[![Canonieke hybride SaaS-app met meerdere tenants][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"></a>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS-voor beeld-app, 0:48:01
[![Wingtip SaaS-voor beeld-app][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"></a>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. scenario's en patronen die in de zelf studies worden besproken, 0:49:00
[![Scenario's en patronen die in de zelf studies worden besproken][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"></a>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. demo van zelf studies en GitHub-opslag plaats, 0:50:12
[![Zelf studies voor demo's en GitHub opslag plaats][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"></a>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub opslag plaats micro soft/WingtipSaaS, 0:50:32
[![GitHub opslag plaats micro soft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"></a>
#### <a name="22-exploring-the-patterns-05615"></a>22. het patronen verkennen, 0:56:15
[![De patronen verkennen][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"></a>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. tenants inrichten en onboarding, 0:56:19
[![Tenants inrichten en onboarding][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"></a>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. tenants en toepassings verbinding inrichten, 0:57:52
[![Tenants en toepassings verbinding inrichten][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"></a>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. demo van beheer scripts die één Tenant inrichten, 0:59:36
[![Demo van beheer scripts die één Tenant inrichten][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"></a>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. Power shell voor het inrichten en catalogiseren, 0:59:56
[![Power shell voor inrichten en catalogiseren][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"></a>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * van TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"></a>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. onvoorspelbare Tenant werkbelastingen beheren, 1:03:34
[![Onvoorspelbare Tenant werkbelastingen beheren][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"></a>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. elastische pool bewaking, 1:06:32
[![Elastische pool-bewaking][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"></a>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. generatie van belasting en prestatie bewaking, 1:09:37
[![Belasting genereren en prestaties bewaken][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"></a>
#### <a name="31-schema-management-at-scale-10940"></a>31. schema beheer op schaal, 1:09:40
[![Schema beheer op schaal][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"></a>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. gedistribueerde query in de Tenant-data bases, 1:11:18
[![Gedistribueerde query in de Tenant-data bases][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"></a>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. demo van het genereren van tickets, 1:12:28
[![Demo van het genereren van tickets][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"></a>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS AD-analyse, 1:12:35
[![SSMS AD-analyse][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"></a>
#### <a name="35-extract-tenant-data-into-azure-synapse-analytics-11546"></a>35. Haal Tenant gegevens op in azure Synapse Analytics, 1:15:46
[![Tenant gegevens extra heren in azure Synapse Analytics][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"></a>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. grafiek van dagelijkse verkoop verdeling, 1:16:38
[![Grafiek van de dagelijkse verkoop verdeling][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"></a>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. inpakken en aanroepen naar actie, 1:17:43
[![Inpakken en aanroepen naar actie][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"></a>
#### <a name="38-resources-for-more-information-12035"></a>38. resources voor meer informatie, 1:20:35
[![Bronnen voor meer informatie][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blog bericht, 22 mei 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Conceptueel:* [multitenancy-patronen voor de multi tenant SaaS-data base][saas-concept-design-patterns-563e]

- *Zelf studies:* [de SaaS-toepassing Wingtip tickets][saas-how-welcome-wingtip-app-679t]

- GitHub-opslag plaatsen voor de smaak van de Wingtip tickets SaaS pacht-toepassing:
    - [Github opslag plaats voor-zelfstandig toepassings model][github-wingtip-standaloneapp].
    - [Github opslag plaats voor-db per Tenant model][github-wingtip-dbpertenant].
    - [Github opslag plaats for-multi TENANT DB model][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Volgende stappen

- [Eerste zelfstudie artikel][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Welkomst-dia"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Sessie doelstellingen."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Vergaderingen."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS-app: multi tenant web-app"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "App Web Form in actie"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Kosten per Tenant, schalen, isolatie, herstel"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Database modellen voor multi tenants: voor-en nadelen"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Voor delen van het hybride model mengsels van MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Single-tenant versus multi tenant: voor-en nadelen"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pools zijn rendabel voor onvoorspelbare workloads"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demo van data base-per-Tenant en hybride ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Live App-formulier met Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB en geen DBA in het gezichts vermogen"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Voor beeld van gebruik van elastische Pools in MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Leren van MYOB en andere Isv's"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Patronen voor E2E SaaS-scenario"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonieke hybride SaaS-app met meerdere tenants"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS-voor beeld-app"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scenario's en patronen die in de zelf studies worden besproken"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demo van zelf studies en GitHub opslag plaats"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub opslag plaats micro soft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "De patronen verkennen"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Tenants inrichten en onboarding"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Tenants en toepassings verbinding inrichten"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demo van beheer scripts die één Tenant inrichten"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "Power shell voor inrichten en catalogiseren"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * van TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Onvoorspelbare Tenant werkbelastingen beheren"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Elastische pool-bewaking"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Belasting genereren en prestaties bewaken"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Schema beheer op schaal"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Gedistribueerde query in de Tenant-data bases"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demo van het genereren van tickets"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS AD-analyse"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Tenant gegevens extra heren in azure Synapse Analytics"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Grafiek van de dagelijkse verkoop verdeling"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Inpakken en aanroepen naar actie"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Bronnen voor meer informatie"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

