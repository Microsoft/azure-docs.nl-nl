---
title: X. 509 authenticatie op basis van certificaten in een Service Fabric cluster
description: Meer informatie over verificatie op basis van certificaten in Service Fabric clusters en het detecteren, beperken en oplossen van aan certificaten gerelateerde problemen.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 8af0246e0e576f9877c4c5e3b1f1a4314ae29827
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901246"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X. 509 authenticatie op basis van certificaten in Service Fabric clusters

Dit artikel vormt een aanvulling op de inleiding tot [service Fabric cluster beveiliging](service-fabric-cluster-security.md)en gaat in de details van verificatie op basis van certificaten in service Fabric clusters. We gaan ervan uit dat de lezer bekend is met fundamentele beveiligings concepten en ook met de besturings elementen die Service Fabric beschrijft om de beveiliging van een cluster te beheren.  

Onderwerpen die onder deze titel vallen:

* Basis beginselen van verificatie op basis van certificaten
* Identiteiten en hun respectieve rollen
* Certificaat configuratie regels
* Problemen oplossen en veelgestelde vragen

## <a name="certificate-based-authentication-basics"></a>Basis beginselen van verificatie op basis van certificaten
In het geval van een korte refresher is een certificaat een instrument dat is bedoeld om informatie te binden over een entiteit (het onderwerp) aan het bezit van een paar asymmetrische cryptografische sleutels, en dus een kern constructie van open bare-sleutel cryptografie. De sleutels die door een certificaat worden vertegenwoordigd, kunnen worden gebruikt voor het beveiligen van gegevens, of voor het bewijzen van de identiteit van belang rijke houders; bij gebruik in combi natie met een PKI-systeem (open bare-sleutel infrastructuur) kan een certificaat extra eigenschappen van het onderwerp vertegenwoordigen, zoals het eigendom van een Internet domein, of bepaalde bevoegdheden die eraan worden verleend door de verlener van het certificaat (ook wel certificerings instantie of CA genoemd). Een gemeen schappelijke toepassing van certificaten biedt ondersteuning voor het cryptografische Transport Layer Security (TLS)-protocol, waardoor beveiligde communicatie mogelijk is via een computernet netwerk. Met name de client en server gebruiken certificaten om de privacy en integriteit van hun communicatie te waarborgen, en ook om wederzijdse verificatie uit te voeren.

In Service Fabric is de fundamentele laag van een cluster (Federatie) ook gebaseerd op TLS (onder andere protocollen) om een betrouwbaar, veilig netwerk van deelnemende knoop punten te bezorgen. Verbindingen met het cluster via Service Fabric-client-Api's gebruiken ook TLS om verkeer te beveiligen en ook om de identiteit van de partijen tot stand te brengen. In het bijzonder, wanneer dit wordt gebruikt voor verificatie in Service Fabric, kan een certificaat worden gebruikt om de volgende claims te bewijzen: a) de presentator van de certificaat referentie heeft de persoonlijke sleutel b van het certificaat, de SHA-1-hash (vinger afdruk) van het certificaat komt overeen met een declaratie die is opgenomen in de cluster definitie. en de uitgever van het certificaat bekend of vertrouwd is.

In de bovenstaande lijst wordt ' b ' colloquially ' vinger vastmaken ' genoemd. in dit geval verwijst de verklaring naar een specifiek certificaat en de sterkte van het verificatie schema berust op de voor waarde dat het niet haalbaar is om een certificaat te vervalsen dat dezelfde hashwaarde produceert als een andere, en nog steeds een geldig, goed gevormd object in alle andere opzichten. Item c vertegenwoordigt een alternatieve vorm van het declareren van een certificaat, waarbij de sterkte van het schema wordt gerust op de combi natie van het onderwerp van het certificaat en de uitgevende instantie. In dit geval verwijst de verklaring naar een klasse certificaten: twee certificaten met dezelfde kenmerken worden als volledig gelijkwaardig beschouwd. 

In de volgende secties wordt uitgelegd hoe de Service Fabric runtime certificaten gebruikt en valideert om te zorgen voor de beveiliging van het cluster.

## <a name="identities-and-their-respective-roles"></a>Identiteiten en hun respectieve rollen
Voordat u de details van verificatie of communicatie kanalen kunt beveiligen, is het belang rijk dat u de deelnemende actors en de bijbehorende rollen die ze in een cluster spelen, vermeldt:
- de Service Fabric runtime, aangeduid als ' System ': de set services die de abstractie en functionaliteit van het cluster bieden. Als er wordt verwezen naar de communicatie in het cluster tussen systeem instanties, gebruiken we de term ' cluster identiteit '; Wanneer u verwijst naar het cluster als ontvanger/doel verkeer van buiten het cluster, gebruiken we de term ' server identiteit '.
- gehoste toepassingen, aangeduid als ' toepassingen ': code die wordt aangeboden door de eigenaar van het cluster. dit wordt in het cluster ingedeeld en uitgevoerd
- clients: entiteiten die verbinding mogen maken met en de functionaliteit uitvoeren in een cluster, volgens de cluster configuratie. Er wordt respectievelijk onderscheid gemaakt tussen twee machtigings niveaus: User en admin. Een ' gebruiker '-client is voornamelijk beperkt tot alleen-lezen bewerkingen (maar niet alle alleen-lezen functionaliteit), terwijl een admin-client onbeperkte toegang tot de functionaliteit van het cluster heeft. (Zie [beveiligings rollen in een service Fabric cluster](service-fabric-cluster-security-roles.md)voor meer informatie.)
- (Alleen voor Azure) de Service Fabric services die controles voor de exploitatie en het beheer van Service Fabric-clusters, aangeduid als gewoon ' service ', indelen en beschikbaar stellen. Afhankelijk van de omgeving kan de ' service ' verwijzen naar de resource provider van Azure Service Fabric of andere resource providers die eigendom zijn van en worden beheerd door het Service Fabric team.

