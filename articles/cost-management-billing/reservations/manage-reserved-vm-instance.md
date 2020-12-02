---
title: Azure-reserveringen beheren
description: Ontdek hoe u Azure-reserveringen kunt beheren. Bekijk stappen om het reserveringsbereik te wijzigen, een reservering te splitsen en het gebruik van een reservering te optimaliseren.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 050984d58137ec03996572d2de41115073e4ab2b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338160"
---
# <a name="manage-reservations-for-azure-resources"></a>Reserveringen voor Azure-resources beheren

Nadat u een Azure-reservering hebt gekocht, moet u de reservering mogelijk toepassen op een ander abonnement, aanpassen wie de reservering kan beheren of het bereik van de reservering wijzigen. U kunt een reservering ook opsplitsen in twee reserveringen om sommige van de exemplaren die u hebt gekocht, toe te passen op een ander abonnement.

Als u voor Azure gereserveerde virtuele machine-instanties hebt aangeschaft, kunt u de optimalisatie-instelling voor de reservering wijzigen. De reserveringskorting kan worden toegepast op virtuele machines in dezelfde serie, maar u kunt ook datacentercapaciteit voor een specifieke VM-grootte reserveren. Probeer ook reserveringen te optimaliseren, zodat deze volledig worden gebruikt.

*De machtiging die is vereist om een reservering te beheren is afzonderlijk van abonnementsmachtigingen.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Reserveringsorder en reservering

Wanneer u een reservering aanschaft, worden twee objecten gemaakt: een **reserveringsorder** en een **reservering**.

Op het moment van aankoop staat er één reservering onder een reserveringsorder. Door bepaalde acties, zoals splitsen, samenvoegen, gedeeltelijke restitutie of uitwisselen, worden nieuwe reserveringen gemaakt onder de **reserveringsorder**.

Als u een reserveringsorder wilt zien, gaat u naar **Reserveringen**, selecteert u de reservering en klikt u vervolgens op de **reserveringsorder-id**.

