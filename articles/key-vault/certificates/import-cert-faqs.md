---
title: Veelgestelde vragen-Azure Key Vault certificaat importeren
description: Krijg antwoorden op veelgestelde vragen over het importeren van Azure Key Vault certificaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 78ec0af0acbb74436af16abba75d64de061d5268
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202162"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Veelgestelde vragen over het importeren van Azure Key Vault certificaten

In dit artikel vindt u antwoorden op veelgestelde vragen over het importeren van Azure Key Vault certificaten.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hoe kan ik een certificaat in Azure Key Vault importeren?

Voor een certificaat import bewerking accepteert Azure Key Vault twee certificaat bestands indelingen: PEM en PFX. Hoewel er PEM-bestanden zijn met alleen het open bare deel, Key Vault vereist en accepteert alleen een PEM-of PFX-bestand met een persoonlijke sleutel. Zie [een certificaat importeren in Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault)voor meer informatie.

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Waarom kan ik het wacht woord dat is gekoppeld niet zien nadat ik een met een wacht woord beveiligd certificaat heb geïmporteerd naar Key Vault en het vervolgens kan downloaden?
    
Nadat een certificaat is geïmporteerd en beveiligd in Key Vault, wordt het bijbehorende wacht woord niet opgeslagen. Het wacht woord is slechts eenmaal vereist tijdens de import bewerking. Dit is standaard, maar u kunt het certificaat altijd als geheim ophalen en dit converteren van base64 naar PFX door het wacht woord via [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)toe te voegen.

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Hoe kan ik de fout ' ongeldige para meter ' oplossen? Wat zijn de ondersteunde certificaat indelingen voor het importeren naar Key Vault?

Wanneer u een certificaat importeert, moet u ervoor zorgen dat de sleutel is opgenomen in het bestand. Als u een persoonlijke sleutel afzonderlijk in een andere indeling hebt opgeslagen, moet u de sleutel combi neren met het certificaat. Sommige certificerings instanties bieden certificaten in andere indelingen. Daarom moet u, voordat u het certificaat importeert, ervoor zorgen dat het de PEM-of PFX-bestands indeling is en dat de sleutel gebruikmaakt van Rivest-Shamir – Adleman (RSA) of een ECC-code ring (elliptisch-Curve Cryptography). 

Zie [certificaat vereisten](./certificate-scenarios.md#formats-of-import-we-support) en [vereisten voor certificaat sleutels](../keys/about-keys.md)voor meer informatie.

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Kan ik een certificaat importeren met behulp van een ARM-sjabloon?

Nee, het is niet mogelijk om certificaat bewerkingen uit te voeren met behulp van een Azure Resource Manager ARM-sjabloon. Een aanbevolen tijdelijke oplossing is het gebruik van de methoden voor het importeren van certificaten in de Azure API, de Azure CLI of Power shell. Als u een bestaand certificaat hebt, kunt u het importeren als een geheim.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Wanneer ik een certificaat Importeer via de Azure Portal, krijg ik de fout ' er is iets fout gegaan '. Hoe kan ik verder onderzoeken?
    
Als u een meer beschrijvende fout wilt weer geven, importeert u het certificaat bestand met behulp van [de Azure cli](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) of [Power shell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hoe kan ik ' fout type: toegang geweigerd of gebruiker is niet gemachtigd om certificaat te importeren ' oplossen '?
    
Voor de import bewerking moet u de gebruikers machtigingen verlenen om het certificaat te importeren onder het toegangs beleid. Als u dit wilt doen, gaat u naar de sleutel kluis **, selecteert u toegangs beleid** toegangs  >  **beleid toevoegen**  >  **Selecteer certificaat machtigingen**  >  **Principal**, zoek naar de gebruiker en voeg het e-mail adres van de gebruiker toe. 

Zie [over Azure Key Vault certificaten](./about-certificates.md#certificate-access-control)voor meer informatie over toegangs beleid voor certificaten.


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hoe kan ik ' fout type: conflict bij het maken van een certificaat ' oplossen '?
    
Elke certificaat naam moet uniek zijn. Een certificaat met dezelfde naam kan in een tijdelijke status worden verwijderd. Daarnaast op basis van de [samen stelling van een certificaat](./about-certificates.md#composition-of-a-certificate)wordt, wanneer er een nieuw certificaat wordt gemaakt, een adresseerbaar geheim gemaakt met dezelfde naam, dus als de sleutel kluis een andere sleutel of een geheim bevat met dezelfde naam als het certificaat dat u probeert op te geven voor uw certificaat, mislukt het maken van de certificaten en moet u de sleutel of het geheim verwijderen of een andere naam voor het certificaat gebruiken 

Zie voor meer informatie een [Verwijderde certificaat bewerking ophalen](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Waarom krijg ik ' fout type: lengte van tekens is te lang '?
Deze fout kan een van de volgende twee oorzaken hebben:    
* De onderwerpnaam van het certificaat is beperkt tot 200 tekens.
* Het certificaat wachtwoord is beperkt tot 200 tekens.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Fout: de opgegeven PEM X. 509-certificaat inhoud heeft een onverwachte indeling. Controleer of het certificaat een geldige PEM-indeling heeft.
Controleer of de inhoud van het PEM-bestand UNIX-Style-scheidings tekens gebruikt `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Kan ik een verlopen certificaat importeren naar Azure Key Vault?
    
Nee, verlopen PFX-certificaten kunnen niet worden geïmporteerd in Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Hoe kan ik mijn certificaat converteren naar de juiste indeling?

U kunt uw CA vragen het certificaat op te geven in de vereiste indeling. Er zijn ook hulpprogram ma's van derden die u kunnen helpen bij het converteren van het certificaat naar de juiste indeling.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Kan ik certificaten van niet-partner-Ca's importeren?
Ja, u kunt certificaten van elke CA importeren, maar uw sleutel kluis kan deze niet automatisch vernieuwen. U kunt herinneringen instellen om op de hoogte te worden gesteld van het verlopen van het certificaat.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Als ik een certificaat Importeer van een partner-CA, werkt de functie automatisch verlengen toch?
Ja. Nadat u het certificaat hebt geüpload, geeft u de autorotation op in het uitgifte beleid van het certificaat. Uw instellingen blijven van kracht totdat de volgende cyclus of certificaat versie wordt vrijgegeven.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Waarom kan ik het App Service certificaat dat ik in Key Vault heb geïmporteerd, niet zien? 
Als u het certificaat hebt geïmporteerd, moet u het kunnen bevestigen door naar het deel venster **geheimen** te gaan.


## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault certificaten](./about-certificates.md)
