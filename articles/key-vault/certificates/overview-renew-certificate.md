---
title: Informatie over het verlengen van Azure Key Vault-certificaten
description: In dit artikel wordt beschreven hoe u Azure Key Vault-certificaten kunt verlengen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: ffa130c0598d2405469d272a3ac6852f281ed965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726359"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Azure Key Vault-certificaten verlengen

Met Azure Key Vault kunt u eenvoudig digitale certificaten voor uw netwerk inrichten, beheren en implementeren en beveiligde communicatie voor toepassingen mogelijk maken. Zie [Azure Key Vault-certificaten](./about-certificates.md) voor meer informatie over certificaten.

Door gebruik te maken van kortlevende certificaten of door de frequentie van de rotatie van certificaten te verhogen, kunt u toegang tot uw toepassingen door onbevoegde gebruikers voorkomen.

In dit artikel wordt beschreven hoe u Azure Key Vault-certificaten kunt verlengen.

## <a name="get-notified-about-certificate-expiration"></a>Meldingen ontvangen over verlopen certificaten
Als u een melding wilt ontvangen over levensgebeurtenissen van een certificaat, moet u een contactpersoon voor dit certificaat toevoegen. Certificaatcontactpersonen bevatten contactgegevens om meldingen te verzenden die worden geactiveerd door de levensduurgebeurtenissen van het certificaat. De contactpersoongegevens worden gedeeld door alle certificaten in de sleutelkluis. Alle opgegeven contactpersonen krijgen een melding bij een gebeurtenis met betrekking tot een certificaat in de sleutelkluis.

### <a name="steps-to-set-certificate-notifications"></a>Stappen voor het instellen van certificaatmeldingen:
Voeg eerst een contactpersoon voor het certificaat toe aan uw sleutelkluis. U kunt toevoegen met behulp van de Azure-portal of PowerShell-cmdlet [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact).

Stel vervolgens het tijdstip in wanneer u wilt worden gewaarschuwd over het verlopen van het certificaat. Zie [Automatisch roteren van certificaatconfiguratie in Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate) voor informatie over het configureren van de levenscycluskenmerken van het certificaat.

Als het beleid van een certificaat is ingesteld op automatisch verlengen, wordt er een melding verzonden bij de volgende gebeurtenissen.

- Vóór verlenging van het certificaat
- Na verlenging van het certificaat, met de mededeling dat het certificaat is verlengd of dat er een fout is opgetreden, waarna het certificaat handmatig moet worden vernieuwd.  

  Wanneer een certificaatbeleid dat is ingesteld om handmatig te worden verlengd (alleen e-mail), wordt er een melding verzonden van het tijdstip waarop het certificaat moet worden verlengd.  

Key Vault kent drie categorieën certificaten:
-    Certificaten die zijn gemaakt met een geïntegreerde certificeringsinstantie (CA), zoals DigiCert of GlobalSign
-    Certificaten die zijn gemaakt met een niet-geïntegreerde CA
-    Zelfondertekende certificaten

## <a name="renew-an-integrated-ca-certificate"></a>Een geïntegreerd CA-certificaat verlengen 
Azure Key Vault verwerkt het end-to-end-onderhoud van certificaten die worden uitgegeven door de vertrouwde Microsoft-certificeringsinstanties DigiCert en GlobalSign. Meer informatie over het [integreren van een vertrouwde CA met Key Vault](./how-to-integrate-certificate-authority.md).

## <a name="renew-a-nonintegrated-ca-certificate"></a>Een niet-geïntegreerd CA-certificaat verlengen 
U kunt met behulp van Azure Key Vault certificaten importeren vanuit elke CA, een voordeel waarmee u met verschillende Azure-resources kunt integreren en de implementatie eenvoudig kunt maken. Als u zich zorgen maakt dat u de vervaldatum van uw certificaat niet meer weet of, erger, dat u hebt ontdekt dat een certificaat al is verlopen, kunt u met behulp van uw sleutelkluis up-to-date blijven. Voor niet-geïntegreerde CA-certificaten kunt u met de sleutelkluis e-mailmeldingen instellen bij bijna verlopen certificaten. Dergelijke meldingen kunnen ook voor meerdere gebruikers worden ingesteld.

> [!IMPORTANT]
> Een certificaat is een object met versiebeheer. Als de huidige versie verloopt, moet u een nieuwe versie maken. Elke nieuwe versie is conceptueel een nieuw certificaat dat bestaat uit een sleutel en een blob die die sleutel aan een identiteit koppelt. Wanneer u een niet-gelieerde CA gebruikt, genereert de sleutelkluis een sleutel-/waardepaar en wordt een aanvraag voor certificaatondertekening (CSR) geretourneerd.

Ga als volgt te werk als u een niet-geïntegreerd CA-certificaat wilt verlengen:

1. Meld u aan bij Azure Portal en open vervolgens het certificaat dat u wilt verlengen.
1. Selecteer **Nieuwe versie** in het certificaatdeelvenster.
1. Selecteer **Certificaatbewerking**.
1. Selecteer **CSR downloaden** om een CSR-bestand naar uw lokale station te downloaden.
1. Verzend de CSR naar de gewenste certificeringsinstantie om de aanvraag te ondertekenen.
1. Breng de ondertekende aanvraag terug en selecteer **CSR samenvoegen** in hetzelfde venster voor de certificaatbewerking.

> [!NOTE]
> Het is van belang om de ondertekende CSR samen te voegen met dezelfde CSR-aanvraag die u hebt gemaakt. Anders komt de sleutel niet overeen.

Zie [een CSR maken en samenvoegen in Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal) voor meer informatie over het maken van een nieuwe CSR.

## <a name="renew-a-self-signed-certificate"></a>Een zelfondertekend certificaat verlengen

Azure Key Vault verwerkt ook het automatisch verlengen van zelfondertekende certificaten. Zie [Automatisch roteren van certificaatconfiguratie in Key Vault](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate) voor meer informatie over het wijzigen van het uitgiftebeleid en het bijwerken van de levenscycluskenmerken van een certificaat.

## <a name="troubleshoot"></a>Problemen oplossen
* Als het uitgegeven certificaat in Azure Portal de status *uitgeschakeld* heeft, gaat u naar **Certificaatbewerking** om het foutbericht voor dat certificaat te bekijken.
* Fout type ' de CSR die is gebruikt om het certificaat op te halen, is al gebruikt. Maak een nieuw certificaat met een nieuwe CSR.
  Ga naar de sectie Geavanceerd beleid van het certificaat en controleer of de optie **sleutel opnieuw gebruiken bij vernieuwen** is uitgeschakeld.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Hoe kan ik de functie voor het automatisch roteren van het certificaat testen?**

Maak een certificaat met een geldigheidsduur van **1 maand** en stel vervolgens de levensduuractie voor rotatie in op **1%** . Met deze instelling wordt het certificaat elke 7,2 uur geroteerd.
  
**Worden de labels gerepliceerd als het certificaat automatisch is verlengd?**

Ja, de labels worden na het verlengen gerepliceerd.

## <a name="next-steps"></a>Volgende stappen
*    [Key Vault integreren met DigiCert-certificeringsinstantie](how-to-integrate-certificate-authority.md)
*    [Zelfstudie: Automatische rotatie van certificaten in Key Vault configureren](tutorial-rotate-certificates.md)
