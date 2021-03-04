---
title: 'Azure Defender voor servers: de voordelen en functies'
description: Meer informatie over de voordelen en functies van Azure Defender voor servers.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 36c32c4eefdaa1c7604f2b0f19e98cf6a6d4310d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096527"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Inleiding tot Azure Defender voor servers

Azure Defender voor servers voegt bedreigingsdetectie en geavanceerde beveiliging toe voor uw Windows- en Linux-machines.

Voor Windows wordt Azure Defender geïntegreerd met Azure-Services om uw op Windows gebaseerde computers te controleren en te beveiligen. Security Center toont de waarschuwingen en suggesties voor herstel van al deze services in een gemakkelijk te gebruiken indeling.

Voor Linux verzamelt Azure Defender controlerecords van Linux-machines met behulp van **auditd**, een van de meest voorkomende Linux-controleframeworks. auditd maakt deel uit van de mainline-kernel. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Wat zijn de voordelen van Azure Defender voor servers?

De bedreigingsdetectie- en beveiligingsmogelijkheden van Azure Defender voor servers zijn onder andere:

- **Geïntegreerde licentie voor Microsoft Defender voor Eindpunten (alleen Windows)** - Azure Defender voor servers bevat [Microsoft Defender voor Eindpunten](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Samen bieden ze uitgebreide mogelijkheden voor eindpuntdetectie en -reactie (EDR). Zie [uw eind punten beveiligen](security-center-wdatp.md)voor meer informatie.

    Wanneer Defender voor Eindpunten een bedreiging detecteert, wordt er een waarschuwing geactiveerd. De waarschuwing wordt weergegeven in Security Center. Vanuit Security Center kunt u ook naar de Defender voor Eindpunten-console draaien en een gedetailleerd onderzoek uitvoeren om het bereik van de aanval te ontdekken. Meer informatie over Microsoft Defender voor Eindpunten.

    > [!IMPORTANT]
    > De **Microsoft Defender voor Eindpunten**-sensor wordt automatisch ingeschakeld voor Windows-servers die gebruikmaken van Security Center.

- **Scannen voor evaluatie van beveiligingsproblemen voor VM's**: de beveiligingsprobleemscanner van Azure Security Center werkt op basis van Qualys. 

    Qualys ' scanner is een van de toonaangevende hulpprogram ma's voor realtime-identificatie van beveiligings problemen in Azure en hybride virtuele machines. U hebt geen Qualys-licentie of Qualys-account nodig. De scans worden naadloos uitgevoerd in Security Center. Zie voor meer informatie [de geïntegreerde oplossing voor de evaluatie van beveiligings problemen van Azure Defender voor Azure-en hybride computers](deploy-vulnerability-assessment-vm.md).

- **Just-in-time-VM-toegang (JIT)** : makers van bedreigingen jagen actief op toegankelijke machines met open beheerpoorten, zoals RDP of SSH. Al uw virtuele machines zijn potentiële doelen voor een aanval. Wanneer het lukt een VM aan te tasten, wordt deze gebruikt als ingangspunt om verdere resources in uw omgeving aan te vallen.

    Wanneer u Azure Defender voor servers inschakelt, kunt u just-in-time-VM-toegang gebruiken om binnenkomend verkeer naar uw Azure-VM's te blokkeren, zodat u minder kwetsbaar bent voor aanvallen maar tegelijkertijd eenvoudig verbinding met VM's kunt maken wanneer dat nodig is. Zie [informatie over JIT-VM-toegang](just-in-time-explained.md)voor meer informatie.

- **FIM (File Integrity Monitoring)** : met bestandsintegriteitscontole (FIM), ook wel bekend als wijzigingscontrole, worden bestanden en registers van het besturingssysteem, toepassingssoftware en andere gecontroleerd op wijzigingen die mogelijk duiden op een aanval. Er wordt een vergelijkingsmethode gebruikt om te bepalen of de huidige toestand van het bestand anders is dan bij de laatste scan van het bestand. U kunt deze vergelijking gebruiken om te bepalen of er geldige of verdachte wijzigingen zijn aangebracht in uw bestanden.

    Wanneer u Azure Defender voor servers inschakelt, kunt u FIM gebruiken om de integriteit van Windows-bestanden, uw Windows-registers en Linux-bestanden te valideren. Zie [File Integrity Monitoring in azure Security Center](security-center-file-integrity-monitoring.md)voor meer informatie.

- **Adaptive Application Controls (AAC)** : adaptieve toepassingsregelaars zijn een intelligente, automatische oplossing voor het definiëren van lijsten met toegestane toepassingen die bewezen veilig zijn voor uw machines.

    Wanneer u adaptieve toepassingsregelaars hebt ingeschakeld en geconfigureerd, krijgt u beveiligingswaarschuwingen als er andere toepassingen worden uitgevoerd dan degene die u als veilig hebt gedefinieerd. Zie voor meer informatie [besturings elementen voor adaptieve toepassingen gebruiken om de kwets bare Opper vlakken van uw computers te reduceren](security-center-adaptive-application.md).

- **Adaptive Network Hardening (ANH)** : het toepassen van netwerkbeveiligingsgroepen (NSG) om het verkeer van en naar resources te filteren, verbetert uw netwerkbeveiligingspostuur. Maar er kunnen toch nog enkele gevallen zijn waarin het werkelijke verkeer dat via de NSG stroomt een subset is van de gedefinieerde NSG-regels. In dergelijke gevallen kunt u het beveiligingspostuur verder verbeteren door de NSG-regels te versterken op basis van de werkelijke verkeerspatronen.

    Adaptieve netwerkbeveiliging biedt aanbevelingen voor verdere versterking van de NSG-regels. Het maakt gebruik van een machine learning-algoritme dat rekening houdt met werkelijk verkeer, bekende vertrouwde configuratie, bedreigingsinformatie en andere aanwijzingen voor aantasting, en geeft vervolgens aanbevelingen om alleen verkeer van bepaalde IP-/poort-tuples toe te staan. Zie [uw netwerk beveiligings postuur verbeteren met adaptieve netwerk](security-center-adaptive-network-hardening.md)beveiliging voor meer informatie.

- **Beveiliging van Docker-host**: Azure Security Center identificeert niet-beheerde containers die worden gehost op IaaS Linux-VM's of andere Linux-machines waarop Docker-containers worden uitgevoerd. Security Center evalueert doorlopend de configuraties van deze containers. Vervolgens worden ze vergeleken met de Docker-benchmark van het CIS (Center for Internet Security). Security Center bevat de volledige regelset van de CIS Docker-benchmark en waarschuwt u als uw containers niet voldoen aan een van de controles. Zie [hard uw docker-hosts beveiligen](harden-docker-hosts.md)voor meer informatie.

- **Detectie van bestandsloze aanvallen (alleen Windows)** : aanvallen zonder bestanden injecteren schadelijke payloads in het geheugen, om detectie door schijfscantechnieken te voorkomen. De payload van de aanvaller blijft vervolgens achter in het geheugen met aangetaste processen en kan allerlei schadelijke activiteiten uitvoeren.

  Met detectie van bestandsloze aanvallen detecteren geautomatiseerde forensische technieken de toolkits, technieken en gedragingen van bestandsloze aanvallen. Deze oplossing scant uw machine periodiek tijdens runtime, en extraheert inzichten rechtstreeks uit het geheugen van processen. Specifieke inzichten zijn de identificatie van: 

  - Bekende toolkits en software voor crypto-mining 

  - Shellcode, een klein blokje code dat meestal wordt gebruikt als de payload bij het misbruiken van een beveiligingsprobleem in software.

  - Schadelijk uitvoerbaar bestand dat wordt geïnjecteerd in het procesgeheugen

  Bestandsloze-aanvaldetectie genereert gedetailleerde beveiligingswaarschuwingen met beschrijvingen en aanvullende metagegevens over processen, zoals netwerkactiviteit. Dit versnelt de triage van waarschuwingen, correlatie en stroomafwaartse reactietijd. Deze benadering is een aanvulling voor op gebeurtenissen gebaseerde EDR-oplossingen en biedt meer detectiedekking.

  Zie de [Referentietabel van waarschuwingen](alerts-reference.md#alerts-windows) voor meer informatie over de waarschuwingen van bestandsloze-aanvaldetectie.

- **Integratie van Linux-auditd-waarschuwingen en Log Analytics-agent (alleen Linux)** : het auditd-systeem bestaat uit een subsysteem op kernelniveau, dat verantwoordelijk is voor het bewaken van systeemaanroepen. Het filtert ze met een opgegeven regelset en schrijft berichten voor ze naar een socket. Security Center integreert de functionaliteit van het auditd-pakket in de Log Analytics-agent. Met deze integratie kunnen auditd-gebeurtenissen in alle ondersteunde Linux-distributies worden verzameld, zonder dat hiervoor vereisten gelden.

    auditd-records worden verzameld, verrijkt en geaggregeerd in gebeurtenissen met behulp van de Log Analytics-agent voor Linux-agent. Security Center voegt doorlopend nieuwe analyses toe die gebruikmaken van Linux-signalen om schadelijk gedrag in Linux-machines in de cloud en on-premises te detecteren. Net als bij Windows-mogelijkheden kijken deze analyses naar verdachte processen, dubieuze aanmeldingspogingen, laden van kernel-modules en andere activiteiten. Deze activiteiten kunnen erop wijzen dat een machine wordt aangevallen of al is aangetast.  

    Zie de [Referentietabel met waarschuwingen](alerts-reference.md#alerts-linux) voor een lijst met Linux-waarschuwingen.


## <a name="simulating-alerts"></a>Waarschuwingen simuleren

U kunt waarschuwingen simuleren door een van de volgende playbooks te downloaden:

- Voor Windows: [Playbook voor Azure Security Center: Beveiligingswaarschuwingen](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Voor Linux: [Playbook voor Azure Security Center: Linux-detecties](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender voor servers. 

Raadpleeg de volgende artikelen voor gerelateerd materiaal: 

- Een waarschuwing kunt u altijd exporteren, ongeacht of deze door Security Center is gegenereerd of door Security Center is ontvangen vanuit een ander beveiligingsproduct. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel, een extern SIEM of een ander extern hulpprogramma, volgt u de instructies in [Waarschuwingen naar een SIEM exporteren](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Azure Defender inschakelen](enable-azure-defender.md)