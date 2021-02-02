---
title: Azure-Attestation met Azure Portal instellen
description: Het instellen en configureren van een Attestation-provider met behulp van Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 86adac557c6de133e95e97bfedbd302cc6a2b27e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429154"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Snelstartgids: Azure Attestation met Azure Portal instellen

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Volg de onderstaande stappen voor het beheren van een Attestation-provider met behulp van Azure Portal.

## <a name="1-attestation-provider"></a>1. Attestation-provider

### <a name="11-create-an-attestation-provider"></a>1,1 een Attestation-provider maken

#### <a name="111-to-configure-the-provider-with-unsigned-policies"></a>1.1.1 de provider configureren met niet-ondertekend beleid

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

#### <a name="112-to-configure-the-provider-with-signed-policies"></a>1.1.2 voor het configureren van de provider met ondertekend beleid

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

### <a name="12-view-attestation-provider"></a>Attestation-provider 1,2-weer gave

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in en selecteer deze

### <a name="13-delete-attestation-provider"></a>1,3 Attestation-provider verwijderen

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Schakel het selectie vakje in en klik op **verwijderen**
4.  Typ Ja en klik op **verwijderen** [of]
1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **verwijderen** in het bovenste menu en klik op **Ja**


## <a name="2-attestation-policy-signers"></a>2. aanmeldingen van attest beleid

### <a name="21-view-policy-signer-certificates"></a>2,1 certificaten van ondertekenaar weer geven

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **certificaten van beleids handtekening** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik op **certificaten van beleids handtekening downloaden** (de knop wordt uitgeschakeld voor de attest providers die zijn gemaakt zonder vereisten voor beleids ondertekening)
6.  Het gedownloade tekst bestand heeft alle certificaten in een JWS-indeling.
a.  Controleer het aantal certificaten en de gedownloade Certificaats.

### <a name="22-add-policy-signer-certificate"></a>2,2 handtekening certificaat voor het beleid toevoegen

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **certificaten van beleids handtekening** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik op **toevoegen** in het bovenste menu (de knop wordt uitgeschakeld voor de attest providers die zijn gemaakt zonder vereisten voor beleids ondertekening)
6.  Certificaat bestand voor beleids ondertekenaar uploaden en klik op **toevoegen**. Zie [hier](./policy-signer-examples.md) voor beelden

### <a name="23-delete-policy-signer-certificate"></a>2,3 handtekening certificaat voor het beleid verwijderen

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **certificaten van beleids handtekening** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Klik op **verwijderen** in het bovenste menu (de knop wordt uitgeschakeld voor de attest providers die zijn gemaakt zonder vereisten voor beleids ondertekening)
6.  Certificaat bestand van beleids handtekening uploaden en op **verwijderen** klikken. Zie [hier](./policy-signer-examples.md) voor beelden 

## <a name="3-attestation-policy"></a>3. Attestation-beleid

### <a name="31-view-attestation-policy"></a>Attestation-beleid 3,1 weer geven

1.  Selecteer in het menu Azure Portal of op de start pagina **alle resources**
2.  Voer in het vak Filter de naam van de Attestation-provider in
3.  Selecteer de Attestation-provider en ga naar de overzichts pagina
4.  Klik op **beleid** in het resource menu aan de linkerkant of in het onderste deel venster
5.  Selecteer het **type voorkeurs attest** en Bekijk het **huidige beleid**

### <a name="32-configure-attestation-policy"></a>3,2 Attestation-beleid configureren

#### <a name="321-when-attestation-provider-is-created-without-policy-signing-requirement"></a>3.2.1 wanneer Attestation-provider is gemaakt zonder vereisten voor beleids ondertekening

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

#### <a name="322-when-attestation-provider-is-created-with-policy-signing-requirement"></a>3.2.2 wanneer Attestation-provider is gemaakt met vereisten voor beleids ondertekening

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

## <a name="next-steps"></a>Volgende stappen

- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Een SGX-enclave-attest verklaren met codevoorbeelden](/samples/browse/?expanded=azure&terms=attestation)

