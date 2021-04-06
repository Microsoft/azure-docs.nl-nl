---
title: Cloud Solution Provider-micro soft Commercial Marketplace
description: Meer informatie over het verkopen van uw aanbiedingen via het programma partner Channel van de Microsoft Cloud Solution Provider (CSP) in de commerciële Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131170"
---
# <a name="cloud-solution-provider-program"></a>Cloud Solution Provider-programma

In dit artikel wordt uitgelegd hoe u uw aanbieding zo configureert dat deze beschikbaar is voor het programma Cloud Solution Provider (CSP). Naast het publiceren van uw aanbiedingen via [online winkels voor commerciële Marketplace](overview.md#commercial-marketplace-online-stores)kunt u ook via het CSP-programma verkopen om miljoenen micro soft-klanten te bereiken die het programma gebruikt.

U kunt nieuwe of bestaande aanbiedingen voor Beschik baarheid configureren in het CSP-programma, zodat CSP-partners uw producten kunnen verkopen en gebundelde oplossingen voor klanten kunnen maken.

Uitgevers zijn verantwoordelijk voor het verstrekken van ondersteuning voor eind gebruikers en voor het bieden van een mechanisme voor partners in het CSP-programma en/of klanten om contact met u op te nemen voor ondersteuning. Het is een best practice om partners in het CSP-programma te voorzien van de gebruikers documentatie, training en service status/uitval meldingen (indien van toepassing), zodat partners in het CSP-programma de ondersteunings aanvragen voor Tier 1 van klanten kunnen afhandelen.  

De volgende aanbiedingen komen in aanmerking voor de keuze om te worden verkocht door partners in het CSP-programma:

- SaaS-aanbiedingen (Software-as-a-Service)
- Virtual Machines (Vm's)
- Oplossingssjablonen
- Beheerde toepassingen

> [!NOTE]
> In containers en worden uw eigen licentie (BYOL)-VM-abonnementen meegebracht die standaard worden verkocht door partners in het CSP-programma.

## <a name="how-to-configure-an-offer"></a>Een aanbieding configureren

Configureer de instelling voor het CSP-programma bij het maken van de aanbieding in Partner Center. Meer [informatie over het wijzigen van Publisher-ervaring](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Partner centrum-opt-in

De opt-in-ervaring bevindt zich in de module CSP reseller-doel groep:

![CSP reseller-groep](media/marketplace-publishers-guide/csp-reseller-audience.png)

U kunt kiezen uit drie opties:

1. Alle partners in het CSP-programma.
2. Specifieke partners in het CSP-programma dat ik selecteer.
3. Geen partners in het CSP-programma.

#### <a name="option-1-any-partner-in-the-csp-program"></a>Optie 1: alle partners in het CSP-programma

![Alle partners in het CSP-programma](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Als u deze optie kiest, komen alle partners in het CSP-programma in aanmerking om uw aanbieding door te verkopen aan hun klanten.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>Optie 2: specifieke partners in het CSP-programma dat ik Selecteer

![Specifieke partners in het CSP-programma dat ik Selecteer](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Als u deze optie kiest, machtigt u welke partners in het CSP-programma in aanmerking komen voor het verkopen van uw aanbieding.

Als u partners wilt machtigen, selecteert u **CSP-partners selecteren** en een menu wordt weer gegeven waarin u kunt zoeken op partner naam of CSP Azure Active Directory (Azure AD) Tenant-id.

![CSP-menu selecteren](media/marketplace-publishers-guide/csp-pop-up-module.png)

U kunt Zoek filters toep assen, zoals **land**, **competentie** of **vaardigheid**.

![Land-, regio-, competentie-en vaardigheids filters voor het zoeken naar partners](media/marketplace-publishers-guide/csp-add-resellers.png)

Wanneer u de lijst met partners hebt gekozen, selecteert u **toevoegen**.

![Voor beeld van een lijst met geautoriseerde partners in het CSP-programma](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Er wordt een tabel weer gegeven met de lijst met partners die u hebt geselecteerd op de pagina CSP reseller.

![Tabel met een lijst met partners op de CSP-dealer pagina](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selecteer **concept opslaan** om uw wijzigingen te registreren.

Als deze aanbieding niet is gepubliceerd, moet u uw aanbieding publiceren om deze beschikbaar te maken voor uw geselecteerde partners.

>[!NOTE]
>Als u een partner machtigt in het CSP-programma in een bepaalde regio, kunnen ze de aanbieding verkopen aan elke klant die tot die specifieke regio behoort. Voor meer informatie over hoe CSP-aanbiedingen worden geclassificeerd onder regio's, zie de [regionale markten en valuta van het Cloud Solution Provider-programma](/partner-center/regional-authorization-overview).

Als u de lijst met CSP'S van een al gepubliceerde aanbieding bijwerkt, voegt u de aanvullende partners toe en selecteert u **CSP-doel groep synchroniseren**.

Als u een aanbieding hebt die al een lijst met geautoriseerde partners heeft en u dezelfde lijst voor een andere aanbieding wilt gebruiken, gebruikt u **importeren/exporteren**. Navigeer naar het aanbod met de lijst met csp's en selecteer **Csp's exporteren**. De functie ontwikkelt een CSV-bestand dat in een andere aanbieding kan worden geïmporteerd.

#### <a name="option-3-no-partners-in-the-csp-program"></a>Optie 3: geen partners in het CSP-programma

![Geen enkele partner in het CSP-programma](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Als u deze optie kiest, kunt u uw aanbieding uit het CSP-programma aansturen. U kunt deze selectie op elk gewenst moment wijzigen.

## <a name="deauthorize-partners-in-the-csp-program"></a>De autorisatie van partners in het CSP-programma intrekken

Als u een partner hebt geautoriseerd in het CSP-programma en die partner het product al naar hun klanten heeft verkocht, mag u de autorisatie van die partner niet ongedaan maken.

Als een partner in het CSP-programma uw product niet aan hun klanten heeft verkocht en u de CSP wilt verwijderen nadat uw aanbieding is gepubliceerd, gebruikt u de volgende instructies:

1. Ga naar de [pagina ondersteuningsaanvraag](https://aka.ms/marketplacepublishersupport). De eerste paar vervolg keuzemenu's worden automatisch ingevuld.

   > [!NOTE]
   > Wijzig de vooraf gevulde vervolg menu selecties niet.

2. Selecteer **Live offer Management** voor **de product versie selecteren**.
3. Voor **een categorie selecteren die het probleem het beste beschrijft**, kiest u de categorie die naar uw aanbieding verwijst.
4. Voor **een probleem selecteren dat het probleem het beste beschrijft**, selecteert u **bestaande aanbieding bijwerken**.
5. Selecteer **volgende** om naar de pagina met **Details** van het probleem te verwijzen om meer informatie over uw probleem op te geven.
6. Gebruik **CSP intrekken** als de titel van het probleem en vul de rest van de vereiste secties in.

## <a name="navigate-between-options"></a>Scha kelen tussen opties

Gebruik deze sectie om te navigeren tussen de drie CSP-reseller opties.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navigeren vanuit optie één: elke partner in het CSP-programma

Als uw aanbieding momenteel is ingesteld **op ' 1 ': alle partners in het CSP-programma** en u wilt navigeren naar een van de twee andere opties, gebruik de volgende instructies om een aanvraag te maken:

1. Ga naar de [pagina ondersteuningsaanvraag](https://aka.ms/marketplacepublishersupport). De eerste paar vervolg keuzemenu's worden automatisch ingevuld.

   > [!NOTE]
   > Wijzig de vooraf gevulde vervolg menu selecties niet.

2. Selecteer **Live offer Management** voor **de product versie selecteren**.
3. Voor **een categorie selecteren die het probleem het beste beschrijft**, kiest u de categorie die naar uw aanbieding verwijst.
4. Voor **een probleem selecteren dat het probleem het beste beschrijft**, selecteert u **bestaande aanbieding bijwerken**.
5. Selecteer **volgende** om naar de pagina met **Details** van het probleem te verwijzen om meer informatie over uw probleem op te geven.
6. Gebruik **CSP intrekken** als de titel van het probleem en vul de rest van de vereiste secties in.

> [!NOTE]
> Als u probeert te navigeren naar optie twee en een partner in het CSP-programma de aanbieding al aan hun klanten heeft doorverkocht, is die partner standaard al aanwezig in de lijst met geautoriseerde partners.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navigeren vanuit de tweede optie: specifieke partners in het CSP-programma dat ik Selecteer

Als uw aanbieding momenteel is ingesteld **op 2: specifieke partners in het CSP-programma dat ik selecteer** en u wilt naar **optie 1 navigeren: een partner in het CSP-programma**, gebruik de volgende instructies om een aanvraag te maken:

1. Ga naar de [pagina ondersteuningsaanvraag](https://aka.ms/marketplacepublishersupport). De eerste paar vervolg keuzemenu's worden automatisch ingevuld.

   > [!NOTE]
   > Wijzig de vooraf gevulde vervolg menu selecties niet.

2. Selecteer **Live offer Management** voor **de product versie selecteren**.
3. Voor **een categorie selecteren die het probleem het beste beschrijft**, kiest u de categorie die naar uw aanbieding verwijst.
4. Voor **een probleem selecteren dat het probleem het beste beschrijft**, selecteert u **bestaande aanbieding bijwerken**.
5. Selecteer **volgende** om naar de pagina met **Details** van het probleem te verwijzen om meer informatie over uw probleem op te geven.
6. Gebruik **CSP intrekken** als de titel van het probleem en vul de rest van de vereiste secties in.

 Als uw aanbieding momenteel is ingesteld **op 2: specifieke partners in het CSP-programma dat ik selecteer** en u wilt navigeren naar **optie 3: geen partners in het CSP-programma**, u kunt alleen naar deze optie navigeren als de partners in het CSP-programma dat u eerder hebt geautoriseerd, uw aanbieding niet aan eind klanten hebben verkocht. Gebruik de volgende instructies om een aanvraag te maken:

1. Ga naar de [pagina ondersteuningsaanvraag](https://aka.ms/marketplacepublishersupport). De eerste paar vervolg keuzemenu's worden automatisch ingevuld.

   > [!NOTE]
   > Wijzig de vooraf gevulde vervolg menu selecties niet.

2. Selecteer **Live offer Management** voor **de product versie selecteren**.
3. Voor **een categorie selecteren die het probleem het beste beschrijft**, kiest u de categorie die naar uw aanbieding verwijst.
4. Voor **een probleem selecteren dat het probleem het beste beschrijft**, selecteert u **bestaande aanbieding bijwerken**.
5. Selecteer **volgende** om naar de pagina met **Details** van het probleem te verwijzen om meer informatie over uw probleem op te geven.
6. Gebruik **CSP intrekken** als de titel van het probleem en vul de rest van de vereiste secties in.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navigeren vanuit optie 3: geen partners in het CSP-programma

Als uw aanbieding momenteel is ingesteld op **3: geen partners in het CSP-programma**, kunt u op elk gewenst moment naar een van de andere twee opties navigeren.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Verkoop-en ondersteunings materialen delen met partners in het CSP-programma

Om ervoor te zorgen dat partners in het Cloud Solution Provider-programma het meest effectief vertegenwoordigen en samen werken met uw organisatie, moet u verkoop-en ondersteunings materialen verzenden die beschikbaar zijn voor de wederverkopers. Deze resources worden niet blootgesteld aan klanten in de online winkels.

### <a name="partner-center-csp-channel"></a>Partner centrum CSP-kanaal

Als u in het partner centrum hebt gekozen voor het CSP-kanaal, moeten uitgevers een URL invoeren die als host fungeert voor relevante marketing materialen en kanaal contactpersoongegevens onder de module aanbieding.

![Partner centrum CSP-informatie over onderpand](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Go-to-Market-Services](https://partner.microsoft.com/reach-customers/gtm).
- Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken en te configureren.