In een beveiligd cluster kan elk van deze rollen worden geconfigureerd met hun eigen unieke identiteit, gedeclareerd als het koppelen van een vooraf gedefinieerde rolnaam en de bijbehorende referentie. Service Fabric ondersteunt het declareren van referenties als certificaten of Service-Principal op basis van een domein. (Windows-/Kerberos-based-identiteiten worden ook ondersteund, maar vallen buiten het bereik van dit artikel; Raadpleeg op [Windows gebaseerde beveiliging in service Fabric clusters](service-fabric-windows-cluster-windows-security.md).) In azure-clusters kunnen client rollen ook worden gedeclareerd als [identiteiten op basis van Azure Active Directory](service-fabric-cluster-creation-setup-aad.md).

Zoals alluded hierboven, definieert de Service Fabric runtime twee bevoegdheids niveaus in een cluster: ' admin ' en ' gebruiker '. Een beheerder-client en een systeem onderdeel kunnen beide worden gebruikt met ' admin-bevoegdheden, en zijn dus niet van elkaar te onderscheiden. Bij het tot stand brengen van een verbinding in/met het cluster wordt een geauthenticeerde aanroeper verleend door de Service Fabric runtime een van de twee rollen als de basis voor de volgende autorisatie. In de volgende secties wordt de verificatie uitgebreid besproken.

## <a name="certificate-configuration-rules"></a>Certificaat configuratie regels
### <a name="validation-rules"></a>Validatie regels
De beveiligings instellingen van een Service Fabric cluster beschrijven in principe de volgende aspecten:
- het verificatie type; Dit is een onveranderbaar kenmerk van het cluster. Voor beelden van dergelijke instellingen zijn ' ClusterCredentialType ', ' ServerCredentialType ' en de toegestane waarden zijn ' none ', ' x509 ' of ' Windows '. Dit artikel is gericht op de x509-type verificatie.
- de validatie regels (verificatie); deze instellingen worden ingesteld door de cluster eigenaar en beschrijven welke referenties moeten worden geaccepteerd voor een bepaalde rol. Voor beelden worden onmiddellijk hieronder weer gegeven.
- instellingen die worden gebruikt om het resultaat van de verificatie te verfijnen of subtiel te wijzigen; Hier vindt u enkele voor beelden van vlaggen (de-) om het afdwingen van certificaatintrekkingslijsten te beperken, enzovoort.

> [!NOTE]
> Voor beelden van cluster configuratie die hieronder worden geboden, zijn fragmenten uit het cluster manifest in XML-indeling, zoals de meeste samen vatting. dit ondersteunt direct de Service Fabric functionaliteit die in dit artikel wordt beschreven. Dezelfde instellingen kunnen rechtstreeks worden weer gegeven in de JSON-representaties van een cluster definitie, ongeacht of er een zelfstandig JSON-cluster manifest of een Azure resource beheer-sjabloon is.

Een regel voor certificaat validatie bestaat uit de volgende elementen:
- de bijbehorende rol: Client, admin-client (geprivilegieerde rol)
- de referentie die is geaccepteerd voor de rol, aangegeven door de vinger afdruk of de algemene onderwerpnaam

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Declaraties voor certificaat validatie op basis van vinger afdruk
In het geval van op vinger afdruk gebaseerde validatie regels worden de referenties die zijn opgegeven door een beller die een verbinding in/naar het cluster aanvraagt als volgt gevalideerd:
  - de referentie is een geldig, goed gevormd certificaat: de bijbehorende keten kan worden gebouwd, de hand tekeningen komen overeen
  - Er is een tijd geldig voor het certificaat (NotBefore <= nu < NotAfter)
  - de SHA-1-hash van het certificaat komt overeen met de declaratie, omdat niet-hoofdletter gevoelige teken reeks vergelijking alle spaties uitsluiten

Eventuele vertrouwens fouten die zijn opgetreden tijdens het maken van een keten of validatie, worden onderdrukt voor declaraties op basis van een vinger afdruk, met uitzonde ring van verlopen certificaten-hoewel er ook voor dat geval voorzieningen bestaan. Met name de volgende fouten zijn opgetreden: de intrekkings status is onbekend of offline, niet-vertrouwde basis, ongeldig sleutel gebruik, worden beschouwd als niet-fatale fouten; de locatie is in dit geval dat het certificaat slechts een envelop voor een sleutel paar is: de beveiliging ligt in het feit dat de cluster eigenaar is ingesteld op posities meting om de persoonlijke sleutel te beveiligen.

