---
title: Certificaat beheer in een Service Fabric cluster
description: Meer informatie over het beheren van certificaten in een Service Fabric cluster dat is beveiligd met X. 509-certificaten.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: a8a7e8954f3c9d5b54c2e1ed9caa330ef92d4512
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099503"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Certificaat beheer in Service Fabric clusters

Dit artikel heeft betrekking op de beheer aspecten van certificaten die worden gebruikt voor het beveiligen van communicatie in azure Service Fabric-clusters en vormt een aanvulling op de inleiding op [service Fabric cluster beveiliging](service-fabric-cluster-security.md) , evenals de uitleger op [X. 509 op certificaten gebaseerde verificatie in service Fabric](cluster-security-certificates.md). We gaan ervan uit dat de lezer bekend is met fundamentele beveiligings concepten, en ook met de besturings elementen die Service Fabric beschrijft voor het configureren van de beveiliging van een cluster.  

Aspecten die onder deze titel vallen:

* Wat is ' certificaat beheer ' precies?
* Rollen en entiteiten die betrokken zijn bij certificaat beheer
* Het traject van een certificaat
* Dieper een voor beeld
* Problemen oplossen en veelgestelde vragen

Maar eerst een disclaimer: in dit artikel wordt een poging gedaan om de theoretische zijde te koppelen met praktijk voorbeelden, waarvoor, goed, specifiekere Services, technologieën, enzovoort. Omdat een aanzienlijk deel van de doel groep micro soft-Internal is, verwijzen we naar Services, technologieën en producten die specifiek zijn voor Microsoft Azure. U kunt in het gedeelte met opmerkingen vragen om uitleg of richt lijnen, waarbij de specifieke informatie van micro soft niet van toepassing is op uw situatie.

## <a name="defining-certificate-management"></a>Certificaat beheer definiëren
Zoals we in het [aanvullende artikel](cluster-security-certificates.md)hebben gezien, is een certificaat een cryptografisch object dat in feite een asymmetrisch sleutel paar bindt met kenmerken die de entiteit vertegenwoordigen die het vertegenwoordigt. Het is echter ook een ' bederfelijkable ' object, in dat geval de levens duur beperkt is en vatbaar is voor inbreuk op onopzettelijke openbaar making, of een geslaagde crack kan een certificaat zodanig weer geven dat het beveiligings oogpunt onbruikbaar wordt. Dit betekent dat u certificaten moet wijzigen, hetzij regel matig of als reactie op een beveiligings incident. Een ander aspect van beheer (en is een volledig onderwerp) is het beveiligen van persoonlijke sleutels van certificaten of geheimen voor het beschermen van de identiteit van de entiteiten die betrokken zijn bij het inkopen en inrichten van certificaten. We verwijzen naar de processen en procedures die worden gebruikt om certificaten te verkrijgen en om deze veilig (en veilig) te transporteren naar waar ze nodig zijn als certificaat beheer. Sommige beheer bewerkingen, zoals inschrijving, beleids instelling en autorisatie-besturings elementen, vallen buiten het bereik van dit artikel. Nog steeds andere, zoals het inrichten, vernieuwen, opnieuw versleutelen of intrekken, zijn alleen incidenten gerelateerd aan Service Fabric; We gaan deze hier echter voor een zekere mate aanpakken, aangezien deze bewerkingen kunnen helpen bij het goed beveiligen van een cluster. 

Het doel is om het certificaat beheer zo veel mogelijk te automatiseren om te zorgen voor een ononderbroken Beschik baarheid van het cluster en beveiligings garanties te bieden, omdat het proces gebruiker-Touch-Free is. Dit doel is momenteel beschikbaar in azure Service Fabric-clusters; in de rest van het artikel wordt het certificaat beheer eerst opgebouwd en wordt de nadruk gelegd op het inschakelen van de functie voor automatisch rollover.

De onderwerpen in het bereik zijn met name:
  - hypo Thesen met betrekking tot de schei ding van de toewijzing van de eigenaar en het platform, in de context van het beheren van certificaten
  - de lange pijp lijn van certificaten van uitgifte naar verbruik
  - rotatie van het certificaat-waarom, hoe en wanneer
  - Wat kan er mogelijk mis gaan?

