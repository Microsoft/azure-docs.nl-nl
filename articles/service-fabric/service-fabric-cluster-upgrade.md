---
title: Azure Service Fabric-clusters upgraden
description: Meer informatie over opties voor het bijwerken van uw Azure Service Fabric-cluster
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 636d4cb11f7cc6780d560d3d0043a89c69840a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731106"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Azure Service Fabric-clusters bijwerken en bijwerken

Een Azure Service Fabric-cluster is een bron die u bezit, maar is gedeeltelijk beheerd door micro soft. In dit artikel worden de opties voor het bijwerken van uw Azure Service Fabric-cluster beschreven.

## <a name="automatic-versus-manual-upgrades"></a>Automatische versus hand matige upgrades

Het is essentieel om ervoor te zorgen dat uw Service Fabric cluster altijd een [ondersteunde runtime versie](service-fabric-versions.md)uitvoert. Telkens wanneer micro soft de release van een nieuwe versie van Service Fabric kondigt, wordt de vorige versie gemarkeerd voor het *einde van de ondersteuning* na mini maal 60 dagen vanaf die datum. Nieuwe releases worden aangekondigd op het [service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

Veer tien dagen vóór het verstrijken van de release van uw cluster wordt uitgevoerd, wordt er een status gebeurtenis gegenereerd waardoor het cluster een status *waarschuwing* krijgt. Het cluster behoudt een waarschuwings status totdat u een upgrade uitvoert naar een ondersteunde runtime versie.

U kunt uw cluster zo instellen dat automatische Service Fabric upgrades worden ontvangen wanneer deze door micro soft worden uitgebracht, of u kunt hand matig kiezen uit een lijst met momenteel ondersteunde versies. Deze opties zijn beschikbaar in de sectie **infrastructuur upgrades** van uw service Fabric cluster bron.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Selecteer Automatische of hand matige upgrades in het gedeelte ' infrastructuur upgrades ' van de cluster bron in Azure Portal.":::

U kunt ook de upgrade modus van het cluster instellen en een runtime versie selecteren [met een resource manager-sjabloon](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Automatische upgrades zijn de aanbevolen upgrade modus. deze optie zorgt ervoor dat uw cluster in een ondersteunde status blijft en voor delen van de nieuwste oplossingen en functies, terwijl u ook updates kunt plannen op een manier die ten minste wordt verstoord voor uw workloads met behulp van een [Wave-implementatie](#wave-deployment-for-automatic-upgrades) strategie.

## <a name="wave-deployment-for-automatic-upgrades"></a>Wave-implementatie voor automatische upgrades

Met Wave-implementatie kunt u de onderbreking van een upgrade naar uw cluster minimaliseren door het verval niveau van een upgrade te selecteren, afhankelijk van uw werk belasting. U kunt bijvoorbeeld een *test*  ->  *fase*  ->  *productie* -Wave-implementatie pijplijn instellen voor uw verschillende service Fabric-clusters om de compatibiliteit van een runtime-upgrade te testen voordat u deze toepast op uw productie werkbelastingen.

Als u wilt kiezen voor een Wave-implementatie, geeft u een van de volgende Wave-waarden voor uw cluster op (in de implementatie sjabloon):

* **Wave 0**: clusters worden bijgewerkt zodra een nieuwe service Fabric build wordt uitgebracht. Bedoeld voor test-en ontwikkelings clusters.
* **Wave 1**: clusters worden een week bijgewerkt (zeven dagen) nadat een nieuwe build is uitgebracht. Bedoeld voor pre-productie/faserings clusters.
* **Golf 2**: clusters worden twee weken (14 dagen) bijgewerkt nadat een nieuwe build is uitgebracht. Bedoeld voor productie clusters.

U kunt zich registreren voor e-mail meldingen met koppelingen naar meer informatie als een cluster upgrade mislukt. Zie [Wave-implementatie voor automatische upgrades](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades) om aan de slag te gaan.

## <a name="phases-of-automatic-upgrade"></a>Fasen van automatische upgrade

Micro soft onderhoudt de Service Fabric runtime code en configuratie die wordt uitgevoerd in een Azure-cluster. We voeren automatisch bewaakte upgrades naar de software uit op basis van de behoeften. Deze upgrades kunnen code, configuratie of beide zijn. Om de impact van deze upgrades voor uw toepassingen te minimaliseren, worden deze uitgevoerd in de volgende fasen:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: een upgrade wordt uitgevoerd met behulp van alle cluster status beleidsregels

Tijdens deze fase gaan de upgrades één upgrade domein per keer door en worden de toepassingen die in het cluster worden uitgevoerd, zonder uitval tijd uitgevoerd. Het cluster status beleid (voor status van het knoop punt en de status van de toepassing) wordt tijdens de upgrade gerespecteerd.

Als niet aan het cluster status beleid wordt voldaan, wordt de upgrade teruggedraaid en wordt er een e-mail bericht verzonden naar de eigenaar van het abonnement. Het e-mail bericht bevat de volgende informatie:

* Melding dat er een upgrade van het cluster is teruggedraaid.
* Voorgestelde herstel acties, indien aanwezig.
* Het aantal dagen (*n*) tot we fase 2 hebben uitgevoerd.

We proberen dezelfde upgrade nog een paar keer uit te voeren als er upgrades zijn mislukt om infrastructuur redenen. Na de *n* dagen vanaf de datum waarop het e-mail bericht is verzonden, gaan we verder met fase 2.

Als aan het cluster status beleid wordt voldaan, wordt de upgrade beschouwd als geslaagd en als voltooid gemarkeerd. Deze situatie kan zich voordoen tijdens de eerste upgrade of wanneer een upgrade opnieuw wordt uitgevoerd in deze fase. Er is geen e-mail bevestiging van een geslaagde uitvoering, om te voor komen dat er overmatig e-mails worden verzonden. Als u een e-mail bericht ontvangt, wordt een uitzonde ring met normale bewerkingen aangegeven. De meeste cluster upgrades worden verwacht zonder dat dit van invloed is op de beschik baarheid van uw toepassing.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: een upgrade wordt uitgevoerd door alleen een standaard status beleid te gebruiken

Het status beleid in deze fase wordt zodanig ingesteld dat het aantal toepassingen dat in orde was aan het begin van de upgrade, hetzelfde blijft tijdens het upgrade proces. Net als bij fase 1 gaan de upgrades fase 2 één upgrade domein tegelijk door en worden de toepassingen die in het cluster werden uitgevoerd, zonder uitval tijd uitgevoerd. Het cluster status beleid (een combi natie van de status van het knoop punt en de status van alle toepassingen die in het cluster worden uitgevoerd) wordt tijdens de upgrade gerespecteerd.

Als niet wordt voldaan aan de beleids regels voor de cluster status, wordt de upgrade teruggedraaid. Vervolgens wordt er een e-mail bericht verzonden naar de eigenaar van het abonnement. Het e-mail bericht bevat de volgende informatie:

* Melding dat er een upgrade van het cluster is teruggedraaid.
* Voorgestelde herstel acties, indien aanwezig.
* Het aantal dagen (n) totdat de fase 3 is uitgevoerd.

We proberen dezelfde upgrade nog een paar keer uit te voeren als er upgrades zijn mislukt om infrastructuur redenen. Een herinnerings-e-mail wordt een paar dagen verzonden voor n dagen. Na de n dagen vanaf de datum waarop het e-mail bericht is verzonden, gaan we verder met fase 3. De e-mail berichten die u in fase 2 toestuurt, moeten serieus worden genomen en er moeten herstel bewerkingen worden uitgevoerd.

Als aan het cluster status beleid wordt voldaan, wordt de upgrade beschouwd als geslaagd en als voltooid gemarkeerd. Dit kan gebeuren tijdens de eerste upgrade of wanneer een upgrade opnieuw wordt uitgevoerd in deze fase. Er is geen e-mail bevestiging van een geslaagde uitvoering.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: een upgrade wordt uitgevoerd door middel van een agressief status beleid

Deze status beleidsregels in deze fase zijn gericht op het volt ooien van de upgrade, in plaats van de status van de toepassingen. Enkele cluster upgrades eindigen in deze fase. Als uw cluster in deze fase wordt weer gegeven, is er een goede kans dat uw toepassing een slechte status krijgt en/of de beschik baarheid verliest.

Net als bij de andere twee fasen gaat fase 3-upgrades één upgrade domein per keer door.

Als niet aan het status beleid van het cluster wordt voldaan, wordt de upgrade teruggedraaid. We proberen dezelfde upgrade nog een paar keer uit te voeren als er upgrades zijn mislukt om infrastructuur redenen. Daarna wordt het cluster vastgemaakt, zodat er geen ondersteuning meer en/of upgrades meer worden ontvangen.

Er wordt een e-mail bericht met deze informatie verzonden naar de eigenaar van het abonnement, samen met de herstel bewerkingen. We verwachten niet dat clusters een status krijgen waarin fase 3 is mislukt.

Als aan het cluster status beleid wordt voldaan, wordt de upgrade beschouwd als geslaagd en als voltooid gemarkeerd. Dit kan gebeuren tijdens de eerste upgrade of wanneer een upgrade opnieuw wordt uitgevoerd in deze fase. Er is geen e-mail bevestiging van een geslaagde uitvoering.

## <a name="custom-policies-for-manual-upgrades"></a>Aangepaste beleids regels voor hand matige upgrades

U kunt aangepaste beleids regels opgeven voor hand matige cluster upgrades. Deze beleids regels worden telkens toegepast wanneer u een nieuwe runtime versie selecteert, waarmee het systeem wordt geactiveerd om de upgrade van het cluster te starten. Als u het beleid niet overschrijft, worden de standaard waarden gebruikt. Zie [aangepaste policies instellen voor hand matige upgrades](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades)voor meer informatie.

## <a name="other-cluster-updates"></a>Andere cluster updates

Buiten het upgraden van de runtime zijn er een aantal andere acties die u moet uitvoeren om uw cluster up-to-date te houden, met inbegrip van het volgende:

### <a name="managing-certificates"></a>Certificaten beheren

Service Fabric maakt gebruik van [X. 509-server certificaten](service-fabric-cluster-security.md) die u opgeeft wanneer u een cluster maakt om de communicatie tussen cluster knooppunten te beveiligen en clients te verifiëren. U kunt certificaten voor het cluster en de client toevoegen, bijwerken of verwijderen in de [Azure Portal](https://portal.azure.com) of met behulp van Power shell/Azure cli.  Lees voor meer informatie het onderwerp [certificaten toevoegen of verwijderen](service-fabric-cluster-security-update-certs-azure.md)

### <a name="opening-application-ports"></a>Toepassings poorten openen

U kunt toepassings poorten wijzigen door de Load Balancer resource-eigenschappen te wijzigen die zijn gekoppeld aan het knooppunt type. U kunt de Azure Portal gebruiken, maar u kunt ook Power shell/Azure CLI gebruiken. Lees [toepassings poorten openen voor een cluster](create-load-balancer-rule.md)voor meer informatie.

### <a name="defining-node-properties"></a>Knooppunt eigenschappen definiëren

Soms wilt u ervoor zorgen dat bepaalde werk belastingen alleen worden uitgevoerd op bepaalde typen knoop punten in het cluster. Een voor beeld: een bepaalde werk belasting kan Gpu's of Ssd's vereisen, terwijl anderen dat niet mogelijk is. Voor elk van de knooppunt typen in een cluster kunt u aangepaste knooppunt eigenschappen toevoegen aan cluster knooppunten. Plaatsings beperkingen zijn de instructies die zijn gekoppeld aan afzonderlijke services die worden geselecteerd voor een of meer knooppunt eigenschappen. Plaatsings beperkingen bepalen waar services moeten worden uitgevoerd.

Lees de [knooppunt eigenschappen en plaatsings beperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)voor meer informatie over het gebruik van plaatsings beperkingen, eigenschappen van knoop punten en hoe u deze definieert.

### <a name="adding-capacity-metrics"></a>Metrische gegevens over capaciteit toevoegen

Voor elk van de typen knoop punten kunt u aangepaste metrische gegevens voor capaciteit toevoegen die u in uw toepassingen wilt gebruiken om de belasting te rapporteren. Raadpleeg de Service Fabric cluster resource manager-documenten voor het [beschrijven van uw cluster](service-fabric-cluster-resource-manager-cluster-description.md) en [metrische gegevens en belasting](service-fabric-cluster-resource-manager-metrics.md)voor meer informatie over het gebruik van metrische gegevens over capaciteit voor het rapporteren van de belasting.

### <a name="customizing-settings-for-your-cluster"></a>Instellingen voor uw cluster aanpassen

Veel verschillende configuratie-instellingen kunnen worden aangepast op een cluster, zoals het betrouwbaarheids niveau van het cluster en de eigenschappen van knoop punten. Lees [service Fabric cluster Fabric-instellingen](service-fabric-cluster-fabric-settings.md)voor meer informatie.

### <a name="upgrading-os-images-for-cluster-nodes"></a>Installatie kopieën van het besturings systeem bijwerken voor cluster knooppunten

Het inschakelen van automatische installatie kopieën van besturings systemen voor uw Service Fabric cluster knooppunten is een best practice. Hiervoor zijn er verschillende vereisten voor het cluster en de stappen die u moet uitvoeren. Een andere optie is het gebruik van patch Orchestration Application (POA, een Service Fabric-toepassing waarmee het besturings systeem patches op een Service Fabric cluster zonder downtime wordt geautomatiseerd. Zie [patch het Windows-besturings systeem in uw service Fabric-cluster](service-fabric-patch-orchestration-application.md)voor meer informatie over deze opties.

## <a name="next-steps"></a>Volgende stappen

* [Service Fabric upgrades beheren](service-fabric-cluster-upgrade-version-azure.md)
* Uw [service Fabric cluster instellingen](service-fabric-cluster-fabric-settings.md) aanpassen
* [Uw cluster in-en uitschalen](service-fabric-cluster-scale-in-out.md)
* Meer informatie over [toepassings upgrades](service-fabric-application-upgrade.md)
