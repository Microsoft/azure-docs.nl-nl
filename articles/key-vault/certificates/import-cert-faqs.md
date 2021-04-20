---
title: Veelgestelde vragen - Azure Key Vault importeren van certificaten
description: Krijg antwoorden op veelgestelde vragen over het importeren van Azure Key Vault certificaten.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 39fe4e77d701f8e9311ea343c88eb3b905496680
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749236"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Veelgestelde vragen Azure Key Vault over het importeren van certificaten

In dit artikel vindt u antwoorden op veelgestelde vragen over het importeren Azure Key Vault certificaten.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hoe kan ik een certificaat importeren in Azure Key Vault?

Voor een certificaatimportbewerking accepteert Azure Key Vault twee certificaatbestandsindelingen: PEM en PFX. Hoewel er PEM-bestanden zijn met alleen het openbare gedeelte, Key Vault alleen een PEM- of PFX-bestand met een persoonlijke sleutel vereist en accepteert. Zie Import [a certificate to Key Vault (Een](./tutorial-import-certificate.md#import-a-certificate-to-key-vault)certificaat importeren naar Key Vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Waarom kan ik het bijbehorende wachtwoord niet zien nadat ik Key Vault met een wachtwoord heb geïmporteerd en vervolgens heb gedownload?
     
Nadat een certificaat is geïmporteerd en beveiligd in Key Vault, wordt het bijbehorende wachtwoord niet opgeslagen. Het wachtwoord is slechts één keer vereist tijdens de importbewerking. Dit is een ontwerp, maar u kunt het certificaat altijd als geheim verkrijgen en converteren van Base64 naar PFX door het wachtwoord toe te voegen [via Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Hoe kan ik de fout 'Bad parameter' oplossen? Wat zijn de ondersteunde certificaatindelingen voor het importeren naar Key Vault?

Wanneer u een certificaat importeert, moet u ervoor zorgen dat de sleutel is opgenomen in het bestand. Als u een persoonlijke sleutel afzonderlijk in een andere indeling hebt opgeslagen, moet u de sleutel combineren met het certificaat. Sommige certificeringsinstanties (CERTIFICERINGsinstanties) bieden certificaten in andere indelingen. Voordat u het certificaat importeert, moet u er daarom voor zorgen dat het bestand de PEM- of PFX-bestandsindeling heeft en dat de sleutel gebruikmaakt van Cryptest–Shamir–Adleman (RSA) of ECC-versleuteling (elliptic-curve cryptography). 

Zie certificaatvereisten [en](./certificate-scenarios.md#formats-of-import-we-support) certificaatsleutelvereisten [voor meer informatie.](../keys/about-keys.md)

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Kan ik een certificaat importeren met behulp van een ARM-sjabloon?

Nee, het is niet mogelijk om certificaatbewerkingen uit te voeren met behulp van een AZURE RESOURCE MANAGER sjabloon (ARM). Een aanbevolen tijdelijke oplossing is het gebruik van de methoden voor het importeren van certificaten in de Azure-API, De Azure CLI of PowerShell. Als u een bestaand certificaat hebt, kunt u het als geheim importeren.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Wanneer ik een certificaat importeer via Azure Portal, krijg ik de fout 'Er is iets misgegaan'. Hoe kan ik dit verder onderzoeken?
     
Als u een meer beschrijvende fout wilt weergeven, importeert u het certificaatbestand met behulp [van de Azure CLI](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) of [PowerShell.](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate)

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hoe kan ik 'Fouttype: Toegang geweigerd of gebruiker is niet gemachtigd om certificaat te importeren' oplossen?
    
Voor de importbewerking moet u de gebruiker machtigingen verlenen om het certificaat te importeren onder het toegangsbeleid. Als u dit wilt doen, gaat u naar de sleutelkluis, selecteert u Toegangsbeleid Toevoegen Toegangsbeleid Principal voor certificaatmachtigingen selecteren, zoekt u naar de gebruiker en voegt u vervolgens het e-mailadres van de gebruiker  >    >    >  toe. 

Zie About Azure Key Vault certificates (Informatie over certificaten) [voor Azure Key Vault toegangsbeleid voor meer informatie over certificaatgerelateerde toegangsbeleidsregels.](./about-certificates.md#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hoe kan ik 'Fouttype: Conflict bij het maken van een certificaat' oplossen?
    
Elke certificaatnaam moet uniek zijn. Een certificaat met dezelfde naam heeft mogelijk de status 'soft-leted'. Op basis van de samenstelling van een certificaat wordt er, wanneer er een nieuw certificaat wordt gemaakt, een adresseerbaar geheim met dezelfde naam gemaakt. Als er dus een andere sleutel of een ander geheim in de sleutelkluis is met dezelfde naam als het geheim dat u voor uw certificaat opgeeft, mislukt het maken van het certificaat en moet u die sleutel of het geheim verwijderen [of](./about-certificates.md#composition-of-a-certificate)een andere naam gebruiken voor uw certificaat. 

Zie bewerking Verwijderd certificaat [op halen voor meer informatie.](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Waarom krijg ik de foutmelding 'Fouttype: lengte van char is te lang'?
Deze fout kan worden veroorzaakt door een van de volgende twee oorzaken:    
* De onderwerpnaam van het certificaat is beperkt tot 200 tekens.
* Het certificaatwachtwoord is beperkt tot 200 tekens.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Fout :De opgegeven PEM X.509-certificaatinhoud heeft een onverwachte indeling. Controleer of het certificaat een geldige PEM-indeling heeft.
Controleer of de inhoud in het PEM-bestand gebruikmaakt van regelscheidingstekens in UNIX-stijl `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Kan ik een verlopen certificaat importeren naar Azure Key Vault?
    
Nee, verlopen PFX-certificaten kunnen niet worden geïmporteerd in Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Hoe kan ik mijn certificaat converteren naar de juiste indeling?

U kunt uw CA vragen om het certificaat in de vereiste indeling op te geven. Er zijn ook hulpprogramma's van derden waarmee u het certificaat kunt converteren naar de juiste indeling.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Kan ik certificaten importeren van niet-partner-CAs?
Ja, u kunt certificaten importeren vanuit elke certificeringsinstantie, maar uw sleutelkluis kan deze niet automatisch vernieuwen. U kunt herinneringen instellen om op de hoogte te worden gesteld van het verlopen van het certificaat.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Als ik een certificaat importeer van een partner-CA, werkt de functie voor automatisch terug laten gaan dan nog steeds?
Ja. Nadat u het certificaat hebt geüpload, moet u de automatischerotatie opgeven in het uitgiftebeleid van het certificaat. Uw instellingen blijven van kracht totdat de volgende cyclus of certificaatversie wordt uitgebracht.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Waarom zie ik het App Service certificaat dat ik heb geïmporteerd in Key Vault? 
Als u het certificaat hebt geïmporteerd, kunt u dit bevestigen door naar het deelvenster **Geheimen te** gaan.


## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault certificaten](./about-certificates.md)
