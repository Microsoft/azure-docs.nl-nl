---
title: De OPC kluis Certificate Management-service veilig uitvoeren-Azure | Microsoft Docs
description: Hierin wordt beschreven hoe u de OPC kluis Certificate Management-service veilig in azure uitvoert en andere beveiligings richtlijnen kunt bedenken.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f54467c03a67797d52ac499ca9ba455c0f75e240
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92071452"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>De OPC kluis Certificate Management-service veilig uitvoeren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

In dit artikel wordt uitgelegd hoe u de OPC kluis Certificate Management-service veilig in azure kunt uitvoeren en andere beveiligings richtlijnen kunt bedenken.

## <a name="roles"></a>Rollen

### <a name="trusted-and-authorized-roles"></a>Vertrouwde en geautoriseerde rollen

Met de OPC-micro service kunt u afzonderlijke rollen gebruiken om toegang te krijgen tot verschillende onderdelen van de service.

> [!IMPORTANT]
> Tijdens de implementatie voegt het script alleen de gebruiker toe die het implementatie script uitvoert als een gebruiker voor alle rollen. Voor een productie-implementatie moet u deze roltoewijzing controleren en op de juiste wijze configureren door de onderstaande richt lijnen te volgen. Deze taak vereist hand matige toewijzing van functies en services in de Enter prise-portal van Azure Active Directory (Azure AD).

### <a name="certificate-management-service-roles"></a>Service rollen voor certificaat beheer

De OPC-kluis micro service definieert de volgende rollen:

- **Lezer**: standaard heeft elke geverifieerde gebruiker in de Tenant Lees toegang. 
  - Lees toegang tot toepassingen en certificaat aanvragen. Kan toepassingen en certificaat aanvragen weer geven en er query's op uitvoeren. Detectie gegevens van apparaten en open bare certificaten zijn ook toegankelijk met lees toegang.
- **Writer**: de rol schrijver wordt toegewezen aan een gebruiker om schrijf machtigingen voor bepaalde taken toe te voegen. 
  - Lees-/schrijftoegang tot toepassingen en certificaat aanvragen. Kan toepassingen registreren, bijwerken en verwijderen. Kan certificaat aanvragen maken en goedgekeurde persoonlijke sleutels en certificaten verkrijgen. Kan ook persoonlijke sleutels verwijderen.
- **Goed keurder**: de rol van de goed keurder is toegewezen aan een gebruiker om certificaat aanvragen goed te keuren of af te wijzen. De rol bevat geen andere functies.
  - Naast de rol fiatteur om toegang te krijgen tot de OPC kluis micro Service-API, moet de gebruiker ook beschikken over de machtiging voor sleutel ondertekening in Azure Key Vault om de certificaten te kunnen ondertekenen.
  - De rol schrijver en goed keurder moet aan verschillende gebruikers worden toegewezen.
  - De belangrijkste rol van de goed keurder is het goed keuren van het genereren en afkeuren van certificaat aanvragen.
- **Beheerder**: de beheerdersrol is toegewezen aan een gebruiker om de certificaat groepen te beheren. De rol biedt geen ondersteuning voor de functie goed keurder, maar bevat de rol schrijver.
  - De beheerder kan de certificaat groepen beheren, de configuratie wijzigen en toepassings certificaten intrekken door een nieuwe certificaatintrekkingslijst (CRL) uit te geven.
  - In het ideale geval worden de rollen schrijver, goed keurder en beheerder toegewezen aan verschillende gebruikers. Voor extra beveiliging moet een gebruiker met de rol goed keurder of beheerder ook beschikken over de machtiging voor het ondertekenen van sleutels in Key Vault, het uitgeven van certificaten of het vernieuwen van een certificaat van een certificerings instantie.
  - Naast de rol van micro Service-beheer, bevat de rol, maar is niet beperkt tot:
    - Verantwoordelijkheid voor het beheren van de implementatie van de beveiligings procedures van de CA.
    - Beheer van het genereren, intrekken en opschorten van certificaten. 
    - Beheer van de levens cyclus van cryptografische sleutels (bijvoorbeeld de verlenging van de CA-sleutels van de uitgever).
    - Installatie, configuratie en onderhoud van services die de CA uitvoeren.
    - Dagelijkse werking van de services. 
    - Certificerings instantie en back-up en herstel van de data base.

### <a name="other-role-assignments"></a>Andere roltoewijzingen

Houd ook rekening met de volgende rollen wanneer u de-service uitvoert:

