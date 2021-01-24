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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 1b034c0f1c62eecf8139ed908a5a242060f3e886
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746557"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

- [De aanbevelingen voor de beveiliging van Kubernetes-workloads worden binnenkort uitgebracht voor algemene Beschik baarheid (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Verbeteringen in aanbeveling voor SQL-gegevens classificatie](#enhancements-to-sql-data-classification-recommendation)
- [Er zijn 35 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>De aanbevelingen voor de beveiliging van Kubernetes-workloads worden binnenkort uitgebracht voor algemene Beschik baarheid (GA)

**Geschatte datum van wijziging:** Januari 2021

De aanbevelingen voor de beveiliging van Kubernetes-workloads die worden beschreven in [uw Kubernetes-workloads beveiligen](kubernetes-workload-protections.md) , zijn momenteel beschikbaar als preview-versie. Hoewel een aanbeveling een preview-versie is, wordt er geen resource weer gegeven en is deze niet opgenomen in de berekeningen van uw beveiligde Score.

Deze aanbevelingen worden binnenkort uitgebracht voor algemene Beschik baarheid (GA *), en worden dus opgenomen* in de score berekening. Als u deze nog niet hebt doorgevoerd, kan dit leiden tot een geringe invloed op uw beveiligde Score.

Herstel ze waar mogelijk (meer informatie over het [oplossen van aanbevelingen in azure Security Center](security-center-remediate-recommendations.md)).

De aanbevelingen voor de beveiliging van Kubernetes-werk belasting zijn:

- Azure Policy-invoeg toepassing voor Kubernetes moet zijn geïnstalleerd en ingeschakeld op uw clusters
- De CPU- en geheugenlimieten van containers moeten worden afgedwongen
- Bevoegde containers moeten worden vermeden
- Onveranderbaar (alleen-lezen) hoofdbestandssysteem moet worden afgedwongen voor containers
- Container met escalatie van bevoegdheden moet worden vermeden
- Het uitvoeren van containers als hoofdgebruiker moet worden vermeden
- Containers die gevoelige hostnaamruimten delen, moeten worden vermeden
- Minimaal bevoegde Linux-functies moeten worden afgedwongen voor containers
- Het gebruik van HostPath-volumekoppelingen voor pods moet worden beperkt tot een bekende lijst
- Containers mogen alleen op toegestane poorten luisteren
- Services mogen alleen op toegestane poorten luisteren
- Het gebruik van hostnetwerken en -poorten moet worden beperkt
- Het overschrijven of uitschakelen van het AppArmor-profiel voor containers moet worden beperkt
- Container installatie kopieën moeten alleen worden geïmplementeerd vanuit vertrouwde registers             

Meer informatie over deze aanbevelingen vindt [u in Beveilig uw Kubernetes-workloads](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft 

**Geschatte datum voor wijziging:** Februari 2021

De volgende twee aanbevelingen zijn gepland om te worden afgeschaft in februari 2021:

- **Uw computers moeten opnieuw worden opgestart om systeem updates toe te passen**. Dit kan een geringe invloed op uw beveiligde Score opleveren.
- **Bewakings agent moet op uw computers zijn geïnstalleerd**. Deze aanbeveling is gekoppeld aan on-premises machines en sommige logica wordt overgezet naar een andere aanbeveling, **log Analytics problemen met de agent status moeten worden opgelost op uw computers**. Dit kan een geringe invloed op uw beveiligde Score opleveren.

We raden u aan om uw continue export-en werk stroom Automation-configuraties te controleren om te zien of deze aanbevelingen in hen zijn opgenomen. Daarnaast moeten alle Dash boards of andere bewakings hulpprogramma's die deze kunnen gebruiken, dienovereenkomstig worden bijgewerkt.

Meer informatie over deze aanbevelingen vindt u op de [overzichts pagina met aanbevelingen voor beveiliging](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Verbeteringen in aanbeveling voor SQL-gegevens classificatie

**Geschatte datum voor wijziging:** Q2 2021

De huidige versie van de gevoelige aanbevelings **gegevens in uw SQL-data bases moet worden geclassificeerd** in het beveiligings beheer **gegevens classificatie Toep assen** wordt afgeschaft en vervangen door een nieuwe versie die beter is afgestemd op de strategie voor gegevens classificatie van micro soft. Als gevolg hiervan:

- De aanbeveling heeft geen invloed meer op uw beveiligde Score
- Het beveiligings beheer (' gegevens classificatie Toep assen ') heeft geen invloed meer op uw beveiligde Score
- De ID van de aanbeveling wordt ook gewijzigd (momenteel b0df6f56-862d-4730-8597-38c0fd4ebd59)


### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>Er worden 35 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen

**Geschatte datum van wijziging:** Januari 2021

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