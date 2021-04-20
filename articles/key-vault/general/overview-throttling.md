---
title: Richtlijnen voor beperkingen in Azure Key Vault
description: Key Vault beperking beperkt het aantal gelijktijdige aanroepen om te voorkomen dat resources te veel worden gebruikt.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7a215b53f673a7414f1b3662f519de5c26faaa9d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749528"
---
# <a name="azure-key-vault-throttling-guidance"></a>Richtlijnen voor beperkingen in Azure Key Vault

Beperking is een proces dat u start en dat het aantal gelijktijdige aanroepen naar de Azure-service beperkt om te voorkomen dat resources te veel worden gebruikt. Azure Key Vault (AKV) is ontworpen voor het verwerken van een groot aantal aanvragen. Als er een overweldigend aantal aanvragen optreedt, helpt het beperken van aanvragen van uw client om optimale prestaties en betrouwbaarheid van de AKV-service te behouden.

Beperkingslimieten variëren afhankelijk van het scenario. Als u bijvoorbeeld een groot aantal schrijfvolumes gebruikt, is de kans op beperking groter dan wanneer u alleen lees- en schrijfvolumes gebruikt.

## <a name="how-does-key-vault-handle-its-limits"></a>Hoe worden Key Vault limieten verwerkt?

Servicelimieten in Key Vault misbruik van resources voorkomen en zorgen voor de kwaliteit van de service voor alle Key Vault van klanten. Wanneer een servicedrempelwaarde wordt overschreden, beperkt Key Vault verdere aanvragen van die client voor een bepaalde tijd, retourneert http-statuscode 429 (te veel aanvragen) en mislukt de aanvraag. Mislukte aanvragen die een 429 retourneren, tellen niet mee voor de vertragingslimieten die door de Key Vault. 

Key Vault is oorspronkelijk ontworpen om te worden gebruikt voor het opslaan en ophalen van uw geheimen tijdens de implementatie.  De wereld is veranderd en Key Vault wordt tijdens run-time gebruikt om geheimen op te slaan en op te halen, en vaak willen apps en services Key Vault gebruiken als een database.  Huidige limieten bieden geen ondersteuning voor hoge doorvoersnelheden.

