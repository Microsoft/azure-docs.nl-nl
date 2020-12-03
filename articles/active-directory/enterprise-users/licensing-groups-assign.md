---
title: Licenties toewijzen aan een groep-Azure Active Directory | Microsoft Docs
description: Licenties toewijzen aan gebruikers met behulp van Azure Active Directory groeps licenties
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c1a6957d347681f1c7dd9979429fffb153ccb39
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546604"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Licenties toewijzen aan gebruikers per groepslid maatschap in Azure Active Directory

In dit artikel vindt u instructies voor het toewijzen van product licenties aan een groep gebruikers en het controleren of ze correct zijn gelicentieerd in Azure Active Directory (Azure AD).

In dit voor beeld bevat de Azure AD-organisatie een beveiligings groep met de naam **HR Department**. Deze groep bevat alle leden van de afdeling Human Resources (rond 1.000 gebruikers). U wilt Office 365 Enter prise E3-licenties toewijzen aan de hele afdeling. De Yammer Enter prise-service die is opgenomen in het product moet tijdelijk worden uitgeschakeld totdat de afdeling klaar is om deze te gebruiken. U wilt ook Enterprise Mobility + Security-licenties implementeren voor dezelfde groep gebruikers.

> [!NOTE]
> Sommige services van Microsoft zijn niet op alle locaties beschikbaar. Voordat een licentie aan een gebruiker kan worden toegewezen, moet de beheerder de eigenschap gebruiks locatie opgeven voor de gebruiker.
>
> Voor het toewijzen van een groeps licentie neemt de locatie van de map over van alle gebruikers waarvoor een gebruiks locatie is opgegeven. Als u gebruikers op meerdere locaties hebt, raden we u aan om de gebruiks locatie altijd in te stellen als onderdeel van de stroom voor het maken van een gebruiker in azure AD (bijvoorbeeld via AAD Connect-configuratie). Dit zorgt ervoor dat het resultaat van licentie toewijzing altijd juist is en dat gebruikers geen services ontvangen op locaties die niet zijn toegestaan.

## <a name="step-1-assign-the-required-licenses"></a>Stap 1: de vereiste licenties toewijzen

