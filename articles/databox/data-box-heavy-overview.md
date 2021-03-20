---
title: Overzicht van Microsoft Azure Data Heavy | Microsoft Docs in gegevens
description: Beschrijft Azure Data Box, een cloudoplossing waarmee u enorme hoeveelheden gegevens naar Azure kunt overdragen.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 2f1f01a8cfa25c222848e7cc5c86dc3532eba348
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80437781"
---
# <a name="what-is-azure-data-box-heavy"></a>Wat is Azure Data Box Heavy?

Met Azure Data Box Heavy kunt u snel, goedkoop en betrouwbaar honderden terabytes aan gegevens verzenden naar Azure. De gegevens worden overgebracht naar Azure door een Data Box Heavy-apparaat met een opslagcapaciteit van 1 PB naar u te verzenden, deze te vullen met uw gegevens en terug te zenden naar Microsoft. Dit apparaat heeft een stevige behuizing om uw gegevens tijdens het verzenden te beschermen.

Nadat we het apparaat hebben ontvangen in het datacenter, kunt u het instellen met de lokale webinterface. Kopieer de gegevens van uw servers naar het apparaat en stuur het terug naar Azure. In het Azure-datacenter worden uw gegevens geüpload naar uw Azure Storage-account(s). U kunt het volledige end-to-end-proces volgen in Azure Portal.


