---
title: Richtlijnen voor beperkingen in Azure Key Vault
description: Key Vault beperking beperkt het aantal gelijktijdige aanroepen om te voor komen dat bronnen worden overmatigd.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7bdc3ac517df6b73fba7231cfe0fdc9855803782
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175750"
---
# <a name="azure-key-vault-throttling-guidance"></a>Richtlijnen voor beperkingen in Azure Key Vault

Beperking is een proces dat u initieert en beperkt het aantal gelijktijdige aanroepen naar de Azure-service om te voor komen dat bronnen worden gevermijdd. Azure Key Vault (Azure) is ontworpen om een groot aantal aanvragen af te handelen. Als er sprake is van een overweldigend aantal aanvragen, helpt het beperken van de aanvragen van uw client tot optimale prestaties en betrouw baarheid van de Azure-service.

Beperkings limieten variëren op basis van het scenario. Als u bijvoorbeeld een groot aantal schrijf bewerkingen uitvoert, is de kans op beperking hoger dan wanneer u alleen lees bewerkingen uitvoert.

## <a name="how-does-key-vault-handle-its-limits"></a>Hoe worden de limieten Key Vault afhandelen?

Service limieten in Key Vault misbruik van bronnen te voor komen en ervoor te zorgen dat de service voor alle clients van Key Vault. Wanneer een service drempel wordt overschreden, wordt door Key Vault alle aanvragen van die client gedurende een bepaalde periode beperkt, wordt de HTTP-status code 429 (te veel aanvragen) geretourneerd en mislukt de aanvraag. Mislukte aanvragen die een 429 retour neren, tellen niet mee voor de beperkings limieten die door Key Vault worden bijgehouden. 

Key Vault was oorspronkelijk ontworpen om te worden gebruikt om uw geheimen op te slaan en op te halen tijdens de implementatie.  De wereld heeft zich ontwikkeld en Key Vault wordt tijdens runtime gebruikt om geheimen op te slaan en op te halen, en vaak willen apps en services Key Vault als een Data Base gebruiken.  Huidige limieten bieden geen ondersteuning voor hoge doorvoer snelheden.

