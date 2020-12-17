---
title: 'Zelfstudie: De gebruikersinterface aanpassen'
titleSuffix: Azure AD B2C
description: Volg deze zelfstudie voor meer informatie over hoe u de gebruikersinterface (UI) van uw toepassingen in Azure Active Directory B2C kunt aanpassen met behulp van Azure Portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 036e9e6bf58d837c698a6092770ef581f77bc20c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108243"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Zelfstudie: De gebruikersinterface in Azure Active Directory B2C aanpassen

Voor veelvoorkomende gebruikersacties, zoals registreren, aanmelden en het profiel bewerken, kunt u in Azure Active Directory B2C (Azure AD B2C) [gebruikersstromen](user-flow-overview.md) toepassen. Aan de hand van de informatie in deze zelfstudie leert u hoe u [de gebruikersinterface (UI) voor dergelijke acties aanpast](customize-ui-with-html.md) met behulp van uw eigen HTML- en CSS-bestanden.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Bestanden voor UI-aanpassing maken
> * De gebruikersstroom zodanig bijwerken dat deze bestanden worden gebruikt
> * De aangepaste gebruikersinterface testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikersstroom](tutorial-create-user-flows.md), zodat gebruikers zich bij uw toepassing kunnen registreren en aanmelden.

## <a name="create-customization-files"></a>Aanpassingsbestanden maken

