---
title: Uw domein koppelen aan uw Gedecentraliseerde id (DID) (preview) - Azure Active Directory verifieerbare referenties
description: Meer informatie over DNS-bindingen
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: ad5bb6e45479b4cccfa0b002427066439135e468
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588442"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Uw domein koppelen aan uw Gedecentraliseerde id (DID)

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel:
> [!div class="checklist"]
> * Waarom moeten we onze DID koppelen aan ons domein?
> * Hoe koppelen we dids en domeinen?
> * Hoe werkt het domeinkoppelingsproces?
> * Hoe werkt de domeinlogica verifiëren/niet-geverifieerd?

## <a name="prerequisites"></a>Vereisten

Als u uw DID wilt koppelen aan uw domein, moet u het volgende hebben voltooid.

- Voltooi de [Aan de slag](get-started-verifiable-credentials.md) en de volgende [zelfstudieset.](enable-your-tenant-verifiable-credentials.md)

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Waarom moeten we onze DID koppelen aan ons domein?

Een DID begint als een id die niet is verankerd in bestaande systemen. Een DID is handig omdat een gebruiker of organisatie er eigenaar van kan zijn en deze kan controleren. Als een entiteit die interactie heeft met de organisatie niet weet 'wie' de DID behoort, is de DID niet zo nuttig.

Als u een DID koppelt aan een domein, wordt het eerste vertrouwensprobleem opgelost door elke entiteit toe te staan de relatie tussen een DID en een domein cryptografisch te verifiëren.

## <a name="how-do-we-link-dids-and-domains"></a>Hoe koppelen we dids en domeinen?

We maken een koppeling tussen een domein en een DID door een open standaard te implementeren die is geschreven door de Decentralized Identity Foundation met de naam [Well-Known DID configuration](https://identity.foundation/.well-known/resources/did-configuration/). De verifieerbare referentieservice in Azure Active Directory (Azure AD) helpt uw organisatie bij het maken van de koppeling tussen de DID en het domein door de domeingegevens op te nemen die u hebt opgegeven in uw DID en het genereren van het bekende configuratiebestand:

1. Azure AD maakt gebruik van de domeingegevens die u tijdens de installatie van de organisatie op geeft om een service-eindpunt te schrijven in het document DID. Alle partijen die met uw DID communiceren, kunnen het domein zien waar uw DID aan moet worden gekoppeld.  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. De verifieerbare referentieservice in Azure AD genereert een compatibele bekende configuratieresource die u in uw domein kunt hosten. Het configuratiebestand bevat een zelf uitgegeven verifieerbare referentie van credentialType 'DomainLinkageCredential' die is ondertekend met uw DID die een oorsprong van uw domein heeft. Hier is een voorbeeld van het configuratie-document dat is opgeslagen op de URL van het hoofddomein.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Nadat u het bekende configuratiebestand hebt, moet u het bestand beschikbaar maken met behulp van de domeinnaam die u hebt opgegeven bij het inschakelen van uw AAD voor verifieerbare referenties.

* Host het bekende DID-configuratiebestand in de hoofdmap van het domein.
* Gebruik geen omleidingen.
* Gebruik https om het configuratiebestand te distribueren.

>[!IMPORTANT]
>Microsoft Authenticator geen omleidingen respecteert, moet de opgegeven URL de uiteindelijke doel-URL zijn.

## <a name="user-experience"></a>Gebruikerservaring 

Wanneer een gebruiker een uitgiftestroom doormaakt of een verifieerbare referentie presenteert, moet deze iets weten over de organisatie en de did. Als het domein onze verifieerbare referentie wallet, Microsoft Authenticator, valideert de relatie van een DID met het domein in het document DID en geeft gebruikers twee verschillende ervaringen, afhankelijk van het resultaat.

## <a name="verified-domain"></a>Geverifieerd domein

Voordat Microsoft Authenticator een geverifieerd **pictogram we weergeven,** moet u een aantal dingen waar zijn:

* De DID-ondertekening van de zelf-uitgegeven open ID (SIOP)-aanvraag moet een service-eindpunt voor gekoppeld domein hebben.
* Het hoofddomein gebruikt geen omleiding en maakt gebruik van https.
* Het domein dat wordt vermeld in het DID-document heeft een op te lossen bekende resource.
* De verifieerbare referentie van de bekende resource is ondertekend met dezelfde DID die is gebruikt voor het ondertekenen van de SIOP die Microsoft Authenticator gebruikt om de stroom te starten.

Als alle eerder genoemde waar zijn, geeft Microsoft Authenticator een geverifieerde pagina weer en bevat het domein dat is gevalideerd.

![nieuwe machtigingsaanvraag](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Niet-geverifieerd domein

Als een van de bovenstaande niet waar is, geeft de Microsoft Authenticator een volledige paginawaarschuwing weer voor de gebruiker dat het domein niet geverifieerd is, de gebruiker bezig is met een riskante transactie en voorzichtig moet zijn. We hebben ervoor gekozen om deze route te nemen, omdat:

* De DID is ofwel niet ankerd in een domein.
* De configuratie is niet juist ingesteld.
* De HEEFT de gebruiker interactie met is schadelijk en kan niet bewijzen dat ze eigenaar zijn van een domein (omdat ze daadwerkelijk niet). Vanwege dit laatste punt is het belangrijk dat u uw DID koppelt aan het domein dat de gebruiker kent, om te voorkomen dat het waarschuwingsbericht wordt doorgegeven.

![waarschuwing voor niet-geverifieerd domein in het scherm Referenties toevoegen](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Bekende configuratie distribueren

1. Navigeer naar de pagina Instellingen in Verifieerbare referenties en kies **Dit domein verifiëren**

   ![Controleer dit domein in instellingen](media/how-to-dnsbind/settings-verify.png) 

2. Download de did-configuration.jsin het bestand dat wordt weergegeven in de onderstaande afbeelding.

   ![Bekende configuratie downloaden](media/how-to-dnsbind/verify-download.png) 

3. Kopieer de JWT, open [jwt.ms](https://www.jwt.ms) controleer of het domein juist is.

4. Kopieer uw DID en open de [ION-Netwerkverkenner](https://identity.foundation/ion/explorer) om te controleren of hetzelfde domein is opgenomen in het DID-document. 

5. Host de bekende configuratieresource op de opgegeven locatie. Voorbeeld: `https://www.example.com/.well-known/did-configuration.json`

6. Test het uitgeven of presenteren van een Microsoft Authenticator te valideren. Zorg ervoor dat de instelling in Authenticator Waarschuwen over onveilige apps is in-/uitschakelen.

>[!NOTE]
>'Waarschuwen over onveilige apps' is standaard ingeschakeld.

Gefeliciteerd, u hebt nu het vertrouwensweb met uw did!

## <a name="next-steps"></a>Volgende stappen

Als u tijdens het onboarden de verkeerde domeingegevens van u in de onboarding op geeft en u besluit deze te wijzigen, moet u [zich uit- en uit- .](how-to-opt-out.md) Op dit moment bieden we geen ondersteuning voor het bijwerken van uw DID-document. Als u zich uit- en weer in kiest, wordt er een geheel nieuwe DID-versie aan de naam gedaan.
