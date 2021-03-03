---
title: 'Snelstartgids: Azure-Attestation instellen met behulp van de Azure Portal'
description: In deze Quick Start leert u hoe u een Attestation-provider kunt instellen en configureren met behulp van de Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d1310e3c4b4a56a27219cce613e8f6109d32c8c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729385"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Snelstartgids: Azure-Attestation instellen met behulp van de Azure Portal

Volg deze Snelstartgids om aan de slag te gaan met Azure Attestation. Meer informatie over het beheren van een Attestation-provider, een beleids handtekening en een beleid met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="attestation-provider"></a>Attestation-provider

In deze sectie maakt u een Attestation-provider en configureert u deze met niet-ondertekend beleid of een ondertekend beleid. U leert ook hoe u de Attestation-provider kunt weer geven en verwijderen.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>De provider met niet-ondertekende beleids regels maken en configureren

1. Ga naar het menu Azure Portal of de start pagina en selecteer **een resource maken**.
1. Voer **Attestation** in het zoekvak in.
1. Selecteer **Microsoft Azure Attestation** in de lijst met resultaten.
1. Op de pagina **attestation Microsoft Azure** selecteert u **maken**.
1. Geef op de pagina **Attestation-provider maken** de volgende invoer op:

   - **Abonnement**: Kies een abonnement.
   - **Resource groep**: Selecteer een bestaande resource groep of selecteer **nieuwe maken** en voer een naam voor de resource groep in.
   - **Naam**: Voer een unieke naam in.
   - **Locatie**: Kies een locatie.
   - **Certificaat bestand voor beleids handtekening**: upload het certificaat bestand van het beleids handtekening niet om de provider met niet-ondertekende beleids regels te configureren.

1. Nadat u de vereiste invoer hebt opgegeven, selecteert u **controleren + maken**.
1. Als er validatie problemen zijn, corrigeert u deze en selecteert u **maken**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>De provider maken en configureren met ondertekend beleid

1. Ga naar het menu Azure Portal of de start pagina en selecteer **een resource maken**.
1. Voer **Attestation** in het zoekvak in.
1. Selecteer **Microsoft Azure Attestation** in de lijst met resultaten.
1. Op de pagina **attestation Microsoft Azure** selecteert u **maken**.
1. Geef op de pagina **Attestation-provider maken** de volgende informatie op:

   - **Abonnement**: Kies een abonnement.
   - **Resource groep**: Selecteer een bestaande resource groep of selecteer **nieuwe maken** en voer een naam voor de resource groep in.
   - **Naam**: Voer een unieke naam in.
   - **Locatie**: Kies een locatie.
   - **Certificaat bestand voor beleids handtekening**: upload het certificaat bestand van het beleids handtekening om de Attestation-provider te configureren met ondertekend beleid. [Zie voor beelden van beleids handtekening certificaten](./policy-signer-examples.md).

1. Nadat u de vereiste invoer hebt opgegeven, selecteert u **controleren + maken**.
1. Als er validatie problemen zijn, corrigeert u deze en selecteert u **maken**.

### <a name="view-the-attestation-provider"></a>De Attestation-provider weer geven

1. Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1. Voer in het vak Filter de naam van de Attestation-provider in en selecteer deze.

### <a name="delete-the-attestation-provider"></a>De Attestation-provider verwijderen

Er zijn twee manieren om de Attestation-provider te verwijderen. U kunt:

1. Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1. Voer in het vak Filter de naam van de Attestation-provider in.
1. Schakel het selectie vakje in en selecteer **verwijderen**.
1. Voer **Ja** in en selecteer **verwijderen**.

U kunt ook het volgende doen:

1. Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1. Voer in het vak Filter de naam van de Attestation-provider in.
1. Selecteer de Attestation-provider en ga naar de pagina overzicht.
1. Selecteer **verwijderen** in de menu balk en selecteer **Ja**.

## <a name="attestation-policy-signers"></a>Attestation-beleids ondertekeners

Volg de stappen in deze sectie om beleids handtekening certificaten te bekijken, toe te voegen en te verwijderen.

### <a name="view-the-policy-signer-certificates"></a>Certificaten van de beleids handtekening weer geven

1. Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1. Voer in het vak Filter de naam van de Attestation-provider in.
1. Selecteer de Attestation-provider en ga naar de pagina overzicht.
1. Selecteer **beleids handtekening certificaten** in het menu resource aan de linkerkant van het venster of in het onderste deel venster. U ziet een prompt om certificaat voor verificatie te selecteren. Kies de juiste optie om door te gaan.
1. Selecteer **certificaat voor beleids handtekening downloaden**. De knop wordt uitgeschakeld voor Attestation-providers die zijn gemaakt zonder de vereiste voor beleids ondertekening.
1. Het gedownloade tekst bestand heeft alle certificaten in een JWS-indeling.
1. Controleer het aantal certificaten en de gedownloade certificaten.

### <a name="add-the-policy-signer-certificate"></a>Het certificaat voor het ondertekenaar van het beleid toevoegen

