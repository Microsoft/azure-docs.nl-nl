---
title: Gebruikersregistratie en productabonnement delegeren
description: Meer informatie over het delegeren van gebruikers registratie en product abonnementen aan derden in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 54193c9333c75fd8b973ebe33470fca3617e2f2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341838"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Gebruikersregistratie en productabonnement delegeren

Met delegering kunt u uw bestaande website gebruiken voor het afhandelen van ontwikkel aars die zich aanmelden/registreren en abonneren op producten, in tegens telling tot het gebruik van de ingebouwde functionaliteit in de ontwikkelaars Portal. Hiermee kan uw website eigenaar worden van de gebruikers gegevens en kan de validatie van deze stappen op een aangepaste manier worden uitgevoerd.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Aanmelden voor ontwikkel aars overdragen en registreren

Als u de ontwikkelaar wilt delegeren, meldt u zich aan en meldt u zich aan bij uw bestaande website. u moet een speciaal overdrachts eindpunt maken op uw site. Het moet fungeren als het toegangs punt voor een dergelijke aanvraag die wordt gestart vanuit het API Management ontwikkelaars Portal.

De uiteindelijke werk stroom ziet er als volgt uit:

1. Ontwikkelaar klikt op de koppeling aanmelden of registreren op de API Management ontwikkelaars Portal
2. Browser wordt omgeleid naar het eind punt voor delegering
3. Eind punt voor delegering in retour omleidingen of geeft gebruikers interface die gebruiker vraagt om zich aan te melden of zich aan te melden
4. Als de gebruiker is geslaagd, wordt deze teruggeleid naar de API Management ontwikkelaars portal-pagina waarmee ze worden gestart

We gaan eerst API Management instellen om aanvragen te routeren via het eind punt van de overdracht. Zoek in het Azure Portal naar **beveiliging** in uw API Management resource en klik vervolgens op het item **delegering** . Klik op het selectie vakje om aanmeld & aanmelden in te scha kelen.

![Pagina overdracht][api-management-delegation-signin-up]

* Bepaal wat de URL van uw speciale eind punt voor delegering is en voer deze in het veld **delegering ENDPOINT URL** in. 
* Voer in het veld verificatie sleutel delegering een geheim in dat wordt gebruikt voor het berekenen van een hand tekening die u voor verificatie hebt ontvangen om ervoor te zorgen dat de aanvraag inderdaad afkomstig is van Azure API Management. U kunt op de knop **genereren** klikken om API Management wille keurig een sleutel te genereren.

U moet nu het **eind punt voor delegering** maken. Het moet een aantal acties uitvoeren:

1. Ontvang een aanvraag in de volgende vorm:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl = {URL van de bron pagina} &Salt = {string} &sig = {String}*
   
    Query parameters voor de case voor aanmelden/registreren:
   
   * **bewerking**: identificeert het type overdrachts aanvraag. het kan alleen **Aanmelden** in dit geval
   * **returnUrl**: de URL van de pagina waarop de gebruiker heeft geklikt op een koppeling voor aanmelden of registreren
   * **Salt**: een speciale Salt-teken reeks die wordt gebruikt voor het berekenen van een beveiligings-hash
   * **sig**: een berekende beveiligings-hash die moet worden gebruikt voor de vergelijking met uw eigen berekende hash
2. Controleer of de aanvraag afkomstig is van Azure API Management (optioneel, maar wordt nadrukkelijk aanbevolen voor beveiliging)
   
   * Een HMAC-SHA512 gebruikt-hash van een teken reeks berekenen op basis van de **returnUrl** -en **Salt** -query parameters ([voorbeeld code hieronder]):
     
     > HMAC (**Salt** + ' \n ' + **returnUrl**)

   * Vergelijk de bovenstaande berekende hash met de waarde van de para meter **sig** -query. Als de twee hashes overeenkomen, gaat u verder met de volgende stap en weigert u de aanvraag.
3. Controleer of u een aanvraag voor aanmelden/aanmelden ontvangt: de **bewerking** query parameter wordt ingesteld op '**Aanmelden**'.
4. De gebruiker met de gebruikers interface weer geven om zich aan te melden of zich aan te melden
5. Als de gebruiker zich aanmeldt, moet u in API Management een bijbehorend account maken. [Maak een gebruiker] met de API Management rest API. Als u dit doet, moet u ervoor zorgen dat u de gebruikers-ID instelt op dezelfde waarde als in uw gebruikers archief of op een ID die u kunt bijhouden.
6. Als de gebruiker is geverifieerd:
   
   * [Een gedeeld toegangs token aanvragen] via de API Management rest API
   * Voeg een returnUrl-query parameter toe aan de SSO-URL die u hebt ontvangen van de bovenstaande API-aanroep:
     
     > bijvoorbeeld: `https://<developer portal domain, for example: contoso.developer.azure-api.net>/signin-sso?token=<URL-encoded token>&returnUrl=<URL-encoded URL, for example: %2Freturn%2Furl>` 
     
   * De gebruiker omleiden naar de bovenstaande geproduceerde URL

