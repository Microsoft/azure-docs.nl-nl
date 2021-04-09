---
title: Azure CDN gebruiken met SAS | Microsoft Docs
description: Azure CDN ondersteunt het gebruik van Shared Access Signature (SAS) om beperkte toegang tot containers voor persoonlijke opslag te verlenen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ccf55e0e3986de8afe23cb646d4df743b576900c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725319"
---
# <a name="using-azure-cdn-with-sas"></a>Azure CDN gebruiken met SAS

Wanneer u een opslag account voor Azure Content Delivery Network (CDN) instelt dat moet worden gebruikt voor het opslaan van inhoud in de cache, kunnen standaard iedereen die de Url's voor uw opslag containers kent, toegang krijgen tot de bestanden die u hebt geüpload. Als u de bestanden in uw opslag account wilt beveiligen, kunt u de toegang van uw opslag containers van openbaar naar privé instellen. Als u dit wel doet, heeft niemand toegang tot uw bestanden. 

Als u beperkte toegang tot persoonlijke opslagcontainers wilt verlenen, kunt u de Shared Access Signature (SAS)-functie van uw Azure-opslagaccount gebruiken. Een SAS is een URI die beperkte toegangsrechten verleent aan uw Azure Storage-resources zonder uw accountsleutel prijs te geven. U kunt een SAS aan clients geven die u niet vertrouwt met de sleutel van uw opslag account, maar aan wie u de toegang tot bepaalde resources van het opslag account wilt delegeren. Door een URI voor een Shared Access-hand tekening te distribueren naar deze clients, verleent u deze gedurende een opgegeven periode toegang tot een resource.
 
Met een SAS kunt u verschillende para meters voor toegang tot een BLOB definiëren, zoals start-en verloop tijden, machtigingen (lezen/schrijven) en IP-bereiken. In dit artikel wordt beschreven hoe u SA'S kunt gebruiken in combi natie met Azure CDN. Zie [using Shared Access signatures (SAS) (Engelstalig)](../storage/common/storage-sas-overview.md)voor meer informatie over sa's, onder andere hoe u deze kunt maken en de parameter opties.

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Azure CDN instellen voor het werken met SAS met opslag
De volgende drie opties worden aanbevolen voor het gebruik van SAS met Azure CDN. Bij alle opties wordt ervan uitgegaan dat u al een Working SA'S hebt gemaakt (Zie vereisten). 
 
### <a name="prerequisites"></a>Vereisten
Maak een opslag account en genereer vervolgens een SAS voor uw asset om te starten. U kunt twee typen opgeslagen toegangs handtekeningen genereren: een service-SAS of een account-SAS. Zie [typen Shared Access signatures](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures)voor meer informatie.

