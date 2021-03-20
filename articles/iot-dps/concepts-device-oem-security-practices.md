---
title: Beveiligings procedures voor fabrikanten-Azure IoT Device Provisioning Service
description: Overzichten van algemene beveiligings procedures voor Oem's en apparaat-fabrieken die apparaten voorbereiden om zich in te schrijven in de Azure IoT Device Provisioning Service (DPS).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 788738082cbf9995fb2f7282bc3f574903275528
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90527204"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Beveiligings procedures voor fabrikanten van Azure IoT-apparaten
Naarmate er meer fabrikanten IoT-apparaten vrijgeven, is het handig om hulp te identificeren rond gang bare prak tijken. Dit artikel bevat een overzicht van de aanbevolen beveiligings procedures waarmee u rekening moet houden wanneer u apparaten produceert voor gebruik met Azure IoT Device Provisioning Service (DPS).  

> [!div class="checklist"]
> * Opties voor verificatie van apparaten selecteren
> * Certificaten op IoT-apparaten installeren
> * Een Trusted Platform Module (TPM) integreren in het productie proces

## <a name="selecting-device-authentication-options"></a>Opties voor verificatie van apparaten selecteren
Het uiteindelijke doel van een IoT Device Security-maat regel is het maken van een veilige IoT-oplossing. Maar problemen, zoals hardwarebeperkingen, kosten en niveau van beveiligings expertise, zijn van invloed op de opties die u kiest. Verder is uw aanpak van beveiliging van invloed op hoe uw IoT-apparaten verbinding maken met de Cloud. Hoewel er [verschillende elementen van IOT-beveiliging](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) zijn om rekening mee te houden, is welk verificatie type moet worden gebruikt door elke klant. 

Er zijn drie veelgebruikte verificatie typen: X. 509-certificaten, TPM (Trusted platform modules) en symmetrische sleutels. Hoewel er andere verificatie typen bestaan, gebruiken de meeste klanten die oplossingen op Azure IoT bouwen, een van deze drie typen. De rest van dit artikel enquêtes en nadelen van het gebruik van elk verificatie type.

### <a name="x509-certificate"></a>X. 509-certificaat
X. 509-certificaten zijn een type digitale identiteit dat u kunt gebruiken voor verificatie. De standaard X. 509-certificaat wordt beschreven in [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). In azure IoT zijn er twee manieren om certificaten te verifiëren:
- Vingerafdruk. Er wordt een vingerafdruk algoritme uitgevoerd op een certificaat om een hexadecimale teken reeks te genereren. De gegenereerde teken reeks is een unieke id voor het certificaat. 
- CA-verificatie op basis van een volledige keten. Een certificaat keten is een hiërarchische lijst van alle certificaten die nodig zijn om een eind entiteit (EE)-certificaat te verifiëren. Als u een EE-certificaat wilt verifiëren, moet u elk certificaat in de keten verifiëren, met inbegrip van een vertrouwde basis certificerings instantie. 

Voor delen voor X. 509:
- X. 509 is het veiligste verificatie type dat wordt ondersteund in azure IoT.
- X. 509 biedt een hoog niveau van beheer voor certificaat beheer.
- Er zijn veel leveranciers beschikbaar om verificatie oplossingen op basis van X. 509 te bieden.

Nadelen voor X. 509:
- Veel klanten moeten mogelijk vertrouwen op externe leveranciers voor hun certificaten.
- Certificaat beheer kan kostbaar zijn en kan worden toegevoegd aan de totale kosten van de oplossing.
- Het beheer van de levens cyclus van certificaten kan lastig zijn als logistiek niet goed wordt uitgedacht. 