Het volgende fragment van een cluster manifest exemplifies een set met validatie regels op basis van de vinger afdruk:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Elk van de bovenstaande vermeldingen verwijzen naar een specifieke identiteit zoals eerder beschreven. elke vermelding biedt ook de mogelijkheid om meerdere waarden op te geven als een door komma's gescheiden lijst met teken reeksen. In dit voor beeld, bij het valideren van de binnenkomende referenties, de presentator van een certificaat met de SHA-1-vinger afdruk 5ec... voor 4264 wordt de rol Admin verleend. daarentegen wordt een aanroeper geverifieerd met het certificaat 7c8f... 01b0 ' wordt een rol ' gebruiker ' toegekend, beperkt tot voornamelijk alleen-lezen bewerkingen. Een binnenkomende beller die een certificaat presenteert waarvan de vinger afdruk ' abcd... is. 1234 ' of ' ef01... 5678 ' wordt geaccepteerd als een knoop punt op hetzelfde niveau in het cluster. Ten slotte zal een client die verbinding maakt met een beheer eindpunt van het cluster, de vinger afdruk van het server certificaat op ' ef01... 5678 '. 

Zoals eerder vermeld, maakt Service Fabric bepalingen voor het accepteren van verlopen certificaten; de reden hiervoor is dat certificaten een beperkte levens duur hebben en, wanneer wordt aangegeven door een vinger afdruk (die verwijst naar een specifiek certificaat exemplaar), waardoor een certificaat verloopt als gevolg van het mislukken van de verbinding met het cluster of het samen vouwen van het cluster. Het is allemaal te gemakkelijk om te verg eten of Verwaarloos een door een vingerafdruk vastgemaakt certificaat en helaas is het herstel van een dergelijke situatie lastig.

Daartoe kan de eigenaar van het cluster expliciet aangeven dat zelfondertekende certificaten die zijn gedeclareerd door de vinger afdruk, als volgt worden beschouwd:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Dit gedrag wordt niet uitgebreid naar certificaten die zijn uitgegeven door de certificerings instantie. Als dat het geval was, zou het verlopen, een bekend, gemanipuleerd verouderd certificaat zouden kunnen worden ' geldig ' zodra het niet meer zou worden afgehandeld in de certificaatintrekkingslijst van de certificerings instantie en zo een beveiligings risico vormen. Met zelfondertekende certificaten wordt de cluster eigenaar beschouwd als de enige partij die verantwoordelijk is voor de beveiliging van de persoonlijke sleutel van het certificaat. Dit is niet het geval met certificaten die door de certificerings instantie zijn uitgegeven. de cluster eigenaar kan niet weten hoe of wanneer het certificaat is gedeclareerd.

#### <a name="common-name-based-certificate-validation-declarations"></a>Algemene declaraties voor certificaat validaties op basis van naam
Algemene declaraties op basis van namen hebben een van de volgende vormen:
- algemene naam van onderwerp (alleen)
- algemene naam onderwerp met verlener vastmaken

Laten we eerst een fragment overwegen van een cluster manifest exemplifying beide declaratie stijlen:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
De declaraties verwijzen respectievelijk naar de id's van de server en het cluster. Houd er rekening mee dat de op CN gebaseerde declaraties hun eigen secties hebben in het cluster manifest, gescheiden van de standaard beveiliging. In beide declaraties staat de ' naam ' voor de algemene onderwerpnaam van het certificaat en het veld ' waarde ' staat voor de verwachte uitgever, als volgt:

- in het eerste geval wordt met de verklaring aangegeven dat het algemene naam element van het DN-onderwerp van het server certificaat naar verwachting overeenkomt met de teken reeks ' server.demo.system. servicefabric. Azure-int '; het lege veld ' waarde ' geeft aan dat de basis van de certificaat keten wordt vertrouwd op het knoop punt/de computer waarop het server certificaat wordt gevalideerd; in Windows betekent dit dat het certificaat kan worden gekoppeld aan een van de certificaten die zijn geïnstalleerd in het archief van de vertrouwde basis certificerings instantie.
- in het tweede geval geeft de verklaring aan dat de presentator van een certificaat als knoop punt van een peer in het cluster wordt geaccepteerd als de algemene naam van het certificaat overeenkomt met de teken reeks "cluster.demo.system. servicefabric. Azure-int" *en* de vinger afdruk van de directe verlener van het certificaat overeenkomt met een van de door komma's gescheiden vermeldingen in het veld waarde. (Dit regel type is colloquially bekend als ' algemene naam bij het vastmaken van de verlener '.)