1.  Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1.  Voer in het vak Filter de naam van de Attestation-provider in.
1.  Selecteer de Attestation-provider en ga naar de pagina overzicht.
1.  Selecteer **beleids handtekening certificaten** in het menu resource aan de linkerkant van het venster of in het onderste deel venster.
1.  Selecteer **toevoegen** in het bovenste menu. De knop wordt uitgeschakeld voor Attestation-providers die zijn gemaakt zonder de vereiste voor beleids ondertekening.
1.  Upload het certificaat bestand voor het ondertekenaar van het beleid en selecteer **toevoegen**. [Zie voor beelden van beleids handtekening certificaten](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>De certificaten voor het ondertekenaar van het beleid verwijderen

1.  Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1.  Voer in het vak Filter de naam van de Attestation-provider in.
1.  Selecteer de Attestation-provider en ga naar de pagina overzicht.
1.  Selecteer **beleids handtekening certificaten** in het menu resource aan de linkerkant van het venster of in het onderste deel venster.
1.  Selecteer **verwijderen** in het bovenste menu. De knop wordt uitgeschakeld voor Attestation-providers die zijn gemaakt zonder de vereiste voor beleids ondertekening.
1.  Upload het certificaat bestand voor het ondertekenaar van het beleid en selecteer **verwijderen**. [Zie voor beelden van beleids handtekening certificaten](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Attestation-beleid

In deze sectie wordt beschreven hoe u een Attestation-beleid bekijkt en beleids regels configureert die zijn gemaakt met en zonder een vereiste voor beleids ondertekening.

### <a name="view-an-attestation-policy"></a>Een Attestation-beleid weer geven

1.  Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1.  Voer in het vak Filter de naam van de Attestation-provider in.
1.  Selecteer de Attestation-provider en ga naar de pagina overzicht.
1.  Selecteer **beleid** in het menu resource aan de linkerkant van het venster of in het onderste deel venster. U ziet een prompt om certificaat voor verificatie te selecteren. Kies de juiste optie om door te gaan.
1.  Selecteer het **type voorkeurs attest** en Bekijk het **huidige beleid**.

### <a name="configure-an-attestation-policy"></a>Een Attestation-beleid configureren

Volg deze stappen voor het uploaden van een beleid in de JWT-of tekst indeling als de Attestation-provider is gemaakt zonder een vereiste voor beleids ondertekening.

1. Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1. Voer in het vak Filter de naam van de Attestation-provider in.
1. Selecteer de Attestation-provider en ga naar de pagina overzicht.
1. Selecteer **beleid** in het menu resource aan de linkerkant van het venster of in het onderste deel venster.
1. Selecteer **configureren** in het bovenste menu.
1. Selecteer een **beleids indeling** als **JWT** of als **tekst**.

   Als de Attestation-provider is gemaakt zonder beleids handtekening vereiste, kan de gebruiker een beleid uploaden in een **JWT** -of **tekst** -indeling.

      - Als u de JWT-indeling hebt gekozen, uploadt u het beleids bestand met de inhoud van het beleid in **niet-ondertekende/ondertekende JWT-** indeling en selecteert u **Opslaan**. [Zie de beleids voorbeelden](./policy-examples.md).
      - Als u tekst indeling hebt gekozen, uploadt u het beleids bestand met de inhoud in **tekst** indeling of voert u de beleids inhoud in het tekst gebied in en selecteert u **Opslaan**. [Zie de beleids voorbeelden](./policy-examples.md).

   Voor de optie voor het uploaden van bestanden wordt de voor beeld van het beleid weer gegeven in de tekst indeling en kan het niet worden bewerkt.

1. Selecteer **vernieuwen** in het bovenste menu om het geconfigureerde beleid weer te geven.


Als de Attestation-provider is gemaakt met een vereiste voor beleids ondertekening, voert u de volgende stappen uit om een beleid te uploaden in JWT-indeling.

1.  Ga naar het menu Azure Portal of de start pagina en selecteer **alle resources**.
1.  Voer in het vak Filter de naam van de Attestation-provider in.
1.  Selecteer de Attestation-provider en ga naar de pagina overzicht.
1.  Selecteer **beleid** in het menu resource aan de linkerkant van het venster of in het onderste deel venster.
1.  Selecteer **configureren** in het bovenste menu.
1.  Upload het beleids bestand in **ondertekende JWT-indeling** en selecteer **Opslaan**. [Zie de beleids voorbeelden](./policy-examples.md).

    Als de Attestation-provider is gemaakt met een beleids handtekening vereiste, kan de gebruiker een beleid alleen uploaden in **ondertekende JWT-indeling**.

    Voor de optie voor het uploaden van bestanden wordt de voor beeld van het beleid weer gegeven in de tekst indeling en kan het niet worden bewerkt.
    
1.  Selecteer **vernieuwen** om het geconfigureerde beleid weer te geven.

## <a name="next-steps"></a>Volgende stappen

- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Een SGX-enclave-attest verklaren met codevoorbeelden](/samples/browse/?expanded=azure&terms=attestation)

