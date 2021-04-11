---
title: Azure Security Center gratis VS Azure Defender ingeschakeld
description: Meer informatie over de voor delen van het inschakelen van Azure Defender voor Cloud beveiliging in Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/23/2021
ms.openlocfilehash: 1825f5be8a4f8a8ddfba931dfbc7e77186b4331f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889447"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center gratis VS Azure Defender ingeschakeld
Azure Defender is gratis gedurende de eerste 30 dagen. Als u aan het einde van 30 dagen wilt door gaan met het gebruik van de service, worden er automatisch kosten voor gebruik in rekening gebracht.

U kunt een upgrade uitvoeren op de pagina met **prijzen &-instellingen** , zoals beschreven in [Quick Start: Azure Defender inschakelen](enable-azure-defender.md). Zie [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor prijs informatie in de valuta van uw keuze en volgens uw regio.

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Wat zijn de voor delen van het inschakelen van Azure Defender?

Security Center wordt op twee manieren aangeboden:

- **Azure Defender UIT** (gratis). Security Center zonder Azure Defender is gratis ingeschakeld voor al uw huidige Azure-abonnementen wanneer u het Azure Security Center-dashboard voor het eerst bezoekt in de Azure-portal of als u programmatisch via API hebt ingeschakeld. Deze gratis modus biedt beveiligingsbeleid, continue beveiligingsevaluatie en praktische beveiligingsaanbevelingen voor het beveiligen van uw Azure-resources.

- **Azure Defender AAN** - Als u Azure Defender inschakelt, breidt dat de mogelijkheden van de gratis versie uit voor workloads die worden uitgevoerd in privé- en andere openbare clouds, met geïntegreerd beveiligingsbeheer en beveiliging tegen bedreigingen voor uw hybride cloudworkloads. Enkele belangrijke functies van Azure Defender:

    - **Microsoft Defender for Endpoint** - Azure Defender voor servers bevat [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) voor uitgebreide eindpuntdetectie en -respons (EDR). Lees meer over de voordelen van het gebruik van Microsoft Defender for Endpoint met Azure Defender in [De geïntegreerde EDR-oplossing van Security Center gebruiken](security-center-wdatp.md).
    - **Beveiligingsproblemen voor virtuele machines en containerregisters**: implementeer eenvoudig een scanner op al uw virtuele machines met de meest geavanceerde oplossing voor het beheer van beveiligingsproblemen. Bekijk onderzoek en herstel de bevinden rechtstreeks binnen Security Center. 
    - **Hybride beveiliging**: krijg een uniforme weergave van beveiliging in al uw on-premises en cloudwerkbelastingen. Pas beveiligingsbeleid voor uw hybride-cloudworkloads toe en beoordeel de beveiliging van uw ervan om de naleving van beveiligingsstandaarden te waarborgen. Verzamel, zoek en analyseer beveiligingsgegevens van meerdere bronnen, waaronder firewalls en oplossingen van andere partners.
    - **Bedreigingswaarschuwingen**: geavanceerde gedragsanalyses en de Microsoft Intelligent Security Graph bieden een voordeel tegen ontwikkelende cyberaanvallen. Met gedragsanalyses en machine learning kunnen aanvallen en zero-day-aanvallen worden geïdentificeerd. Bewaak netwerken, machines en cloudservices voor inkomende aanvallen en activiteiten na inbreuk. Stroomlijn onderzoek met interactieve hulpprogramma's en bedreigingsinformatie in context.
    - **Toegang- en toepassingsbeheer** (AAC): blokkeer malware en andere ongewenste toepassingen door machine learning op basis van aanbevelingen toe te passen die is toegespitst op uw specifieke workloads om goedkeurings- en weigeringslijsten te maken. Verklein het oppervlak voor netwerkaanvallen met beheerde JIT-toegang tot beheerpoorten op Azure-VM's. AAC vermindert blootstelling aan brute force-beveiligingsaanvallen en andere netwerkaanvallen.
    - **Beveiligingsfuncties voor containers**: profiteer van beiligingsprobleembeheer en realtime bedreigingsbescherming in uw containeromgevingen. Wanneer u **Azure Defender voor containerregisters** inschakelt, kan het tot 12 uur duren voordat alle functies zijn ingeschakeld. Kosten zijn gebaseerd op het aantal unieke containerinstallatiekopieën dat naar uw gekoppelde register wordt gepusht. Nadat een installatiekopie is gescand, worden er geen kosten meer in rekening voor gebracht, tenzij deze wordt bewerkt en nogmaals wordt gepusht.
    - **Omvangrijke bescherming tegen dreigingen voor resources die verbonden zijn met de Azure-omgeving**: Azure Defender omvat omvangrijke bescherming van Azure tegen dreigingen voor de Azure-services die op al uw resources aanwezig zijn: Azure Resource Manager, Azure DNS, Azure-netwerklaag en Azure Key Vault. Azure Defender kent unieke zichtbaarheid in de Azure-beheerlaag en de Azure DNS-laag en kan dus cloudresources beveiligen die met deze lagen zijn verbonden.


## <a name="faq---pricing-and-billing"></a>Veelgestelde vragen - Prijzen en facturering 

- [Hoe kan ik bijhouden wie in mijn organisatie Azure Defender-wijzigingen in Security Center heeft ingeschakeld?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Welke abonnementen worden aangeboden in Security Center?](#what-are-the-plans-offered-by-security-center)
- [Hoe schakel ik Azure Defender in voor mijn abonnement?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Kan ik Azure Defender voor servers inschakelen op een subset servers in mijn abonnement?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Als ik al een licentie heb voor Microsoft Defender for Endpoint, kan ik dan korting krijgen voor Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Azure Defender voor servers is ingeschakeld in mijn abonnement. Moet ik betalen voor niet-actieve servers?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Worden er kosten in rekening gebracht voor machines waarop de Log Analytics-agent is geïnstalleerd?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Als een Log Analytics-agent aan meerdere werkruimten rapporteert, worden er dan tweemaal kosten in rekening gebracht?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Als een Log Analytics-agent aan meerdere werkruimten rapporteert, is er dan voor elke werkruimte gratis gegevensopname van 500 MB beschikbaar?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Wordt de gratis gegevensopname van 500 MB berekend voor een gehele werkruimte of uitsluitend per machine?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Welke gegevens typen worden er voor de dagelijkse limiet van 500 MB gebruikt?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Hoe kan ik bijhouden wie in mijn organisatie Azure Defender-wijzigingen in Security Center heeft ingeschakeld?
Azure-abonnementen kunnen meerdere beheerders met machtigingen voor het wijzigen van prijsinstellingen hebben. Als u wilt weten welke gebruiker een wijziging heeft aangebracht, gebruikt u het Azure-activiteitenlogboek.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Azure-activiteitenlogboek met een prijswijzigingsgebeurtenis":::

Als de gegevens van de gebruiker niet worden weergegeven in de kolom **Gebeurtenis gestart door**, zoekt u de relevante gegevens in de JSON van de gebeurtenis.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Verkenner met JSON voor Azure-activiteitenlogboek":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Welke abonnementen worden aangeboden in Security Center? 
Security Center heeft twee aanbiedingen: 

- De gratis versie van Azure Security Center 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Hoe schakel ik Azure Defender in voor mijn abonnement? 
U kunt Azure Defender voor uw abonnement op een van de volgende manieren inschakelen: 

| Methode                                          | Instructies                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Security Center-pagina's van Azure Portal | [Azure Defender inschakelen](enable-azure-defender.md)                                                                                                  |
| REST-API                                        | [Pricings-API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Bundle Pricings](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Kan ik Azure Defender voor servers inschakelen op een subset servers in mijn abonnement?
Nee. Als u [Azure Defender voor servers](defender-for-servers-introduction.md) inschakelt in een abonnement, vallen alle servers in het abonnement vervolgens onder Azure Defender. 

U kunt Azure Defender voor servers ook inschakelen op het niveau van de Log Analytics-werkruimte. Als u dit doet, worden alleen servers die aan die werkruimte rapporteren, beveiligd en gefactureerd. Een aantal mogelijkheden is dan echter niet beschikbaar. Hiertoe behoren onder meer Just-in-Time-VM-toegang, netwerkdetectie, naleving van regelgeving, adaptieve netwerkbeveiliging en adaptieve toepassingsregelaars. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Als ik al een licentie heb voor Microsoft Defender for Endpoint, kan ik dan korting krijgen voor Azure Defender?
Als u al een licentie hebt voor Microsoft Defender for Endpoint, hoeft u niet te betalen voor dat deel van uw Azure Defender-licentie.

Neem contact op met het ondersteuningsteam van Security Center en geef de relevante werkruimte-ID, regio en licentiegegevens van elke relevante licentie op om uw korting te bevestigen.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Azure Defender voor servers is ingeschakeld in mijn abonnement. Moet ik betalen voor niet-actieve servers? 
Nee. Wanneer u [Azure Defender voor servers](defender-for-servers-introduction.md) in een abonnement inschakelt, worden er geen kosten in rekening gebracht voor machines die de vrijgekomen energie status hebben wanneer deze zich in die staat bevinden. Computers worden gefactureerd op basis van hun energie status, zoals wordt weer gegeven in de volgende tabel:

| Staat        | Beschrijving                                                                                                                                      | Gebruik van exemplaar gefactureerd |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Starten     | De VM wordt opgestart.                                                                                                                               | Niet gefactureerd            |
| Wordt uitgevoerd      | Normale werk status voor een virtuele machine                                                                                                                    | Gefactureerd                |
| Stoppen     | Dit is een overgangs status. Als deze functie is voltooid, wordt deze weer gegeven als gestopt.                                                                           | Gefactureerd                |
| Gestopt      | De virtuele machine is afgesloten vanuit het gast besturingssysteem of met behulp van de uitgeschakeld-Api's. Er wordt nog steeds hardware toegewezen aan de virtuele machine en deze blijft op de host. | Gefactureerd                |
| Vrijgeven | Overgangs status. Als de VM is voltooid, wordt deze weer gegeven als opgeheven.                                                                             | Niet gefactureerd            |
| Toewijzing ongedaan gemaakt  | De virtuele machine is gestopt en verwijderd van de host.                                                                                  | Niet gefactureerd            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Azure Virtual Machines het weer geven van een ontoegewezen machine":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Worden er kosten in rekening gebracht voor machines waarop de Log Analytics-agent is geïnstalleerd?
Ja. Wanneer u [Azure Defender voor servers](defender-for-servers-introduction.md) in een abonnement inschakelt, worden de machines in dat abonnement op diverse manieren beveiligd, zelfs als u de Log Analytics-agent niet hebt geïnstalleerd.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Als een Log Analytics-agent aan meerdere werkruimten rapporteert, worden er dan tweemaal kosten in rekening gebracht? 
Ja. Als uw Log Analytics-agent is geconfigureerd voor het verzenden van gegevens naar twee of meer verschillende Log Analytics-werkruimten (multi-homing), worden er kosten in rekening gebracht voor elke werkruimte waarop Security of AntiMalware is geïnstalleerd. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Als een Log Analytics-agent aan meerdere werkruimten rapporteert, is er dan voor elke werkruimte gratis gegevensopname van 500 MB beschikbaar?
Ja. Als u de Log Analytics-agent hebt geconfigureerd voor het verzenden van gegevens naar twee of meer verschillende Log Analytics-werkruimten (multi-homing), ontvangt u 500 MB gratis gegevensopname. Dit wordt berekend per knooppunt, per gerapporteerde werkruimte, per dag en is beschikbaar voor elke werkruimte waarop de oplossing Security of AntiMalware is geïnstalleerd. Er worden kosten in rekening gebracht voor gegevensopname van meer dan 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Wordt de gratis gegevensopname van 500 MB berekend voor een gehele werkruimte of uitsluitend per machine?
U krijgt 500 MB gratis gegevensopname per dag, voor elke machine die is verbonden met de werkruimte. Dit geldt met name voor beveiligingsgegevenstypen die rechtstreeks door Azure Security Center worden verzameld.

Deze hoeveelheid gegevens is een dagelijks gemiddelde voor alle knooppunten. Er worden dus geen extra kosten in rekening gebracht als sommige machines 100 MB en andere 800 MB verzenden, mits het totaal de gratis limiet van **[aantal machines] x 500 MB** niet overschrijdt.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Welke gegevens typen worden er voor de dagelijkse limiet van 500 MB gebruikt?

De facturering van Security Center is nauw verbonden met de facturering voor Log Analytics. Security Center biedt een toewijzing van 500 MB/knoop punt/dag voor de volgende subset van [beveiligings gegevens typen](/azure/azure-monitor/reference/tables/tables-category.md#security):
- WindowsEvent
- SecurityAlert
- Security Baseline Baseline
- Summary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- LinuxAuditLog
- SysmonEvent
- ProtectionStatus
- Gegevens typen bijwerken en update Summary wanneer de Updatebeheer-oplossing niet wordt uitgevoerd op de werk ruimte of de doel groep van de oplossing is ingeschakeld

Als de werk ruimte in de prijs categorie verouderd per knoop punt staat, worden de Security Center-en Log Analytics toewijzingen gecombineerd en gezamenlijk toegepast op alle factureer bare opgenomen gegevens.

## <a name="next-steps"></a>Volgende stappen
In dit artikel worden de prijsopties voor Security Center beschreven. Zie voor gerelateerd materiaal:

- [De kosten van uw Azure-werkbelasting optimaliseren](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Prijsinformatie in de gewenste valuta en op basis van uw regio](https://azure.microsoft.com/pricing/details/security-center/)
- Mogelijk wilt u uw kosten beheren en het aantal verzamelde gegevens voor oplossing beperken door deze te beperken tot een bepaalde set agents. Met [gerichte oplossingen](../azure-monitor/insights/solution-targeting.md) kunt u een bereik instellen voor de oplossing en u richten op een subset computers in de werkruimte. Als u gerichte oplossingen gebruikt, vermeldt Security Center dat de werkruimte geen oplossing heeft.