In beide gevallen wordt de keten van het certificaat gebouwd en wordt naar verwachting een fout vrij. dat wil zeggen, intrekkings fouten, gedeeltelijke keten of tijd-ongeldige vertrouwens fouten worden als onherstelbaar beschouwd en de certificaat validatie mislukt. Het vastmaken van de uitgevers leidt ertoe dat de status niet-vertrouwd root wordt beschouwd als een niet-fatale fout. Ondanks het uiterlijk is dit een striktere vorm van validatie, omdat de cluster eigenaar het mogelijk maakt om de set geautoriseerde/geaccepteerde verleners te beperken tot hun eigen PKI.

Nadat de certificaat keten is gebouwd, wordt deze gevalideerd op basis van een standaard TLS/SSL-beleid met het gedeclareerde onderwerp als de externe naam; een certificaat wordt beschouwd als een overeenkomst als de algemene naam van het onderwerp of een van de alternatieve namen van het onderwerp overeenkomt met de CN-verklaring van het cluster manifest. Joker tekens worden in dit geval ondersteund en de teken reeks komt niet overeen met hoofdletter gevoelig.

(We moeten verduidelijken dat de hierboven beschreven volg orde kan worden uitgevoerd voor elk type sleutel gebruik dat wordt gedeclareerd door het certificaat. als het certificaat het gebruik van de client verificatie sleutel opgeeft, wordt de keten eerst gebouwd en geëvalueerd voor een client functie. In het geval van geslaagd, is de evaluatie voltooid en is de validatie geslaagd. Als het certificaat niet het gebruik van client verificatie heeft, of als de validatie is mislukt, wordt door de Service Fabric runtime de keten voor Server verificatie gemaakt en geëvalueerd.)

Voor het volt ooien van het voor beeld illustreert het volgende fragment het declareren van client certificaten op basis van de algemene naam:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

De bovenstaande declaraties komen respectievelijk overeen met de id's van de beheerder en gebruiker; validatie van de op deze manier gedeclareerde certificaten is precies zoals beschreven in de voor gaande voor beelden, van cluster-en server certificaten.

> [!NOTE]
> Algemene op naam gebaseerde declaraties zijn bedoeld om de draaiing en het beheer van cluster certificaten in het algemeen te vereenvoudigen. Het wordt echter aangeraden om te voldoen aan de volgende aanbevelingen om te zorgen voor de voortdurende Beschik baarheid en beveiliging van het cluster:
  * de voor keur voor het vastmaken van vertrouwens relatie voor vertrouwde hoofd mappen
  * Vermijd het combi neren van verleners van verschillende Pki's
  * Zorg ervoor dat alle verwachte verleners worden vermeld op de certificaat verklaring. een uitgever die niet overeenkomt, resulteert in een mislukte validatie
  * Zorg ervoor dat de eind punten van het certificaat beleid van de PKI detecteerbaar, beschikbaar en toegankelijk zijn. Dit betekent dat de AIA, CRL of OCSP-eind punten zijn gedeclareerd op het blad certificaat en dat ze toegankelijk zijn, zodat het maken van de certificaat keten kan worden voltooid.

Als u een aanvraag voor een verbinding ontvangt in een cluster dat is beveiligd met X. 509-certificaten, moet de Service Fabric runtime de beveiligings instellingen van het cluster gebruiken om de referenties van de externe partij zoals hierboven beschreven, te valideren. Als dit lukt, wordt de aanroeper/externe partij beschouwd als geverifieerd. Als de referentie overeenkomt met meerdere validatie regels, zal de runtime de aanroeper de rol met de hoogste privileges van een van de overeenkomende regels verlenen. 

### <a name="presentation-rules"></a>Presentatie regels
In de vorige sectie wordt beschreven hoe verificatie werkt in een cluster met certificaat beveiliging. in deze sectie wordt uitgelegd hoe de Service Fabric runtime zelf de certificaten detecteert en laadt die worden gebruikt voor de communicatie in het cluster. deze regels voor presentaties worden aangeroepen.

Net als bij de validatie regels worden met de presentatie regels een rol en de bijbehorende referentie declaratie opgegeven, uitgedrukt in een vinger afdruk of algemene naam. In tegens telling tot de validatie regels hebben algemene op naam gebaseerde declaraties geen voorzieningen voor het vastmaken van een uitgever. Dit biedt meer flexibiliteit en verbeterde prestaties. De presentatie regels worden in de sectie (n) van het cluster manifest gedefinieerd voor elk type knoop punt. de instellingen worden gesplitst uit de beveiligings secties van het cluster, zodat elk knooppunt type de volledige configuratie in één sectie kan hebben. In azure Service Fabric clusters wordt het knooppunt type certificaat declaraties standaard ingesteld op de bijbehorende instellingen in de sectie beveiliging van de definitie van het cluster.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Declaraties voor certificaat presentaties op basis van vinger afdruk
Zoals eerder beschreven, maakt de Service Fabric runtime onderscheid tussen de bijbehorende functies als de peer van andere knoop punten in het cluster en als de server voor cluster beheer bewerkingen. In principe kunnen deze instellingen afzonderlijk worden geconfigureerd, maar in de praktijk worden ze meestal uitgelijnd. Voor de rest van dit artikel gaan we ervan uit dat de instellingen overeenkomen voor eenvoud.

