---
title: Overzicht van Microsoft Azure Data Box-beveiliging | Microsoft Docs in gegevens
description: Beschrijft Azure Data Box beveiligings functies in het apparaat, de service en de gegevens die zich op Data Box bevinden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: alkohli
ms.openlocfilehash: 4d6c77b3e8920cabc397cdcbc235baefa031e5ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655489"
---
# <a name="azure-data-box-security-and-data-protection"></a>Azure Data Box-beveiliging en -gegevensbescherming

Data Box biedt een veilige oplossing voor gegevensbescherming door te garanderen dat alleen bevoegde entiteiten uw gegevens kunnen bekijken, wijzigen of verwijderen. In dit artikel worden de Azure Data Box-beveiligingsfuncties beschreven die de Data Box-oplossingsonderdelen helpen te beschermen, evenals de gegevens die daarin worden opgeslagen.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Gegevens stromen door onderdelen

De Microsoft Azure Data Box-oplossing bestaat uit vier hoofdonderdelen die met elkaar samenwerken:

- **Azure Data Box-service die in Azure wordt gehost** – De beheerservice die u gebruikt om de apparaatorder te maken, het apparaat te configureren en de order vervolgens bij te houden totdat deze is voltooid.
- **Data Box-apparaat** – Het overdrachtsapparaat dat naar u worden verzonden om uw on-premises gegevens in Azure te importeren.
- **Clients/hosts die met het apparaat zijn verbonden** – De clients in uw infrastructuur die via USB verbinding maken met de Data Box-apparaat en gegevens bevatten die moeten worden beschermd.
- **Cloudopslag** – De locatie in de Azure-cloud waar gegevens worden opgeslagen. Deze locatie is doorgaans het opslag account dat is gekoppeld aan de Azure Data Box bron die u hebt gemaakt.

In het volgende diagram ziet u de stroom van gegevens via de Azure Data Box oplossing van on-premises naar Azure en de verschillende beveiligings functies die worden toegepast als de gegevens via de oplossing worden uitgevoerd. Deze stroom is voor een import volgorde voor uw Data Box.

![Beveiliging Data Box importeren](media/data-box-security/data-box-security-import.png)

Het volgende diagram is voor de export volgorde voor uw Data Box.

![Beveiliging Data Box exporteren](media/data-box-security/data-box-security-export.png)

Wanneer de gegevens via deze oplossing stromen, worden gebeurtenissen vastgelegd en worden logboeken gegenereerd. Ga voor meer informatie naar:

- [Tracering en logboek registratie voor uw Azure data Box import orders](data-box-logs.md).
- [Tracering en logboek registratie voor uw Azure Data Box export orders](data-box-export-logs.md)

## <a name="security-features"></a>Beveiligingsfuncties

Data Box biedt een veilige oplossing voor gegevensbescherming door te garanderen dat alleen bevoegde entiteiten uw gegevens kunnen bekijken, wijzigen of verwijderen. De beveiligingsfuncties van deze oplossing zijn voor de schijf en voor de bijbehorende service, zodat de gegevens worden beschermd die daarop worden opgeslagen.

### <a name="data-box-device-protection"></a>Data Box-apparaatbescherming

Het Data Box-apparaat wordt door de volgende functies beschermd:

