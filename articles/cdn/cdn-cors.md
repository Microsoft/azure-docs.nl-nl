---
title: Azure CDN gebruiken met CORS | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Content Delivery Network (CDN) voor het delen van cross-Origin-resources (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018644"
---
# <a name="using-azure-cdn-with-cors"></a>Azure CDN gebruiken met CORS
## <a name="what-is-cors"></a>Wat is CORS?
CORS (Cross-Origin Resource Sharing) is een HTTP-functie waarmee een webtoepassing die wordt uitgevoerd onder één domein, toegang kan krijgen tot resources in een ander domein. Om de kans op cross-site scripting aanvallen te verminderen, implementeren alle moderne webbrowsers een beveiligings beperking die bekend staat als [hetzelfde-Origin-beleid](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Hiermee wordt voor komen dat een webpagina Api's in een ander domein aanroept.  CORS biedt een veilige manier om één oorsprong (het oorspronkelijke domein) toe te staan om Api's in een andere oorsprong aan te roepen.

## <a name="how-it-works"></a>Uitleg
Er zijn twee typen CORS-aanvragen, *eenvoudige aanvragen* en *complexe aanvragen.*

### <a name="for-simple-requests"></a>Voor eenvoudige aanvragen:

1. De browser verzendt de CORS-aanvraag met een extra header van de HTTP-aanvraag van de **bron** . De waarde van deze header is de oorsprong die de bovenliggende pagina heeft geleverd. deze is gedefinieerd als de combi natie van *protocol,* *domein* en *poort.*  Wanneer een pagina van HTTPS \: //www.contoso.com probeert toegang te krijgen tot de gegevens van een gebruiker in de fabrikam.com-oorsprong, wordt de volgende aanvraag header verzonden naar fabrikam.com:

   `Origin: https://www.contoso.com`

2. De server reageert mogelijk met een van de volgende:

   * Een ' **Access-Control-Allow-Origin** -header in het antwoord dat aangeeft welke oorspronkelijke site is toegestaan. Bijvoorbeeld:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Een HTTP-fout code zoals 403 als de server de cross-Origin-aanvraag niet toestaat na het controleren van de oorspronkelijke header

   * Een ' **Access-Control-Allow-Origin** -header met een Joker teken waarmee alle oorsprongen worden toegestaan:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Voor complexe aanvragen:

Een complexe aanvraag is een CORS-aanvraag waarbij de browser een *Preflight-aanvraag* (dat wil zeggen een oriënterende test) moet verzenden voordat de daad werkelijke CORS-aanvraag wordt verzonden. De Preflight-aanvraag vraagt de server machtiging aan als de oorspronkelijke CORS-aanvraag kan door gaan en een `OPTIONS` aanvraag voor dezelfde URL is.

> [!TIP]
> Voor meer informatie over CORS-stromen en veelvoorkomende Valk uilen, raadpleegt [u de hand leiding voor cors voor rest api's](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Joker tekens of scenario's met één oorsprong
CORS op Azure CDN werkt automatisch zonder extra configuratie wanneer de header **Access-Control-Allow-Origin** is ingesteld op Joker teken (*) of één oorsprong.  Het CDN zal het eerste antwoord in de cache opslaan en de volgende aanvragen zullen dezelfde header gebruiken.

Als er al aanvragen zijn gedaan in het CDN voordat CORS is ingesteld op uw oorsprong, moet u de inhoud van de eindpunt inhoud verwijderen om de inhoud opnieuw te laden met de header **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Scenario's met meerdere oorsprong
Als u wilt toestaan dat een specifieke lijst met oorsprongen voor CORS wordt toegestaan, zijn de dingen wat gecompliceerder. Het probleem treedt op wanneer de CDN het **toegangs beheer-allow-Origin** -header voor de eerste CORS-oorsprong in cache opslaat.  Wanneer een andere CORS-oorsprong een volgende aanvraag doet, wordt het CDN de in cache opgeslagen **Access-Control-Allow-Origin** -header, die niet overeenkomt.  Er zijn verschillende manieren om dit te corrigeren.

### <a name="azure-cdn-standard-profiles"></a>Azure CDN standaard profielen
In Azure CDN standaard van micro soft kunt u een regel maken in de [standaard regels-engine](cdn-standard-rules-engine-reference.md) om de **oorspronkelijke** header van de aanvraag te controleren. Als het een geldige oorsprong is, wordt met de regel de header **Access-Control-Allow-Origin** ingesteld met de gewenste waarde. In dit geval wordt de header **Access-Control-Allow-Origin** van de oorspronkelijke server van het bestand genegeerd en worden de toegestane CORS-oorsprong volledig beheerd door de engine van de CDN.

![Voor beeld van regels met standaard regels-engine](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> U kunt extra acties aan uw regel toevoegen om extra antwoord headers te wijzigen, zoals **Access-Control-Allow-methoden**.
> 

Op **Azure CDN standaard van Akamai** is het enige mechanisme voor het toestaan van meerdere oorsprongen zonder het gebruik van de oorsprong van de Joker [teken reeks cache](cdn-query-string.md)gebruik te gebruiken. Schakel de query teken reeks instelling voor het CDN-eind punt in en gebruik vervolgens een unieke query reeks voor aanvragen van elk toegestaan domein. Dit leidt ertoe dat de CDN een afzonderlijk object in de cache opslaat voor elke unieke query reeks. Deze methode is echter niet ideaal, omdat deze ertoe leidt dat meerdere kopieën van hetzelfde bestand in het cache geheugen van de CDN worden opgeslagen.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium van Verizon
U moet [een regel maken](./cdn-verizon-premium-rules-engine.md) voor het controleren van de **oorspronkelijke** header van de aanvraag met de Verizon Premium-regels engine.  Als het een geldige oorsprong is, stelt uw regel de header **Access-Control-Allow-Origin** in met de oorsprong die in de aanvraag is opgegeven.  Als de oorsprong die in de koptekst van de **oorsprong** is opgegeven, niet is toegestaan, moet uw regel de header **Access-Control-Allow-Origin** weglaten, waardoor de browser de aanvraag weigert. 

Er zijn twee manieren om dit te doen met de Premium-regel engine. In beide gevallen wordt de header **Access-Control-Allow-Origin** van de oorspronkelijke server van het bestand genegeerd en worden de toegestane CORS-oorsprong volledig beheerd door de engine van de CDN.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Eén reguliere expressie met alle geldige oorsprongen
In dit geval maakt u een reguliere expressie die alle oorsprongen bevat die u wilt toestaan: 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> **Azure CDN Premium van Verizon** gebruikt [perl-compatibele reguliere expressies](https://pcre.org/) als engine voor reguliere expressies.  U kunt een hulp programma zoals [reguliere expressies 101](https://regex101.com/) gebruiken om uw reguliere expressie te valideren.  Houd er rekening mee dat het teken '/' geldig is in reguliere expressies en niet hoeft te worden ontsnapt, maar dat het escape-teken wordt beschouwd als een best practice en wordt verwacht door sommige regex-validatie functies.
> 
> 

Als de reguliere expressie overeenkomt, vervangt uw regel de header **Access-Control-Allow-Origin** (indien van toepassing) van de oorsprong met de oorsprong die de aanvraag heeft verzonden.  U kunt ook extra CORS-headers toevoegen, zoals **Access-Control-Allow-methoden**.

![Voor beeld van regels met reguliere expressie](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Regel voor aanvraag header voor elke oorsprong.
U kunt in plaats van reguliere expressies een afzonderlijke regel maken voor elke oorsprong die u wilt toestaan met de [voor waarde](/previous-versions/azure/mt757336(v=azure.100)#match-conditions)voor het gebruik van de **aanvraag header joker tekens** . Net als bij de methode reguliere expressie, worden de CORS-headers door de engine voor regels alleen ingesteld. 

![Voor beeld van regels zonder reguliere expressie](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> In het bovenstaande voor beeld wordt het gebruik van het Joker teken * door gegeven aan de regel Engine om zowel HTTP als HTTPS aan te passen.
> 
>