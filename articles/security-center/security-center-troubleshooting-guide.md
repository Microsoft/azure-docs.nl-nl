---
title: Handleiding voor het oplossen van problemen met Azure Security Center | Microsoft Docs
description: Deze hand leiding is voor IT-professionals, beveiligings analisten en Cloud beheerders die Azure Security Center gerelateerde problemen moeten oplossen.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: e49d8bcc55036b21f53720aab17be02edb114d4b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100250"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Handleiding voor het oplossen van problemen met Azure Security Center

Deze handleiding is bedoeld voor IT-specialisten, informatiebeveiligingsanalisten en cloudbeheerders van organisaties die Azure Security Center gebruiken en biedt procedures voor het oplossen van problemen met Azure Security Center.

Security Center gebruikt de Log Analytics agent om gegevens te verzamelen en op te slaan. Zie [Migratie van Azure Security Center-platform](./security-center-enable-data-collection.md) voor meer informatie. De informatie in dit artikel bevat Security Center functionaliteit na de overgang naar de Log Analytics-agent.

## <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

In deze handleiding wordt uitgelegd hoe u problemen oplost die betrekking hebben op Security Center.

Waarschuwings typen:

* VMBA (Virtual Machine Behavioral Analysis)
* Netwerkanalyse
* Analyse van SQL Database en Azure Synapse Analytics
* Contextuele informatie

Afhankelijk van de soorten waarschuwingen, kunnen klanten met behulp van de volgende resources de benodigde gegevens verzamelen om de waarschuwing te onderzoeken:

* Beveiligingslogboeken in de functie Logboeken van de virtuele machine (VM) in Windows
* AuditD in Linux
* De Azure-activiteitenlogboeken en de logboeken over het inschakelen van diagnostische gegevens voor de aangevallen resource.

Klanten kunnen feedback delen voor waarschuwingsbeschrijving en relevantie. Navigeer naar de waarschuwing zelf, selecteer de knop **Was dit nuttig**, selecteer de reden en voer een opmerking in om de feedback uit te leggen. We houden dit feedbackkanaal constant in de gaten om onze waarschuwingen te verbeteren.

## <a name="audit-log"></a>Auditlogboek

In de meeste gevallen vindt probleemoplossing in Security Center plaats door eerst de records in het [Controlelogboek](../azure-monitor/essentials/platform-logs-overview.md) van het onderdeel met de fout te raadplegen. Met auditlogboeken kunt u het volgende bepalen:

* Welke bewerkingen er hebben plaatsgevonden
* Wie de bewerking heeft gestart
* Wanneer de bewerking is uitgevoerd
* De status van de bewerking
* De waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van het probleem

In het controlelogboek staan alle schrijfbewerkingen (PUT, POST, DELETE) die op uw resources zijn uitgevoerd, maar er staan geen leesbewerkingen (GET) in.

## <a name="log-analytics-agent"></a>Log Analytics-agent

Security Center maakt gebruik van de Log Analytics agent – dit is dezelfde agent die wordt gebruikt door de Azure Monitor-service – om beveiligings gegevens van uw Azure virtual machines te verzamelen. Nadat het verzamelen van gegevens is ingeschakeld en de agent juist is geïnstalleerd op de doelcomputer, wordt als het goed is dit proces uitgevoerd:

* HealthService.exe

Als u de Services Management Console (Services. msc) opent, ziet u ook de Log Analytics Agent-service die wordt uitgevoerd, zoals hieronder wordt weer gegeven:

