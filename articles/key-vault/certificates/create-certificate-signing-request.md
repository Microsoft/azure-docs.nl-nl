---
title: Een CSR in Azure Key Vault maken en samenvoegen
description: Meer informatie over het maken en samenvoegen van een CSR in Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: aa631f4c505200c2c8abc67d4e22ffbab23e015c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789024"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Een CSR in Key Vault maken en samenvoegen

Azure Key Vault ondersteunt het opslaan van digitale certificaten die zijn uitgegeven door een certificeringsinstantie. Het biedt ondersteuning voor het maken van een aanvraag voor certificaat ondertekening (CSR) met een privé/openbaar sleutelpaar. De CSR kan worden ondertekend door elke CA (een interne ondernemings-CA of een externe openbare CA). Een CSR is een bericht dat u naar een certificeringsinstantie verzendt om een digitaal certificaat aan te vragen.

Zie [Azure Key Vault-certificaten](./about-certificates.md) voor meer algemene informatie over certificaten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Certificaten toevoegen aan Key Vault die zijn uitgegeven door partnercertificeringsinstanties

Key Vault is een partnerschap aangegaan met de volgende certificeringsinstanties om het maken van certificaten te vereenvoudigen.

|Provider|Certificaattype|Configuratie-installatie  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault biedt OV- of EV SSL-certificaten met DigiCert| [Integratiehandleiding](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault biedt OV- of EV SSL-certificaten met GlobalSign| [Integratiehandleiding](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Certificaten toevoegen aan Key Vault die zijn uitgegeven door niet-partnercertificeringsinstanties

Volg deze stappen om een certificaat toe te voegen van CA's die geen partner zijn van Key Vault. (GoDaddy is bijvoorbeeld geen vertrouwde Key Vault CA.)

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga naar de sleutelkluis waaraan u het certificaat wilt toevoegen.
1. Selecteer op de eigenschappenpagina **Certificaten**.
1. Selecteer het tabblad **Genereren/importeren**.
1. Kies op het scherm **Een certificaat maken** de volgende waarden:
    - **Methode voor het maken van certificaten**: Genereren.
    - **Naam van het certificaat**: ContosoManualCSRCertificate.
    - **Type certificeringsinstantie (CA)** : Certificaat dat is uitgegeven door een niet-geïntegreerde CA.
    - **Onderwerp**: `"CN=www.contosoHRApp.com"`.
     > [!NOTE]
     > Als u een Relatieve DN-naam (RDN) gebruikt met een komma (,) in de waarde, plaats dan de waarde met het speciale teken tussen dubbele aanhalingstekens. 
     >
     > Voorbeeldvermelding van **Onderwerp**: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > In dit voorbeeld bevat de RDN `OU` een waarde met een komma in de naam. De resulterende uitvoer voor `OU` is **Docs, Contoso**.
1. Selecteer de andere waarden naar wens. Selecteer vervolgens **Maken** om het certificaat toe te voegen aan de lijst **Certificaten**.

    ![Schermopname van de certificaateigenschappen](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Selecteer, in de lijst **Certificaten**, het nieuwe certificaat. De huidige status van het certificaat is **uitgeschakeld** omdat het certificaat nog niet is uitgegeven door de CA.
1. Ga naar tabblad **Certificaatbewerking** en selecteer vervolgens **CSR downloaden**.

   ![Schermafbeelding met de knop CSR downloaden gemarkeerd.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Laat de CA de CSR (.csr) ondertekenen.
1. Nadat de aanvraag is ondertekend, selecteert u **Ondertekende aanvraag samenvoegen** op het tabblad **Certificaatbewerking** om het ondertekende certificaat aan Key Vault toe te voegen.

De certificaataanvraag is nu samengevoegd.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Maak een certificaatbeleid. Omdat de CA in dit scenario gekozen geen partner is, is **IssuerName** ingesteld op **Onbekend** en wordt het certificaat niet door Key Vault ingeschreven of verlengd.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Als u een Relatieve DN-naam (RDN) gebruikt met een komma (,) in de waarde, gebruik dan enkele aanhalingstekens voor de volledige waarde of waardeset en plaats de waarde met het speciale teken tussen dubbele aanhalingstekens. 
     >
     >Voorbeeldvermelding van **Onderwerpnaam**: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. In dit voorbeeld ziet de waarde voor `OU` er als volgt uit **Docs, Contoso**. Deze indeling werkt voor alle waarden die een komma bevatten.
     > 
     > In dit voorbeeld bevat de RDN `OU` een waarde met een komma in de naam. De resulterende uitvoer voor `OU` is **Docs, Contoso**.

1. Maak de CSR.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Laat de CA de CSR ondertekenen. De `$csr.CertificateSigningRequest` is de CSR die de basis gecodeerd heeft voor het certificaat. U kunt deze blob maken en dumpen in de website van de certificaatverlener van de aanvraag. Deze stap verschilt per certificeringsinstantie. Zoek naar de richtlijnen van uw CA voor het uitvoeren van deze stap. U kunt ook hulpprogramma's zoals certreq of openssl gebruiken om de CSR te ondertekenen en het proces voor het genereren van een certificaat te voltooien.

1. Voeg de ondertekende aanvraag samen in Key Vault. Wanneer de certificaat aanvraag is ondertekend, kunt u deze samenvoegen met het eerste persoonlijke/openbare sleutelpaar dat is gemaakt in Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

De certificaataanvraag is nu samengevoegd.

---

## <a name="add-more-information-to-the-csr"></a>Meer informatie toevoegen aan CSR

Als u meer informatie wilt toevoegen tijdens het maken van de CSR, definieert u dit in **SubjectName**. Mogelijk wilt u informatie toevoegen zoals:
- Land/regio
- Plaats
- Provincie
- Organisatie
- Organisatie-eenheid

Voorbeeld

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Als u een certificaat voor domeinvalidatie (DV) aanvraagt met aanvullende informatie, kan de CA de aanvraag afwijzen als het niet mogelijk is om alle gegevens in de aanvraag te valideren. De aanvullende informatie is mogelijk beter geschikt als u een certificaat voor organisatievalidatie (OV) aanvraagt.

## <a name="faqs"></a>Veelgestelde vragen

- Hoe kan ik mijn CSR controleren of beheren?

     Zie [Het maken van certificaten controleren en beheren](./create-certificate-scenarios.md).

- Wat als ik deze melding krijg: **Fouttype 'De openbare sleutel van het certificaat van de eindentiteit in de opgegeven X.509-certificaatinhoud komt niet overeen met het openbare gedeelte van de opgegeven persoonlijke sleutel. Controleer of het certificaat geldig is'** ?

     Deze fout treedt op als u de CSR niet samenvoegt met dezelfde CSR-aanvraag die u heeft geïnitieerd. Elke nieuwe CSR die u maakt, heeft een persoonlijke sleutel die moet overeenkomen wanneer u de ondertekende aanvraag samenvoegt.

- Wanneer een CSR wordt samengevoegd, wordt dan de gehele keten samengevoegd?

     Ja, de hele keten wordt samengevoegd, op voorwaarde dat de gebruiker het samen te voegen p7b-bestand heeft teruggezet.

- Wat gebeurt er als het uitgegeven certificaat de status 'uitgeschakeld' heeft in de Azure Portal?

     Ga naar het tabblad **Certificaatbewerking** om het foutbericht voor dat certificaat weer te geven.

- Wat gebeurt er als **Fouttype 'De opgegeven objectnaam is geen geldige x500-naam'** ?

     Deze fout kan optreden als **SubjectName** speciale tekens bevat. Bekijk de opmerkingen in de instructies van de Azure Portal en PowerShell.

---

## <a name="next-steps"></a>Volgende stappen

- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
- [Verwijzing van de REST-API van Azure Key Vault](/rest/api/keyvault)
- [Kluizen: maken of bijwerken](/rest/api/keyvault/vaults/createorupdate)
- [Kluizen: toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy)