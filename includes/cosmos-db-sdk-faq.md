---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90068729"
---
**Hoe word ik op de hoogte gesteld van de buitengebruikstelling van de SDK?**

Microsoft zal u 12 maanden vóór het einde van de ondersteuning op de hoogte stellen van de buitengebruikstelling van de SDK om een soepele overgang naar een ondersteunde SDK te vergemakkelijken. We sturen u een melding via verschillende communicatiekanalen: de Azure Portal, Azure-updates en rechtstreekse communicatie met toegewezen servicebeheerders.

**Kan ik toepassingen maken met behulp van de Azure Cosmos DB SDK die buiten gebruik gesteld zal worden gedurende de periode van 12 maanden?** 

Ja, u kunt toepassingen ontwerpen, implementeren en wijzigen met behulp van de Azure Cosmos DB SDK die buiten gebruik gesteld zal worden, gedurende de periode van 12 maanden. Wij raden u aan, indien van toepassing, te migreren naar een nieuwere, ondersteunde versie van de Azure Cosmos DB SDK gedurende de kennisgevingsperiode van 12 maanden. 

**Wat gebeurt er na de buitengebruikstellingsdatum met de toepassingen die de niet-ondersteunde Azure Cosmos DB SDK gebruiken?** 

Na de buitengebruikstellingsdatum lost Azure Cosmos DB geen problemen meer op, voegt geen nieuwe functies meer toe en levert geen ondersteuning meer voor de buiten gebruik gestelde SDK-versies. Als u liever geen upgrade uitvoert, worden aanvragen die zijn verzonden vanaf de buiten gebruik gestelde versies van de SDK nog steeds behandeld door de Azure Cosmos DB-service. 

**Welke SDK-versies hebben de nieuwste functies en updates?**

Nieuwe functies en updates worden alleen toegevoegd aan de meest recente secundaire versie van de meest recente ondersteunde primaire SDK-versie. Wij raden aan altijd de nieuwste versie te gebruiken om te profiteren van nieuwe functies, prestatieverbeteringen en oplossingen voor problemen. Als u een oude, niet buiten gebruik gestelde versie van de SDK gebruikt, zullen uw aanvragen voor Azure Cosmos DB nog steeds werken, maar hebt u geen toegang tot nieuwe mogelijkheden.  

**Wat moet ik doen als ik mijn toepassing niet kan bijwerken vóór een afsluitdatum?**

Wij raden u aan zo snel mogelijk een upgrade naar de nieuwste SDK uit te voeren. Nadat een SDK is gelabeld voor buitengebruikstelling, hebt u 12 maanden om de toepassing bij te werken. Als u niet kunt bijwerken voor de buitengebruikstellingsdatum, worden aanvragen die zijn verzonden vanuit de buiten gebruik gestelde versies van de SDK nog steeds behandeld door Azure Cosmos DB, zodat uw actieve toepassingen blijven functioneren. Maar Azure Cosmos DB lost geen problemen meer op, voegt geen nieuwe functies meer toe en levert geen ondersteuning meer voor de buiten gebruik gestelde SDK-versies. 

Als u een ondersteuningsplan hebt en technische ondersteuning nodig hebt, [neem contact met ons op](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) door een ondersteuningsticket in te vullen.
    


