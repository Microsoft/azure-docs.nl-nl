---
title: Releaseopmerkingen voor Azure Security Center
description: Een beschrijving van wat er nieuw is en gewijzigd is in Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 837ba5a0fd5ff94cc4f55cd4b01b8cb8a27425fd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634257"
---
# <a name="whats-new-in-azure-security-center"></a>Wat is er nieuw in Azure Security Center?

Azure Security Center is in actieve ontwikkeling en wordt voortdurend verbeterd. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt deze pagina u informatie over nieuwe functies, opgeloste fouten en afgeschafte functionaliteit.

Deze pagina wordt regelmatig bijgewerkt. Kom hier daarom regelmatig terug. 

Zie [Belangrijke aanstaande wijzigingen aan Azure Security Center](upcoming-changes.md) voor meer informatie over *geplande* wijzigingen die binnenkort in Security Center worden doorgevoerd. 

> [!TIP]
> Als u op zoek bent naar items die ouder zijn dan zes maanden, vindt u deze in het [Archief voor nieuwe functies in Azure AD in Azure Security Center](release-notes-archive.md).


## <a name="february-2021"></a>Februari 2021

De updates in februari zijn onder andere:

- [Pagina nieuwe beveiligings waarschuwingen in de Azure Portal uitgebracht voor algemene Beschik baarheid (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Aanbevelingen voor de beveiliging van Kubernetes-werk belasting uitgebracht voor algemene Beschik baarheid (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Pagina direct koppelen aan beleid van aanbevelings gegevens](#direct-link-to-policy-from-recommendation-details-page)
- [Aanbeveling voor SQL-gegevens classificatie heeft geen invloed meer op uw beveiligde Score](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Werk stroom automatisering kan worden geactiveerd door wijzigingen in de nalevings evaluaties van de regelgeving (preview-versie)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-preview)
- [Pagina-uitbrei dingen Asset Inventory](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Pagina nieuwe beveiligings waarschuwingen in de Azure Portal uitgebracht voor algemene Beschik baarheid (GA)

De pagina Beveiligingswaarschuwingen van Azure Security Center is opnieuw ontworpen en biedt nu het volgende:

- **Verbeterde sorteren-ervaring voor waarschuwingen** : helpt om de vermoeidheid van waarschuwingen te verminderen en de aandacht te vestigen op de meest relevante bedreigingen eenvoudiger, de lijst bevat aanpas bare filters en groeperings opties.
- **Meer informatie in de lijst met waarschuwingen** , zoals MITRE att&ACK tactiek.
- **Om voorbeeld waarschuwingen te maken** : als u de mogelijkheden van Azure Defender wilt evalueren en uw waarschuwingen wilt testen. configuratie (voor SIEM-integratie, e-mail meldingen en werk stroom automatisering) kunt u voorbeeld waarschuwingen maken op basis van alle Azure Defender-abonnementen.
- **Uitlijning met de incident ervaring van Azure-Sentinel** : voor klanten die beide producten gebruiken, is het niet meer eenvoudig om te scha kelen tussen de verschillende gebruikers.
- **Betere prestaties** voor grote waarschuwings lijsten.
- **Toetsenbord navigatie** via de lijst met waarschuwingen.
- **Waarschuwingen van Azure Resource Graph**: u kunt query's uitvoeren op waarschuwingen in Azure Resource Graph, de Kusto-achtige API voor al uw resources. Dit is ook handig als u uw eigen waarschuwingendashboards bouwt. [Meer informatie over Azure Resource Graph](../governance/resource-graph/index.yml).
- **Functie voor voorbeeld waarschuwingen maken** : Zie voor [beelden van Azure Defender-waarschuwingen genereren](security-center-alert-validation.md#generate-sample-azure-defender-alerts)voor het maken van voorbeeld waarschuwingen van de nieuwe waarschuwingen.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lijst met beveiligings waarschuwingen voor Azure Security Center":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Aanbevelingen voor de beveiliging van Kubernetes-werk belasting uitgebracht voor algemene Beschik baarheid (GA)

We zijn blij met het aankondigen van de algemene Beschik baarheid (GA) van de set aanbevelingen voor Kubernetes-werk belasting beveiligingen.

Om ervoor te zorgen dat Kubernetes-workloads standaard beveiligd zijn, heeft Security Center aanbevelingen voor het beveiligen van Kubernetes-niveau toegevoegd, waaronder handhavings opties met Kubernetes Admission Control.

Wanneer de Azure Policy-invoeg toepassing voor Kubernetes is geïnstalleerd op uw Azure Kubernetes service (AKS)-cluster, wordt elke aanvraag voor de Kubernetes API-server gecontroleerd op basis van de vooraf gedefinieerde set aanbevolen procedures-weer gegeven als 13 aanbevelingen voor beveiliging-voordat deze in het cluster worden bewaard. Vervolgens kunt u instellingen configureren om de aanbevolen procedures af te dwingen en ze te verplichten voor toekomstige workloads.

U kunt er bijvoorbeeld voor zorgen dat containers met machtigingen niet moeten worden gemaakt, en toekomstige aanvragen hiervoor worden dan geblokkeerd.

Meer informatie vindt u in [Aanbevolen procedures voor workloadbeveiliging met behulp van Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Hoewel de aanbevelingen in Preview waren, hebben ze geen AKS-cluster bron gerenderd en zijn ze niet opgenomen in de berekeningen van uw beveiligde Score. deze aankondiging wordt opgenomen in de berekening van de score. Als u deze nog niet hebt doorgevoerd, kan dit leiden tot een geringe invloed op uw beveiligde Score. Herstel ze waar mogelijk, zoals beschreven in [aanbevelingen herstellen in azure Security Center](security-center-remediate-recommendations.md).


### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Pagina direct koppelen aan beleid van aanbevelings gegevens

Wanneer u de details van een aanbeveling bekijkt, is het vaak handig om het onderliggende beleid te kunnen zien. Voor elke aanbeveling die wordt ondersteund door een beleid, is er een nieuwe koppeling op de pagina met aanbevelings Details:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Koppeling naar Azure Policy pagina voor het specifieke beleid dat een aanbeveling ondersteunt":::

Gebruik deze koppeling om de beleids definitie weer te geven en de evaluatie logica te controleren. 

Als u de lijst met aanbevelingen in onze [Naslag Gids voor beveiligings aanbevelingen](recommendations-reference.md)bekijkt, ziet u ook koppelingen naar de pagina met beleids definities:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Toegang tot de Azure Policy-pagina voor een specifiek beleid rechtstreeks op de pagina met Azure Security Center aanbevelingen" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Aanbeveling voor SQL-gegevens classificatie heeft geen invloed meer op uw beveiligde Score
De aanbeveling **gevoelige gegevens in uw SQL-data bases moeten worden geclassificeerd** die niet langer van invloed zijn op uw beveiligde Score. Dit is de enige aanbeveling in het beveiligings beheer **gegevens classificatie Toep assen** , zodat het besturings element nu een beveiligde Score waarde van 0 heeft.


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-preview"></a>Werk stroom automatisering kan worden geactiveerd door wijzigingen in de nalevings evaluaties van de regelgeving (preview-versie)
We hebben een derde gegevens type toegevoegd aan de trigger opties voor uw werk stroom Automatiseringen: wijzigingen in de nalevings beoordeling van de regelgeving.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Wijzigingen in de nalevings beoordeling van de regelgeving gebruiken om een werk stroom automatisering te activeren" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Pagina-uitbrei dingen Asset Inventory
De pagina Asset Inventory van Security Center is verbeterd op de volgende manieren:

- Samen vattingen boven aan de pagina bevatten nu niet- **geregistreerde abonnementen**, met het aantal abonnementen zonder Security Center ingeschakeld.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Aantal niet-geregistreerde abonnementen in de samen vattingen boven aan de pagina Asset Inventory":::

- Filters zijn uitgevouwen en uitgebreid, zodat ze het volgende bevatten:
    - **Aantal** -elk filter geeft het aantal resources aan dat voldoet aan de criteria van elke categorie

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Telt in de filters op de pagina Asset Inventory van Azure Security Center":::

    - **Bevat filter uitzonde ringen** (optioneel): Verfijn de resultaten tot resources die/geen uitzonde ringen hebben. Dit filter wordt niet standaard weer gegeven, maar is wel toegankelijk via de knop **filter toevoegen** .

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Het filter ' bevat uitzonde ring ' wordt toegevoegd aan de pagina Asset Inventory van Azure Security Center":::

Meer informatie over hoe u [uw resources kunt verkennen en beheren met inventarisatie van activa](asset-inventory.md).

## <a name="january-2021"></a>Januari 2021

De updates in januari zijn onder andere:

- [Azure Security Bench Mark is nu het standaard beleids initiatief voor Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Evaluatie van beveiligings problemen voor on-premises en multi-Cloud machines wordt uitgebracht voor algemene Beschik baarheid (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Beveiligde score voor beheer groepen is nu beschikbaar als preview-versie](#secure-score-for-management-groups-is-now-available-in-preview)
- [Secure Score-API wordt uitgebracht voor algemene Beschik baarheid (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Dangling DNS-beveiligingen die zijn toegevoegd aan Azure Defender voor App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Multi-Cloud connectors worden uitgebracht voor algemene Beschik baarheid (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Volledige aanbevelingen uitsluiten van uw beveiligde score voor abonnementen en beheer groepen](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Gebruikers kunnen nu de Tenant-brede zicht baarheid aanvragen bij hun globale beheerder](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Er zijn 35 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [CSV-export van een gefilterde lijst met aanbevelingen](#csv-export-of-filtered-list-of-recommendations)
- [' Niet van toepassing ' bronnen die nu zijn gerapporteerd als ' compatibel ' in Azure Policy-evaluaties](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Wekelijkse moment opnamen van beveiligde scores en nalevings gegevens van de regelgeving exporteren met doorlopend exporteren (preview)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Security Bench Mark is nu het standaard beleids initiatief voor Azure Security Center

Azure Security Benchmark is de door Microsoft ontworpen, Azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingsframeworks. Dit algemeen gerespecteerde Bench Mark bouwt voort op de besturings elementen van het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging.

In de afgelopen maanden is de lijst met ingebouwde beveiligings aanbevelingen van Security Center significant geworden om onze dekking van deze benchmark periode uit te breiden.

In deze release is de Bench Mark de basis voor de aanbevelingen van Security Center en volledig geïntegreerd als het standaard beleid. 

Alle Azure-Services hebben een beveiligings basislijn pagina in hun documentatie. [Dit is bijvoorbeeld de basis lijn van Security Center](security-baseline.md). Deze basis lijnen zijn gebaseerd op de beveiligings benchmarks van Azure.

Als u een dash board voor nalevings vereisten van Security Center gebruikt, ziet u twee exemplaren van de Bench Mark tijdens een overgangs periode:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Het dash board nalevings beleid van de Azure Security Center met de Security Bench Mark van Azure":::

Bestaande aanbevelingen worden niet beïnvloed en naarmate de Bench Mark groeit, worden wijzigingen automatisch weer gegeven in Security Center. 

Ga voor meer informatie naar de volgende pagina's:

- [Meer informatie over Azure Security Benchmark](../security/benchmarks/introduction.md)
- [De set van standaarden aanpassen in het dash board nalevings regelgeving](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Evaluatie van beveiligings problemen voor on-premises en multi-Cloud machines wordt uitgebracht voor algemene Beschik baarheid (GA)

In oktober hebben we een preview voor het scannen van servers met Azure Arc met [Azure Defender voor servers](defender-for-servers-introduction.md) geïntegreerde evaluatie van beveiligingsproblemen (mogelijk gemaakt door Qualys).

Het is nu uitgebracht voor algemene Beschik baarheid (GA).

Wanneer u Azure Arc op uw niet-Azure-machines hebt ingeschakeld, kan de geïntegreerde scanner voor beveiligingsproblemen handmatig en op schaal worden geïmplementeerd.

Met deze update kunt u de mogelijkheden van **Azure Defender voor servers** gebruiken om uw beheerprogramma voor beveiligingsproblemen te consolideren voor al uw Azure- en niet-Azure-assets.

Belangrijkste functies:

- Bewaken van de inrichtingsstatus van de scanner voor de evaluatie van beveiligingsproblemen van Azure Arc-machines
- Inrichten van de geïntegreerde agent voor de evaluatie van beveiligingsproblemen op niet-beveiligde Azure Arc-machines onder Windows en Linux (handmatig en op schaal)
- Ontvangen en analyseren van gedetecteerde beveiligingsproblemen afkomstig van geïmplementeerde agents (handmatig en op schaal)
- Geïntegreerde ervaring voor Azure-VM's en Azure Arc-machines

[Meer informatie over het implementeren van de geïntegreerde scanner voor beveiligings problemen op uw hybride machines](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Meer informatie over servers met Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Beveiligde score voor beheer groepen is nu beschikbaar als preview-versie

Op de pagina beveiligde Score worden nu de geaggregeerde beveiligde scores voor uw beheer groepen weer gegeven naast het abonnements niveau. Nu ziet u de lijst met beheer groepen in uw organisatie en de score voor elke beheer groep.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="De beveiligde scores voor uw beheer groepen weer geven.":::

Meer informatie over [beveiligingsscore en besturingselementen voor beveiliging in Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Secure Score-API wordt uitgebracht voor algemene Beschik baarheid (GA)

U hebt nu toegang tot uw score via de [API voor beveiligde scores](/rest/api/securitycenter/securescores/). De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportagemechanisme te bouwen van uw beveiligingsscores in de loop van de tijd. Bijvoorbeeld:

- de **Secure scores** -API gebruiken om de score voor een specifiek abonnement op te halen
- Gebruik de **besturings elementen voor beveiligde scores** om de beveiligings controles en de huidige Score van uw abonnementen weer te geven

Meer informatie over externe hulpprogram ma's die mogelijk zijn gemaakt met de beveiligde Score-API in [het beveiligde Score gebied van onze github-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Meer informatie over [beveiligingsscore en besturingselementen voor beveiliging in Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Dangling DNS-beveiligingen die zijn toegevoegd aan Azure Defender voor App Service

De overname van subdomeinen is een veelvoorkomende bedreiging van hoge urgentie voor organisaties. Een overname van een subdomein kan optreden wanneer u een DNS-record hebt die naar een website van een onvoorziene inrichting wijst. Dergelijke DNS-records worden ook wel ' Dangling DNS-vermeldingen genoemd. CNAME-records zijn met name gevoelig voor deze bedreiging. 

Met de overname van subdomeinen kunnen bedreigings gegevens verkeer dat is bedoeld voor het domein van een organisatie, omleiden naar een site die schadelijke activiteiten uitvoert.

Azure Defender voor App Service detecteert nu Dangling DNS-vermeldingen wanneer een App Service website buiten gebruik wordt gesteld. Dit is het moment waarop de DNS-vermelding verwijst naar een niet-bestaande bron en uw website is kwetsbaar voor een overname van een subdomein. Deze beveiligingen zijn beschikbaar, ongeacht of uw domeinen worden beheerd met Azure DNS of een extern domein registratie en van toepassing zijn op zowel App Service op Windows als App Service in Linux.

Meer informatie:

- [Naslag tabel voor app service-waarschuwing](alerts-reference.md#alerts-azureappserv) : bevat twee nieuwe Azure Defender-waarschuwingen die worden geactiveerd wanneer een DNS-vermelding van Dangling wordt gedetecteerd
- [Dangling DNS-vermeldingen voor komen en de overname van subdomeinen voor komen](../security/fundamentals/subdomain-takeover.md) -informatie over de dreiging van de beveiliging van subdomeinen en het DNS-aspect van Dangling
- [Inleiding tot Azure Defender voor App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Multi-Cloud connectors worden uitgebracht voor algemene Beschik baarheid (GA)

Veel workloads in de cloud beslaan meerdere cloudplatforms, dus moeten cloudbeveiligingsservices hetzelfde doen.

Azure Security Center biedt bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

Het koppelen van uw AWS-of GCP-accounts integreert de systeem eigen beveiligings Programma's, zoals AWS Security hub en GCP Security Command Center in Azure Security Center.

Dit betekent dat Security Center zicht baarheid en bescherming biedt in alle grote Cloud omgevingen. Enkele van de voor delen van deze integratie:

- Automatic agent Provisioning-Security Center gebruikt Azure Arc om de Log Analytics agent te implementeren in uw AWS-instanties
- Beleidsbeheer
- Beheer van beveiligingsproblemen
- Geïntegreerde eindpuntdetectie en -respons (EDR)
- Detectie van onjuiste beveiligingsconfiguraties
- Eén weer gave met aanbevelingen voor de beveiliging van alle cloud providers
- Al uw resources opnemen in de berekening van de veilige Score van Security Center
- Nalevings beoordeling van de regelgeving van uw AWS-en GCP-resources

Selecteer in het menu van Security Center **meerdere Cloud connectors** en u ziet de opties voor het maken van nieuwe connectors:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Knop 'AWS-account toevoegen' op de pagina voor meerdere cloudconnectors van Security Center":::

Meer informatie in:
- [Uw AWS-accounts verbinden met Azure Security Center](quickstart-onboard-aws.md)
- [Uw GCP-accounts verbinden met Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Volledige aanbevelingen uitsluiten van uw beveiligde score voor abonnementen en beheer groepen

De uitzonde ring kan worden uitgebreid om volledige aanbevelingen op te nemen. Meer opties voor het afstemmen van de beveiligings aanbevelingen die Security Center maakt voor uw abonnementen, beheer groep of resources.

In sommige gevallen wordt een resource als beschadigd weer gegeven wanneer u weet dat het probleem is opgelost door een hulp programma van derden dat Security Center niet is gedetecteerd. Het is ook mogelijk dat er een aanbeveling wordt weer gegeven in een bereik waarvan u denkt dat deze niet deel uitmaakt. De aanbeveling is mogelijk niet geschikt voor een specifiek abonnement. Of misschien heeft uw organisatie besloten om de Risico's te accepteren die zijn gerelateerd aan de specifieke resource of aanbeveling.

Met deze preview-functie kunt u nu een uitzonde ring maken voor een aanbeveling:

- **Een resource vrij** te stellen om ervoor te zorgen dat deze niet in de toekomst wordt weer gegeven met de foutieve resources en niet van invloed is op uw beveiligde Score. De resource wordt weer gegeven als niet van toepassing en de reden wordt weer gegeven als ' uitgesloten ' met de specifieke reden die u selecteert.

- De **uitsluiting van een abonnement of beheer groep** om ervoor te zorgen dat de aanbeveling niet van invloed is op uw beveiligde Score en niet in de toekomst wordt weer gegeven voor het abonnement of de beheer groep. Dit heeft betrekking op bestaande resources en alle die u in de toekomst maakt. De aanbeveling wordt gemarkeerd met de specifieke reden die u selecteert voor het bereik dat u hebt geselecteerd.

Meer informatie over [het uitsluiten van resources en aanbevelingen van uw beveiligde Score](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Gebruikers kunnen nu de Tenant-brede zicht baarheid aanvragen bij hun globale beheerder

Als een gebruiker geen machtigingen heeft om Security Center gegevens weer te geven, ziet u nu een koppeling om machtigingen aan te vragen bij de globale beheerder van de organisatie. De aanvraag bevat de gewenste rol en de reden waarom het nodig is.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Vaandel dat een gebruiker informeert, kan de machtigingen voor de hele Tenant aanvragen.":::

Meer informatie over [het aanvragen van machtigingen voor tenants wanneer dat niet het geval is](security-center-management-groups.md#request-tenant-wide-permissions-when-yours-are-insufficient) .


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Er zijn 35 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen

Azure Security Bench Mark is het standaard beleid Initiative in Azure Security Center. 

Om de dekking van deze bench Mark te verg Roten, zijn de volgende 35 preview-aanbevelingen toegevoegd aan Security Center.

> [!TIP]
> Preview-aanbevelingen zorgen er niet voor dat een resource als beschadigd wordt weergegeven en ze worden niet opgenomen in de berekeningen van uw beveiligde score. Herstel ze waar mogelijk, zodat zij wanneer de preview-periode afloopt zullen bijdragen aan uw score. Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

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




### <a name="csv-export-of-filtered-list-of-recommendations"></a>CSV-export van een gefilterde lijst met aanbevelingen 

In november 2020 hebben we filters toegevoegd aan de aanbevelingspagina ([Lijst met aanbevelingen bevat nu filters](#recommendations-list-now-includes-filters)). In december hebben we die filters uitgebreid ([De aanbevelingspagina bevat nieuwe filters voor omgeving, ernst en beschikbare reacties](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Met deze aankondiging veranderen we het gedrag van de knop **Downloaden naar CSV**, zodat de CSV-export alleen de aanbevelingen omvat die momenteel worden weergegeven in de gefilterde lijst. 

In de onderstaande afbeeldingen ziet u bijvoorbeeld dat de lijst is gefilterd in twee aanbevelingen. Het gegenereerde CSV-bestand omvat de statusgegevens voor elke resource die door deze twee aanbevelingen wordt beïnvloed.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Gefilterde aanbevelingen exporteren naar een CSV-bestand":::

Meer informatie in [Beveiligingsaanbevelingen in Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>' Niet van toepassing ' bronnen die nu zijn gerapporteerd als ' compatibel ' in Azure Policy-evaluaties

Voorheen werden resources die zijn geëvalueerd voor een aanbeveling en **niet van toepassing** zijn, weer gegeven in azure Policy als ' niet-compatibel '. Gebruikers acties kunnen hun status niet wijzigen in compatibel. Met deze wijziging worden ze gerapporteerd als "compatibel" voor betere duidelijkheid.

Dit is alleen van invloed op Azure Policy, waar het aantal compatibele resources zal toenemen. Het is niet van invloed om uw beveiligingsscore in Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Wekelijkse moment opnamen van beveiligde scores en nalevings gegevens van de regelgeving exporteren met doorlopend exporteren (preview)

We hebben een nieuwe preview-functie toegevoegd aan de [continue export](continuous-export.md) tools voor het exporteren van wekelijkse moment opnamen van beveiligde scores en nalevings gegevens voor de regelgeving.

Wanneer u een continue export definieert, stelt u de export frequentie in:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="De frequentie van uw continue export kiezen":::

- **Streaming** – beoordelingen worden in realtime verzonden wanneer de status van een resource wordt bijgewerkt (als er geen updates worden uitgevoerd, worden er geen gegevens verzonden).
- **Moment opnamen** : er wordt elke week een moment opname gemaakt van de huidige status van alle nalevings evaluaties (dit is een preview-functie voor wekelijkse moment opnamen van beveiligde scores en nalevings gegevens voor regelgeving).

Meer informatie over de volledige mogelijkheden van deze functie in het [voortdurend exporteren van Security Center gegevens](continuous-export.md)

## <a name="december-2020"></a>December 2020

Updates in december omvatten:

- [Azure Defender voor SQL-servers op computers is algemeen beschikbaar](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender voor SQL-ondersteuning voor een toegewezen SQL-pool in Azure Synapse Analytics is algemeen beschikbaar](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globale beheerders kunnen nu machtigingen op tenantniveau aan zichzelf verlenen](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Twee nieuwe Azure Defender-abonnementen: Azure Defender voor DNS en Azure Defender voor Resource Manager (in preview)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Pagina Nieuwe beveiligingswaarschuwingen in Azure Portal (preview)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Revitalized Security Center-ervaring in Azure SQL Database & SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Tools en filters voor inventarisatie van activa bijgewerkt](#asset-inventory-tools-and-filters-updated)
- [Aanbeveling over web-apps die SSL-certificaten aanvragen die geen deel meer uitmaken van beveiligde score](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [De aanbevelingspagina bevat nieuwe filters voor omgeving, ernst en beschikbare reacties](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Continue export haalt nieuwe gegevenstypen en verbeterde deployifnotexist-beleidsregels op](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender voor SQL-servers op computers is algemeen beschikbaar

Azure Security Center biedt twee Azure Defender-abonnementen voor SQL-servers:

- **Azure Defender voor Azure SQL-databaseservers**: hiermee beveiligt u uw systeemeigen SQL-servers in Azure 
- **Azure Defender voor SQL-servers op computers**: hiermee breidt u dezelfde beveiliging uit naar SQL-servers in hybride, multicloud- en on-premises omgevingen

Voortaan beveiligt **Azure Defender voor SQL** uw databases en de bijbehorende gegevens, waar ze zich ook bevinden.

Azure Defender voor SQL bevat functies voor de evaluatie van beveiligingsproblemen. Het hulpprogramma voor de evaluatie van beveiligingsproblemen biedt de volgende geavanceerde functies:

- **Basislijnconfiguratie** (nieuw!) om de resultaten van het scannen naar beveiligingsproblemen op intelligente wijze te verfijnen, zodat alleen de problemen overblijven die een daadwerkelijk beveiligingsrisico vormen. Nadat u de beveiligingsstatus van uw basislijn hebt ingesteld, worden door het hulpprogramma voor de evaluatie van beveiligingsproblemen alleen afwijkingen van die basislijnstatus gerapporteerd. Resultaten die overeenkomen met de basislijn, worden niet meer meegenomen in vervolgscans. Zo kunnen u en uw analisten zich volledig richten op de belangrijkste problemen.
- **Gedetailleerde benchmarkgegevens** om u *meer inzicht te geven* in de gedetecteerde problemen en in hoe deze gerelateerd zijn aan uw resources.
- **Herstelscripts** om u te helpen bij het oplossen van geïdentificeerde risico's.

Lees meer informatie over [Azure Defender voor SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender voor SQL-ondersteuning voor een toegewezen SQL-pool in Azure Synapse Analytics is algemeen beschikbaar

Azure Synapse Analytics (voorheen SQL DW) is een analyseservice die datawarehousing voor ondernemingen en big data-analyses combineert. In Azure Synapse bieden toegewezen SQL-pools de functionaliteit voor datawarehousing voor ondernemingen. Meer informatie leest u in [Wat is Azure Synapse Analytics (voorheen SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender voor SQL beveiligt uw toegewezen SQL-pools met:

- **Geavanceerde bescherming tegen bedreigingen** om bedreigingen en aanvallen te detecteren 
- **Functionaliteit voor de evaluatie van beveiligingsproblemen** om onjuiste beveiligingsconfiguraties te identificeren en te herstellen

Azure Defender voor SQL-ondersteuning voor SQL-pools in Azure Synapse Analytics wordt automatisch toegevoegd aan de bundel Azure SQL-databases in Azure Security Center. Op de pagina met de Synapse-werkruimte in Azure Portal ziet u een nieuw tabblad Azure Defender voor SQL.

Lees meer informatie over [Azure Defender voor SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Globale beheerders kunnen nu machtigingen op tenantniveau aan zichzelf verlenen

Een gebruiker met de Azure Active Directory-rol **Globale beheerder** kan verantwoordelijkheden hebben voor de hele tenant, maar geen Azure-machtigingen om deze organisatiebrede informatie te bekijken in Azure Security Center. 

Volg de instructies in [Tenantbrede machtigingen toewijzen aan uzelf](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself) om machtigingen op tenantniveau toe te wijzen aan uzelf.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Twee nieuwe Azure Defender-abonnementen: Azure Defender voor DNS en Azure Defender voor Resource Manager (in preview)

We hebben twee nieuwe, cloudeigen beschermingsmogelijkheden tegen bedreigingen toegevoegd voor uw Azure-omgeving.

Deze nieuwe beschermingen verbeteren uw tolerantie tegen aanvullen van bedreigende actoren en vergroten het aantal Azure-resources dat wordt beschermd door Azure Defender.

- **Azure Defender voor Resource Manager** - bewaakt automatisch alle resourcebeheerbewerkingen die in uw organisatie worden uitgevoerd. Zie voor meer informatie:
    - [Inleiding op Azure Defender voor Resource Manager](defender-for-resource-manager-introduction.md)
    - [Reageren op Azure Defender voor Resource Manager-waarschuwingen](defender-for-resource-manager-usage.md)
    - [Lijst met waarschuwingen door Azure Defender voor Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender voor DNS** - bewaakt continu alle DNS-query's van uw Azure-resources. Zie voor meer informatie:
    - [Inleiding tot Azure Defender voor DNS](defender-for-dns-introduction.md)
    - [Reageren op Azure Defender voor DNS-waarschuwingen](defender-for-dns-usage.md)
    - [Lijst met waarschuwingen door Azure Defender voor DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Pagina Nieuwe beveiligingswaarschuwingen in Azure Portal (preview)

De pagina Beveiligingswaarschuwingen van Azure Security Center is opnieuw ontworpen en biedt nu het volgende:

- **Verbeterde sorteerervaring voor waarschuwingen**: hiermee wordt de alertheid op waarschuwingen verbeterd en de focus meer gericht op de meest relevante bedreigingen; de lijst bevat aanpasbare opties voor filters en groeperingen
- **Meer informatie in de lijst met waarschuwingen**, bijvoorbeeld MITRE ATT&ACK-tactieken
- **Knop om voorbeeldwaarschuwingen te maken**: voor het evalueren van de mogelijkheden van Azure Defender en het testen van de configuratie van uw waarschuwingen (voor SIEM-integratie, e-mailmeldingen en werkstroomautomatisering), kunt u voorbeeldwaarschuwingen maken in alle Azure Defender-abonnementen
- **Overeenstemming met de incidentervaring van Azure Sentinel**: klanten die beide producten gebruiken, kunnen deze nu eenvoudiger afwisselen en het ene leren op basis van het andere
- **Betere prestaties** voor lange lijsten met waarschuwingen
- **Toetsenbordnavigatie** via de lijst met waarschuwingen
- **Waarschuwingen van Azure Resource Graph**: u kunt query's uitvoeren op waarschuwingen in Azure Resource Graph, de Kusto-achtige API voor al uw resources. Dit is ook handig als u uw eigen waarschuwingendashboards bouwt. [Meer informatie over Azure Resource Graph](../governance/resource-graph/index.yml).

Als u toegang wilt krijgen tot de nieuwe ervaring, gebruikt u de koppeling Nu proberen in de banner boven aan de pagina met beveiligingswaarschuwingen.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banner met koppeling naar het nieuwe waarschuwingenproces (preview)":::

Zie [Azure Defender-voorbeeldwaarschuwingen genereren](security-center-alert-validation.md#generate-sample-azure-defender-alerts) als u voorbeeldwaarschuwingen wilt maken vanuit het nieuwe waarschuwingenproces.


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Revitalized Security Center-ervaring in Azure SQL Database & SQL Managed Instance 

De Security Center-ervaring in SQL biedt toegang tot de volgende Security Center-functies en Azure Defender voor SQL-functies:

- **Beveiligingsaanbevelingen**: Security Center analyseert periodiek de beveiligingsstatus van alle Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens worden aanbevelingen gedaan om deze beveiligingsproblemen op te lossen en de beveiligingspostuur van de organisatie te verbeteren.
- **Beveiligingswaarschuwingen**: een detectieservice die Azure SQL-activiteiten continu bewaakt als het gaat om bedreigingen (zoals SQL-injectie, brute-force-aanvallen en misbruik van bevoegdheden). Deze service activeert gedetailleerde en actiegerichte beveiligingswaarschuwingen in Security Center en biedt opties voor verder onderzoek met Azure Sentinel, de Azure-native SIEM-oplossing van Microsoft.
- **Bevindingen**: een service voor evaluatie van beveiligingsproblemen die continu Azure SQL-configuraties bewaakt en beveiligingsproblemen helpt op te lossen. Evaluatiescans bieden een overzicht van de Azure SQL-beveiligingsstatussen met gedetailleerde beveiligingsresultaten.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="De beveiligingsfuncties van Azure Security Center voor SQL zijn beschikbaar vanuit Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Tools en filters voor inventarisatie van activa bijgewerkt

De voorraadpagina in Azure Security Center is vernieuwd met de volgende wijzigingen:

- **Hulplijnen en feedback** toegevoegd aan de werkbalk. Hiermee opent u een deelvenster met koppelingen naar gerelateerde informatie en hulpprogramma's. 
- **Het filter abonnementen** is toegevoegd aan de standaardfilters die beschikbaar zijn voor uw resources.
- **Open query**-koppeling om de huidige filteropties te openen als een Azure Resource Graph-query (voorheen 'Weergeven in Resource Graph Explorer' genoemd).
- **Operatoropties** voor elke filter. U kunt nu kiezen uit meer logische Opera tors dan ' = '. U kunt bijvoorbeeld alle resources zoeken met actieve aanbevelingen waarvan de titels de tekenreeks 'versleutelen' bevatten. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Besturingselementen voor de operatoroptie in de filters van assetinventarisatie":::

Voor meer informatie over voorraad, zie [Uw resources verkennen en beheren met assetvoorraad](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Aanbeveling over web-apps die SSL-certificaten aanvragen, maakt geen deel meer uit van beveiligde score

De aanbeveling "Web-apps moeten een SSL-certificaat aanvragen voor alle binnenkomende aanvragen" is verplaatst van het beveiligingsbeheer **Toegang en machtigingen beheren** (maximaal 4 punten waard) naar **De aanbevolen procedures voor beveiliging implementeren** (geen punten waard). 

Het waarborgen van een web-app vraagt een certificaat zeker beter te beveiligen. Voor openbare web-apps is het echter irrelevant. Als u toegang tot uw site hebt via HTTP en niet HTTPS, ontvangt u geen clientcertificaat. Als voor uw toepassing clientcertificaten zijn vereist, moet u dus geen aanvragen voor uw toepassing via HTTP toestaan. Voor meer informatie raadpleegt u [Wederzijdse TLS-verificatie voor Azure App Service configureren](../app-service/app-service-web-configure-tls-mutual-auth.md).

Met deze wijziging is de aanbeveling nu een aanbevolen best practice die niet van invloed is op uw score. 

Voor meer informatie over welke aanbevelingen zich in elk beveiligingsbeheer bevinden, raadpleegt u [Beveiligingscontroles en de bijbehorende aanbevelingen](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>De aanbevelingspagina bevat nieuwe filters voor omgeving, ernst en beschikbare reacties

Azure Security Center bewaakt alle verbonden bronnen en genereert beveiligingsaanbevelingen. Gebruik deze aanbevelingen om uw hybride cloudpostuur te versterken en naleving te volgen van de beleidsregels en standaarden die relevant zijn voor uw organisatie, branche en land.

Naarmate Security Center de dekking en functies blijft uitbreiden, neemt de lijst van beveiligingsaanbevelingen elke maand toe. Zie, bijvoorbeeld, [Er zijn 29 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

In de groeiende lijst moeten de aanbevelingen worden gefilterd om te zoeken naar de meest interessante resultaten. In november hebben we filters toegevoegd aan de aanbevelingspagina (zie [Lijst met aanbevelingen bevat nu filters](#recommendations-list-now-includes-filters)).

De filters die deze maand zijn toegevoegd bieden opties om de lijst met aanbevelingen te verfijnen op basis van:

- **Omgeving**: aanbevelingen weergeven voor uw AWS, GCP of Azure-resources (of een combinatie hiervan)
- **Ernst**: aanbevelingen weergeven op basis van de ernstclassificatie die is ingesteld door Security Center
- **Antwoordacties**: aanbevelingen weergeven op basis van de beschikbaarheid van Security Center-antwoordopties: Snel oplossen, Weigeren en Afdwingen

    > [!TIP]
    > Het filter voor antwoordacties vervangt het filter **Snelle oplossing beschikbaar (Ja/Nee)** . 
    > 
    > Meer informatie over elk van deze antwoordopties:
    > - [Snelle oplossingen voor herstel](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Onjuiste configuraties voorkomen met afdwingen/weigeren](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Aanbevelingen gegroepeerd op beveiligingsbeheer" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Continue export haalt nieuwe gegevenstypen en verbeterde deployifnotexist-beleidsregels op

Met de hulpprogramma's van Azure Security Center voor continue export kunt u de aanbevelingen en waarschuwingen van Security Center exporteren voor gebruik met andere controle hulpprogramma's in uw omgeving.

Met continue export kunt u volledig aanpassen wat waarheen wordt geëxporteerd. Voor meer informatie, zie [Security Center-gegevens continu exporteren](continuous-export.md).

Deze hulpprogramma's zijn verbeterd en uitgebreid op de volgende manieren:

- **Het deployifnotexist-beleid van continue export is verbeterd**. Het beleid is nu:

    - **Controleer of de configuratie is ingeschakeld.** Als dat niet het geval is, wordt het beleid weergegeven als niet-compatibel en wordt er een compatibele resource gemaakt. Meer informatie over de meegeleverde Azure Policy sjablonen vindt u in het tabblad ' implementeren op schaal met Azure Policy ' in [een continue export instellen](continuous-export.md#set-up-a-continuous-export).

    - **Ondersteuning voor het exporteren van beveiligingsresultaten.** Wanneer u de Azure Policy sjablonen gebruikt, kunt u uw continue export configureren om bevindingen op te stellen. Dit is relevant bij het exporteren van aanbevelingen met 'subaanbevelingen' (zoals bevindingen van scanners voor beveiligingsevaluatie) of specifieke systeemupdates voor de 'hoofdaanbeveling': "Systeemupdates moeten op uw computers worden geïnstalleerd".
    
    - **Ondersteuning voor het exporteren van beveiligingsscoregegevens.**

- **Gegevens over reglementaire nalevingsbeoordeling toegevoegd (in preview).** U kunt nu voortdurend updates exporteren naar compliance-evaluaties voor regelgeving, met inbegrip van aangepaste initiatieven, naar een Log Analytics-werkruimte of Event Hub. Deze functie is niet beschikbaar in nationale/onafhankelijke clouds.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="De opties voor het opnemen van informatie van compliance-evaluaties voor regelgeving met uw continue exportgegevens.":::


## <a name="november-2020"></a>November 2020

Updates in november omvatten:

- [Er zijn 29 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 is toegevoegd aan het nalevingsdashboard van de Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Er zijn filters opgenomen in de lijst met aanbevelingen](#recommendations-list-now-includes-filters)
- [De ervaring voor automatische inrichting is verbeterd en uitgebreid](#auto-provisioning-experience-improved-and-expanded)
- [Beveiligingsscore is nu beschikbaar in continue export (preview)](#secure-score-is-now-available-in-continuous-export-preview)
- [Aanbeveling ' systeem updates moeten worden geïnstalleerd op de computers ' bevat nu subaanbevelingen](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Op de pagina Beleidsbeheer in de Azure-portal wordt nu de status van standaardbeleidstoewijzingen weergegeven](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Er zijn 29 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen

Azure Security Bench Mark is de door micro soft ontworpen, Azure specifieke, set richt lijnen voor best practices voor beveiliging en naleving op basis van algemene nalevings kaders. [Meer informatie over Azure Security-benchmark](../security/benchmarks/introduction.md).

De volgende 29 preview-aanbevelingen zijn toegevoegd aan Security Center om de dekking van deze benchmark te verhogen.

Preview-aanbevelingen zorgen er niet voor dat een resource als beschadigd wordt weergegeven en ze worden niet opgenomen in de berekeningen van uw beveiligde score. Herstel ze waar mogelijk, zodat zij wanneer de preview-periode afloopt zullen bijdragen aan uw score. Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

| Beveiligingsmaatregelen                     | Nieuwe aanbevelingen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Actieve gegevens versleutelen              | - SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-databaseservers<br>- SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-databaseservers<br>- TLS moet zijn bijgewerkt naar de nieuwste versie van uw API-app<br>- TLS moet zijn bijgewerkt naar de nieuwste versie van uw functie-app<br>- TLS moet zijn bijgewerkt naar de nieuwste versie van uw web-app<br>- FTPS moet zijn vereist in uw API-app<br>- FTPS moet zijn vereist in uw functie-app<br>- FTPS moet zijn vereist in uw web-app                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Toegang en machtigingen beheren        | - Web-apps moeten een SSL-certificaat aanvragen voor alle inkomende aanvragen<br>- Er moet een beheerde identiteit worden gebruikt in uw API-app<br>- Er moet een beheerde identiteit worden gebruikt in uw functie-app<br>- Er moet een beheerde identiteit worden gebruikt in uw web-app                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Onbevoegde netwerktoegang beperken | - Het privé-eindpunt moet zijn ingeschakeld voor PostgreSQL-servers<br>- Het privé-eindpunt moet zijn ingeschakeld voor MariaDB-servers<br>- Het privé-eindpunt moet zijn ingeschakeld voor MySQL-servers                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Controle en logboekregistratie inschakelen          | - Diagnostische logboeken in App Services moeten zijn ingeschakeld                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Best practices voor beveiliging implementeren    | - Azure Backup moet zijn ingeschakeld voor virtuele machines<br>- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB<br>- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL<br>- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL<br>- PHP moet zijn bijgewerkt naar de nieuwste versie van uw API-app<br>- PHP moet zijn bijgewerkt naar de nieuwste versie van uw web-app<br>- Java moet zijn bijgewerkt naar de nieuwste versie van uw API-app<br>- Java moet zijn bijgewerkt naar de nieuwste versie van uw functie-app<br>- Java moet zijn bijgewerkt naar de nieuwste versie van uw web-app<br>- Python moet zijn bijgewerkt naar de nieuwste versie van uw API-app<br>- Python moet zijn bijgewerkt naar de nieuwste versie van uw functie-app<br>- Python moet zijn bijgewerkt naar de nieuwste versie van uw web-app<br>- Retentie voor controle voor SQL-servers moet zijn ingesteld op minstens 90 dagen |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Gerelateerde links:

- [Meer informatie over Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Meer informatie over Azure API-apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Meer informatie over Azure functie-apps](../azure-functions/functions-overview.md)
- [Meer informatie over Azure web-apps](../app-service/overview.md)
- [Meer informatie over Azure Database for MariaDB](../mariadb/overview.md)
- [Meer informatie over Azure Database for MySQL](../mysql/overview.md)
- [Meer informatie over Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 is toegevoegd aan het nalevingsdashboard van Security Center

De NIST SP 800-171 R2-standaard is nu beschikbaar als ingebouwd initiatief voor gebruik met het nalevingsdashboard voor Azure Security Center. De toewijzingen voor de controles worden beschreven in [Details van het ingebouwde NIST SP 800-171 R2-nalevingsinitiatief](../governance/policy/samples/nist-sp-800-171-r2.md). 

Als u de standaard wilt Toep assen op uw abonnementen en voortdurend uw nalevings status wilt controleren, gebruikt u de instructies in [de set met standaarden aanpassen in uw nalevings dashboard voor regelgeving](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="De NIST SP 800 171 R2-standaard in het nalevingsdashboard van Security Center":::

Zie [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)voor meer informatie over deze nalevingsstandaard.


### <a name="recommendations-list-now-includes-filters"></a>Er zijn filters opgenomen in de lijst met aanbevelingen

U kunt de lijst met aanbevelingen voor de beveiliging nu filteren op verschillende criteria. In het volgende voorbeeld is de lijst met aanbevelingen gefilterd zodat er aanbevelingen worden weergegeven die voldoen aan de volgende criteria:

- zijn **algemeen beschikbaar** (niet in preview)
- Ze zijn bedoeld voor **opslagaccounts**
- Ze bieden ondersteuning voor **snelle oplossingen** voor herstel

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filters voor de lijst met aanbevelingen":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>De ervaring voor automatische inrichting is verbeterd en uitgebreid

Met de functie voor automatische inrichting kunt u de beheeroverhead verminderen door de vereiste extensies op nieuwe (en bestaande) Azure-VM's te installeren, zodat ze gebruik kunnen maken van de beschermingen van Security Center. 

Naarmate Azure Security Center is gegroeid, zijn er meer extensies ontwikkeld en kan Security Center een grotere lijst met resourcetypen bewaken. De hulpprogram ma's voor automatisch inrichten zijn nu uitgebreid ter ondersteuning van andere extensies en resource typen door gebruik te maken van de mogelijkheden van Azure Policy.

U kunt nu automatische inrichting configureren voor:

- Log Analytics-agent
- (Nieuw) Azure Policy-invoegtoepassing voor Kubernetes
- (Nieuw) Microsoft Dependency Agent

Meer informatie vindt u in [Auto provisioning agents and extensions from Azure Security Center](security-center-enable-data-collection.md) (Automatische inrichting van agents en extensies van Azure Security Center).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Beveiligingsscore is nu beschikbaar in continue export (preview)

Met continue export van de beveiligingsscore kunt u wijzigingen in uw score in realtime streamen naar Azure Event Hubs of een Log Analytics-werkruimte. Gebruik deze mogelijkheid om:

- uw beveiligingsscore na verloop van tijd bij te houden met dynamische rapporten
- gegevens van de beveiligingsscore exporteren naar Azure Sentinel (of een andere SIEM)
- deze gegevens integreren met andere processen die u mogelijk al gebruikt om de beveiligingsscore in uw organisatie te bewaken

Meer informatie over hoe u [Security Center-gegevens continue exporteert](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>Aanbeveling ' systeem updates moeten worden geïnstalleerd op de computers ' bevat nu subaanbevelingen

De aanbeveling **Er moeten systeemupdates worden geïnstalleerd op uw computers** is verbeterd. De nieuwe versie bevat subaanbevelingen voor elke ontbrekende update en biedt de volgende verbeteringen:

- Een opnieuw ontworpen ervaring op de Azure Security Center-pagina's van Azure Portal. De pagina met aanbevelingsinformatie voor **Er moeten systeemupdates worden geïnstalleerd op uw computer** bevat de lijst met resultaten zoals hieronder wordt weergegeven. Wanneer u één resultaat selecteert, wordt het deelvenster Details geopend met een koppeling naar de herstelgegevens en een lijst met betrokken resources.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Het openen van een van de aanbevelingen in de portal-ervaring voor de bijgewerkte aanbeveling":::

- Uitgebreide gegevens voor de aanbeveling van Azure Resource Graph (ARG). ARG is een Azure-service die is ontworpen voor efficiëntere resourceverkenning. U kunt ARG gebruiken om op schaal een query uit te voeren in een bepaalde set abonnementen, zodat u uw omgeving effectief kunt beheren. 

    Voor Azure Security Center kunt u gebruikmaken van ARG en de [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) om query's uit te voeren op een breed scala aan postuurgegevens.

    Als u in ARG eerder deze query uitvoerde op deze aanbeveling, was de enige beschikbare informatie dat de aanbeveling moet worden herstel op een machine. De volgende query van de verbeterde versie retourneert elke ontbrekende systeemupdate, gegroepeerd op machine.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Op de pagina Beleidsbeheer in de Azure-portal wordt nu de status van standaardbeleidstoewijzingen weergegeven

U kunt nu zien of het standaard Security Center-beleid is toegewezen aan uw abonnementen op de pagina **Beveiligingsbeleid** van het Security Center in de Azure-portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="De pagina Beleidsbeheer van Azure Security Center met de standaardbeleidstoewijzingen":::

## <a name="october-2020"></a>Oktober 2020

De updates in oktober omvatten:
- [Evaluatie van beveiligingsproblemen voor on-premises en multi-cloudmachines (preview)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall-aanbeveling toegevoegd (preview)](#azure-firewall-recommendation-added-preview)
- [Aanbeveling Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services is bijgewerkt met een snelle oplossing](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Het dashboard Naleving van regelgeving bevat nu een optie voor het verwijderen van standaarden](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Tabel Microsoft.Security/securityStatuses is verwijderd uit Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Evaluatie van beveiligingsproblemen voor on-premises en multi-cloudmachines (preview)

Met [Azure Defender voor servers](defender-for-servers-introduction.md) geïntegreerde evaluatie van beveiligingsproblemen (mogelijk gemaakt door Qualys) worden nu servers met Azure Arc gescand.

Wanneer u Azure Arc op uw niet-Azure-machines hebt ingeschakeld, kan de geïntegreerde scanner voor beveiligingsproblemen handmatig en op schaal worden geïmplementeerd.

Met deze update kunt u de mogelijkheden van **Azure Defender voor servers** gebruiken om uw beheerprogramma voor beveiligingsproblemen te consolideren voor al uw Azure- en niet-Azure-assets.

Belangrijkste functies:

- Bewaken van de inrichtingsstatus van de scanner voor de evaluatie van beveiligingsproblemen van Azure Arc-machines
- Inrichten van de geïntegreerde agent voor de evaluatie van beveiligingsproblemen op niet-beveiligde Azure Arc-machines onder Windows en Linux (handmatig en op schaal)
- Ontvangen en analyseren van gedetecteerde beveiligingsproblemen afkomstig van geïmplementeerde agents (handmatig en op schaal)
- Geïntegreerde ervaring voor Azure-VM's en Azure Arc-machines

[Meer informatie over het implementeren van de geïntegreerde scanner voor beveiligings problemen op uw hybride machines](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Meer informatie over servers met Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall-aanbeveling toegevoegd (preview)

Er is een nieuwe aanbeveling toegevoegd om al uw virtuele netwerken met Azure Firewall te beveiligen.

De aanbeveling, **Virtuele netwerken moeten worden beveiligd met Azure Firewall**, adviseert u om de toegang tot uw virtuele netwerken te beperken en mogelijke dreigingen te voorkomen door gebruik te maken van Azure Firewall.

Meer informatie over [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>De aanbeveling Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services is bijgewerkt met een snelle oplossing

De aanbeveling **Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services** bevat nu een snelle oplossing.

Raadpleeg [Aanbevelingen voor beveiliging: een naslaggids](recommendations-reference.md) voor meer informatie over deze aanbeveling en alle andere aanbevelingen van Security Center.

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Aanbeveling Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services met de snelle oplossing":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Het dashboard Naleving van regelgeving bevat nu een optie voor het verwijderen van standaarden

Het Dashboard Naleving van regelgeving van Security Center biedt inzicht in uw nalevingsstatus op basis van in hoeverre u aan specifieke nalevingsmechanismen en -vereisten voldoet.

Het dashboard bevat een vaste set reglementaire standaarden. Als een van de opgegeven standaarden niet relevant is voor uw organisatie, is het nu een eenvoudig proces om ze te verwijderen uit de gebruikers interface voor een abonnement. Standaarden kunnen alleen worden verwijderd op het niveau van het *abonnement*, niet in het bereik van de beheergroep.

Meer informatie vindt [u in een standaard verwijderen van uw dash board](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabel Microsoft.Security/securityStatuses is verwijderd uit Azure Resource Graph (ARG)

Azure Resource Graph is een service in Azure. Het is ontworpen voor een efficiënte resourceverkenning en biedt de mogelijkheid query's op schaal uit te voeren binnen een bepaalde groep abonnementen, zodat u uw omgeving effectief kunt beheren. 

Voor Azure Security Center kunt u gebruikmaken van ARG en de [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) om query's uit te voeren op een breed scala aan postuurgegevens. Bijvoorbeeld:

- De assetvoorraad maakt gebruik van ARG
- Er is een voorbeeld van een ARG-query gedocumenteerd voor het [identificeren van accounts zonder dat MFA (meervoudige verificatie) is ingeschakeld](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Binnen ARG zijn er tabellen met gegevens die u in uw query's kunt gebruiken.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer en de beschikbare tabellen":::

> [!TIP]
> In de ARG-documentatie vindt u een overzicht van alle beschikbare tabellen in de [Azure Resource Graph-tabel en de resourcetypeverwijzing](../governance/resource-graph/reference/supported-tables-resources.md).

Uit deze update is de tabel **Microsoft.Security/securityStatuses** verwijderd. De securityStatuses-API is nog steeds beschikbaar.

De tabel Microsoft.Security/Assessments kan gebruikmaken van gegevensvervanging.

Het belangrijkste verschil tussen Microsoft.Security/securityStatuses en Microsoft.Security/Assessments is dat de eerste een aggregatie van evaluaties toont en de tweede één record voor elke evaluatie bevat.

Zo retourneert bijvoorbeeld Microsoft.Security/securityStatuses een resultaat met een matrix met twee policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Microsoft.Security/Assessments daarentegen bevat een record voor elk van een dergelijke beleidsevaluatie. Dit werkt als volgt:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Voorbeeld van het converteren van een bestaande ARG-query met behulp van securityStatuses om nu de tabel met evaluaties te kunnen gebruiken:**

Query die verwijst naar SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Vervangingsquery voor de tabel met evaluaties:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Meer informatie vindt u via de volgende koppelingen:
- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Kusto-querytaal (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>September 2020

De updates in september zijn onder meer:
- [Security Center krijgt een nieuwe look.](#security-center-gets-a-new-look)
- [Azure Defender uitgebracht](#azure-defender-released)
- [Azure Defender voor Key Vault is algemeen verkrijgbaar](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender voor opslagbeveiliging voor bestanden en ADLS Gen2 is algemeen beschikbaar](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Hulpprogramma's voor assetinventarisatie zijn nu algemeen beschikbaar](#asset-inventory-tools-are-now-generally-available)
- [Een specifiek gevonden beveiligingsprobleem voor scans van containerregisters en virtuele machines uitschakelen](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Een resource uitsluiten van een aanbeveling](#exempt-a-resource-from-a-recommendation)
- [AWS- en GCP-connectors in Security Center bieden een multi-cloudervaring](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Bundel met aanbevelingen voor Kubernetes-workloadbeveiliging](#kubernetes-workload-protection-recommendation-bundle)
- [Resultaten van evaluatie van beveiligingsproblemen zijn nu beschikbaar in continue export](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Onjuiste beveiligingsconfiguraties voorkomen door aanbevelingen af te dwingen bij het maken van nieuwe resources](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Aanbevelingen voor netwerkbeveiligingsgroep verbeterd](#network-security-group-recommendations-improved)
- [Aanbeveling AKS-preview 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' afgeschaft'](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-mailmeldingen van Azure Security Center verbeterd](#email-notifications-from-azure-security-center-improved)
- [Beveiligingsscore bevat geen preview-aanbevelingen](#secure-score-doesnt-include-preview-recommendations)
- [Aanbevelingen bevatten nu een ernst-indicator en vernieuwingsinterval](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center krijgt een nieuwe look.

We hebben een vernieuwde gebruikersinterface voor de portalpagina's van Security Center uitgebracht. De nieuwe pagina's bevatten een nieuwe overzichts pagina en dash boards voor een veilige Score, inventarisatie van de activa en Azure Defender.

De opnieuw ontworpen overzichtspagina heeft nu een tegel voor toegang tot de dashboards voor de beveiligingsscore, assetinventarisatie en Azure Defender. Het bevat ook een tegel met een koppeling naar het dashboard voor naleving van regelgeving.

Meer informatie over de [overzichtspagina](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender uitgebracht

**Azure Defender** is het Cloud Workload Protection Platform (CWPP) dat is geïntegreerd in Security Center voor geavanceerde, intelligente beveiliging van uw Azure- en hybride workloads. Het vervangt de standaardprijscategorie van Security Center. 

Als u Azure Defender inschakelt in het gedeelte **Prijzen en instellingen** van Azure Security Center, worden de volgende Defender-abonnementen allemaal tegelijkertijd ingeschakeld en kunt u gebruikmaken van een totaaloplossing voor de bescherming van de berekenings-, gegevens- en servicelagen in uw omgeving:

- [Azure Defender voor servers](defender-for-servers-introduction.md)
- [Azure Defender voor App Service](defender-for-app-service-introduction.md)
- [Azure Defender voor Storage](defender-for-storage-introduction.md)
- [Azure Defender voor SQL](defender-for-sql-introduction.md)
- [Azure Defender voor Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender voor containerregisters](defender-for-container-registries-introduction.md)

Elk van deze abonnementen wordt afzonderlijk beschreven in de documentatie voor Security Center.

Met zijn speciale dashboard biedt Azure Defender beveiligingswaarschuwingen en geavanceerde bescherming tegen dreigingen voor virtuele machines, SQL databases, containers, webtoepassingen, uw netwerk en meer.

[Meer informatie over Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender voor Key Vault is algemeen verkrijgbaar

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. 

**Azure Defender voor Key Vault** biedt voor Azure systeemeigen, geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault, waarmee u een extra laag beveiligingsinformatie kunt leveren. Als uitbreiding biedt Azure Defender voor Key Vault consequent bescherming aan veel van de resources die afhankelijk zijn van uw Key Vault-accounts.

Het optionele abonnement is nu algemeen beschikbaar. Deze functie was in preview als 'geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault'.

Daarnaast bevatten de Key Vault-pagina's in Azure Portal nu een speciale pagina **Beveiliging** voor aanbevelingen en waarschuwingen van **Security Center**.

Meer informatie vindt u in [Azure Defender voor Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender voor opslagbeveiliging voor bestanden en ADLS Gen2 is algemeen beschikbaar 

**Azure Defender for Storage** detecteert potentieel schadelijke activiteiten in uw Azure Storage-accounts. Uw gegevens kunnen worden beschermd, ongeacht of deze zijn opgeslagen als blobcontainers, bestandsshares of data lakes.

Ondersteuning voor [Azure Files](../storage/files/storage-files-introduction.md) en [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) is nu algemeen beschikbaar.

Van 1 oktober 2020 beginnen we met de beveiliging van resources op deze services.

Meer informatie vindt u in [Azure Defender voor Storage](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Hulpprogramma's voor assetinventarisatie zijn nu algemeen beschikbaar

De pagina voor assetinventarisatie van Azure Security Center biedt één pagina voor het weergeven van het beveiligingspostuur van de resources die u hebt verbonden met Security Center.

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligingsproblemen.

Wanneer een resource openstaande aanbevelingen heeft, worden deze weergegeven in de inventarisatie.

Voor meer informatie raadpleegt u [Uw resources verkennen en beheren met assetvoorraad](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Een specifiek gevonden beveiligingsprobleem voor scans van containerregisters en virtuele machines uitschakelen

Azure Defender bevat scanners voor beveiligingsproblemen om installatiekopieën in uw Azure Container Registry en uw virtuele machines te scannen.

Als u een organisatorische behoefte hebt om een resultaat te negeren in plaats van dit te herstellen, kunt u het eventueel uitschakelen. Uitgeschakelde resultaten hebben geen invloed op uw beveiligingsscore en genereren geen ongewenste ruis.

Wanneer een resultaat overeenkomt met de criteria die u hebt gedefinieerd in de regels voor uitschakelen, wordt dit niet weergegeven in de lijst met resultaten.

Deze optie is beschikbaar op de pagina met aanbevelingsdetails voor:

- **Beveiligingsproblemen met installatiekopieën in Azure Container Registry moeten worden hersteld**
- **Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**

Meer informatie vindt u in [Specifieke resultaten voor uw containerinstallatiekopieën uitschakelen](defender-for-container-registries-usage.md#disable-specific-findings-preview) en [Specifieke resultaten voor uw virtuele machines uitschakelen](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Een resource uitsluiten van een aanbeveling

In sommige gevallen wordt een resource vermeld als niet in orde met betrekking tot een specifieke aanbeveling (en waardoor uw beveiligingsscore lager wordt), zelfs als u denkt dat dit niet zo zou moeten zijn. Dit kan zijn opgelost door een proces dat niet wordt bijgehouden door Security Center. Of misschien heeft uw organisatie besloten het risico voor die specifieke resource te accepteren. 

In dergelijke gevallen kunt u een uitzonderingsregel maken en ervoor zorgen dat de resource in de toekomst niet wordt vermeld als een resource die niet in orde is. Deze regels kunnen gedocumenteerde redenen bevatten, zoals hieronder wordt beschreven.

Meer informatie vindt u in [Een resource uitsluiten van aanbevelingen en de beveiligingsscore](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS- en GCP-connectors in Security Center bieden een multi-cloudervaring

Veel workloads in de cloud beslaan meerdere cloudplatforms, dus moeten cloudbeveiligingsservices hetzelfde doen.

Azure Security Center biedt nu bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

Met de onboarding van uw AWS- en GCP-accounts in Security Center worden AWS Security Hub, GCP Security Command en Azure Security Center geïntegreerd. 

Meer informatie vindt u in [Uw AWS-accounts verbinden met Azure Security Center](quickstart-onboard-aws.md) en [Uw GCP-accounts verbinden met Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Bundel met aanbevelingen voor Kubernetes-workloadbeveiliging

Om ervoor te zorgen dat Kubernetes-workloads standaard veilig zijn, voegt Security Center aanbevelingen voor beveiliging op Kubernetes-niveau toe, met inbegrip van afdwingingsopties met Kubernetes Admission Control.

Wanneer u de Azure Policy-invoegtoepassing voor Kubernetes op uw AKS-cluster hebt geïnstalleerd, wordt elke aanvraag voor de Kubernetes API-server gecontroleerd op basis van de vooraf gedefinieerde set aanbevolen procedures voordat deze naar het cluster gaat. Vervolgens kunt u instellingen configureren om de aanbevolen procedures af te dwingen en ze te verplichten voor toekomstige workloads.

U kunt er bijvoorbeeld voor zorgen dat containers met machtigingen niet moeten worden gemaakt, en toekomstige aanvragen hiervoor worden dan geblokkeerd.

Meer informatie vindt u in [Aanbevolen procedures voor workloadbeveiliging met behulp van Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Resultaten van evaluatie van beveiligingsproblemen zijn nu beschikbaar in continue export

Maak gebruik van continue export om uw waarschuwingen en aanbevelingen in realtime te streamen naar Azure Event Hubs, Log Analytics-werkruimten of Azure Monitor. Daar kunt u deze gegevens integreren met SIEM's (zoals Azure Sentinel, Power BI, Azure Data Explorer en meer).

De hulpprogramma's voor evaluatie van beveiligingsproblemen van Security Center retourneren informatie over uw resources als aanbevelingen voor het uitvoeren van acties in een 'hoofdaanbeveling', zoals 'beveiligingsproblemen op uw virtuele machines, moeten worden hersteld'. 

De beveiligingsresultaten zijn nu beschikbaar voor export door middel van continue export wanneer u aanbevelingen selecteert en de optie **Beveiligingsresultaten insluiten** in te schakelen.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="De schakeloptie Beveiligingsresultaten insluiten in de configuratie voor continue export" :::

Gerelateerde pagina's:

- [De geïntegreerde oplossing van Security Center voor de evaluatie van beveiligingsproblemen met virtuele Azure-machines](deploy-vulnerability-assessment-vm.md)
- [De geïntegreerde oplossing van Security Center voor de evaluatie van beveiligingsproblemen met Azure Container Registry](defender-for-container-registries-usage.md)
- [Continue export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Onjuiste beveiligingsconfiguraties voorkomen door aanbevelingen af te dwingen bij het maken van nieuwe resources

Onjuiste beveiligingsconfiguraties zijn een belangrijke oorzaak van beveiligingsincidenten. Security Center biedt nu de mogelijkheid om onjuiste configuratie van nieuwe resources met betrekking tot specifieke aanbevelingen te helpen *voorkomen*. 

Deze functie kan u helpen uw workloads veilig te houden en uw beveiligingsscore stabiel te houden.

Het afdwingen van een veilige configuratie op basis van een specifieke aanbeveling wordt aangeboden in twee modi:

- Met behulp van de optie **Weigeren** van Azure Policy, kunt u het maken van resources die niet in orde zijn, stoppen

- Met de optie **Afdwingen** kunt u profiteren van het effect **DeployIfNotExist** van Azure Policy en niet-compatibele resources automatisch herstellen wanneer deze worden gemaakt
 
Dit is beschikbaar voor geselecteerde beveiligingsaanbevelingen en is bovenaan de pagina met resourcedetails te vinden.

Meer informatie vindt u in [Onjuiste configuraties voorkomen met de aanbevelingen voor afdwingen/weigeren](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Aanbevelingen voor netwerkbeveiligingsgroep verbeterd

De volgende beveiligingsaanbevelingen met betrekking tot netwerkbeveiligingsgroepen zijn verbeterd om enkele gevallen van fout-positieven te verminderen.

- Alle netwerkpoorten moeten worden beperkt voor de netwerkbeveiligingsgroep die is gekoppeld aan uw VM
- Beheerpoorten moeten gesloten zijn op uw virtuele machines
- Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen
- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Aanbeveling AKS-preview 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' afgeschaft

De preview-aanbeveling 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' wordt afgeschaft zoals beschreven in de documentatie van [Azure Kubernetes Service](../aks/use-pod-security-policies.md).

De functie beveiligings beleid voor pod (preview) is ingesteld voor afschaffing en is na 15 oktober 2020 niet langer beschikbaar voor Azure Policy voor AKS.

Nadat de functie voor beveiligingsbeleid voor pods (preview) is afgeschaft, moet u de functie op alle bestaande clusters uitschakelen met behulp van de afgeschafte functie om toekomstige clusterupgrades uit te voeren en de ondersteuning van Azure te kunnen blijven gebruiken.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-mailmeldingen van Azure Security Center verbeterd

E-mailmeldingen met betrekking tot beveiligingswaarschuwingen zijn als volgt verbeterd: 

- De mogelijkheid voor het verzenden van e-mailmeldingen over waarschuwingen voor alle ernstniveaus is toegevoegd
- De mogelijkheid om gebruikers op de hoogte te stellen van verschillende Azure-rollen in het abonnement is toegevoegd
- We informeren abonnementseigenaren standaard proactief over waarschuwingen met een hoge urgentie (die een hoge waarschijnlijkheid hebben een echte inbreuk te zijn)
- Het telefoonnummerveld is verwijderd van de configuratiepagina voor e-mailmeldingen

Meer informatie vindt u in [E-mailmeldingen instellen voor beveiligingswaarschuwingen](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Beveiligingsscore bevat geen preview-aanbevelingen 

Security Center controleert uw resources, abonnementen en organisatie doorlopend op beveiligingsproblemen. Vervolgens worden alle bevindingen tot één enkele score samengevoegd, zodat u in een oogopslag uw huidige beveiligingssituatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risiconiveau is.

Als er nieuwe bedreigingen worden gedetecteerd, wordt nieuw beveiligingsadvies beschikbaar gesteld in Security Center via nieuwe aanbevelingen. Om te voorkomen dat uw beveiligingsscore plotseling verandert, en om een respijtperiode te bieden waarin u nieuwe aanbevelingen kunt verkennen voordat ze van invloed zijn op uw scores, worden de aanbevelingen die zijn gemarkeerd als **Preview** niet meer opgenomen in de berekeningen van uw beveiligingsscore. Ze moeten, waar mogelijk, nog steeds worden hersteld. Wanneer de preview-periode is afgelopen, worden ze dus meegerekend in uw score.

Daarnaast worden in **Preview**-aanbevelingen resources niet als 'Niet in orde' weergegeven.

Een voorbeeld van een preview-aanbeveling:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Aanbeveling met de preview-markering":::

[Meer informatie over de beveiligingsscore](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Aanbevelingen bevatten nu een ernst-indicator en vernieuwingsinterval

De detailpagina voor aanbevelingen bevat nu een indicator voor het vernieuwingsinterval (indien relevant) en een duidelijke weergave van de ernst van de aanbeveling.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Aanbevelingspagina met vernieuwingsinterval en ernst":::