---
title: Facturerings model voor Azure Active Directory B2C
description: Meer informatie over het facturerings model voor maandelijkse actieve gebruikers (MAU) van Azure AD B2C's, hoe u een Azure AD B2C Tenant koppelt aan een Azure-abonnement en hoe u de juiste prijzen voor de Premium-laag selecteert.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a54ed9dc6557d9b613485bf28e74af0c59fc9e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225205"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Facturerings model voor Azure Active Directory B2C

Prijzen voor Azure Active Directory B2C (Azure AD B2C) zijn gebaseerd op maandelijkse actieve gebruikers (MAU), het aantal unieke gebruikers met verificatie activiteiten binnen een kalender maand. Dit facturerings model is van toepassing op zowel Azure AD B2C tenants als [Azure AD gast gebruikers samenwerking (B2B)](../active-directory/external-identities/external-identities-pricing.md). MAU-facturering helpt u kosten te verlagen door een gratis laag en flexibele, voorspel bare prijzen te bieden. In dit artikel vindt u meer informatie over MAU-facturering, het koppelen van uw Azure AD B2C-tenants aan een abonnement en het wijzigen van de prijs categorie.

> [!IMPORTANT]
> Dit artikel bevat geen prijs informatie. Zie [Azure Active Directory B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor de meest recente informatie over het gebruik van facturering en prijzen. Zie ook [Azure AD B2C regio Beschik baarheid en data locatie](data-residency.md) voor meer informatie over waar de Azure AD B2C-service beschikbaar is en waar de gebruikers gegevens worden opgeslagen.

## <a name="what-do-i-need-to-do"></a>Wat moet ik doen?

Als u gebruik wilt maken van MAU-facturering, moet uw Azure AD B2C-Tenant zijn gekoppeld aan een Azure-abonnement. Mogelijk moet u ook de Azure AD B2C-Tenant overschakelen naar een andere prijs categorie als u Azure AD B2C Premium P2-functies wilt gebruiken, zoals op Risico's gebaseerde voorwaardelijke toegang.

|Als uw Tenant:  |U moet het volgende doen:  |
|---------|---------|
| Een Azure AD B2C-Tenant wordt al gefactureerd op basis van een per-MAU     | Geen actie uitvoeren. Wanneer gebruikers worden geverifieerd bij uw Azure AD B2C-Tenant, wordt u automatisch gefactureerd op basis van het MAU facturerings model.        |
| Een Azure AD B2C-Tenant die nog niet is gekoppeld aan een abonnement     |  [Koppel uw Azure AD B2C-Tenant aan een abonnement](#link-an-azure-ad-b2c-tenant-to-a-subscription) om Mau-facturering te activeren.     |
| Een Azure AD B2C-Tenant die is gekoppeld aan een abonnement vóór 1 november 2019    | [Schakel over naar Mau-facturering (aanbevolen)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)of blijf op het facturerings model per verificatie.     |
| Een Azure AD B2C-Tenant en u Premium-functies wilt gebruiken (zoals op Risico's gebaseerde voorwaardelijke toegang)    | [Ga naar een Azure AD-prijs categorie](#change-your-azure-ad-pricing-tier) die ondersteuning biedt voor de functies die u wilt gebruiken.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Over het facturerings model voor maandelijkse actieve gebruikers (MAU)

MAU-facturering is van kracht geworden voor Azure AD B2C-tenants op **1 November 2019**. Azure AD B2C-tenants die u hebt gemaakt en gekoppeld aan een abonnement op of na die datum, worden per MAU gefactureerd. Als u een Azure AD B2C Tenant hebt die niet is gekoppeld aan een abonnement, moet u dat nu doen. Als u een bestaande Azure AD B2C Tenant hebt die is gekoppeld aan een abonnement vóór 1 november 2019, raden we u aan een upgrade uit te voeren naar het maandelijkse actieve gebruikers (MAU)-facturerings model, of u kunt het facturerings model per verificatie blijven.
  
Uw Azure AD B2C-Tenant moet ook worden gekoppeld aan de juiste prijs categorie van Azure op basis van de functies die u wilt gebruiken. Premium-functies vereisen Azure AD B2C [Premium P1-of P2-prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Mogelijk moet u de prijs categorie bijwerken tijdens het gebruik van nieuwe functies. Als u bijvoorbeeld voorwaardelijke toegang hebt, moet u de prijs categorie Azure AD B2C Premium P2 selecteren voor uw Tenant.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Een Azure AD B2C-Tenant koppelen aan een abonnement

Gebruiks kosten voor Azure Active Directory B2C (Azure AD B2C) worden gefactureerd voor een Azure-abonnement. U moet een Azure AD B2C-Tenant expliciet koppelen aan een Azure-abonnement door een Azure AD B2C *resource* te maken binnen het doel-Azure-abonnement. Verschillende Azure AD B2C resources kunnen worden gemaakt in één Azure-abonnement, samen met andere Azure-resources, zoals virtuele machines, opslag accounts en Logic Apps. U kunt alle resources in een abonnement zien door te gaan naar de Azure Active Directory (Azure AD)-Tenant waaraan het abonnement is gekoppeld.

Een abonnement dat is gekoppeld aan een Azure AD B2C-Tenant kan worden gebruikt voor het factureren van Azure AD B2C gebruik of andere Azure-resources, inclusief aanvullende Azure AD B2C resources. Het kan niet worden gebruikt voor het toevoegen van andere Azure-licentie services of Office 365-licenties in de Azure AD B2C Tenant.

### <a name="prerequisites"></a>Vereisten

* [Azure-abonnement](https://azure.microsoft.com/free/)
* [Azure AD B2C Tenant](tutorial-create-tenant.md) die u wilt koppelen aan een abonnement
  * U moet een Tenant beheerder zijn
  * De Tenant mag niet al zijn gekoppeld aan een abonnement

### <a name="create-the-link"></a>De koppeling maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met het Azure-abonnement dat u wilt gebruiken (*niet* de map die de Azure AD B2C Tenant bevat).
3. Selecteer **een resource maken**, Voer `Active Directory B2C` in het veld **Marketplace doorzoeken** in en selecteer vervolgens **Azure Active Directory B2C**.
4. Selecteer **Maken**.
5. Selecteer **Een bestaande Azure AD B2C-tenant koppelen aan mijn Azure-abonnement**.
6. Selecteer een **Azure AD B2C Tenant** in de vervolg keuzelijst. Alleen tenants waarvoor u een globale beheerder bent en die nog niet aan een abonnement zijn gekoppeld, worden weer gegeven. Het veld **Azure AD B2C resource naam** wordt ingevuld met de domein naam van de Azure AD B2C-Tenant die u selecteert.
7. Selecteer een actief Azure- **abonnement** waarvan u een beheerder bent.
8. Selecteer onder **resource groep** de optie **nieuwe maken** en geef vervolgens de **locatie van de resource groep** op. De instellingen voor de resource groep zijn hier niet van invloed op uw Azure AD B2C Tenant locatie, prestaties of facturerings status.
9. Selecteer **Maken**.

    ![De pagina Azure AD B2C het maken van resources in Azure Portal](./media/billing/portal-01-create-b2c-resource-page.png)

Nadat u deze stappen voor een Azure AD B2C Tenant hebt voltooid, wordt uw Azure-abonnement gefactureerd op basis van uw Azure direct-of Enterprise Overeenkomst-gegevens, indien van toepassing.

## <a name="change-your-azure-ad-pricing-tier"></a>Uw Azure AD-prijs categorie wijzigen

Een Tenant moet worden gekoppeld aan de juiste prijs categorie van Azure op basis van de functies die u wilt gebruiken met uw Azure AD B2C-Tenant. Premium-functies vereisen Azure AD B2C Premium P1 of P2, zoals wordt beschreven in de [Azure Active Directory B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/). In sommige gevallen moet u de prijs categorie bijwerken tijdens het gebruik van nieuwe functies. Als u bijvoorbeeld identiteits beveiliging, op Risico's gebaseerde voorwaardelijke toegang en toekomstige Premium P2-mogelijkheden met Azure AD B2C wilt gebruiken, moet u de prijs categorie Azure AD B2C Premium P2 selecteren voor uw Tenant.

Voer de volgende stappen uit om uw prijs categorie te wijzigen.

1. Meld u aan bij Azure Portal.

2. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die het Azure-abonnement bevat waaraan uw Azure B2C-Tenant is gekoppeld (Selecteer *niet* de Azure AD B2C Tenant zelf).

3. Voer in het zoekvak boven aan de Portal de naam van uw Azure AD B2C-Tenant in. Selecteer vervolgens de Tenant in de zoek resultaten onder **resources**.

4. Selecteer op de pagina **overzicht** van resources onder **prijs categorie** **wijzigen**.

   ![Prijscategorie wijzigen](media/billing/change-pricing-tier.png)
 
5. Selecteer de prijs categorie die de functies bevat die u wilt inschakelen.

   ![De prijs categorie selecteren](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Overschakelen naar MAU-facturering (vóór november 2019 Azure AD B2C-tenants)

Als u vóór **1 November 2019** uw Azure AD B2C-Tenant aan een abonnement hebt gekoppeld, wordt het vorige facturerings model per verificatie gebruikt. U wordt aangeraden om een upgrade uit te voeren naar het maandelijkse actieve gebruikers (MAU)-facturerings model. Facturerings opties worden geconfigureerd in uw Azure AD B2C-resource.

De schakel optie voor facturering van maandelijkse actieve gebruikers (MAU) is **onomkeerbaar**. Nadat u een Azure AD B2C resource hebt geconverteerd naar het MAU-factuur model, kunt u die resource niet herstellen naar het facturerings model per verificatie.

Ga als volgt te werk om de overschakeling naar MAU facturering voor een bestaande Azure AD B2C resource te maken:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als de eigenaar van het abonnement met beheerders toegang tot de Azure AD B2C resource.

2. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de Azure AD B2C Directory die u wilt bijwerken naar Mau-facturering.<br/>

    ![Directory-en abonnements filter in Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)

3. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.

4. Selecteer op de pagina **overzicht** van de Azure AD B2C Tenant de koppeling onder **resource naam**. U wordt omgeleid naar de Azure AD B2C-resource in uw Azure AD-Tenant.<br/>

    ![Azure AD B2C resource koppeling gemarkeerd in Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Selecteer op de pagina **overzicht** van de Azure AD B2C resource onder **factureer bare eenheden** de koppeling **per verificatie (wijzigen in Mau)** .<br/>

    ![Wijziging in de MAU-koppeling is gemarkeerd in Azure Portal](./media/billing/portal-mau-03-change-to-mau-link.png)

6. Selecteer **bevestigen** om de upgrade naar Mau-facturering te volt ooien.<br/>

    ![Bevestigings venster voor facturering op basis van MAU in Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Wat u kunt verwachten wanneer u overstapt naar MAU-facturering vanuit facturering per verificatie

MAU Metering is ingeschakeld zodra u, de eigenaar van het abonnement/de resource, de wijziging bevestigt. Op uw maandelijkse factuur worden de gefactureerde verificatie-eenheden weer gegeven totdat de wijziging en de nieuwe eenheden van MAU beginnen met de wijziging.

Gebruikers hebben geen dubbele telling tijdens de overgangs maand. Unieke actieve gebruikers die zich vóór de wijziging verifiëren, worden in rekening gebracht per verificatie tarief in een kalender maand. Dezelfde gebruikers worden niet opgenomen in de berekening van de MAU voor de rest van de facturerings cyclus van het abonnement. Bijvoorbeeld:

* De contoso B2C-Tenant heeft 1.000 gebruikers. 250 gebruikers zijn in een bepaalde maand actief. De abonnements beheerder verandert van per verificatie voor maandelijkse actieve gebruikers (MAU) op de tiende van de maand.
* Facturering voor 1e tiende wordt gefactureerd met het model per verificatie.
  * Als 100 gebruikers zich aanmelden tijdens deze periode (1 tot en met 10), worden deze gebruikers gemarkeerd als *betaald voor de maand*.
* Facturering van de tiende (de geldigheids duur van de overgang) wordt gefactureerd op basis van het MAU tarief.
  * Als een aanvullend 150-gebruiker zich tijdens deze periode (tiende-30) aanmeldt, wordt alleen de extra 150 gefactureerd.
  * De continue activiteit van de eerste 100-gebruikers heeft geen invloed op de facturering voor de rest van de kalender maand.

Tijdens de facturerings periode van de overgang ziet de eigenaar van het abonnement waarschijnlijk vermeldingen voor beide methoden (per verificatie en per MAU) in hun facturerings overzicht van het Azure-abonnement:

* Een vermelding voor het gebruik tot de datum/tijd van de wijziging die per verificatie weerspiegelt.
* Een vermelding voor het gebruik na de wijziging die maandelijkse actieve gebruikers (MAU) weergeeft.

Zie [Azure Active Directory B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor de meest recente informatie over het factureren van gebruik en de prijzen voor Azure AD B2C.

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Uw Azure AD B2C-Tenant resources beheren

Nadat u de Azure AD B2C resource in een Azure-abonnement hebt gemaakt, ziet u een nieuwe resource van het type ' B2C-Tenant ' weer gegeven met uw andere Azure-resources.

U kunt deze resource gebruiken voor het volgende:

* Navigeer naar het abonnement om facturerings gegevens te bekijken
* De Tenant-ID van de Azure AD B2C Tenant in GUID-indeling ophalen
* Ga naar uw Azure AD B2C-Tenant
* Een ondersteuningsaanvraag indienen
* Uw Azure AD B2C Tenant resource verplaatsen naar een ander Azure-abonnement of een andere resource groep

### <a name="regional-restrictions"></a>Regionale beperkingen

Als u regionale beperkingen hebt ingesteld voor het maken van Azure-resources in uw abonnement, kan deze beperking ertoe leiden dat u de Azure AD B2C resource niet kunt maken.

Om dit probleem te verhelpen, kunt u uw regionale beperkingen versoepelen.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure Cloud Solution Providers (CSP)-abonnementen

Azure Cloud Solution Providers (CSP)-abonnementen worden ondersteund in Azure AD B2C. De functionaliteit is beschikbaar via Api's of de Azure Portal voor Azure AD B2C en voor alle Azure-resources. Beheerders van CSP-abonnementen kunnen relaties met Azure AD B2C koppelen, verplaatsen en verwijderen, net als bij andere Azure-resources.

Het beheer van Azure AD B2C met op rollen gebaseerd toegangs beheer wordt niet beïnvloed door de koppeling tussen de Azure AD B2C Tenant en een Azure CSP-abonnement. Toegangs beheer op basis van rollen wordt bereikt door gebruik te maken van op tenants gebaseerde rollen, niet op abonnementen gebaseerde rollen.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Het facturerings abonnement voor de Azure AD B2C-Tenant wijzigen

### <a name="move-using-azure-resource-manager"></a>Verplaatsen met behulp van Azure Resource Manager

Azure AD B2C-tenants kunnen worden verplaatst naar een ander abonnement met behulp van Azure Resource Manager als de bron-en doel abonnementen binnen dezelfde Azure Active Directory Tenant bestaan.

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie over het verplaatsen van Azure-resources, zoals uw Azure AD B2C-Tenant naar een ander abonnement.

Lees voordat u begint met verplaatsen het hele artikel om de beperkingen en vereisten voor een dergelijke verplaatsing volledig te begrijpen. Naast instructies voor het verplaatsen van resources bevat deze essentiële informatie zoals een controle lijst voorafgaand aan het verplaatsen en het valideren van de verplaatsings bewerking.

### <a name="move-by-unlinking-and-relinking"></a>Verplaatsen door de koppeling te verwijderen en opnieuw te koppelen

Als de bron-en doel abonnementen zijn gekoppeld aan verschillende Azure Active Directory-tenants, kunt u de stap niet uitvoeren via Azure Resource Manager zoals hierboven wordt beschreven. U kunt echter wel hetzelfde resultaat krijgen door de Azure AD B2C Tenant van het bron abonnement te ontkoppelen en deze opnieuw te koppelen aan het doel abonnement. Deze methode is veilig omdat het enige object dat u verwijdert de *facturerings koppeling* is, niet de Azure AD B2C Tenant zelf. Geen van de gebruikers, apps, gebruikers stromen enz. worden beïnvloed.

1. Vanuit de Azure AD B2C Directory zelf [een gast gebruiker uitnodigen](user-overview.md#guest-user) vanuit de Azure AD-doel-Tenant (waarmee het doel-Azure-abonnement is gekoppeld) en ervoor zorgen dat deze gebruiker de rol **globale beheerder** heeft in azure AD B2C.
1. Ga naar de *Azure-resource* die Azure AD B2C in uw bron-Azure-abonnement vertegenwoordigt, zoals wordt uitgelegd in het gedeelte [uw Azure AD B2C Tenant resources beheren](#manage-your-azure-ad-b2c-tenant-resources) . Schakel niet over naar de daad werkelijke Azure AD B2C Tenant.
1. Selecteer de knop **verwijderen** op de pagina **overzicht** . Hiermee worden de verwante Azure AD B2C gebruikers of toepassingen van de Tenant *niet* verwijderd. De facturerings koppeling wordt alleen uit het bron abonnement verwijderd.
1. Meld u aan bij de Azure Portal met het gebruikers account dat is toegevoegd als beheerder in Azure AD B2C in stap 1. Ga vervolgens naar het doel-Azure-abonnement dat is gekoppeld aan de doel-Azure Active Directory Tenant. 
1. Stel de facturerings koppeling in het doel abonnement opnieuw in door de bovenstaande procedure [koppeling maken](#create-the-link) te volgen.
1. Uw Azure AD B2C resource is nu verplaatst naar het bestemmings-Azure-abonnement (gekoppeld aan het doel Azure Active Directory) en wordt nu via dit abonnement gefactureerd.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor de meest recente prijs informatie.