Aspect zoals het beveiligen/beheren van domein namen, het inschrijven van certificaten of het instellen van autorisatie controles voor het afdwingen van certificaat uitgifte valt buiten het bereik van dit artikel. Raadpleeg de registratie-instantie (RA) van uw favoriete open bare-sleutel infrastructuur (PKI)-service. Micro soft-interne consumenten: Neem contact op met Azure-beveiliging.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Rollen en entiteiten die betrokken zijn bij certificaat beheer
De beveiligings benadering in een Service Fabric cluster is het geval ' de cluster eigenaar het declareert, Service Fabric runtime dit afdwingt '. Dit betekent dat bijna geen van de certificaten, sleutels of andere referenties van identiteiten die deel uitmaken van de werking van een cluster afkomstig zijn van de service zelf. ze worden allemaal gedeclareerd door de eigenaar van het cluster. Daarnaast is de cluster eigenaar ook verantwoordelijk voor het inrichten van de certificaten in het cluster, het vernieuwen ervan naar behoefte en de beveiliging van de certificaten te allen tijde. In het bijzonder moet de cluster eigenaar ervoor zorgen dat:
  - certificaten die zijn gedeclareerd in het gedeelte NodeType van het cluster manifest, kunnen worden gevonden op elk knoop punt van dat type, volgens de [presentatie regels](cluster-security-certificates.md#presentation-rules)
  - de hierboven gedeclareerde certificaten worden geïnstalleerd inclusief de bijbehorende persoonlijke sleutels
  - certificaten die zijn gedeclareerd in de presentatie regels moeten de [validatie regels](cluster-security-certificates.md#validation-rules) door geven 

Service Fabric, voor het deel ervan, wordt ervan uitgegaan dat de volgende verantwoordelijkheden zijn:
  - zoeken/zoeken naar certificaten die overeenkomen met de declaraties in de cluster definitie  
  - verlenen van toegang tot de bijbehorende persoonlijke sleutels aan door Service Fabric beheerde entiteiten op basis van een behoefte
  - certificaten valideren volgens de vastgestelde aanbevolen procedures voor beveiliging en de cluster definitie
  - waarschuwingen verhogen over het verstrijken van certificaten of mislukte pogingen om de basis stappen van certificaat validatie uit te voeren
  - valideren (ten opzichte van een zekere mate) dat aan de certificaat-gerelateerde aspecten van de cluster definitie wordt voldaan door de onderliggende configuratie van de hosts 

Het volgt erop dat de belasting van het certificaat beheer (als actieve bewerkingen) alleen voor de cluster eigenaar valt. In de volgende secties worden alle beheer bewerkingen nader bekeken, met behulp van de beschik bare mechanismen en de invloed ervan op het cluster.

## <a name="the-journey-of-a-certificate"></a>Het traject van een certificaat
Laat ons de voortgang van een certificaat in de context van een Service Fabric cluster snel opnieuw bezoeken:

  1. Een domein eigenaar meldt zich aan bij de RA van een PKI, een domein of het onderwerp dat ze willen koppelen aan het verlenen van certificaten. de certificaten vormen op zijn beurt bewijs van eigendom van het domein of onderwerp
  2. De domein eigenaar wijst ook de identiteit van de gemachtigde aanvragers toe aan de RA-entiteiten die recht hebben op de inschrijving van certificaten bij het opgegeven domein of onderwerp. in Microsoft Azure is de standaard id-provider Azure Active Directory en geautoriseerde aanvragers worden aangeduid met hun bijbehorende AAD-identiteit (of via beveiligings groepen)
  3. Een geautoriseerde aanvrager schrijft zich vervolgens in een certificaat in met behulp van een service voor geheime beheer. in Microsoft Azure is de keuze van de SM'S Azure Key Vault (Azure), die het ophalen van geheimen/certificaten door geautoriseerde entiteiten veilig opslaat en toestaat. Azure wordt het certificaat ook opnieuw vernieuwd/opnieuw ingesteld zoals geconfigureerd in het bijbehorende certificaat beleid. (Azure gebruikt AAD als de ID-provider.)
  4. Een geautoriseerde Ophalende, waarnaar wordt verwezen als ' inrichtings agent ': haalt het certificaat op, inclusief de persoonlijke sleutel, van de kluis en installeert dit op de computers die als host fungeren voor het cluster
  5. De Service Fabric-service (die op elk knoop punt wordt uitgevoerd) verleent toegang tot het certificaat voor het toestaan van Service Fabric entiteiten; deze worden aangeduid door lokale groepen en gesplitst tussen ServiceFabricAdministrators en ServiceFabricAllowedUsers
  6. De Service Fabric runtime opent en gebruikt het certificaat voor het maken van Federatie of voor het verifiëren van inkomende aanvragen van geautoriseerde clients
  7. De inrichtings agent bewaakt het kluis certificaat en activeert de inrichtings stroom bij het detecteren van de verlenging; vervolgens werkt de cluster eigenaar de cluster definitie zo nodig bij om aan te geven dat de bedoeling het certificaat moet meenemen.
  8. De inrichtings agent of de cluster eigenaar is ook verantwoordelijk voor het opschonen/verwijderen van ongebruikte certificaten
  
Voor onze doel einden zijn de eerste twee stappen in de bovenstaande volg orde grotendeels niet gerelateerd; de enige verbinding is dat de algemene onderwerpnaam van het certificaat de DNS-naam is die in de cluster definitie is gedeclareerd.

Deze stappen worden hieronder beschreven. Let op de verschillen in de inrichting van de certificaten die respectievelijk worden aangegeven door de vinger afdruk en de algemene naam.

*Fig. 1.* Uitgifte-en inrichtings stroom voor certificaten die zijn gedeclareerd door de vinger afdruk.
![Inrichtings certificaten die zijn gedeclareerd door de vinger afdruk][Image1]

*Figuur 2.* Uitgifte-en inrichtings stroom voor certificaten die zijn gedeclareerd door de algemene onderwerpnaam.
![Het inrichtings certificaat dat is gedeclareerd door de algemene naam voor het onderwerp][Image2]

### <a name="certificate-enrollment"></a>Certificaat inschrijving
In dit onderwerp wordt gedetailleerd beschreven in de Key Vault [documentatie](../key-vault/certificates/create-certificate.md). Hier vindt u een samen vatting voor continuïteit en gemakkelijker Naslag informatie. Als u doorgaat met Azure als de context en Azure Key Vault gebruikt als de service van het geheim beheer, moet een geautoriseerde certificaat aanvrager ten minste machtigingen voor certificaat beheer hebben op de kluis die wordt verleend door de eigenaar van de kluis. de aanvrager moet zich als volgt aanmelden bij een certificaat:
    - Hiermee maakt u een certificaat beleid in Azure Key Vault (Azure), dat het domein/het onderwerp van het certificaat opgeeft, de gewenste verlener, het sleutel type en de lengte, het beoogde sleutel gebruik en meer; Zie [certificaten in azure Key Vault](../key-vault/certificates/certificate-scenarios.md) voor meer informatie. 
    - Hiermee maakt u een certificaat in dezelfde kluis met het hierboven opgegeven beleid. Dit genereert op zijn beurt een sleutel paar als kluis objecten, een aanvraag voor certificaat ondertekening die is ondertekend met de persoonlijke sleutel en die vervolgens wordt doorgestuurd naar de aangewezen verlener voor ondertekening
    - Zodra de uitgever (certificerings instantie) antwoordt met het ondertekende certificaat, wordt het resultaat in de kluis samengevoegd en is het certificaat beschikbaar voor de volgende bewerkingen:
      - onder {vaultUri}/certificates/{name}: het certificaat met de open bare sleutel en meta gegevens
      - onder {vaultUri}/Keys/{name}: de persoonlijke sleutel van het certificaat, beschikbaar voor cryptografische bewerkingen (terugloop/uitpakken, ondertekenen/controleren)
      - onder {vaultUri}/Secrets/{name}: het certificaat dat is inclusief de persoonlijke sleutel, dat kan worden gedownload als een niet-beveiligd PFX-of PEM-bestand  
    U herinnert zich dat een kluis certificaat, in feite, een chronologische regel van certificaat instanties is, door een beleid te delen. Certificaat versies worden gemaakt op basis van de levens duur en vernieuwings kenmerken van het beleid. Het wordt ten zeerste aanbevolen dat kluis certificaten geen onderwerpen of domeinen en DNS-namen delen. het kan storend zijn in een cluster voor het inrichten van certificerings instanties van verschillende kluis certificaten, met identieke onderwerpen, maar wel in essentie andere kenmerken, zoals verlener, sleutel gebruik, enzovoort.

Op dit punt bevindt zich in de kluis een certificaat dat gereed is voor gebruik. Naar:

### <a name="certificate-provisioning"></a>Certificaat inrichting
We hebben een ' inrichtings agent ' genoemd, de entiteit die het certificaat ophaalt, inclusief de persoonlijke sleutel, van de kluis en installeert op elk van de hosts van het cluster. (Intrekken dat Service Fabric geen certificaten inricht.) In onze context wordt het cluster gehost op een verzameling Azure Vm's en/of virtuele-machine schaal sets. In azure kan het inrichten van een certificaat van een kluis naar een VM-VMSS worden gerealiseerd met de volgende mechanismen: ervan uitgaande dat de inrichtings agent eerder ' Get '-machtigingen heeft verleend aan de kluis door de eigenaar van de kluis: 
  - ad-hoc: een operator haalt het certificaat op uit de kluis (als pfx/PKCS #12 of PEM) en installeert dit op elk knoop punt
  - Als een ' geheim ' van een virtuele-machine schaalset tijdens de implementatie: de compute-service haalt met behulp van de identiteit van de eerste partij namens de operator, het certificaat uit een kluis met implementatie van een sjabloon en installeert dit op elk knoop punt van de virtuele-machine schaalset ([zoals u dat](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)). Houd er rekening mee dat het inrichten van alleen versie-geheimen alleen mogelijk is
  - de [Key Vault VM-extensie](../virtual-machines/extensions/key-vault-windows.md)gebruiken; Hierdoor kunnen certificaten worden ingericht met behulp van versie-less-declaraties, waarbij de waargenomen certificaten periodiek worden vernieuwd. In dit geval wordt naar verwachting dat de VM-VMSS een [beheerde identiteit](../virtual-machines/security-policy.md#managed-identities-for-azure-resources)heeft, een identiteit waaraan toegang is verleend tot de kluis (s) die de geobserveerde certificaten bevatten.

Het ad-hoc mechanisme wordt niet aanbevolen om meerdere redenen, variërend van beveiliging tot Beschik baarheid en wordt hier niet verder besproken. Raadpleeg [certificaten in virtuele-machine schaal sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)voor meer informatie.

De VMSS-/Compute-based-inrichting presenteert de voor delen van beveiliging en beschik baarheid, maar biedt ook beperkingen. Het vereist-door het ontwerpen van certificaten als versie met geheimen, waardoor deze alleen geschikt is voor clusters die zijn beveiligd met certificaten die zijn gedeclareerd met de vinger afdruk. Daarentegen installeert de Key Vault inrichting op basis van VM-extensies altijd de nieuwste versie van elk waargenomen certificaat, waardoor het alleen geschikt is voor clusters die zijn beveiligd met certificaten die zijn gedeclareerd door de algemene naam van het onderwerp. Gebruik niet een mechanisme voor het inrichten van automatisch vernieuwen (zoals de KVVM-extensie) voor certificaten die zijn gedeclareerd door een instantie (dat wil zeggen, via de vinger afdruk): het risico dat de beschik baarheid verloren gaat, is aanzienlijk.

Er kunnen andere inrichtings mechanismen bestaan; de bovenstaande worden momenteel geaccepteerd voor Azure Service Fabric-clusters.

### <a name="certificate-consumption-and-monitoring"></a>Certificaat gebruik en-bewaking
Zoals eerder vermeld, is de Service Fabric runtime verantwoordelijk voor het zoeken naar en het gebruik van de certificaten die zijn gedeclareerd in de cluster definitie. In het artikel over [verificatie op basis van certificaten](cluster-security-certificates.md) wordt uitgelegd hoe service Fabric de presentatie-en validatie regels implementeert, en hier niet gestart. We kijken naar toegang en het verlenen van machtigingen, en voor het bewaken van de controle.

Intrekken dat certificaten in Service Fabric worden gebruikt voor een groot aantal doelen, van wederzijdse authenticatie in de federatieve laag tot TLS-verificatie voor de beheer eindpunten; Hiervoor moeten verschillende onderdelen of systeem services toegang hebben tot de persoonlijke sleutel van het certificaat. De Service Fabric-runtime scant regel matig het certificaat archief en zoekt naar overeenkomsten voor elk van de bekende presentatie regels; voor elk van de overeenkomende certificaten bevindt zich de bijbehorende persoonlijke sleutel en is de discretionaire toegangs beheer lijst bijgewerkt met machtigingen-doorgaans lezen en uitvoeren-verleend aan de respectieve identiteit die ze nodig hebben. (Dit proces wordt aangeduid als ' Acl's voor '.) Het proces wordt uitgevoerd op een uitgebracht van 1 minuut en omvat ook ' toepassings certificaten ', zoals die worden gebruikt om instellingen te versleutelen of als eindpunt certificaten. Acl's voor volgt de presentatie regels, dus is het belang rijk om te onthouden dat certificaten die zijn gedeclareerd door de vinger afdruk en die automatisch worden vernieuwd zonder dat de representatieve cluster configuratie-update niet toegankelijk is.    

### <a name="certificate-rotation"></a>Certificaat rotatie
Als een kantlijn notitie: IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) formeel definieert de [verlenging](https://tools.ietf.org/html/rfc3647#section-4.4.6) van een certificaat met dezelfde kenmerken als het certificaat dat wordt vervangen: de uitgever, de open bare sleutel en de gegevens van het onderwerp worden bewaard en [opnieuw gesleuteld](https://tools.ietf.org/html/rfc3647#section-4.4.7) als de uitgifte van een certificaat met een nieuw sleutel paar, en geen beperkingen voor de vraag of de uitgever kan veranderen. Gezien het verschil kan belang rijk zijn (Houd rekening met het geval van certificaten die zijn gedeclareerd door de algemene naam van het onderwerp bij het vastmaken van de uitgever), we kiezen voor de neutrale term ' Rotation ' om beide scenario's te behandelen. (Denk eraan dat bij informeel gebruik ' reverse ' wordt gebruikt om een nieuwe sleutel te genereren.) 

We hebben eerder gezien dat Azure Key Vault automatische draaiing van certificaten ondersteunt: het beleid voor het koppelen van certificaten definieert het tijdstip, hetzij per dagen vóór de verval datum of het percentage van de totale levens duur, wanneer het certificaat in de kluis wordt gedraaid. De inrichtings agent moet worden aangeroepen na dit moment en vóór het verlopen van het nu-vorige certificaat om dit nieuwe certificaat te distribueren naar alle knoop punten van het cluster. Service Fabric helpt bij het verhogen van status waarschuwingen wanneer de verval datum van een certificaat (en dat momenteel in gebruik is in het cluster) eerder is dan een vooraf bepaald interval. Een automatische inrichtings agent (de extensie van de sleutel kluis-VM) die is geconfigureerd om het kluis certificaat te observeren, zal regel matig de kluis pollen, de rotatie detecteren en het nieuwe certificaat ophalen en installeren. Voor het inrichten via de functie geheimen van de VM-VMSS is een geautoriseerde operator vereist voor het bijwerken van de virtuele machine/VMSS met de versie van de sleutel-kluis die overeenkomt met het nieuwe certificaat.

In beide gevallen wordt het geroteerde certificaat nu ingericht op alle knoop punten en we hebben het mechanisme beschreven dat wordt Service Fabric gebruiken om rotaties te detecteren. Laat ons kijken wat er gebeurt als volgende: er wordt aangenomen dat de rotatie is toegepast op het cluster certificaat dat is gedeclareerd door de algemene onderwerpnaam
  - voor nieuwe verbindingen binnen en in het cluster zoekt de Service Fabric-runtime het meest recente overeenkomende certificaat dat is uitgegeven (grootste waarde van de eigenschap ' NotBefore '). Opmerking: dit is een wijziging ten opzichte van eerdere versies van de Service Fabric runtime.
  - bestaande verbindingen worden bewaard/toegestaan om natuurlijke te verlopen of anderszins af te breken. een interne handler krijgt een melding dat er een nieuwe overeenkomst bestaat

> [!NOTE] 
> Vóór versie 7.2.445 (7,2 CU4) Service Fabric het meest verlopende certificaat geselecteerd (het certificaat met de meest rechtse eigenschap ' NotAfter ')

Dit is de volgende belang rijke opmerkingen:
  - Het vernieuwings certificaat kan worden genegeerd als de verval datum eerder is dan die van het certificaat dat momenteel in gebruik is.
  - De beschik baarheid van het cluster of van de gehoste toepassingen heeft voor rang op de richt lijn om het certificaat te draaien; het cluster zal uiteindelijk convergeren op het nieuwe certificaat, maar zonder timing garanties. Het volgt:
  - Het is mogelijk niet onmiddellijk duidelijk te weten dat het geroteerde certificaat de voorafgaande taak volledig heeft vervangen; de enige manier om ervoor te zorgen dat (voor cluster certificaten) de hostcomputers opnieuw opstarten. Houd er rekening mee dat het niet voldoende is om de Service Fabric-knoop punten opnieuw op te starten, als onderdelen van de kernelmodus die geen lease verbinding hebben in een cluster. Houd er ook rekening mee dat het opnieuw opstarten van de VM-VMSS kan leiden tot een tijdelijk verlies van Beschik baarheid. (Voor toepassings certificaten is het voldoende om de respectievelijke toepassings exemplaren opnieuw op te starten.)
  - Introductie van een opnieuw versleuteld certificaat dat niet aan de validatie regels voldoet, kan het cluster effectief opdelen. Het meest voorkomende voor beeld hiervan is het geval van een onverwachte Uitgever: de cluster certificaten worden gedeclareerd door de algemene naam van het onderwerp bij het vastmaken van de verlener, maar het geroteerde certificaat is uitgegeven door een nieuwe/niet-gedeclareerde Uitgever.     

### <a name="certificate-cleanup"></a>Certificaat opschonen
Op dit moment zijn er geen voorzieningen in azure voor expliciete verwijdering van certificaten. Het is vaak een niet-triviale taak om te bepalen of een bepaald certificaat op een bepaald moment wordt gebruikt. Dit is moeilijker voor toepassings certificaten dan voor cluster certificaten. Service Fabric zichzelf niet in de inrichtings agent bevindt, wordt een certificaat dat door de gebruiker is gedeclareerd in geen geval verwijderd. Voor de standaard inrichtings mechanismen:
  - Certificaten die zijn gedeclareerd als VM/VMSS-geheimen, worden ingericht als er wordt verwezen in de definitie van de virtuele machine/VMSS, en ze kunnen worden opgehaald uit de kluis (het verwijderen van een kluis/certificaat van een Secret zal mislukken de volgende VM/VMSS-implementaties; op dezelfde manier wordt de VM/VMSS-implementaties die verwijzen naar de geheime versie, niet meer uitgevoerd.
  - Eerdere versies van certificaten die zijn ingericht via de VM-extensie van de sleutel kluis, kunnen al dan niet aanwezig zijn op een VM-VMSS knoop punt. De agent haalt alleen de huidige versie op en installeert deze en verwijdert geen certificaten. Als u een knoop punt herstelt (dat gewoonlijk elke maand plaatsvindt), wordt het certificaat archief opnieuw ingesteld op de inhoud van de installatie kopie van het besturings systeem, zodat eerdere versies impliciet worden verwijderd. Als u een schaalset voor virtuele machines schaalt, is er echter alleen de huidige versie van de geobserveerde certificaten geïnstalleerd. Ga niet uit van de homogeniteit van knoop punten met betrekking tot geïnstalleerde certificaten.   

## <a name="simplifying-management---an-autorollover-example"></a>Beheer vereenvoudigen: een voor beeld van een autorollover
We hebben methoden, beperkingen, uiteengezette ingewikkelde regels en definities beschreven en ernstige voor spellingen van storingen aangebracht. Het is mogelijk dat de tijd om te laten zien hoe automatische certificaat beheer moet worden ingesteld om al deze problemen te voor komen. We doen dit in de context van een Azure Service Fabric-cluster dat wordt uitgevoerd op een PaaSv2-schaalset voor virtuele machines, met behulp van Azure Key Vault voor geheimen-beheer en het gebruik van beheerde identiteiten, als volgt:
  - De validatie van certificaten wordt gewijzigd van de vinger afdruk van het onderwerp en het vastmaken van de Uitgever: elk certificaat met een bepaald onderwerp van een bepaalde verlener wordt gelijkelijk vertrouwd
    - Certificaten worden inge schreven bij en opgehaald uit een vertrouwde Store (Key Vault) en vernieuwd door een agent. in dit geval wordt de VM-extensie voor de sleutel kluis
    - Het inrichten van certificaten wordt gewijzigd van implementatie-en op basis van de versie (zoals uitgevoerd door ComputeRP) naar na de implementatie en het gebruik van versie-minder sleutel kluis-Uri's
    - Toegang tot de sleutel kluis wordt verleend via door de gebruiker toegewezen beheerde identiteiten. de UA-identiteit wordt gemaakt en toegewezen aan de schaalset voor virtuele machines tijdens de implementatie
    - Na de implementatie wordt door de agent (de KV-extensie van de covirtualisatie) op elk knoop punt van de virtuele-machine schaalset geobserveerde certificaten gecontroleerd en vernieuwd. de rotatie van het certificaat is dus volledig geautomatiseerd, omdat SF automatisch het verse geldige certificaat zal ophalen

De reeks is volledig scriptbaar/geautomatiseerd en maakt het mogelijk dat een door de gebruiker aanraak vrije eerste implementatie van een cluster dat is geconfigureerd voor automatische rollover van certificaten. Hieronder vindt u gedetailleerde stappen. We gebruiken een combi natie van Power shell-cmdlets en-fragmenten van JSON-sjablonen. Dezelfde functionaliteit kan worden behaald met alle ondersteunde manieren van interactie met Azure.

> [!NOTE]
> In dit voor beeld wordt ervan uitgegaan dat er al een certificaat aanwezig is in de kluis. voor het inschrijven en vernieuwen van een door een sleutel kluis beheerd certificaat zijn vereiste hand matige stappen vereist, zoals eerder in dit artikel is beschreven. Voor productie omgevingen gebruikt u door de sleutel kluis beheerde certificaten. Hieronder vindt u een voor beeld van een specifiek script voor een micro soft-interne PKI.

> [!NOTE]
> De functie voor het door certificerings instanties uitgegeven certificaat is alleen zinvol voor certificaten. het gebruik van zelfondertekende certificaten, met inbegrip van deze die zijn gegenereerd bij het implementeren van een Service Fabric cluster in de Azure Portal, is nonsensical, maar kan nog wel worden gebruikt voor lokaal/door een ontwikkelaar gehoste implementaties, door de vinger afdruk van de verlener te declareren zodat deze hetzelfde is als het blad certificaat.

### <a name="starting-point"></a>Begin punt
Voor het boogere wordt de volgende begin status in rekening gebracht:
  - Het Service Fabric cluster bestaat en is beveiligd met een door een certificerings instantie uitgegeven certificaat dat is gedeclareerd door de vinger afdruk.
  - Het certificaat wordt opgeslagen in een kluis en ingericht als een virtuele-machine Scale set-geheim
  - Hetzelfde certificaat wordt gebruikt om het cluster te converteren naar algemene certificaat declaraties op basis van namen, en kan dus worden gevalideerd door het onderwerp en de verlener. Als dit niet het geval is, moet u het door de certificerings instantie verleende certificaat ophalen dat is bedoeld voor dit doel einde en dit toevoegen aan de cluster definitie via de vinger afdruk zoals [hier](service-fabric-cluster-security-update-certs-azure.md) wordt uitgelegd

Dit is een JSON-fragment van een sjabloon die overeenkomt met een dergelijke status: Houd er rekening mee dat de vereiste instellingen worden wegge laten en illustreert alleen de aan het certificaat gerelateerde aspecten:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

In het bovenstaande wordt in wezen aangegeven dat het certificaat met de vinger afdruk ```json [parameters('primaryClusterCertificateTP')] ``` en de sleutel kluis ```json [parameters('clusterCertificateUrlValue')] ``` -URI is gedeclareerd als het enige certificaat van het cluster, via de vinger afdruk. Vervolgens stellen we de extra resources in die nodig zijn om de autorollover van het certificaat te garanderen.

### <a name="setting-up-prerequisite-resources"></a>Vereiste resources instellen
Zoals eerder vermeld, wordt een certificaat dat is ingericht als een geheim voor een schaalset voor virtuele machines, opgehaald uit de kluis door de service micro soft. Compute resource provider, met behulp van de identiteit van de eerste partij en namens de implementatie operator. Voor automatische rollover wordt dat gewijzigd: er wordt overgeschakeld naar het gebruik van een beheerde identiteit die is toegewezen aan de virtuele-machine schaalset en die machtigingen heeft voor de geheimen van de kluis.

Alle volgende fragmenten moeten worden geïmplementeerd concomitantly: ze worden afzonderlijk weer gegeven voor analyse en uitleg bij Play-by-Play.

Eerst een door de gebruiker toegewezen identiteit definiëren (standaard waarden zijn opgenomen als voor beelden): Raadpleeg de [officiële documentatie](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) voor actuele informatie.
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Ken deze identiteit vervolgens toegang tot de kluis geheimen-Raadpleeg de [officiële documentatie](/rest/api/keyvault/vaults/updateaccesspolicy) voor actuele informatie:
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

In de volgende stap:
  - de door de gebruiker toegewezen identiteit toewijzen aan de schaalset voor virtuele machines
  - de afhankelijkheid van de virtuele-machine Scale set declareren bij het maken van de beheerde identiteit en op basis van het verlenen van toegang tot de kluis
  - Declareer de naam van de VM-extensie voor de virtuele machine, zodat er waargenomen certificaten worden opgehaald bij het opstarten ([officiële documentatie](../virtual-machines/extensions/key-vault-windows.md))
  - werk de definitie van de Service Fabric VM-extensie zodanig bij dat deze afhankelijk is van de KVVM-extensie en om het cluster certificaat te converteren naar de algemene naam (deze wijzigingen worden in één stap aangebracht, omdat ze onder het bereik van dezelfde resource vallen.)

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Opmerking: Hoewel hierboven niet expliciet wordt vermeld, is de URL van het kluis certificaat verwijderd uit de sectie ' OsProfile ' van de schaalset voor virtuele machines.
De laatste stap bestaat uit het bijwerken van de cluster definitie voor het wijzigen van de certificaat declaratie van de vinger afdruk in de algemene naam. hier worden ook de vinger afdrukken van de uitgever vastgemaakt:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

Op dit moment kunt u de hierboven vermelde updates uitvoeren in één implementatie. de Service Fabric resource Provider service splitst de cluster upgrade in verschillende stappen, zoals beschreven in het segment bij het [converteren van cluster certificaten van een vinger afdruk naar een algemene naam](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations).

### <a name="analysis-and-observations"></a>Analyse en waarnemingen
Dit gedeelte bevat een catch-all voor uitleg over de stappen die hierboven worden beschreven, en het tekenen van aandacht op belang rijke aspecten.

#### <a name="certificate-provisioning-explained"></a>Certificaten inrichten, uitleg
De KVVM-extensie, als inrichtings agent, wordt continu uitgevoerd op basis van een vooraf ingestelde frequentie. Als een waargenomen certificaat niet kan worden opgehaald, wordt het volgende in de regel voortgezet en vervolgens de sluimer stand tot de volgende cyclus. De SFVM-extensie, als de Boots Trapper-agent van het cluster, vereist de gedeclareerde certificaten voordat het cluster kan vormen. Dit betekent dat de uitbrei ding SFVM alleen kan worden uitgevoerd na het ophalen van de cluster certificaten die hier worden aangegeven door de ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` -component, en door de instelling van de KeyVaultVM-uitbrei ding ```json "requireInitialSync": true``` . Dit geeft aan de KVVM-uitbrei ding die bij de eerste uitvoering (na implementatie of opnieuw opstarten) de geobserveerde certificaten moet door lopen totdat alle fouten zijn gedownload. Als deze para meter wordt ingesteld op False en er een fout is opgetreden bij het ophalen van de cluster certificaten, resulteert dit in een fout in de cluster implementatie. Als een eerste synchronisatie met een onjuiste/ongeldige lijst van geobserveerde certificaten is vereist, resulteert dit in een fout in de KVVM-extensie en dus opnieuw, een fout bij het implementeren van het cluster.  

#### <a name="certificate-linking-explained"></a>Certificaat koppeling, uitleg
Mogelijk hebt u de vlag ' linkOnRenewal ' van de KVVM-extensie gezien en is het feit dat deze is ingesteld op false. We adresseren hier dieper aan het gedrag dat door deze vlag wordt geregeld en de implicaties voor het functioneren van een cluster. Houd er rekening mee dat dit gedrag specifiek is voor Windows.

Volgens de [definitie](../virtual-machines/extensions/key-vault-windows.md#extension-schema):

```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Certificaten die worden gebruikt om een TLS-verbinding tot stand te brengen, worden doorgaans [verkregen als een ingang](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) via de beveiligings serviceprovider van S-Channel. dat wil zeggen dat de client niet rechtstreeks toegang heeft tot de persoonlijke sleutel van het certificaat zelf. S-Channel ondersteunt omleiding (koppeling) van referenties in de vorm van een certificaat extensie ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)): als deze eigenschap is ingesteld, vertegenwoordigt de waarde van de vinger afdruk van het certificaat vernieuwen en wordt in plaats daarvan het gekoppelde certificaat geladen. De IT-lijst wordt door lopen totdat deze is verbonden met het ' laatste ' certificaat, een zonder een verlengings teken. Deze functie, wanneer dit verstandig wordt gebruikt, is een uitstekende beperking tegen het verlies van Beschik baarheid, veroorzaakt door verlopen certificaten (bijvoorbeeld). In andere gevallen kan dit de oorzaak zijn van uitval die lastig zijn om te diagnosticeren en te beperken. S-Channel voert het Trans Port van certificaten op hun verlengings eigenschappen onvoorwaardelijk uit, onafhankelijk van het onderwerp, verleners of andere specifieke kenmerken die deel uitmaken van de validatie van het resulterende certificaat door de client. Het is mogelijk dat het resulterende certificaat geen gekoppelde persoonlijke sleutel heeft, of dat de sleutel niet is ACLed aan de potentiële verbruiker van de gebruiker. 
 
Als koppelen is ingeschakeld, wordt bij het ophalen van een waargenomen certificaat van de kluis door de VM-extensie van de sleutel kluis geprobeerd overeenkomende, bestaande certificaten te vinden om deze te koppelen via de verlengings extensie-eigenschap. De overeenkomst is (exclusief) op basis van de alternatieve naam voor het onderwerp (SAN) en werkt als geïllustreerd hieronder.
Stel dat er twee bestaande certificaten zijn: A: CN = "Bureau-accessoires", SAN = {"alice.universalexports.com"}, vernieuwing = ' B: CN = "Bob-bits", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, verlenging = "'
 
Ga ervan uit dat een certificaat C wordt opgehaald door de KVVM ext: CN = "Mallory malware", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
Een SAN-lijst is volledig opgenomen in C, en dus een. verlengen = C. vinger afdruk; De SAN-lijst van B heeft een gemeen schappelijke snij punt met C, maar is nog niet volledig opgenomen, dus B. de verlenging blijft leeg.
 
Elke poging tot het aanroepen van AcquireCredentialsHandle (S-Channel) in deze status van certificaat A zal het verzenden van C naar de externe partij uiteindelijk beëindigen. In het geval van Service Fabric gebruikt het [Trans Port-subsysteem](service-fabric-architecture.md#transport-subsystem) van een cluster S-Channel voor wederzijdse verificatie, zodat het hierboven beschreven gedrag van invloed is op de fundamentele communicatie van het cluster. Als u het bovenstaande voor beeld voortzet en ervan uitgaand dat het cluster certificaat is, wat er nu gebeurt, is afhankelijk van het volgende:
  - Als de persoonlijke sleutel van C niet ACLd is naar het account waarmee de infra structuur wordt uitgevoerd, worden er fouten weer gegeven voor het verkrijgen van de persoonlijke sleutel (SEC_E_UNKNOWN_CREDENTIALS of vergelijkbaar)
  - Als de persoonlijke sleutel van C toegankelijk is, zien we autorisatie fouten die zijn geretourneerd door de andere knoop punten (CertificateNotMatched, niet-geautoriseerd enz.) 
 
In beide gevallen mislukt het Trans Port en kan het cluster uitvallen. de symptomen kunnen variëren. De koppeling is afhankelijk van de volg orde van de verlenging, die wordt bepaald door de volg orde van de lijst met waargenomen certificaten van de KVVM-uitbrei ding, het vernieuwings schema in de kluis of zelfs tijdelijke fouten waarmee de volg orde van het ophalen zou worden gewijzigd.

Voor het oplossen van dergelijke incidenten, raden we het volgende aan:
  - Combi neer geen San's van verschillende kluis certificaten. elk kluis certificaat moet een uniek doel hebben, en het onderwerp en het SAN moeten overeenkomen met de specificiteit
  - de algemene naam van het onderwerp opnemen in de SAN-lijst (zoals letterlijk, CN = <subject common name> )  
  - Als dit niet het geval is, schakelt u koppeling bij vernieuwen uit voor certificaten die zijn ingericht met de KVVM-extensie 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Waarom een door de gebruiker toegewezen beheerde identiteit gebruiken? Wat zijn de gevolgen van het gebruik ervan?
Aangezien het uit de bovenstaande JSON-fragmenten is afgeleid, is een specifieke sequentiëren van de bewerkingen/updates vereist om het succes van de conversie te garanderen en om de beschik baarheid van het cluster te behouden. Met name de resource van de virtuele-machine schaalset declareert en gebruikt de identiteit voor het ophalen van geheimen in één (van de perspectief van de gebruiker). De extensie van de Service Fabric-VM (die het cluster Boots trapt) is afhankelijk van de voltooiing van de VM-extensie van de sleutel kluis, afhankelijk van het ophalen van de waargenomen certificaten. De uitbrei ding KVVM maakt gebruik van de identiteit van de virtuele-machine schaalset voor toegang tot de kluis. Dit betekent dat het toegangs beleid op de kluis al is bijgewerkt vóór de implementatie van de virtuele-machine schaalset. 

Om het maken van een beheerde identiteit te verwijderen, of om deze toe te wijzen aan een andere resource, moet de implementatie operator de vereiste rol (ManagedIdentityOperator) hebben in het abonnement of de resource groep, naast de functies die nodig zijn voor het beheren van de andere resources waarnaar wordt verwezen in de sjabloon. 

Uit veiligheids oogpunt herinnert u zich dat de virtuele machine (schaalset) wordt beschouwd als een beveiligings grens ten aanzien van de Azure-identiteit. Dit betekent dat elke toepassing die op de virtuele machine wordt gehost, in principe een toegangs token voor de door de host beheerde identiteits toegangs tokens krijgt verkregen van het niet-geverifieerde IMDS-eind punt. Als u de virtuele machine als een gedeelde of multi tenant omgeving beschouwt, wordt deze methode voor het ophalen van cluster certificaten mogelijk niet aangegeven. Het is echter alleen geschikt voor het inrichtings mechanisme van certificaten.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Problemen oplossen en veelgestelde vragen

*V*: hoe kan ik via een programma worden inge schreven bij een door een sleutel kluis beheerd certificaat?
*A*: Zoek de naam van de certificaat verlener uit de documentatie van de sleutel kluis en vervang deze in het onderstaande script.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*V*: wat gebeurt er wanneer een certificaat wordt uitgegeven door een niet-gedeclareerde/niet-opgegeven Uitgever? Waar kan ik de volledige lijst met actieve uitgevers van een bepaalde PKI verkrijgen?
*A*: als in de verklaring van de certificaat verlener de uitgevende vinger afdrukken zijn opgegeven en de directe verlener van het certificaat niet is opgenomen in de lijst met vastgemaakte verleners, wordt het certificaat beschouwd als ongeldig-onafhankelijk van het feit of de hoofdmap wordt vertrouwd door de client. Daarom is het van essentieel belang om ervoor te zorgen dat de lijst met verleners actueel is/up-to-date is. De introductie van een nieuwe verlener is een zeldzame gebeurtenis en moet algemeen worden geopenbaard voordat de certificaten worden uitgegeven. 

Over het algemeen publiceert en onderhouden een PKI een verklaring van certificerings instructies, in overeenstemming met IETF [RFC 7382](https://tools.ietf.org/html/rfc7382). Naast andere informatie bevat alle actieve verleners. Het ophalen van deze lijst via een programma kan verschillen van een PKI naar een andere.   

Voor micro soft-Internal Pki's raadpleegt u de interne documentatie over de eind punten/Sdk's die worden gebruikt voor het ophalen van de geautoriseerde verleners; het is de verantwoordelijkheid van de cluster eigenaar om deze lijst regel matig te testen en ervoor te zorgen dat de cluster definitie *alle* verwachte verleners bevat.

*V*: worden meerdere pki's ondersteund? 
*A*: Ja; u mag niet meerdere CN-vermeldingen in het cluster manifest declareren met dezelfde waarde, maar kunnen verleners van meerdere Pki's die overeenkomen met dezelfde CN, weer geven. Het wordt niet aanbevolen om te voor komen dat certificaten worden uitgegeven door transparantie procedures voor certificaten. Maar omdat de migratie van de ene PKI naar een andere is, is dit een aanvaardbaar mechanisme.

*V*: wat moet ik doen als het huidige cluster certificaat niet door de certificerings instantie is uitgegeven of niet het beoogde onderwerp heeft? 
*A*: Verschaf een certificaat met het beoogde onderwerp en voeg dit toe aan de definitie van het cluster als secundair, op vinger afdruk. Zodra de upgrade is voltooid, start u een upgrade van een andere cluster configuratie om de certificaat declaratie te converteren naar de algemene naam. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