U maakt een Azure-opslagaccount en -container en plaatst vervolgens eenvoudige HTML- en CSS-bestanden in de container.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Hoewel u uw bestanden op veel verschillende manieren kunt opslaan, slaat u ze ditmaal op in [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure-abonnement bevat. Selecteer in het bovenste menu het filter **Map + abonnement** en kies de map waarin uw abonnement zich bevindt. Dit is een andere map dan de map met uw Azure B2C-tenant.
3. Kies linksboven in Azure Portal op Alle services, zoek **Opslagaccounts** en selecteer deze optie.
4. Selecteer **Toevoegen**.
5. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**, geef een naam op voor de nieuwe resourcegroep en klik vervolgens op **OK**.
6. Voer een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn in Azure, moet tussen de 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten.
7. Selecteer de locatie van het opslagaccount of accepteer de standaardlocatie.
8. Accepteer alle andere standaardwaarden, selecteer **Beoordelen en maken** en klik vervolgens op **Maken**.
9. Nadat het opslagaccount is gemaakt, selecteert u **Naar de resource gaan**.

### <a name="create-a-container"></a>Een container maken

1. Selecteer op de overzichtspagina van het opslagaccount de optie **Blobs**.
2. Selecteer **Container**, geef een naam op voor de container, kies **Blob (alleen anonieme leestoegang voor blobs)** en klik vervolgens op **OK**.

### <a name="enable-cors"></a>CORS inschakelen

 In browsers maakt Azure AD B2C-code gebruik van een moderne en gestandaardiseerde benadering om aangepaste inhoud te laden vanaf een URL die u in een gebruikersstroom opgeeft. CORS (Cross-Origin Resource Sharing) maakt het mogelijk om vanuit andere domeinen beperkte resources op een webpagina aan te vragen.

1. Selecteer in het menu de optie **CORS**.
2. Geef `https://your-tenant-name.b2clogin.com` op bij **Toegestane oorsprongen**. Vervang `your-tenant-name` door de naam van uw Azure AD B2C-tenant. Bijvoorbeeld `https://fabrikam.b2clogin.com`. Gebruik bij het opgeven van de naam van uw tenant alleen kleine letters.
3. Selecteer bij **Toegestane methoden** de opties `GET`, `PUT` en `OPTIONS`.
4. Voer bij **Toegestane headers** een asterisk (*) in.
5. Voer bij **Zichtbare headers** een asterisk (*) in.
6. Voer bij **Maximumleeftijd** 200 in.

    ![De CORS-configuratiepagina in Azure Blob Storage in Azure Portal](./media/tutorial-customize-ui/enable-cors.png)

5. Klik op **Opslaan**.

### <a name="create-the-customization-files"></a>De aanpassingsbestanden maken

U maakt nu eerst een eenvoudig HTML- en CSS-bestand om de gebruikersinterface van de registratiepagina aan te passen. U kunt de HTML-code op elke gewenste manier configureren, als deze maar een **div**-element met de id `api`bevat. Bijvoorbeeld `<div id="api"></div>`. Azure AD B2C injecteert elementen in de `api`-container wanneer de pagina wordt weergegeven.

1. Maak in een lokale map het volgende bestand. Zorg er hierbij voor dat u `your-storage-account` wijzigt in de naam van het opslagaccount en `your-container` in de naam van de container die u hebt gemaakt. Bijvoorbeeld `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    De pagina kan op elke gewenste manier worden ontworpen, maar het div-element **api** is vereist in elk HTML-aanpassingsbestand dat u maakt.

3. Sla het bestand op als *custom-ui.html*.
4. Maak de volgende eenvoudige CSS waarmee alle elementen op de registratie- of aanmeldingspagina worden gecentreerd, waaronder de elementen die Azure AD B2C injecteert.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Sla het bestand op als *style.css*.

### <a name="upload-the-customization-files"></a>De aanpassingsbestanden uploaden

In deze zelfstudie slaat u de bestanden die u hebt gemaakt, op in het opslagaccount, zodat Azure AD B2C er toegang toe heeft.

1. Kies linksboven in Azure Portal **Alle services**, zoek **Opslagaccounts** en selecteer deze optie.
2. Selecteer achtereenvolgens het opslagaccount dat u hebt gemaakt, de optie **Blobs** en de container die u hebt gemaakt.
3. Selecteer **Uploaden**, navigeer naar het bestand *custom-ui.html*, selecteer dit en klik vervolgens op **Uploaden**.

    ![De pagina voor blobupload in de portal met de knop Uploaden en de bestanden gemarkeerd](./media/tutorial-customize-ui/upload-blob.png)

4. Kopieer de URL van het geüploade bestand voor gebruik verderop in de zelfstudie.
5. Herhaal stap 3 en 4 voor het bestand *style.css*.

## <a name="update-the-user-flow"></a>De gebruikersstroom bijwerken

1. Kies linksboven in Azure Portal **Alle services**, zoek **Azure AD B2C** en selecteer dit.
2. Selecteer **Gebruikersstromen** en vervolgens de gebruikersstroom *B2C_1_signupsignin1*.
3. Selecteer **Pagina-indelingen** en klik vervolgens bij **Gecombineerde pagina voor registratie en aanmelding** op **Ja** voor **Aangepaste pagina-inhoud gebruiken**.
4. Geef in **URI voor aangepaste pagina** de URI op voor het eerder gemaakte bestand *custom-ui.html*.
5. Klik bovenaan de pagina op **Opslaan**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer in uw Azure AD B2C-tenant de optie **Gebruikersstromen** en vervolgens de gebruikersstroom *B2C_1_signupsignin1*.
2. Klik bovenaan de pagina op **Gebruikersstroom uitvoeren**.
3. Klik op de knop **Gebruikersstroom uitvoeren**.

    ![De pagina Gebruikersstroom uitvoeren voor de registratie- of aanmeldingsgebruikersstroom](./media/tutorial-customize-ui/run-user-flow.png)

    Als het goed is, ziet u een pagina die lijkt op het volgende voorbeeld, met de elementen gecentreerd op basis van het CSS-bestand dat u hebt gemaakt:

    ![Webbrowser met een registratie- of aanmeldingspagina met aangepaste UI-elementen](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Bestanden voor UI-aanpassing maken
> * De gebruikersstroom zodanig bijwerken dat deze bestanden worden gebruikt
> * De aangepaste gebruikersinterface testen

> [!div class="nextstepaction"]
> [De gebruikersinterface in Azure Active Directory B2C aanpassen](customize-ui-with-html.md)
