---
title: Archief van wat is er nieuw in Azure Security Center
description: Een beschrijving van wat er nieuw is en is gewijzigd in Azure Security Center van zes maanden geleden en eerder.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2020
ms.author: memildin
ms.openlocfilehash: 751ee19225e7e550f368fff2415cd07f25b02d25
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539933"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Wilt u archiveren wat er nieuw is in Azure Security Center?

De belangrijkste [nieuwe functies in azure Security Center?](release-notes.md) de pagina release opmerkingen bevat updates voor de afgelopen zes maanden, terwijl deze pagina oudere items bevat.

Op deze pagina vindt u informatie over:

- Nieuwe functies
- Opgeloste fouten
- Afgeschafte functionaliteit


## <a name="july-2020"></a>Juli 2020

De updates in juli zijn onder meer:
- [De evaluatie van beveiligingsproblemen voor virtuele machines is nu beschikbaar voor niet-Marketplace-installatiekopieën](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Beveiliging tegen bedreigingen voor Azure Storage is uitgebreid tot Azure Files en Azure Data Lake Storage Gen2 (preview)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Acht nieuwe aanbevelingen voor het inschakelen van beveiligingsfuncties voor bedreigingen](#eight-new-recommendations-to-enable-threat-protection-features)
- [Verbeteringen in de containerbeveiliging - sneller zoeken in het register en vernieuwde documentatie](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptieve toepassingsregelaars bijgewerkt met een nieuwe aanbeveling en ondersteuning voor jokertekens in padregels](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Zes beleidsregels voor Advanced Data Security van SQL afgeschaft](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>De evaluatie van beveiligingsproblemen voor virtuele machines is nu beschikbaar voor niet-Marketplace-installatiekopieën

Bij het implementeren van een oplossing voor de evaluatie van beveiligingsproblemen voerde Security Center eerder een validatiecontrole uit voorafgaand aan implementatie. De controle was om te bevestigen dat de virtuele doelmachine een Marketplace-SKU bevatte. 

Met deze update is de controle verwijderd en kunt u nu hulpprogramma's voor de evaluatie van beveiligingsproblemen implementeren op 'aangepaste' Windows- en Linux-machines. Aangepaste installatiekopieën zijn bestanden die u hebt gewijzigd op basis van de standaardinstellingen voor Marketplace.

Hoewel u nu de geïntegreerde uitbreiding van de evaluatie van beveiligingsproblemen kunt implementeren (mogelijk gemaakt door Qualys) op veel meer computers, is ondersteuning alleen beschikbaar als u een besturingssysteem gebruikt dat wordt vermeld in [De geïntegreerde scanner voor beveiligingsproblemen implementeren op virtuele machines van de Standaard-laag](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

Meer informatie over de [geïntegreerde beveiligingsscanner voor virtuele machines (vereist Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Meer informatie over het gebruik van uw eigen oplossing voor de evaluatie van beveiligingsproblemen met een privélicentie van Qualys of Rapid7 vindt u in [Een oplossing voor het scannen van problemen van een partner implementeren](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Beveiliging tegen bedreigingen voor Azure Storage is uitgebreid tot Azure Files en Azure Data Lake Storage Gen2 (preview)

Beveiliging tegen bedreigingen voor Azure Storage detecteert potentieel schadelijke activiteiten in uw Azure Storage-accounts. Security Center geeft waarschuwingen weer wanneer er wordt geprobeerd toegang te krijgen tot uw opslagaccounts. 

Uw gegevens kunnen worden beschermd, ongeacht of deze zijn opgeslagen als blobcontainers, bestandsshares of data lakes.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Acht nieuwe aanbevelingen voor het inschakelen van beveiligingsfuncties voor bedreigingen

Er zijn acht nieuwe aanbevelingen toegevoegd om een eenvoudige manier te bieden om beveiligingsfuncties van Azure Security Center voor de volgende typen resources in te schakelen: virtuele machines, App Service-abonnementen, Azure SQL Database-servers, SQL-servers op computers, Azure Storage-accounts, Azure Kubernetes Service-clusters, Azure Container Registry-registers en Azure Key Vault-kluizen.

De nieuwe aanbevelingen zijn:

- **Advanced Data Security moet zijn ingeschakeld voor Azure SQL Database-servers**
- **Advanced Data Security moet zijn ingeschakeld voor SQL-servers op computers**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure App Service-plannen**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Container Registry-registers**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Key Vault-kluizen**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Kubernetes Service-clusters**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Storage-accounts**
- **Advanced Thread Protection moet zijn ingeschakeld op virtuele machines**

Deze nieuwe aanbevelingen maken deel uit van het beveiligingsbeheer **Azure Defender inschakelen**.

De aanbevelingen bevatten ook de mogelijkheid voor snelle oplossingen. 

> [!IMPORTANT]
> Als u een van deze aanbevelingen herstelt, worden er kosten in rekening gebracht voor de beveiliging van de relevante resources. Deze kosten worden onmiddellijk van kracht als er gerelateerde resources zijn in het huidige abonnement. Of in de toekomst, als u ze op een later tijdstip toevoegt.
> 
> Als u bijvoorbeeld geen Azure Kubernetes Service-clusters in uw abonnement hebt en u de beveiliging tegen bedreigingen inschakelt, worden er geen kosten in rekening gebracht. Als u in de toekomst een cluster toevoegt aan dit abonnement, wordt dit automatisch beveiligd en worden de kosten op dat moment gestart.

Meer informatie hierover vindt u op de [pagina met naslaginformatie over beveiligingsaanbevelingen](recommendations-reference.md).

Meer informatie over [bescherming tegen bedreigingen in Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Verbeteringen in de containerbeveiliging - sneller zoeken in het register en vernieuwde documentatie

Als onderdeel van de continue investeringen in het domein van containerbeveiliging zijn we blij een aanzienlijke prestatieverbetering in de dynamische scans van Security Center-containerinstallatiekopieën die zijn opgeslagen in Azure Container Registry te kunnen delen. Scans zijn nu doorgaans in ongeveer twee minuten voltooid. In sommige gevallen kunnen ze maximaal 15 minuten duren.

Ter verbetering van de duidelijkheid en richtlijnen met betrekking tot de beveiligingsmogelijkheden van containers van Azure Security Center, hebben we ook de pagina's met documentatie over containerbeveiliging vernieuwd. 

Zie de volgende artikelen voor meer informatie over containerbeveiliging van Security Center:

- [Overzicht van beveiligingsfuncties voor containers van Security Center](container-security.md)
- [Details van de integratie met Azure Container Registry](defender-for-container-registries-introduction.md)
- [Details van de integratie met Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Uw registers scannen en uw Docker-hosts beveiligen](container-security.md)
- [Beveiligingswaarschuwingen van de functies voor beveiliging tegen bedreigingen voor Azure Kubernetes Service clusters](alerts-reference.md#alerts-akscluster)
- [Beveiligingswaarschuwingen van de functies voor beveiliging tegen bedreigingen voor Azure Kubernetes Service-hosts](alerts-reference.md#alerts-containerhost)
- [Beveiligingsaanbevelingen voor containers](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptieve toepassingsregelaars bijgewerkt met een nieuwe aanbeveling en ondersteuning voor jokertekens in padregels

De functie voor adaptieve toepassingsregelaars heeft twee belangrijke updates ontvangen:

* Een nieuwe aanbeveling duidt mogelijk legitiem gedrag aan dat nog niet is toegestaan. De nieuwe aanbeveling, **De Allowlist-regels in uw beleid voor adaptief toepassingsbeheer moeten worden bijgewerkt**, vraagt u om nieuwe regels aan het bestaande beleid toe te voegen om het aantal fout-positieven in adaptieve toepassingsregelaars te verminderen.

* Padregels ondersteunen nu jokertekens. Vanaf deze update kunt u regels voor toegestane paden configureren met behulp van jokertekens. Er zijn twee ondersteunde scenario's:

    * Een jokerteken aan het einde van een pad gebruiken om alle uitvoerbare bestanden in deze map en submappen toe te staan

    * Een jokerteken in het midden van een pad gebruiken om het mogelijk te maken een bekende naam van een uitvoerbaar bestand te gebruiken met een veranderende mapnaam (bijvoorbeeld persoonlijke gebruikersmappen met een bekend uitvoerbaar bestand, automatisch gegenereerde mapnamen, etc.).


[Meer informatie over adaptieve toepassingsregelaars](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Zes beleidsregels voor Advanced Data Security van SQL afgeschaft

Er worden zes beleidsregels met betrekking tot geavanceerde gegevensbeveiliging voor SQL-machines afgeschaft:

- Advanced Threat Protection-typen moeten zijn ingesteld op 'Alles' in de Advanced Data Security-instellingen van SQL Managed Instance
- Advanced Threat Protection-typen moeten worden ingesteld op 'Alles' in de Advanced Data Security-instellingen van SQL Server
- De instellingen voor Advanced Data Security voor het beheerde SQL-exemplaar moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- De Advanced Data Security-instellingen voor SQL Server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen voor het beheerde SQL-exemplaar
- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen van de SQL-server

Meer informatie over [ingebouwd beleid](./policy-reference.md).



## <a name="june-2020"></a>Juni 2020

De updates in juni zijn onder meer:
- [Beveiligingsscore-API (preview)](#secure-score-api-preview)
- [Geavanceerde gegevens beveiliging voor SQL-machines (Azure, andere Clouds en on-premises) (preview-versie)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics-agent op Azure Arc-machines (preview)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nieuw beleid voor het maken van configuraties voor continue export en werkstroomautomatisering op schaal](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nieuwe aanbeveling voor het gebruik van netwerkbeveiligingsgroepen om niet op internet gerichte virtuele machines te beveiligen](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nieuw beleid voor het inschakelen van beveiliging tegen bedreigingen en geavanceerde gegevensbeveiliging](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Beveiligingsscore-API (preview)

U hebt nu toegang tot uw score via de [Beveiligingsscore-API](/rest/api/securitycenter/securescores/) (momenteel als preview). De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportagemechanisme te bouwen van uw beveiligingsscores in de loop van de tijd. U kunt bijvoorbeeld de **Beveiligingsscore**-API gebruiken om de score voor een specifiek abonnement op te halen. Daarnaast kunt u de API voor **besturingselementen van de beveiligingsscore** gebruiken om de besturingselementen voor beveiliging en de huidige score van uw abonnementen weer te geven.

Zie [het gebied voor beveiligingsscores van onze GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) voor voorbeelden van externe hulpprogramma's die mogelijk zijn gemaakt met de Beveiligingsscore-API.

Meer informatie over [beveiligingsscore en besturingselementen voor beveiliging in Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Geavanceerde gegevens beveiliging voor SQL-machines (Azure, andere Clouds en on-premises) (preview-versie)

De geavanceerde gegevensbeveiliging van Azure Security Center voor SQL-machines biedt nu beveiliging voor SQL-servers die worden gehost op Azure, in andere cloudomgevingen en zelfs op on-premises machines. Hiermee wordt de beveiliging voor uw systeemeigen SQL-servers van Azure uitgebreid om hybride omgevingen volledig te ondersteunen.

Geavanceerde gegevensbeveiliging biedt evaluatie van beveiligingsproblemen en geavanceerde beveiliging tegen bedreigingen voor uw SQL-machines, waar ze zich ook bevinden.

Het instellen bestaat uit twee stappen:

1. De Log Analytics-agent implementeren op de hostcomputer van uw SQL Server om verbinding te maken met het Azure-account.

1. Het inschakelen van de optionele bundel op de pagina met prijzen en instellingen van Security Center.

Meer informatie over [geavanceerde gegevensbeveiliging voor SQL-machines](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics-agent op Azure Arc-machines (preview)

Er zijn twee nieuwe aanbevelingen toegevoegd om de [Log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md) te implementeren op uw Azure Arc-machines en ervoor te zorgen dat ze worden beveiligd door Azure Security Center:

- **De Log Analytics-agent moet zijn geïnstalleerd op uw Windows Azure Arc-machines (preview)**
- **De Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-machines (preview)**

Deze nieuwe aanbevelingen worden weergegeven in dezelfde vier besturingselementen voor beveiliging als de bestaande (gerelateerde) aanbeveling, **De bewakingsagent moet op uw computers worden geïnstalleerd**: beveiligingsconfiguraties herstellen, adaptieve toepassingsregelaars toepassen, systeemupdates toepassen en eindpuntbeveiliging inschakelen.

De aanbevelingen omvatten ook de mogelijkheid voor snelle oplossingen om het implementatieproces te versnellen. 

Meer informatie over deze twee nieuwe aanbevelingen vindt u in de tabel [Compute- en app-aanbevelingen](recommendations-reference.md#recs-compute).

Meer informatie over hoe Azure Security Center de agent gebruikt, vindt u in [Wat is de Log Analytics-agent?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Meer informatie over [extensies voor Azure Arc-machines](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nieuw beleid voor het maken van configuraties voor continue export en werkstroomautomatisering op schaal

Het automatiseren van de processen voor bewaking en reageren op incidenten van uw organisatie kan de tijd die nodig is om beveiligingsincidenten te onderzoeken en te verhelpen aanzienlijk verbeteren.

Als u uw automatiseringsconfiguraties in uw organisatie wilt implementeren, gebruikt u deze ingebouwde DeployIfdNotExist-beleidsregels van Azure voor het maken en configureren van procedures voor [continue export](continuous-export.md) en [werkstroomautomatisering](workflow-automation.md):

Het beleid is te vinden in Azure Policy:


|Doel  |Beleid  |Beleids-id  |
|---------|---------|---------|
|Continue export naar Event Hub|[Export implementeren in Event Hub voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Continue export naar Log Analytics-werkruimte|[Export implementeren in Log Analytics-werkruimte voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Werkstroomautomatisering voor beveiligingswaarschuwingen|[Werkstroomautomatisering implementeren voor Azure Security Center-waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Werkstroomautomatisering voor beveiligingsaanbevelingen|[Werkstroomautomatisering implementeren voor Azure Security Center-aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Aan de slag met [sjablonen voor werkstroomautomatisering](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Meer informatie over het gebruik van de twee exportbeleidsregels vindt u in [Configure workflow automation at scale using the supplied policies](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) (Werkstroomautomatisering op schaal configureren met de meegeleverde beleidsregels) en [Set up a continuous export](continuous-export.md#set-up-a-continuous-export) (Continue export instellen).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nieuwe aanbeveling voor het gebruik van netwerkbeveiligingsgroepen om niet op internet gerichte virtuele machines te beveiligen

Het besturingselement voor beveiliging 'aanbevolen procedures voor beveiliging implementeren' bevat nu de volgende nieuwe aanbeveling:

- **Niet-internetgerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen**

In een bestaande aanbeveling, **Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen**, werd geen onderscheid gemaakt tussen op internet gerichte en niet op internet gerichte virtuele machines. Voor beide werd een aanbeveling met een hoge urgentie gegenereerd als een virtuele machine niet aan een netwerkbeveiligingsgroep was toegewezen. In deze nieuwe aanbeveling wordt een onderscheid gemaakt met niet op internet gerichte machines om de fout-positieven te verminderen en onnodige waarschuwingen met hoge urgentie te voorkomen.

Meer informatie vindt u in de tabel [Aanbevelingen voor netwerken](recommendations-reference.md#recs-networking).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nieuw beleid voor het inschakelen van beveiliging tegen bedreigingen en geavanceerde gegevensbeveiliging

De onderstaande nieuwe beleidsregels zijn toegevoegd aan het ASC-standaardinitiatief en zijn ontworpen om te helpen bij het inschakelen van beveiliging tegen bedreigingen of geavanceerde gegevensbeveiliging voor de relevante resourcetypen.

Het beleid is te vinden in Azure Policy:


| Beleid                                                                                                                                                                                                                                                                | Beleids-id                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Advanced Data Security moet zijn ingeschakeld voor Azure SQL Database-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Advanced Data Security moet zijn ingeschakeld voor SQL-servers op computers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Storage-accounts](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Key Vault-kluizen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure App Service-plannen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Container Registry-registers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Kubernetes Service-clusters](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Advanced Thread Protection moet zijn ingeschakeld op virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Meer informatie over [bescherming tegen bedreigingen in Azure Security Center](azure-defender.md).


## <a name="may-2020"></a>Mei 2020

De updates in mei zijn onder meer:
- [Regels voor waarschuwingsonderdrukking (preview)](#alert-suppression-rules-preview)
- [Evaluatie van beveiligingsproblemen van virtuele machines is nu algemeen beschikbaar](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Wijzigingen in de Just-In-Time-toegang van virtuele machines (VM)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk besturingselement voor beveiliging](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Schakeloptie toegevoegd om aanbevelingen weer te geven in besturingselementen of als een platte lijst](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Besturingselement voor beveiliging 'Aanbevolen procedures voor beveiliging implementeren' uitgebreid](#expanded-security-control-implement-security-best-practices)
- [Aangepaste beleidsregels met aangepaste metagegevens zijn nu algemeen beschikbaar](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Mogelijkheden voor crashdumpanalyse migreren naar detectie van bestandsloze aanvallen](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regels voor waarschuwingsonderdrukking (preview)

Met deze nieuwe functie (momenteel in preview) kunt u overbodige waarschuwingen verminderen. Gebruik regels om waarschuwingen waarvan bekend is dat ze onschuldig of gerelateerd zijn aan normale activiteiten in uw organisatie automatisch te verbergen. Zo kunt u zich richten op de meest relevante bedreigingen. 

Waarschuwingen die overeenkomen met uw ingeschakelde onderdrukkingsregels worden nog steeds gegenereerd, maar de status wordt ingesteld op 'Genegeerd'. U kunt de status bekijken in Azure Portal of de manier waarop u uw Security Center beveiligingswaarschuwingen bekijkt.

Met onderdrukkingsregels worden de criteria gedefinieerd waarvoor waarschuwingen automatisch moeten worden genegeerd. Normaal gesproken gebruikt u een onderdrukkingsregel voor het volgende:

- waarschuwingen onderdrukken die u als fout-positieven hebt geïdentificeerd

- waarschuwingen onderdrukken die te vaak worden geactiveerd om nuttig te zijn

Meer informatie over [het onderdrukken van waarschuwingen voor beveiliging tegen bedreigingen van Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Evaluatie van beveiligingsproblemen van virtuele machines is nu algemeen beschikbaar

De Standaard-laag van Security Center bevat nu een geïntegreerde evaluatie van beveiligingsproblemen voor virtuele machines zonder extra kosten. Deze uitbreiding wordt mogelijk gemaakt door Qualys, maar rapporteert de resultaten direct terug naar Security Center. U hebt geen Qualys-licentie of Qualys-account nodig. De scans worden naadloos uitgevoerd in Security Center.

De nieuwe oplossing kan voortdurend uw virtuele machines scannen om beveiligingsproblemen op te sporen en de bevindingen in Security Center weergeven. 

Als u de oplossing wilt implementeren, gebruikt u de nieuwe beveiligingsaanbeveling:

'De ingebouwde oplossing voor evaluatie van beveiligingsproblemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys)'

Meer informatie over [De geïntegreerde evaluatie van beveiligingsproblemen voor virtuele machines van Security Center](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Wijzigingen in de Just-In-Time-toegang van virtuele machines (VM)

Security Center bevat een optionele functie waarmee u de beheerpoorten van uw virtuele machines kunt beveiligen. Dit biedt een verdediging tegen de meest voorkomende vorm van beveiligingsaanvallen.

Met deze update worden de volgende wijzigingen doorgevoerd in deze functie:

- De naam van de aanbeveling waarmee u wordt geadviseerd om JIT in te schakelen, is gewijzigd. Eerder was de naam 'Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines' en nu is de naam: 'Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer'.

- De aanbeveling wordt alleen geactiveerd als er open beheerpoorten zijn.

Meer informatie over [de JIT-toegangsfunctie](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk besturingselement voor beveiliging

Een besturingselement voor beveiliging dat is geïntroduceerd in de verbeterde beveiligingsscore is 'Aanbevolen procedures voor beveiliging implementeren'. Aangepaste aanbevelingen die zijn gemaakt voor uw abonnementen, worden automatisch in dat besturingselement geplaatst. 

Om u te helpen uw aangepaste aanbevelingen gemakkelijker te vinden, zijn deze naar een speciaal besturingselement voor beveiliging, 'Aangepaste aanbevelingen ', verplaatst. Dit besturingselement heeft geen invloed op uw beveiligingsscore.

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview) in Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Schakeloptie toegevoegd om aanbevelingen weer te geven in besturingselementen of als een platte lijst

Besturingselementen voor beveiliging zijn logische groepen van gerelateerde beveiligingsaanbevelingen. Ze zijn gebaseerd op gebieden waar u kwetsbaar bent voor aanvallen. Een besturingselement is een reeks beveiligingsaanbevelingen met instructies die u helpen bij het implementeren van deze aanbevelingen.

Als u onmiddellijk wilt zien hoe goed uw organisatie een afzonderlijke kwetsbaarheid beveiligt, controleert u de scores voor elk besturingselement voor beveiliging.

Uw aanbevelingen worden standaard weergegeven in de besturingselementen voor beveiliging. Vanaf deze update kunt u ze ook als een lijst weergeven. Als u ze wilt weergeven als een eenvoudige lijst, gesorteerd op de status van de betrokken resources, gebruikt u de nieuwe schakeloptie 'Groeperen op besturingselementen'. De schakeloptie bevindt zich boven de lijst in de portal.

De besturingselementen voor beveiliging - en deze schakeloptie - maken deel uit van de vernieuwde beveiligingsscore. Vergeet niet om ons uw feedback te sturen vanuit de portal.

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview) in Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="De schakeloptie Groeperen op besturingselementen voor aanbevelingen":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Besturingselement voor beveiliging 'Aanbevolen procedures voor beveiliging implementeren' uitgebreid 

Een besturingselement voor beveiliging dat is geïntroduceerd in de verbeterde beveiligingsscore is 'Aanbevolen procedures voor beveiliging implementeren'. Wanneer een aanbeveling zich in dit besturingselement bevindt, heeft dit geen invloed op de beveiligingsscore. 

Met deze update zijn drie aanbevelingen uit de besturingselementen waarin deze oorspronkelijk zijn geplaatst naar dit besturingselement voor aanbevolen procedures verplaatst. We hebben deze stap doorgevoerd omdat er is vastgesteld dat het risico van deze drie aanbevelingen lager is dan oorspronkelijk werd aangenomen.

Daarnaast zijn er twee nieuwe aanbevelingen geïntroduceerd en toegevoegd aan dit besturingselement.

De drie aanbevelingen die zijn verplaatst, zijn:

- **MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement** (oorspronkelijk in het besturingselement 'MFA inschakelen')
- **Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement** (oorspronkelijk in het besturingselement 'Toegang en machtigingen beheren')
- **Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement** (oorspronkelijk in het besturingselement 'Toegang en machtigingen beheren')

De twee nieuwe aanbevelingen die aan het besturingselement zijn toegevoegd, zijn:

- **De Gastconfiguratie-extensie moet op virtuele Windows-machines zijn geïnstalleerd (preview)** : het gebruik van [Azure Policy-gastconfiguratie](../governance/policy/concepts/guest-configuration.md) biedt inzicht in de instellingen van de virtuele machines op de server en toepassing (alleen Windows).

- **Windows Defender Exploit Guard moet zijn ingeschakeld op uw computers (preview)** : Windows Defender Exploit Guard maakt gebruik van de Azure Policy-gastconfiguratieagent. Exploit Guard bestaat uit vier onderdelen die zijn ontworpen om apparaten te vergrendelen tegen diverse aanvalsvectoren en blokkeergedrag die in malware-aanvallen worden gebruikt en die bedrijven de mogelijkheid bieden om een goede balans te vinden tussen hun vereisten op het gebied van beveiligingsrisico's en productiviteit (alleen Windows).

Meer informatie over Windows Defender Exploit Guard vindt u in [Een Exploit Guard-beleid maken en implementeren](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Aangepaste beleidsregels met aangepaste metagegevens zijn nu algemeen beschikbaar

Aangepaste beleidsregels maken nu deel uit van de Security Center-aanbevelingen, de beveiligingsscore en het dashboard voor naleving van regelgeving. Deze functie is nu algemeen beschikbaar en biedt u de mogelijkheid om de dekking van de evaluatie van beveiligingsproblemen van uw organisatie in Security Center uit te breiden. 

Maak een aangepaste initiatief in Azure Policy, voeg er beleidsregels aan toe en onboard dit naar Azure Security Center, en visualiseer het vervolgens als aanbevelingen.

We hebben nu ook de optie toegevoegd voor het bewerken van de aangepaste metagegevens voor aanbevelingen. Opties voor metagegevens zijn ernst, herstelstappen, informatie over bedreigingen en meer.  

Meer informatie over [uw aangepaste aanbevelingen verbeteren met gedetailleerde informatie](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Mogelijkheden voor crashdumpanalyse migreren naar detectie van bestandsloze aanvallen 

De detectiemogelijkheden van Windows Crash Dump Analysis (CDA) worden geïntegreerd in [detectie van bestandsloze aanvallen](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). Analyse van detectie van bestandsloze aanvallen biedt verbeterde versies van de volgende beveiligingswaarschuwingen voor Windows-computers: 'Code-injectie gedetecteerd', 'Verdachte Windows-module gedetecteerd', 'Shellcode gedetecteerd' en 'Verdacht codesegment gedetecteerd'.

Enkele voordelen hiervan:

- **Proactieve en tijdige detectie van malware**: bij de CDA-benadering werd gewacht op een crash, en werden vervolgens analyses uitgevoerd om schadelijke artefacten te vinden. Bij het gebruik van detectie van bestandsloze aanvallen worden bedreigingen in het geheugen proactief geïdentificeerd terwijl ze worden uitgevoerd. 

- **Verrijkte waarschuwingen**: de beveiligingswaarschuwingen van detectie van bestandsloze aanvallen bevatten verrijkingen die niet beschikbaar zijn via CDA, zoals informatie over actieve netwerkverbindingen. 

- **Waarschuwingsaggregatie**: wanneer CDA meerdere aanvalspatronen in één crashdump heeft gedetecteerd, worden er meerdere beveiligingswaarschuwingen geactiveerd. Bij detectie van bestandsloze aanvallen worden alle geïdentificeerde aanvalspatronen uit hetzelfde proces gecombineerd tot één waarschuwing, waardoor de noodzaak om meerdere waarschuwingen met elkaar in verband te brengen, wordt weggenomen.

- **Minder vereisten voor uw Log Analytics-werkruimte**: crashdumps met mogelijk gevoelige gegevens worden niet meer geüpload naar uw Log Analytics-werkruimte.






## <a name="april-2020"></a>April 2020

De updates in april zijn onder meer:
- [Dynamische nalevingspakketten zijn nu algemeen beschikbaar](#dynamic-compliance-packages-are-now-generally-available)
- [Identiteitsaanbevelingen nu opgenomen in de gratis laag van Azure Security Center](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamische nalevingspakketten zijn nu algemeen beschikbaar

Het dashboard voor naleving van regelgeving van Azure Security Center bevat nu **dynamische nalevingspakketten** (nu algemeen beschikbaar) om aanvullende regelgevende en bedrijfstakstandaarden bij te houden.

Dynamische nalevingspakketten kunnen worden toegevoegd aan uw abonnement of beheergroep via de pagina met beveiligingsbeleid van Security Center. Wanneer u een standaard of benchmark hebt geïntroduceerd, wordt de standaard weergegeven in het dashboard voor naleving van regelgeving met alle gekoppelde nalevingsgegevens die zijn gekoppeld als evaluaties. Er kan een overzichtsrapport voor alle geïntroduceerde standaarden worden gedownload.

U kunt nu standaarden toevoegen zoals:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK OFFICIAL en UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nieuw)** (een meer volledige weergave van Azure CIS 1.1.0)

Daarnaast hebben we onlangs de **Azure Security-benchmark** toegevoegd, de door Microsoft ontworpen, voor Azure specifieke richtlijnen voor aanbevolen procedures voor beveiliging en naleving op basis van algemene nalevingskaders. Er worden extra standaarden ondersteund in het dashboard zodra deze beschikbaar komen.  
 
Meer informatie over het [aanpassen van de set standaarden in uw dashboard voor naleving van regelgeving](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Identiteitsaanbevelingen nu opgenomen in de gratis laag van Azure Security Center

Beveiligingsaanbevelingen voor identiteit en toegang in de gratis laag van Azure Security Center zijn nu algemeen beschikbaar. Dit maakt deel uit van de inspanningen om de CSPM-functies (Cloud Security Posture Management) gratis te maken. Tot nu toe zijn deze aanbevelingen alleen beschikbaar in de prijscategorie Standaard.

Voorbeelden van aanbevelingen voor identiteit en toegang zijn onder meer:

- 'Meervoudige verificatie moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement.'
- 'Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement.'
- 'Afgeschafte accounts moeten worden verwijderd uit uw abonnement.'

Als u abonnementen hebt in de gratis laag, worden de beveiligingsscores ervan beïnvloed door deze wijziging omdat ze nooit zijn geëvalueerd op hun identiteits- en toegangsbeveiliging.

Meer informatie over [aanbevelingen voor identiteit en toegang](recommendations-reference.md#recs-identityandaccess).

Meer informatie over [bewaking van identiteit en toegang](security-center-identity-access.md).



## <a name="march-2020"></a>Maart 2020

De updates in maart zijn onder andere:

- [Werk stroom automatisering is nu algemeen beschikbaar](#workflow-automation-is-now-generally-available)
- [Integratie van Azure Security Center met Windows-beheer centrum](#integration-of-azure-security-center-with-windows-admin-center)
- [Beveiliging voor de Azure Kubernetes-service](#protection-for-azure-kubernetes-service)
- [Verbeterde just-in-time-ervaring](#improved-just-in-time-experience)
- [Twee beveiligings aanbevelingen voor afgeschafte webtoepassingen](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Werk stroom automatisering is nu algemeen beschikbaar

De functie werk stroom automatisering van Azure Security Center is nu algemeen beschikbaar. Gebruik deze functie om automatisch Logic Apps te activeren voor beveiligings waarschuwingen en aanbevelingen. Daarnaast zijn er hand matige triggers beschikbaar voor waarschuwingen en worden alle aanbevelingen met de optie snel herstellen beschikbaar.

Elk beveiligings programma bevat meerdere werk stromen voor reactie op incidenten. Deze processen kunnen het melden van relevante belanghebbenden, het starten van een wijzigings beheer proces en het Toep assen van specifieke herbemiddelings stappen zijn. Beveiligings experts raden u aan zo veel mogelijk stappen van deze procedures te automatiseren. Automation vermindert de overhead en kan de beveiliging verbeteren door ervoor te zorgen dat de proces stappen snel, consistent en volgens uw vooraf gedefinieerde vereisten worden uitgevoerd.

Zie [werk stroom automatisering](workflow-automation.md)voor meer informatie over de mogelijkheden voor automatische en hand matige Security Center voor het uitvoeren van uw werk stromen.

Meer informatie over [het maken van Logic apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integratie van Azure Security Center met Windows-beheer centrum

Het is nu mogelijk om uw on-premises Windows-servers rechtstreeks naar de Azure Security Center te verplaatsen vanuit het Windows-beheer centrum. Security Center wordt vervolgens uw enige glas venster om beveiligings gegevens weer te geven voor alle resources van uw Windows-beheer centrum, waaronder on-premises servers, virtuele machines en extra PaaS-workloads.

Nadat u een server hebt verplaatst van het Windows-beheer centrum naar Azure Security Center, kunt u het volgende doen:

- Beveiligings waarschuwingen en aanbevelingen weer geven in de uitbrei ding Security Center van het Windows-beheer centrum.
- Bekijk de beveiligings postuur en haal meer gedetailleerde informatie op over uw door Windows-beheer centrum beheerde servers in de Security Center in het Azure Portal (of via een API).

Meer informatie over [het integreren van Azure Security Center met het Windows-beheer centrum](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Beveiliging voor de Azure Kubernetes-service

Azure Security Center breidt de beveiligings functies van de container uit om Azure Kubernetes service (AKS) te beveiligen.

De populaire open-source platform Kubernetes is zodanig aangenomen dat het nu een industrie standaard is voor container indeling. Ondanks deze wijde implementatie is er nog steeds geen informatie over het beveiligen van een Kubernetes-omgeving. Voor het beschermen van de kwets baarheid van een container toepassing is expertise vereist om te garanderen dat de infra structuur veilig is geconfigureerd en voortdurend wordt bewaakt voor mogelijke dreigingen.

De Security Center verdediging omvat:

- **Detectie en zicht baarheid** : doorlopende detectie van beheerde AKS-instanties binnen de abonnementen die zijn geregistreerd voor Security Center.
- **Aanbevelingen voor beveiliging** -beschik bare aanbevelingen om u te helpen te voldoen aan de aanbevolen procedures voor beveiliging voor AKS. Deze aanbevelingen zijn opgenomen in uw beveiligde Score om ervoor te zorgen dat ze worden weer gegeven als onderdeel van de beveiligings postuur van uw organisatie. Een voor beeld van een AKS-aanbeveling die u kunt zien, is ' op rollen gebaseerd toegangs beheer moet worden gebruikt om de toegang tot een Kubernetes-service cluster te beperken '.
- **Bedreigings beveiliging** : Security Center u wordt gewaarschuwd voor bedreigingen en schadelijke activiteiten die zijn gedetecteerd op het niveau van de host en het AKS-cluster.

Meer informatie over de [integratie van Azure Kubernetes Services met Security Center](defender-for-kubernetes-introduction.md).

Meer informatie over [de beveiligings functies van de container in Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Verbeterde just-in-time-ervaring

De functies, bewerking en gebruikers interface voor het Azure Security Center van just-in-time hulpprogram ma's die uw beheer poorten beveiligen, zijn als volgt verbeterd: 

- **Veld motivering** : als u via de just-in-time pagina van de Azure portal toegang tot een virtuele machine (VM) wilt aanvragen, is er een nieuw optioneel veld beschikbaar om een reden voor de aanvraag in te voeren. Gegevens die in dit veld worden ingevoerd, kunnen in het activiteiten logboek worden bijgehouden. 
- **Automatisch opschonen van redundante just-in-time (JIT)-regels** : wanneer u een JIT-beleid bijwerkt, wordt automatisch een opschoon programma uitgevoerd om de geldigheid van de volledige ruleset te controleren. Het hulp programma zoekt naar verschillen tussen regels in uw beleid en regels in het NSG. Als het hulp programma voor opschonen een niet-overeenkomend item detecteert, wordt de oorzaak bepaald en, wanneer het veilig is om dit te doen, verwijdert u ingebouwde regels die niet meer nodig zijn. De verruiming verwijdert nooit regels die u hebt gemaakt. 

Meer informatie over [de JIT-toegangsfunctie](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Twee beveiligings aanbevelingen voor afgeschafte webtoepassingen

Twee beveiligings aanbevelingen met betrekking tot webtoepassingen worden afgeschaft: 

- De regels voor webtoepassingen op IaaS Nsg's moeten worden gehard.
    (Gerelateerd beleid: De regels voor NSG's ten aanzien van webtoepassingen op IaaS moeten strenger worden)

- Toegang tot App Services moet worden beperkt.
    (Gerelateerd beleid: toegang tot App Services moet worden beperkt [Preview])

Deze aanbevelingen worden niet meer weer gegeven in de Security Center lijst met aanbevelingen. Het gerelateerde beleid wordt niet meer opgenomen in het initiatief ' Security Center default '.

Meer informatie over [beveiligings aanbevelingen](recommendations-reference.md).




## <a name="february-2020"></a>Februari 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Aanvals detectie met een bestand voor Linux (preview-versie)

Als aanvallers stealthier Azure Security Center-methoden gebruiken om detectie te voor komen, wordt in aanvulling op Windows de detectie van de aanval van bestanden voor Linux uitgebreid. Aanvallen waarbij misbruik wordt gemaakt van software, schadelijke nettoladingen in goed aardige systeem processen injecteren en in het geheugen verbergen. Deze technieken:

- traceringen van malware op schijf minimaliseren of elimineren
- de kans op detectie door op schijf gebaseerde scan oplossingen op basis van malware aanzienlijk beperken

Als u deze bedreiging wilt bemeteren, Azure Security Center u de aanvals detectie van bestanden voor Windows in oktober 2018 vrijgegeven en nu ook uitgebreide aanvals detectie op Linux. 



## <a name="january-2020"></a>Januari 2020

### <a name="enhanced-secure-score-preview"></a>Verbeterde beveiligde Score (preview-versie)

Een verbeterde versie van de functie beveiligde Score van Azure Security Center is nu beschikbaar als preview. In deze versie zijn meerdere aanbevelingen onderverdeeld in beveiligings controles die beter aansluiten op uw kwets bare aanvallen (bijvoorbeeld de toegang tot beheer poorten beperken).

Lees meer over de wijzigingen in de beveiligde score tijdens de preview-fase en bepaal andere herstel bewerkingen waarmee u uw omgeving verder kunt beveiligen.

Meer informatie over [verbeterde beveiligde Score (preview-versie)](secure-score-security-controls.md).



## <a name="november-2019"></a>November 2019

Updates in november omvatten:
 - [Bedreigings beveiliging voor Azure Key Vault in Noord-Amerika regio's (preview-versie)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Bedreigings beveiliging voor Azure Storage omvat het controleren van malware-reputatie](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Werk stroom automatisering met Logic Apps (preview-versie)](#workflow-automation-with-logic-apps-preview)
 - [Snelle oplossing voor bulk bronnen algemeen beschikbaar](#quick-fix-for-bulk-resources-generally-available)
 - [Container installatie kopieën scannen op beveiligings problemen (preview-versie)](#scan-container-images-for-vulnerabilities-preview)
 - [Aanvullende normen voor naleving van regelgeving (preview-versie)](#additional-regulatory-compliance-standards-preview)
 - [Bedreigings beveiliging voor Azure Kubernetes service (preview)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Evaluatie van beveiligings problemen met virtuele machines (preview-versie)](#virtual-machine-vulnerability-assessment-preview)
 - [Geavanceerde gegevens beveiliging voor SQL-servers in azure Virtual Machines (preview-versie)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Ondersteuning voor aangepast beleid (preview-versie)](#support-for-custom-policies-preview)
 - [Azure Security Center dekking uitbreiden met platform voor community en partners](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Geavanceerde integraties met export van aanbevelingen en waarschuwingen (preview-versie)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [On-premises servers onboarden naar Security Center vanuit het Windows-beheer centrum (preview-versie)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Bedreigings beveiliging voor Azure Key Vault in Noord-Amerika regio's (preview-versie)

Azure Key Vault is een essentiële service voor het beveiligen van gegevens en het verbeteren van de prestaties van Cloud toepassingen door de mogelijkheid te bieden om sleutels, geheimen, cryptografische sleutels en beleids regels centraal te beheren in de Cloud. Omdat Azure Key Vault gevoelige en bedrijfskritische gegevens opslaat, is er maximale beveiliging vereist voor de sleutel kluizen en de gegevens die erin zijn opgeslagen.

Azure Security Center ondersteuning voor beveiliging tegen bedreigingen voor Azure Key Vault biedt een extra beveiligingslaag die ongebruikelijke en mogelijk schadelijke pogingen van toegang tot of misbruik van sleutel kluizen detecteert. Met deze nieuwe beveiligingslaag kunnen klanten bedreigingen tegen hun sleutel kluizen aanpakken zonder een beveiligings expert of systemen voor beveiligings bewaking te beheren. De functie bevindt zich in de open bare preview van Noord-Amerika regio's.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Bedreigings beveiliging voor Azure Storage omvat het controleren van malware-reputatie

Bedreigings beveiliging voor Azure Storage biedt nieuwe detecties met micro soft Threat Intelligence voor het detecteren van malware-uploads naar Azure Storage met behulp van hash-reputatie analyse en verdachte toegang vanaf een actieve Tor-afsluit knooppunt (een anoniem-proxy). U kunt nu gedetecteerde malware voor opslag accounts weer geven met behulp van Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Werk stroom automatisering met Logic Apps (preview-versie)

Organisaties met centraal beheerde beveiliging en IT/bewerkingen implementeren interne werk stroom processen om de vereiste actie binnen de organisatie te testen wanneer er discrepanties worden gedetecteerd in hun omgevingen. In veel gevallen kunnen deze werk stromen Herhaal bare processen en automatisering de processen binnen de organisatie aanzienlijk stroom lijnen.

Vandaag introduceren we een nieuwe mogelijkheid in Security Center waarmee klanten automatiserings configuraties kunnen maken met behulp van Azure Logic Apps en om beleids regels te maken waarmee ze automatisch worden geactiveerd op basis van specifieke ASC-bevindingen zoals aanbevelingen of waarschuwingen. De Azure Logic-app kan worden geconfigureerd voor het uitvoeren van aangepaste acties die worden ondersteund door de enorme community van Logic app-connectors, of om een van de sjablonen te gebruiken die worden geboden door Security Center, zoals het verzenden van een e-mail bericht of het openen van een ServiceNow™-ticket.

Zie [werk stroom automatisering](workflow-automation.md)voor meer informatie over de mogelijkheden voor automatische en hand matige Security Center voor het uitvoeren van uw werk stromen.

Zie [Azure Logic apps](../logic-apps/logic-apps-overview.md)voor meer informatie over het maken van Logic apps.


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Snelle oplossing voor bulk bronnen algemeen beschikbaar

Met de vele taken die een gebruiker krijgt als onderdeel van een beveiligde Score, kan het lastig zijn om problemen in een grote vloot te herstellen.

Voor het vereenvoudigen van het herstel van onjuiste beveiligings configuraties en voor het snel oplossen van aanbevelingen voor een groot aantal resources en het verbeteren van uw beveiligde Score, gebruikt u snel herstel herstellen.

Met deze bewerking kunt u de resources selecteren waarop u het herstel wilt Toep assen en een herstel actie starten waarmee de instelling namens u wordt geconfigureerd.

Snelle correctie is in het algemeen beschikbaar als onderdeel van de pagina met Security Center aanbevelingen.

Zie in de [Naslag Gids voor beveiligings aanbevelingen voor](recommendations-reference.md)meer informatie over de aanbevelingen waarvoor snelle oplossing is ingeschakeld.


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Container installatie kopieën scannen op beveiligings problemen (preview-versie)

Azure Security Center kunt nu container installatie kopieën in Azure Container Registry scannen op beveiligings problemen.

De scan van de installatie kopie werkt door het parseren van het container installatie kopie bestand en vervolgens te controleren of er bekende beveiligings problemen zijn (mogelijk gemaakt door Qualys).

De scan zelf wordt automatisch geactiveerd wanneer nieuwe container installatie kopieën naar Azure Container Registry worden gepusht. Gevonden beveiligings problemen worden weer gegeven als Security Center aanbevelingen en opgenomen in de beveiligde Score van Azure, samen met informatie over hoe u deze kunt patchen om de kwets baarheid voor aanvallen te verminderen.


### <a name="additional-regulatory-compliance-standards-preview"></a>Aanvullende normen voor naleving van regelgeving (preview-versie)

Het nalevings Dashboard van de regelgeving biedt inzicht in uw nalevings postuur op basis van Security Center Beoordelingen. Het dash board laat zien hoe uw omgeving voldoet aan de besturings elementen en vereisten die zijn aangewezen door specifieke regelgevings normen en industrie benchmarks en biedt voorschrijvende aanbevelingen voor het oplossen van deze vereisten.

Het nalevings Dashboard van de regelgeving heeft tot nu toe vier ingebouwde standaarden ondersteund: Azure CIS 1.1.0, PCI-DSS, ISO 27001 en SOC-TSP. We kondigen nu de open bare preview-versie van aanvullende ondersteunde standaarden: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM en UK ambtenaar samen met UK NHS. We hebben ook een bijgewerkte versie van Azure CIS 1.1.0 uitgebracht, waardoor er meer besturings elementen van de standaard en uitbreid baarheid worden uitgebreid.

[Meer informatie over het aanpassen van de set normen in uw nalevings dashboard](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Bedreigings beveiliging voor Azure Kubernetes service (preview)

Kubernetes is snel de nieuwe standaard voor het implementeren en beheren van software in de Cloud. Enkele mensen hebben uitgebreide ervaring met Kubernetes en veel hebben alleen aandacht besteed aan de algemene engineering en het beheer van de beveiliging. De Kubernetes-omgeving moet zorgvuldig worden geconfigureerd om te worden beveiligd, waardoor er geen toegang is tot de containers die zijn gericht op de aanvals oppervlakte, voor aanvallers open wordt gesteld. Security Center wordt de ondersteuning in de container ruimte uitgebreid naar een van de snelste groeiende Services in azure-Azure Kubernetes service (AKS).

De nieuwe mogelijkheden in deze open bare preview-versie zijn onder andere:

- **Detectie & zicht baarheid** : doorlopende detectie van beheerde AKS-instanties binnen de geregistreerde abonnementen van Security Center.
- **Veilige Score aanbevelingen** : items die kunnen worden uitgevoerd om klanten te helpen bij het naleven van de aanbevolen beveiligings procedures voor AKS, en het verhogen van hun beveiligde Score. Aanbevelingen zijn items zoals ' op rollen gebaseerd toegangs beheer moeten worden gebruikt om de toegang tot een Kubernetes-service cluster te beperken '.
- **Detectie van bedreigingen** -host-en cluster-analyses, zoals ' een geprivilegieerde container gedetecteerd '.


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Evaluatie van beveiligings problemen met virtuele machines (preview-versie)

Toepassingen die in virtuele machines worden geïnstalleerd, hebben vaak beveiligings problemen die kunnen leiden tot een schending van de virtuele machine. We kondigen dat de Security Center Standard-laag geïntegreerde evaluatie van beveiligings problemen bevat voor virtuele machines zonder extra kosten. Met de evaluatie van beveiligings problemen, mogelijk gemaakt door Qualys in de open bare preview, kunt u voortdurend alle geïnstalleerde toepassingen op een virtuele machine scannen om te zoeken naar kwets bare toepassingen en de bevindingen presen teren in de ervaring van de Security Center Portal. Security Center zorgt voor alle implementatie bewerkingen, zodat er geen extra werk van de gebruiker is vereist. We zijn van plan om opties voor evaluatie van beveiligings problemen op te geven voor het ondersteunen van de unieke bedrijfs behoeften van onze klanten.

Meer [informatie over de evaluatie van beveiligings problemen voor uw Azure-virtual machines](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Geavanceerde gegevens beveiliging voor SQL-servers in azure Virtual Machines (preview-versie)

De ondersteuning van Azure Security Center voor beveiliging tegen bedreigingen en evaluatie van beveiligings problemen voor SQL-Db's op IaaS Vm's is nu beschikbaar als preview-versie.

[Evaluatie van beveiligingsproblemen](../azure-sql/database/sql-vulnerability-assessment.md) is een eenvoudig te configureren service waarmee u potentiële zwakke plekken in de beveiliging van de database kunt detecteren, volgen en verhelpen. Het biedt inzicht in uw beveiligings postuur als onderdeel van een beveiligde Azure-Score en bevat de stappen voor het oplossen van beveiligings problemen en het verbeteren van uw data base-Fortifications.

[Geavanceerde bedreigingen beveiliging](../azure-sql/database/threat-detection-overview.md) detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot uw SQL-Server of deze misbruiken. Het controleert uw data base voortdurend op verdachte activiteiten en biedt actie gerichte beveiligings waarschuwingen voor afwijkende database toegangs patronen. Deze waarschuwingen bieden informatie over verdachte activiteiten en aanbevolen acties voor het onderzoeken en oplossen van de dreiging.


### <a name="support-for-custom-policies-preview"></a>Ondersteuning voor aangepast beleid (preview-versie)

Azure Security Center ondersteunt nu aangepaste beleids regels (in preview-versie).

Onze klanten willen hun huidige dekking van beveiligings beoordelingen in Security Center uitbreiden met hun eigen beveiligings beoordelingen op basis van beleids regels die ze in Azure Policy maken. Met ondersteuning voor aangepast beleid is dit nu mogelijk.

Deze nieuwe beleids regels zullen deel uitmaken van de Security Center aanbevelingen, een beveiligde Score en het dash board nalevings normen. Met de ondersteuning voor aangepast beleid kunt u nu een aangepast initiatief maken in Azure Policy, het vervolgens als beleid toevoegen in Security Center en dit als een aanbeveling visualiseren.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Azure Security Center dekking uitbreiden met platform voor community en partners

Gebruik Security Center om aanbevelingen te ontvangen die niet alleen van micro soft zijn, maar ook van bestaande oplossingen van partners, zoals Check Point, Tenable en CyberArk met veel meer integraties.  Met de eenvoudige stroom voor onboarding van Security Center kan uw bestaande oplossingen worden verbonden met Security Center, zodat u uw aanbevelingen voor beveiligings postuur op één plek kunt bekijken, Unified rapporten moet uitvoeren en alle mogelijkheden van Security Center kunt benutten voor zowel ingebouwde als partner aanbevelingen. U kunt ook Security Center aanbevelingen exporteren naar partner producten.

Meer [informatie over intelligente beveiligings koppelingen van micro soft](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Geavanceerde integraties met export van aanbevelingen en waarschuwingen (preview-versie)

Om scenario's op ondernemings niveau boven op Security Center in te scha kelen, is het nu mogelijk om Security Center waarschuwingen en aanbevelingen op extra plaatsen te gebruiken, met uitzonde ring van de Azure Portal of API. Deze kunnen rechtstreeks worden geëxporteerd naar een event hub en naar Log Analytics-werk ruimten. Hier volgen enkele werk stromen die u kunt maken rond deze nieuwe mogelijkheden:

- Met exporteren naar Log Analytics werk ruimte kunt u aangepaste Dash boards maken met Power BI.
- Met exporteren naar Event hub kunt u Security Center waarschuwingen en aanbevelingen exporteren naar uw Siem's van derden, naar een oplossing van derden in real-time of Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>On-premises servers onboarden naar Security Center vanuit het Windows-beheer centrum (preview-versie)

Windows-beheer centrum is een beheer portal voor Windows-servers die niet zijn geïmplementeerd in Azure en die geen verschillende Azure-beheer mogelijkheden, zoals back-up-en systeem updates, aanbieden. We hebben onlangs een mogelijkheid toegevoegd om deze niet-Azure-servers te laten beveiligen door deze rechtstreeks vanuit de Windows-beheer centrum-ervaring op te ruimen.

Met deze nieuwe ervaring hebben gebruikers een WAC-server voor Azure Security Center en kunnen ze de beveiligings waarschuwingen en aanbevelingen rechtstreeks in de Windows-beheer centrum-ervaring weer geven.


## <a name="september-2019"></a>September 2019

De updates in september zijn onder meer:

 - [Regels beheren met verbeteringen voor besturings elementen voor adaptieve toepassingen](#managing-rules-with-adaptive-application-controls-improvements)
 - [Aanbevelingen voor controle container beveiliging met behulp van Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Regels beheren met verbeteringen voor besturings elementen voor adaptieve toepassingen

De ervaring van het beheren van regels voor virtuele machines met behulp van adaptieve toepassings besturings elementen is verbeterd. Met de adaptieve toepassings besturings elementen van Azure Security Center kunt u bepalen welke toepassingen kunnen worden uitgevoerd op uw virtuele machines. Naast een algemene verbetering van regel beheer, kunt u met een nieuw voor deel bepalen welke bestands typen worden beveiligd wanneer u een nieuwe regel toevoegt.

[Meer informatie over adaptieve toepassingsregelaars](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Aanbevelingen voor controle container beveiliging met behulp van Azure Policy

De aanbeveling van Azure Security Center voor het oplossen van beveiligings problemen in container beveiliging kan nu worden ingeschakeld of uitgeschakeld via Azure Policy.

Als u uw ingeschakelde beveiligings beleid wilt bekijken, opent u de pagina beveiligings beleid vanuit Security Center.


## <a name="august-2019"></a>Augustus 2019

De updates in augustus zijn onder meer:

 - [Just-in-time-VM-toegang voor Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Eén Klik op herbemiddeling om uw beveiligings postuur te verbeteren (preview-versie)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Beheer in meerdere tenants](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Just-in-time-VM-toegang voor Azure Firewall 

Just-in-time-VM-toegang voor Azure Firewall is nu algemeen beschikbaar. Gebruik het om uw Azure Firewall beveiligde omgevingen te beveiligen naast uw beveiligde NSG-omgevingen.

JIT-VM-toegang vermindert de bloot stelling aan netwerk volumetrische aanvallen door alleen beheerde toegang tot Vm's te bieden wanneer dit nodig is, met behulp van uw NSG-en Azure Firewall regels.

Wanneer u JIT voor uw virtuele machines inschakelt, kunt u een beleid maken dat bepaalt welke poorten moeten worden beveiligd, hoe lang de poorten open blijven en goedgekeurde IP-adressen van waaruit deze poorten toegankelijk zijn. Dit beleid helpt u de controle te houden over wat gebruikers kunnen doen wanneer ze toegang aanvragen.

Aanvragen worden vastgelegd in het Azure-activiteiten logboek, zodat u de toegang eenvoudig kunt bewaken en controleren. De just-in-time pagina helpt u ook snel bestaande Vm's te identificeren waarvoor JIT is ingeschakeld en Vm's waarvoor JIT wordt aanbevolen.

Meer informatie over [Azure Firewall](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Eén Klik op herbemiddeling om uw beveiligings postuur te verbeteren (preview-versie)

Secure Score is een hulp programma waarmee u de beveiligings postuur van uw werk belasting kunt beoordelen. Hiermee worden uw beveiligings aanbevelingen beoordeeld en worden prioriteiten voor u gegeven, zodat u weet welke aanbevelingen het eerst moeten worden uitgevoerd. Zo kunt u de meest ernstige beveiligings problemen vinden om het onderzoek te priori teren.

We hebben een nieuwe mogelijkheid toegevoegd waarmee u een aanbeveling kunt oplossen voor een groot aantal resources met één klik om het herstel van de configuratie van de beveiliging te vereenvoudigen en u te helpen uw beveiligde score snel te verbeteren.

Met deze bewerking kunt u de resources selecteren waarop u het herstel wilt Toep assen en een herstel actie starten waarmee de instelling namens u wordt geconfigureerd.

Zie in de [Naslag Gids voor beveiligings aanbevelingen voor](recommendations-reference.md)meer informatie over de aanbevelingen waarvoor snelle oplossing is ingeschakeld.


### <a name="cross-tenant-management"></a>Beheer in meerdere tenants

Security Center ondersteunt nu cross-Tenant beheer scenario's als onderdeel van Azure Lighthouse. Op die manier krijgt u inzicht in de beveiligings postuur van meerdere tenants in Security Center. 

Meer [informatie over de ervaring voor cross-Tenant beheer](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Juli 2019

### <a name="updates-to-network-recommendations"></a>Updates voor netwerk aanbevelingen

Azure Security Center (ASC) heeft nieuwe aanbevelingen van het netwerk gelanceerd en een aantal bestaande verbeteringen verbeterd. Nu met behulp van Security Center hebt u nog meer netwerk beveiliging voor uw resources. 

Meer [informatie over aanbevelingen voor het netwerk](recommendations-reference.md#recs-networking).


## <a name="june-2019"></a>Juni 2019

### <a name="adaptive-network-hardening---generally-available"></a>Adaptieve netwerk beveiliging: algemeen beschikbaar

Een van de grootste kwets bare Opper vlakken voor werk belastingen die worden uitgevoerd in de publieke Cloud, is verbindingen van en naar het open bare Internet. Onze klanten vinden het moeilijk om te weten welke NSG-regels (netwerk beveiligings groep) moeten worden uitgevoerd om ervoor te zorgen dat Azure-workloads alleen beschikbaar zijn voor de vereiste bronbereiken. Met deze functie leert Security Center het netwerk verkeer en de verbindings patronen van Azure-workloads en NSG regel aanbevelingen voor Internet gerichte virtuele machines. Zo kan onze klant beter hun netwerk toegangs beleid configureren en de bloot stelling aan aanvallen beperken. 

Meer [informatie over adaptieve netwerk beveiliging](security-center-adaptive-network-hardening.md).