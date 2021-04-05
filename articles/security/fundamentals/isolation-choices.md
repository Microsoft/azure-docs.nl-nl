---
title: Isolatie in de open bare Azure-Cloud | Microsoft Docs
description: Meer informatie over hoe Azure isolatie biedt voor kwaadwillende en niet-kwaadwillende gebruikers en verschillende isolatie opties biedt voor architecten.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c06fb0830ae709918b668ed60efbaaf47a63ce84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94842835"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolatie in de open bare Azure-Cloud

Met Azure kunt u toepassingen en virtuele machines (Vm's) uitvoeren op gedeelde fysieke infra structuur. Een van de Prime economische motivaties voor het uitvoeren van toepassingen in een cloud omgeving is de mogelijkheid om de kosten van gedeelde resources over meerdere klanten te verdelen. In deze praktijk van multitenancy wordt de efficiëntie verbeterd door resources te multiplexen tussen verschillende klanten tegen lage kosten. Helaas introduceert het ook het risico van het delen van fysieke servers en andere infrastructuur resources om uw gevoelige toepassingen en Vm's uit te voeren die kunnen behoren tot een wille keurige en mogelijk schadelijke gebruiker.

In dit artikel wordt beschreven hoe Azure isolatie biedt tegen kwaadwillende en niet-kwaadwillende gebruikers en fungeert als richt lijn voor het ontwikkelen van cloud oplossingen door diverse isolatie keuzes te bieden aan architecten.

## <a name="tenant-level-isolation"></a>Isolatie op Tenant niveau

Een van de belangrijkste voor delen van Cloud Computing is een concept van een gedeelde, gemeen schappelijke infra structuur van talloze klanten tegelijk, waardoor de schaal voordelen worden uitgebreid. Dit concept heet multitenancy. Micro soft werkt continu om ervoor te zorgen dat de architectuur met meerdere tenants van Microsoft Cloud Azure ondersteuning biedt voor beveiliging, vertrouwelijkheid, privacy, integriteit en beschik baarheid.

Met betrekking tot werklocaties in de cloud kan een tenant worden gedefinieerd als een client of organisatie die een bepaald exemplaar van de gebruikte cloudservice in eigendom heeft en beheert. Met het identiteits platform dat is geleverd door Microsoft Azure, is een Tenant gewoon een toegewezen exemplaar van Azure Active Directory (Azure AD) dat uw organisatie ontvangt en die eigenaar is van een micro soft-Cloud service.

Elke Azure AD-directory is uniek en werkt afzonderlijk van andere Azure AD-directory’s. Net zoals een kantoorgebouw een beveiligd bedrijfsmiddel is van uw organisatie, is ook een Azure AD-directory ontworpen als een beveiligd bedrijfsmiddel dat alleen door uw organisatie kan worden gebruikt. De Azure AD-architectuur isoleert klant- en identiteitsgegevens, zodat deze niet door elkaar worden gehaald. Dit betekent dat gebruikers en beheerders van een Azure AD-directory niet per ongeluk of opzettelijk gegevens in een andere directory kunnen openen.

### <a name="azure-tenancy"></a>Azure pacht

Azure pacht (Azure-abonnement) verwijst naar een ' klant/facturerings relatie ' en een unieke [Tenant](../../active-directory/develop/quickstart-create-new-tenant.md) in [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Isolatie op Tenant niveau in Microsoft Azure wordt bereikt door gebruik te maken van Azure Active Directory en [op rollen gebaseerd toegangs beheer van Azure](../../role-based-access-control/overview.md) . Elk Azure-abonnement is gekoppeld aan één Azure Active Directory (AD) Directory.

Gebruikers, groepen en toepassingen van die Directory kunnen resources in het Azure-abonnement beheren. U kunt deze toegangs rechten toewijzen met behulp van de Azure Portal, Azure-opdracht regel Programma's en Azure Management-Api's. Een Azure AD-Tenant is logisch geïsoleerd met behulp van beveiligings grenzen, zodat geen enkele klant op een schadelijke of onopzettelijke manier mede tenants kan openen of misbruiken. Azure AD wordt uitgevoerd op ' bare metal ' servers die zijn geïsoleerd op een gescheiden netwerk segment, waarbij pakket filters op hostniveau worden gefilterd en Windows Firewall ongewenste verbindingen en verkeer blokkeert.

- Toegang tot gegevens in azure AD vereist verificatie van de gebruiker via een beveiligings token service (STS). Informatie over de aanwezigheid, ingeschakelde status en rol van de gebruiker wordt door het autorisatie systeem gebruikt om te bepalen of de aangevraagde toegang tot de doel-Tenant is geautoriseerd voor deze gebruiker tijdens deze sessie.

![Azure pacht](./media/isolation-choices/azure-isolation-fig1.png)

- Tenants zijn discrete containers en er is geen relatie tussen deze.

- Geen toegang via tenants, tenzij de Tenant beheerder deze via Federatie verleent of gebruikers accounts inricht vanuit andere tenants.

- Fysieke toegang tot servers die bestaan uit de Azure AD-service en directe toegang tot de back-endsystemen van Azure AD, is beperkt.

- Azure AD-gebruikers hebben geen toegang tot fysieke assets of locaties en daarom is het niet mogelijk om de logische Azure RBAC-beleids controles die hieronder worden vermeld, te omzeilen.

Voor diagnostische en onderhouds behoeften is een operationeel model dat gebruikmaakt van een just-in-time-uitbrei ding van bevoegdheden vereist en wordt gebruikt. Azure AD Privileged Identity Management (PIM) introduceert het concept van een in aanmerking komende beheerder. [in aanmerking komende beheerders](../../active-directory/privileged-identity-management/pim-configure.md) moeten gebruikers zijn die nu uitgebreide toegang nodig hebben en vervolgens, maar niet elke dag. De rol is inactief totdat gebruikers toegang nodig hebben. Op dat moment voeren ze een activeringsproces uit en zijn ze gedurende een vooraf bepaalde hoeveelheid tijd een actieve beheerder.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory als host voor elke Tenant in een eigen beveiligde container, met beleids regels en machtigingen voor en binnen de container, die alleen eigendom zijn van en worden beheerd door de Tenant.

Het concept van Tenant containers is diep gekorreld in de Directory service op alle lagen, van portals tot permanente opslag.

Zelfs wanneer meta gegevens van meerdere Azure Active Directory tenants op dezelfde fysieke schijf worden opgeslagen, is er geen relatie tussen de containers die zijn gedefinieerd door de Directory service, die op zijn beurt door de Tenant beheerder wordt bepaald.

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)

Met op [rollen gebaseerd toegangs beheer (Azure RBAC) van Azure](../../role-based-access-control/overview.md) kunt u verschillende onderdelen delen die beschikbaar zijn binnen een Azure-abonnement door nauw keurig toegang te bieden voor Azure. Met Azure RBAC kunt u taken binnen uw organisatie scheiden en toegang verlenen op basis van wat gebruikers nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen in een Azure-abonnement of-resources te geven, kunt u alleen bepaalde acties toestaan.

Azure RBAC heeft drie basis rollen die van toepassing zijn op alle resource typen:

- **Eigenaar** heeft volledige toegang tot alle resources, waaronder het recht om de toegang tot anderen te delegeren.

- **Inzender** kan alle typen Azure-resources maken en beheren, maar geen toegang verlenen aan anderen.

- **Lezer** kan bestaande Azure-resources weer geven.

![Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](./media/isolation-choices/azure-isolation-fig3.png)

Met de rest van de Azure-rollen in azure kunt u specifieke Azure-resources beheren. Met de rol Inzender voor virtuele machines kan een gebruiker bijvoorbeeld virtuele machines maken en beheren. Deze geeft geen toegang tot de Azure-Virtual Network of het subnet waarmee de virtuele machine verbinding maakt.

De [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md) geven een lijst van de functies die beschikbaar zijn in Azure. Hiermee geeft u de bewerkingen en het bereik op die elke ingebouwde rol aan gebruikers verleent. Als u uw eigen rollen wilt definiëren voor nog meer controle, raadpleegt u [aangepaste rollen bouwen in azure RBAC](../../role-based-access-control/custom-roles.md).

Azure Active Directory zijn onder andere de volgende mogelijkheden:

- Azure AD maakt eenmalige aanmelding (SSO) bij SaaS-toepassingen mogelijk, ongeacht waar ze worden gehost. Voor sommige toepassingen kan federatieve aanmelding worden gebruikt via Azure AD en voor andere toepassingen kan eenmalige aanmelding (SSO) met een wachtwoord worden gebruikt. Federatieve toepassingen kunnen ook gebruikers inrichten en [wachtwoord kluizen](https://www.techopedia.com/definition/31415/password-vault)ondersteunen.

- De toegang tot gegevens in [Azure Storage](https://azure.microsoft.com/services/storage/) wordt geregeld via verificatie. Elk opslag account heeft een primaire sleutel ([opslag account Key](../../storage/common/storage-account-create.md), of SAK) en een secundaire geheime sleutel (de Shared Access-hand tekening of SAS).

- Azure AD biedt identiteit als een service via federatie door gebruik te maken van [Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs), synchronisatie en replicatie met on-premises directory's.

- [Azure AD multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) is de multi-factor Authentication-service die gebruikers nodig hebben om aanmeldingen te verifiëren met behulp van een mobiele app, telefoon gesprek of SMS-bericht. Het kan worden gebruikt met Azure AD om on-premises resources te beveiligen met de Azure Multi-Factor Authentication-Server, en ook met aangepaste toepassingen en mappen met behulp van de SDK.

- Met [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kunt u virtuele Azure-machines toevoegen aan een Active Directory domein zonder dat u domein controllers hoeft te implementeren. U kunt u aanmelden bij deze virtuele machines met uw zakelijke Active Directory referenties en virtuele machines die lid zijn van een domein beheren door gebruik te maken van groepsbeleid om beveiligings basislijnen af te dwingen op al uw virtuele machines van Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) biedt een Maxi maal beschik bare service voor wereld wijde identiteits beheer voor consumenten toepassingen die worden geschaald naar honderden miljoenen identiteiten. De service kan worden geïntegreerd in zowel mobiele als webplatforms. Uw consumenten kunnen zich bij al uw toepassingen aanmelden via een aanpas bare ervaring met behulp van hun bestaande sociale accounts of door referenties te maken.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolatie van micro soft-beheerders & gegevens verwijderen

Micro soft neemt sterke maat regelen om uw gegevens te beschermen tegen ongeoorloofde toegang of gebruik door onbevoegde personen. Deze operationele processen en besturings elementen worden ondersteund door de [voor waarden voor Online Services](https://aka.ms/Online-Services-Terms), die contractuele toezeg gingen bieden die de toegang tot uw gegevens regelen.

- Micro soft-technici hebben geen standaard toegang tot uw gegevens in de Cloud. In plaats daarvan wordt toegang verleend onder beheer toezicht, alleen wanneer dat nodig is. Deze toegang wordt zorgvuldig gecontroleerd en geregistreerd en ingetrokken wanneer deze niet meer nodig is.
- Micro soft kan andere bedrijven aannemen om namens u beperkte services te leveren. Onderaannemers hebben alleen toegang tot klant gegevens om de services te leveren waarvoor we ze hebben ingehuurd, en ze mogen deze niet gebruiken voor andere doel einden. Daarnaast zijn ze contractueel gebonden om de vertrouwelijkheid van de informatie van onze klanten te waarborgen.

Zakelijke services met gecontroleerde certificeringen, zoals ISO/IEC 27001, worden regel matig geverifieerd door micro soft en erkende audit bedrijven, die voorbeeld controles uitvoeren om de toegang te bevestigen, alleen voor legitieme bedrijfs doeleinden. U hebt op elk gewenst moment altijd toegang tot uw eigen klant gegevens en om welke reden dan ook.

Als u gegevens verwijdert, worden de gegevens door Microsoft Azure verwijderd, inclusief alle cache-of back-upkopieën. In het bereik van Services wordt deze verwijdering binnen 90 dagen na het einde van de Bewaar periode uitgevoerd. (In-Scope-services worden gedefinieerd in de sectie gegevens verwerkings voorwaarden van de [voor waarden voor Online Services](https://aka.ms/Online-Services-Terms).)

Als op een schijf station dat wordt gebruikt voor opslag een hardwarestoring optreedt, wordt deze veilig [gewist of vernietigd](https://microsoft.com/trustcenter/privacy/you-own-your-data) voordat micro soft deze terugstuurt naar de fabrikant voor vervanging of herstel. De gegevens op het station worden overschreven om ervoor te zorgen dat de gegevens niet op enigerlei wijze kunnen worden hersteld.

## <a name="compute-isolation"></a>Reken isolatie

Microsoft Azure biedt verschillende Cloud Computing Services die een breed scala aan reken instanties bevatten & services die automatisch omhoog en omlaag kunnen worden geschaald om te voldoen aan de behoeften van uw toepassing of onderneming. Deze reken instantie en service bieden isolatie op meerdere niveaus om gegevens te beveiligen zonder dat de flexibiliteit van de configuratie die klanten vereisen, wordt gegarandeerd.

### <a name="isolated-virtual-machine-sizes"></a>Geïsoleerde grootte van virtuele machines

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Toegewezen hosts

Naast de geïsoleerde hosts die in de voor gaande sectie worden beschreven, biedt Azure ook speciale hosts. Toegewezen hosts in Azure is een service die fysieke servers levert die als host kunnen fungeren voor een of meer virtuele machines, en die zijn toegewezen aan één Azure-abonnement. Toegewezen hosts bieden hardwarematige isolatie op het niveau van de fysieke server. Er worden geen andere Vm's op uw hosts geplaatst. Toegewezen hosts worden geïmplementeerd in dezelfde data centers en delen hetzelfde netwerk en onderliggende opslag infrastructuur als andere, niet-geïsoleerde hosts. Zie het gedetailleerde overzicht van met [Azure toegewezen hosts](../../virtual-machines/dedicated-hosts.md)voor meer informatie.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V-&-basis besturingssysteem isolatie tussen de virtuele machines van de virtuele machine & gast-Vm's

Het Compute-platform van Azure is gebaseerd op machine virtualisatie, wat inhoudt dat alle klant code wordt uitgevoerd in een Hyper-V-virtuele machine. Op elk Azure-knoop punt (of netwerk eindpunt) bevindt zich een Hyper Visor die rechtstreeks over de hardware wordt uitgevoerd en die een knoop punt opsplitst in een variabele aantal gast Virtual Machines (Vm's).

![Hyper-V-&-basis besturingssysteem isolatie tussen de virtuele machines van de virtuele machine & gast-Vm's](./media/isolation-choices/azure-isolation-fig4.jpg)

Elk knoop punt heeft ook één speciale basis-VM, waarmee het hostbesturingssysteem wordt uitgevoerd. Een kritieke grens is de isolatie van de hoofd-VM van de virtuele gast-Vm's en de gast-Vm's van elkaar, die worden beheerd door de Hyper Visor en het basis besturingssysteem. De koppeling tussen Hyper Visor/root OS maakt gebruik van de tien tallen beveiligings ervaring van micro soft en meer recente lessen van micro soft Hyper-V, om een krachtige isolatie van gast-Vm's mogelijk te maken.

Voor het Azure-platform wordt gebruikgemaakt van een gevirtualiseerde omgeving. Gebruikers exemplaren fungeren als zelfstandige virtuele machines die geen toegang hebben tot een fysieke hostserver.

De Azure-Hyper Visor fungeert als een micro-kernel en geeft alle aanvragen voor hardware-toegang van virtuele gast machines door aan de host voor verwerking met behulp van een Shared-Memory-interface met de naam VMBus. Dit voorkomt dat gebruikers onbeperkt toegang krijgen tot het systeem om gegevens te lezen en te schrijven of programma’s uit te voeren en vermindert de risico’s die kleven aan het delen van systeemresources.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Geavanceerd VM-plaatsings algoritme & bescherming tegen side Channel-aanvallen

Een cross-VM-aanval bestaat uit twee stappen: het plaatsen van een door kwaadwillende persoon beheerde virtuele machine op dezelfde host als een van de virtuele machines van het slacht offer. vervolgens wordt de isolatie grens geschonden om gevoelige informatie over het slacht offer te stelen of de prestaties van greed of Vandalism te beïnvloeden. Microsoft Azure biedt beveiliging in beide stappen door gebruik te maken van een geavanceerde VM-plaatsings algoritme en-beveiliging van alle bekende side Channel-aanvallen, waaronder ruis-Vm's voor de buur.

### <a name="the-azure-fabric-controller"></a>De Azure Fabric-controller

De Azure Fabric-controller is verantwoordelijk voor het toewijzen van infrastructuur resources aan Tenant werkbelastingen en het beheer van de communicatie met één richting van de host naar virtuele machines. Het algoritme voor het plaatsen van de virtuele machine van de Azure Fabric-controller is zeer complex en bijna onmogelijk als fysiek hostniveau.

![De Azure Fabric-controller](./media/isolation-choices/azure-isolation-fig5.png)

De Azure-Hyper Visor dwingt geheugen en proces scheiding af tussen virtuele machines, en stuurt veilig netwerk verkeer naar gast besturingssysteem tenants. Hiermee elimineert u de kans op een aanval op VM-niveau.

In Azure is de hoofd-VM speciaal: er wordt een harder besturings systeem uitgevoerd dat het basis besturingssysteem wordt genoemd en waarop een infrastructuur agent (FA) wordt gehost. FAs worden gebruikt om gast agenten (GA) te beheren binnen gast besturingssystemen op klant-Vm's. FAs beheert ook opslag knooppunten.

De verzameling van Azure Hyper Visor, root OS/FA en de klant-Vm's/GAs bestaat uit een reken knooppunt. FAs worden beheerd door een infra structuur-controller (FC), die zich buiten Compute-en opslag knooppunten bevindt (Compute-en opslag clusters worden beheerd door afzonderlijke FCs). Als een klant het configuratie bestand van de toepassing bijwerkt terwijl deze wordt uitgevoerd, communiceert de FC met de FA, die vervolgens contact maakt met het GAs, die de toepassing van de configuratie wijziging op de hoogte stelt. In het geval van een hardwarestoring wordt de beschik bare hardware automatisch door de FC gevonden en wordt de VM daar opnieuw opgestart.

![Azure Fabric-controller](./media/isolation-choices/azure-isolation-fig6.jpg)

Communicatie van een infrastructuur controller naar een agent is een unidirectioneel. De agent implementeert een met SSL beveiligde service die alleen reageert op aanvragen van de controller. Het kan geen verbindingen initiëren met de controller of andere bevoegde interne knoop punten. De FC behandelt alle reacties alsof ze niet worden vertrouwd.

![Infrastructuur controller](./media/isolation-choices/azure-isolation-fig7.png)

Isolatie gaat uit van de hoofd-VM vanaf de gast-Vm's en de gast-Vm's van elkaar. Reken knooppunten zijn ook geïsoleerd van opslag knooppunten voor een betere beveiliging.

De Hyper Visor en het hostbesturingssysteem bieden filters voor het netwerk pakket om ervoor te zorgen dat niet-vertrouwde virtuele machines vervalste verkeer niet kunnen genereren of verkeer ontvangen dat niet is geadresseerd, direct verkeer naar beveiligde infra structuur-eind punten of verzenden/ontvangen ongepast broadcast verkeer.

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Aanvullende regels die door de Fabric controller-agent zijn geconfigureerd voor het isoleren van de virtuele machine

Standaard wordt al het verkeer geblokkeerd wanneer een virtuele machine wordt gemaakt, waarna de infrastructuur controller agent het pakket filter configureert om regels en uitzonde ringen toe te voegen om geautoriseerd verkeer toe te staan.

Er zijn twee categorieën regels die zijn geprogrammeerd:

- **Machine configuratie-of infrastructuur regels:** Standaard wordt alle communicatie geblokkeerd. Er zijn uitzonde ringen waarmee een virtuele machine DHCP-en DNS-verkeer kan verzenden en ontvangen. Virtuele machines kunnen ook verkeer verzenden naar het ' open bare ' Internet en verkeer verzenden naar andere virtuele machines binnen hetzelfde Azure-Virtual Network en de activerings server van het besturings systeem. De lijst met toegestane uitgaande doelen van de virtuele machines omvat geen Azure-router subnetten, Azure-beheer en andere micro soft-eigenschappen.
- **Configuratie bestand van rol:** Hiermee worden de binnenkomende Access Control lijsten (Acl's) gedefinieerd op basis van het service model van de Tenant.

### <a name="vlan-isolation"></a>VLAN-isolatie

Er zijn in elk cluster drie VLAN'S:

![VLAN-isolatie](./media/isolation-choices/azure-isolation-fig8.jpg)

- Het belangrijkste VLAN: verbindt niet-vertrouwde klant knooppunten
- De FC VLAN – bevat vertrouwde FCs en ondersteunende systemen
- De VLAN van het apparaat: bevat vertrouwde netwerk-en andere infrastructuur apparaten

Communicatie is toegestaan vanuit het FC VLAN naar het hoofd-VLAN, maar kan niet vanuit het hoofd-VLAN naar het FC VLAN worden gestart. Communicatie wordt ook geblokkeerd vanuit het hoofd-VLAN naar het VLAN van het apparaat. Dit zorgt ervoor dat zelfs als een knoop punt met klant code wordt aangetast, het niet mogelijk is om knoop punten op de FC of het apparaat te beschadigen.

## <a name="storage-isolation"></a>Opslag isolatie

### <a name="logical-isolation-between-compute-and-storage"></a>Logische isolatie tussen Compute en opslag

Als onderdeel van het ontwerp van de Cloud, Microsoft Azure van opslag op basis van een virtuele machine gescheiden. Met deze schei ding kunnen reken kracht en opslag onafhankelijk worden geschaald, waardoor het eenvoudiger is om multitenancy en isolatie te bieden.

Daarom worden Azure Storage uitgevoerd op afzonderlijke hardware zonder netwerk verbinding met Azure compute, behalve logisch. Dit betekent dat er geen schijf ruimte wordt toegewezen voor de gehele capaciteit wanneer een virtuele schijf wordt gemaakt. In plaats daarvan wordt een tabel gemaakt die adressen op de virtuele schijf toewijst aan gebieden op de fysieke schijf en die tabel in eerste instantie leeg is. **De eerste keer dat een klant gegevens op de virtuele schijf schrijft, ruimte op de fysieke schijf wordt toegewezen en er een verwijzing naar het bestand in de tabel wordt geplaatst.**

### <a name="isolation-using-storage-access-control"></a>Isolatie met behulp van opslag toegangs beheer

**Access Control in azure Storage** heeft een eenvoudig toegangs beheer model. Elk Azure-abonnement kan een of meer opslag accounts maken. Elk opslag account heeft één geheime sleutel die wordt gebruikt om de toegang tot alle gegevens in dat opslag account te beheren.

![Isolatie met behulp van opslag toegangs beheer](./media/isolation-choices/azure-isolation-fig9.png)

**Toegang tot Azure Storage gegevens (met inbegrip van tabellen)** kan worden beheerd via een [SAS-token (Shared Access Signature)](../../storage/common/storage-sas-overview.md) , waarmee toegang met een bereik wordt verleend. De SA'S worden gemaakt via een query sjabloon (URL), ondertekend met de [SAK (Storage account Key)](/previous-versions/azure/reference/ee460785(v=azure.100)). Deze [ondertekende URL](../../storage/common/storage-sas-overview.md) kan worden toegewezen aan een ander proces (dat wil zeggen, gedelegeerd), waardoor de details van de query kunnen worden ingevuld en de opslag service wordt aangevraagd. Met een SAS kunt u op tijd gebaseerde toegang verlenen aan clients zonder dat de geheime sleutel van het opslag account wordt onthuld.

De SAS betekent dat we een door de client beperkte machtigingen kunnen verlenen aan objecten in het opslag account voor een bepaalde periode en met een opgegeven set machtigingen. We kunnen deze beperkte machtigingen verlenen zonder dat ze de toegangs sleutels van uw account hoeven te delen.

### <a name="ip-level-storage-isolation"></a>Isolatie van opslag op IP-niveau

U kunt firewalls tot stand brengen en een IP-adres bereik definiëren voor uw vertrouwde clients. Met een IP-adres bereik kunnen alleen clients met een IP-adres binnen het gedefinieerde bereik verbinding maken met [Azure Storage](../../storage/blobs/security-recommendations.md).

IP-opslag gegevens kunnen worden beschermd tegen onbevoegde gebruikers via een netwerk mechanisme dat wordt gebruikt om een toegewezen of toegewezen tunnel van verkeer toe te wijzen aan de IP-opslag.

### <a name="encryption"></a>Versleuteling

Azure biedt de volgende typen versleuteling om gegevens te beveiligen:

- Versleuteling 'in transit'
- Versleuteling 'at rest'

#### <a name="encryption-in-transit"></a>Versleuteling in transit

Versleuteling in transit is een mechanisme voor het beveiligen van gegevens wanneer deze via netwerken worden verzonden. Met Azure Storage kunt u gegevens beveiligen met behulp van:

- [Versleuteling op transport niveau](../../storage/blobs/security-recommendations.md), zoals https wanneer u gegevens overbrengt naar of van Azure Storage.
- [Wire-versleuteling](../../storage/blobs/security-recommendations.md), zoals SMB 3,0-versleuteling voor Azure-bestands shares.
- [Versleuteling aan de client zijde](../../storage/blobs/security-recommendations.md), voor het versleutelen van de gegevens voordat deze naar de opslag wordt overgebracht en voor het ontsleutelen van de gegevens nadat deze buiten de opslag zijn overgedragen.

#### <a name="encryption-at-rest"></a>Versleuteling bij rest

Voor veel organisaties is [gegevens versleuteling in rust](isolation-choices.md) een verplichte stap op het vlak van gegevens privacy, naleving en data soevereiniteit. Er zijn drie Azure-functies voor het versleutelen van gegevens met de waarde ' op rest ':

- Met [Storage service Encryption](../../storage/blobs/security-recommendations.md) kunt u aanvragen dat de opslag service automatisch gegevens versleutelt bij het schrijven naar Azure Storage.
- [Versleuteling aan de client zijde](../../storage/blobs/security-recommendations.md) biedt ook de mogelijkheid om op rest te versleutelen.
- Met [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) kunt u de stations van het besturings systeem en de gegevens schijven die worden gebruikt door een virtuele machine van IaaS versleutelen.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) voor virtuele machines (vm's) helpt u bij het versleutelen van de beveiligings-en nalevings vereisten van uw organisatie door uw VM-schijven (inclusief opstart-en gegevens schijven) met sleutels en beleids regels die u beheert in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

De oplossing voor schijf versleuteling voor Windows is gebaseerd op [micro soft BitLocker-stationsversleuteling](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11))en de Linux-oplossing is gebaseerd op [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

De oplossing ondersteunt de volgende scenario's voor IaaS Vm's wanneer deze zijn ingeschakeld in Microsoft Azure:

- Integratie met Azure Key Vault
- Virtuele machines uit de Standard-laag: A, D, DS, G, GS, enzovoort, serie IaaS Vm's
- Versleuteling inschakelen op Windows-en Linux IaaS-Vm's
- Versleuteling uitschakelen voor besturings systeem en gegevens stations voor Windows IaaS-Vm's
- Versleuteling uitschakelen op gegevens stations voor Linux IaaS Vm's
- Versleuteling inschakelen voor IaaS-Vm's waarop Windows client-besturings systeem wordt uitgevoerd
- Versleuteling inschakelen voor volumes met koppel paden
- Versleuteling inschakelen voor Linux-Vm's die zijn geconfigureerd met schijf striping (RAID) met behulp van [mdadm](https://en.wikipedia.org/wiki/Mdadm)
- Versleuteling inschakelen op virtuele Linux-machines met behulp van [lvm (Logical Volume Manager)](/windows/win32/fileio/about-volume-management) voor gegevens schijven
- Versleuteling inschakelen op Windows-Vm's die zijn geconfigureerd met behulp van opslag ruimten
- Alle open bare Azure-regio's worden ondersteund

De oplossing biedt geen ondersteuning voor de volgende scenario's, functies en technologie in de release:

- IaaS-Vm's voor de Basic-laag
- Versleuteling uitschakelen op een OS-station voor Linux IaaS Vm's
- IaaS-Vm's die worden gemaakt met behulp van de klassieke methode voor het maken van VM'S
- Integratie met uw on-premises sleutel beheer service
- Azure Files (gedeeld bestands systeem), NFS (Network File System), dynamische volumes en Windows-Vm's die zijn geconfigureerd met RAID-systemen op basis van software

## <a name="sql-database-isolation"></a>SQL Database isolatie

SQL Database is een relationele database-service in de Microsoft Cloud op basis van de toonaangevende Microsoft SQL Server-engine en is in staat bedrijfskritieke workloads af te handelen. SQL Database biedt voorspel bare gegevens isolatie op account niveau, geografie/regio en op basis van netwerken, allemaal met bijna nul beheer.

### <a name="sql-database-application-model"></a>SQL Database toepassings model

[Micro soft SQL database](../../azure-sql/database/single-database-create-quickstart.md) is een relationele database service in de cloud die is gebaseerd op SQL Server technologieën. Het biedt een Maxi maal beschik bare, meerdere Tenant database service die door micro soft wordt gehost in de Cloud.

SQL Database levert vanuit een toepassings perspectief de volgende hiërarchie: elk niveau heeft een een-op-veel insluitings niveaus.

![SQL Database toepassings model](./media/isolation-choices/azure-isolation-fig10.png)

Het account en abonnement zijn Microsoft Azure platform concepten om facturering en beheer te koppelen.

Logische SQL-servers en-data bases zijn SQL Database specifieke concepten en worden beheerd met behulp van SQL Database, voorzien van OData-en TSQL-interfaces of via de Azure Portal.

Servers in SQL Database zijn geen fysieke of VM-exemplaren. in plaats daarvan zijn dit verzamelingen data bases, het delen van beheer-en beveiligings beleid, die zijn opgeslagen in de data base ' logische Master '.

![SQL Database](./media/isolation-choices/azure-isolation-fig11.png)

Logische hoofd databases zijn:

- SQL-aanmeldingen die worden gebruikt om verbinding te maken met de server
- Firewall-regels

Factuur-en gebruiks gegevens voor data bases van dezelfde server zijn niet gegarandeerd op hetzelfde fysieke exemplaar in het cluster. in plaats daarvan moeten toepassingen de naam van de doel database opgeven tijdens het verbinden.

Vanuit het oogpunt van een klant wordt een server gemaakt in een geografisch gebied, terwijl de daad werkelijke aanmaak van de server plaatsvindt in een van de clusters in de regio.

### <a name="isolation-through-network-topology"></a>Isolatie via de netwerk topologie

Wanneer een server wordt gemaakt en de bijbehorende DNS-naam is geregistreerd, verwijst de DNS-naam naar het zogenaamde gateway VIP-adres in het specifieke Data Center waarin de server is geplaatst.

Achter het VIP (virtueel IP-adres) hebben we een verzameling stateless Gateway Services. Over het algemeen worden gateways bij elkaar betrokken wanneer er coördinatie nodig is tussen meerdere gegevens bronnen (hoofd database, gebruikers database, enzovoort). Gateway Services implementeren het volgende:

- **TDS-verbinding met proxy.** Dit omvat het lokaliseren van de gebruikers database in het back-end-cluster, het implementeren van de aanmeldings procedure en het door sturen van de TDS-pakketten naar de back-end en terug.
- **Database beheer.** Dit omvat het implementeren van een verzameling werk stromen voor het maken/wijzigen/verwijderen van database bewerkingen. U kunt de database bewerkingen aanroepen door TDS-pakketten of expliciete OData-Api's te sniffen.
- Aanmeld-en gebruikers bewerkingen maken/wijzigen/verwijderen
- Server beheer bewerkingen via OData-API

![Isolatie via de netwerk topologie](./media/isolation-choices/azure-isolation-fig12.png)

De laag achter de gateways wordt ' back-end ' genoemd. Hier worden alle gegevens opgeslagen op een Maxi maal beschik bare manier. Elk stukje gegevens wordt aangeduid met een ' partitie ' of ' failover-eenheid ', die allemaal ten minste drie replica's hebben. Replica's worden opgeslagen en gerepliceerd door SQL Server engine en worden beheerd door een failover-systeem, ook wel ' fabric ' genoemd.

Over het algemeen communiceert het back-end-systeem niet uitgaande van andere systemen als veiligheids maatregel. Dit is gereserveerd voor de systemen in de front-end (gateway)-laag. De gateway-laag computers hebben beperkte bevoegdheden op de back-end-computers om de kwets baarheid te minimaliseren als een ingrijpende mechanisme.

### <a name="isolation-by-machine-function-and-access"></a>Isolatie per computer functie en toegang

SQL Database (bestaat uit services die worden uitgevoerd op verschillende machine functies. SQL Database is onderverdeeld in een back-end-Cloud database en een front-end-omgeving (gateway/Management), met het algemene principe van verkeer dat alleen in back-endservers gaat en niet. De front-end-omgeving kan communiceren met de buiten wereld van andere services en in het algemeen heeft alleen beperkte machtigingen in de back-end (genoeg om de ingangs punten aan te roepen die moeten worden aangeroepen).

## <a name="networking-isolation"></a>Netwerk isolatie

Azure-implementatie heeft meerdere lagen voor netwerk isolatie. In het volgende diagram ziet u verschillende lagen van netwerk isolatie die Azure biedt voor klanten. Deze lagen zijn zowel native in het Azure-platform zelf als door de klant gedefinieerde functies. Azure DDoS biedt isolatie voor inkomend verkeer via het internet tegen grootschalige aanvallen op Azure. De volgende laag van isolatie is door de klant gedefinieerde open bare IP-adressen (eind punten) die worden gebruikt om te bepalen welk verkeer door de Cloud service kan worden door gegeven aan het virtuele netwerk. Systeem eigen Azure Virtual Network-isolatie zorgt voor volledige isolatie van alle andere netwerken en dat verkeer alleen door door de gebruiker geconfigureerde paden en methoden doorloopt. Deze paden en methoden zijn de volgende laag, waar Nsg's, UDR en virtuele netwerk apparaten kunnen worden gebruikt om isolatie grenzen te maken om de toepassings implementaties in het beveiligde netwerk te beveiligen.

![Netwerk isolatie](./media/isolation-choices/azure-isolation-fig13.png)

**Verkeers isolatie:** Een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) is de isolatie grens van het verkeer op het Azure-platform. Virtuele machines (Vm's) in één virtueel netwerk kunnen niet rechtstreeks communiceren met Vm's in een ander virtueel netwerk, zelfs niet als beide virtuele netwerken door dezelfde klant worden gemaakt. Isolatie is een kritieke eigenschap waarmee wordt gegarandeerd dat de virtuele machines van de klant en de communicatie privé blijven binnen een virtueel netwerk.

[Subnet](../../virtual-network/virtual-networks-overview.md) biedt een extra laag isolatie met in het virtuele netwerk op basis van het IP-bereik. IP-adressen in het virtuele netwerk kunt u een virtueel netwerk onderverdelen in meerdere subnetten voor organisatie en beveiliging. Tussen VM's en PaaS-rolexemplaren die in (dezelfde of verschillende) subnetten in een VNET zijn geïmplementeerd, is communicatie mogelijk zonder extra configuratie. U kunt ook [netwerk beveiligings groep (nsg's)](../../virtual-network/virtual-networks-overview.md) configureren om netwerk verkeer naar een VM-exemplaar toe te staan of te weigeren op basis van regels die zijn geconfigureerd in de toegangs beheer lijst (ACL) van NSG. NSG's kunnen worden gekoppeld aan subnetten of afzonderlijke VM-exemplaren in dat subnet. Als een NSG is gekoppeld aan een subnet, zijn de ACL-regels van toepassing op alle VM-exemplaren in dat subnet.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [netwerk isolatie opties voor computers in virtuele Windows Azure-netwerken](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Dit omvat het klassieke front-end-en back-end-scenario waarbij computers in een bepaald back-end-netwerk of subnetwerk mogelijk alleen toestaan dat bepaalde clients of andere computers verbinding maken met een bepaald eind punt op basis van een lijst met toegestane IP-adressen.

- Meer informatie over de [isolatie van virtuele machines in azure](../../virtual-machines/isolation.md). Azure Compute biedt virtuele machine grootten die zijn geïsoleerd voor een specifiek hardwaretype en die zijn toegewezen aan één klant.