- Zakelijke eigenaar van het certificaat voor de inkoop van certificaten met de externe basis certificerings instantie (bijvoorbeeld wanneer de eigenaar certificaten koopt van een externe CA of een CA ondergaat die ondergeschikt is aan een externe CA).
- Ontwikkeling en validatie van de certificerings instantie.
- Controle records controleren.
- Mede werkers die ondersteuning bieden voor de CA of het beheer van de fysieke en Cloud faciliteiten, maar niet rechtstreeks worden vertrouwd voor het uitvoeren van CA-bewerkingen, bevinden zich in de *gemachtigde* rol. De set taken die in de gemachtigde rol kunnen worden uitgevoerd, moet ook worden gedocumenteerd.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Lidmaatschappen van vertrouwde en geautoriseerde rollen per kwar taal controleren

Controleer het lidmaatschap van vertrouwde en geautoriseerde rollen ten minste één kwar taal. Zorg ervoor dat de verzameling personen (voor hand matige processen) of service-identiteiten (voor automatische processen) in elke rol tot een minimum wordt beperkt.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Functie scheiding tussen certificaat aanvrager en goed keurder

Het certificaat uitgifte proces moet functie scheiding afdwingen tussen de certificaat aanvrager en de rollen van de goed keurder van het certificaat (personen of geautomatiseerde systemen). Certificaat uitgifte moet worden geautoriseerd door een rol van een certificaat goed keurder die verifieert of de certificaat aanvrager toestemming heeft om certificaten te verkrijgen. De personen die de rol van certificaat goed keurder hebben, moeten een formeel gemachtigde persoon zijn.

### <a name="restrict-assignment-of-privileged-roles"></a>Toewijzing van geprivilegieerde rollen beperken

U moet de toewijzing van geprivilegieerde rollen beperken, zoals het machtigen van lidmaatschap van de groep Administrators en goed keurders tot een beperkt aantal geautoriseerde mede werkers. Alle wijzigingen van geprivilegieerde rollen moeten binnen 24 uur toegang hebben ingetrokken. Ten slotte kunt u privileged Role Assignments op elk kwar taal controleren en overbodige of verlopen toewijzingen verwijderen.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Geprivilegieerde rollen moeten gebruikmaken van twee ledige verificatie

Gebruik multi-factor Authentication (ook wel twee ledige verificatie genoemd) voor interactieve aanmeldingen van goed keurders en beheerders bij de service.

## <a name="certificate-service-operation-guidelines"></a>Richt lijnen voor de werking van de certificaat service

### <a name="operational-contacts"></a>Operationele contact personen

De Certificate-Service moet een up-to-date beveiligings antwoord plan hebben dat gedetailleerde contact personen voor operationele incidenten bevat.

### <a name="security-updates"></a>Beveiligingsupdates

Alle systemen moeten voortdurend worden bewaakt en bijgewerkt met de meest recente beveiligings updates.

> [!IMPORTANT]
> De GitHub-opslag plaats van de OPC-kluis service wordt voortdurend bijgewerkt met beveiligings patches. Controleer deze updates en pas ze met regel matige tussen pozen toe op de service.

### <a name="security-monitoring"></a>Beveiligingsbewaking

Abonneer u op of implementeer de juiste beveiligings bewaking. Neem bijvoorbeeld een abonnement op een centrale bewakings oplossing (zoals Azure Security Center of Microsoft 365 bewakings oplossing) en configureer deze op de juiste manier om ervoor te zorgen dat beveiligings gebeurtenissen worden verzonden naar de bewakings oplossing.

