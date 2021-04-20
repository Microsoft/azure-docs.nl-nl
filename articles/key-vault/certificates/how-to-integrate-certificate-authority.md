---
title: Key Vault integreren met DigiCert-certificeringsinstantie
description: Key Vault integreren met DigiCert-certificeringsinstantie
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 4351526c77961856b118bdeae07cecf48340f5fe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749308"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Key Vault integreren met DigiCert-certificeringsinstantie

Met Azure Key Vault kunt u eenvoudig digitale certificaten voor uw netwerk inrichten, beheren en implementeren en beveiligde communicatie mogelijk maken voor toepassingen. Een digitaal certificaat is een elektronische referentie voor het vaststellen van de identiteit in een elektronische transactie. 

Gebruikers van Azure Key Vault kunnen DigiCert-certificaten rechtstreeks genereren vanuit hun Key Vault. Key Vault zorgt voor end-to-end levenscyclusbeheer van certificaten voor de certificaten die zijn uitgegeven door DigiCert via de vertrouwde amenwerking van Key Vault met de DigiCert-certificeringsinstantie.

Zie [Azure Key Vault-certificaten](./about-certificates.md) voor algemene informatie over certificaten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om deze handleiding te voltooien.
* Een sleutelkluis. U kunt een bestaande sleutelkluis gebruiken of een nieuwe maken door de stappen in een van deze quickstarts te volgen:
   - [Een sleutelkluis maken met de Azure CLI](../general/quick-create-cli.md)
   - [Een sleutelkluis maken met Azure PowerShell](../general/quick-create-powershell.md)
   - [Een sleutelkluis maken met de Azure-portal](../general/quick-create-portal.md).
*   U moet het DigiCert CertCentral-account activeren. [Registreer u](https://www.digicert.com/account/signup/) voor uw CertCentral-account.
*   Beheerdersmachtigingen in uw accounts.


### <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u over de volgende informatie beschikt voor uw DigiCert CertCentral-account:
-   Id van CertCentral-account
-   Organisatie-id
-   API-sleutel

## <a name="adding-certificate-authority-in-key-vault"></a>Certificeringsinstantie toevoegen in Key Vault 
Nadat u de bovenstaande gegevens van het DigiCert CertCentral-account hebt verzameld, kunt u nu DigiCert toevoegen aan de lijst met certificeringsinstanties in de sleutelkluis.

### <a name="azure-portal"></a>Azure Portal

1.  Als u de DigiCert-certificeringsinstantie wilt toevoegen, gaat u naar de sleutelkluis waaraan u DigiCert wilt toevoegen. 
2.  Selecteer op de eigenschappenpagina's van de sleutelkluis **Certificaten**.
3.  Selecteer het tabblad **Certificerings instanties**. ![certificeringsinstanties selecteren](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Selecteer de optie **Toevoegen**.
 ![certificeringsinstanties toevoegen](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  Kies in het scherm **Een certificeringsinstantie maken** de volgende waarden:
    -   **Naam**: Voeg een herkenbare naam van een certificaatverlener toe. Voorbeeld van DigicertCA
    -   **Provider**: Selecteer DigiCert in het menu.
    -   **Account-id**: Voer de id van uw DigiCert CertCentral-account in
    -   **Accountwachtwoord**: Voer de API-sleutel in die u hebt gegenereerd in uw DigiCert CertCentral-account
    -   **Organisatie-id**: Voer OrgID in, opgehaald uit het DigiCert CertCentral-account 
    -   Klik op **Create**.
   
6.  U ziet dat DigicertCA nu is toegevoegd in de lijst met certificeringsinstanties.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Azure host Azure Cloud Shell, een interactieve shell-omgeving die u via uw Azure-portal kunt gebruiken in de browser zelf.

Als u ervoor kiest om PowerShell lokaal te installeren en gebruiken, is versie 1.0.0 of hoger van de Azure PowerShell-module vereist voor deze zelfstudie. Typ `$PSVersionTable.PSVersion` om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Een **resourcegroep** maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Een **sleutelkluis** maken

U moet een unieke naam opgeven voor uw sleutelkluis. Hier is 'Contoso-Vaultname' de naam voor de sleutelkluis in deze handleiding.

- **Kluisnaam** Contoso-Vaultname.
- **Naam van resourcegroep** ContosoResourceGroup.
- **Locatie** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Definieer variabelen voor informatie die is verzameld via het DigiCert CertCentral-account.

- Definieer de variabele **Account-id**
- Definieer de variabele **Organisatie-id**
- Definieer de variabele **API-sleutel**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Stel **Certificaatverlener** in. Hiermee wordt Digicert toegevoegd als een certificeringsinstantie in de sleutelkluis. Meer informatie over de parameters [vindt u hier](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Beleid instellen voor het certificaat en het certificaat verlenen** vanuit DigiCert rechtstreeks in Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Het certificaat is nu verleend door de Digicert-certificeringsinstantie in de opgegeven sleutelkluis door middel van deze integratie.

## <a name="troubleshoot"></a>Problemen oplossen

Als het certificaat is uitgegeven in de status 'uitgeschakeld' in de Azure-portal, gaat u door met het weergeven van de **Certificaatbewerking** om het DigiCert-foutbericht voor dat certificaat te bekijken.

 ![Certificaatbewerking](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Foutbericht: 'Voer een samenvoegingsbewerking uit om deze aanvraag voor een certificaat te voltooien'.
U moet de CSR die door de CA is ondertekend, samenvoegen om deze aanvraag te voltooien. Klik [hier](./create-certificate-signing-request.md) voor meer informatie

Raadpleeg de [Certificaatbewerkingen in de Key Vault REST API-referentie](/rest/api/keyvault) voor meer informatie. Raadpleeg [Kluizen: maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [Kluizen: toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy) voor meer informatie over het instellen van machtigingen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

- Kan ik een Digicert-certificaat met jokerteken genereren met behulp van KeyVault? 
   Ja. Dit is afhankelijk van hoe u uw Digicert-account hebt geconfigureerd.
- Hoe kan ik een **OV-SSL- of EV-SSL-** certificaat maken met DigiCert? 
   Key Vault biedt ondersteuning voor het maken van OV- en EV SSL-certificaten. Wanneer u een certificaat maakt, klikt u op Geavanceerde beleidsconfiguratie en geeft u vervolgens het certificaattype op. Ondersteunde waarden zijn: OV-SSL, EV-SSL
   
   U kunt dit type certificaat in Key Vault maken als uw Digicert-account dit toestaat. Voor dit type certificaat wordt de validatie uitgevoerd door DigiCert. Als de validatie mislukt, kan het ondersteuningsteam van DigiCert u het beste helpen bij dit probleem. Wanneer u een certificaat maakt, kunt u aanvullende informatie toevoegen door deze in subjectName te definiëren.

Voorbeeld
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Zit er een vertraging in het maken van het Digicert-certificaat via integratie versus het rechtstreeks verkrijgen van het certificaat via Digicert?
   Nee. Als er een certificaat wordt gemaakt, kan het verificatieproces tijd kosten. Deze verificatie is afhankelijk van het proces dat door DigiCert wordt gevolgd.


## <a name="next-steps"></a>Volgende stappen

- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)