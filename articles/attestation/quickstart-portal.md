---
title: Azure-Attestation met Azure Portal instellen
description: Het instellen en configureren van een Attestation-provider met behulp van Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ab1e6011a1c127c9ac5a2c7652a4bf458372e1e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733934"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Snelstartgids: Azure Attestation met Azure Portal instellen

Volg de onderstaande stappen voor het beheren van een Attestation-provider met behulp van Azure Portal.

## <a name="attestation-provider"></a>Attestation-provider

### <a name="create-an-attestation-provider"></a>Attestation-provider maken

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>De provider configureren met niet-ondertekende beleids regels

1.  Selecteer in het menu Azure Portal of op de start pagina de optie **een resource maken**
2.  Voer in het zoekvak **Attestation** in
3.  Kies **Microsoft Azure Attestation** in de lijst met resultaten
4.  Kies op de pagina Microsoft Azure attestation de optie **maken**
5.  Geef op de pagina Attestation-provider maken de volgende invoer op:
    
    **Abonnement**: Kies een abonnement
    
    **Resource groep**: Selecteer een bestaande resource groep of kies **nieuwe maken** en voer een naam voor de resource groep in
    
    **Naam**: een unieke naam is vereist

    **Locatie**: Kies een locatie 
    
    **Certificaat bestand voor beleids ondertekening**: certificaat bestand voor beleids handtekening niet uploaden om de provider met niet-ondertekende beleids regels te configureren 
6.  Nadat u de vereiste invoer hebt opgegeven, klikt u op **bekijken + maken** .
7.  Los eventuele validatie problemen op en klik op **maken**.

#### <a name="to-configure-the-provider-with-signed-policies"></a>De provider configureren met ondertekend beleid

1.  Selecteer in het menu Azure Portal of op de start pagina de optie **een resource maken**
2.  Voer in het zoekvak **Attestation** in
3.  Kies **Microsoft Azure Attestation** in de lijst met resultaten
4.  Kies op de pagina Microsoft Azure attestation de optie **maken**
5.  Geef op de pagina Attestation-provider maken de volgende informatie op:
    
    a. **Abonnement**: Kies een abonnement
    
    b. **Resource groep**: Selecteer een bestaande resource groep of kies **nieuwe maken** en voer een naam voor de resource groep in
    
    c. **Naam**: een unieke naam is vereist

    d. **Locatie**: Kies een locatie 
    
    e. **Certificaat bestand voor beleids handtekening**: voor het configureren van de Attestation-provider met beleids handtekening certificaten, upload certificaten bestand. Zie [hier](./policy-signer-examples.md) voor beelden 
6.  Nadat u de vereiste invoer hebt opgegeven, klikt u op **bekijken + maken** .
7.  Los eventuele validatie problemen op en klik op **maken**.

### <a name="view-attestation-provider"></a>Attestation-provider weer geven

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in en selecteer deze

### <a name="delete-attestation-provider"></a>Attestation-provider verwijderen

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Schakel het selectie vakje in en klik op **verwijderen**
4.  Typ Ja en klik op **verwijderen** [of]
1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **verwijderen** in het bovenste menu en klik op **Ja**


## <a name="attestation-policy-signers"></a>Attestation-beleids ondertekeners

### <a name="view-policy-signer-certificates"></a>Certificaten van beleids handtekening weer geven

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **certificaten van beleids handtekening** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik op **certificaten van beleids handtekening downloaden** (de knop wordt uitgeschakeld voor de attest providers die zijn gemaakt zonder vereisten voor beleids ondertekening)
6.  Het gedownloade tekst bestand heeft alle certificaten in een JWS-indeling.
a.  Controleer het aantal certificaten en de gedownloade Certificaats.

### <a name="add-policy-signer-certificate"></a>Certificaat voor beleids handtekening toevoegen

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **certificaten van beleids handtekening** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik op **toevoegen** in het bovenste menu (de knop wordt uitgeschakeld voor de attest providers die zijn gemaakt zonder vereisten voor beleids ondertekening)
6.  Certificaat bestand voor beleids ondertekenaar uploaden en klik op **toevoegen**. Zie [hier](./policy-signer-examples.md) voor beelden

### <a name="delete-policy-signer-certificate"></a>Certificaat van beleids handtekening verwijderen

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **certificaten van beleids handtekening** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik op **verwijderen** in het bovenste menu (de knop wordt uitgeschakeld voor de attest providers die zijn gemaakt zonder vereisten voor beleids ondertekening)
6.  Certificaat bestand van beleids handtekening uploaden en op **verwijderen** klikken. Zie [hier](./policy-signer-examples.md) voor beelden 

## <a name="attestation-policy"></a>Attestation-beleid

### <a name="view-attestation-policy"></a>Attestation-beleid weer geven

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **beleid** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Selecteer het **type voorkeurs attest** en Bekijk het **huidige beleid**

### <a name="configure-attestation-policy"></a>Attestation-beleid configureren

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>Als Attestation-provider is gemaakt zonder vereisten voor beleids ondertekening

##### <a name="upload-policy-in-jwt-format"></a>Beleid uploaden in JWT-indeling

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **beleid** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik in het bovenste menu op **configureren** .
6.  Wanneer de Attestation-provider is gemaakt zonder beleids handtekening vereiste, kan de gebruiker een beleid uploaden in de **JWT** -of **tekst** indeling
7.  Selecteer een **beleids indeling** als **JWT**
8.  Upload het beleids bestand met beleids inhoud in een **niet-ondertekende/ondertekende JWT-** indeling en klik op **Opslaan**. Zie [hier](./policy-examples.md) voor beelden
    
    Voor de optie voor het uploaden van bestanden wordt de beleids voorbeeld weer gegeven in de tekst indeling en de voorbeeld weergave van het beleid kan niet worden bewerkt.

7.  Klik op **vernieuwen** in het bovenste menu om het geconfigureerde beleid weer te geven

##### <a name="upload-policy-in-text-format"></a>Beleid uploaden in tekst indeling

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **beleid** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik in het bovenste menu op **configureren** .
6.  Wanneer de Attestation-provider is gemaakt zonder beleids handtekening vereiste, kan de gebruiker een beleid uploaden in de **JWT** -of **tekst** indeling
7.  Selecteer een **beleids indeling** als **tekst**
8.  Upload het beleids bestand met inhoud in **tekst** indeling of voer beleids inhoud in het tekst gebied in en klik op **Opslaan**. Zie [hier](./policy-examples.md) voor beelden

    Voor de optie voor het uploaden van bestanden wordt de beleids voorbeeld weer gegeven in de tekst indeling en de voorbeeld weergave van het beleid kan niet worden bewerkt.

8.  Klik op **vernieuwen** om het geconfigureerde beleid weer te geven

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>Wanneer Attestation-provider is gemaakt met vereisten voor beleids ondertekening

##### <a name="upload-policy-in-jwt-format"></a>Beleid uploaden in JWT-indeling

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **beleid** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik in het bovenste menu op **configureren** .
6.  Wanneer de Attestation-provider is gemaakt met vereisten voor beleids ondertekening, kan de gebruiker een beleid alleen uploaden in **ondertekende JWT-indeling**
7.  Upload beleids bestand is **ondertekende JWT-indeling** en klik op **Opslaan**. Zie [hier](./policy-examples.md) voor beelden

    Voor de optie voor het uploaden van bestanden wordt de beleids voorbeeld weer gegeven in de tekst indeling en de voorbeeld weergave van het beleid kan niet worden bewerkt.
    
8.  Klik op **vernieuwen** om het geconfigureerde beleid weer te geven