- Een robuuste apparaatbehuizing die bestand is tegen schokken, nadelig transport en milieuomstandigheden. 
- Detectie van onrechtmatig gebruik van de hard- en software die verder gebruik van het apparaat voorkomt.
- Kan alleen worden uitgevoerd met Data Box-specifieke software.
- Wordt in vergrendelde toestand opgestart.
- Hiermee beheert u de toegang tot apparaten via een ontgrendelings wachtwoord voor het apparaat. Deze sleutel wordt beveiligd door een versleutelings sleutel. U kunt uw eigen door de klant beheerde sleutel gebruiken om de sleutel te beveiligen. Zie [Door de klant beheerde sleutels gebruiken in Azure Key Vault voor Azure Data Box](data-box-customer-managed-encryption-key-portal.md) voor meer informatie.
- Toegangsreferenties om gegevens van en naar het apparaat te kopiëren. Elke toegang tot de pagina met de **referenties** van het Azure portal wordt geregistreerd in de [activiteiten logboeken](data-box-logs.md#query-activity-logs-during-setup).
- U kunt uw eigen wacht woorden gebruiken voor het apparaat en de toegang tot de share. Zie [zelf studie: Order Azure data Box](data-box-deploy-ordered.md)voor meer informatie.

### <a name="establish-trust-with-the-device-via-certificates"></a>Een vertrouwens relatie met het apparaat tot stand brengen via certificaten

Met een Data Box-apparaat kunt u uw eigen certificaten meenemen en installeren die moeten worden gebruikt om verbinding te maken met de lokale web-UI en Blob-opslag. Zie [uw eigen certificaten gebruiken met data box en data Box Heavy apparaten](data-box-bring-your-own-certificates.md)voor meer informatie.

### <a name="data-box-data-protection"></a>Data Box-gegevensbescherming

De gegevens die in en uit de Data Box stromen, worden door de volgende functies beschermd:

- AES 256-bits versleuteling voor data-at-rest. In een omgeving met hoge beveiliging kunt u dubbele versleuteling op basis van software gebruiken. Zie [zelf studie: Order Azure data Box](data-box-deploy-ordered.md)voor meer informatie.
- Versleutelde protocollen kunnen worden gebruikt voor data-in-flight. U wordt aangeraden SMB 3,0 te gebruiken met versleuteling om gegevens te beveiligen wanneer u deze kopieert van uw gegevens servers.
- Beveiligde verwijdering van gegevens van het apparaat nadat het uploaden naar Azure is voltooid. Gegevens verwijdering is conform richt lijnen in [bijlage A voor ATA harde schijven in de 88r1-standaarden van NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). De gebeurtenis voor gegevens verwijdering wordt vastgelegd in de [order geschiedenis](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Data Box-servicebescherming

De Data Box-service wordt door de volgende functies beschermd:

- Voor toegang tot de Data Box-Service moet uw organisatie een Azure-abonnement hebben dat Data Box bevat. Uw abonnement bepaalt tot welke functies u toegang hebt in de Azure-portal.
- Omdat de Data Box-service in Azure wordt gehost, wordt deze beschermd door de Azure-beveiligingsfuncties. Ga naar het [Vertrouwenscentrum van Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx) voor meer informatie over de beveiligingsfuncties die worden geleverd door Microsoft Azure.
- Toegang tot de Data Box order kan worden beheerd via het gebruik van Azure-rollen. Zie [toegangs beheer voor data Box order instellen](data-box-logs.md#set-up-access-control-on-the-order) voor meer informatie
- De Data Box-Service slaat het wacht woord voor ontgrendelen op dat wordt gebruikt om het apparaat te ontgrendelen in de service.
- In de Data Box-service worden de ordergegevens en -status opgeslagen. Deze informatie wordt verwijderd wanneer de order wordt verwijderd.

## <a name="managing-personal-data"></a>Persoonlijke gegevens beheren

Azure Data Box verzamelt en toont persoonlijke informatie in de volgende belangrijke gevallen in de service:

- **Meldingsinstellingen** – Wanneer u een order maakt, configureert u het e-mailadres van gebruikers onder Meldingsinstellingen. Deze informatie kan worden bekeken door de beheerder. Deze informatie wordt verwijderd door de service wanneer de order de definitieve status bereikt of wanneer u de order verwijdert.

- **Bestellingsgegevens** : zodra de order is gemaakt, worden het verzend adres, de e-mail en de contact gegevens van de gebruikers opgeslagen in de Azure Portal. De opgeslagen informatie omvat:

  - Naam van contactpersoon
  - Telefoonnummer
  - E-mail
  - Adres
  - Plaats
  - Postcode
  - Staat
  - Land/Provincie/Regio
  - Accountnummer van transporteur
  - Volgnummer van verzending

    De ordergegevens worden verwijderd door de Data Box-service wanneer de order is voltooid of wanneer u de order verwijdert.

- **Verzendadres** – Nadat de order is geplaatst, geeft de Data Box-service het verzendadres aan externe transporteurs, zoals UPS of DHL. 

Bekijk het Microsoft-privacybeleid in het [Vertrouwenscentrum](https://www.microsoft.com/trustcenter) voor meer informatie.


## <a name="security-guidelines-reference"></a>Naslag met richtlijnen voor beveiliging

De volgende beveiligingsrichtlijnen zijn geïmplementeerd in Data Box:

|Richtlijn   |Description   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Voor bescherming tegen water en stof         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Voor het weerstaan van nadelige vervoersomstandigheden          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Voor het veilig bijwerken van firmware         |
|[FIPS 140-2 Level 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Voor gegevensbescherming         |
|Bijlage A, voor ATA harde schijven in [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Voor het opschonen van gegevens         |

## <a name="next-steps"></a>Volgende stappen

- De [Vereisten voor Data Box](data-box-system-requirements.md) bekijken.
- Informatie over de [Limieten voor Data Box](data-box-limits.md).
- [Azure Data Box](data-box-quickstart-portal.md) snel implementeren in de Azure-portal.
