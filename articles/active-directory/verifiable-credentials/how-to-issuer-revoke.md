---
title: Een verifieer bare referentie intrekken als een verlener-Azure Active Directory Controleerable referenties
description: Meer informatie over het intrekken van een geverifieerde referentie die u hebt gegeven
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222840"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Een eerder uitgegeven verifieer bare referentie intrekken (preview-versie)

Als onderdeel van het proces van het werken met Controleer bare referenties (VCs) hoeft u niet alleen referenties te verlenen, maar soms moet u ze ook intrekken. In dit artikel gaan we verder met de eigenschap **status** van de VC-specificatie. het is een goed idee om het intrekkings proces uit te voeren, waarom we referenties en enkele gevolgen voor gegevens en privacy kunnen intrekken.

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="status-property-in-verifiable-credentials-specification"></a>De eigenschap status in de specificatie verifieer bare referenties

Voordat we de implicaties van het intrekken van een verifieer bare referentie kunnen begrijpen, kan het helpen om te weten wat de **status controle** is en hoe deze vandaag werkt.

De [specificatie](https://www.w3.org/TR/vc-data-model/) van de W3C-controle bare referenties verwijst naar de eigenschap **status** in sectie [4,9:](https://www.w3.org/TR/vc-data-model/#status)

"Met deze specificatie definieert u de volgende **credentialStatus** -eigenschap voor de detectie van informatie over de huidige status van een verifieer bare referentie, zoals of deze is opgeschort of ingetrokken."

De W3C-specificatie definieert echter geen indeling voor de manier waarop de **status controle** moet worden geïmplementeerd.

Het definiëren van het gegevens model, de indelingen en de protocollen voor de status schema's valt buiten het bereik van deze specificatie. Er is een verifieerbaar REGI ster voor referentie-uitbrei ding [VC-EXTENSION-REGISTRY] aanwezig dat beschik bare status schema's bevat voor implementaties die de status controle van de geverifieerde referenties willen implementeren. "

>[!NOTE]
>Voor nu is de implementatie van de status controle van micro soft van toepassing, maar we werken actief samen met de gegroepeerde Community om op basis van een Standard uit te lijnen.

## <a name="how-does-the-status-property-work"></a>Hoe werkt de eigenschap **status** ?

In elke door micro soft uitgegeven geverifieerde referentie is er een kenmerk met de naam credentialStatus. Het wordt ingevuld met een status-API die door micro soft namens u wordt beheerd. Hier volgt een voor beeld van hoe het eruit ziet.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

De geverifieerde open source-referenties SDK verwerkt de status-API en levert de benodigde gegevens.

Zodra de API is aangeroepen en de juiste informatie heeft verstrekt, retourneert de API de waarde True of false. Waar is de verifieer bare referentie nog steeds actief met de verlener en ONWAAR, wat aangeeft dat de Controleer bare referentie actief is door de certificaat verlener.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Waarom zou u een VC wilt intrekken?

Elke klant heeft hun eigen unieke reden voor het intrekken van een verifieer bare referentie, maar hier volgen enkele van de algemene Thema's die we tot nu toe hebben gehoord. 

- Student-ID: de student is niet langer een actieve student bij de Universiteit.
- Werk nemer-ID: de werk nemer is niet langer een actieve werk nemer.
- Status Stuur Programma's licentie: het stuur programma is niet meer in die staat.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Een verifieer bare referentie instellen met de mogelijkheid om in te trekken

Alle verifieer bare referentie gegevens worden standaard niet met micro soft opgeslagen. Daarom hebben we geen gegevens waarnaar kan worden verwezen om een specifieke Controleer bare referentie-ID in te trekken. De uitgever moet een specifiek veld opgeven van het kenmerk verifieerbaar referentie punt voor micro soft om te indexeren en vervolgens zout en hash.

>[!NOTE]
>Hashing is een eenrichtings cryptografische bewerking die een invoer maakt, een ' een ' genoemd ```preimage``` , en een uitvoer produceert die een hash met een vaste lengte heeft. Het kan op dit moment niet worden verrekend om een hash-bewerking om te keren.

U kunt micro soft vertellen welk kenmerk van de verifieer bare referentie u wilt indexeren. De implicatie van indexeren is dat geïndexeerde waarden kunnen worden gebruikt om te zoeken naar uw verifieer bare referenties voor de VCs die u wilt intrekken.

**Voor beeld:** Alice is een werk nemer van Woodgrove. Anja links Woodgrove om te werken bij contoso. Jeroen, de IT-beheerder van Woodgrove, zoekt naar het e-mail bericht van Alice in de zoek query ingetrokken referenties intrekken. In dit voor beeld heeft Jeroen het veld e-mail van de geverifieerde werknemers referentie van Woodgrove geïndexeerd. 

Hieronder vindt u een voor beeld van hoe het regel bestand is gewijzigd om de index op te geven.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Er kan slechts één kenmerk worden geïndexeerd vanuit een regel bestand.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Een verifieer bare referentie Hoe kan ik intrekken

Zodra een index claim is ingesteld en er verifieer bare referenties aan uw gebruikers zijn uitgegeven, is het tijd om te zien hoe u een Controleer bare referentie kunt intrekken op de VC-Blade.

1. Navigeer naar de Blade verifieer bare referenties in Azure Active Directory.
1. Kies de verifieer bare referentie waarvoor u de index claim eerder hebt ingesteld en een verifieer bare referentie naar een gebruiker hebt uitgegeven. =
1. Kies in het menu links **een referentie intrekken** een 
    ![ referentie intrekken](media/how-to-issuer-revoke/settings-revoke.png) 
1. Zoek naar het index kenmerk van de gebruiker die u wilt intrekken. 

   ![De referentie zoeken die u wilt intrekken](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Omdat er alleen een hash van de geïndexeerde claim van de verifieer bare referentie wordt opgeslagen, worden met alleen een exacte overeenkomst de zoek resultaten gevuld. De invoer wordt door de IT-beheerder doorzocht en we gebruiken hetzelfde hash-algoritme om te zien of er een hash-overeenkomst in de data base is.
    
1. Zodra u een overeenkomst hebt gevonden, selecteert u de optie **intrekken** rechts van de referentie die u wilt intrekken.

   ![Een waarschuwing dat u weet dat na het intrekken van de gebruiker nog steeds de referentie](media/how-to-issuer-revoke/warning.png) 

1. Nadat het intrekken is voltooid, ziet u de status update en verschijnt een groene banner boven aan de pagina. 
   ![Dit domein controleren in instellingen](media/how-to-issuer-revoke/revoke-successful.png) 

Telkens wanneer een Relying Party aanroept om de status van deze specifieke verifieer bare referentie te controleren, retourneert de status-API van micro soft namens de Tenant een ' onwaar ' antwoord.

## <a name="next-steps"></a>Volgende stappen

Test de functionaliteit zelf met een test referentie om te gebruiken voor de stroom. Bekijk [onze zelf studies](get-started-verifiable-credentials.md)voor informatie over het configureren van uw Tenant om verifieer bare referenties te verlenen.