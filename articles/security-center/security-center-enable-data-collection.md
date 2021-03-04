---
title: Automatisch agents implementeren voor Azure Security Center | Microsoft Docs
description: In dit artikel wordt beschreven hoe u automatische inrichting instelt van de Log Analytics agent en andere agents en extensies die worden gebruikt door Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: d9d0739704a9f5f16bdbde80661192b2f1ca9bb1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099417"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>Automatische inrichting van agents en uitbrei dingen van Azure Security Center configureren

Security Center verzamelt gegevens uit uw resources met behulp van de relevante agent of uitbrei dingen voor die bron en het type gegevens verzameling dat u hebt ingeschakeld. Gebruik de onderstaande precedures om ervoor te zorgen dat uw resource over het vereiste is dat in dit artikel wordt beschreven hoe u automatische inrichting van de Log Analytics agent en andere agents en extensies instelt die worden gebruikt door Azure Security Center

## <a name="prerequisites"></a>Vereisten
U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).

## <a name="availability"></a>Beschikbaarheid

| Aspect                  | Details                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Releasestatus:          | **Functie**: Automatisch inrichten is algemeen beschikbaar (GA)<br>**Agent en extensies**: De Log Analytics-agent voor Azure-VM's is algemeen beschikbaar. De Microsoft Dependency-agent is beschikbaar in de preview-versie. De invoegtoepassing voor Kubernetes-beleid is algemeen beschikbaar                |
| Prijzen:                | Gratis                                                                                                                                                                                                                         |
| Ondersteunde bestemmingen: | ![Ja](./media/icons/yes-icon.png) Azure-machines<br>![Nee](./media/icons/no-icon.png) Azure Arc-machines<br>![Nee](./media/icons/no-icon.png) Kubernetes-knooppunten<br>![Nee](./media/icons/no-icon.png) Virtuele-machineschaalsets |
| Clouds:                 | ![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov, China Gov, andere overheden                                                                                                      |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>Hoe worden gegevens Security Center verzameld?

Security Center verzamelt gegevens van uw virtuele Azure-machines (VM's), virtuele-machineschaalsets, IaaS-containers en niet-Azure-computers (waaronder on-premises computers) om deze te bewaken op beveiligingsproblemen en bedreigingen. 

Het verzamelen van gegevens is vereist om inzicht te krijgen in ontbrekende updates, onjuist geconfigureerde beveiligingsinstellingen voor het besturingssysteem, eindpuntbeveiligingsstatus, beveiliging van de status en beveiliging tegen bedreigingen. Het verzamelen van gegevens is alleen nodig voor rekenresources (VM's, virtuele-machineschaalsets, IaaS-containers en niet-Azure-computers). U kunt profiteren van Azure Security Center, zelfs als u geen agents inricht. U hebt echter beperkte beveiliging en de mogelijkheden die hierboven worden vermeld, worden niet ondersteund.  

Gegevens worden verzameld met:

- De **Log Analytics-agent**, die verschillende configuraties en gebeurtenislogboeken met betrekking tot beveiliging van de machine leest en de gegevens kopieert naar uw werkruimte voor analyse. Voorbeelden van dergelijke gegevens zijn: besturingssysteemtype en -versie, besturingssysteemlogboeken (Windows-gebeurtenislogboeken), actieve processen, computernaam, IP-adressen en aangemelde gebruiker.
- **Beveiligingsextensies**, zoals de [Azure Policy-invoegtoepassing voor Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), waarmee ook gegevens over gespecialiseerde resourcetypen aan Security Center kunnen worden verstrekt.

> [!TIP]
> Naarmate Security Center is gegroeid, is het ook mogelijk om meer typen resources te bewaken. Het aantal extensies is ook gegroeid. Automatische inrichting is nu uitgebreid, zodat meer resourcetypen kunnen worden ondersteund door gebruik te maken van de mogelijkheden van Azure Policy.

## <a name="why-use-auto-provisioning"></a>Wat is het nut van automatische inrichting?
Elk van de agents en extensies die op deze pagina worden beschreven *kunnen* handmatig worden geïnstalleerd. (Zie [Handmatige installatie van de Log Analytics-agent](#manual-agent).) Maar met **automatische inrichting** vermindert u de overhead voor beheer doordat alle vereiste agents en extensies op bestaande en nieuwe machines worden geïnstalleerd, waardoor een snellere beveiligingsdekking voor alle ondersteunde resources wordt gerealiseerd. 

Het is raadzaam om automatische inrichting in te schakelen, maar het is standaard uitgeschakeld.

## <a name="how-does-auto-provisioning-work"></a>Hoe werkt automatische inrichting?
De instellingen voor automatische inrichting van Security Center hebben een wisselknop voor elk type ondersteunde extensie. Wanneer u automatisch inrichten van een extensie inschakelt, wijst u het juiste beleid **Implementeren indien niet aanwezig** toe om ervoor te zorgen dat de extensie wordt ingericht op alle bestaande en toekomstige resources van dat type.

> [!TIP]
> Meer informatie over effecten van Azure Policy, zoals Implementeren indien niet aanwezig, vindt u in [Inzicht in de effecten van Azure Policy](../governance/policy/concepts/effects.md).


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>Automatische inrichting van de Log Analytics agent en uitbrei dingen inschakelen <a name="auto-provision-mma"></a>

Als automatisch inrichten aan staat voor de Log Analytics-agent, implementeert Security Center de agent op alle ondersteunde Azure-VM's en op alle nieuwe VM's die nog worden gemaakt. Zie [Ondersteunde platforms in Azure Security Center](security-center-os-coverage.md) voor een lijst met ondersteunde platforms.

Automatische inrichting van de Log Analytics-agent inschakelen:

1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.
1. Selecteer het betreffende abonnement.
1. Stel op de pagina **automatische inrichting** de status van de log Analytics agent in **op aan**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Automatische inrichting van de Log Analytics-agent inschakelen":::

1. Definieer in het deelvenster met de configuratieopties de werkruimte die gebruikt moet worden.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Configuratieopties voor het automatisch inrichten van Log Analytics-agents op VM's" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Azure-VM's verbinden met de door Security Center gemaakte standaard werkruimte(n)** : Security Center maakt een nieuwe resourcegroep en standaardwerkruimte op die geolocatie en verbindt de agent met die werkruimte. Als een abonnement VM's op meerdere geolocaties bevat, maakt Security Center meerdere werkruimten zodat er wordt voldaan aan de vereisten met betrekking tot de gegevensprivacy.

        De naamconventie voor de werkruimte en de resourcegroep is: 
        - Werkruimte: DefaultWorkspace-[abonnements-id]-[geolocatie] 
        - Resourcegroep: DefaultResourceGroup-[geo] 

        Security Center schakelt automatisch een Security Center-oplossing voor de werkruimte in volgens de prijscategorie voor het abonnement. 

        > [!TIP]
        > Bij vragen over standaard werkruimten zie:
        >
        > - [Word ik gefactureerd voor Azure Monitor-logboeken in de werkruimten die door Security Center zijn gemaakt?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Waar wordt de standaard Log Analytics-werkruimte gemaakt?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Kan ik de standaard werkruimten verwijderen die zijn gemaakt door Security Center?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Azure VM's verbinden met een andere werkruimte**: selecteer in de vervolgkeuzelijst de werkruimte waarin verzamelde gegevens moeten worden opgeslagen. De vervolgkeuzelijst bevat alle werkruimten in al uw abonnementen. U kunt deze optie gebruiken om gegevens te verzamelen van virtuele machines die worden uitgevoerd in verschillende abonnementen, en deze allemaal op te slaan in uw geselecteerde werkruimte.  

        Als u al een bestaande Log Analytics-werkruimte hebt, wilt u mogelijk dezelfde werkruimte gebruiken (vereist lees- en schrijfmachtigingen voor de werkruimte). Deze optie is handig als u een centrale werkruimte gebruikt in uw organisatie en u deze ook wilt gebruiken voor het verzamelen van beveiligingsgegevens. Meer informatie vindt u in [Toegang tot logboekgegevens en werkruimten beheren in Azure Monitor](../azure-monitor/logs/manage-access.md).

        Als voor de geselecteerde werk ruimte al een ' Security '-of ' SecurityCenterFree-oplossing is ingeschakeld, worden de prijzen automatisch ingesteld. Als dat niet het geval is, installeert u een Security Center-oplossing in de werkruimte:

        1. Open **Prijzen en instellingen** vanuit het menu van Security Center.
        1. Selecteer de werkruimte waarmee u verbinding wilt maken met de agents.
        1. Selecteer **Azure Defender aan** of **Azure Defender uit**.

1. Selecteer in de configuratie van de **Windows-beveiligingsgebeurtenissen** de hoeveelheid onbewerkte gebeurtenisgegevens die moet worden opgeslagen:
    - **Geen** : opslag voor beveiligingsgebeurtenissen uitschakelen. Dit is de standaardinstelling.
    - **Minimaal**: een kleine set gebeurtenissen voor als u het gebeurtenisvolume wilt minimaliseren.
    - **Algemeen**: een set gebeurtenissen die voldoet voor de meeste klanten en een volledige audittrail biedt.
    - **Alle gebeurtenissen**: voor klanten die willen controleren of alle gebeurtenissen zijn opgeslagen.

    > [!TIP]
    > Zie [Instellen van de beveiligingsgebeurtenisoptie op het niveau van de werkruimte](#setting-the-security-event-option-at-the-workspace-level) als u deze opties wilt instellen op het niveau van de werkruimte.
    > 
    > Zie [Windows-beveiligingsgebeurtenisopties voor de Log Analytics-agent](#data-collection-tier) voor meer informatie over deze opties.

1. Selecteer **Toepassen** in het configuratievenster.

1. Voor het inschakelen van de automatische inrichting van een extensie anders dan de Log Analytics-agent, gaat u als volgt te werk: 

    1. Als u automatisch inrichten inschakelt voor de micro soft-afhankelijkheids agent, moet u ervoor zorgen dat de Log Analytics-agent is ingesteld op automatisch implementeren.
    1. Zet voor de relevante extensie de wisselknop voor de status op **Aan**.

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Stel de wisselknop zo in dat automatische inrichting is ingeschakeld voor de Policy-invoegtoepassing K8s":::

    1. Selecteer **Opslaan**. Het Azure-beleid wordt toegewezen en er wordt een hersteltaak gemaakt.

        |Extensie  |Beleid  |
        |---------|---------|
        |Policy-invoegtoepassing voor Kubernetes|[Azure Policy-invoegtoepassing implementeren op Azure Kubernetes Service-clusters](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Microsoft Dependency Agent (preview) (Windows-VM's)|[Dependency Agent implementeren voor Windows-VM's](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Microsoft Dependency Agent (preview) (Linux-VM's)|[Dependency Agent implementeren voor Linux-VM's](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |||

1. Selecteer **Opslaan**. Als een werkruimte moet worden ingericht, kan de installatie van de agent tot wel 25 minuten duren.

1. U wordt gevraagd of u de bewaakte VM's die eerder waren verbonden met een standaardwerkruimte, opnieuw wilt configureren:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="Opties bekijken voor het opnieuw configureren van bewaakte VM's":::

    - **Nee**: uw nieuwe werkruimte-instellingen worden alleen toegepast op nieuw gedetecteerde VM's waarop de Log Analytics-agent niet is geïnstalleerd.
    - **Ja**: uw nieuwe werkruimte-instellingen worden toegepast op alle VM's, en elke VM die momenteel is verbonden met een werkruimte die met Security Center is gemaakt, wordt opnieuw verbonden met de nieuwe doelwerkruimte.

   > [!NOTE]
   > Als u **Ja** selecteert, moet u de werkruimten die zijn gemaakt door Security Center pas verwijderen als alle VM's opnieuw zijn verbonden met de nieuwe doelwerkruimte. Deze bewerking mislukt als een werkruimte te vroeg wordt verwijderd.


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Windows-beveiligingsgebeurtenisopties voor de Log Analytics-agent <a name="data-collection-tier"></a> 

Het selecteren van een laag voor gegevensverzameling in Azure Security Center heeft alleen invloed op de *opslag* van beveiligingsgebeurtenissen in de Log Analytics-werkruimte. De Log Analytics-agent blijft de beveiligingsgebeurtenissen verzamelen en analyseren die Security Center nodig heeft om u te beschermen tegen bedreigingen, ongeacht welk niveau beveiligingsgebeurtenissen u opslaat in uw Log Analytics-werkruimte. Als u ervoor kiest om beveiligingsgebeurtenissen op te slaan, kunt u gebeurtenissen vanuit die werkruimte onderzoeken, zoeken en controleren.

### <a name="requirements"></a>Vereisten 
Azure Defender is vereist voor het opslaan van gegevens van Windows-beveiligingsgebeurtenissen. [Meer informatie over Azure Defender](azure-defender.md).

Voor het opslaan van gegevens in Log Analytics worden mogelijk extra kosten voor gegevensopslag in rekening gebracht. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

### <a name="information-for-azure-sentinel-users"></a>Informatie voor Azure Sentinel-gebruikers 
Gebruikers van Azure Sentinel: houd er rekening mee dat de verzameling van beveiligingsgebeurtenissen binnen de context van één werkruimte kan worden geconfigureerd vanuit Azure Security Center of Azure Sentinel, maar niet beide. Als u van plan bent om Azure Sentinel toe te voegen aan een werkruimte die al waarschuwingen van Azure Security Center ontvangt en is ingesteld op het verzamelen van beveiligingsgebeurtenissen, hebt u twee opties:
- Laat de verzameling van beveiligingsgebeurtenissen in Azure Security Center ongewijzigd. U kunt deze gebeurtenissen in Azure Sentinel en in Azure Defender doorzoeken en analyseren. U kunt echter niet de verbindingsstatus van de connector controleren of de configuratie ervan wijzigen in Azure Sentinel. Als dit belangrijk voor u is, kunt u de tweede optie overwegen.
- Schakel het verzamelen van beveiligingsgebeurtenissen in Azure Security Center uit (door **Windows-beveiligingsgebeurtenissen** in te stellen op **Geen** in de configuratie van uw Log Analytics-agent). Voeg vervolgens de connector voor beveiligingsgebeurtenissen toe aan Azure Sentinel. Net zoals bij de eerste optie, kunt u gebeurtenissen in Azure Sentinel en Azure Defender/ASC opvragen en analyseren, maar u kunt nu de verbindingsstatus van de connector bewaken of de configuratie ervan wijzigen in (en alleen in) Azure Sentinel.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Welke gebeurtenistypen worden er opgeslagen voor 'Algemeen' en 'Minimaal'?
Deze sets zijn ontworpen om typische scenario's te ondersteunen. Controleer welke set aan uw behoeften voldoet voordat u deze implementeert.

Bij het bepalen van de gebeurtenissen voor de opties **Algemeen** en **Minimaal** hebben we samengewerkt met klanten, en industriestandaards gebruikt om de ongefilterde frequentie van elke gebeurtenis en het gebruik ervan te weten te komen. Bij dit proces hebben we de volgende richtlijnen gebruikt:

- **Minimaal**: ervoor zorgen dat deze set alleen gebeurtenissen bevat die kunnen wijzen op een geslaagde inbraak, en belangrijke gebeurtenissen met een zeer laag volume. Deze set bevat bijvoorbeeld geslaagde en mislukte aanmeldingspogingen van gebruikers (gebeurtenis-id's 4624 en 4625), maar geen afmeldingen; die zijn belangrijk voor de controle, maar zijn niet belangrijk voor detectie en hebben een betrekkelijk hoog volume. Het grootste deel van het gegevensvolume van deze set bestaat uit de aanmeldingsgebeurtenissen en de procesaanmaakgebeurtenis (gebeurtenis-id 4688).
- **Algemeen**: een volledig auditlogboek bieden in deze set. Deze set bevat bijvoorbeeld zowel aanmeldingen als afmeldingen van gebruikers (gebeurtenis-ID 4634). Hierin zijn controleacties opgenomen zoals wijzigingen van beveiligingsgroep, belangrijke Kerberos-bewerkingen voor domeincontrollers, en andere gebeurtenissen die worden aanbevolen door brancheorganisaties.

Gebeurtenissen met een zeer laag volume zijn opgenomen in de set Algemeen, aangezien de belangrijkste motivatie om deze te verkiezen boven Alle gebeurtenissen het verminderen van het volume is, en niet om specifieke gebeurtenissen eruit te filteren.

Hier volgt een volledige uitsplitsing van de gebeurtenis-id's van Security en App Locker voor elke set:

| Gegevenslaag | Verzamelde gebeurtenis-id's |
| --- | --- |
| Minimaal | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Algemeen | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Als u gebruikmaakt van GPO (groepsbeleidsobject), is het raadzaam om het controlebeleid voor procesaanmaakgebeurtenis 4688 en het veld *CommandLine* binnen gebeurtenis 4688 in te schakelen. Zie de [Veelgestelde vragen](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled) van Security Center voor meer informatie over procesaanmaakgebeurtenis 4688. Zie [Audit Policy Recommendations](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations) (aanbevelingen voor controlebeleid) voor meer informatie over deze controlebeleidsregels.
> -  Om het verzamelen van gegevens voor [besturingselementen voor adaptieve toepassingsregelaars](security-center-adaptive-application.md)in te schakelen, configureert Security Center een lokaal AppLocker-beleid in de controlemodus om alle toepassingen toe te staan. Dit zorgt ervoor dat AppLocker gebeurtenissen genereert die vervolgens door Security Center worden verzameld en gebruikt. Het is belangrijk te weten dat dit beleid niet wordt geconfigureerd op machines waarop al een AppLocker-beleid is geconfigureerd. 
> - Als u Windows-filterplatform [gebeurtenis-id 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156) wilt verzamelen, moet u [	Filterplatform-verbinding controleren](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) inschakelen (Auditpol / set / subcategory:"Filtering Platform Connection" /Success:Enable)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>De beveiligingsgebeurtenisoptie op het niveau van de werkruimte instellen

U kunt het niveau beveiligingsgebeurtenisgegevens definiëren dat moet worden opgeslagen op het niveau van de werkruimte.

1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center in Azure Portal.
1. Selecteer de relevante werkruimte. De enige gebeurtenissen voor het verzamelen van gegevens voor een werkruimte zijn de Windows-beveiligingsgebeurtenissen die op deze pagina worden beschreven.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Instellen welke gegevens van de beveiligingsgebeurtenis in een werkruimte moeten worden opgeslagen":::

1. Selecteer de hoeveelheid onbewerkte gebeurtenisgegevens die moeten worden opgeslagen en selecteer **Opslaan**.

## <a name="manual-agent-provisioning"></a>Handmatige agentinrichting <a name="manual-agent"></a>
 
De Log Analytics-agent handmatig installeren:

1. Schakel automatische inrichting uit.

1. Maak desgewenst een werkruimte.

1. Schakel Azure Defender in voor de werkruimte waarin u de Log Analytics-agent installeert:

    1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.

    1. Stel de werkruimte in waar u de agent gaat installeren. Zorg ervoor dat de werkruimte tot hetzelfde abonnement behoort dat u in Security Center gebruikt, en dat u voor de werkruimte lees- en schrijfmachtigingen hebt.

    1. Selecteer **Azure Defender aan** en **Opslaan**.

       >[!NOTE]
       >Als voor de werkruimte al een **Security**- of **SecurityCenterFree**-oplossing is ingeschakeld, wordt de prijs automatisch ingesteld. 

1. Als u agents op nieuwe VM's wilt implementeren met behulp van een Resource Manager-sjabloon, installeert u de Log Analytics-agent:

   - [De Log Analytics-agent voor Windows installeren](../virtual-machines/extensions/oms-windows.md)
   - [De Log Analytics-agent voor Linux installeren](../virtual-machines/extensions/oms-linux.md)

1. Als u agents wilt implementeren op uw bestaande VM's, volgt u de instructies in [Gegevens verzamelen over Azure Virtual Machines](../azure-monitor/vm/quick-collect-azurevm.md) (de sectie **Gebeurtenis- en prestatiegegevens verzamelen** is optioneel).

1. Als u PowerShell wilt gebruiken om de agents te implementeren, gebruikt u de instructies in de documentatie voor virtuele machines:

    - [Voor Windows-machines](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Voor Linux-machines](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> Voor instructies over het onboarden van Security Center met behulp van PowerShell raadpleegt u [Het onboarden van Azure Security Center automatiseren met PowerShell](security-center-powershell-onboarding.md).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatische inrichting in het geval van een reeds bestaande agentinstallatie <a name="preexisting"></a> 

In de volgende use-cases wordt aangegeven hoe automatische inrichting werkt in gevallen waarin er al een agent of extensie is geïnstalleerd. 

- **Log Analytics-agent is geïnstalleerd op de computer, maar niet als een extensie (directe agent)** : als de Log Analytics-agent rechtstreeks op de VM is geïnstalleerd (niet als een Azure-extensie), installeert Security Center de Log Analytics-agentextensie, en wordt de Log Analytics-agent mogelijk bijgewerkt naar de nieuwste versie.
De geïnstalleerde agent blijft rapporteren aan de reeds geconfigureerde werkruimte(n), en rapporteert bovendien aan de in Security Center geconfigureerde werkruimte (multihoming wordt ondersteund op Windows-machines).
Als de geconfigureerde werk ruimte een gebruikers werkruimte is (niet Security Center de standaard werkruimte), moet u de oplossing ' Security ' of ' SecurityCenterFree ' installeren voor Security Center om te beginnen met het verwerken van gebeurtenissen van Vm's en computers die aan die werk ruimte rapporteren.

    Voor Linux-machines wordt Agent-multihoming nog niet ondersteund. Als er een bestaande agentinstallatie wordt gedetecteerd, treedt er geen automatische inrichting op en wordt de configuratie van de machine niet gewijzigd.

    Voor bestaande machines op abonnementen die vóór 17 maart 2019 aan Security Center zijn toegevoegd, wordt de Log Analytics-agentextensie niet geïnstalleerd wanneer een bestaande agent wordt gedetecteerd, en de machine wordt niet beïnvloed. Zie voor deze machines de aanbeveling 'Resolve monitoring agent health issues on your machines' (Agentstatusproblemen op uw machines oplossen) om de agentinstallatieproblemen op deze machines op te lossen.
  
- **System Center Operations Manager-agent is geïnstalleerd op de machine**: Security Center installeert de Log Analytics-agentextensie naast de bestaande Operations Manager. De bestaande Operations Manager-agent blijft normaal aan de Operations Manager-server rapporteren. De Operations Manager-agent en Log Analytics-agent hebben gemeenschappelijke runtime-bibliotheken, die tijdens dit proces worden bijgewerkt naar de nieuwste versie. Als Operations Manager-agent versie 2012 is geïnstalleerd, schakel automatische inrichting dan **niet** in.

- **Er is een reeds bestaande VM-extensie aanwezig**:
    - Wanneer de bewakingsagent is geïnstalleerd als een extensie, staat de extensieconfiguratie slechts rapportage aan één werkruimte toe. Security Center overschrijft bestaande verbindingen met gebruikerswerkruimten niet. Security Center worden beveiligings gegevens van de virtuele machine opgeslagen in de werk ruimte die al is verbonden, op voor waarde dat de oplossing Security of SecurityCenterFree is geïnstalleerd. Tijdens die proces kan Security Center de extensie upgraden naar de nieuwste versie.
    - Als u wilt zien naar welke werkruimte de bestaande extensie gegevens verzendt, voert u de test uit om de [verbinding met Azure Security Center te valideren](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). U kunt ook Log Analytics-werkruimten openen, een werkruimte selecteren, de VM selecteren en kijken naar de verbinding met de Log Analytics-agent.
    - Als u een omgeving hebt waarin de Log Analytics-agent is geïnstalleerd op clientwerkstations en rapporteert aan een bestaande Log Analytics-werkruimte, kunt u de lijst met [door Azure Security Center ondersteunde besturingssystemen](security-center-os-coverage.md) bekijken om u ervan te verzekeren dat uw besturingssysteem wordt ondersteund. Zie [bestaande Log Analytics-klanten](./faq-azure-monitor-logs.md)voor meer informatie.
 

## <a name="disable-auto-provisioning"></a>Automatische inrichting <a name="offprovisioning"></a> uitschakelen

Wanneer u automatische inrichting uitschakelt, worden er geen agents ingericht op nieuwe VM's.

Automatische inrichting van een agent uitschakelen:

1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center in de portal.
1. Selecteer het betreffende abonnement.
1. Selecteer **Automatische inrichting**.
1. Zet voor de relevante agent de wisselknop voor de status op **Aan**.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Een wisselknop om automatische inrichting per agenttype in of uit te schakelen":::

1. Selecteer **Opslaan**. Als automatische inrichting is uitgeschakeld, wordt de sectie Configuratie van de standaardwerkruimte niet weergegeven:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Als automatische inrichting is uitgeschakeld, is de configuratiecel leeg":::


> [!NOTE]
>  Wanneer u automatische inrichting uitschakelt, wordt de Log Analytics-agent niet verwijderd van Azure-VM's waarop de agent al is ingericht. Zie voor meer informatie over het verwijderen van de OMS-extensie [Hoe kan ik OMS-extensies die door Security Center zijn geïnstalleerd, verwijderen?](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Problemen oplossen

-   Zie [agent status problemen controleren](security-center-troubleshooting-guide.md#mon-agent)om problemen met de automatische inrichting van de installatie te identificeren.
-  Zie [Problemen oplossen met de netwerkvereisten voor de Monitoring Agent ](security-center-troubleshooting-guide.md#mon-network-req) om de netwerkvereisten voor de bewakingsagent te identificeren.
-   Zie [Onboarding-problemen van Operations Management Suite oplossen](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) voor het identificeren van problemen met handmatige onboarding.



## <a name="next-steps"></a>Volgende stappen
Op deze pagina wordt uitgelegd hoe u automatisch inrichten voor de Log Analytics agent en andere Security Center extensies inschakelt. Ook wordt beschreven hoe u een Log Analytics werk ruimte definieert waarin de verzamelde gegevens worden opgeslagen. Beide bewerkingen zijn vereist om gegevensverzameling mogelijk te maken. Wanneer u gegevens opslaat in Log Analytics, ongeacht of u een nieuwe of bestaande werkruimte gebruikt, kunnen er extra kosten in rekening worden gebracht voor gegevensopslag. Zie [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor prijs informatie in de valuta van uw keuze en volgens uw regio.