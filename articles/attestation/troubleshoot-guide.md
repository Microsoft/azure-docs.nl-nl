---
title: Handleiding voor het oplossen van problemen met Azure Attestation
description: Handleiding voor het oplossen van problemen met veel waargenomen problemen
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 5eefcb55bb5447d557f097af872847576aa86eed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519303"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure attestation-gids voor probleemoplossing

Foutafhandeling in Azure Attestation is geïmplementeerd volgens de [microsoft REST API richtlijnen.](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses) Het foutbericht dat wordt geretourneerd Azure Attestation API's bevat HTTP-statuscode en naam/waarde-paren met de namen 'code' en 'bericht'. De waarde van 'code' is leesbaar voor mensen en is een indicator van het type fout. De waarde van 'bericht' is van plan de gebruiker te helpen en geeft foutdetails.

Als uw probleem niet wordt opgelost in dit artikel, kunt u ook een aanvraag ondersteuning voor Azure indienen op de [ondersteuning voor Azure pagina](https://azure.microsoft.com/support/options/).

Hieronder vindt u enkele voorbeelden van de fouten die worden geretourneerd door Azure Attestation:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP–401: Niet-geautoriseerde uitzondering

### <a name="http-status-code"></a>HTTP-statuscode
401

**Foutcode** Onbevoegde

**Scenariovoorbeelden**
  - Kan attestation-beleid niet beheren omdat de gebruiker niet is toegewezen met de juiste rollen
  - Kan geen attestation-beleidsdeeltekens beheren omdat de gebruiker niet is toegewezen met de juiste rollen

Gebruiker met de rol Lezer die probeert een Attestation-beleid te bewerken in PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Stappen voor probleemoplossing**

Voor het beheren van beleid, heeft een Azure AD-gebruiker de volgende machtigingen nodig voor 'Acties':
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Als u deze acties wilt uitvoeren, moet een Azure AD-gebruiker de rol 'Attestation-inzender' hebben voor de Attestation-provider. Deze machtigingen kunnen ook worden overgenomen door rollen zoals 'Eigenaar' (machtigingen voor jokertekens), 'Inzender' (machtigingen met jokertekens) voor het abonnement/de resourcegroep.  

Voor het lezen van beleid, heeft een Azure AD-gebruiker de volgende machtigingen nodig voor 'Acties':
- Microsoft.Attestation/attestationProviders/attestation/read

  Als u deze actie wilt uitvoeren, moet een Azure AD-gebruiker de rol 'Attestation-lezer' hebben voor de Attestation-provider. De leesmachtiging kan ook worden overgenomen door rollen zoals Lezer (machtigingen voor jokertekens) voor het abonnement of de resourcegroep.  

Voer de onderstaande stappen uit om de rollen in PowerShell te controleren:

a. Start PowerShell en meld u aan bij Azure via de cmdlet Connect-AzAccount

b. Raadpleeg de richtlijnen [hier om](../role-based-access-control/role-assignments-list-powershell.md) uw Azure-roltoewijzing bij de Attestation-provider te controleren

c. Als u geen geschikte roltoewijzing vindt, volgt u de [instructies](../role-based-access-control/role-assignments-powershell.md) hier

## <a name="2-http--400-errors"></a>2. HTTP : 400 fouten

### <a name="http-status-code"></a>HTTP-statuscode
400

Er zijn verschillende redenen waarom een aanvraag 400 kan retourneren. Hieronder vindt u enkele voorbeelden van fouten die worden geretourneerd door Azure Attestation API's:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Attestation-fout vanwege beleidsevaluatiefouten

Attestation-beleid bevat autorisatieregels en uitgifteregels. Enclave-bewijs wordt geëvalueerd op basis van de autorisatieregels. Uitgifteregels definiëren de claims die moeten worden opgenomen in het Attestation-token. Als claims in enclave-bewijs niet voldoen aan de autorisatieregels, retourneren attest-aanroepen een beleidsevaluatiefout. 

**Foutcode** PolicyEvaluationError

**Scenariovoorbeelden** Wanneer claims in de enclave-offerte niet overeenkomen met de autorisatieregels van het Attestation-beleid

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Stappen voor probleemoplossing** Gebruikers kunnen enclave-bewijs evalueren op een SGX Attestation-beleid voordat ze hetzelfde configureren.

Verzend een aanvraag om de API te bevestigen door beleidstekst op te geven in de parameter draftPolicyForAttestation. De AttestSgxEn enclavee-API gebruikt dit beleidsdocument tijdens de attest-aanroep en deze kan worden gebruikt om attestation-beleid te testen voordat ze worden gebruikt. Het attestation-token dat wordt gegenereerd wanneer dit veld aanwezig is, is onbeveiligd.

Zie [Voorbeelden van attestation-beleid](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Attestation-fout vanwege ongeldige invoer

**Foutcode** InvalidParameter

**Scenariovoorbeelden** SGX Attestation-fout vanwege ongeldige invoer. Hieronder vindt u enkele voorbeelden van foutberichten:
- De opgegeven prijsopgave is ongeldig vanwege een fout in de offerteverklaring 
- De opgegeven prijsopgave is ongeldig omdat het apparaat waarop de prijsopgave is gegenereerd, niet voldoet aan de vereisten voor de Azure-basislijn
- De opgegeven prijsopgave is ongeldig omdat de TCBInfo of QEID die is opgegeven door de PCK Cache Service ongeldig is

**Stappen voor probleemoplossing**

Microsoft Azure Attestation ondersteunt attestation van SGX-aanhalingstekens die zijn gegenereerd door Intel SDK en Open Enclave SDK.

Raadpleeg [codevoorbeelden voor](/samples/browse/?expanded=azure&terms=attestation) het uitvoeren van attestation met behulp van Open Enclave SDK/ Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Ongeldige certificaatketenfout tijdens het uploaden van beleid/beleidsvermelding

**Foutcode** InvalidParameter

**Scenariovoorbeelden** Ondertekend beleid configureren of een beleidsaanmelding voor toevoegen/verwijderen configureren, die is ondertekend met een ongeldige certificaatketen (bijvoorbeeld wanneer de uitbreiding basisbeperkingen van het basiscertificaat niet is ingesteld op Onderwerptype = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Stappen voor probleemoplossing** Het basiscertificaat moet worden gemarkeerd als uitgegeven door een CA (de X.509-basisbeperkingen), anders wordt het niet beschouwd als een geldig certificaat. 

Zorg ervoor dat de uitbreiding basisbeperkingen van het basiscertificaat is ingesteld om aan te geven dat het onderwerptype = CA

Anders wordt de certificaatketen als ongeldig beschouwd.

Zie [voorbeelden van beleidsvertekenaar](./policy-signer-examples.md) [en beleid](./policy-examples.md) 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Fout bij toevoegen/verwijderen van beleidsverlener

**Foutcode** InvalidOperation

**Scenariovoorbeelden**

Wanneer de gebruiker JWS uploadt zonder de claim 'ific-policyCertificate'

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

Wanneer de gebruiker geen certificaat in JWS-indeling uploadt

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

**Stappen voor probleemoplossing** Gebruik RFC7519 JSON Web Token (JWT) met een claim met de naam 'x-ms-policyCertificate' om een nieuw certificaat voor beleidsverdeler toe te voegen of te verwijderen. De waarde van de claim is een RFC7517-JSON Web Key, die het certificaat bevat dat moet worden toegevoegd. JWT moet zijn ondertekend met een persoonlijke sleutel van een van de geldige certificaten voor beleidsafwegingen die zijn gekoppeld aan de provider. Zie [voorbeelden van beleids ondertekent](./policy-signer-examples.md).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Fout bij de configuratie van attestation-beleid

**Foutcode** PolicyParsingError

**Scenariovoorbeelden** Beleid met onjuiste syntaxis (bijvoorbeeld ontbrekende puntkomma)/geldig JWT-beleid)

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

**Foutcode** InvalidOperation

**Scenariovoorbeelden** Ongeldige inhoud opgegeven (bijvoorbeeld beleid uploaden/niet-ondertekend beleid wanneer beleidsondertekening is vereist)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Stappen voor probleemoplossing** Zorg ervoor dat het beleid in tekstindeling UTF-8 is gecodeerd.

Als ondertekening van beleid is vereist, moet attestation-beleid alleen worden geconfigureerd in de indeling RFC7519 JSON Web Token (JWT). Als ondertekening van beleid niet is vereist, kan het beleid worden geconfigureerd in tekst- of JWT-indeling.

Als u een beleid in JWT-indeling wilt configureren, gebruikt u JWT met een claim met de naam AttestationPolicy. De waarde van de claim is base64URL gecodeerde versie van de beleidstekst. Als de Attestation-provider is geconfigureerd met certificaten voor beleidsafwegingen, moet de JWT worden ondertekend met een persoonlijke sleutel van een van de geldige certificaten van de beleidsverlener die aan de provider zijn gekoppeld. 

Als u een beleid in tekstindeling wilt configureren, geeft u rechtstreeks beleidstekst op.

Geef in PowerShell PolicyFormat op als JWT om beleid in JWT-indeling te configureren. De standaard beleidsindeling is Tekst.

Zie voorbeelden van [attestation-beleid](./policy-examples.md) en [hoe u een Attestation-beleid kunt maken](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Installatieproblemen met Az.Attestation in PowerShell

Kan Az- of Az.Attestation-modules niet installeren in PowerShell

### <a name="error"></a>Fout

WAARSCHUWING: Kan pakketbron 'PackageManagement\Install-Package: er is geen overeenkomst gevonden voor de opgegeven zoekcriteria en https://www.powershellgallery.com/api/v2 modulenaam

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

PowerShell Gallery versies 1.0 en 1.1 zijn Transport Layer Security (TLS) afgeschaft. 

TLS 1,2 of een latere versie wordt aanbevolen. 

Als u wilt blijven werken met de PowerShell Gallery, voert u de volgende opdracht uit vóór de installatie-module-opdrachten

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. Problemen met toegang tot beleid/configuratie in PowerShell

Gebruiker toegewezen met de juiste rollen. Maar met autorisatieproblemen tijdens het beheren van attestation-beleid via PowerShell.

### <a name="error"></a>Fout
De client met object-id object-id is niet autorisatie voor het uitvoeren van de actie &lt; &gt;  Microsoft.Authorization/roleassignments/write over scope 'subcriptions/ &lt; subscriptionId &gt; resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/role &lt; assignmentId' of het &gt; bereik is ongeldig. Als er onlangs toegang is verleend, vernieuwt u uw referenties

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Hieronder vindt u de minimale versie van Az-modules die nodig zijn om attestation-bewerkingen te ondersteunen: 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Voer de onderstaande opdracht uit om de geïnstalleerde versie van alle AZ-modules te controleren 

```powershell
Get-InstalledModule 
```

Als de versies niet overeenkomen met de minimumvereiste, voert u de Update-Module uit

bijvoorbeeld - Update-Module -Name Az.Attestation