### <a name="trusted-platform-module-tpm"></a>Trusted Platform Module (TPM)
TPM, ook wel aangeduid als [ISO/IEC 11889](https://www.iso.org/standard/66510.html), is een standaard voor het veilig genereren en opslaan van cryptografische sleutels. TPM verwijst ook naar een virtueel of fysiek I/O-apparaat dat communiceert met modules die de standaard implementeren. Een TPM-apparaat kan bestaan als afzonderlijke hardware, geïntegreerde hardware, een op firmware gebaseerde module of op software gebaseerde module. 

Er zijn twee belang rijke verschillen tussen Tpm's en symmetrische sleutels: 
- TPM-chips kunnen ook X. 509-certificaten opslaan.
- TPM-Attestation in DPS maakt gebruik van de TPM-goedkeurings sleutel (EK), een vorm van asymmetrische authenticatie. Met asymmetrische authenticatie wordt een open bare sleutel gebruikt voor versleuteling en wordt een afzonderlijke persoonlijke sleutel gebruikt voor ontsleuteling. Symmetrische sleutels daarentegen gebruiken symmetrische verificatie, waarbij de persoonlijke sleutel wordt gebruikt voor versleuteling en ontsleuteling. 

Voor-en-professionals:
- Tpm's zijn opgenomen als standaard hardware op veel Windows-apparaten, met ingebouwde ondersteuning voor het besturings systeem. 
- TPM-Attestation is gemakkelijker te beveiligen dan SAS (Shared Access Signature) op tokens gebaseerde symmetrische sleutel Attestation.
- U kunt de referenties van het apparaat gemakkelijk laten verlopen en vernieuwen. De IoT Hub referenties worden automatisch door DPS gedistribueerd wanneer een TPM-apparaat moet worden ingericht.

Nadelen voor TPM: 
- Tpm's zijn complex en kunnen lastig te gebruiken zijn. 
- Het ontwikkelen van toepassingen met Tpm's is moeilijk, tenzij u een fysieke TPM of een kwaliteits emulator hebt.
- Mogelijk moet u het bord van het apparaat opnieuw ontwerpen om een TPM in de hardware op te zetten. 
- Als u de EK op een TPM implementeert, wordt de identiteit van de TPM verwijderd en wordt er een nieuwe gemaakt. Hoewel de fysieke chip hetzelfde blijft, heeft deze een nieuwe identiteit in uw IoT-oplossing.

### <a name="symmetric-key"></a>Symmetrische sleutel
Met symmetrische sleutels wordt dezelfde sleutel gebruikt voor het versleutelen en ontsleutelen van berichten. Als gevolg hiervan is dezelfde sleutel bekend voor zowel het apparaat als de service die het verifieert. Azure IoT ondersteunt symmetrische sleutel verbindingen op basis van SAS-tokens. Voor de verificatie van symmetrische sleutels is een belang rijke eigenaar verantwoordelijk voor het beveiligen van de sleutels en het verwezenlijken van een gelijk beveiligings niveau met X. 509-verificatie. Als u symmetrische sleutels gebruikt, is de aanbevolen procedure om de sleutels te beveiligen met behulp van een Hardware Security module (HSM).

Voor-en-professionals voor symmetrische sleutels:
- Het gebruik van symmetrische sleutels is de eenvoudigste en laagste kosten manier om aan de slag te gaan met verificatie.
- Door gebruik te maken van symmetrische sleutels, stroomlijnt u uw proces omdat er niets extra kan worden gegenereerd. 

Nadelen voor symmetrische sleutels: 
- Symmetrische sleutels nemen veel moeite om de sleutels te beveiligen. Dezelfde sleutel wordt gedeeld tussen het apparaat en de Cloud, wat betekent dat de sleutel op twee locaties moet worden beveiligd. De uitdaging met TPM en X. 509-certificaten daarentegen is het eigendom van de open bare sleutel, zonder dat de persoonlijke sleutel wordt onthuld.
- Symmetrische sleutels maken het eenvoudig om beveiligings procedures te volgen. Een gang bare tendens met symmetrische sleutels is het harde coderen van de niet-versleutelde sleutels op apparaten. Hoewel deze praktijk handig is, blijven de sleutels kwetsbaar. U kunt het risico verminderen door de symmetrische sleutel op het apparaat veilig op te slaan. Als uw prioriteit uiteindelijk echter beter is dan het gemak, gebruikt u X. 509-certificaten of TPM voor verificatie. 

### <a name="shared-symmetric-key"></a>Gedeelde symmetrische sleutel
Er is een variatie van de symmetrische sleutel verificatie, ook wel gedeelde symmetrische sleutel genoemd. Deze benadering omvat het gebruik van dezelfde symmetrische sleutel in alle apparaten. De aanbeveling is om te voor komen dat gedeelde symmetrische sleutels op uw apparaten worden gebruikt. 

Pro voor gedeelde symmetrische sleutel:
- Eenvoudig te implementeren en goed koop te maken op schaal. 

Nadelen voor gedeelde symmetrische sleutel: 
- Zeer kwetsbaar voor aanvallen. Het voor deel van de eenvoudige implementatie is zeer kwetsbaar voor het risico. 
- Iedereen kan uw apparaten imiteren als ze de gedeelde sleutel verkrijgen.
- Als u afhankelijk bent van een gedeelde symmetrische sleutel die wordt aangetast, verliest u waarschijnlijk de controle over de apparaten. 

### <a name="making-the-right-choice-for-your-devices"></a>De juiste keuze maken voor uw apparaten
Als u een verificatie methode wilt kiezen, moet u rekening houden met de voor delen en kosten van elke benadering van uw unieke productie proces.  Voor de verificatie van het apparaat is er meestal een inverse relatie tussen hoe veilig een bepaalde benadering is en hoe handig het is.  

## <a name="installing-certificates-on-iot-devices"></a>Certificaten op IoT-apparaten installeren
Als u X. 509-certificaten gebruikt om uw IoT-apparaten te verifiëren, biedt deze sectie richt lijnen voor het integreren van certificaten in uw productie proces. U moet verschillende beslissingen nemen.  Dit zijn beslissingen over algemene certificaat variabelen, wanneer certificaten worden gegenereerd en wanneer ze worden geïnstalleerd. 

Als u gebruikt om wacht woorden te gebruiken, kunt u vragen waarom u hetzelfde certificaat niet kunt gebruiken op al uw apparaten, op dezelfde manier als u hetzelfde wacht woord in al uw apparaten zou kunnen gebruiken. Ten eerste is het gebruik van hetzelfde wacht woord overal gevaarlijk. De praktijk heeft bedrijven blootgesteld aan grote DDoS-aanvallen, met inbegrip van de omgeving die de DNS op de VS-Oost kust enkele jaren geleden heeft geduurd. Gebruik nooit hetzelfde wacht woord overal, zelfs met persoonlijke accounts. Ten tweede is een certificaat geen wacht woord. Dit is een unieke identiteit. Een wacht woord is vergelijkbaar met een geheime code die iedereen kan gebruiken om een deur te openen bij een beveiligd gebouw.  Het is iets wat u weet en u kunt het wacht woord aan iemand geven om toegang te krijgen.  Een certificaat is net als een rijbewijs met uw foto en andere details die u kunt weer geven voor een beveiligd gebouw. Het is gekoppeld aan wie u bent.  Als de beveiliging nauw keurig overeenkomt met personen met de licenties van het stuur programma, kunt u alleen uw licentie (identiteit) gebruiken om de toegang te krijgen. 

### <a name="variables-involved-in-certificate-decisions"></a>Variabelen die betrokken zijn bij het nemen van certificaten
Houd rekening met de volgende variabelen en de invloed hiervan op het algehele productie proces. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Waarvan de certificaat basis van de vertrouwens relatie afkomstig is
Het kan kostbaar en complex zijn om een open bare-sleutel infrastructuur (PKI) te beheren.  Met name als uw bedrijf geen ervaring heeft met het beheren van een PKI. Uw opties zijn:
- Gebruik een PKI van derden. U kunt tussenliggende handtekening certificaten kopen bij een certificaat leverancier van derden. U kunt ook een persoonlijke certificerings instantie (CA) gebruiken. 
- Een zelf beheerde PKI gebruiken. U kunt uw eigen PKI-systeem onderhouden en uw eigen certificaten genereren.
- Gebruik de [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/) -beveiligings service. Deze optie is alleen van toepassing op Azure Sphere apparaten. 

#### <a name="where-certificates-are-stored"></a>Waar certificaten worden opgeslagen
Er zijn enkele factoren die van invloed zijn op de beslissing waar certificaten worden opgeslagen. Deze factoren omvatten het type apparaat, verwachte winst marges (of u beveiligde opslag kunt veroorloven), mogelijkheden voor apparaten en bestaande beveiligings technologie op het apparaat dat u mogelijk kunt gebruiken. Houd rekening met de volgende opties:
- In een Hardware Security module (HSM). Het gebruik van een HSM wordt sterk aanbevolen. Controleer of er al een HSM is geïnstalleerd op het controle bord van uw apparaat. Als u weet dat u geen HSM hebt, moet u samen werken met uw hardwarefabrikant om een HSM te identificeren die aan uw behoeften voldoet.
- Op een veilige plaats op schijf, zoals een ' Trusted Execution Environment ' (TEE).
- In het lokale bestands systeem of een certificaat archief. Bijvoorbeeld het Windows-certificaat archief. 

#### <a name="connectivity-at-the-factory"></a>Connectiviteit in de fabriek
Connectiviteit in de fabriek bepaalt hoe en wanneer u de certificaten op de apparaten wilt installeren. Connectiviteits opties zijn als volgt:
- Mogelijkheden. De connectiviteit is optimaal, maar maakt het proces van het lokaal genereren van certificaten gestroomlijnd. 
- Geen connectiviteit. In dit geval gebruikt u een ondertekend certificaat van een CA om apparaten lokaal en offline te genereren. 
- Geen connectiviteit. In dit geval kunt u certificaten verkrijgen die vooraf zijn gegenereerd. U kunt ook een offline-PKI gebruiken om certificaten lokaal te genereren.

#### <a name="audit-requirement"></a>Controle vereiste
Afhankelijk van het type apparaten dat u produceert, hebt u mogelijk een regelgevings vereiste voor het maken van een audittrail van de manier waarop apparaat-id's op uw apparaten worden geïnstalleerd. Controle voegt aanzienlijke productie kosten toe. In de meeste gevallen kunt u dit echter alleen doen als dat nodig is. Als u niet zeker weet of een audit vereist is, raadpleegt u de juridische afdeling van uw bedrijf. Controle opties zijn: 
- Geen gevoelige branche. Er is geen controle vereist.
- Gevoelige industrie. Certificaten moeten worden geïnstalleerd in een beveiligde ruimte volgens de vereisten voor de naleving van de certificerings instantie. Als u een beveiligde ruimte nodig hebt om certificaten te installeren, weet u waarschijnlijk al hoe certificaten op uw apparaten worden geïnstalleerd. En u hebt waarschijnlijk al een controle systeem op locatie. 

#### <a name="length-of-certificate-validity"></a>Geldigheids duur van certificaat
Net als bij een rijbewijs hebben certificaten een verval datum die wordt ingesteld wanneer ze worden gemaakt. Dit zijn de opties voor de lengte van de geldigheid van het certificaat:
- Verlenging is niet vereist.  Deze benadering maakt gebruik van een lange verlengings periode, zodat u het certificaat nooit hoeft te vernieuwen tijdens de levens duur van het apparaat. Hoewel een dergelijke benadering handig is, is dit ook riskant.  U kunt het risico verminderen door gebruik te maken van beveiligde opslag als een HSM op uw apparaten. De aanbevolen procedure is echter om te voor komen dat certificaten met een lange levens duur worden gebruikt.
- Verlenging vereist.  U moet het certificaat vernieuwen tijdens de levens duur van het apparaat. De geldigheids duur van het certificaat is afhankelijk van de context, en u hebt een strategie nodig voor het vernieuwen.  De strategie moet bevatten waar u certificaten krijgt en welk type over-the-Air-functionaliteit uw apparaten moeten gebruiken in het vernieuwings proces. 

### <a name="when-to-generate-certificates"></a>Wanneer u certificaten wilt genereren
De mogelijkheden voor Internet connectiviteit in uw fabriek zijn van invloed op uw proces voor het genereren van certificaten. Er zijn verschillende opties voor het genereren van certificaten: 

- Vooraf geladen certificaten.  Sommige HSM-leveranciers bieden een Premium-Service waarin de HSM-leverancier certificaten voor de klant installeert. Klanten geven de HSM-leverancier eerst toegang tot een handtekening certificaat. De HSM-leverancier installeert certificaten die zijn ondertekend door dat handtekening certificaat op elke HSM die de klant koopt. Alle klanten moeten de HSM installeren op het apparaat. Terwijl deze service kosten toevoegt, helpt u bij het stroom lijnen van uw productie proces.  En de vraag van wanneer certificaten moeten worden geïnstalleerd.
- Door apparaat gegenereerde certificaten.  Als uw apparaten intern certificaten genereren, moet u het open bare X. 509-certificaat ophalen van het apparaat om het in te schrijven in DPS. 
- Verbonden Factory.  Als uw fabriek connectiviteit heeft, kunt u apparaat certificaten genereren wanneer u ze nodig hebt.
- Offline fabriek met uw eigen PKI. Als uw fabriek geen verbinding heeft en u uw eigen PKI gebruikt met offline ondersteuning, kunt u de certificaten genereren wanneer u ze nodig hebt.
- Offline fabriek met PKI van derden. Als uw fabriek geen verbinding heeft en u een PKI van derden gebruikt, moet u de certificaten van tevoren genereren. En het is nodig om de certificaten te genereren op basis van een locatie die verbinding heeft. 

### <a name="when-to-install-certificates"></a>Wanneer u certificaten installeert
Nadat u certificaten voor uw IoT-apparaten hebt gegenereerd, kunt u deze op de apparaten installeren. 

Als u vooraf geladen certificaten met een HSM gebruikt, wordt het proces vereenvoudigd. Nadat de HSM op het apparaat is geïnstalleerd, kan de apparaatcode er toegang toe hebben. Vervolgens roept u de HSM-Api's aan voor toegang tot het certificaat dat is opgeslagen in de HSM. Deze benadering is het handigst voor uw productie proces. 

Als u geen vooraf geladen certificaat gebruikt, moet u het certificaat installeren als onderdeel van het productie proces. De eenvoudigste benadering is het installeren van het certificaat in de HSM op het moment dat u de oorspronkelijke installatie kopie van de firmware flash. In het proces moet een stap worden toegevoegd om de installatie kopie op elk apparaat te installeren. Nadat u deze stap hebt uitgevoerd, kunt u de laatste kwaliteits controles en andere stappen uitvoeren voordat u het apparaat verpakt en verzendt. 

Er zijn software hulpprogramma's beschikbaar waarmee u het installatie proces en de uiteindelijke kwaliteits controle in één stap kunt uitvoeren. U kunt deze hulpprogram ma's wijzigen om een certificaat te genereren of om een certificaat te halen uit een vooraf gegenereerd certificaat archief. Vervolgens kan de software het certificaat installeren waarin u het moet installeren. Met software tools van dit type kunt u productie kwaliteitsborging op schaal uitvoeren. 

Nadat u certificaten op uw apparaten hebt geïnstalleerd, is de volgende stap informatie over het registreren van de apparaten met [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Een TPM integreren in het productie proces
Als u een TPM gebruikt om uw IoT-apparaten te verifiëren, vindt u in deze sectie richt lijnen. De richt lijnen hebben betrekking op de meestgebruikte TPM 2,0-apparaten die ondersteuning bieden voor HMAC-sleutels (Hash-based Message Authentication Code). De TPM-specificatie voor TPM-chips is een ISO-standaard die wordt onderhouden door de Trusted Computing Group. Zie de specificaties voor [tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) en [ISO/IEC 11889](https://www.iso.org/standard/66510.html)voor meer informatie over TPM. 

### <a name="taking-ownership-of-the-tpm"></a>Eigenaar worden van de TPM
Een belang rijke stap bij het produceren van een apparaat met een TPM-chip is om eigenaar te worden van de TPM. Deze stap is vereist zodat u een sleutel voor de eigenaar van het apparaat kunt opgeven. De eerste stap bestaat uit het extra heren van de goedkeurings sleutel (EK) van het apparaat. De volgende stap is om het eigendom te claimen. Hoe u dit doet, is afhankelijk van welke TPM en welk besturings systeem u gebruikt. Als dat nodig is, neemt u contact op met de TPM-fabrikant of de ontwikkelaar van het besturings systeem van het apparaat om te bepalen hoe eigendom moet worden claimen. 

In uw productie proces kunt u de EK en claim eigendom op verschillende tijdstippen extra heren, waardoor flexibiliteit wordt toegevoegd. Veel fabrikanten profiteren van deze flexibiliteit door een HSM (Hardware Security module) toe te voegen voor het verbeteren van de beveiliging van hun apparaten. Deze sectie bevat richt lijnen voor het uitpakken van de EK, wanneer u het eigendom van de TPM kunt claimen en overwegingen voor het integreren van deze stappen in een productie tijdlijn. 

> [!IMPORTANT]
> In de volgende richt lijnen wordt ervan uitgegaan dat u een afzonderlijke, firmware of geïntegreerde TPM gebruikt. Op plaatsen waar deze van toepassing is, voegt de richt lijnen notities toe voor het gebruik van een niet-discrete of software-TPM. Als u een software-TPM gebruikt, zijn er mogelijk extra stappen die deze richt lijn niet bevat. Software-Tpm's hebben een aantal implementaties die buiten het bereik van dit artikel vallen.  Over het algemeen is het mogelijk om een software-TPM te integreren in de volgende algemene productie tijdlijn. Hoewel een geëmuleerde software-TPM geschikt is voor het maken van prototypen en testen, is het niet mogelijk om hetzelfde beveiligings niveau te bieden als een afzonderlijke, firmware of geïntegreerde TPM. In het algemeen kunt u het gebruik van een software-TPM in productie vermijden.

### <a name="general-manufacturing-timeline"></a>Algemene productie tijdlijn
In de volgende tijd lijn ziet u hoe een TPM een productie proces doorloopt en eindigt op een apparaat. Elk productie proces is uniek en deze tijd lijn toont de meest voorkomende patronen. De tijd lijn biedt richt lijnen voor het uitvoeren van bepaalde acties met de sleutels. 

#### <a name="step-1-tpm-is-manufactured"></a>Stap 1: TPM is vervaardigd
- Als u Tpm's van een fabrikant koopt voor gebruik op uw apparaten, raadpleeg dan of ze open bare goedkeurings sleutels (EK_pubs) voor u ophalen. Het is handig als de fabrikant de lijst met EK_pubs met de geleverde apparaten bevat. 
    > [!NOTE]
    > U kunt de TPM-fabrikant schrijf toegang tot uw registratie lijst geven door gebruik te maken van het beleid voor gedeelde toegang in uw inrichtings service.  Met deze aanpak kunnen ze de Tpm's toevoegen aan de registratie lijst voor u.  Maar dat is vroeger in het productie proces en hiervoor is vertrouwen vereist in de TPM-fabrikant. Doe dit voor uw eigen risico. 

- Als u Tpm's fabriceert om te verkopen aan fabrikanten van apparaten, kunt u overwegen om uw klanten een lijst met EK_pubs samen met hun fysieke Tpm's te geven.  Door klanten te voorzien van EK_pubs slaat een stap op in hun proces. 
- Als u Tpm's fabriceert om te gebruiken met uw eigen apparaten, identificeert u welk punt in uw proces het handigst is om de EK_pub uit te pakken. U kunt de EK_pub op elk van de resterende punten in de tijd lijn extra heren. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Stap 2: TPM is geïnstalleerd op een apparaat
Op dit moment in het productie proces moet u weten met welk DPS-exemplaar het apparaat wordt gebruikt. Als gevolg hiervan kunt u apparaten toevoegen aan de registratie lijst voor automatische inrichting. Zie de [DPS-documentatie](about-iot-dps.md)voor meer informatie over het automatisch inrichten van apparaten.
- Als u de EK_pub nog niet hebt uitgepakt, is dit nu een goed moment. 
- Afhankelijk van het installatie proces van de TPM is deze stap ook een goed moment om eigenaar te worden van de TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Stap 3: er zijn firmware en software geïnstalleerd op het apparaat
Installeer op dit punt in het proces de DPS-client samen met het ID-bereik en de globale URL voor het inrichten.
- Nu is de laatste kans om de EK_pub uit te pakken. Als een derde partij de software op uw apparaat installeert, is het een goed idee om eerst de EK_pub te extra heren.
- Dit punt in het productie proces is ideaal om eigenaar te worden van de TPM.  
    > [!NOTE]
    > Als u een software-TPM gebruikt, kunt u deze nu installeren.  Pak de EK_pub tegelijk uit.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Stap 4: het apparaat wordt verpakt en naar het magazijn verzonden
Een apparaat kan gedurende 6-12 maanden in een magazijn zitten voordat het wordt geïmplementeerd. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Stap 5: het apparaat wordt op de locatie geïnstalleerd
Wanneer het apparaat op de uiteindelijke locatie arriveert, wordt het geautomatiseerd ingericht met DPS.

Zie [inrichten](about-iot-dps.md#provisioning-process) en [TPM-Attestation](concepts-tpm-attestation.md)(Engelstalig) voor meer informatie. 

## <a name="resources"></a>Resources

Naast de aanbevolen beveiligings procedures in dit artikel biedt Azure IoT bronnen om u te helpen bij het selecteren van beveiligde hardware en het maken van veilige IoT-implementaties: 
- Aanbevelingen voor Azure IoT- [beveiliging](../iot-fundamentals/security-recommendations.md) om het implementatie proces te begeleiden. 
- De [Azure Security Center](https://azure.microsoft.com/services/security-center/) biedt een service om veilige IOT-implementaties te maken. 
- Zie voor meer informatie over het evalueren van uw hardware-omgeving de White Paper over het [evalueren van uw IOT-beveiliging](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Zie [de juiste veilige hardware voor uw IOT-implementatie](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf)voor meer informatie over het selecteren van beveiligde hardware. 