Laten we het volgende fragment van een cluster manifest overwegen:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
Het element ' ClusterCertificate ' illustreert het volledige schema, inclusief optionele para meters (' X509FindValueSecondary ') of gebruikers met de juiste standaard waarden (' X509StoreName '). de andere declaraties tonen een afgekort formulier. In de declaratie van het cluster certificaat hierboven wordt aangegeven dat de beveiligings instellingen van knoop punten van het type ' nt1vm ' zijn geïnitialiseerd met het certificaat cc71.. 1984 ' als de primaire en ' 49e2.. 19d6 ' certificaat als secundaire; beide certificaten worden naar verwachting gevonden in het LocalMachine \' mijn certificaat archief (of het bijbehorende Linux-pad, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Algemene presentatie declaraties op basis van naam certificaten
Het knooppunt type certificaten kunnen ook worden gedeclareerd door de algemene naam van het onderwerp, zoals hieronder geïllustreerd:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Voor beide typen declaraties leest een Service Fabric knoop punt de configuratie bij het opstarten, zoekt en laadt de opgegeven certificaten en sorteert deze in aflopende volg orde van hun NotBefore-kenmerk. verlopen certificaten worden genegeerd en het eerste element van de lijst is geselecteerd als de client referentie voor een Service Fabric verbindings poging door dit knoop punt. (In feite is Service Fabric het meest recent verleende certificaat als voor keur.)

> [!NOTE]
> Vóór versie 7.2.445 (7,2 CU4) Service Fabric het meest verlopende certificaat geselecteerd (het certificaat met de meest rechtse eigenschap ' NotAfter ')

Houd er rekening mee dat voor op de algemene naam gebaseerde presentatie declaraties een certificaat als een overeenkomst wordt beschouwd als de algemene naam van het onderwerp gelijk is aan het X509FindValue-veld (of X509FindValueSecondary) van de declaratie als een hoofdletter gevoelige, exacte vergelijking van teken reeksen. Dit is in tegens telling tot de validatie regels, die ondersteuning bieden voor joker tekens, evenals niet-hoofdletter gevoelige teken reeks vergelijkingen.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Configuratie-instellingen voor diverse certificaten
Voorheen werd vermeld dat de beveiligings instellingen van een Service Fabric cluster ook het gedrag van de verificatie code in de tussenwaarde kunnen veranderen. Hoewel het artikel on [service Fabric cluster instellingen](service-fabric-cluster-fabric-settings.md) de uitgebreide en meest recente lijst met instellingen vertegenwoordigt, zullen we uitzoomen op de betekenis van een aantal van de beveiligings instellingen die hier worden weer gegeven om de volledige weer gave van op certificaten gebaseerde verificatie uit te voeren. Voor elke instelling wordt de bedoeling, de standaard waarde/het gedrag, de manier waarop deze van invloed is op verificatie en welke waarden acceptabel zijn, uitgelegd.

