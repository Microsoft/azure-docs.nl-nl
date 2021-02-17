---
title: Preview-certificaten voor Azure Firewall Premium
description: Voor een correcte configuratie van TLS-inspectie op Azure Firewall Premium Preview moet u tussenliggende CA-certificaten configureren en installeren.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3914a82903c293cf1a8306b5ecc1f542fef83e72
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549755"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Preview-certificaten voor Azure Firewall Premium 

> [!IMPORTANT]
> Azure Firewall Premium is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

 Om Azure Firewall Premium preview TLS-inspectie correct te configureren, moet u een geldig tussenliggend CA-certificaat opgeven en dit in azure Key kluis aanwijzen.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certificaten die worden gebruikt door Azure Firewall Premium preview

Er worden in een typische implementatie drie typen certificaten gebruikt:

- **Tussenliggend CA-certificaat (CA-certificaat)**

   Een certificerings instantie (CA) is een organisatie die vertrouwd is voor het ondertekenen van digitale certificaten. Een certificerings instantie verifieert identiteit en rechtmatigheid van een bedrijf of een persoon die een certificaat aanvraagt. Als de verificatie is geslaagd, wordt door de CA een ondertekend certificaat uitgegeven. Wanneer de server het certificaat aan de client (bijvoorbeeld uw webbrowser) presenteert tijdens een SSL/TLS-Handshake, probeert de client de hand tekening te verifiëren op basis van een lijst met *bekende goede* ondertekenaars. Webbrowsers worden normaal gesp roken geleverd met lijsten van Ca's die impliciet worden vertrouwd om hosts te identificeren. Als de instantie zich niet in de lijst bevindt, zoals bij sommige sites die hun eigen certificaten ondertekenen, wordt de gebruiker door de browser gewaarschuwd dat het certificaat niet is ondertekend door een erkende instantie en wordt de gebruiker gevraagd om de communicatie met niet-geverifieerde site te voortzetten.

- **Server certificaat (website certificaat)**

   Een certificaat dat is gekoppeld aan een specifieke domein naam. Als een website een geldig certificaat heeft, betekent dit dat een certificerings instantie stappen heeft ondernomen om te controleren of het webadres daad werkelijk deel uitmaakt van die organisatie. Wanneer u een URL typt of een koppeling naar een beveiligde website volgt, controleert uw browser het certificaat op de volgende kenmerken:
   - Het adres van de website komt overeen met het adres op het certificaat.
   - Het certificaat is ondertekend door een certificerings instantie die door de browser wordt herkend als een *vertrouwde* instantie.
   
   Af en toe kunnen gebruikers verbinding maken met een server met een niet-vertrouwd certificaat. Azure Firewall wordt de verbinding verbroken alsof de server de verbinding verbreekt.

- **Basis-CA-certificaat (basis certificaat)**

   Een certificerings instantie kan meerdere certificaten uitgeven in de vorm van een boom structuur. Een basis certificaat is het bovenste certificaat van de structuur.

Azure Firewall Premium preview kan uitgaande HTTP/S-verkeer onderscheppen en automatisch een server certificaat genereren voor `www.website.com` . Dit certificaat wordt gegenereerd met behulp van het tussenliggende CA-certificaat dat u opgeeft. Browser-en client toepassingen van de eind gebruiker moeten het basis-CA-certificaat of het tussenliggende CA-certificaat van de organisatie vertrouwen om deze procedure te kunnen gebruiken. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Certificaat proces":::

## <a name="intermediate-ca-certificate-requirements"></a>Vereisten voor tussenliggende CA-certificaten

Zorg ervoor dat uw CA-certificaat voldoet aan de volgende vereisten:

- Als Key Vault geheim is geïmplementeerd, moet u wacht woord-minder PFX (Pkcs12/pfx-profiel) gebruiken met een certificaat en een persoonlijke sleutel.

- Het moet één certificaat zijn en mag niet de volledige keten van certificaten bevatten.  

