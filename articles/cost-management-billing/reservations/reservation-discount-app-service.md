---
title: Reserveringskortingen voor Azure App Service
description: Meer informatie over hoe reserverings kortingen van toepassing zijn op Azure App Service Premium v3-instanties en afzonderlijke stem pels.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: banders
ms.openlocfilehash: debe02a89e10712ad8a0b8d61b0fdc3f8a4bd7b2
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577782"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>Hoe reserverings kortingen van toepassing zijn op Azure App Service Premium v3-instanties en geïsoleerde stem pels

Dit artikel helpt u inzicht te krijgen in de manier waarop kortingen van toepassing zijn op Azure App Service Premium v3-instanties en afzonderlijke stem pels.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Hoe reserverings kortingen van toepassing zijn op Premium v3-exemplaren

Nadat u een gereserveerde Azure App Service Premium v3-exemplaar hebt gekocht, wordt de reserverings korting automatisch toegepast op App Service exemplaren die overeenkomen met de kenmerken en hoeveelheid van de reserve ring. Een reserve ring geldt voor de kosten van uw Premium v3-exemplaren. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Hoe de korting wordt toegepast op Azure App Service 

Een reserverings korting is *use-it of-verlies*. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.
Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

### <a name="reservation-discount-for-premium-v3-instances"></a>Reserverings korting voor Premium v3-exemplaren

De korting voor Azure-reserve ringen wordt op elk uur toegepast op het uitvoeren van Premium v3-exemplaren. De reserve ringen die u hebt aangeschaft, komen overeen met het gebruik dat door de lopende Premium v3-instanties wordt gegenereerd om de reserverings korting toe te passen. Voor Premium v3-exemplaren die het volledige uur niet uitvoeren, wordt de reserve ring gevuld van andere instanties die geen reserve ring gebruiken, inclusief gelijktijdig uitgevoerde exemplaren. Aan het einde van het uur is de reserverings toepassing voor exemplaren in het uur vergrendeld. In het geval dat een exemplaar niet wordt uitgevoerd gedurende een uur of wanneer er geen gelijktijdige exemplaren binnen het uur zijn, wordt het uur van de reserve ring niet gevuld, wordt de reserve ring voor dat uur ondergebruikt. De volgende grafiek illustreert de toepassing van een reservering voor facturabel VM-gebruik. Het voorbeeld is gebaseerd op één aangekochte reservering en twee gekoppelde VM-instanties.

