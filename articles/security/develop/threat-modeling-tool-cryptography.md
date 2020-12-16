---
title: Crypto grafie-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Meer informatie over de oplossing van crypto grafie voor bedreigingen die worden weer gegeven in de Threat Modeling Tool. Zie informatie over risico beperking en voor beelden van code weer geven.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 6af4fba8ddf50b795d847a2c7b4e2fbc02fe593f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587176"
---
# <a name="security-frame-cryptography--mitigations"></a>Beveiligings frame: crypto grafie | Oplossingen 

| Product/service | Artikel |
| --------------- | ------- |
| **Webtoepassing** | <ul><li>[Alleen goedgekeurde symmetrische blok versleuteling en sleutel lengten gebruiken](#cipher-length)</li><li>[Goedgekeurde blok coderings modi en initialisatie vectoren voor symmetrische code ringen gebruiken](#vector-ciphers)</li><li>[Goedgekeurde asymmetrische algoritmen, sleutel lengten en opvulling gebruiken](#padding)</li><li>[Goedgekeurde Generators voor wille keurige getallen gebruiken](#numgen)</li><li>[Geen symmetrische stroom versleuteling gebruiken](#stream-ciphers)</li><li>[Goedgekeurde MAC/HMAC/versleutelings hash-algoritmen gebruiken](#mac-hash)</li><li>[Alleen goedgekeurde cryptografische hash-functies gebruiken](#hash-functions)</li></ul> |
| **Database** | <ul><li>[Sterke versleutelings algoritmen gebruiken om gegevens in de data base te versleutelen](#strong-db)</li><li>[SSIS-pakketten moeten worden versleuteld en digitaal ondertekend](#ssis-signed)</li><li>[Digitale hand tekening toevoegen aan kritieke data base-Beveilig bare items](#securables-db)</li><li>[SQL Server EKM gebruiken om versleutelings sleutels te beveiligen](#ekm-keys)</li><li>[De functie AlwaysEncrypted gebruiken als de versleutelings sleutels niet moeten worden getoond in de data base-engine](#keys-engine)</li></ul> |
| **IoT-apparaat** | <ul><li>[Cryptografische sleutels veilig opslaan op IoT-apparaat](#keys-iot)</li></ul> | 
| **IoT-Cloud gateway** | <ul><li>[Een wille keurige symmetrische sleutel met voldoende lengte voor authenticatie voor de IoT Hub genereren](#random-hub)</li></ul> | 
| **Dynamics CRM Mobile-client** | <ul><li>[Zorg ervoor dat er een beheer beleid voor apparaten is dat een pincode voor het gebruik van het apparaat vereist en Extern wissen toestaat](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook-client** | <ul><li>[Zorg ervoor dat er een beheer beleid voor apparaten is dat een pincode of wacht woord/automatische vergren deling vereist en alle gegevens versleutelt (bijvoorbeeld BitLocker)](#bitlocker)</li></ul> | 
| **Identiteits server** | <ul><li>[Zorg ervoor dat de handtekening sleutels worden doorgevoerd wanneer identiteits server wordt gebruikt](#rolled-server)</li><li>[Zorg ervoor dat de cryptografische sterke client-ID, het client geheim, wordt gebruikt in de identiteits server](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Alleen goedgekeurde symmetrische blok versleuteling en sleutel lengten gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Producten moeten alleen gebruikmaken van de symmetrische blok cijfers en de bijbehorende sleutel lengten die expliciet zijn goedgekeurd door de crypto Advisor in uw organisatie. Goedgekeurde symmetrische algoritmen bij micro soft omvatten de volgende blok versleuteling:</p><ul><li>Voor nieuwe code-AES-128, AES-192 en AES-256 zijn toegestaan</li><li>Voor achterwaartse compatibiliteit met bestaande code is 3DES met drie sleutels acceptabel</li><li>Voor producten die gebruikmaken van symmetrische blok cijfers:<ul><li>Advanced Encryption Standard (AES) is vereist voor nieuwe code</li><li>Triple Data Encryption Standard (3DES) met drie sleutels is toegestaan in bestaande code voor achterwaartse compatibiliteit</li><li>Alle andere blok versleuteling, inclusief RC2, DES, 2 sleutel 3DES, DESX en skipjack, mogen alleen worden gebruikt voor het ontsleutelen van oude gegevens en moet worden vervangen als deze wordt gebruikt voor versleuteling</li></ul></li><li>Voor symmetrische blok versleutelings algoritmen is een minimum sleutel lengte van 128 bits vereist. De enige algoritme voor blok versleuteling die wordt aanbevolen voor nieuwe code is AES (AES-128, AES-192 en AES-256 zijn toegestaan)</li><li>3DES met drie sleutels is momenteel acceptabel als deze al in gebruik is in bestaande code; overgang naar AES wordt aanbevolen. DES, DESX, RC2 en skipjack worden niet meer als veilig beschouwd. Deze algoritmen mogen alleen worden gebruikt voor het ontsleutelen van bestaande gegevens ten behoeve van achterwaartse compatibiliteit en gegevens moeten opnieuw worden versleuteld met een aanbevolen blok codering</li></ul><p>Houd er rekening mee dat alle symmetrische blok versleuteling moet worden gebruikt met een goedgekeurde versleutelings modus, waarvoor het gebruik van een geschikte initialisatie vector (IV) vereist is. Een geschikte IV is doorgaans een wille keurig getal en nooit een constante waarde</p><p>Het gebruik van verouderde of anderszins niet-goedgekeurde crypto-algoritmen en kleinere sleutel lengten voor het lezen van bestaande gegevens (in tegens telling tot het schrijven van nieuwe gegevens) is mogelijk toegestaan na de beoordeling van de cryptografie kaart van uw organisatie. U moet echter een uitzonde ring op deze vereiste hebben. In Enter prise-implementaties moeten producten bovendien waarschuwings beheerders kunnen overwegen wanneer zwakke crypto grafie wordt gebruikt om gegevens te lezen. Dergelijke waarschuwingen moeten worden gewaarschuwd en kunnen acties worden uitgevoerd. In sommige gevallen kan het nodig zijn om groepsbeleid controle over het gebruik van zwakke crypto grafie</p><p>Toegestane .NET-algoritmen voor de flexibiliteit van beheerde crypto grafie (in volg orde van voor keur)</p><ul><li>AesCng (FIPS-compatibel)</li><li>AuthenticatedAesCng (FIPS-compatibel)</li><li>AESCryptoServiceProvider (FIPS-compatibel)</li><li>AESManaged (niet-FIPS-compatibel)</li></ul><p>Houd er rekening mee dat u geen van deze algoritmen kunt opgeven via de `SymmetricAlgorithm.Create` of- `CryptoConfig.CreateFromName` methoden zonder wijzigingen aan te brengen in het machine.config-bestand. Houd er ook rekening mee dat AES in versies van .NET vóór .NET 3,5 de naam heeft en `RijndaelManaged` `AesCng` `AuthenticatedAesCng` >beschikbaar is via CODEPLEX en dat CNG in het onderliggende besturings systeem vereist is</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Goedgekeurde blok coderings modi en initialisatie vectoren voor symmetrische code ringen gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Alle symmetrische blok versleuteling moeten worden gebruikt met een goedgekeurde symmetrische-coderings modus. De enige goedgekeurde modi zijn CBC en CTS. In het bijzonder moet de werk wijze van het elektronisch code boek (de ECB) worden vermeden; het gebruik van de ECB vereist de beoordeling van het crypto Board van uw organisatie. Het gebruik van OFB, CFB, plaats, CCM en GCM of een andere versleutelings modus moet worden gecontroleerd door de crypto-kaart van uw organisatie. Als u dezelfde initialisatie vector (IV) opnieuw gebruikt met blok versleuteling in de modi streaming-code ringen, zoals plaats, kunnen versleutelde gegevens worden onthuld. Alle symmetrische blok versleuteling moeten ook worden gebruikt met een geschikte initialisatie vector (IV). Een geschikte IV is een cryptografisch sterk, wille keurig getal en nooit een constante waarde. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Goedgekeurde asymmetrische algoritmen, sleutel lengten en opvulling gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Door het gebruik van verboden cryptografische algoritmen wordt een aanzienlijk risico op de product beveiliging geïntroduceerd en moet worden vermeden. Producten moeten alleen gebruikmaken van cryptografische algoritmen en de bijbehorende sleutel lengten en opvulling die expliciet zijn goedgekeurd door de crypto-kaart van uw organisatie.</p><ul><li>**RSA:** kan worden gebruikt voor versleuteling, sleutel uitwisseling en hand tekening. RSA-versleuteling moet alleen de opvullings modi OAEP of RSA-KEM gebruiken. Bestaande code kan de modus PKCS #1 v 1.5 alleen gebruiken voor de compatibiliteit. Het gebruik van Null-opvulling is expliciet verboden. Sleutels >= 2048 bits is vereist voor nieuwe code. Bestaande code ondersteunt sleutels < 2048 bits alleen voor neerwaartse compatibiliteit na een beoordeling door de crypto kaart van uw organisatie. Sleutels < 1024-bits mogen alleen worden gebruikt voor het ontsleutelen/verifiëren van oude gegevens, en moet worden vervangen als deze wordt gebruikt voor versleutelings-of handtekening bewerkingen</li><li>**ECDSA-** mag alleen worden gebruikt voor hand tekening. ECDSA met >= 256-bits sleutels is vereist voor nieuwe code. Op ECDSA gebaseerde hand tekeningen moeten een van de drie door NIST goedgekeurde curven (P-256, P-384 of P521) gebruiken. Curven die grondig zijn geanalyseerd, kunnen alleen worden gebruikt na een beoordeling met de crypto-kaart van uw organisatie.</li><li>**ECDH-** mag alleen worden gebruikt voor sleutel uitwisseling. ECDH met >= 256-bits sleutels is vereist voor nieuwe code. Op ECDH gebaseerde sleutel uitwisseling moet een van de drie door NIST goedgekeurde curven (P-256, P-384 of P521) worden gebruikt. Curven die grondig zijn geanalyseerd, kunnen alleen worden gebruikt na een beoordeling met de crypto-kaart van uw organisatie.</li><li>**Dsa-** kan acceptabel zijn na beoordeling en goed keuring van de crypto-kaart van uw organisatie. Neem contact op met uw Security Advisor om de controle van de crypto grafie van uw organisatie te plannen. Als uw gebruik van DSA is goedgekeurd, moet u er rekening mee houden dat het gebruik van sleutels van minder dan 2048 bits mag worden verboden. CNG ondersteunt 2048-bits en grotere sleutel lengten vanaf Windows 8.</li><li>**Diffie-Hellman-** kan alleen worden gebruikt voor sessie sleutel beheer. Sleutel lengte >= 2048 bits is vereist voor nieuwe code. Bestaande code ondersteunt mogelijk sleutel lengten < 2048 bits voor achterwaartse compatibiliteit na een beoordeling door de crypto kaart van uw organisatie. Sleutels < 1024-bits mogen niet worden gebruikt.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Goedgekeurde Generators voor wille keurige getallen gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Producten moeten goedgekeurde wille keurige regenerators gebruiken. Pseudorandom functions, zoals de functie ASELECT van de C-runtime, het .NET Framework klassen systeem. wille keurige of systeem functies, zoals GetTickCount, moeten daarom nooit in dergelijke code worden gebruikt. Het gebruik van de dubbele elliptische curve-algoritme voor het genereren van wille keurige getallen (DUAL_EC_DRBG) is niet toegestaan</p><ul><li>**CNG-** BCryptGenRandom (gebruik van de vlag BCRYPT_USE_SYSTEM_PREFERRED_RNG aanbevolen tenzij de aanroeper kan worden uitgevoerd op een IRQL groter dan 0 [dat wil zeggen, PASSIVE_LEVEL])</li><li>**Capi-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nieuwe implementaties moeten gebruikmaken van BCryptGenRandom of CryptGenRandom) * rand_s * SystemPrng (voor kernelmodus)</li><li>**. NET-** RNGCryptoServiceProvider of RNGCng</li><li>**Windows Store-apps-** Windows. Security. Cryptography. CryptographicBuffer. GenerateRandom of. GenerateRandomNumber</li><li>**Apple OS X (10.7 +)/IOS (2.0 +)-** int SecRandomCopyBytes (SecRandomRef wille keurig, size_t aantal, uint8_t \* bytes)</li><li>**Apple OS X (<10,7)-** /Dev/random gebruiken om wille keurige getallen op te halen</li><li>**Java (inclusief Google Android Java-code)-** de klasse java. Security. SecureRandom. Voor Android 4,3 (gelei bonen) moeten ontwikkel aars de aanbevolen tijdelijke oplossing voor Android volgen en hun toepassingen bijwerken om de PRNG expliciet te initialiseren met entropie van/dev/urandom of/dev/random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Geen symmetrische stroom versleuteling gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Symmetrische stroom versleuteling, zoals RC4, mag niet worden gebruikt. In plaats van symmetrische stroom versleuteling moeten producten gebruikmaken van een blok codering, met name AES met een sleutel lengte van ten minste 128 bits. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Goedgekeurde MAC/HMAC/versleutelings hash-algoritmen gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Producten moeten alleen goedgekeurde (MAC) of hash-algoritmen (Message Authentication Code) van het verificatie programma gebruiken.</p><p>Een MAC (Message Authentication Code) is een stukje informatie dat is gekoppeld aan een bericht waarmee de ontvanger zowel de authenticiteit van de afzender als de integriteit van het bericht met behulp van een geheime sleutel kan controleren. Het gebruik van een op hash gebaseerde MAC ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) of een [Mac op basis van blok versleuteling](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) is toegestaan zolang alle onderliggende hash-of symmetrische versleutelings algoritmen ook zijn goedgekeurd voor gebruik. Dit omvat momenteel de HMAC-SHA2 functions (HMAC-SHA256, HMAC-SHA384 en HMAC-SHA512 gebruikt) en de op CMAC/OMAC1 en OMAC2 gebaseerde MACs (deze zijn gebaseerd op AES).</p><p>Het gebruik van HMAC-SHA1 kan worden toegestaan voor platform compatibiliteit, maar u moet een uitzonde ring op deze procedure opslaan en de crypto controle van uw organisatie ondergaan. Afkap ping van HMAC-waarde op minder dan 128 bits is niet toegestaan. Het gebruik van klant methoden voor het hashen van een sleutel en gegevens is niet goedgekeurd en moet de controle van de crypto grafie van uw organisatie vóór gebruik ondergaan.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Alleen goedgekeurde cryptografische hash-functies gebruiken

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Webtoepassing | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Producten moeten de SHA-2-familie van hash-algoritmen gebruiken (SHA256, SHA384 en SHA512 gebruikt). Als er een korte hash nodig is, zoals een 128-bits uitvoer lengte zodat deze past bij een gegevens structuur die is ontworpen met de kortere MD5-hash, kunnen product teams een van de SHA2-hashes afkappen (meestal SHA256). Houd er rekening mee dat SHA384 een afgekapte versie van SHA512 gebruikt is. Afkap ping van cryptografische hashes voor beveiligings doeleinden tot minder dan 128 bits is niet toegestaan. Voor nieuwe code mogen geen MD2-, MD4-, MD5-, SHA-0-, SHA-1-of RIPEMD-hash-algoritmen worden gebruikt. Hash-conflicten kunnen worden verrekend voor deze algoritmen, waardoor ze effectief worden opgesplitst.</p><p>Toegestane .NET-hash-algoritmen voor de flexibiliteit van beheerde crypto grafie (in volg orde van voor keur):</p><ul><li>SHA512Cng (FIPS-compatibel)</li><li>SHA384Cng (FIPS-compatibel)</li><li>SHA256Cng (FIPS-compatibel)</li><li>SHA512Managed (niet-FIPS-compatibel) (gebruik SHA512 gebruikt als algoritme naam in aanroepen van HashAlgorithm. Create of CryptoConfig. CreateFromName)</li><li>SHA384Managed (niet-FIPS-compatibel) (gebruik SHA384 als algoritme naam in aanroepen van HashAlgorithm. Create of CryptoConfig. CreateFromName)</li><li>SHA256Managed (niet-FIPS-compatibel) (gebruik SHA256 als algoritme naam in aanroepen van HashAlgorithm. Create of CryptoConfig. CreateFromName)</li><li>SHA512CryptoServiceProvider (FIPS-compatibel)</li><li>SHA256CryptoServiceProvider (FIPS-compatibel)</li><li>SHA384CryptoServiceProvider (FIPS-compatibel)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Sterke versleutelings algoritmen gebruiken om gegevens in de data base te versleutelen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Een versleutelings algoritme kiezen](/sql/relational-databases/security/encryption/choose-an-encryption-algorithm) |
| **Stappen** | Versleutelings algoritmen definiëren gegevens transformaties die niet eenvoudig kunnen worden teruggedraaid door onbevoegde gebruikers. Met SQL Server kunnen beheerders en ontwikkel aars kiezen uit verschillende algoritmen, waaronder DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bits RC4, DESX, 128-bits AES, 192-bits AES en 256-bits AES |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>SSIS-pakketten moeten worden versleuteld en digitaal ondertekend

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [De bron van pakketten met digitale hand tekeningen](/sql/integration-services/security/identify-the-source-of-packages-with-digital-signatures), [bedreiging en beveiligings problemen (integratie Services)](/sql/integration-services/security/security-overview-integration-services) identificeren |
| **Stappen** | De bron van een pakket is de persoon of organisatie die het pakket heeft gemaakt. Het uitvoeren van een pakket van een onbekende of niet-vertrouwde bron kan riskant zijn. Digitale hand tekeningen moeten worden gebruikt om ongeoorloofde manipulatie van SSIS-pakketten te voor komen. Om ervoor te zorgen dat de pakketten tijdens Storage/Transit geheim blijven, moeten SSIS-pakketten ook worden versleuteld |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Digitale hand tekening toevoegen aan kritieke data base-Beveilig bare items

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [HAND tekening toevoegen (Transact-SQL)](/sql/t-sql/statements/add-signature-transact-sql) |
| **Stappen** | In gevallen waarin de integriteit van een kritieke data base moet worden geverifieerd, moeten digitale hand tekeningen worden gebruikt. Data base-Beveilig bare items zoals een opgeslagen procedure, functie, assembly of trigger kunnen digitaal worden ondertekend. Hieronder ziet u een voor beeld van wanneer dit nuttig kan zijn: laten we zeggen dat een ISV (Independent Software Vendor) ondersteuning biedt voor een software die aan een van hun klanten wordt geleverd. Voordat de ondersteuning wordt geboden, wil de ISV er zeker van zijn dat een in de software beveiligte data base niet per ongeluk of met een schadelijke poging is geknoeid. Als de Beveilig bare digitaal is ondertekend, kan de ISV de digitale hand tekening controleren en de integriteit ervan valideren.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>SQL Server EKM gebruiken om versleutelings sleutels te beveiligen

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [SQL Server voor Extensible Key Management (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm), [uitbreidbaar sleutel beheer met behulp van Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server) |
| **Stappen** | SQL Server Extensible Key Management kunnen de versleutelings sleutels waarmee de database bestanden worden beveiligd, worden opgeslagen in een off-box apparaat, zoals een Smart Card, een USB-apparaat of een EKM/HSM-module. Hiermee wordt ook de gegevens bescherming van database beheerders (behalve leden van de groep sysadmin) ingeschakeld. Gegevens kunnen worden versleuteld met behulp van versleutelings sleutels die alleen toegankelijk zijn voor de database gebruiker in de externe EKM/HSM-module. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>De functie AlwaysEncrypted gebruiken als de versleutelings sleutels niet moeten worden getoond in de data base-engine

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Database | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | SQL Azure, premises |
| **Kenmerken**              | SQL-versie-V12, MsSQL2016 |
| **Referenties**              | [Always Encrypted (data base-engine)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |
| **Stappen** | Always Encrypted is een functie die is ontworpen om gevoelige gegevens te beveiligen, zoals creditcard nummers of nationale identificatie nummers (bijvoorbeeld Amerikaanse sociale-beveiligings nummers), opgeslagen in Azure SQL Database of SQL Server data bases. Met Always Encrypted kunnen clients gevoelige gegevens binnen client toepassingen versleutelen en de versleutelings sleutels niet aan de data base-engine (SQL Database of SQL Server) tonen. Als gevolg hiervan biedt Always Encrypted een schei ding tussen degenen die eigenaar zijn van de gegevens (en deze kunnen bekijken) en degenen die de gegevens beheren (maar geen toegang mogen hebben) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Cryptografische sleutels veilig opslaan op IoT-apparaat

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-apparaat | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Device OS-Windows IoT core, connectiviteit van apparaten-Sdk's van Azure IoT-apparaat |
| **Referenties**              | [TPM op Windows IOT core](/windows/iot-core/secure-your-device/TPM), [TPM instellen op Windows IOT core](/windows/iot-core/secure-your-device/setuptpm), [Azure IOT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Stappen** | Symmetrische of persoonlijke sleutels van certificaten veilig in een hardwarematig beveiligde opslag, zoals TPM-of Smart Card-chips. Windows 10 IoT core ondersteunt de gebruiker van een TPM en er zijn verschillende compatibele Tpm's die kunnen worden gebruikt: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm . Het is raadzaam om een firmware of een aparte TPM te gebruiken. Een software-TPM mag alleen worden gebruikt voor ontwikkelings-en test doeleinden. Als er een TPM beschikbaar is en de sleutels hierin worden ingericht, moet de code die het token genereert, worden geschreven zonder dat hierin gevoelige gegevens worden gecodeerd. | 

### <a name="example"></a>Voorbeeld
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Zoals kan worden weer gegeven, is de primaire sleutel van het apparaat niet aanwezig in de code. In plaats daarvan wordt het opgeslagen in de TPM op sleuf 0. TPM-apparaat genereert een SAS-token met korte levens duur dat vervolgens wordt gebruikt om verbinding te maken met de IoT Hub. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Een wille keurige symmetrische sleutel met voldoende lengte voor authenticatie voor de IoT Hub genereren

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | IoT-Cloud gateway | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | Gateway keuze-Azure IoT Hub |
| **Referenties**              | N.v.t.  |
| **Stappen** | IoT Hub bevat een apparaat-id-REGI ster en tijdens het inrichten van een apparaat, genereert automatisch een wille keurige symmetrische sleutel. Het is raadzaam om deze functie van het Azure IoT Hub-identiteits register te gebruiken om de sleutel te genereren die wordt gebruikt voor verificatie. IoT Hub kunt ook een sleutel opgeven tijdens het maken van het apparaat. Als een sleutel wordt gegenereerd buiten IoT Hub tijdens het inrichten van het apparaat, wordt aanbevolen een wille keurige symmetrische sleutel of ten minste 256 bits te maken. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Zorg ervoor dat er een beheer beleid voor apparaten is dat een pincode voor het gebruik van het apparaat vereist en Extern wissen toestaat

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Dynamics CRM Mobile-client | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat er een beheer beleid voor apparaten is dat een pincode voor het gebruik van het apparaat vereist en Extern wissen toestaat |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Zorg ervoor dat er een beheer beleid voor apparaten is dat een pincode of wacht woord/automatische vergren deling vereist en alle gegevens versleutelt (bijvoorbeeld BitLocker)

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Dynamics CRM Outlook-client | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | Zorg ervoor dat er een beheer beleid voor apparaten is dat een pincode of wacht woord/automatische vergren deling vereist en alle gegevens versleutelt (bijvoorbeeld BitLocker) |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Zorg ervoor dat de handtekening sleutels worden doorgevoerd wanneer identiteits server wordt gebruikt

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Identiteits server | 
| **SDL-fase**               | Implementatie |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | [Identiteits Server-sleutels, hand tekeningen en crypto grafie](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Stappen** | Zorg ervoor dat de handtekening sleutels worden doorgevoerd wanneer u identiteits server gebruikt. De koppeling in het gedeelte verwijzingen legt uit hoe dit moet worden gepland zonder dat er storingen worden veroorzaakt door toepassingen die afhankelijk zijn van de identiteits server. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Zorg ervoor dat de cryptografische sterke client-ID, het client geheim, wordt gebruikt in de identiteits server

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Onderdeel**               | Identiteits server | 
| **SDL-fase**               | Ontwikkelen |  
| **Toepasselijke technologieën** | Algemeen |
| **Kenmerken**              | N.v.t.  |
| **Referenties**              | N.v.t.  |
| **Stappen** | <p>Zorg ervoor dat de cryptografische sterke client-ID, het client geheim, wordt gebruikt in de identiteits server. De volgende richt lijnen moeten worden gebruikt bij het genereren van een client-ID en-geheim:</p><ul><li>Een wille keurige GUID genereren als de client-ID</li><li>Een cryptografische, wille keurige 256-bits sleutel genereren als geheim</li></ul>|