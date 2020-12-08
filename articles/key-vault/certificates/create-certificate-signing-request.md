---
title: CSR in Azure Key Vault maken en samenvoegen
description: CSR in Azure Key Vault maken en samenvoegen
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 6d66648680aa14baa53372732df52a6c247a0117
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483760"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>CSR in Key Vault maken en samenvoegen

Azure Key Vault ondersteunt het opslaan van digitaal certificaat dat is uitgegeven door een certificeringsinstantie van uw keuze in uw sleutelkluis. Het biedt ondersteuning voor het maken van de aanvraag voor certificaatondertekening met een persoonlijk-openbaar sleutelpaar en kan worden ondertekend door elke gekozen certificeringsinstantie. Dit kan een interne ondernemings-CA of een externe openbare CA zijn. Een aanvraag voor certificaatondertekening (ook CSR of certificerings aanvraag) is een bericht dat door de gebruiker naar een certificeringsinstantie (CA) wordt verzonden om de uitgifte van een digitaal certificaat aan te vragen.

Zie [Azure Key Vault-certificaten](./about-certificates.md) voor meer algemene informatie over certificaten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>Een certificaat toevoegen aan Key Vault dat is uitgegeven door een partnercertificeringsinstantie
Key Vault is een partnerschap aangegaan met de volgende twee certificeringsinstanties om het maken van certificaten te vereenvoudigen. 