Zoals vermeld, houdt certificaat validatie altijd in voor het bouwen en evalueren van de keten van het certificaat. Voor certificaten die door de certificerings instantie worden verleend, worden meestal enkele uitgaande aanroepen naar verschillende eind punten van de uitgevende PKI, het opslaan van antwoorden in de cache, uitgevoerd. Gezien de prevalentie van certificaat validatie aanroepen in een Service Fabric-cluster, kunnen eventuele problemen in de eind punten van de PKI leiden tot een lagere Beschik baarheid van het cluster of uitsplitsing. De uitgaande aanroepen kunnen niet worden onderdrukt (zie hieronder in het gedeelte met veelgestelde vragen voor meer informatie): de volgende instellingen kunnen worden gebruikt om validatie fouten te maskeren die worden veroorzaakt door mislukte CRL-aanroepen.

  * CrlCheckingFlag: de teken reeks die is geconverteerd naar UINT in het gedeelte Security. De waarde van deze instelling wordt door Service Fabric gebruikt om status fouten in de certificaat keten te maskeren door het gedrag van het bouwen van de keten te wijzigen. het wordt door gegeven aan de Win32 CryptoAPI [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) -aanroep als de para meter dwFlags en kan worden ingesteld op een geldige combi natie van vlaggen die worden geaccepteerd door de functie. Met de waarde 0 wordt de Service Fabric runtime gedwongen om status fouten van vertrouwens relaties te negeren. dit wordt niet aanbevolen, omdat het gebruik hiervan een belang rijke beveiligings risico vormt. De standaard waarde is 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Wanneer gebruikt u: voor lokale tests met zelfondertekende certificaten of ontwikkelaars certificaten die niet volledig zijn opgemaakt/geen juiste open bare-sleutel infrastructuur hebben ter ondersteuning van de certificaten. Kan ook worden gebruikt als oplossing in gapped omgevingen tijdens de overgang tussen Pki's.

  Gebruik: we nemen een voor beeld waarbij intrekkings controle wordt afgedwongen voor toegang tot alleen in de cache opgeslagen Url's. Veronderstelling
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  de declaratie in het cluster manifest wordt dan als volgt:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError: Klik onder de sectie Security op Boolean met de standaard waarde false. Hiermee wordt een snelkoppeling aangeduid voor het onderdrukken van een status van het samen stellen van een intrekkings fout in de keten (of een volgende validatie fout status van het keten beleid).

  Gebruik: lokaal testen of met niet-ondersteunde ontwikkelaars certificaten van een juiste PKI. Gebruik als oplossing in gapped omgevingen of wanneer de PKI niet toegankelijk is.

  Gebruik:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Andere belang rijke instellingen (allemaal onder het gedeelte Security):
  * AcceptExpiredPinnedClusterCertificate: besproken in de sectie die is gericht op certificaat validatie op basis van een vinger afdruk. Hiermee kunt u verlopen zelfondertekende cluster certificaten accepteren. 
  * CertificateExpirySafetyMargin-interval, uitgedrukt in minuten voorafgaand aan het NotAfter-tijds tempel van het certificaat, en gedurende welke het certificaat wordt beschouwd als risico voor de verval datum. Service Fabric bewaakt cluster certificaten en verzendt periodiek status rapporten over hun resterende Beschik baarheid. Binnen het veiligheids interval worden deze status rapporten verhoogd naar de status ' warning '. De standaard waarde is 30 dagen.
  * CertificateHealthReportingInterval: Hiermee bepaalt u de frequentie van status rapporten met betrekking tot de resterende geldigheids duur van cluster certificaten. Rapporten worden slechts één keer per dit interval verzonden. De waarde wordt uitgedrukt in seconden, met een standaard instelling van 8 uur.
  * EnforcePrevalidationOnSecurityChanges-Boolean, bepaalt het gedrag van cluster upgrades bij het detecteren van wijzigingen in de beveiligings instellingen. Als deze eigenschap is ingesteld op ' True ', probeert de upgrade van het cluster ervoor te zorgen dat ten minste één van de certificaten die overeenkomen met een van de presentatie regels, een bijbehorende validatie regel kan door geven. De validatie wordt uitgevoerd voordat de nieuwe instellingen worden toegepast op een wille keurig knoop punt, maar wordt alleen uitgevoerd op het knoop punt dat als host fungeert voor de primaire replica van de Cluster beheer-service op het moment dat de upgrade wordt gestart. Vanaf dit punt heeft de instelling de standaard waarde ' false ' en wordt ingesteld op ' True ' voor nieuwe Azure-Service Fabric clusters met een runtime versie die begint met 7,1.
 
### <a name="end-to-end-scenario-examples"></a>End-to-end-scenario (voor beelden)
We hebben de presentatie regels, validatie regels en tweak markeringen bekeken, maar hoe werkt dit allemaal samen? In deze sectie wordt gebruikgemaakt van twee end-to-end-voor beelden die laten zien hoe de beveiligings instellingen kunnen worden gebruikt voor veilige cluster upgrades. Let op: dit is niet bedoeld als een uitgebreide Dissertation op het juiste certificaat beheer in Service Fabric, zoek naar een onderliggend artikel over dat onderwerp.

De schei ding van de presentatie-en validatie regels vormt de duidelijke vraag (of bezorgdheid) van of ze afwijkend kunnen zijn, en wat de gevolgen hiervan zouden zijn. Het is dus mogelijk dat de validatie regels van een ander knoop punt niet door de selectie van een verificatie certificaat worden door gegeven. Dit verschil is in feite de primaire oorzaak van incidenten met betrekking tot authenticatie. Op hetzelfde moment kan de schei ding van deze regels voor een cluster blijven werken tijdens een upgrade waarbij de beveiligings instellingen van het cluster worden gewijzigd. Houd er rekening mee dat door eerst de validatie regels als een eerste stap uit te breiden, alle knoop punten van het cluster convergeren op de nieuwe instellingen en nog steeds de huidige referenties gebruiken. 

Als er een upgrade wordt uitgevoerd, wordt er in een Service Fabric cluster gevoortgangt (Maxi maal 5) ' upgrade domeinen ' of een UDs. Alleen knoop punten in de huidige UD worden op een bepaald moment bijgewerkt/gewijzigd, en de upgrade gaat door naar de volgende UD alleen als de beschik baarheid van het cluster dit toestaat. (Raadpleeg [service Fabric cluster upgrades](service-fabric-cluster-upgrade.md) en andere artikelen in hetzelfde onderwerp voor meer informatie.) Wijzigingen in de certificaat/beveiliging zijn bijzonder riskant, omdat ze knoop punten uit het cluster kunnen isoleren of het cluster aan de rand van quorum verlies mogen verlaten.

De volgende notatie wordt gebruikt om de beveiligings instellingen van een knoop punt te beschrijven:

Lege: {P:{TP = A}, V:{TP = A}}, waarbij:
  - ' Lege ' staat voor een knoop punt in het upgrade domein *k*
  - ' P ' staat voor de huidige presentatie regels van het knoop punt (ervan uitgaande dat er alleen naar cluster certificaten wordt verwezen). 
  - ' V ' staat voor de huidige validatie regels van het knoop punt (alleen cluster certificaat)
  - ' TP = A ' vertegenwoordigt een op vinger afdruk gebaseerde declaratie (TP), waarbij ' A ' een vinger afdruk van een certificaat is
  - ' CN = B ' vertegenwoordigt een algemene op naam gebaseerde declaratie (CN), waarbij ' B ' de algemene naam van het certificaat is 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Een cluster certificaat draaien dat is gedeclareerd door de vinger afdruk
