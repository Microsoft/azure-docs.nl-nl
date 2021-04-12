---
title: 'Zelf studie: aan de slag met Azure Active Directory verifieer bare referenties met behulp van een voor beeld-app (preview)'
description: In deze zelf studie leert u hoe u verifieer bare referenties kunt verlenen met behulp van onze voor beeld-app en Tenant testen
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: deebaf31197d8b7335f887ae447f05add45278b2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222882"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Zelf studie: aan de slag met Azure Active Directory verifieer bare referenties met behulp van een voor beeld-app (preview)

In deze zelf studie gaan we verder met de stappen die nodig zijn om uw eerste verifieer bare referentie uit te geven: een geverifieerde referentie kaart voor referenties. U kunt deze kaart vervolgens gebruiken om te bewijzen dat u een gecontroleerde referentie expert bent die is gemastereerd in de illustratie van digitale referenties. Ga aan de slag met Azure Active Directory verifieer bare referenties met behulp van de voor beeld-app verifieer bare referenties om uw eerste verifieer bare referentie uit te geven.

![Dit is een afbeelding van een voorbeeld kaart](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- [NodeJS](https://nodejs.org/en/download/) versie 10,14 of hoger is op het test systeem geïnstalleerd.
- U moet [Git](https://git-scm.com/downloads) geïnstalleerd hebben als u de opslag plaats wilt klonen die als host fungeert voor de voor beeld-app,
- [Visual Studio Code](https://code.visualstudio.com/Download)
- Een systeem om onze voorbeeld site te hosten.
- Een mobiel apparaat met Microsoft Authenticator versie 6.2005.3599 of hoger geïnstalleerd.
- [NGROK](https://ngrok.com/) gratis.

## <a name="download-the-sample-code"></a>De voorbeeld code downloaden

Als u zelf een geverifieerde referentie kaart voor referenties wilt uitgeven, moet u een website op uw lokale computer uitvoeren. De website wordt gebruikt om een verifieerbaar aanmeldings proces voor referenties te initiëren. We hebben een eenvoudige website die is geschreven in NodeJS, die in deze zelf studie wordt gebruikt.

Down load eerst onze voorbeeld code [uit github of](https://github.com/Azure-Samples/active-directory-verifiable-credentials)kloon de opslag plaats naar uw lokale computer:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Misschien wilt u de code in de voor beeld-websites leren kennen. De `issuer` map bevat alle code die wordt gebruikt voor het uitgeven van een verifieer bare referentie. Meer informatie vindt u in het Leesmij- [bestand](https://github.com/Azure-Samples/active-directory-verifiable-credentials)van het voor beeld.

## <a name="run-the-issuer-website"></a>De website van de uitgever uitvoeren

U kunt de stappen uitvoeren vanuit Visual Studio code of een opdracht regel die beschikbaar is in uw besturings systeem. 

1. Navigeer naar de map `issuer`. 

    ```terminal
    cd issuer
    ```

2. Zodra u alle vereiste pakketten hebt geïnstalleerd, moet u de site starten.

   ```terminal
    npm install
    node app.js
    ```

3. In de terminal ziet u nu dat de uitgever van de app luistert op poort 8081. We gaan nu een omgekeerde proxy instellen met Ngrok zodat de verificator met uw app kan communiceren. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Een reverse proxy maken met Ngrok

Wanneer u de voorbeeld website uitvoert, moet uw apparaat communiceren met de knooppunt server die wordt uitgevoerd op uw lokale computer. We raden u aan [ngrok](https://ngrok.com/) te gebruiken als een eenvoudige manier om uw lokale ontwikkel server beschikbaar te maken via internet.

1.  Nadat u **ngrok** hebt gedownload en uitgepakt, moeten we het volgende uitvoeren:

    ```terminal
     ngrok http 8081
    ```

De voorbeeld website wordt standaard uitgevoerd op poort `8081` . **Ngrok** uitvoer twee doorstuur-url's voor uw server. Kopieer de URL met het `https://` voor voegsel.

![ngrok helpt u bij het beschikbaar maken van de eind punten van uw toepassing via Internet](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Als u Power shell gebruikt, moet u mogelijk typen om `./ngrok` de opdracht te herkennen.

Nu de lokale poort wordt weer gegeven op internet met behulp van ngrok, gebruikt de voorbeeld site automatisch de hostnaam die is gegenereerd door ngrok. Open uw browser en navigeer naar de ngrok https forwarding-URL. De start pagina van de voorbeeld site kan nu worden bezocht. Als de pagina wordt geopend, kan het apparaat communiceren met de voor beeld-app die wordt uitgevoerd op de lokale server. U bent nu klaar om uzelf een geverifieerde referentie kaart voor referenties te verlenen.

## <a name="issue-a-credential"></a>Een referentie uitgeven

1. Installeer verificator op uw mobiele apparaat. Microsoft Authenticator wordt gebruikt om uw verifieer bare referenties te ontvangen, op te slaan en te presen teren aan de betrokken partijen.

2. Vervolgens moet u zichzelf een verifieer bare referentie verlenen. **Klik op** de knop **referentie ophalen** . Wanneer u op de knop **referentie ophalen** klikt, wordt in de voorbeeld website een QR-code weer gegeven, die u met behulp van verificator kunt scannen. Als u de site bekijkt vanuit de browser op uw mobiele apparaat, wordt door te klikken op de knop **referentie ophalen** een diepe koppeling geactiveerd waarmee de verificator-app wordt geopend. het is niet nodig om een QR-code te scannen.

   ![Knop referentie ophalen](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Scan de QR-code van de website met behulp van verificator of als u via een mobiel toegang hebt tot de website, klikt u op de knop referentie ophalen om de diepte koppeling te activeren. 

   ![QR-code ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. U ziet dat de knop **toevoegen** op dit moment grijs wordt weer gegeven. Kies **Aanmelden bij uw account** onder de kaart afbeelding.

   ![Aanmelden ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Voordat u uw referentie kaart voor referenties krijgt, moet u voor de Tenant die u gebruikt verificatie gegevens opgeven. Als dit de eerste keer is dat u zelf studie volgt, hebt u geen account voor referentie expert en maakt u een nieuw gebruikers account in onze B2C-Tenant.

   ![verifiëren voordat u doorgaat](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Nadat u bent aangemeld, wordt de knop **toevoegen** niet meer grijs weer gegeven. Kies **toevoegen** om uw nieuwe VC te accepteren.

   ![Kies toevoegen na verificatie](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Gefeliciteerd U hebt nu een gecontroleerde referentie expert VC.

   ![Referentie expert VC toegevoegd](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Daarna is het tijd om uw referentie te controleren.

## <a name="validate-credentials"></a>Referenties valideren

Nu u het gedeelte voor de uitgifte van de zelf studie hebt voltooid en u een verifieer bare referentie in verificator hebt, is het tijd om deze in uw eigen Verifier-app te valideren.

1.  Stop met het uitvoeren van de ngrok-service van de verlener.

    ```terminal
     control+c
    ```


2. Open in een ander Terminal venster de map Verifier app en voer deze op dezelfde manier uit als de uitgever van de app.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Voer nu ngrok uit met de Verifier-poort 8082.

    ```terminal
    ngrok http 8082
    ```

4. Open de ngrok https forwarding-URL in uw browser en tik op de knop **referenties verifiëren** .  

   ![knop voor ervaren referenties verifiëren](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Open verificator en scan de QR-code.

   ![QR-code scannen om referentie te verifiëren](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > Op iOS is het de rechter bovenhoek en op Android de rechter benedenhoek. Scan de QR-code.

6. Kies **toestaan** op het scherm nieuwe machtigings aanvraag in verificator. Door op toestaan te klikken, kunt u een Controleer bare presentatie ondertekenen met uw gecentraliseerde id, zodat u zeker weet dat u deze verifieer bare referentie wilt controleren.

   ![nieuwe machtigings aanvraag](media/get-started-verifiable-credentials/new-permission-request.png)

    Nadat een geslaagde presentatie drie dingen zou moeten zijn bijgewerkt:

   1. Op de webpagina wordt nu "Gefeliciteerd, uw naam" + is een geverifieerde referentie expert! "weer gegeven.
   
    ![Gefeliciteerd, bevestig opnieuw](media/get-started-verifiable-credentials/congratulations.png)


   2. De Verifier-app-terminal moet ook hetzelfde bericht in de logboeken weer geven.
   
    ![toepassings activiteit in de opdracht prompt](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. In verificator moet er een vermelding voor recente activiteiten van deze presentatie zijn.

    ![Activiteit in verificator](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Tijdens het uitvoeren van de Verifier-app werkt ngrok mogelijk niet meer en wordt er een fout weer gegeven dat er te veel verbindingen zijn. Dit kan worden vermeden door uw account te registreren bij ngrok. 

## <a name="next-steps"></a>Volgende stappen

Nu u de Snelstartgids hebt voltooid, is het tijd om uw eigen gedecentraliseerde id te maken in de Azure AD-verificatie service voor geverifieerde gegevens en uw eigen verifieer bare referenties te verlenen.

>[!div class="nextstepaction"] 
>[Uw eigen verlener configureren met behulp van de voor beeld-app verifieer bare referenties](./enable-your-tenant-verifiable-credentials.md)