> [!IMPORTANT]
> Standaard wordt de OPC-kluis service geïmplementeerd met [Azure-toepassing Insights](../azure-monitor/app/devops.md) als een bewakings oplossing. Het is raadzaam om een beveiligings oplossing toe te voegen, zoals [Azure Security Center](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-the-security-of-open-source-software-components"></a>De beveiliging van open-source software onderdelen evalueren

Alle open source-onderdelen die in een product of service worden gebruikt, mogen geen beveiligings problemen met een gemiddelde of een grotere beveiliging hebben.

> [!IMPORTANT]
> Tijdens doorlopende integratie versies scant de GitHub-opslag plaats van de OPC-kluis service alle onderdelen op beveiligings problemen. Controleer deze updates op GitHub en pas ze met regel matige tussen pozen toe op de service.

### <a name="maintain-an-inventory"></a>Een inventaris onderhouden

Onderhoud van een activa-inventaris voor alle productie-hosts (inclusief permanente virtuele machines), apparaten, alle interne IP-adresbereiken, Vip's en open bare DNS-domein namen. Telkens wanneer u een systeem, IP-adres van apparaat, VIP of openbaar DNS-domein toevoegt of verwijdert, moet u de inventaris binnen 30 dagen bijwerken.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventarisatie van de standaard implementatie van Azure OPC kluis micro Services productie 

Doe het volgende in Azure:
- **App service plan**: app service-plan voor service-hosts. Standaard S1.
- **App service** voor micro service: de OPC kluis servicehost.
- **App service** voor voorbeeld toepassing: de voor beeld-Toepassingshost van de OPC-kluis.
- **Key Vault standaard**: voor het opslaan van geheimen en Azure Cosmos DB sleutels voor de webservices.
- **Key Vault Premium**: voor het hosten van de CA-sleutels van de verlener, voor het ondertekenen van service en voor de kluis configuratie en opslag van persoonlijke sleutels van toepassingen.
- **Azure Cosmos DB**: Data Base voor toepassings-en certificaat aanvragen. 
- **Application Insights**: (optioneel) bewakings oplossing voor de webservice en toepassing.
- **Registratie van Azure AD-toepassing**: een registratie voor de voorbeeld toepassing, de service en de Edge-module.

Voor de Cloud Services moeten alle hostnamen, resource groepen, resource namen, abonnements-Id's en Tenant-Id's die worden gebruikt voor het implementeren van de service worden gedocumenteerd. 

In Azure IoT Edge of een lokale IoT Edge Server:
- **OPC-kluis IOT Edge module**: ter ondersteuning van een Factory Network OPC UA Global Discovery-server. 

Voor de IoT Edge-apparaten moeten de hostnamen en IP-adressen worden gedocumenteerd. 

### <a name="document-the-certification-authorities-cas"></a>De certificerings instanties (Ca's) documenteren

De documentatie van de CA-hiërarchie moet alle geëxploiteerde certificerings instanties bevatten. Dit omvat alle verwante onderliggende Ca's, bovenliggende Ca's en basis certificerings instanties, zelfs wanneer deze niet door de service worden beheerd. In plaats van formele documentatie kunt u een volledige set van alle niet-verlopen CA-certificaten opgeven.

> [!NOTE]
> De OPC-kluis voorbeeld toepassing ondersteunt het downloaden van alle certificaten die in de service voor documentatie worden gebruikt en gemaakt.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>De verleende certificaten documenteren door alle certificerings instanties (Ca's)

Geef een volledig aantal certificaten op dat in de afgelopen twaalf maanden is uitgegeven.

> [!NOTE]
> De OPC-kluis voorbeeld toepassing ondersteunt het downloaden van alle certificaten die in de service voor documentatie worden gebruikt en gemaakt.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>De standaard-werk procedure voor het veilig verwijderen van cryptografische sleutels documenteren

Tijdens de levens duur van een CA kan de sleutel niet meer worden verwijderd. Daarom heeft geen enkele gebruiker Key Vault certificaat verwijderen recht toegewezen en waarom er geen Api's beschikbaar zijn voor het verwijderen van een certificaat van een certificerings instantie. De hand matige standaard werk wijze voor het veilig verwijderen van cryptografische sleutels van certificerings instanties is alleen beschikbaar door rechtstreeks toegang te krijgen tot Key Vault in de Azure Portal. U kunt ook de certificaat groep in Key Vault verwijderen. Schakel de [Key Vault Soft](../key-vault/general/soft-delete-overview.md) -functie voor verwijderen uit om ervoor te zorgen dat deze direct wordt verwijderd.

## <a name="certificates"></a>Certificaten

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certificaten moeten voldoen aan het minimum certificaat profiel

De OPC-kluis service is een online certificerings instantie die eind entity certificaten verleent aan abonnees. De OPC-kluis micro service volgt deze richt lijnen in de standaard implementatie.

- Alle certificaten moeten de volgende X. 509-velden bevatten, zoals hieronder is aangegeven:
  - De inhoud van het versie veld moet v3 zijn. 
  - De inhoud van het veld serialNumber moet ten minste 8 bytes van de entropie bevatten die is verkregen van een FIPS (Federal Information Processing Standards) 140 goedgekeurde wille keurige code ring.<br>
    > [!IMPORTANT]
    > Het OPC-kluis serie nummer is standaard 20 bytes en wordt opgehaald uit het besturings systeem cryptografische wille keurig aantal generator. De generator voor wille keurige getallen is FIPS 140 goedgekeurd op Windows-apparaten, maar niet in Linux. Houd rekening met het volgende wanneer u een service-implementatie kiest die gebruikmaakt van Linux Vm's of Linux docker-containers, waarbij de onderliggende technologie OpenSSL niet is goedgekeurd voor FIPS 140.
  - De velden issuerUniqueID en subjectUniqueID mogen niet aanwezig zijn.
  - Certificaten van eind entiteiten moeten worden aangeduid met de extensie basis beperkingen, in overeenstemming met IETF RFC 5280.
  - Het veld pathLenConstraint moet worden ingesteld op 0 voor het certificaat van de verlenende CA. 
  - De uitbrei ding voor uitgebreide-sleutel gebruik moet aanwezig zijn en moet de minimale set met object-id's (Oid's) voor uitgebreide-sleutel gebruik bevatten. De OID van de anyExtendedKeyUsage (2.5.29.37.0) moet niet worden opgegeven. 
  - De CRL-distributie punt (CDP) uitbrei ding moet aanwezig zijn in het CA-certificaat van de certificaat verlener.<br>
    > [!IMPORTANT]
    > De CDP-uitbrei ding is aanwezig in OPC-kluis CA-certificaten. OPC UA-apparaten gebruiken echter aangepaste methoden voor het distribueren van Crl's.
  - De extensie voor toegang tot CA-gegevens moet aanwezig zijn in de abonnee certificaten.<br>
    > [!IMPORTANT]
    > De extensie voor toegang tot CA-gegevens is aanwezig in OPC kluis abonnee certificaten. De OPC UA-apparaten gebruiken echter aangepaste methoden voor het distribueren van CA-gegevens van de uitgever.
- Goedgekeurde asymmetrische algoritmen, sleutel lengten, hash-functies en opvullings modi moeten worden gebruikt.
  - RSA en SHA-2 zijn de enige algoritmen die worden ondersteund.
  - RSA kan worden gebruikt voor versleuteling, sleutel uitwisseling en hand tekening.
  - RSA-versleuteling moet alleen de opvullings modi OAEP, RSA-KEM of RSA-PSS gebruiken.
  - Sleutel lengten die groter zijn dan of gelijk zijn aan 2048 bits zijn vereist.
  - Gebruik de SHA-2-familie van hash-algoritmen (SHA256, SHA384 en SHA512 gebruikt).
  - RSA-basis-CA-sleutels met een typische levens duur die groter is dan of gelijk is aan 20 jaar, moeten 4096 bits of meer zijn.
  - CA-sleutels voor RSA-verlener moeten ten minste 2048 bits zijn. Als de verval datum van het CA-certificaat na 2030 is, moet de CA-sleutel 4096 bits of hoger zijn.
- Levens duur van certificaat
  - Basis-CA-certificaten: de maximale geldigheids periode van het certificaat voor basis-Ca's mag niet langer zijn dan 25 jaar.
  - CA-certificaten voor subcertificerings instanties of online uitgevers: de maximale geldigheids periode van het certificaat voor Ca's die online zijn en alleen abonnee certificaten verlenen, mag niet langer zijn dan 6 jaar. Voor deze Ca's mag de gerelateerde persoonlijke handtekening sleutel niet langer dan drie jaar worden gebruikt voor het uitgeven van nieuwe certificaten.<br>
    > [!IMPORTANT]
    > Het certificaat van de certificerings instantie, zoals het wordt gegenereerd in de standaard OPC-kluis micro service zonder externe basis certificerings instantie, wordt beschouwd als een online subca, met de betreffende vereisten en levens duur. De standaard levensduur wordt ingesteld op 5 jaar, met een sleutel lengte die groter is dan of gelijk is aan 2048.
  - Alle asymmetrische sleutels moeten een maximale levens duur van vijf jaar hebben en een aanbevolen levens duur van 1 jaar.<br>
    > [!IMPORTANT]
    > De levens duur van toepassings certificaten die worden uitgegeven met de OPC-kluis hebben standaard een levens duur van 2 jaar en moeten elk jaar worden vervangen. 
  - Wanneer een certificaat wordt vernieuwd, wordt het vernieuwd met een nieuwe sleutel.
- OPC UA-specifieke uitbrei dingen in toepassings exemplaar certificaten
  - De subjectAltName-extensie bevat de toepassings-URI en hostnamen. Deze kunnen ook FQDN-, IPv4-en IPv6-adressen bevatten.
  - Het gebruik van de digitalSignature, afwijzing, keyEncipherment en dataEncipherment.
  - De extendedKeyUsage omvat serverAuth en clientAuth.
  - De authorityKeyIdentifier wordt opgegeven in ondertekende certificaten.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>CA-sleutels en certificaten moeten voldoen aan de minimum vereisten

- **Persoonlijke sleutels**: RSA-sleutels moeten ten minste 2048 bits zijn. Als de verval datum van het CA-certificaat na 2030 is, moet de CA-sleutel 4096 bits of hoger zijn.
- **Levens duur**: de maximale geldigheids periode van het certificaat voor ca's die online zijn en alleen abonnee certificaten mogen niet langer zijn dan 6 jaar. Voor deze Ca's mag de gerelateerde persoonlijke handtekening sleutel niet langer dan drie jaar worden gebruikt voor het uitgeven van nieuwe certificaten.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>CA-sleutels worden beveiligd met behulp van hardware security modules

OpcVault maakt gebruik van Azure Key Vault Premium en sleutels worden beveiligd door de HSM (Hardware Security modules) van FIPS 140-2 level 2. 

De cryptografische modules die Key Vault gebruikt, of HSM of software, FIPS-gevalideerd zijn. Sleutels die zijn gemaakt of geïmporteerd als met HSM beveiligd, worden verwerkt in een HSM, gevalideerd op FIPS 140-2 level 2. Sleutels die zijn gemaakt of geïmporteerd als software-beveiligd, worden verwerkt in cryptografische modules die zijn gevalideerd voor FIPS 140-2 level 1.

## <a name="operational-practices"></a>Operationele procedures

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Standaard operationele PKI-procedures voor certificaat inschrijving documenteren en onderhouden

Standaardwerk procedures (Vereisteverklaringen) documenteren en onderhouden voor de manier waarop CAs certificaten uitgeeft, waaronder: 
- Hoe de abonnee wordt geïdentificeerd en geverifieerd. 
- Hoe de certificaat aanvraag wordt verwerkt en gevalideerd (indien van toepassing, moet u ook zien hoe certificaat vernieuwing en aanvragen voor opnieuw genereren worden verwerkt). 
- Hoe verleende certificaten worden gedistribueerd naar de abonnees. 

De OPC kluis micro service VKA wordt beschreven in [OPC-kluis architectuur](overview-opc-vault-architecture.md) en [beheert de OPC-kluis certificaat service](howto-opc-vault-manage.md). De procedures volgen ' OPC Unified architecture specification part ' 12: Discovery and Global Services. '


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Standaard operationele PKI-procedures voor het intrekken van certificaten documenteren en onderhouden

Het intrekkings proces van het certificaat wordt beschreven in [OPC-kluis architectuur](overview-opc-vault-architecture.md) en [beheert de OPC-kluis certificaat service](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Ceremonie van CA-sleutel generatie documenteren 

De CA-sleutel van de certificaat verlener in de OPC-kluis micro service is vereenvoudigd, vanwege de beveiligde opslag in Azure Key Vault. Zie [de OPC-kluis certificaat service beheren](howto-opc-vault-manage.md)voor meer informatie.

Wanneer u echter een externe basis certificerings instantie gebruikt, moet een generatie van de CA-sleutel voldoen aan de volgende vereisten.

Het genereren van de CA-sleutel moet worden uitgevoerd op basis van een gedocumenteerde script dat ten minste de volgende items bevat: 
- Definitie van rollen en verantwoordelijkheden van deel nemers.
- Goed keuring voor het genereren van de CA-sleutel generatie.
- Cryptografische hardware en activerings materialen die vereist zijn voor de ceremonie.
- Hardware-voor bereiding (inclusief Asset-en configuratie-informatie bijwerken en afmelden).
- Installatie van het besturings systeem.
- Specifieke stappen die worden uitgevoerd tijdens het genereren van de CA-sleutel, zoals: 
  - Installatie en configuratie van de CA-toepassing.
  - CA-sleutel genereren.
  - CA-sleutel back-up.
  - CA-certificaat ondertekening.
  - Het importeren van ondertekende sleutels in de beveiligde HSM van de service.
  - Het CA-systeem wordt afgesloten.
  - Voor bereiding van materialen voor opslag.


## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u veilig OPC-kluis beheert, kunt u het volgende doen:

> [!div class="nextstepaction"]
> [Veilige OPC UA-apparaten met OPC-kluis](howto-opc-vault-secure.md)