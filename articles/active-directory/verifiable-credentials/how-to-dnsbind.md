---
title: Uw domein koppelen aan uw gedecentraliseerde id (voor beeld) (preview)
description: Meer informatie over DNS-binding?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: be7db16a8e3a827d08c0db637961bf004af1d621
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170027"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Uw domein koppelen aan uw gedecentraliseerde id (gelukt)

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel:
> [!div class="checklist"]
> * Waarom moet ik onze domein koppeling koppelen
> * Hoe kunnen we DIDs en domeinen koppelen?
> * Hoe werkt het domein koppelings proces?
> * Hoe werkt de logica voor verifiëren/niet-geverifieerde domeinen?

## <a name="prerequisites"></a>Vereisten

Als u het aan uw domein wilt koppelen, moet u het volgende hebben voltooid.

- Voltooi de set [aan](get-started-verifiable-credentials.md) de slag en volgende [zelf studie](enable-your-tenant-verifiable-credentials.md).

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Waarom moet ik onze domein koppeling koppelen

A werd gestart als een id die niet is verankerd met bestaande systemen. Een was handig omdat een gebruiker of organisatie de eigenaar kan worden en deze kan beheren. Als een entiteit die met de organisatie communiceert niet weet ' wie ' hoort, is de niet zo nuttig.

Als u een hebt gekoppeld aan een domein, wordt het eerste vertrouwens probleem opgelost door een wille keurige entiteit te toestaan de relatie te controleren tussen een en een domein.

## <a name="how-do-we-link-dids-and-domains"></a>Hoe kunnen we DIDs en domeinen koppelen?

We maken een koppeling tussen een domein en een geslaagd door een open standaard te implementeren die is geschreven door de gedecentraliseerde identiteits basis genaamd [goed bekende geslaagde configuratie](https://identity.foundation/.well-known/resources/did-configuration/). De verifieer bare referentie service in Azure Active Directory (Azure AD) helpt uw organisatie bij het maken van de koppeling tussen de gewiste en het domein door de domein gegevens op te nemen die u in uw hebt opgegeven en het genereren van het bekende configuratie bestand:

1. Azure AD gebruikt de domein gegevens die u tijdens het instellen van de organisatie hebt opgegeven voor het schrijven van een service-eind punt binnen het DID-document. Alle partijen die met uw communiceren, kunnen het domein zien waaraan uw proclaims zijn gekoppeld.  

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

2. De verifieer bare referentie service in azure AD genereert een compatibele bekende configuratie bron die u in uw domein kunt hosten. Het configuratie bestand bevat een zelf-verleende, geverifieerde referentie van de credentialType ' DomainLinkageCredential ' die is ondertekend met de oorsprong van uw domein. Hier volgt een voor beeld van het configuratie-doc dat is opgeslagen op de URL van het hoofd domein.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Nadat u het bekende configuratie bestand hebt, moet u het bestand beschikbaar maken met de domein naam die u hebt opgegeven bij het inschakelen van uw AAD voor Controleer bare referenties.

* Host het bekende configuratie bestand in de hoofdmap van het domein.
* Gebruik geen omleidingen.
* Gebruik HTTPS om het configuratie bestand te distribueren.

>[!IMPORTANT]
>Microsoft Authenticator niet voldoet aan omleidingen, moet de opgegeven URL de uiteindelijke doel-URL zijn.

## <a name="user-experience"></a>Gebruikerservaring 

Wanneer een gebruiker een uitgifte stroom doorloopt of een verifieer bare referentie presenteert, moeten ze iets weten over de organisatie en zijn/haar hebben. Als het domein onze verifieer bare referentie Wallet Microsoft Authenticator, wordt er een relatie met het domein in het DID-bestand gevalideerd en worden gebruikers twee verschillende ervaringen gepresenteerd, afhankelijk van het resultaat.

## <a name="verified-domain"></a>Geverifieerd domein

Voordat Microsoft Authenticator een **gecontroleerd** pictogram weergeeft, moeten enkele dingen waar zijn:

* Het ondertekenen van de zelf-verleende Open-ID-aanvraag (SIOP) moet een service-eind punt hebben voor het gekoppelde domein.
* Het hoofd domein gebruikt geen omleiding en maakt gebruik van HTTPS.
* Het domein dat in het DID-document wordt vermeld, heeft een omzet bare, bekende resource.
* De verifieer bare referentie van de bekende bron is ondertekend met dezelfde, waarmee de SIOP werd ondertekend die Microsoft Authenticator gebruikt om de stroom te starten.

Als alle eerder genoemde voor waarden waar zijn, wordt in Microsoft Authenticator een gecontroleerde pagina weer gegeven en wordt het domein dat is gevalideerd, opgenomen.

![nieuwe machtigings aanvraag](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Niet-geverifieerd domein

Als een van de bovenstaande waarden niet waar is, wordt in de Microsoft Authenticator een volledige pagina waarschuwing weer gegeven aan de gebruiker dat het domein niet is geverifieerd. de gebruiker bevindt zich in het midden van een Risk ante trans actie en moet voorzichtig door gaan. We hebben ervoor gekozen om deze route te volgen omdat:

* De is niet verankerd met een domein.
* De configuratie is niet juist ingesteld.
* De gebruiker is in staat om kwaad aardig te zijn en kan niet bewijzen dat ze eigenaar zijn van een domein (omdat ze er eigenlijk niet zijn). Als gevolg van dit laatste punt is het van cruciaal belang dat u uw hebt gekoppeld aan het domein waarmee de gebruiker bekend is, om te voor komen dat het waarschuwings bericht wordt door gegeven.

![niet-geverifieerde domein waarschuwing in het scherm referentie toevoegen](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Bekende configuratie distribueren

1. Navigeer naar de pagina instellingen in Controleer bare referenties en kies **dit domein verifiëren**

   ![Dit domein controleren in instellingen](media/how-to-dnsbind/settings-verify.png) 

2. Down load de did-configuration.jsin het bestand dat wordt weer gegeven in de onderstaande afbeelding.

   ![Bekende configuratie downloaden](media/how-to-dnsbind/verify-download.png) 

3. Kopieer de JWT, open [JWT.MS](https://www.jwt.ms) en controleer of het domein juist is.

4. Kopieer uw was en open de [Ion-netwerk Verkenner](https://identity.foundation/ion/explorer) om te controleren of hetzelfde domein is opgenomen in het document. 

5. Host de bekende configuratie bron op de opgegeven locatie. Voorbeeld: https://www.example.com/.well-known/did-configuration.json

6. Test het uitgeven of presen teren met Microsoft Authenticator om te valideren. Zorg ervoor dat de instelling waarschuwing over onveilige apps wordt in-of uitgeschakeld.

>[!NOTE]
>Standaard is ' waarschuwen over onveilige apps ' ingeschakeld.

Gefeliciteerd, u hebt nu het Internet van de vertrouwens relatie met uw geslaagd.

## <a name="next-steps"></a>Volgende stappen

Als u tijdens het voorbereiden de verkeerde domein gegevens invoert, moet u [ervoor](how-to-opt-out.md)kiezen om deze te wijzigen. Op dit moment bieden we geen ondersteuning voor het bijwerken van het opgestelde document. Als u inactief maakt en inschakelt, wordt er een gloed nieuwe gemaakt.