> [!IMPORTANT]
> - Meld u aan bij [Azure Portal](https://portal.azure.com) om een apparaat aan te vragen.


## <a name="use-cases"></a>Gebruiksvoorbeelden

Data Box Heavy is het meest geschikt voor gegevens in de grootte van honderden terabytes, waarbij de netwerkverbinding onvoldoende is om de gegevens naar Azure te uploaden. De gegevensverplaatsing kan eenmalig of periodiek zijn, of eerst een grote gegevensoverdracht, gevolgd door meerdere periodieke overdrachten. Hier volgen de verschillende scenario’s waarin Data Box Heavy kan worden gebruikt voor gegevensoverdracht.

 - **Eenmalige migratie**: wanneer grote aantallen on-premises gegevens worden verplaatst naar Azure.
     - Verplaatsen van een mediabibliotheek van offline tapes naar Azure, voor het maken van een online mediabibliotheek.
     - Uw virtuele machines, SQL Server en toepassingen migreren naar Azure.
     - Historische gegevens naar Azure verplaatsen voor gedetailleerde analyse en rapportage met HDInsight.

 - **Initiële bulkoverdracht**: wanneer eerst een grote overdracht plaatsvindt met Data Box Heavy (seed), gevolgd door incrementele overdrachten via het netwerk.
     - Bijvoorbeeld: voor de eerste grote back-up van historische gegevens naar Azure wordt gebruikgemaakt van Data Box Heavy en een back-upoplossing. Zodra dat is voltooid, worden gegevens incrementeel via het netwerk overgebracht naar Azure-opslag.

 - **Periodieke uploads**: wanneer er periodiek grote aantallen gegevens worden gegenereerd en verplaatst moeten worden naar Azure. Bijvoorbeeld in de energie-exploitatie, waar er video's worden gegenereerd op olieplatforms en windmolenparken.

## <a name="benefits"></a>Voordelen

Data Box Heavy is ontworpen om enorme aantallen gegevens naar Azure te verplaatsen, zonder impact op het netwerk. Deze oplossing biedt de volgende voordelen:

- **Snelheid**: Data Box Heavy maakt gebruik van netwerkinterfaces met een hoge capaciteit van 40 Gpbs.

- **Beveiliging**: Data Box Heavy heeft ingebouwde beveiliging voor het apparaat, de gegevens en de service.
    - Het apparaat heeft een robuuste behuizing die is beveiligd met fraudebestendige schroeven en verzegelde stickers.
    - De gegevens op het apparaat zijn altijd beveiligd met AES 256-bitsversleuteling.
    - Het apparaat kan alleen worden ontgrendeld met een wachtwoord dat via Azure Portal wordt doorgegeven.
    - De service wordt beschermd door beveiligingsfuncties van Azure.
    - Nadat uw gegevens zijn geüpload naar Azure, worden alle schijven in het apparaat gewist overeenkomstig de 800-88r1-standaarden van het NIST (National Institute of Standards and Technology).


## <a name="features-and-specifications"></a>Functies en specificaties

Het Data Box Heavy-apparaat biedt de volgende functies in deze versie.

| Specificaties                                          | Beschrijving              |
|---------------------------------------------------------|--------------------------|
| Gewicht                                                  | ~ 500 lbs. <br>Apparaat op vergrendelbare wielen voor transport|
| Afmetingen                                              | Breedte: 26 inch Hoogte: 28 inch Lengte: 48 inch |
| Rackruimte                                              | Kan niet in een rek worden gemonteerd|
| Vereiste kabels                                         | 4 gegronde stroomkabels van 120 V/10 A (NEMA 5-15) worden meegeleverd <br> Het apparaat biedt ondersteuning voor voeding van 240 V en heeft C-13-stopcontacten <br> Gebruik netwerkkabels die compatibel zijn met [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Voeding                                                    | 4 ingebouwde voedingseenheden (PSU's) die worden gedeeld tussen de knooppunten van het apparaat <br> stroomverbruik 1200 watt onder normale omstandigheden|
| Opslagcapaciteit                                        | ~ 1 PB ruwe capaciteit, 70 schijven van elk 14 TB <br> 770 TB aan bruikbare capaciteit|
| Aantal knooppunten                                          | 2 onafhankelijke knooppunten per apparaat (500 TB elk) |
| Netwerkinterfaces per knooppunt                             | 4 netwerkinterfaces per knooppunt <br><br> MGMT, DATA3 <ul><li> 2 X 1 GbE-interfaces </li><li> MGMT is voor beheer en voor eerste installatie, niet configureerbaar door gebruikers </li><li> DATA3 is standaard door de gebruiker te configureren en maakt gebruik van het Dynamic Host Configuration Protocol (DHCP)</li></ul>DATA1-, DATA2-gegevensinterfaces <ul><li>2 X 40 GbE-interfaces </li><li> Door de gebruiker te configureren voor DHCP (standaard) of statisch</li></ul>|


## <a name="components"></a>Onderdelen

Deze Data Box Heavy bevat de volgende onderdelen:

* **Data Box Heavy-apparaat**: Een fysiek apparaat met een stevige behuizing waarop gegevens veilig worden opgeslagen. Dit apparaat heeft een bruikbare opslagcapaciteit van 770 TB.
    
* **Data Box-service**: Een extensie in Azure Portal waarmee u een Data Box Heavy-apparaat kunt beheren via een webinterface waartoe u toegang hebt vanaf verschillende geografische locaties. Gebruik de Data Box-service om uw Data Box Heavy-apparaat te beheren. Met de service kunt u taken uitvoeren zoals orders maken en beheren, waarschuwingen bekijken en beheren en shares beheren.  

* **Lokale webinterface**: een webinterface die wordt gebruikt voor het configureren van het apparaat, zodat u verbinding kunt maken met het lokale netwerk en het apparaat bij de Data Box-service kunt registreren. Gebruik de lokale webinterface om het apparaat uit te schakelen of opnieuw op te starten, logboeken met kopieerbewerkingen te bekijken en contact op te nemen met Microsoft Ondersteuning om een serviceaanvraag in te dienen.


## <a name="the-workflow"></a>De werkstroom

Een typische stroom bestaat uit de volgende stappen:

1. **Bestellen**: plaats een bestelling in Azure Portal en geef de verzendingsgegevens en het Azure-doelopslagaccount voor uw gegevens op. Wanneer het apparaat beschikbaar is, bereidt Azure het voor en verstuurt Azure het met volgnummer.

2. **Ontvangen**: Nadat het apparaat wordt geleverd, sluit u de opgegeven netwerk- en voedingskabels aan. Zet het apparaat aan en sluit het aan. Configureer de netwerkinstellingen voor het apparaat en koppel bestandsshares op de hostcomputer van waar u gegevens wilt kopiëren.

3. **Gegevens kopiëren**: Kopieer gegevens naar de shares van Data Box Heavy.

4. **Retourneren**: Bereid het apparaat voor, zet het uit en stuur het terug naar het Azure-datacenter.

5. **Uploaden**: De gegevens worden automatisch van het apparaat naar Azure gekopieerd. De schijven in het apparaat worden veilig gewist overeenkomstig de richtlijnen van het National Institute of Standards and Technology.

Tijdens dit proces wordt u via e-mail op de hoogte gesteld van alle statuswijzigingen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Data Box Heavy kan bestanden overgedragen op basis van de regio waarin de service is geïmplementeerd, het land/de regio waarnaar het apparaat wordt verzonden en het Azure-opslagaccount waarnaar de gegevens worden overgebracht.

- **Beschikbaarheid van service**: Voor deze release is de Data Box Heavy-resource beschikbaar in de volgende regio’s:
    - Alle openbare cloud-regio's in de Verenigde Staten: VS - west-centraal, VS - west 2, VS - west, VS - zuid-centraal, VS - centraal, VS - noord-centraal, VS - oost en VS - oost 2.
    - Europese Unie: Europa - west en Europa - noord.
    - Verenigd Koninkrijk: VK - zuid en VK - west.
    - Frankrijk: Frankrijk - centraal en Frankrijk - zuid.

- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s waar de service beschikbaar is.

Ga naar [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor de meest actuele informatie over de beschikbaarheid in regio's van Data Box Heavy.

## <a name="sign-up"></a>Aanmelden

Voer de volgende stappen uit om u te registreren voor Data Box Heavy:

1. [Meld u aan bij Azure Portal](https://portal.azure.com).
2. Klik op **+ Een resource maken** om een nieuwe resource te maken. Zoek naar **Azure Data Box**. Selecteer de **Azure Data Box**-service.
3. Klik op **Create**.
4. Kies het abonnement dat u wilt gebruiken voor Data Box Heavy. Selecteer de regio waar u de Data Box Heavy-resource wilt implementeren. Klik bij de optie **Data Box Heavy** op **Registreren**.
5. Beantwoord de vragen met betrekking tot in welk land of welke regio de gegevens zich bevinden, het tijdskader, de Azure-doelservice voor gegevensoverdracht, de netwerkbandbreedte en de frequentie van gegevensoverdracht. Lees de privacyverklaring en voorwaarden en schakel het selectievakje in zodat Microsoft uw e-mailadres kan gebruiken om contact met u op te nemen.

Zodra u zich hebt geregistreerd, kunt u een Data Box Heavy bestellen.

    