In de volgende reeks wordt beschreven hoe een upgrade van twee fasen kan worden gebruikt om veilig een secundair cluster certificaat te introduceren, dat door de vinger afdruk is gedefinieerd. in de eerste fase wordt de nieuwe certificaat declaratie in de validatie regels geïntroduceerd en in de tweede fase wordt deze in de presentatie regels geïntroduceerd:
  - begin status: N0 = {P:{TP = A}, V:{TP = A}},... Lege = {P:{TP = A}, V:{TP = A}}-het cluster is in rust, alle knoop punten delen een gemeen schappelijke configuratie
  - bij het volt ooien van het upgrade domein 0: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... Lege = {P:{TP = A}, V:{TP = A}}-knoop punten in UD0 geven certificaat A op en accepteren certificaten A of B; alle andere knoop punten aanwezig zijn en alleen certificaat accepteren
  - bij het volt ooien van het laatste upgrade domein: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... Lege = {P:{TP = A}, V:{TP = A, TP = B}}-alle knoop punten aanwezig certificaat A, alle knoop punten accepteren certificaat A of B
      
Op dit moment is het cluster opnieuw in evenwicht en de tweede fase van de beveiligings instellingen voor bijwerken en wijzigen kan beginnen:
  - bij het volt ooien van het upgrade domein 0: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... Lege = {P:{TP = A}, V:{TP = A, TP = B}}-knoop punten in UD0 beginnen met het presen teren van B, dat wordt geaccepteerd door een ander knoop punt in het cluster.
  - bij het volt ooien van het laatste upgrade domein: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... Lege = {P:{TP = A, TP = B}, V:{TP = A, TP = B}}-alle knoop punten zijn overgeschakeld op certificaat B. certificaat A kan nu buiten gebruik worden gesteld/verwijderd uit de cluster definitie met een volgende set upgrades.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Een cluster converteren van een vinger afdruk naar op algemene naam gebaseerde certificaat declaraties
Op dezelfde manier wordt het type certificaat declaratie (van vinger afdruk naar algemene naam) gevolgd door hetzelfde patroon als hierboven. Houd er rekening mee dat validatie regels toestaan dat de certificaten van een bepaalde rol worden gedeclareerd door zowel de vinger afdruk als de algemene naam in dezelfde cluster definitie. Daarentegen is in de presentatie regels slechts één vorm van declaratie toegestaan. Incidenteel, de veilige benadering van het converteren van een cluster certificaat van een vinger afdruk naar een algemene naam is om het beoogde doel certificaat eerst op de vinger afdruk door te voeren en deze declaratie vervolgens te wijzigen in een algemene naam. In het volgende voor beeld wordt ervan uitgegaan dat de vinger afdruk ' A ' en de algemene naam ' B ' verwijzen naar hetzelfde certificaat. 

  - begin status: N0 = {P:{TP = A}, V:{TP = A}},... Lege = {P:{TP = A}, V:{TP = A}}-het cluster is in rust, alle knoop punten delen een gemeen schappelijke configuratie, met een primaire certificaat vingerafdruk
  - bij het volt ooien van het upgrade domein 0: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... Lege = {P:{TP = A}, V:{TP = A}}-knoop punten in UD0 geven certificaat A op en accepteren certificaten met een vinger afdruk A of algemene naam B; alle andere knoop punten aanwezig zijn en alleen certificaat accepteren
  - bij het volt ooien van het laatste upgrade domein: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... Lege = {P:{TP = A}, V:{TP = A, CN = B}}-alle knoop punten aanwezig certificaat A, alle knoop punten accepteren certificaat A (TP) of B (CN)

Op dit moment kunnen we door gaan met het wijzigen van de presentatie regels met een volgende upgrade:
  - bij het volt ooien van het upgrade domein 0: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... Lege = {P:{TP = A}, V:{TP = A, CN = B}}-knoop punten in UD0 geven certificaat B op dat door CN wordt gevonden en accepteert certificaten met de vinger afdruk A of algemene naam B; alle andere knoop punten zijn aanwezig en accepteren alleen certificaat A, geselecteerd door de vinger afdruk
  - bij het volt ooien van het laatste upgrade domein: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... Lege = {P:{CN = B}, V:{TP = A, CN = B}}-alle knoop punten die door CN worden gevonden, worden door alle knoop punten certificaat A (TP) of B (CN) geaccepteerd
    
Als fase 2 is voltooid, wordt ook de conversie van het cluster naar algemene op naam gebaseerde certificaten gemarkeerd. de validatie declaraties op basis van de vinger afdruk kunnen worden verwijderd bij een volgende cluster upgrade.

