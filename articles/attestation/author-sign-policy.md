---
title: Azure Attestation-beleid ontwerpen
description: Uitleg van het ontwerpen van Attestation-beleid.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: aaf05b293fa5a13c5fe6e341f7de987c6002a5cf
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607963"
---
# <a name="how-to-author-an-attestation-policy"></a>Attestation-beleid ontwerpen

Een Attestation-beleid is een naar Microsoft Azure Attestation geüpload bestand. Azure Attestation biedt de flexibiliteit voor het uploaden van een beleid in een beleidsindeling die specifiek is voor Attestation. U kunt ook een gecodeerde versie van het beleid in JSON Web Signature uploaden. De beleidsbeheerder is verantwoordelijk voor het schrijven van het Attestation-beleid. In de meeste scenario's fungeert de Relying Party als beleidsbeheerder. De client die de Attestation-aanroep doet, verzendt Attestation-bewijzen, die door de service worden geparseerd en omgezet in binnenkomende claims (sets van eigenschappen, waarde). De service verwerkt vervolgens de claims op basis van het gedefinieerde beleid en retourneert het berekende resultaat.

Het beleid bevat regels die de autorisatiecriteria, eigenschappen en de inhoud van de Attestation-token bepalen. Een voorbeeld van een beleidsbestand ziet er als volgt uit:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnabled", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnabled", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Een beleidsbestand heeft drie segmenten, zoals hierboven te zien is:

- **version** (versie):  De versie is het versienummer van de grammatica die wordt gevolgd. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Momenteel wordt alleen versie 1.0 ondersteund.

- **authorizationrules** (autorisatieregels): Een verzameling claimregels die eerst worden gecontroleerd, om te bepalen of Azure Attestation door moet gaan naar **issuancerules**. De claimregels worden toegepast in de volgorde waarin ze zijn gedefinieerd.

- **issuancerules** (uitgifteregels): Een verzameling claimregels die wordt geëvalueerd om aanvullende informatie toe te voegen aan het Attestation-resultaat zoals gedefinieerd in het beleid. De claimregels worden toegepast in de volgorde waarin ze zijn gedefinieerd en zijn ook optioneel.

Zie [claim en claimregels](claim-rule-grammar.md) voor meer informatie.
   
## <a name="drafting-the-policy-file"></a>Het beleidsbestand opstellen

1. Maak een nieuw bestand.
1. Voeg de versie toe aan het bestand.
1. Voeg secties toe voor **authorizationrules** en **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  De autorisatieregels bevatten de actie deny() (afwijzen) zonder voorwaarde, om ervoor te zorgen dat er geen uitgifteregels worden verwerkt. De autorisatieregel kan ook de actie permit() (toestaan) bevatten, om de verwerking van uitgifteregels toe te staan.
  
4. Claimregels toevoegen aan de autorisatieregels

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Als de binnenkomende claimset een claim bevat die overeenkomt met het type, de waarde en de verlener, geeft de actie permit() aan dat de beleidsengine de **issuancerules** moet verwerken.
  
5. Voeg claimregels toe aan **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  De set uitgaande claims bevat een claim met:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Complexe beleidsregels kunnen op een vergelijkbare manier worden ontworpen. Zie [Attestation-beleidsvoorbeelden](policy-examples.md)voor meer informatie.
  
6. Sla het bestand op.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Het beleidsbestand maken in de indeling JSON Web Signature

Nadat u een beleidsbestand hebt gemaakt, volgt u de onderstaande stappen om een beleid te uploaden in de JWS-indeling.

1. Genereer de JWS, RFC 7515 met beleid (UTF-8 gecodeerd) als de payload
     - De payload-id voor het met Base64Url gecodeerde beleid moet "AttestationPolicy" zijn.
     
     Voorbeeld-JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (Optioneel) Onderteken het beleid. Azure Attestation ondersteunt de volgende algoritmen:
     - **Geen**: De payload van het beleid niet ondertekenen.
     - **RS256**: Ondersteund algoritme voor het ondertekenen van de payload van het beleid

3. Upload de JWS en valideer het beleid.
     - Als het beleidsbestand geen syntaxisfouten bevat, wordt het geaccepteerd door de service.
     - Als het beleidsbestand wel syntaxisfouten bevat, wordt het afgewezen door de service.

## <a name="next-steps"></a>Volgende stappen
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)
- [Een SGX-enclave-attest verklaren met codevoorbeelden](/samples/browse/?expanded=azure&terms=attestation)