Key Vault oorspronkelijk gemaakt met de limieten die zijn opgegeven in [Azure Key Vault servicelimieten](service-limits.md).  Hier volgen enkele Key Vault aanbevolen richtlijnen/aanbevolen procedures voor het maximaliseren van uw doorvoer:
1. Zorg ervoor dat er beperkingen zijn.  De client moet zich houden aan het exponentieel beleid voor 429's en ervoor zorgen dat u nieuwe keren een nieuwe doet volgens de onderstaande richtlijnen.
1. Verdeel uw Key Vault verkeer over meerdere kluizen en verschillende regio's.   Gebruik een afzonderlijke kluis voor elk beveiligings-/beschikbaarheidsdomein.   Als u vijf apps hebt, elk in twee regio's, raden we 10 kluizen aan die elk de geheimen bevatten die uniek zijn voor de app en regio.  Een abonnementsbrede limiet voor alle transactietypen is vijf keer de limiet voor afzonderlijke sleutelkluizen. Andere HSM-transacties per abonnement zijn bijvoorbeeld beperkt tot 5000 transacties binnen tien seconden per abonnement. Overweeg het geheim in de cloud op te halen in uw service of app om de RPS ook rechtstreeks naar de sleutelkluis te verminderen en/of burstverkeer te verwerken.  U kunt uw verkeer ook onderverdelen in verschillende regio's om de latentie te minimaliseren en een ander abonnement/een andere kluis te gebruiken.  Verzend niet meer dan de abonnementslimiet voor de Key Vault service in één Azure-regio.
1. Cache de geheimen die u opgeeft Azure Key Vault in het geheugen en hergebruik indien mogelijk uit het geheugen.  Lees de gegevens alleen opnieuw Azure Key Vault wanneer de kopie in de cache niet meer werkt (bijvoorbeeld omdat deze is geroteerd bij de bron). 
1. Key Vault is ontworpen voor uw eigen servicesgeheimen.   Als u de geheimen van uw klanten opslaat (met name voor sleutelopslagscenario's met hoge doorvoer), kunt u overwegen de sleutels in een database of opslagaccount met versleuteling op te slaan en alleen de hoofdsleutel op te slaan in Azure Key Vault.
1. Openbare-sleutelbewerkingen kunnen worden versleuteld, verpakt en gecontroleerd zonder toegang tot Key Vault, wat niet alleen het risico op beperking vermindert, maar ook de betrouwbaarheid verbetert (zolang u het openbare sleutelmateriaal op de juiste manier in de cache opgeslagen).
1. Als u een Key Vault voor een service op te slaan, controleert u of die service ondersteuning biedt voor Azure AD-verificatie om rechtstreeks te verifiëren. Dit vermindert de belasting van Key Vault, verbetert de betrouwbaarheid en vereenvoudigt uw code omdat Key Vault nu het Azure AD-token kan gebruiken.  Veel services zijn verplaatst naar het gebruik van Azure AD-auth.  Zie de huidige lijst in [Services die beheerde identiteiten voor Azure-resources ondersteunen.](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)
1. Overweeg de belasting/implementatie langer te belasten om onder de huidige RPS-limieten te blijven.
1. Als uw app uit meerdere knooppunten bestaat die dezelfde geheim(en) moeten lezen, kunt u overwegen een fan out-patroon te gebruiken, waarbij één entiteit het geheim leest van Key Vault en alle knooppunten uitwaa uitwaaeert.   Cache de opgehaalde geheimen alleen in het geheugen.
Als u vindt dat het bovenstaande nog steeds niet aan uw behoeften voldoet, vult u de onderstaande tabel in en neemt u contact met ons op om te bepalen welke extra capaciteit kan worden toegevoegd (zie het onderstaande voorbeeld alleen ter illustratie).

| Kluisnaam | Kluisregio | Objecttype (geheim, sleutel of certificaat) | Bewerking(en)* | Sleuteltype | Sleutellengte of -curve | HSM-sleutel?| Stabiele status RPS vereist | Piek RPS vereist |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Sleutel | Teken | EC | P-256 | No | 200 | 1000 |

\*Zie voor een volledige lijst met mogelijke waarden [Azure Key Vault bewerkingen.](/rest/api/keyvault/key-operations)

Als extra capaciteit is goedgekeurd, moet u rekening maken met het volgende als gevolg van de capaciteitsverhoging:
1. Wijzigingen in het gegevensconsistentiemodel. Zodra een kluis is toegestaan met extra doorvoercapaciteit, garandeert de gegevensconsistentie van de Key Vault-service wijzigingen (nodig om te voldoen aan RPS met een hoger volume omdat de onderliggende Azure Storage-service het niet kan bij houden).  In een notendop:
  1. **Zonder vermelding van toestaan:** de Key Vault service geeft de resultaten van een schrijfbewerking weer (bijvoorbeeld SecretSet, CreateKey) direct in volgende aanroepen (bijvoorbeeld SecretGet, KeySign).
  1. **Met vermelding toestaan:** de Key Vault service geeft de resultaten van een schrijfbewerking weer (bijvoorbeeld SecretSet, CreateKey) binnen 60 seconden in volgende aanroepen (bijvoorbeeld SecretGet, KeySign).
1. Clientcode moet het beleid voor het uitschakelen van 429 nieuwe keren in ere houden. De clientcode die de Key Vault service aanroept, mag niet onmiddellijk opnieuw proberen Key Vault aanvragen wanneer deze een 429-responscode ontvangt.  In Azure Key Vault richtlijnen voor bandbreedtebeperking die hier worden gepubliceerd, wordt aangeraden exponentieel terugloop toe te passen bij het ontvangen van een 429 HTTP-responscode.

Als u een geldige business case hebt voor hogere limieten, kunt u contact met ons opnemen.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Uw app beperkt in reactie op servicelimieten

Hier volgen de **best practices die** u moet implementeren wanneer uw service wordt beperkt:
- Verminder het aantal bewerkingen per aanvraag.
- Verminder de frequentie van aanvragen.
- Vermijd onmiddellijke nieuwe proberen. 
    - Alle aanvragen worden verzameld op basis van uw gebruikslimieten.

Wanneer u de foutafhandeling van uw app implementeert, gebruikt u de HTTP-foutcode 429 om te detecteren of beperking aan clientzijde nodig is. Als de aanvraag opnieuw mislukt met een HTTP 429-foutcode, wordt er nog steeds een Azure-servicelimiet aangetroffen. Ga door met het gebruik van de aanbevolen beperkingsmethode aan de clientzijde, waarbij u de aanvraag opnieuw wilt proberen totdat deze is gelukt.

Code die exponentieel afboeking implementeert, wordt hieronder weergegeven. 
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


Het gebruik van deze code in een client-C#-toepassing is eenvoudig. 

### <a name="recommended-client-side-throttling-method"></a>Aanbevolen beperkingsmethode aan clientzijde

In HTTP-foutcode 429 kunt u beginnen met het beperken van uw client met behulp van een exponentiële back-offbenadering:

1. 1 seconde wachten, aanvraag opnieuw proberen
2. Als de wachttijd nog steeds twee seconden is beperkt, kunt u de aanvraag opnieuw proberen
3. Als de wachttijd nog steeds 4 seconden is beperkt, kunt u de aanvraag opnieuw proberen
4. Als de wachttijd nog steeds 8 seconden is beperkt, kunt u de aanvraag opnieuw proberen
5. Als de wachttijd nog steeds 16 seconden is beperkt, kunt u de aanvraag opnieuw proberen

Op dit moment zou u geen HTTP 429-responscodes moeten krijgen.

## <a name="see-also"></a>Zie ook

Zie Beperkingspatroon voor een diepere oriëntatie van beperking in de Microsoft [Cloud.](/azure/architecture/patterns/throttling)
