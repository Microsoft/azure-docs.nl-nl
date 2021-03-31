---
title: Testlab-beleids regels beheren in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het definiëren van Lab-beleid, zoals VM-grootten, maximum aantal Vm's per gebruiker en het afsluiten van de automatisering.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a7ada980b2a251b8164f2e1a20f65da54c89ba72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530349"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Alle beleids regels voor een lab in Azure DevTest Labs beheren

Met Azure DevTest Labs kunt u kosten besparen en verspilling in uw Labs minimaliseren door beleids regels (instellingen) voor elk lab te beheren. In dit artikel wordt stapsgewijs uitgelegd hoe u elk beleid kunt instellen.  

## <a name="set-allowed-virtual-machine-sizes"></a>Toegestane grootten van virtuele machines instellen
Het beleid voor het instellen van de toegestane VM-grootten helpt Lab-afval te minimaliseren door u in staat te stellen welke VM-grootten in het lab zijn toegestaan. Als dit beleid is geactiveerd, kunnen alleen VM-grootten uit deze lijst worden gebruikt voor het maken van Vm's.

1. Selecteer in de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)een lab en selecteer vervolgens **configuratie en beleid**.

    ![De configuratie en het beleid van het lab openen](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Selecteer in het deel venster **configuratie en beleid** van de Lab **toegestane grootte van virtuele machines**.
   
    ![Toegestane grootte van virtuele machines](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selecteer aan om dit beleid **in te scha** kelen en **uit** te scha kelen.

1. Als u dit beleid inschakelt, selecteert u een of meer VM-grootten die in uw Lab kunnen worden gemaakt.

1. Selecteer **Opslaan**.

## <a name="set-virtual-machines-per-user"></a>Virtuele machines per gebruiker instellen
Met het beleid voor **virtuele machines per gebruiker** kunt u het aantal vm's opgeven dat door een afzonderlijke gebruiker kan worden gemaakt. Als een gebruiker probeert een virtuele machine te maken of te claimen wanneer aan de limiet van de gebruiker is voldaan, geeft een fout bericht aan dat de virtuele machine niet kan worden gemaakt/geclaimd. 

1. Selecteer **virtuele machines per gebruiker** in het deel venster **configuratie en beleid** van de test omgeving.
   
    ![Virtuele machines per gebruiker](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selecteer **Ja** als u het aantal vm's per gebruiker wilt beperken. Als u het aantal Vm's per gebruiker niet wilt beperken, selecteert u **Nee**. Als u **Ja** selecteert, voert u een numerieke waarde in waarmee het aantal vm's wordt aangegeven dat door een gebruiker kan worden gemaakt of geclaimd. 

1. Selecteer **Ja** als u het aantal vm's wilt beperken dat SSD (Solid-State Disk) kan gebruiken. Als u het aantal Vm's dat SSD mag gebruiken niet wilt beperken, selecteert u **Nee**. Als u **Ja** selecteert, geeft u een waarde op waarmee het aantal vm's wordt aangegeven dat kan worden gemaakt met SSD. 

1. Selecteer **Opslaan**.

## <a name="set-virtual-machines-per-lab"></a>Virtuele machines per Lab instellen
Met het beleid voor **virtuele machines per Lab** kunt u het aantal vm's opgeven dat voor het huidige Lab kan worden gemaakt. Als een gebruiker probeert een virtuele machine te maken wanneer de test limiet is bereikt, wordt in een fout bericht aangegeven dat de virtuele machine niet kan worden gemaakt. 

1. Selecteer **virtuele machines per Lab** in het deel venster **configuratie en beleid** van de test omgeving.
   
    ![Virtuele machines per Lab](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selecteer **Ja** om het aantal vm's per Lab te beperken. Als u het aantal Vm's per Lab niet wilt beperken, selecteert u **Nee**. Als u **Ja** selecteert, voert u een numerieke waarde in waarmee het aantal vm's wordt aangegeven dat door een gebruiker kan worden gemaakt of geclaimd. 

1. Selecteer **Ja** als u het aantal vm's wilt beperken dat SSD (Solid-State Disk) kan gebruiken. Als u het aantal Vm's dat SSD mag gebruiken niet wilt beperken, selecteert u **Nee**. Als u **Ja** selecteert, geeft u een waarde op waarmee het aantal vm's wordt aangegeven dat kan worden gemaakt met SSD. 

1. Selecteer **Opslaan**.

## <a name="set-auto-shutdown"></a>Automatisch afsluiten instellen
Het beleid voor automatisch afsluiten helpt Lab-afval te minimaliseren door de tijd op te geven waarop de Vm's van deze Lab worden afgesloten.

1. Selecteer **automatisch afsluiten** in het deel venster **configuratie en beleid** van de test omgeving.
   
    ![Automatisch afsluiten](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selecteer aan om dit beleid **in te scha** kelen en **uit** te scha kelen.

1. Als u dit beleid inschakelt, geeft u de tijd (en tijd zone) op om alle virtuele machines in het huidige Lab af te sluiten.

1. Geef **Ja** of **Nee** op voor de optie voor het verzenden van een melding 15 minuten vóór de opgegeven tijd voor automatisch afsluiten. Als u **Ja** kiest, voert u het eind punt van de webhook-URL of een e-mail adres in om op te geven waar de melding moet worden gepost of verzonden. De gebruiker ontvangt een melding en krijgt de mogelijkheid om het afsluiten uit te stellen.

   Zie [een webhook of API Azure function maken](../azure-functions/functions-bindings-http-webhook.md)voor meer informatie over webhooks. 

1. Selecteer **Opslaan**.

Wanneer dit beleid is ingeschakeld, wordt standaard toegepast op alle virtuele machines in het huidige lab. Als u deze instelling van een specifieke virtuele machine wilt verwijderen, opent u het deel venster beheer van de virtuele machine en wijzigt u de instelling voor **automatisch afsluiten** .

## <a name="set-auto-shutdown-policy"></a>Beleid voor automatisch afsluiten instellen
Als eigenaar van het lab kunt u een afsluit schema configureren voor alle virtuele machines in uw Lab. Op die manier kunt u kosten besparen van het uitvoeren van machines die niet worden gebruikt (niet-actief). U kunt een afsluit beleid afdwingen op al uw virtuele lab-Vm's, maar ook uw Lab-gebruikers de moeite hebben om een planning in te stellen voor de afzonderlijke machines. Met deze functie kunt u het beleid op uw test schema instellen, te beginnen met het beheer van volledig beheer aan uw Lab-gebruikers. Als eigenaar van het lab kunt u dit beleid configureren door de volgende stappen uit te voeren:

1. Op de start pagina van uw Lab selecteert u **configuratie en beleid**.
2. Selecteer **beleid voor automatisch afsluiten** in de sectie **schema's** van het menu links.
3. Selecteer een van de opties. In de volgende secties vindt u meer informatie over deze opties: het set-beleid is alleen van toepassing op nieuwe virtuele machines die zijn gemaakt in het lab en niet op de bestaande virtuele machines. 

    ![Opties voor automatisch afsluiten van beleid](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Gebruiker stelt een schema in en kan zich afmelden
Als u uw Lab instelt op dit beleid, kunnen de Lab-gebruikers het lab-schema overschrijven of afmelden. Met deze optie krijgen gebruikers van het lab volledig beheer over het automatische afsluit schema van hun Vm's. Lab-gebruikers zien geen wijziging op de pagina planning voor automatisch afsluiten van de VM.

![Optie voor automatisch afsluiten van beleid-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Gebruiker stelt een schema in en kan niet afmelden
Als u uw Lab instelt op dit beleid, kunnen gebruikers met een lab het lab-schema overschrijven. Het beleid voor automatisch afsluiten kan echter niet worden afgemeld. Deze optie zorgt ervoor dat elke machine in uw Lab een schema voor automatisch afsluiten heeft. Gebruikers met een Lab kunnen het schema voor automatisch afsluiten van hun Vm's bijwerken en meldingen voor afsluiten instellen.

![Optie voor automatisch afsluiten van beleid-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>De gebruiker heeft geen controle over de planning die is ingesteld door de Lab-beheerder
Als u uw Lab instelt op dit beleid, kunnen Lab-gebruikers het lab-schema niet overschrijven of afmelden. Deze optie biedt Lab-beheerder de volledige controle over de planning voor elke machine in het lab. Gebruikers met een Lab kunnen alleen meldingen voor automatisch afsluiten instellen voor hun virtuele machines.

![Optie voor automatisch afsluiten van beleid-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Automatisch starten instellen
Met het beleid voor automatisch starten kunt u opgeven wanneer de Vm's in het huidige Lab moeten worden gestart.  

1. Selecteer in het deel venster **configuratie en beleid** van Lab de optie **automatisch starten**.
   
    ![Scherm opname toont de automatische start opties voor een virtuele machine.](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selecteer aan om dit beleid **in te scha** kelen en **uit** te scha kelen.

3. Als u dit beleid inschakelt, geeft u de geplande begin tijd, de tijd zone en de dagen van de week op waarop de tijd van toepassing is. 

4. Selecteer **Opslaan**.

Als deze functie is ingeschakeld, wordt dit beleid niet automatisch toegepast op virtuele machines in het huidige lab. Als u deze instelling wilt Toep assen op een specifieke virtuele machine, opent u het deel venster beheer van de virtuele machine en wijzigt u de instelling voor **automatisch starten** .

## <a name="set-expiration-date"></a>Verval datum instellen
U kunt een verval datum instellen wanneer u [de virtuele machine maakt](devtest-lab-add-vm.md). Kies in **Geavanceerde instellingen** het kalender pictogram om een datum op te geven waarop de virtuele machine automatisch wordt verwijderd. De virtuele machine verloopt standaard nooit.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Wanneer u de verschillende VM-beleids instellingen voor uw Lab hebt gedefinieerd en toegepast, kunt u het volgende proberen:

* Meer [informatie over gedeelde IP-adressen](devtest-lab-shared-ip.md) : hierin wordt uitgelegd hoe gedeelde IP-adressen worden gebruikt in DevTest Labs om het aantal open bare IP-adressen te minimaliseren dat nodig is om verbinding te maken met uw Lab-vm's.
* [Kosten beheer configureren](devtest-lab-configure-cost-management.md) : illustreert hoe u de **maandelijkse geschatte kosten trend** grafiek gebruikt  
  de geschatte kosten voor de huidige maand en de verwachte kosten voor het einde van de maand weer geven.
* [Aangepaste installatie kopie maken](devtest-lab-create-template.md) : wanneer u een virtuele machine maakt, geeft u een basis op. Dit kan een aangepaste installatie kopie of een Marketplace-installatie kopie zijn. In dit artikel wordt beschreven hoe u een aangepaste installatie kopie maakt op basis van een VHD-bestand.
* [Marketplace-installatie kopieën configureren](devtest-lab-configure-marketplace-images.md) -Azure DevTest Labs ondersteunt het maken van vm's op basis van Azure Marketplace-installatie kopieën. In dit artikel wordt beschreven hoe u kunt opgeven welke Azure Marketplace-installatie kopieën kunnen worden gebruikt bij het maken van Vm's in een lab.
* [Een virtuele machine maken in een Lab](devtest-lab-add-vm.md) : illustreert hoe u een virtuele machine maakt op basis van een standaard installatie kopie (een aangepaste of Marketplace) en hoe u met artefacten in uw virtuele machine werkt.
