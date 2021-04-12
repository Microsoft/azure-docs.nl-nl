---
title: Handleiding voor het oplossen van problemen met Azure Attestation
description: Probleem oplossing voor veelvoorkomende problemen
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ae3e12c11f194b3efcc149382dc952bd74d38b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704313"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Gids voor het oplossen van problemen met Microsoft Azure

Fout afhandeling in azure Attestation wordt geïmplementeerd volgens de [richt lijnen voor micro soft rest API](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses). Het fout bericht dat wordt geretourneerd door Azure Attestation-Api's bevat HTTP-status code en naam/waarde-paren met de namen ' code ' en ' bericht '. De waarde van ' code ' is menselijk leesbaar en is een indicatie van het type fout. De waarde van ' bericht ' is om de gebruiker te helpen en geeft fout Details.

Als uw probleem niet in dit artikel wordt behandeld, kunt u ook een Azure-ondersteunings aanvraag indienen op de [ondersteunings pagina van Azure](https://azure.microsoft.com/support/options/).

Hieronder ziet u enkele voor beelden van fouten die zijn geretourneerd door Azure Attestation:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: niet-geautoriseerde uitzonde ring

### <a name="http-status-code"></a>HTTP-statuscode
401

**Fout code** Gasten

**Scenario voorbeelden**
  - Attestation-fout als de gebruiker niet is toegewezen met de rol van de Attestation-lezer
  - Kan geen Attestation-beleid beheren omdat de gebruiker niet is toegewezen aan de juiste rollen
  - Kan de Attestation-beleids ondertekeners niet beheren omdat de gebruiker niet is toegewezen aan de juiste rollen

Gebruiker met rol van lezer probeert een Attestation-beleid te bewerken in Power shell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Probleemoplossings stappen**

Voor het weer geven van Attestation-beleid/beleids ondertekeners is een Azure AD-gebruiker gemachtigd voor ' acties ':
- Microsoft.Attestation/attestationProviders/attestation/read

  Deze machtiging kan worden toegewezen aan een AD-gebruiker via een rol zoals ' eigenaar ' (machtigingen voor joker tekens) of ' lezer ' (machtigingen voor joker tekens) of ' attestation-lezer ' (specifieke machtigingen voor alleen Azure Attestation).

Als u beleids ondertekeners wilt toevoegen/verwijderen of beleids regels wilt configureren, vereist een Azure AD-gebruiker de volgende machtigingen voor ' acties ':
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Deze machtigingen kunnen worden toegewezen aan een AD-gebruiker via een rol zoals ' eigenaar ' (machtigingen voor joker tekens), ' Inzender ' (machtigingen voor joker tekens) of ' attestation-Inzender ' (alleen specifieke machtigingen voor Azure Attestation).

Klanten kunnen ervoor kiezen om de standaard provider voor attestation te gebruiken of hun eigen providers met aangepast beleid te maken. Om Attestation-aanvragen naar aangepaste Attestation-providers te verzenden, is de rol Owner (joker tekens) of ' lezer ' (joker tekens) of ' attestation-lezer ' vereist voor de gebruiker. De standaard providers zijn toegankelijk voor elke Azure AD-gebruiker.

Voer onderstaande stappen uit om de rollen in Power shell te controleren:

a. Start Power shell en meld u aan bij Azure via de cmdlet Connect-AzAccount

b. De toewijzings instellingen voor Azure-rollen controleren


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  De uitvoer moet er ongeveer als volgt uitzien:

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. Als u geen geschikte roltoewijzing in de lijst vindt, volgt u de instructies [hier](../role-based-access-control/role-assignments-powershell.md)

## <a name="2-http--400-errors"></a>2. HTTP: 400 fouten

### <a name="http-status-code"></a>HTTP-statuscode
400

Er zijn verschillende redenen waarom een aanvraag 400 kan retour neren. Hieronder vindt u enkele voor beelden van fouten die zijn geretourneerd door Azure Attestation-Api's:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Attestation-fout vanwege beleids evaluatie fouten

Attestation-beleid bevat autorisatie regels en uitgifte regels. Enclave-bewijzen worden geëvalueerd op basis van de autorisatie regels. Met uitgifte regels worden de claims gedefinieerd die moeten worden opgenomen in het Attestation-token. Als claims in enclave-bewijzen niet voldoen aan de autorisatie regels, wordt door Attestation-aanroepen een beleids evaluatie fout geretourneerd. 

**Fout code** PolicyEvaluationError

**Scenario voorbeelden** Wanneer claims in de enclave-offerte niet overeenkomen met de autorisatie regels van het attest beleid

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Probleemoplossings stappen** Gebruikers kunnen enclave-bewijzen evalueren op basis van een SGX-attestatie beleid voordat ze hetzelfde configureren.

Een aanvraag verzenden naar een Attestation-API door beleids tekst op te geven in de para meter "draftPolicyForAttestation". De AttestSgxEnclave-API gebruikt dit beleids document tijdens de attest aanroep en dit kan worden gebruikt om Attestation-beleid te testen voordat ze worden verbruikt. Het Attestation-token dat wordt gegenereerd wanneer dit veld aanwezig is, wordt niet beveiligd.

Zie [voor beelden van Attestation-beleid](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Attestation-fout vanwege ongeldige invoer

**Fout code** InvalidParameter

**Scenario voorbeelden** SGX-Attestation-fout vanwege ongeldige invoer. Hieronder ziet u enkele voor beelden van fout berichten:
- De opgegeven offerte is ongeldig vanwege een fout in het prijsopgave onderpand 
- De opgegeven offerte is ongeldig omdat het apparaat waarop de offerte werd gegenereerd niet voldoet aan de vereisten voor de Azure-basis lijn
- De opgegeven offerte is ongeldig omdat de TCBInfo of QEID van de PCK Cache Service ongeldig is.

**Probleemoplossings stappen**

Microsoft Azure Attestation ondersteunt Attestation van SGX-offertes die zijn gegenereerd door de Intel SDK en open enclave SDK.

Raadpleeg [code voorbeelden](/samples/browse/?expanded=azure&terms=attestation) voor het uitvoeren van Attestation met behulp van de open enclave SDK/Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Ongeldige fout in certificaat keten tijdens uploaden van beleids-of beleids handtekening

**Fout code** InvalidParameter

**Scenario voorbeelden** Ondertekend beleid configureren of beleids handtekening toevoegen/verwijderen, die is ondertekend met een ongeldige certificaat keten (bijvoorbeeld wanneer de uitbrei ding voor basis beperkingen van het basis certificaat niet is ingesteld op subject type = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Probleemoplossings stappen** Het basis certificaat moet worden gemarkeerd als wordt uitgegeven door een certificerings instantie (X. 509 Basic-beperkingen). anders wordt het niet beschouwd als een geldig certificaat. 

Zorg ervoor dat de uitbrei ding basis beperkingen van het basis certificaat is ingesteld om aan te geven dat subject type = CA

Anders wordt de certificaat keten als ongeldig beschouwd.

Zie [beleids handtekening](./policy-signer-examples.md) en [beleids](./policy-examples.md) voorbeelden 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Fout met beleids handtekening toevoegen/verwijderen

**Fout code** InvalidOperation

**Scenario voorbeelden**

Wanneer de gebruiker JWS uploadt zonder de claim ' Maa-policyCertificate '

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Wanneer de gebruiker geen certificaat uploadt in de JWS-indeling

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Probleemoplossings stappen** Als u een nieuw certificaat voor een beleids ondertekenaar wilt toevoegen/verwijderen, gebruikt u RFC7519 JSON Web Token (JWT) met een claim met de naam "x-MS-policyCertificate". De waarde van de claim is een RFC7517 JSON-websleutel, die het certificaat bevat dat moet worden toegevoegd. JWT moet zijn ondertekend met een persoonlijke sleutel van een van de geldige beleids handtekening certificaten die zijn gekoppeld aan de provider. Zie [voor beelden van beleids regels voor aanmelden](./policy-signer-examples.md).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Configuratie fout Attestation-beleid

**Fout code** PolicyParsingError

**Scenario voorbeelden** Er is een onjuiste syntaxis in het beleid opgenomen (bijvoorbeeld: ontbrekende punt komma)/valid JWT-beleid)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Fout code** InvalidOperation

**Scenario voorbeelden** Ongeldige inhoud opgegeven (bijvoorbeeld beleid voor uploaden/niet-ondertekend beleid als beleids ondertekening is vereist)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Probleemoplossings stappen** Zorg ervoor dat het beleid in de tekst indeling UTF-8 is gecodeerd.

Als beleids ondertekening is vereist, moet het Attestation-beleid alleen worden geconfigureerd in de RFC7519 JSON Web Token (JWT)-indeling. Als beleids ondertekening niet is vereist, kan beleid worden geconfigureerd in tekst-of JWT-indeling.

Als u een beleid in de JWT-indeling wilt configureren, gebruikt u JWT met een claim met de naam ' AttestationPolicy '. De waarde van de claim is Base64URL gecodeerde versie van de beleids tekst. Als de Attestation-provider is geconfigureerd met beleids handtekening certificaten, moet de JWT zijn ondertekend met een persoonlijke sleutel van een van de geldige beleids handtekening certificaten die aan de provider zijn gekoppeld. 

Als u een beleid wilt configureren in tekst indeling, geeft u de beleids tekst rechtstreeks op.

Geef in Power shell PolicyFormat als JWT op om beleid te configureren in de JWT-indeling. De standaard indeling voor het beleid is tekst.

Zie [voor beelden](./policy-examples.md) van Attestation-beleid en [een Attestation-beleid ontwerpen](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. AZ. Attestation-installatie problemen in Power shell

Kan AZ of AZ. Attestation-modules niet installeren in Power shell

### <a name="error"></a>Fout

Waarschuwing: kan pakket bron ' https://www.powershellgallery.com/api/v2 ' PackageManagement\Install-package niet omzetten: er is geen overeenkomst gevonden voor de opgegeven zoek criteria en module naam

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

PowerShell Gallery heeft verouderde Transport Layer Security (TLS) versie 1,0 en 1,1. 

TLS 1,2 of een latere versie wordt aanbevolen. 

Als u wilt blijven werken met de PowerShell Gallery, voert u de volgende opdracht uit vóór de installatie-module-opdrachten

**[Net. ServicePointManager]:: exemplaar = [net. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. problemen met beleid voor toegang/configuratie in Power shell

De gebruiker is toegewezen met de juiste rollen. Maar gerichte autorisatie problemen tijdens het beheren van attest beleid via Power shell.

### <a name="error"></a>Fout
De client met object-id &lt; -object-id heeft &gt;  geen toestemming om de actie micro soft. Authorization/roleassignments/write over het bereik ' subcriptions/ &lt; subscriptionId &gt; ResourceGroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/ &lt; Role assignmentId ' uit te voeren &gt; of het bereik is ongeldig. Als u onlangs toegang hebt verleend, kunt u uw referenties vernieuwen

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

De minimale versie van AZ-modules die vereist zijn om Attestation-bewerkingen te ondersteunen zijn: 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Voer de onderstaande opdracht uit om de geïnstalleerde versie van alle AZ-modules te controleren 

```powershell
Get-InstalledModule 
```

Als de versies niet overeenkomen met de minimum vereiste, voert u Update-Module opdrachten uit

bijvoorbeeld-Update-Module-naam AZ. Attestation
