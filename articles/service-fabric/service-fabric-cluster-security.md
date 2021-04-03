---
title: Een Azure Service Fabric-cluster beveiligen
description: Meer informatie over beveiligings scenario's voor een Azure Service Fabric-cluster en de verschillende technologieën die u kunt gebruiken om ze te implementeren.
ms.topic: conceptual
ms.date: 08/14/2018
ms.openlocfilehash: 6f7bb785184938fe5c1e20e3c915b0112c7723ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573065"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Beveiligings scenario's voor Service Fabric cluster

Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent. Het is uw verantwoordelijkheid om uw clusters te beveiligen om te voor komen dat onbevoegde gebruikers verbinding kunnen maken. Een beveiligd cluster is vooral belang rijk wanneer u werk belastingen voor productie op het cluster uitvoert. Het is mogelijk om een niet-beveiligd cluster te maken, maar als het cluster beheer eindpunten voor het open bare internet beschikbaar stelt, kunnen anonieme gebruikers er verbinding mee maken. Niet-beveiligde clusters worden niet ondersteund voor productie werkbelastingen. 

Dit artikel bevat een overzicht van beveiligings scenario's voor Azure-clusters en zelfstandige clusters en de verschillende technologieën die u kunt gebruiken om ze te implementeren:

* Beveiliging van knoop punt naar knoop punt
* Beveiliging van client naar knoop punt
* Service Fabric op rollen gebaseerd toegangs beheer

## <a name="node-to-node-security"></a>Beveiliging van knoop punt naar knoop punt

Met de beveiliging van knoop punt naar knoop punt kunt u de communicatie tussen de Vm's of computers in een cluster beveiligen. Dit beveiligings scenario zorgt ervoor dat alleen computers die gemachtigd zijn om deel te nemen aan het cluster, kunnen deel nemen aan het hosten van toepassingen en services in het cluster.

![Diagram van de communicatie tussen knoop punten][Node-to-Node]

Clusters die worden uitgevoerd op Azure en zelfstandige clusters die op Windows worden uitgevoerd, kunnen gebruikmaken van de [certificaat beveiliging](/previous-versions/msp-n-p/ff649801(v=pandp.10)) of [Windows-beveiliging](/previous-versions/msp-n-p/ff649396(v=pandp.10)) voor Windows Server-computers.

### <a name="node-to-node-certificate-security"></a>Certificaat beveiliging van knoop punt naar knoop punt

Service Fabric maakt gebruik van X. 509-server certificaten die u opgeeft als onderdeel van de configuratie van het knooppunt type wanneer u een cluster maakt. Aan het einde van dit artikel ziet u een beknopt overzicht van wat deze certificaten zijn en hoe u deze kunt verkrijgen of maken.