1. Meld u aan bij het [**Azure AD-beheer centrum**](https://aad.portal.azure.com) met een licentie beheerders account. Voor het beheren van licenties moet het account een licentie beheerder, gebruikers beheerder of globale beheerder zijn.

1. Selecteer **licenties** om een pagina te openen waar u alle licentie-producten in de organisatie kunt zien en beheren.

1. Onder **alle producten** selecteert u zowel Office 365 Enter prise E5 als Enterprise Mobility + Security E3 door de product namen te selecteren. Als u de toewijzing wilt starten, selecteert u boven aan de pagina **toewijzen** .

   ![Producten selecteren om licenties toe te wijzen](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Selecteer op de pagina **licentie toewijzen** de optie **gebruikers en groepen** om een lijst met gebruikers en groepen te openen.

1. Selecteer een gebruiker of groep en gebruik vervolgens de knop **selecteren** onder aan de pagina om uw selectie te bevestigen.

1. Klik op de pagina **licentie toewijzen** op **toewijzings opties**, waarin alle service plannen worden weer gegeven die zijn opgenomen in de twee producten die u eerder hebt geselecteerd. Zoek **Yammer Enter prise** **en schakel deze uit om de** service uit te scha kelen via de product licentie. Bevestig door te klikken op **OK** onder aan de **licentie opties**.

   ![Selecteer service plannen voor licenties](./media/licensing-groups-assign/assignment-options.png)
  
1. Als u de toewijzing wilt volt ooien, klikt u op de pagina **licentie toewijzen** op **toewijzen** onder aan de pagina.

1. Er wordt een melding weer gegeven in de rechter bovenhoek met daarin de status en het resultaat van het proces. Als de toewijzing aan de groep niet kan worden voltooid (bijvoorbeeld omdat er al bestaande licenties in de groep zijn), klikt u op de melding om de details van de fout weer te geven.

Wanneer u licenties aan een groep toewijst, worden alle bestaande leden van die groep door Azure AD verwerkt. Dit proces kan enige tijd duren, variërend van de grootte van de groep. In de volgende stap wordt beschreven hoe u kunt controleren of het proces is voltooid en bepalen of er verdere aandacht is vereist om problemen op te lossen.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Stap 2: controleren of de eerste toewijzing is voltooid

1. Ga naar **Azure Active Directory**  >  **groepen**. Selecteer de groep waaraan de licenties zijn toegewezen.

1. Selecteer op de pagina groep de optie **licenties**. Zo kunt u snel controleren of licenties volledig zijn toegewezen aan gebruikers en of er fouten zijn die u moet zoeken. De volgende informatie is beschikbaar:

   - Service licenties die momenteel aan de groep zijn toegewezen. Selecteer een vermelding om de specifieke services weer te geven die zijn ingeschakeld en om wijzigingen aan te brengen.

   - Status updates van de nieuwste licentie wijzigingen, die beschikbaar zijn als de wijzigingen worden verwerkt of als de verwerking voor alle gebruikers leden is voltooid.

   - Informatie over gebruikers licentie toewijzingen met een fout status.

   ![licentie fouten en licentie status](./media/licensing-groups-assign/assignment-errors.png)

1. Meer gedetailleerde informatie over de licentie verwerking onder **Azure Active Directory**  >  **gebruikers en groepen**  >  *groeps naam*  >  **controle logboeken**. Controleer de volgende activiteiten:

   - Activiteit: `Start applying group based license to users` . Dit wordt geregistreerd wanneer het systeem de wijziging van de licentie toewijzing ophaalt voor de groep en op alle gebruikers leden wordt toegepast. Het bevat informatie over de wijziging die is aangebracht.

   - Activiteit: `Finish applying group based license to users` . Dit wordt geregistreerd wanneer het systeem klaar is met de verwerking van alle gebruikers in de groep. Het bevat een samen vatting van het aantal gebruikers dat is verwerkt en aan hoeveel gebruikers geen groeps licenties kunnen worden toegewezen.

   [Lees deze sectie](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) voor meer informatie over hoe audit logboeken kunnen worden gebruikt voor het analyseren van wijzigingen die zijn aangebracht door op groep gebaseerde licentie verlening.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Stap 3: controleren op licentie problemen en deze oplossen

1. Ga naar **Azure Active Directory**  >  **groepen** en zoek de groep waaraan de licenties zijn toegewezen.
1. Selecteer op de pagina groep de optie **licenties**. In de melding boven op de pagina ziet u dat er 10 gebruikers zijn waaraan geen licenties kunnen worden toegewezen. Open het om een lijst met alle gebruikers met een licentie fout status voor deze groep weer te geven.
1. In de kolom **Mislukte toewijzingen** wordt aangegeven dat beide product licenties niet aan de gebruikers kunnen worden toegewezen. De **eerste reden voor de fout** kolom bevat de oorzaak van de fout. In dit geval zijn er **conflicterende service plannen**.

   ![licenties die niet kunnen worden toegewezen](./media/licensing-groups-assign/failed-assignments.png)

1. Selecteer een gebruiker om de pagina **licenties** van de gebruiker te openen. Op deze pagina worden alle licenties weer gegeven die momenteel aan de gebruiker zijn toegewezen. In dit voor beeld heeft de gebruiker de Office 365 Enter prise E1-licentie die is overgenomen van de **kiosk gebruikers** groep. Dit is in conflict met de E3-licentie die het systeem heeft geprobeerd toe te passen vanuit de **HR-afdelings** groep. Als gevolg hiervan zijn geen van de licenties van die groep toegewezen aan de gebruiker.

   ![Alle licentie conflicten voor een gebruiker weer geven](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Als u dit conflict wilt oplossen, verwijdert u de gebruiker uit de groep **kiosk gebruikers** . Nadat de wijziging door Azure AD is verwerkt, worden de **HR-afdelings** licenties correct toegewezen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over de functieset voor licentie toewijzing met behulp van groepen:

- [Wat is op een groep gebaseerde licentie verlening in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Gebruikers met een afzonderlijke licentie migreren naar licenties op basis van groepen in Azure Active Directory](licensing-groups-migrate-users.md)
- [Gebruikers tussen product licenties migreren met op groepen gebaseerde licentie verlening in Azure Active Directory](licensing-groups-change-licenses.md)
- [Aanvullende scenario’s voor Azure Active Directory-licenties op basis van groepen](licensing-group-advanced.md)
- [Power shell-voor beelden voor op groep gebaseerde licentie verlening in Azure Active Directory](licensing-ps-examples.md)