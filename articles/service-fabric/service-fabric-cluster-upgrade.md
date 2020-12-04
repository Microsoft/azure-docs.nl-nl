---
title: Een Azure Service Fabric-cluster upgraden
description: Meer informatie over het bijwerken van de versie of configuratie van een Azure Service Fabric-cluster, het instellen van de cluster update modus, het bijwerken van certificaten, het toevoegen van toepassings poorten, het uitvoeren van besturingssysteem patches en wat u kunt verwachten wanneer de upgrades worden uitgevoerd.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 028c91f85a6e318f7ea686c1bcd50262eb7c6bf1
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571025"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Een Azure Service Fabric-cluster upgraden en bijwerken

Voor elk modern systeem is het ontwerpen van een hoge mate van omvang voor het succes van uw product op lange termijn. Een Azure Service Fabric-cluster is een resource waarvan u de eigenaar bent, maar wordt gedeeltelijk beheerd door micro soft. In dit artikel wordt beschreven wat er automatisch wordt beheerd en wat u zelf kunt configureren.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>De infrastructuur versie beheren die op uw cluster wordt uitgevoerd

Zorg ervoor dat op uw cluster altijd een [ondersteunde infrastructuur versie](service-fabric-versions.md)wordt uitgevoerd. Telkens wanneer micro soft de release van een nieuwe versie van Service Fabric kondigt, wordt de vorige versie gemarkeerd voor het einde van de ondersteuning na mini maal 60 dagen vanaf die datum. Nieuwe releases worden aangekondigd op het [service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

14 dagen vóór het verstrijken van de release van het cluster wordt uitgevoerd, wordt er een status gebeurtenis gegenereerd waardoor het cluster een status waarschuwing krijgt. Het cluster behoudt een waarschuwings status totdat u een upgrade naar een ondersteunde infrastructuur versie uitvoert.

U kunt uw cluster zo instellen dat automatische infrastructuur upgrades worden ontvangen wanneer deze door micro soft worden uitgebracht of u kunt een ondersteunde infrastructuur versie selecteren waarop u het cluster wilt uitvoeren.  Lees voor meer informatie [de service Fabric versie van uw cluster bijwerken](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Infrastructuur upgrade gedrag tijdens automatische upgrades

Micro soft onderhoudt de infrastructuur code en configuratie die wordt uitgevoerd in een Azure-cluster. We voeren een automatische bewaakte upgrade naar de software uit op basis van de behoeften. Deze upgrades kunnen code, configuratie of beide zijn. Om ervoor te zorgen dat uw toepassing geen gevolgen heeft voor of minimale gevolgen als gevolg van deze upgrades, worden upgrades uitgevoerd in de volgende fasen:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: een upgrade wordt uitgevoerd met behulp van alle cluster status beleidsregels

Tijdens deze fase gaan de upgrades één upgrade domein per keer door en worden de toepassingen die in het cluster worden uitgevoerd, zonder uitval tijd uitgevoerd. Het cluster status beleid (voor status van het knoop punt en de status van de toepassing) wordt tijdens de upgrade gerespecteerd.

Als niet aan het cluster status beleid wordt voldaan, wordt de upgrade teruggedraaid en wordt er een e-mail bericht verzonden naar de eigenaar van het abonnement. Het e-mail bericht bevat de volgende informatie:

* Melding dat er een upgrade van het cluster is teruggedraaid.
* Voorgestelde herstel acties, indien aanwezig.
* Het aantal dagen (*n*) tot we fase 2 hebben uitgevoerd.

We proberen dezelfde upgrade nog een paar keer uit te voeren als er upgrades zijn mislukt om infrastructuur redenen. Na de *n* dagen vanaf de datum waarop het e-mail bericht is verzonden, gaan we verder met fase 2.

Als aan het cluster status beleid wordt voldaan, wordt de upgrade beschouwd als geslaagd en als voltooid gemarkeerd. Dit kan gebeuren tijdens de eerste upgrade of wanneer een upgrade opnieuw wordt uitgevoerd in deze fase. Er is geen e-mail bevestiging van een geslaagde uitvoering. Dit is om te voor komen dat u te veel e-mails verzendt. het ontvangen van een e-mail bericht moet worden gezien als een uitzonde ring op normaal. De meeste cluster upgrades worden verwacht zonder dat dit van invloed is op de beschik baarheid van uw toepassing.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: een upgrade wordt uitgevoerd door alleen een standaard status beleid te gebruiken

Het status beleid in deze fase wordt zodanig ingesteld dat het aantal toepassingen dat in orde was aan het begin van de upgrade, hetzelfde blijft voor de duur van het upgrade proces. Net als bij fase 1 gaan de upgrades fase 2 één upgrade domein tegelijk door en worden de toepassingen die in het cluster werden uitgevoerd, zonder uitval tijd uitgevoerd. Het cluster status beleid (een combi natie van de status van het knoop punt en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt gerespecteerd voor de duur van de upgrade.

Als niet wordt voldaan aan de beleids regels voor de cluster status, wordt de upgrade teruggedraaid. Vervolgens wordt er een e-mail bericht verzonden naar de eigenaar van het abonnement. Het e-mail bericht bevat de volgende informatie:

* Melding dat er een upgrade van het cluster is teruggedraaid.
* Voorgestelde herstel acties, indien aanwezig.
* Het aantal dagen (n) totdat de fase 3 is uitgevoerd.

We proberen dezelfde upgrade nog een paar keer uit te voeren als er upgrades zijn mislukt om infrastructuur redenen. Een herinnerings-e-mail wordt een paar dagen verzonden voor n dagen. Na de n dagen vanaf de datum waarop het e-mail bericht is verzonden, gaan we verder met fase 3. De e-mail berichten die u in fase 2 toestuurt, moeten serieus worden genomen en er moeten herstel bewerkingen worden uitgevoerd.

Als aan het cluster status beleid wordt voldaan, wordt de upgrade beschouwd als geslaagd en als voltooid gemarkeerd. Dit kan gebeuren tijdens de eerste upgrade of wanneer een upgrade opnieuw wordt uitgevoerd in deze fase. Er is geen e-mail bevestiging van een geslaagde uitvoering.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: een upgrade wordt uitgevoerd door middel van een agressief status beleid

Deze status beleidsregels in deze fase zijn gericht op het volt ooien van de upgrade, in plaats van de status van de toepassingen. Enkele cluster upgrades eindigen in deze fase. Als uw cluster deze fase krijgt, is er een goede kans dat uw toepassing een slechte status krijgt en/of de beschik baarheid verliest.

Net als bij de andere twee fasen gaat fase 3-upgrades één upgrade domein per keer door.

Als niet aan het status beleid van het cluster wordt voldaan, wordt de upgrade teruggedraaid. We proberen dezelfde upgrade nog een paar keer uit te voeren als er upgrades zijn mislukt om infrastructuur redenen. Daarna wordt het cluster vastgemaakt, zodat er geen ondersteuning meer en/of upgrades meer worden ontvangen.

Er wordt een e-mail bericht met deze informatie verzonden naar de eigenaar van het abonnement, samen met de herstel bewerkingen. We verwachten niet dat clusters een status krijgen waarin fase 3 is mislukt.

Als aan het cluster status beleid wordt voldaan, wordt de upgrade beschouwd als geslaagd en als voltooid gemarkeerd. Dit kan gebeuren tijdens de eerste upgrade of wanneer een upgrade opnieuw wordt uitgevoerd in deze fase. Er is geen e-mail bevestiging van een geslaagde uitvoering.

## <a name="manage-certificates"></a>Certificaten beheren

Service Fabric maakt gebruik van [X. 509-server certificaten](service-fabric-cluster-security.md) die u opgeeft wanneer u een cluster maakt om de communicatie tussen cluster knooppunten te beveiligen en clients te verifiëren. U kunt certificaten voor het cluster en de client toevoegen, bijwerken of verwijderen in de [Azure Portal](https://portal.azure.com) of met behulp van Power shell/Azure cli.  Lees voor meer informatie het onderwerp [certificaten toevoegen of verwijderen](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Toepassings poorten openen

U kunt toepassings poorten wijzigen door de Load Balancer resource-eigenschappen te wijzigen die zijn gekoppeld aan het knooppunt type. U kunt de Azure Portal gebruiken, maar u kunt ook Power shell/Azure CLI gebruiken. Lees [toepassings poorten openen voor een cluster](create-load-balancer-rule.md)voor meer informatie.

## <a name="define-node-properties"></a>Knooppunt eigenschappen definiëren

Soms wilt u ervoor zorgen dat bepaalde werk belastingen alleen worden uitgevoerd op bepaalde typen knoop punten in het cluster. Een voor beeld: een bepaalde werk belasting kan Gpu's of Ssd's vereisen, terwijl anderen dat niet mogelijk is. Voor elk van de knooppunt typen in een cluster kunt u aangepaste knooppunt eigenschappen toevoegen aan cluster knooppunten. Plaatsings beperkingen zijn de instructies die zijn gekoppeld aan afzonderlijke services die worden geselecteerd voor een of meer knooppunt eigenschappen. Plaatsings beperkingen bepalen waar services moeten worden uitgevoerd.

Lees de [knooppunt eigenschappen en plaatsings beperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)voor meer informatie over het gebruik van plaatsings beperkingen, eigenschappen van knoop punten en hoe u deze definieert.

## <a name="add-capacity-metrics"></a>Metrische gegevens over capaciteit toevoegen

Voor elk van de typen knoop punten kunt u aangepaste metrische gegevens voor capaciteit toevoegen die u in uw toepassingen wilt gebruiken om de belasting te rapporteren. Raadpleeg de Service Fabric cluster resource manager-documenten voor het [beschrijven van uw cluster](service-fabric-cluster-resource-manager-cluster-description.md) en [metrische gegevens en belasting](service-fabric-cluster-resource-manager-metrics.md)voor meer informatie over het gebruik van metrische gegevens over capaciteit voor het rapporteren van de belasting.

## <a name="set-health-policies-for-automatic-upgrades"></a>Status beleid voor automatische upgrades instellen

U kunt aangepaste status beleidsregels opgeven voor de upgrade van de infra structuur. Als u uw cluster hebt ingesteld op automatische infrastructuur upgrades, worden deze beleids regels toegepast op fase 1 van de automatische infrastructuur upgrades.
Als u uw cluster hebt ingesteld voor hand matige infrastructuur upgrades, worden deze beleids regels toegepast telkens wanneer u een nieuwe versie selecteert die het systeem activeert om de infrastructuur upgrade in uw cluster te starten. Als u het beleid niet overschrijft, worden de standaard waarden gebruikt.

U kunt het aangepaste status beleid opgeven of de huidige instellingen controleren onder de Blade upgrade van infrastructuur resources door de geavanceerde upgrade-instellingen te selecteren. Bekijk de volgende afbeelding voor instructies.

![Aangepast status beleid beheren][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Infrastructuur instellingen voor uw cluster aanpassen

Veel verschillende configuratie-instellingen kunnen worden aangepast op een cluster, zoals het betrouwbaarheids niveau van het cluster en de eigenschappen van knoop punten. Lees [service Fabric cluster Fabric-instellingen](service-fabric-cluster-fabric-settings.md)voor meer informatie.

## <a name="patch-the-os-in-the-cluster-nodes"></a>Het besturings systeem in de cluster knooppunten bijwerken

De patch Orchestration Application (POA) is een Service Fabric-toepassing waarmee het besturings systeem patches op een Service Fabric cluster zonder downtime wordt geautomatiseerd. De [patch Orchestration-toepassing voor Windows](service-fabric-patch-orchestration-application.md) kan worden geïmplementeerd in uw cluster om patches te installeren op een gegroepeerde manier terwijl de services steeds beschikbaar blijven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het aanpassen van een aantal van de [service Fabric-cluster infrastructuur instellingen](service-fabric-cluster-fabric-settings.md)
* Meer informatie over hoe u [uw cluster in-en uitschaalt](service-fabric-cluster-scale-in-out.md)
* Meer informatie over [toepassings upgrades](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