|Provider|Certificaattype|Configuratie-installatie  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault biedt OV- of EV SSL-certificaten met DigiCert| [Integratiehandleiding](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault biedt OV- of EV SSL-certificaten met GlobalSign| [Integratiehandleiding](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>Een certificaat toevoegen aan Key Vault dat is uitgegeven door een niet-partnercertificeringsinstantie

De volgende stappen helpen u bij het maken van een certificaat van certificeringsinstanties die niet zijn gekoppeld aan Key Vault (GoDaddy is bijvoorbeeld is geen vertrouwde sleutelkluis-CA) 


### <a name="azure-powershell"></a>Azure PowerShell



1. Begin met **Maak het certificaatbeleid**. Key Vault zal het certificaat van de uitgever niet inschrijven of vernieuwen namens de gebruiker, omdat de CA die in dit scenario is gekozen, niet wordt ondersteund, en daarom is de IssuerName ingesteld op onbekend.

   ```azurepowershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
    
   > [!NOTE]
   > Als u een relatieve DN-naam (RDN) gebruikt met een komma (,) in de waarde, gebruik dan enkele aanhalingstekens en plaats de waarde met het speciale teken tussen dubbele aanhalingstekens. Bijvoorbeeld: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. In dit voorbeeld ziet de waarde voor `OU` er als volgt uit **Docs, Contoso**. Deze indeling werkt voor alle waarden die een komma bevatten.

2. Een **aanvraag voor certificaatondertekening** maken

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Het ophalen van de CSR-**aanvraag die is ondertekend door de CA**. De `$csr.CertificateSigningRequest` is de aanvraag voor het ondertekenen van versleuteld Base4-certificaat voor het certificaat. U kunt deze blob maken en dumpen in de certificaataanvraagwebsite van de verlener. Deze stap is afhankelijk van de certificeringsinstantie. De beste manier is om de richt lijnen van uw CA op te zoeken voor het uitvoeren van deze stap. U kunt ook hulpprogramma's zoals certreq of openssl gebruiken om de certificaataanvraag te ondertekenen en het proces voor het genereren van een certificaat te voltooien.


4. **Het samenvoegen van de ondertekende aanvraag** in Key Vault. Nadat de certificaataanvraag is ondertekend door de uitgever, kunt u het ondertekende certificaat terughalen en samenvoegen met het eerste persoonlijke openbare sleutelpaar dat is gemaakt in Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    De certificaataanvraag is nu succesvol samengevoegd.

### <a name="azure-portal"></a>Azure Portal

1.  Als u CSR wilt genereren voor de CA van uw keuze, gaat u naar de sleutelkluis die u wilt toevoegen van het certificaat.
2.  Selecteer op de eigenschappenpagina's van de sleutelkluis **Certificaten**.
3.  Selecteer tabblad **Genereren/importeren**.
4.  Kies op het scherm **Een certificaat maken** de volgende waarden:
    - **Methode voor het maken van certificaten:** Genereren.
    - **Naam van het certificaat:** ContosoManualCSRCertificate.
    - **Type certificeringsinstantie (CA):** Certificaat dat is uitgegeven door een niet-geïntegreerde certificeringsinstantie
    - **Onderwerp:** `"CN=www.contosoHRApp.com"`
    - Selecteer de andere waarden naar wens. Klik op **Create**.

    ![Certificaateigenschappen](../media/certificates/create-csr-merge-csr/create-certificate.png)  


6.  U ziet dat het certificaat nu is toegevoegd in de lijst Certificaten. Selecteer dit nieuwe certificaat dat u zojuist hebt gemaakt. De huidige status van het certificaat is uitgeschakeld omdat het nog niet is uitgegeven door de CA.
7. Klik op tabblad **Certificaatbewerking** en selecteer **CSR downloaden**.

   ![Schermafbeelding met de knop CSR downloaden gemarkeerd.](../media/certificates/create-csr-merge-csr/download-csr.png)
 
8.  Breng het CSR-bestand naar de certificeringsinstantie om de aanvraag te kunnen ondertekenen.
9.  Zodra de aanvraag is ondertekend door de CA, gaat u terug naar het certificaatbestand om en kiest u **De ondertekende aanvraag samenvoegen** in hetzelfde Certificaatbewerkingsscherm.

De certificaataanvraag is nu samengevoegd.

> [!NOTE]
> Als uw waarden voor de RD-naam komma's bevatten, kunt u ze ook toevoegen aan het veld **Onderwerp** door de waarde tussen dubbele aanhalingstekens te plaatsen, zoals weergegeven in stap 4.
> Voorbeeld van vermelding in 'Onderwerp': `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`In dit voorbeeld bevat de relatieve DN-naam`OU` een waarde met een komma in de naam. De resulterende uitvoer voor `OU` is **Docs, Contoso**.


## <a name="adding-more-information-to-csr"></a>Meer informatie toevoegen aan CSR

Als u meer informatie wilt toevoegen bij het maken van een CSR, bijvoorbeeld: 
    - Land/regio:
    - Plaats/locatie:
    - Provincie:
    - Organisatie:
    - Organisatie-eenheid: Kunt u al deze informatie toevoegen wanneer u een CSR maakt, door deze te definiëren in de onderwerpnaam.

Voorbeeld
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

> [!NOTE]
> Als u een DV-certificaat aanvraagt met al deze details in de CSR, kan de aanvraag worden geweigerd door de CA, omdat deze mogelijk niet alle informatie in de aanvraag kan valideren. Als u een OV-certificaat aanvraagt, is het beter om alle informatie toe te voegen aan de CSR.


## <a name="troubleshoot"></a>Problemen oplossen

- **Fouttype 'De openbare sleutel van het certificaat van de eindentiteit in de opgegeven X.509-certificaatinhoud komt niet overeen met het openbare gedeelte van de opgegeven persoonlijke sleutel. Controleer of het certificaat geldig is.** 'Deze fout kan optreden als u de CSR niet samenvoegt met dezelfde CSR-aanvraag die is geïnitieerd. Telkens als een CSR wordt gemaakt, wordt er een persoonlijke sleutel gemaakt die moet worden vergeleken bij het samenvoegen van de ondertekende aanvraag.
    
- Wanneer CSR wordt samengevoegd, wordt dan de gehele keten samengevoegd?
    Ja, de hele keten wordt samengevoegd, op voorwaarde dat de gebruiker het samen te voegen p7b-bestand heeft teruggezet.

- Als het certificaat is uitgegeven in de status 'uitgeschakeld' in de Azure-portal, gaat u door met het weergeven van de **Certificaatbewerking** om het foutbericht voor dat certificaat te bekijken.

Raadpleeg de [Certificaatbewerkingen in de Key Vault REST API-referentie](/rest/api/keyvault) voor meer informatie. Raadpleeg [Kluizen: maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [Kluizen: toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy) voor meer informatie over het instellen van machtigingen.

- **Fouttype: 'De opgegeven onderwerpnaam is geen geldige X500 naam'** . Deze fout kan optreden als u 'speciale tekens' in de waarden van SubjectName hebt opgenomen. Bekijk de opmerkingen in respectievelijk de instructies in Azure Portal en PowerShell. 

## <a name="next-steps"></a>Volgende stappen

- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
