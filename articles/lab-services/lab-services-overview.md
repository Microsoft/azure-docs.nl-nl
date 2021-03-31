---
title: Informatie over Azure Lab Services | Microsoft Docs
description: Lees hoe u met Lab Services gemakkelijk labs kunt maken, beheren en beveiligen met virtuele machines die kunnen worden gebruikt door ontwikkelaars, testers, docenten, studenten en anderen.
ms.topic: overview
ms.date: 09/16/2020
ms.openlocfilehash: ad17ebb3a803a15d1ac9ef8cb71cf8ca7976243b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333933"
---
# <a name="an-introduction-to-azure-lab-services"></a>Een inleiding tot Azure Lab Services
Met **Azure Lab Services** kunt u labs maken waarvan de infrastructuur wordt beheerd door Azure. Op dit moment is het leslokaallab het enige type beheerde lab dat wordt ondersteund door Azure Lab Services. De service zelf is verantwoordelijk voor het volledige beheer van de infrastructuur voor een beheerd lab, van het inrichten van VM's tot de afhandeling van fouten en het schalen van de infrastructuur. Nadat een IT-beheerder een labaccount heeft gemaakt in Azure Lab Services, kan een docent snel een lab instellen voor de klas, het aantal en type virtuele machines opgeven dat nodig is voor de klas, en gebruikers toevoegen aan de klas. Wanneer een gebruiker zich heeft geregistreerd voor de klas, heeft hij of zij toegang tot de virtuele machine om oefeningen uit te voeren voor de klas.  

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden
Azure Lab Services ondersteunt de volgende belangrijke mogelijkheden en-functies:

- **Snelle en flexibele installatie van een lab**. Met Azure Lab Services kunnen labeigenaren snel een lab instellen voor hun behoeften. Via de service kunnen alle Azure-infrastructuurtaken worden uitgevoerd voor beheerde labtypen. De service biedt ingebouwde schaalbaarheid en tolerantie van de infrastructuur voor labs die de service voor u beheert.
- **Vereenvoudigde ervaring voor labgebruikers**. Labgebruikers kunnen zich met een registratiecode registreren bij een lab en het lab vervolgens op elk gewenst moment openen om de resources van het lab te gebruiken. 
- **Optimalisatie van kosten en analyse**. De eigenaar van een lab kan planningen voor het lab instellen om virtuele machines automatisch af te sluiten en op te starten. De eigenaar van het lab kan een planning instellen om de tijdstippen op te geven waarop de virtuele machines van het lab toegankelijk zijn voor gebruikers. De eigenaar kan bovendien een gebruiksbeleid per gebruiker of per lab instellen om de kosten te optimaliseren. 

Als u alleen in een lab wilt invoeren wat u nodig hebt en de service de voor het lab vereiste infrastructuur wilt laten instellen, dient u een van de **beheerde labtypen** te kiezen. Op dit moment is het **leslokaallab** het enige type beheerde lab dat u met Azure Lab Services kunt maken.

De volgende secties bevatten meer informatie over deze labs. 

## <a name="managed-lab-types"></a>Beheerde labtypen
Met Azure Lab Services kunt u labs maken waarvan de infrastructuur wordt beheerd door Azure. In dit artikel worden deze beheerde labs genoemd. Beheerde labs bieden verschillende soorten labs die geschikt zijn voor uw specifieke behoeften. Op dit moment is **leslokaallab** het enige type beheerde lab dat wordt ondersteund. 

Met beheerde labs kunt u meteen aan de slag met een minimale installatie. De service zelf is verantwoordelijk voor het volledige beheer van de infrastructuur voor het lab, van het inrichten van de VM's tot de afhandeling van fouten en het schalen van de infrastructuur.  Als u een beheerd lab, bijvoorbeeld een leslokaallab, wilt maken, moet u eerst een labaccount voor uw organisatie maken. Het lab-account fungeert als het centrale account waarin alle labs in de organisatie worden beheerd. 

Wanneer u Azure-resources in deze beheerde labs maakt en gebruikt, worden deze door de service in interne abonnementen van Microsoft gemaakt en beheerd. Ze worden niet in uw eigen Azure-abonnement gemaakt. De service houdt het gebruik van deze resources bij in de interne Microsoft-abonnementen. Dit gebruik wordt weer in rekening gebracht op uw Azure-abonnement dat het lab-account bevat.   

Hier volgen enkele van de **gebruikscases voor beheerde labtypen**: 

- Bied leerlingen en studenten een lab met virtuele machines die zijn geconfigureerd met exact wat nodig is voor een les of college. Geef elke leerling of student een beperkt aantal uren voor het gebruik van de VM's voor hun huiswerk of persoonlijke projecten.
- Stel een pool in van hoogwaardige reken-VM’s om rekenintensief of grafisch-intensief onderzoek uit te voeren. Voer de virtuele machines uit, indien nodig, en schoon de machines op wanneer u klaar bent. 
- Breng het fysieke computerlab van uw school over naar de cloud. U kunt het aantal VM's automatisch schalen tot de drempelwaarde voor gebruik en kosten die u instelt voor het lab.  
- Richt snel een lab met virtuele machines in voor het hosten van een hackathon. Verwijder het lab met één muisklik als u klaar bent. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelfstudies voor stapsgewijze instructies voor het maken van een labaccount en een leslokaallab.

- [Zelfstudie: een labaccount instellen](tutorial-setup-lab-account.md)
- [Zelfstudie: een leslokaallab maken](tutorial-setup-classroom-lab.md)