Key Vault oorspronkelijk is gemaakt met de limieten die zijn opgegeven in [Azure Key Vault service limieten](service-limits.md).  Als u uw Key Vault wilt maximaliseren door middel van tarieven, zijn hier enkele aanbevolen richt lijnen/aanbevolen procedures voor het maximaliseren van uw door Voer:
1. Zorg ervoor dat u de beperking hebt ingesteld.  De client moet voldoen aan het beleid voor exponentiële back-ups voor 429 en ervoor zorgen dat u nieuwe pogingen doet volgens onderstaande instructies.
1. Deel uw Key Vault verkeer onder meerdere kluizen en verschillende regio's.   Gebruik een afzonderlijke kluis voor elk domein voor beveiliging/Beschik baarheid.   Als u vijf apps hebt, elk in twee regio's, raden we 10 kluizen aan met elk geheim dat uniek is voor de app en regio.  Een limiet voor het hele abonnement voor alle transactie typen is vijf keer de limiet voor de individuele sleutel kluis. Andere HSM-transacties per abonnement zijn bijvoorbeeld beperkt tot 5000 transacties binnen tien seconden per abonnement. Overweeg het opslaan van het geheim binnen uw service of app om ook de RPS rechtstreeks te verminderen naar sleutel kluis en/of op Burst gebaseerd verkeer te verwerken.  U kunt het verkeer ook verdelen over verschillende regio's om latentie te minimaliseren en een ander abonnement/kluis gebruiken.  Verzend niet meer dan de limiet van het abonnement voor de Key Vault-service in één Azure-regio.
1. Cache de geheimen die u ophaalt uit Azure Key Vault in het geheugen en gebruik waar mogelijk van geheugen.  Het opnieuw lezen van Azure Key Vault alleen wanneer de kopie in de cache niet meer werkt (bijvoorbeeld omdat deze is gedraaid bij de bron). 
1. Key Vault is ontworpen voor uw eigen service geheimen.   Als u de geheimen van uw klanten opslaat (met name voor scenario's met hoge doorvoer opslag), kunt u overwegen om de sleutels in een Data Base of opslag account met versleuteling te plaatsen en alleen de hoofd sleutel in Azure Key Vault op te slaan.
1. Het versleutelen, verpakken en controleren van open bare-sleutel bewerkingen kunnen worden uitgevoerd zonder toegang tot Key Vault, wat niet alleen het risico op beperking vermindert, maar ook de betrouw baarheid verbetert (zolang u het open bare-sleutel materiaal op de juiste wijze in de cache opslaat).
1. Als u Key Vault gebruikt om referenties voor een service op te slaan, controleert u of deze service ondersteuning biedt voor Azure AD-verificatie voor het rechtstreeks verifiëren. Dit vermindert de belasting van Key Vault, verbetert de betrouw baarheid en vereenvoudigt uw code omdat Key Vault nu de Azure AD-token kan gebruiken.  Veel services zijn verplaatst naar met behulp van Azure AD-verificatie.  Bekijk de huidige lijst bij [Services die ondersteuning bieden voor beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Overweeg uw belasting/implementatie over een langere periode te spreiden om te blijven onder de huidige RPS-limieten.
1. Als uw app meerdere knoop punten omvat die dezelfde geheimen moeten lezen, kunt u het beste een uitzonderings patroon gebruiken, waarbij de ene entiteit het geheim leest van Key Vault en uitwaaiers naar alle knoop punten.   De opgehaalde geheimen alleen in de cache opslaan in het geheugen.
Als u merkt dat het bovenstaande nog niet aan uw behoeften voldoet, vult u de onderstaande tabel in en neemt u contact met ons op om te bepalen welke extra capaciteit er kan worden toegevoegd (voor beeld hieronder is alleen bedoeld voor demonstratie doeleinden).

| Kluisnaam | Kluis regio | Object type (geheim, sleutel of certificaat) | Bewerking (en) * | Sleutel type | Sleutel lengte of-curve | HSM-sleutel?| Er is een constante status RPS vereist | Piek RPS vereist |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Sleutel | Teken | EC | P-256 | Nee | 200 | 1000 |

\* Zie [Azure Key Vault-bewerkingen](/rest/api/keyvault/key-operations)voor een volledige lijst met mogelijke waarden.

Als aanvullende capaciteit is goedgekeurd, let dan op het volgende als resultaat van de capaciteits toename:
1. Wijzigingen in het gegevens consistentie model. Zodra een kluis in de lijst met extra doorvoer capaciteit is toegestaan, wordt de Key Vault service gegevens consistentie garantie gewijzigd (nood zakelijk om te voldoen aan de RPS van een hoger volume omdat de onderliggende Azure Storage service niet kan worden uitgevoerd).  In een kort gezegd:
  1. **Zonder vermelding toestaan**: de Key Vault-service geeft de resultaten van een schrijf bewerking weer (bijvoorbeeld Geheimset, CreateKey) direct in volgende aanroepen (bijvoorbeeld SecretGet).
  1. **Met vermelding toestaan**: de Key Vault-service geeft de resultaten van een schrijf bewerking weer (bijvoorbeeld Geheimset, CreateKey) binnen 60 seconden in volgende aanroepen (bijvoorbeeld SecretGet).
1. Client code moet voldoen aan het beleid voor uitstel van 429 nieuwe pogingen. De client code die de Key Vault-service aanroept, mag niet direct Key Vault aanvragen opnieuw proberen wanneer de 429-respons code wordt ontvangen.  De Azure Key Vault beperkings richtlijnen die hier worden gepubliceerd, wordt aanbevolen exponentiële uitstel toe te passen wanneer u een 429 HTTP-respons code ontvangt.

Neem contact met ons op als u een geldige zakelijke case hebt voor hogere beperkings limieten.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Uw app beperken als reactie op service limieten

Hier volgen enkele **Aanbevolen procedures** voor het uitvoeren van een beperking van uw service:
- Verminder het aantal bewerkingen per aanvraag.
- Verminder de frequentie van aanvragen.
- Vermijd onmiddellijke pogingen. 
    - Alle aanvragen toenemen ten opzichte van uw gebruiks limieten.

Wanneer u de fout afhandeling van uw app implementeert, gebruikt u de HTTP-fout code 429 om te detecteren of de behoefte aan client zijde wordt beperkt. Als de aanvraag opnieuw mislukt met een HTTP 429-fout code, wordt er nog steeds een Azure-service limiet tegen komen. Ga verder met het gebruik van de aanbevolen beperkings methode voor de client en probeer de aanvraag opnieuw uit te voeren totdat deze slaagt.

De code die exponentiële uitstel implementeert, wordt hieronder weer gegeven. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Het gebruik van deze code in een client C#-toepassing is eenvoudig. 

### <a name="recommended-client-side-throttling-method"></a>Aanbevolen beperkings methode aan client zijde

In HTTP-fout code 429 begint het beperken van uw client met een exponentiële uitstel-benadering:

1. Wacht 1 seconde, aanvraag opnieuw proberen
2. Als er nog steeds een wacht tijd van 2 seconden is, aanvraag opnieuw proberen
3. Als er nog steeds een wacht tijd van 4 seconden is, aanvraag opnieuw proberen
4. Als er nog steeds een wacht tijd van 8 seconden is, aanvraag opnieuw proberen
5. Als er nog steeds een wacht tijd is, 16 seconden, aanvraag opnieuw proberen

Op dit moment moet u geen HTTP 429-antwoord codes ophalen.

## <a name="see-also"></a>Zie ook

Zie [beperkings patroon](/azure/architecture/patterns/throttling)voor een dieper richting van het beperken van de Microsoft Cloud.