Naast de **aanmeldings** bewerking kunt u ook account beheer uitvoeren door de vorige stappen te volgen en een van de volgende bewerkingen uit te voeren:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**
* **Melden**

U moet de volgende query parameters door geven voor account beheer bewerkingen.

* **bewerking**: identificeert welk type overdrachts aanvraag is (ChangePassword, ChangeProfile of CloseAccount)
* **GebruikersID**: de gebruikers-id van het account dat u wilt beheren
* **Salt**: een speciale Salt-teken reeks die wordt gebruikt voor het berekenen van een beveiligings-hash
* **sig**: een berekende beveiligings-hash die moet worden gebruikt voor de vergelijking met uw eigen berekende hash

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Product abonnement delegeren

Het delegeren van het product abonnement werkt op dezelfde manier als het overdragen van gebruikers die zich hebben aangemeld. De uiteindelijke werk stroom zou er als volgt uitzien:

1. Ontwikkelaar selecteert een product in het API Management ontwikkelaars Portal en klikt op de knop abonneren.
2. De browser wordt omgeleid naar het eind punt voor delegering.
3. Met het delegatie-eind punt worden de vereiste stappen voor een product abonnement uitgevoerd. Het is aan te raden om de stappen te ontwerpen. Ze kunnen ook omleiden naar een andere pagina voor het aanvragen van facturerings gegevens, het stellen van aanvullende vragen of het opslaan van de gegevens en het niet vereisen van een gebruikers actie.

Als u de functionaliteit wilt inschakelen, klikt u op de pagina **delegeren** op **product abonnement delegeren**.

Zorg er vervolgens voor dat het eind punt voor delegering de volgende acties uitvoeren:

1. Ontvang een aanvraag in de volgende vorm:
   
   > *http: \/ /www.yourwebsite.com/apimdelegation? Operation = {Operation} &ProductID = {product to Subscriber to} &userId = {Request aanvraag} &Salt = {string} &sig = {String}*
   >
   
    Query parameters voor het case-product abonnement:
   
   * **bewerking**: identificeert welk type overdrachts aanvraag. Voor aanvragen voor product abonnementen zijn de volgende geldige opties:
     * "Abonneren": een aanvraag om de gebruiker te abonneren op een bepaald product met de opgegeven ID (zie hieronder)
     * ' Abonnement opzeggen ': een aanvraag voor het afmelden van een gebruiker bij een product
     * "Renew": een aanvraag om een abonnement te vernieuwen (bijvoorbeeld dat mogelijk verloopt)
   * **ProductID**: bij het *Abonneren* -de id van het product waarvoor de gebruiker zich heeft gevraagd om zich te abonneren
   * **subscriptionId**: on *unsubscribe* and *renew* -de id van het product abonnement
   * **gebruikers**-id: bij het *Aanmelden* -de gebruikers naam van de gebruiker voor wie de aanvraag is ingediend
   * **Salt**: een speciale Salt-teken reeks die wordt gebruikt voor het berekenen van een beveiligings-hash
   * **sig**: een berekende beveiligings-hash die moet worden gebruikt voor de vergelijking met uw eigen berekende hash

2. Controleer of de aanvraag afkomstig is van Azure API Management (optioneel, maar wordt nadrukkelijk aanbevolen voor beveiliging)
   
   * Een HMAC-SHA512 gebruikt van een teken reeks berekenen op basis van de query parameters **ProductID**, **userId** en **Salt** :
     
     > HMAC (**Salt** + ' \n ' + **ProductID** + ' \n ' + **GebruikersID**)
     > 
     > 
   * Vergelijk de bovenstaande berekende hash met de waarde van de para meter **sig** -query. Als de twee hashes overeenkomen, gaat u verder met de volgende stap en weigert u de aanvraag.
3. Product abonnement verwerken op basis van het type bewerking dat in **bewerking** is aangevraagd, bijvoorbeeld facturering, verdere vragen, enzovoort.
4. Als u de gebruiker aan het product wilt abonneren, moet u zich abonneren op het API Management product door [de rest API aan te roepen voor abonnementen].

## <a name="example-code"></a><a name="delegate-example-code"></a> Voorbeeld code

Deze code voorbeelden laten zien hoe u:

* Neem de *verificatie sleutel voor delegering* op die is ingesteld in het scherm overdracht van de uitgevers Portal
* Maak een HMAC, dat vervolgens wordt gebruikt om de hand tekening te valideren en de geldigheid van de door gegeven returnUrl te bewijzen.

Dezelfde code werkt voor de productId en de gebruikers naam met een kleine wijziging.

**C#-code voor het genereren van hash van returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**NodeJS-code voor het genereren van de hash van returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> U moet [de ontwikkelaars Portal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de overdrachts wijzigingen van kracht te laten worden.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende video voor meer informatie over delegering:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Een gedeeld toegangs token aanvragen]: /rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken
[een gebruiker maken]: /rest/api/apimanagement/2019-12-01/user/createorupdate
[de REST API voor abonnementen aanroepen]: /rest/api/apimanagement/2019-12-01/subscription/createorupdate
[Next steps]: #next-steps
[voorbeeld code die hieronder wordt vermeld]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