- Deze moet geldig zijn voor één jaar.  

- Dit moet een persoonlijke RSA-sleutel zijn met een minimale grootte van 4096 bytes.  

- De `KeyUsage` uitbrei ding moet als kritiek zijn gemarkeerd met de `KeyCertSign` vlag (RFC 5280; 4.2.1.3 Key Usage).

- De `BasicContraints` uitbrei ding moet als kritiek zijn gemarkeerd (RFC 5280; 4.2.1.9 Basic-beperkingen).  

- De `CA` vlag moet worden ingesteld op True.

- De lengte van het pad moet groter zijn dan of gelijk zijn aan een.

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) is een door een platform beheerd geheim archief dat u kunt gebruiken voor het beveiligen van geheimen, sleutels en TLS/SSL-certificaten. Azure Firewall Premium ondersteunt de integratie met Key Vault voor server certificaten die zijn gekoppeld aan een firewall beleid.
 
Uw sleutel kluis configureren:

- U moet een bestaand certificaat met het sleutel paar importeren in uw sleutel kluis. 
- U kunt ook een sleutel kluis geheim gebruiken dat is opgeslagen als een met een wacht woord kleiner, met base 64 gecodeerd PFX-bestand.  Een PFX-bestand is een digitaal certificaat dat zowel een persoonlijke sleutel als een open bare sleutel bevat.
- Het is raadzaam om een CA-certificaat te importeren, omdat hiermee een waarschuwing kan worden geconfigureerd op basis van de verval datum van het certificaat.
- Nadat u een certificaat of een geheim hebt geïmporteerd, moet u toegangs beleid in de sleutel kluis definiëren om toe te staan dat de toe te kennen identiteit toegang krijgt tot het certificaat/geheim.
- Het gegeven CA-certificaat moet worden vertrouwd door de Azure-workload. Controleer of ze correct zijn geïmplementeerd.

U kunt een bestaande door de gebruiker toegewezen beheerde identiteit maken of hergebruiken, die Azure Firewall gebruikt om certificaten van Key Vault namens u op te halen. Zie [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie. 

## <a name="configure-a-certificate-in-your-policy"></a>Een certificaat in uw beleid configureren

Als u een CA-certificaat wilt configureren in het beleid voor de Firewall Premium, selecteert u uw beleid en selecteert u vervolgens **TLS-inspectie (preview)**. Selecteer **ingeschakeld** op de pagina **TLS-inspectie** . Selecteer vervolgens uw CA-certificaat in Azure Key Vault, zoals wordt weer gegeven in de volgende afbeelding:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Overzichts diagram van Azure Firewall Premium":::
 
> [!IMPORTANT]
> Als u een certificaat wilt bekijken en configureren via de Azure Portal, moet u uw Azure-gebruikers account toevoegen aan het Key Vault toegangs beleid. Geef uw gebruikers account **Get** en **vermeld** onder **geheime machtigingen**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Toegangs beleid Azure Key Vault":::


## <a name="troubleshooting"></a>Problemen oplossen

Als uw CA-certificaat geldig is, maar u geen toegang hebt tot FQDN-namen of Url's onder TLS-inspectie, controleert u de volgende items:

- Zorg ervoor dat het webserver certificaat geldig is.  

- Zorg ervoor dat het basis-CA-certificaat is geïnstalleerd op het client besturingssysteem.  

- Zorg ervoor dat de browser of de HTTPS-client een geldig basis certificaat bevat. Firefox en andere browsers hebben mogelijk een speciaal certificerings beleid.  

- Zorg ervoor dat het doel type van de URL in uw toepassings regel betrekking heeft op het juiste pad en eventuele andere hyper links die zijn Inge sloten in de doel-HTML-pagina. U kunt joker tekens gebruiken voor een eenvoudige dekking van het volledige vereiste URL-pad.  


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Firewall Premium-functies](premium-features.md)