![Voorbeeld van details van een reserveringsorder, met reserveringsorder-id ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Voor een reservering worden de machtigingen van de bijbehorende reserveringsorder overgenomen.

## <a name="change-the-reservation-scope"></a>Het reserveringsbereik wijzigen

 Uw reserveringskorting is van toepassing op virtuele machines, SQL-databases, Azure Cosmos DB of andere resources die overeenkomen met uw reservering en die in het reserveringsbereik worden uitgevoerd. De factureringscontext hangt af van het abonnement dat is gebruikt om de reservering aan te schaffen.

U kunt het bereik van een reservering als volgt wijzigen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer de reservering.
4. Selecteer **Instellingen** > **Configuratie**.
5. Wijzig het bereik.

Als u overschakelt van Gedeeld naar Eén bereik, kunt u alleen abonnementen selecteren waarvan u de eigenaar bent. Alleen abonnementen binnen dezelfde factureringscontext als de reservering kunnen worden geselecteerd.

Het bereik is alleen van toepassing op afzonderlijke abonnementen met tarieven voor betalen per gebruik (de aanbieding MS-AZR-0003P of MS-AZR-0023P), de Enterprise-aanbieding MS-AZR-0017P of MS-AZR-0148P of CSP-abonnementstypen.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Gebruikers toevoegen of wijzigen die een reservering kunnen beheren

U kunt het reserveringsbeheer delegeren door personen toe te voegen aan rollen voor de reserveringsbestelling of de reservering. De standaardinstelling is dat de persoon die de reservering heeft besteld en de accountbeheerder de rol van eigenaar hebben voor de reserveringsbestelling en de reservering.

U kunt de toegang tot reserveringsorders en reserveringen beheren, *onafhankelijk van de abonnementen* waarvoor de reserveringskorting geldt. Wanneer u iemand een machtiging geeft om een reserveringsorder of de reservering te beheren, geeft u die persoon hiermee geen machtiging om ook het abonnement te beheren. Evenzo is iemand die van u een machtiging krijgt om een abonnement in het bereik van de reservering te beheren, ook niet gemachtigd om de reserveringsorder of de reservering te beheren.

Als de gebruiker een uitwisseling of een restitutie wil uitvoeren, moet deze persoon toegang hebben tot de reserveringsorder. Wanneer u machtigingen aan iemand toewijst, kunt u het beste machtigingen voor de reserveringsorder geven, niet voor de reservering.

U kunt het toegangsbeheer voor een reservering als volgt delegeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Alle services** > **Reservering** om de reserveringen te bekijken waartoe u toegang hebt.
3. Selecteer de reservering waarvoor u de toegang wilt delegeren aan andere gebruikers.
4. Klik op **Toegangsbeheer (IAM)** .
5. Selecteer **Roltoewijzing toevoegen** > **Rol** > **Eigenaar**. Als u beperkte toegang wilt geven, selecteert u een andere rol.
6. Typ het e-mailadres van de gebruiker die u als eigenaar wilt toevoegen.
7. Selecteer de gebruiker en selecteer vervolgens **Opslaan**.

## <a name="split-a-single-reservation-into-two-reservations"></a>Eén reservering in twee reserveringen splitsen

 Nadat u meer dan één resource-instantie in een reservering hebt aangeschaft, kunt u ervoor kiezen instanties in die reservering aan verschillende abonnementen toe te wijzen. Standaard hebben alle instanties één bereik: Eén abonnement, Resourcegroep of Gedeeld. Stel, u hebt een reservering voor 10 VM-instanties aangeschaft en abonnement A als bereik opgegeven. Nu wilt u dat wijzigen in 7 instanties voor abonnement A en 3 instanties voor abonnement B. U kunt dit doen door een reservering te splitsen. Nadat u een reservering hebt gesplitst, wordt de oorspronkelijke reserverings-id geannuleerd en worden er twee nieuwe reserveringen gemaakt. Splitsen heeft geen invloed op de reserveringsorder: een splitsing is geen nieuwe commerciële transactie en de nieuwe reserveringen hebben dezelfde einddatum als de gesplitste reservering.

 U kunt een reservering in twee reserveringen splitsen via PowerShell, CLI of de API.

### <a name="split-a-reservation-by-using-powershell"></a>Een reservering splitsen met behulp van PowerShell

1. Voer de volgende opdracht uit om de reserveringsorder-id op te halen:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Haal de gegevens van een reservering op:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Splits de reservering in twee delen en distribueer de instanties:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Voer de volgende opdracht uit om het bereik bij te werken:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>De optimalisatie-instelling wijzigen voor gereserveerde VM-instanties

 Wanneer u een gereserveerde VM-instantie aanschaft, kunt u de flexibiliteit of de capaciteit van de instantiegrootte prioriteit geven. De flexibiliteit van de instantiegrootte is van toepassing op de reserveringskorting op andere virtuele machines in de [groep met VM’s met dezelfde grootte](../../virtual-machines/reserved-vm-instance-size-flexibility.md). Met de capaciteitsprioriteit wordt prioriteit toegekend aan de datacentercapaciteit voor uw implementaties. Deze optie biedt u extra vertrouwen dat u de VM-instanties kunt starten op het moment dat u ze nodig hebt.

Wanneer het bereik van de reservering Gedeeld is, is de flexibiliteit van de instantiegrootte ingeschakeld. De datacentercapaciteit heeft geen prioriteit voor VM-implementaties.

Voor reserveringen met het bereik Enkelvoudig kunt u de reservering optimaliseren voor de capaciteitsprioriteit in plaats van flexibiliteit van de VM-instantiegrootte.

U kunt de optimalisatie-instelling voor de reservering als volgt bijwerken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer de reservering.
4. Selecteer **Instellingen** > **Configuratie**.  
  ![Voorbeeld met het configuratie-item](./media/manage-reserved-vm-instance/add-product03.png)
5. Wijzig de instelling **Optimaliseren voor**.  
  ![Voorbeeld met de instelling Optimaliseren voor](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Gebruik van reservering optimaliseren

Besparingen voor Azure-reserveringen zijn alleen mogelijk bij duurzaam gebruik van resources. Wanneer u een reservering aanschaft, betaalt u voor wat in feite 100% mogelijk resourcegebruik is voor een termijn van één of drie jaar. Probeer uw reservering te maximaliseren voor zo veel mogelijk gebruik en besparingen. In de volgende secties wordt uitgelegd hoe u een reservering bewaakt en het gebruik hiervan optimaliseert.

### <a name="view-reservation-use-in-the-azure-portal"></a>Gebruik van reserveringen weergeven in Azure Portal

Een van de manieren om het gebruik van reserveringen weer te geven, is via Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Alle services** > [**Reserveringen**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) en bekijk het **Percentage gebruik** voor een reservering.  
  ![Afbeelding met de lijst met reserveringen](./media/manage-reserved-vm-instance/reservation-list.png)
3. Selecteer een reservering.
4. Controleer de trend van het gebruik van deze reservering over een bepaalde tijd.  
  ![Afbeelding met het gebruik van de reservering ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Het gebruik van de reservering weergeven met een API

Als u een EA-klant (Enterprise Agreement) bent, kunt u programmatisch zien hoe de reserveringen in uw organisatie worden gebruikt. U krijgt ongebruikte reserveringen via gebruiksgegevens. Wanneer u de reserveringskosten controleert, moet u er rekening mee houden dat gegevens worden verdeeld in daadwerkelijke kosten en afgeschreven kosten. De daadwerkelijke kosten bieden gegevens waarmee u uw maandelijkse factuur kunt afstemmen. Ook worden hiertoe de aanschafkosten van de reservering en details over reserveringstoepassingen gerekend. De afgeschreven kosten zijn vergelijkbaar met de daadwerkelijke kosten, met de uitzondering dat vooraf een tarief voor de effectieve prijs voor gebruik van de reserveringen wordt bepaald. Niet-gebruikte gereserveerde uren worden weergegeven bij de gegevens over de afgeschreven kosten. Zie [Kosten en gebruik van Enterprise Agreement-reserveringen ophalen](understand-reserved-instance-usage-ea.md) voor meer informatie over gebruiksgegevens voor EA-klanten.

Gebruik de API [Samenvattingen van reserveringen - Lijst op reserveringsorder en reservering](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation) voor andere abonnementstypen.

### <a name="optimize-your-reservation"></a>De reservering optimaliseren

Als u merkt dat de reserveringen van uw organisatie niet voldoende worden gebruikt:

- Zorg ervoor dat de virtuele machines die in uw organisatie worden gemaakt, overeenkomen met de VM-grootte die voor de reservering is bedoeld.
- Zorg ervoor dat Flexibiliteit van de instantiegrootte is ingeschakeld. Zie [Reserveringen beheren - De optimalisatie-instelling wijzigen voor gereserveerde VM-instanties](#change-optimize-setting-for-reserved-vm-instances) voor meer informatie.
- Wijzig het bereik van de reservering in _Gedeeld_ voor een breder toepassingsgebied. Zie [Het bereik voor een reservering wijzigen](#change-the-reservation-scope) voor meer informatie.
- U kunt ook de niet-gebruikte hoeveelheid uitwisselen. Zie [Annuleringen en uitwisselingen](#cancel-exchange-or-refund-reservations) voor meer informatie.


## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn reserveringen voor Azure?](save-compute-costs-reservations.md)

Een serviceabonnement kopen:
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Vooruitbetalen voor compute-resources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Vooruitbetalen voor Azure Cosmos DB-resources met gereserveerde capaciteit van Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)

Een softwareabonnement kopen:
- [Vooruitbetalen voor Red Hat-software-abonnementen vanuit Azure Reservations](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Vooruitbetalen voor SUSE-software-abonnementen vanuit Azure Reservations](../../virtual-machines/linux/prepay-suse-software-charges.md)

Inzicht in kortingen en gebruik:
- [Inzicht in de manier waarop de korting voor VM-reserveringen wordt toegepast](../manage/understand-vm-reservation-charges.md)
- [Inzicht in de manier waarop de korting voor het Red Hat Enterprise Linux-softwareabonnement wordt toegepast](understand-rhel-reservation-charges.md)
- [Inzicht in de manier waarop de korting voor het SUSE Linux Enterprise-softwareabonnement wordt toegepast](understand-suse-reservation-charges.md)
- [Inzicht in de manier waarop andere reserveringskortingen worden toegepast](understand-reservation-charges.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
- [Kosten van Windows-software zijn niet inbegrepen bij reserveringen](reserved-instance-windows-software-costs.md)