---
title: Belangrijke wijzigingen afkomstig van Azure Security Center
description: Aanstaande wijzigingen in Azure Security Center waarvan u mogelijk op de hoogte moet zijn en waarmee u mogelijk rekening moet houden
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2020
ms.author: memildin
ms.openlocfilehash: 052758079d8d413f7b0fead2a5abf3b47b9a691e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511328"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

- [Resources die 'Niet van toepassing' zijn, worden in Azure Policy-beoordelingen voortaan gerapporteerd als 'Compatibel'](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [Er zijn 35 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>Resources die 'Niet van toepassing' zijn, worden in Azure Policy-beoordelingen voortaan gerapporteerd als 'Compatibel'

**Geschatte datum van wijziging:** Januari 2021

Momenteel worden resources die zijn geëvalueerd voor een aanbeveling, en zijn beoordeeld als **niet van toepassing**, in Azure Policy weergegeven als 'Niet-compatibel'. Er zijn geen gebruikersacties mogelijk om deze status te wijzigen in 'Compatibel'. Vanaf deze geplande wijziging worden ze voor de duidelijkheid gerapporteerd als 'Compatibel'.

Dit is alleen van invloed op Azure Policy, waar het aantal compatibele resources zal toenemen. Het is niet van invloed om uw beveiligingsscore in Azure Security Center.

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>Er worden 35 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen

**Geschatte datum van wijziging:** December 2020

Azure Security Benchmark is de door Microsoft ontworpen, Azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingsframeworks. [Meer informatie over Azure Security-benchmark](../security/benchmarks/introduction.md).

De volgende 35 preview-aanbevelingen worden toegevoegd aan Security Center om de dekking van deze benchmark te verhogen.

Preview-aanbevelingen zorgen er niet voor dat een resource als beschadigd wordt weergegeven en ze worden niet opgenomen in de berekeningen van uw beveiligde score. Herstel ze waar mogelijk, zodat zij wanneer de preview-periode afloopt zullen bijdragen aan uw score. Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

| Beveiligingsmaatregelen                     | Nieuwe aanbevelingen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versleuteling at-rest inschakelen            | - Voor Azure Cosmos DB-accounts moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest<br>- Azure Machine Learning-werkruimten moeten worden versleuteld met een door de klant beheerde sleutel (CMK)<br>- BYOK-gegevensversleuteling moet zijn ingeschakeld voor MySQL-servers<br>- BYOK-gegevensversleuteling moet zijn ingeschakeld voor PostgreSQL-servers<br>- Voor Cognitive Services-accounts moet gegevensversleuteling zijn ingeschakeld met een door de klant beheerde sleutel (CMK)<br>- Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)<br>- Voor SQL Managed Instance moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest<br>- Voor SQL Server moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest<br>- Voor opslagaccounts moet een door de klant beheerde sleutel (CMK) worden gebruikt voor versleuteling                                                                                                                                                              |
| Best practices voor beveiliging implementeren    | - Abonnementen moeten een e-mailadres van contactpersonen voor beveiligingsproblemen bevatten<br> - Automatisch inrichten van de Log Analytics-agent moet zijn ingeschakeld voor uw abonnement<br> - E-mailmelding voor waarschuwingen met hoge urgentie moet zijn ingeschakeld<br> - E-mailmelding aan abonnementseigenaar voor waarschuwingen met hoge urgentie moet zijn ingeschakeld<br> - Beveiliging tegen leegmaken moet zijn ingeschakeld voor sleutelkluizen<br> - Voorlopig verwijderen moet zijn ingeschakeld voor sleutelkluizen |
| Toegang en machtigingen beheren        | - Clientcertificaten (binnenkomende clientcertificaten) moeten voor functie-apps zijn ingeschakeld |
| Toepassingen beschermen tegen DDoS-aanvallen | - WAF (Web Application Firewall) moet zijn ingeschakeld voor Application Gateway<br> - WAF (Web Application Firewall) moet zijn ingeschakeld voor de Azure Front Door-service |
| Onbevoegde netwerktoegang beperken | - De firewall moet zijn ingeschakeld in Key Vault<br> - Er moet een privé-eindpunt worden geconfigureerd voor Key Vault<br> - Voor App Configuration moeten privékoppelingen worden gebruikt<br> - Azure Cache voor Redis moet zich in een virtueel netwerk bevinden<br> - Voor Azure Event Grid-domeinen moet Private Link worden gebruikt<br> - Voor Azure Event Grid-onderwerpen moete Private Link worden gebruikt<br> - Voor Azure Machine Learning-werkruimten moet Private Link worden gebruikt<br> - Voor Azure SignalR Service moet Private Link worden gebruikt<br> - Azure Spring Cloud moet netwerkinjectie gebruiken<br> - Containerregisters mogen geen onbeperkte netwerktoegang toestaan<br> - Voor containerregisters moet Private Link worden gebruikt<br> - Openbare netwerktoegang moet worden uitgeschakeld voor MariaDB-servers<br> - Openbare netwerktoegang moet worden uitgeschakeld voor MySQL-servers<br> - Openbare netwerktoegang moet worden uitgeschakeld voor PostgreSQL-servers<br> - Voor een opslagaccount moet een Private Link-verbinding worden gebruikt<br> - Opslagaccounts moeten netwerktoegang beperken met behulp van regels voor virtuele netwerken<br> - Voor VM Image Builder-sjablonen moet Private Link worden gebruikt|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Gerelateerde links:

- [Meer informatie over Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Meer informatie over Azure Database for MariaDB](../mariadb/overview.md)
- [Meer informatie over Azure Database for MySQL](../mysql/overview.md)
- [Meer informatie over Azure Database for PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>Volgende stappen

Zie [Wat is er nieuw in Azure Security Center?](release-notes.md) voor alle recente wijzigingen aan het product.