> [!NOTE]
> In azure Service Fabric clusters worden de hierboven weer gegeven werk stromen georganiseerd door de resource provider van Service Fabric. de cluster eigenaar is nog steeds verantwoordelijk voor het inrichten van certificaten in het cluster volgens de aangegeven regels (presentatie of validatie) en wordt aangemoedigd om wijzigingen in meerdere stappen uit te voeren.

In een afzonderlijk artikel gaan we het onderwerp over het beheren en inrichten van certificaten in een Service Fabric-cluster.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Problemen oplossen en veelgestelde vragen
Bij het opsporen van problemen met betrekking tot de verificatie in Service Fabric clusters is het niet eenvoudig om de volgende hints te hopeful en tips kunnen helpen. De eenvoudigste manier om te beginnen met onderzoeken is het controleren van de Service Fabric gebeurtenis logboeken op de knoop punten van het cluster, niet noodzakelijkerwijs alleen die symptomen, maar ook knoop punten die wel of geen verbinding kunnen maken met een van hun neighbors. In Windows worden gebeurtenissen die van belang zijn, meestal vastgelegd in respectievelijk de kanalen ' toepassingen en services Logs\Microsoft-ServiceFabric\Admin ' of ' operationeel '. Soms kan het nuttig zijn om [Diagnostische CAPI2-logboek registratie in te scha kelen](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)om meer informatie vast te leggen over de validatie van het certificaat, het ophalen van CRL'S/CTL, enzovoort. (Vergeet niet om het uit te scha kelen na het volt ooien van de reproduceren.)

Voor beelden van problemen die zich voordoen in een cluster met verificatie, zijn: 
  - knoop punten zijn omlaag/scha kelen 
  - Verbindings pogingen worden geweigerd
  - time-out bij Verbindings pogingen

Elk van de symptomen kan worden veroorzaakt door verschillende problemen en dezelfde hoofd oorzaak kan verschillende manifesten weer geven. Daarom wordt er een klein voor beeld van gang bare problemen vermeld, met aanbevelingen voor het oplossen ervan. 

* Knoop punten kunnen berichten uitwisselen, maar kunnen geen verbinding maken. Een mogelijke oorzaak voor het beëindigen van verbindings pogingen is de fout ' certificaat komt niet overeen ', een van de partijen in een Service Fabric-to-Service Fabric-verbindingen presenteert een certificaat dat de validatie regels van de ontvanger niet kan verwerken. Kan worden gecombineerd met een van de volgende fouten: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Ga als volgt te voor diagnose/onderzoek: op elk knoop punt dat verbinding probeert te voeren, bepaalt u welk certificaat wordt weer gegeven. Controleer het certificaat en gebruik en Simuleer de validatie regels (Controleer of de vinger afdruk of de algemene naam gelijkheid is opgegeven).

  Een andere veelvoorkomende fout code is:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  In dit geval wordt het certificaat gedeclareerd met een algemene naam en is een van de volgende van toepassing:
    - de verleners worden niet vastgemaakt en het basis certificaat wordt niet vertrouwd of
    - de verleners zijn vastgemaakt, maar de verklaring bevat niet de vinger afdruk van de directe verlener van dit certificaat

* Een knoop punt is actief, maar kan geen verbinding maken met andere knoop punten. andere knoop punten ontvangen geen binnenkomend verkeer van het knoop punt dat niet werkt. In dit geval is het mogelijk dat het laden van het certificaat mislukt op het lokale knoop punt. Zoek naar de volgende fouten:
  - het certificaat is niet gevonden. Controleer of de certificaten die zijn gedeclareerd in de presentatie regels, kunnen worden omgezet door de inhoud van het LocalMachine\My (of het opgegeven certificaat archief). 
    Mogelijke oorzaken voor fouten zijn: 
      - ongeldige tekens in de vingerafdruk declaratie
      - het certificaat is niet geïnstalleerd
      - het certificaat is verlopen
      - de declaratie common-name bevat het voor voegsel CN =
      - de declaratie bevat een Joker teken en er is geen exacte overeenkomst aanwezig in het certificaat archief (declaratie: CN = *. mijn domein. com, werkelijk certificaat: CN = server. mijn domein. com)

  - onbekende referenties: geeft een ontbrekende persoonlijke sleutel aan die overeenkomt met het certificaat, meestal vergezeld van fout code: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Controleer of de persoonlijke sleutel bestaat, om dit te verhelpen. Controleer of SFAdmins de toegang ' lezen | uitvoeren ' heeft gekregen voor de persoonlijke sleutel.

  - onjuist provider type: geeft een CNG-certificaat (crypto grafie voor nieuwe generatie) aan ("micro soft-software sleutel opslag provider"); op dit moment biedt Service Fabric alleen ondersteuning voor CAPI1-certificaten. Meestal vergezeld van fout code:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    U kunt dit oplossen door het cluster certificaat opnieuw te maken met behulp van een CAPI1 (bijvoorbeeld ' micro soft Enhanced RSA en AES Cryptographic Provider '). Raadpleeg voor meer informatie over crypto grafie de [informatie over cryptografische providers](/windows/win32/seccertenroll/understanding-cryptographic-providers)