Nadat u een SAS-token hebt gegenereerd, kunt u toegang krijgen tot uw Blob Storage-bestand door toe te voegen `?sv=<SAS token>` aan uw URL. Deze URL heeft de volgende indeling: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Bijvoorbeeld:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Zie [SAS-parameter overwegingen](#sas-parameter-considerations) en [para meters voor gedeelde toegangs handtekeningen](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works)voor meer informatie over het instellen van para meters.

![Instellingen voor CDN SAS](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Optie 1: SAS gebruiken met pass-through naar Blob-opslag van Azure CDN

Deze optie is het eenvoudigste en maakt gebruik van één SAS-token, dat wordt door gegeven van Azure CDN naar de oorspronkelijke server.
 
1. Selecteer een eind punt, selecteer **cache regels** en selecteer vervolgens **elke unieke URL** in de cache lijst voor de **query reeks** .

    ![Regels voor CDN-caching](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Nadat u SAS hebt ingesteld voor uw opslag account, moet u het SAS-token met het CDN-eind punt en de oorspronkelijke server-Url's gebruiken om het bestand te openen. 
   
   De resulterende URL van het CDN-eind punt heeft de volgende indeling: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Bijvoorbeeld:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Verfijn de cache duur door gebruik te maken van cache regels of door headers toe te voegen `Cache-Control` op de oorspronkelijke server. Omdat Azure CDN het SAS-token als een gewone query teken reeks behandelt, als een best practice u moet een cache duur instellen die verloopt op of vóór de SAS-verval tijd. Als een bestand in de cache gedurende een langere periode wordt opgeslagen dan de SAS actief is, is het bestand mogelijk toegankelijk vanaf de bron server Azure CDN nadat de SAS-verval tijd is verstreken. Als deze situatie zich voordoet en u het bestand in de cache ontoegankelijk wilt maken, moet u een opschoon bewerking op het bestand uitvoeren om het uit de cache te verwijderen. Voor informatie over het instellen van de cache duur op Azure CDN, Zie [beheer Azure CDN caching met cache regels](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Optie 2: verborgen CDN SAS-token met behulp van een herschrijf regel
 
Deze optie is alleen beschikbaar voor **Azure CDN Premium van Verizon** -profielen. Met deze optie kunt u de Blob-opslag op de oorspronkelijke server beveiligen. U kunt deze optie gebruiken als u geen specifieke toegangs beperkingen voor het bestand nodig hebt, maar u wilt voor komen dat gebruikers rechtstreeks toegang krijgen tot de opslag oorsprong om Azure CDN offload-tijden te verbeteren. Het SAS-token, dat onbekend is bij de gebruiker, is vereist voor iedereen die toegang heeft tot bestanden in de opgegeven container van de oorspronkelijke server. Vanwege de regel voor het herschrijven van de URL is het SAS-token echter niet vereist op het CDN-eind punt.
 
1. Gebruik de [engine regels](./cdn-verizon-premium-rules-engine.md) om een regel voor het opnieuw schrijven van een URL te maken. Het kan Maxi maal vier uur duren voordat nieuwe regels worden door gegeven.

   ![De knop CDN beheren](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Knop voor de engine voor CDN-regels](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   De volgende regel voor het herschrijven van een URL-voor beeld maakt gebruik van een reguliere-expressie patroon met een opname groep en een eind punt met de naam *sasstoragedemo*:
   
   Bron:   
   `(container1/.*)`


   Bestemming:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regel voor het opnieuw schrijven van de CDN-URL-links ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ CDN URL herschrijven regel-rechts](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Nadat de nieuwe regel actief wordt, heeft iedereen toegang tot bestanden in de opgegeven container op het CDN-eind punt, ongeacht of ze een SAS-token in de URL gebruiken. Dit is de indeling: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Bijvoorbeeld:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Verfijn de cache duur door gebruik te maken van cache regels of door headers toe te voegen `Cache-Control` op de oorspronkelijke server. Omdat Azure CDN het SAS-token als een gewone query teken reeks behandelt, als een best practice u moet een cache duur instellen die verloopt op of vóór de SAS-verval tijd. Als een bestand gedurende een langere duur in de cache wordt opgeslagen dan de SAS actief is, is het bestand mogelijk toegankelijk vanaf het Azure CDN-eind punt nadat de SAS-verval tijd is verstreken. Als deze situatie zich voordoet en u het bestand in de cache ontoegankelijk wilt maken, moet u een opschoon bewerking op het bestand uitvoeren om het uit de cache te verwijderen. Voor informatie over het instellen van de cache duur op Azure CDN, Zie [beheer Azure CDN caching met cache regels](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Optie 3: gebruik de verificatie van het CDN-beveiligings token met een regel voor herschrijven

Als u Azure CDN verificatie van beveiligings tokens wilt gebruiken, moet u een **Azure CDN Premium-Profiel van Verizon** hebben. Deze optie is het veiligst en aanpasbaar. Client toegang is gebaseerd op de beveiligings parameters die u hebt ingesteld voor het beveiligings token. Zodra u het beveiligings token hebt gemaakt en ingesteld, is het vereist voor alle URL van het CDN-eind punt. Vanwege de regel voor het herschrijven van de URL is het SAS-token echter niet vereist op het CDN-eind punt. Als het SAS-token later ongeldig wordt, kan Azure CDN de inhoud niet meer opnieuw valideren vanaf de oorspronkelijke server.

1. [Maak een beveiligings token voor Azure CDN](./cdn-token-auth.md#setting-up-token-authentication) en Activeer dit met behulp van de regel engine voor het CDN-eind punt en het pad waar uw gebruikers toegang tot het bestand hebben.

   Een eind punt-URL voor een beveiligings token heeft de volgende indeling:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Bijvoorbeeld:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   De parameter opties voor een beveiligings token verificatie verschillen van de parameter opties voor een SAS-token. Als u ervoor kiest een verloop tijd te gebruiken wanneer u een beveiligings token maakt, moet u dit instellen op dezelfde waarde als de verloop tijd voor het SAS-token. Dit zorgt ervoor dat de verloop tijd voorspelbaar is. 
 
2. Gebruik de [engine regels](./cdn-verizon-premium-rules-engine.md) om een regel voor het opnieuw schrijven van een URL te maken om SAS-token toegang tot alle blobs in de container in te scha kelen. Het kan Maxi maal vier uur duren voordat nieuwe regels worden door gegeven.

   De volgende regel voor het herschrijven van een URL-voor beeld maakt gebruik van een reguliere-expressie patroon met een opname groep en een eind punt met de naam *sasstoragedemo*:
   
   Bron:   
   `(container1/.*)`
   
   Bestemming:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Regel voor het opnieuw schrijven van de CDN-URL-links ](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
    ![ CDN URL herschrijven regel-rechts](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Als u de SAS verlengt, moet u ervoor zorgen dat u de regel voor het herschrijven van de URL bijwerkt met de nieuwe SAS-token. 

## <a name="sas-parameter-considerations"></a>SAS-parameter overwegingen

Omdat SAS-para meters niet zichtbaar zijn voor Azure CDN, kan Azure CDN het bezorgings gedrag niet wijzigen op basis hiervan. De gedefinieerde parameter beperkingen gelden alleen voor aanvragen die Azure CDN van de oorspronkelijke server, en niet van aanvragen van de client naar Azure CDN. Het is belang rijk dat u rekening houdt met het instellen van SAS-para meters. Als deze geavanceerde mogelijkheden vereist zijn en u [optie 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)gebruikt, stelt u de juiste beperkingen in voor het beveiligings token van Azure CDN.

| SAS-parameter naam | Description |
| --- | --- |
| Starten | Het tijdstip waarop Azure CDN toegang tot het blobbestand kan krijgen. Als gevolg van Clock scheefheid (wanneer een klok signaal op verschillende tijdstippen voor verschillende onderdelen arriveert), kiest u een tijd van 15 minuten eerder als u wilt dat de Asset direct beschikbaar is. |
| Beëindigen | De tijd waarna Azure CDN geen toegang meer heeft tot het blobbestand. Eerder in de cache opgeslagen bestanden op Azure CDN zijn nog steeds toegankelijk. Als u de verval tijd van het bestand wilt beheren, stelt u de juiste verloop tijd in op het Azure CDN beveiligings token of verwijdert u de Asset. |
| Toegestane IP-adressen | Optioneel. Als u **Azure CDN van Verizon** gebruikt, kunt u deze para meter instellen op de bereiken die zijn gedefinieerd in [Azure CDN van de IP-adresbereiken van de Verizon Edge-Server](./cdn-pop-list-api.md). Als u **Azure CDN van Akamai** gebruikt, kunt u de IP-adresbereiken para meter niet instellen omdat de IP-adressen niet statisch zijn.|
| Toegestane protocollen | De protocollen die zijn toegestaan voor een aanvraag met de account-SAS. De HTTPS-instelling wordt aanbevolen.|

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over SA'S:
- [Shared Access Signatures (SAS) gebruiken](../storage/common/storage-sas-overview.md)
- [Hand tekeningen voor gedeelde toegang, deel 2: een SAS maken en gebruiken met Blob Storage](../storage/common/storage-sas-overview.md)

Zie [Azure Content Delivery Network-assets beveiligen met token verificatie](./cdn-token-auth.md)voor meer informatie over het instellen van token verificatie.