Certificaat beveiliging instellen wanneer u het cluster maakt, hetzij in het Azure Portal, met behulp van een Azure Resource Manager sjabloon of met behulp van een zelfstandige JSON-sjabloon. Het standaard gedrag van Service Fabric SDK bestaat uit het implementeren en installeren van het certificaat met de nieuwe datum in de toekomst. het klassieke gedrag heeft betrekking op het definiëren van primaire en secundaire certificaten, om hand matig geïnitieerde Rollo vers toe te staan en wordt niet aanbevolen voor gebruik over de nieuwe functionaliteit. De primaire certificaten die worden gebruikt, hebben de meest recente verval datum, moeten afwijken van de admin-client en alleen-lezen client certificaten die u hebt ingesteld voor beveiliging van [client-naar-knoop punt](#client-to-node-security).

Zie [een cluster instellen met behulp van een Azure Resource Manager sjabloon](service-fabric-cluster-creation-via-arm.md)voor meer informatie over het instellen van certificaat beveiliging in een cluster voor Azure.

Zie voor meer informatie over het instellen van certificaat beveiliging in een cluster voor een zelfstandig Windows Server-cluster [een zelfstandige cluster in Windows beveiligen met behulp van X. 509-certificaten](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Windows-beveiliging van knoop punt naar knoop punt

> [!NOTE]
> Windows-verificatie is gebaseerd op Kerberos. NTLM wordt niet ondersteund als verificatie type.
>
> Gebruik, indien mogelijk, X. 509-certificaat verificatie voor Service Fabric clusters.

Zie [een zelfstandige cluster in Windows beveiligen met behulp van Windows-beveiliging](service-fabric-windows-cluster-windows-security.md)voor meer informatie over het instellen van Windows-beveiliging voor een zelfstandig Windows Server-cluster.

## <a name="client-to-node-security"></a>Beveiliging van client naar knoop punt

Bij client-naar-knoop punt-beveiliging worden clients geverifieerd en wordt de communicatie tussen een client en afzonderlijke knoop punten in het cluster beveiligd. Met dit type beveiliging kunt u ervoor zorgen dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en de toepassingen die op het cluster worden geïmplementeerd. Clients worden geïdentificeerd aan de hand van hun beveiligings referenties voor Windows of hun beveiligings referenties voor het certificaat.

![Diagram van client-naar-knoop punt-communicatie][Client-to-Node]

Clusters die worden uitgevoerd op Azure en zelfstandige clusters waarop Windows wordt uitgevoerd, kunnen gebruikmaken van [certificaat beveiliging](/previous-versions/msp-n-p/ff649801(v=pandp.10)) of [Windows-beveiliging](/previous-versions/msp-n-p/ff649396(v=pandp.10)), hoewel het aanbeveling is om indien mogelijk X. 509-certificaat verificatie te gebruiken.

### <a name="client-to-node-certificate-security"></a>Client-naar-knoop punt certificaat beveiliging

Stel de client-naar-knoop punt certificaat beveiliging in wanneer u het cluster maakt, in de Azure Portal, met behulp van een resource manager-sjabloon of met behulp van een zelfstandige JSON-sjabloon. Als u het certificaat wilt maken, geeft u een client certificaat voor de beheerder of een gebruikers certificaat op. Als best practice moeten de client certificaten van de client en de gebruiker die u opgeeft, afwijken van de primaire en secundaire certificaten die u opgeeft voor [beveiliging](#node-to-node-security)tussen knoop punten. Cluster certificaten hebben dezelfde rechten als client beheerders certificaten. Ze moeten echter alleen worden gebruikt door het cluster en niet door gebruikers met beheerders rechten als beveiligings best practice.

Clients die verbinding maken met het cluster met behulp van het beheerders certificaat, hebben volledige toegang tot beheer mogelijkheden. Clients die verbinding maken met het cluster met behulp van het client certificaat alleen-lezen gebruiker hebben alleen lees toegang tot beheer mogelijkheden. Deze certificaten worden gebruikt voor de Service Fabric RBAC die verderop in dit artikel wordt beschreven.

Zie [een cluster instellen met behulp van een Azure Resource Manager sjabloon](service-fabric-cluster-creation-via-arm.md)voor meer informatie over het instellen van certificaat beveiliging in een cluster voor Azure.

Zie voor meer informatie over het instellen van certificaat beveiliging in een cluster voor een zelfstandig Windows Server-cluster [een zelfstandige cluster in Windows beveiligen met behulp van X. 509-certificaten](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Beveiliging van client naar knoop punt Azure Active Directory op Azure

Azure Active Directory maakt het beheren van toegang tot toepassingen door organisaties (bekend als tenants) mogelijk. Toepassingen worden onderverdeeld in gebruikers met een webgebaseerde aanmeldings gebruikersinterface en die met een systeem eigen client ervaring. Als u nog geen Tenant hebt gemaakt, moet u eerst lezen [hoe u een Azure Active Directory Tenant krijgt][active-directory-howto-tenant].

Een Service Fabric-cluster biedt verschillende toegangspunten bij de management-functionaliteit, met inbegrip van de webconsoles [Service Fabric Explorer][service-fabric-visualizing-your-cluster] en [Visual Studio][service-fabric-manage-application-in-visual-studio]. Als gevolg hiervan maakt u twee Azure AD-toepassingen waarmee u de toegang tot het cluster, een webtoepassing en één systeem eigen toepassing kunt beheren.

Voor clusters die worden uitgevoerd op Azure, kunt u ook de toegang tot beheer eindpunten beveiligen met behulp van Azure Active Directory (Azure AD). Zie [Azure AD instellen voor het verifiëren van clients](service-fabric-cluster-creation-setup-aad.md)voor meer informatie over het maken van de vereiste Azure AD-artefacten en hoe u deze kunt vullen wanneer u het cluster maakt.

## <a name="security-recommendations"></a>Aanbevelingen voor beveiliging

Voor Service Fabric-clusters die zijn geïmplementeerd in een openbaar netwerk dat wordt gehost op Azure, is de aanbeveling voor wederzijdse verificatie van client-naar-knooppunt:

* Azure Active Directory gebruiken voor de identiteit van de client
* Een certificaat voor server identiteit-en TLS-versleuteling van http-communicatie

Voor Service Fabric clusters die zijn geïmplementeerd in een openbaar netwerk dat wordt gehost op Azure, is de aanbeveling voor knoop punt-naar-knooppunt Beveiliging het gebruik van een cluster certificaat voor het verifiëren van knoop punten.

Als u Windows Server 2012 R2 en Windows Active Directory, kunt u voor zelfstandige Windows Server-clusters het beste Windows-beveiliging gebruiken met door groepen beheerde service accounts. Gebruik anders Windows-beveiliging met Windows-accounts.

## <a name="service-fabric-role-based-access-control"></a>Service Fabric op rollen gebaseerd toegangs beheer

U kunt toegangs beheer gebruiken om de toegang tot bepaalde cluster bewerkingen voor verschillende groepen gebruikers te beperken. Dit helpt het cluster beter te beveiligen. Twee typen toegangs beheer worden ondersteund voor clients die verbinding maken met een cluster: beheerdersrol en gebruikersrol.

Gebruikers aan wie de beheerdersrol is toegewezen, hebben volledige toegang tot beheer mogelijkheden, waaronder lees-en schrijf mogelijkheden. Gebruikers aan wie de gebruikersrol is toegewezen, hebben standaard alleen lees toegang tot beheer mogelijkheden (bijvoorbeeld query mogelijkheden). Ze kunnen ook toepassingen en services omzetten.

Stel de client rollen beheerder en gebruiker in wanneer u het cluster maakt. Wijs rollen toe door afzonderlijke identiteiten (bijvoorbeeld door het gebruik van certificaten of Azure AD) op te geven voor elk type rol. Zie [service Fabric op rollen gebaseerd toegangs beheer voor service Fabric-clients](service-fabric-cluster-security-roles.md)voor meer informatie over standaard instellingen voor toegangs beheer en het wijzigen van de standaard instellingen.

## <a name="x509-certificates-and-service-fabric"></a>X. 509-certificaten en-Service Fabric

X. 509-digitale certificaten worden meestal gebruikt voor het verifiëren van clients en servers. Ze worden ook gebruikt voor het versleutelen en digitaal ondertekenen van berichten. Service Fabric maakt gebruik van X. 509-certificaten voor het beveiligen van een cluster en het bieden van beveiligings functies van toepassingen. Zie [werken met certificaten](/dotnet/framework/wcf/feature-details/working-with-certificates)voor meer informatie over X. 509 digitale certificaten. U gebruikt [Key Vault](../key-vault/general/overview.md) om certificaten voor service Fabric clusters in azure te beheren.

Enkele belang rijke dingen die u moet overwegen:

* Gebruik een correct geconfigureerde Windows Server Certificate Service of een van een goedgekeurde certificerings [instantie (CA)](https://en.wikipedia.org/wiki/Certificate_authority)om certificaten te maken voor clusters met productie werkbelastingen.
* Gebruik nooit tijdelijke of test certificaten die u maakt met behulp van hulpprogram ma's als MakeCert.exe in een productie omgeving.
* U kunt een zelfondertekend certificaat gebruiken, maar alleen in een test cluster. Gebruik geen zelfondertekend certificaat in de productie omgeving.
* Zorg ervoor dat u bij het genereren van de vinger afdruk van het certificaat een SHA1-vinger afdruk genereert. SHA1 is wat wordt gebruikt bij het configureren van de client-en cluster certificaat vingerafdrukken.

### <a name="cluster-and-server-certificate-required"></a>Cluster-en server certificaat (vereist)

Deze certificaten (één primair en optioneel een secundair) zijn vereist voor het beveiligen van een cluster en voor komen dat er onbevoegde toegang tot is. Deze certificaten bieden cluster-en server authenticatie.

Met cluster verificatie wordt de communicatie tussen knoop punten voor de cluster Federatie geverifieerd. Alleen knoop punten die hun identiteit kunnen bewijzen met dit certificaat kunnen lid worden van het cluster. Server verificatie verifieert de Cluster beheer-eind punten aan een Management-client, zodat de Management-client weet dat deze is praten met het echte cluster en niet een ' man in het midden '. Dit certificaat biedt ook een TLS voor de HTTPS-beheer-API en voor Service Fabric Explorer via HTTPS. Wanneer een client of knoop punt een knoop punt verifieert, is een van de eerste controles de waarde van de algemene naam in het veld **onderwerp** . Deze algemene naam of een van de alternatieve namen voor de certificaat houder (San's) moet aanwezig zijn in de lijst met toegestane algemene namen.

Het certificaat moet aan de volgende vereisten voldoen:

* Het certificaat moet een persoonlijke sleutel bevatten. Deze certificaten hebben doorgaans extensies. pfx of. pem  
* Het certificaat moet worden gemaakt voor sleutel uitwisseling, dat kan worden geëxporteerd naar een pfx-bestand (Personal Information Exchange).
* De **onderwerpnaam van het certificaat moet overeenkomen met het domein dat u gebruikt voor toegang tot het service Fabric cluster**. Deze overeenkomst is vereist voor het opgeven van een TLS voor het HTTPS-beheer eindpunt van het cluster en Service Fabric Explorer. U kunt geen TLS/SSL-certificaat verkrijgen van een certificerings instantie (CA) voor het domein *. cloudapp.azure.com. U hebt voor uw cluster een aangepaste domeinnaam nodig. Wanneer u een certificaat van een CA aanvraagt, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domeinnaam die u voor uw cluster gebruikt.

Enkele andere zaken die u moet overwegen:

* Het veld **onderwerp** kan meerdere waarden hebben. Elke waarde wordt voorafgegaan door een initialisatie om het waardetype aan te geven. Normaal gesp roken is de initialisatie **CN** (voor *algemene naam*); bijvoorbeeld: **CN = www- \. contoso.com**.
* Het veld **onderwerp** kan leeg zijn.
* Als het optionele veld **alternatieve naam voor onderwerp** is ingevuld, moet het de algemene naam van het certificaat en één vermelding per San bevatten. Deze worden ingevoerd als **DNS-naam** waarden. Zie [een alternatieve naam voor een onderwerp toevoegen aan een beveiligd LDAP-certificaat](https://support.microsoft.com/kb/931351)voor meer informatie over het genereren van certificaten die san's hebben.
* De waarde van het veld **beoogde doel einden** van het certificaat moet een geschikte waarde bevatten, zoals **Server verificatie** of **client verificatie**.

### <a name="application-certificates-optional"></a>Toepassings certificaten (optioneel)

Een wille keurig aantal extra certificaten kan worden geïnstalleerd op een cluster voor de beveiliging van toepassingen. Voordat u uw cluster maakt, moet u rekening houden met de beveiligings scenario's voor toepassingen waarvoor een certificaat moet worden geïnstalleerd op de knoop punten, zoals:

* Versleuteling en ontsleuteling van toepassings configuratie waarden.
* Versleuteling van gegevens tussen knoop punten tijdens de replicatie.

Het concept van het maken van beveiligde clusters is hetzelfde, of het nu gaat om Linux-of Windows-clusters.

### <a name="client-authentication-certificates-optional"></a>Client verificatie certificaten (optioneel)

U kunt een wille keurig aantal extra certificaten opgeven voor beheer-of gebruikers-client bewerkingen. De client kan deze certificaten gebruiken wanneer wederzijdse verificatie is vereist. Client certificaten worden meestal niet uitgegeven door een certificerings instantie van derden. In plaats daarvan bevat het persoonlijke archief van de huidige gebruikers locatie meestal client certificaten die daar zijn geplaatst door een basis instantie. Het certificaat moet een waarde hebben **die bedoeld** is voor **client verificatie**.  

Het cluster certificaat heeft standaard beheerders machtigingen voor de beheerder. Deze aanvullende client certificaten mogen niet worden geïnstalleerd in het cluster, maar zijn wel opgegeven als toegestaan in de cluster configuratie.  De client certificaten moeten echter worden geïnstalleerd op de client computers om verbinding te maken met het cluster en bewerkingen uit te voeren.

> [!NOTE]
> Voor alle beheer bewerkingen op een Service Fabric-cluster zijn server certificaten vereist. Client certificaten kunnen niet worden gebruikt voor beheer.

## <a name="next-steps"></a>Volgende stappen

* [Een cluster maken in azure met behulp van een resource manager-sjabloon](service-fabric-cluster-creation-via-arm.md)
* [Een cluster maken met Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