![Services](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Als u wilt zien welke versie van de agent u hebt, opent u **taak beheer**, gaat u naar het tabblad **processen** en gaat u naar de **log Analytics Agent-service**, klikt u erop met de rechter muisknop en klikt u op **Eigenschappen**. Op het tabblad **Details** kunt u de bestandsversie vinden, zoals hieronder wordt weergegeven:

![File](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Installatie scenario's voor Log Analytics agent

Er zijn twee installatie scenario's die verschillende resultaten kunnen opleveren bij de installatie van de Log Analytics-agent op uw computer. Dit zijn de ondersteunde scenario's:

* **Agent automatisch geïnstalleerd door Security Center**: in dit scenario kunt u de waarschuwingen op beide locaties bekijken, in Security Center en door te zoeken in logboeken. U ontvangt e-mail meldingen voor het e-mail adres dat is geconfigureerd in het beveiligings beleid voor het abonnement waartoe de resource behoort.

* **Agent hand matig geïnstalleerd op een virtuele machine die zich in azure bevindt**: in dit scenario kunt u de waarschuwingen in de portal van Security Center alleen bekijken als u agents hebt gedownload en 2017 geïnstalleerd, alleen als u filtert op het abonnement waartoe de werk ruimte behoort. Als u filtert op het abonnement waartoe de resource behoort, worden er geen waarschuwingen weer geven. U ontvangt e-mail meldingen voor het e-mail adres dat is geconfigureerd in het beveiligings beleid voor het abonnement waarvan de werk ruimte deel uitmaakt.

> [!NOTE]
> U kunt het gedrag uit het tweede scenario voorkomen door de nieuwste versie van de agent te downloaden.

## <a name="monitoring-agent-health-issues"></a>Problemen met de agentstatus controleren <a name="mon-agent"></a>

**Bewakingsstatus** toont de reden waarom Security Center VM's en computers die zijn geïnitialiseerd voor automatische inrichting niet afdoende kan bewaken. In de volgende tabel ziet u de waarden, beschrijvingen en oplossingen voor **Bewakingsstatus**.

| Bewakingsstatus | Beschrijving | Stappen om het probleem op te lossen |
|---|---|---|
| Agent wacht op installatie | De installatie van de Log Analytics-agent is nog actief.  De installatie kan enkele uren duren. | Wacht totdat de automatische installatie is voltooid. |
| Energiestatus uitgeschakeld | De virtuele machine is gestopt.  De Log Analytics-agent kan alleen worden geïnstalleerd op een virtuele machine waarop wordt uitgevoerd. | Start de VM opnieuw op. |
| Azure VM-agent ontbreekt of is ongeldig | De Log Analytics-agent is nog niet geïnstalleerd.  Security Center installeert de extensie pas als er een geldige Azure VM-agent is. | Installeer de Azure VM-agent (opnieuw) op de virtuele machine of voer een upgrade uit. |
| VM-status niet gereed voor installatie  | De Log Analytics-agent is nog niet geïnstalleerd, omdat de VM niet gereed is voor installatie. De virtuele machine is niet gereed voor installatie vanwege een probleem met de VM-agent of de VM-inrichting. | Controleer de status van uw virtuele machine. Ga terug naar **Virtuele machines** in de portal en selecteer de virtuele machine voor statusinformatie. |
|Installatie is mislukt: algemene fout | De Log Analytics-agent is geïnstalleerd, maar is mislukt vanwege een fout. | [Installeer de extensie handmatig](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) of verwijder de extensie zodat Security Center deze opnieuw probeert te installeren. |
| Installatie is mislukt: lokale agent is al geïnstalleerd | De installatie van Log Analytics agent is mislukt. Security Center heeft vastgesteld dat er al een lokale agent (Log Analytics of System Center Operations Manager) is geïnstalleerd op de VM. Om te voor komen dat de multi-multihoming-configuratie, waarbij de VM wordt gerapporteerd aan twee afzonderlijke werk ruimten, de installatie van de Log Analytics-agent is gestopt. | U kunt dit op twee manieren oplossen: [de extensie handmatig installeren](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) en verbinden met de gewenste werkruimte. Of, de gewenste werkruimte instellen als uw standaardwerkruimte en automatische inrichting van de agent inschakelen.  Zie [Automatische inrichting inschakelen](security-center-enable-data-collection.md). |
| Kan geen verbinding maken tussen agent en werkruimte | Log Analytics-agent is geïnstalleerd, maar is mislukt vanwege een netwerk verbinding.  Controleer of er internettoegang is. En anders moet er een geldige HTTP-proxy voor de agent zijn geconfigureerd. | Zie Netwerkvereisten voor Monitoring Agent. |
| Agent verbonden met ontbrekende of onbekende werkruimte | Security Center geïdentificeerd dat de Log Analytics-agent die op de virtuele machine is geïnstalleerd, is verbonden met een werk ruimte waartoe hij geen toegang heeft. | Dit kan gebeuren in twee gevallen. De werkruimte is verwijderd en bestaat niet meer. Installeer de agent opnieuw met de juiste werkruimte of verwijder de agent en laat Security Center de automatische inrichtingsinstallatie voltooien. In het tweede geval behoort de werkruimte tot een abonnement waarvoor Security Center geen machtigingen heeft. Security Center verleent de Microsoft Security Resource Provider toegang op basis van een abonnement. Als u toegang wilt inschakelen, registreert u het abonnement op Microsoft Security Resource Provider. U kunt dit doen via API, PowerShell, portal of gewoon door in het dashboard **Overzicht** van het Security Center te filteren op abonnement. Zie [Resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) voor meer informatie. |
| Agent reageert niet of ID ontbreekt | Security Center kan de beveiligingsgegevens die zijn gescand van de virtuele machine niet ophalen, zelfs niet als de agent is geïnstalleerd. | De agent rapporteert geen gegevens, ook de heartbeat niet. De agent is mogelijk beschadigd of het verkeer wordt geblokkeerd. Het is ook mogelijk dat de agent gegevens rapporteert, maar er ontbreekt een Azure-Resource-ID zodat de gegevens niet overeenkomen met de Azure-VM. Zie [probleemoplossings handleiding voor log Analytics-agent voor Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal)voor meer informatie over het oplossen van problemen met Linux. Zie [Troubleshooting Windows Virtual Machines](../virtual-machines/extensions/oms-windows.md#troubleshoot-and-support) (Problemen met virtuele Windows-machines oplossen) voor het oplossen van problemen in Windows. |
| Agent niet geïnstalleerd | Gegevensverzameling is uitgeschakeld. | Schakel het verzamelen van gegevens in het beveiligings beleid in of installeer de Log Analytics agent hand matig. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Problemen met de netwerk vereisten voor de bewakings agent oplossen <a name="mon-network-req"></a>

Agents kunnen alleen verbinding maken met Security Center en zich daarbij registreren als ze toegang hebben tot netwerkbronnen, inclusief de poortnummers en domein-URL's.

* Voor proxyservers moet u ervoor zorgen dat de juiste resources voor de proxyserver zijn geconfigureerd in de instellingen voor de agent. Lees dit artikel voor meer informatie over [het wijzigen van de proxy-instellingen](../azure-monitor/agents/agent-windows.md).
* Als u een firewall gebruikt om de toegang tot internet te beperken, moet u uw firewall zodanig configureren dat toegang tot Log Analytics wordt toegestaan. De agent-instellingen hoeven niet te worden aangepast.

De volgende tabel bevat de resources die nodig zijn voor communicatie.

| Agentresource | Poorten | HTTPS-controle overslaan |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Ja |
| *.oms.opinsights.azure.com | 443 | Ja |
| *.blob.core.windows.net | 443 | Ja |
| *.azure-automation.net | 443 | Ja |

Als er problemen zijn tijdens het onboarden van de agent, leest u het artikel [Onboarding-problemen van Operations Management Suite oplossen](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Oplossen van problemen met niet goed werkende eindpuntbeveiliging

De gastagent is het bovenliggende proces van alles wat de [Microsoft Antimalware](../security/fundamentals/antimalware.md)-extensie doet. Wanneer het proces van de gastagent mislukt, kan de Microsoft Antimalware die wordt uitgevoerd als onderliggend proces van de gastagent ook mislukken.  In dergelijke scenario's wordt aanbevolen de volgende opties te controleren:

* Als de doel-VM een aangepaste installatiekopie is en de maker van de VM nooit een gastagent heeft geïnstalleerd.
* Als het doel een Linux-VM in plaats van een Windows-VM is, mislukt het installeren van de Windows-versie van de anti-malware-extensie op een Linux-VM. De Linux-gastagent heeft specifieke vereisten voor het besturingssysteem en de vereiste pakketten. Als niet aan deze vereisten wordt voldaan, werkt de VM-agent ook niet.
* Als de VM is gemaakt met een oudere versie van gastagent. Als dat het geval is, houd er dan rekening mee dat een aantal oude agents niet automatisch kunnen bijwerken naar de nieuwe versie en dat dit tot dit probleem kan leiden. Gebruik altijd de nieuwste versie van gastagent als u uw eigen installatiekopieën maakt.
* Sommige beheersoftware van derden kan de gastagent uitschakelen of toegang tot bepaalde bestandslocaties blokkeren. Als u beheersoftware van derden op de VM hebt geïnstalleerd, zorgt u ervoor dat de agent op de uitsluitingslijst staat.
* Bepaalde firewallinstellingen of netwerkbeveiligingsgroepen (NSG) blokkeren mogelijk het netwerkverkeer naar en van de gastagent.
* Een toegangsbeheerlijst (ACL) blokkeert mogelijk de toegang tot de schijf.
* Onvoldoende schijfruimte kan er voor zorgen dat de gastagent niet goed functioneert.

De gebruikersinterface van Microsoft Antimalware is standaard uitgeschakeld. Zie [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](/archive/blogs/azuresecurity/enabling-microsoft-antimalware-user-interface-post-deployment) (De Microsoft Antimalware-gebruikersinterface inschakelen op Azure Resource Manager-VM's na implementatie) voor meer informatie over hoe u deze kunt inschakelen.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Problemen oplossen met het laden van het dashboard

Als u problemen ondervindt met het laden van het dashboard van Security Center, moet u ervoor zorgen dat de gebruiker die het Security Center-abonnement registreert (de eerste gebruiker die Security Center voor het abonnement opent) en de gebruiker die het verzamelen van gegevens inschakelt, in het abonnement de rol van *Eigenaar* of *Inzender* hebben. Vanaf dat moment kunnen ook gebruikers die in het abonnement de rol van *Lezer* hebben, het dashboard weergeven, en waarschuwingen, aanbevelingen en het beleid bekijken.

## <a name="contacting-microsoft-support"></a>Contact opnemen met Microsoft-ondersteuning

Sommige problemen kunnen worden geïdentificeerd aan de hand van de richt lijnen in dit artikel, anderen die u ook kunt vinden op de Security Center open bare [micro soft Q&een pagina](/answers/topics/azure-security-center.html). Als u echter meer problemen wilt oplossen, kunt u een nieuwe ondersteunings aanvraag openen met **Azure Portal** , zoals hieronder wordt weer gegeven:

![Microsoft-ondersteuning](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Zie ook

In dit document hebt u kunnen lezen hoe u het beveiligingsbeleid configureert in Azure Security Center. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

* [Plannings- en bedieningsgids voor het Azure Beveiligingscentrum](security-center-planning-and-operations-guide.md): leer de ontwerpoverwegingen kennen en plan hiervoor bij de overstap naar Azure Security Center.
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): leer hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren
* [Beveiligingswaarschuwingen in Azure Security Center](./security-center-alerts-overview.md)
* [Zelfstudie: Reageren op beveiligingsincidenten](tutorial-security-incident.md)
* [Validatie van waarschuwingen in Azure Security Center](security-center-alert-validation.md)
* [E-mailmeldingen in Azure Security Center](security-center-provide-security-contact-details.md)
* [Beveiligingsincidenten afhandelen in Azure Security Center](security-center-incident.md)
* [Detectiemogelijkheden van Azure Security Center](./security-center-alerts-overview.md)
* [Partneroplossingen bewaken met Azure Security Center](./security-center-partner-integration.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
* [Veelgestelde](faq-general.md) vragen over Azure Security Center: vind een veelgestelde vraag over het gebruik van de service
* [Azure-beveiligingsblog](/archive/blogs/azuresecurity/): lees blogberichten over de beveiliging en naleving van Azure