![Afbeelding van de toepassing van een reserve ring voor het factureer bare VM-gebruik](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  Elk gebruik boven de reserveringslijn wordt gefactureerd tegen de normale tarieven op gebruiksbasis. Er worden geen kosten in rekening gebracht voor gebruik onder de reserveringslijn, aangezien dat al is betaald in het kader van de reservering.
2.  In uur 1 is instantie 1 gedurende 0,75 uur actief en instantie 2 gedurende 0,5 uur. Het totale gebruik voor uur 1 bedraagt 1,25 uur. U betaalt het tarief op gebruiksbasis voor de resterende 0,25 uur.
3.  In uur 2 en uur 3 waren beide instanties gedurende 1 uur actief. Eén instantie valt onder de reservering en de andere wordt gefactureerd volgens het tarief op gebruiksbasis.
4.  In uur 4 is instantie 1 gedurende 0,5 uur actief en instantie 2 gedurende 1 uur. Instantie 1 wordt volledig gedekt door de reservering en van instantie 2 wordt 0,5 uur gedekt. U betaalt het tarief op gebruiksbasis voor de resterende 0,5 uur.

Voor meer informatie over de toepassing van uw Azure-reserveringen in uw gebruiksrapporten voor facturering, raadpleegt u [Inzicht in reserveringsgebruik](understand-reserved-instance-usage-ea.md).

### <a name="discount-can-apply-to-different-sizes"></a>Korting kan van toepassing zijn op verschillende grootten

Wanneer u een gereserveerd Premium v3-exemplaar koopt en **geoptimaliseerd voor de flexibiliteit van de instantie grootte** selecteert, is de kortings dekking van toepassing op de Premium v3-instantie grootte die u selecteert. Dit kan ook van toepassing zijn op andere exemplaar grootten die zich in dezelfde groep van dezelfde reeks-instantie grootte bevinden.

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Hoe reserverings kortingen van toepassing zijn op geïsoleerde stem pels

Wanneer u gereserveerde capaciteit hebt gekocht om de zegelkosten van App Service Isolated te dekken, wordt de reserveringskorting automatisch toegepast op de zegelkosten in een bepaalde regio. De reserveringskorting geldt voor het gebruik dat door de zegelkostenmeter van App Service Isolated wordt bijgehouden. Werkrollen, extra front-ends en overige resources die aan de zegel zijn gekoppeld, worden als voorheen gefactureerd op basis van het normale tarief.

### <a name="reservation-discount-application"></a>Toepassing van reserveringskorting

De korting op de zegelkosten voor App Service Isolated wordt op uurbasis toegepast op actieve Isolated-zegels. Als u een zegel geen vol uur implementeert, gaat de resterende gereserveerde capaciteit voor dat uur verloren. Deze restcapaciteit kan niet worden overgedragen.

Na aankoop wordt de door u gekochte reservering gekoppeld aan een App Service Isolated-zegel die wordt uitgevoerd in een specifieke regio. Als u deze zegel afsluit, wordt de reserveringskorting automatisch toegepast op andere zegels die in de betreffende regio worden uitgevoerd. Als er geen zegels aanwezig zijn, wordt de reservering toegepast op de volgende zegel die in de regio wordt gemaakt.

Als een zegel geen volledig uur wordt uitgevoerd, wordt de reservering gedurende de resterende tijd in dat uur automatisch toegepast op andere in aanmerking komende zegels in dezelfde regio.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Een type zegel kiezen: Windows of Linux

Standaard wordt voor een lege Isolated-zegel de Windows-zegelmeter gebruikt. Dit is bijvoorbeeld het geval wanneer er geen werkrollen zijn geïmplementeerd. Deze meter blijft in gebruik als er Windows-werkrollen worden geïmplementeerd. De meter wordt echter gewijzigd in een Linux-zegelmeter als u een Linux-werkrol implementeert. Als er zowel Linux- als Windows-werkrollen zijn geïmplementeerd, wordt voor de zegel de Windows-meter gebruikt.

De zegelmeter kan tijdens de levensduur dus veranderen van Windows in Linux en omgekeerd. Reserveringen zijn echter specifiek voor een bepaald besturingssysteem. U moet daarom een reservering kopen die ondersteuning biedt voor de werkrollen die u via de zegel wilt implementeren. Voor zegels met uitsluitend Windows-werkrollen en voor zegels met gemengde werkrollen wordt de Windows-reservering gebruikt. Voor zegels met alleen Linux-werkrollen wordt gebruikgemaakt van de Linux-reservering.

U moet dus alleen een Linux-reservering kopen als u van plan bent via de zegel _uitsluitend_ Linux-werkrollen te implementeren.

### <a name="discount-examples"></a>Kortingsvoorbeelden

De volgende voorbeelden laten zien hoe de korting op de zegelkosten voor gereserveerde instanties van App Service Isolated voor verschillende implementaties wordt toegepast.

- **Voorbeeld 1**: U koopt één instantie van gereserveerde App Service Isolated-zegelcapaciteit voor een regio zonder Isolated-zegels. U implementeert een nieuwe zegel in de betreffende regio en betaalt hiervoor het tarief voor gereserveerde capaciteit.
- **Voorbeeld 2**: U koopt één instantie van gereserveerde App Service Isolated-zegelcapaciteit voor een regio waarin al een Isolated-zegel is geïmplementeerd. U betaalt voor de geïmplementeerde zegel vanaf nu het tarief voor gereserveerde capaciteit.
- **Voorbeeld 3**: U koopt één instantie van gereserveerde App Service Isolated-zegelcapaciteit voor een regio waarin al een Isolated-zegel is geïmplementeerd. U betaalt voor de geïmplementeerde zegel vanaf nu het tarief voor gereserveerde capaciteit. Later verwijdert u de bestaande zegel en implementeert u een nieuwe zegel. U betaalt voor de nieuwe zegel het tarief voor gereserveerde capaciteit. De korting kan niet worden overgedragen als er geen geïmplementeerde zegels zijn.
- **Voorbeeld 4**: U koopt één instantie van Linux-specifieke gereserveerde App Service Isolated-zegelcapaciteit voor een bepaalde regio en implementeert daar vervolgens een nieuwe zegel. Als de zegel aanvankelijk zonder werkrollen wordt geïmplementeerd, wordt de Windows-zegelmeter gebruikt. Er wordt geen korting toegepast. Wanneer de eerste Linux-werkrol via de zegel wordt geïmplementeerd, wordt er overgeschakeld naar de Linux-zegelmeter en is de reserveringskorting van kracht. Als er later via de zegel een Windows-werkrol wordt geïmplementeerd, wordt er teruggeschakeld naar de Windows-zegelmeter. De korting voor Linux-specifieke gereserveerde App Service Isolated-zegelcapaciteit vervalt dan weer.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Zie voor meer informatie over het vooraf kopen van App Service Premium v3 en geïsoleerde stempel gereserveerde capaciteit voor het besparen van geld, de verkoop [voor Azure app service met gereserveerde capaciteit](prepay-app-service.md).
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
  - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
  - [Reserveringen beheren in Azure](manage-reserved-vm-instance.md)
  - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
  - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
