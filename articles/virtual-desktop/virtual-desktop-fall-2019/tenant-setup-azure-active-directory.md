---
title: Een tenant maken in Windows Virtual Desktop (klassiek) - Azure
description: Hier wordt beschreven hoe u Windows Virtual Desktop (klassiek)-tenants instelt in Azure Active Directory.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b2421c8b8c2d74649084f1cc8b75160857f40fc1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444323"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Zelfstudie: Een tenant maken in Windows Virtual Desktop (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten.

Het maken van een tenant in Windows Virtual Desktop vormt de eerste stap voor het bouwen van uw bureaubladvirtualisatieoplossing. Een tenant is een groep van een of meer hostgroepen. Elke hostgroep bestaat uit meerdere sessiehosts die worden uitgevoerd als virtuele machines in Azure en zijn geregistreerd bij de Windows Virtual Desktop-service. Elke hostgroep bestaat ook uit een of meer app-groepen die worden gebruikt voor het publiceren van extern-bureaublad- en externe toepassingsbronnen voor gebruikers. Met een tenant kunt u hostgroepen maken, app-groepen maken, gebruikers toewijzen en verbindingen via de service maken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure Active Directory-machtigingen toekennen aan de Windows Virtual Desktop-service.
> * De toepassingsrol TenantCreator toewijzen aan een gebruiker in uw Azure Active Directory-tenant.
> * Een Windows Virtual Desktop-tenant maken.

## <a name="what-you-need-to-set-up-a-tenant"></a>Wat u nodig hebt om een tenant in te stellen

Voordat u begint met het instellen van uw Windows Virtual Desktop-tenant, moet u controleren of u over het volgende beschikt:

* De [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)-tenant-id voor gebruikers van Windows Virtual Desktop.
* Een globaal beheerdersaccount binnen de Azure Active Directory-tenant.
   * Dit geldt ook voor CSP-organisaties (Cloud Solution Provider) die een Windows Virtual Desktop-tenant voor hun klanten maken. Als u zich in een CSP-organisatie bevindt, moet u zich kunnen aanmelden als globale beheerder van het Azure Active Directory-exemplaar van de klant.
   * Het beheerdersaccount moet afkomstig zijn uit de Azure Active Directory-tenant waarin u de Windows Virtual Desktop-tenant probeert te maken. Dit proces biedt geen ondersteuning voor Azure Active Directory B2B-(gast)accounts.
   * Het beheerdersaccount moet een werk- of schoolaccount zijn.
* Een Azure-abonnement.

U moet beschikken over de tenant-id, het globale beheerdersaccount en het Azure-abonnement zodat het proces dat in deze zelfstudie wordt beschreven goed kan werken.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Machtigingen verlenen aan Windows Virtual Desktop

Als u al machtigingen hebt verkregen voor Windows Virtual Desktop voor dit exemplaar van Azure Active Directory, kunt u deze sectie overslaan.

Door machtigingen te verlenen aan Windows Virtual Desktop kan het query’s uitvoeren op Azure Active Directory voor administratieve en eindgebruikerstaken.

Ga als volgt te werk om de servicemachtigingen te verlenen:

1. Open een browser en start de werkstroom voor beheerderstoestemming naar de [server-app van Windows Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Als u een klant beheert en beheerderstoestemming wilt verlenen voor de directory van de klant, voert u de volgende URL in de browser in en vervangt u {tenant} door de naam van het Azure AD-domein van de klant. Als de organisatie van de klant bijvoorbeeld de Azure AD-domeinnaam contoso.onmicrosoft.com heeft geregistreerd, vervangt u {tenant} door contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Meld u aan bij de toestemmingspagina van Windows Virtual Desktop met een globaal beheerdersaccount. Als u bijvoorbeeld met de Contoso-organisatie werkt, kan uw account admin@contoso.com of admin@contoso.onmicrosoft.com zijn.
3. Selecteer **Accepteren**.
4. Wacht een minuut zodat Azure AD toestemming kan geven.
5. Open een browser en start de werkstroom voor beheerderstoestemming naar de [client-app van Windows Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Als u een klant beheert en beheerderstoestemming wilt verlenen voor de directory van de klant, voert u de volgende URL in de browser in en vervangt u {tenant} door de naam van het Azure AD-domein van de klant. Als de organisatie van de klant bijvoorbeeld de Azure AD-domeinnaam contoso.onmicrosoft.com heeft geregistreerd, vervangt u {tenant} door contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Meld u bij de toestemmingspagina van Windows Virtual Desktop als een globale beheerder aan, zoals u in stap 2 hebt gedaan.
7. Selecteer **Accepteren**.

## <a name="assign-the-tenantcreator-application-role"></a>De toepassingsrol TenantCreator toewijzen

Door een Azure Active Directory-gebruiker de toepassingsrol TenantCreator toe te wijzen, kan die gebruiker een Windows Virtual Desktop-tenant maken die is gekoppeld aan het Azure Active Directory exemplaar. U moet uw globale beheerdersaccount gebruiken om de TenantCreator-rol toe te wijzen.

Ga als volgt te werk om de toepassingsrol TenantCreator toe te wijzen:

1. Ga naar de [Azure portal](https://portal.azure.com) om de toepassingsrol TenantCreator te beheren. Zoek en selecteer **Bedrijfstoepassingen**. Als u met meerdere Azure Active Directory tenants werkt, kunt u het beste een persoonlijke browsersessie openen en de URL's kopiëren en in de adresbalk plakken.

   > [!div class="mx-imgBorder"]
   > ![Schermopname van het zoeken naar bedrijfstoepassingen in Azure Portal](../media/azure-portal-enterprise-applications.png)

2. Zoek binnen **Bedrijfstoepassingen** naar **Windows Virtual Desktop**. U ziet de twee toepassingen waarvoor u in de vorige sectie toestemming hebt gegeven. Van deze twee toepassingen selecteert u **Windows Virtual Desktop**.

   > [!div class="mx-imgBorder"]
   > ![Een schermopname van de zoek resultaten bij het zoeken naar 'Windows Virtual Desktop' in 'Bedrijfstoepassingen'. De app met de naam 'Windows Virtual Desktop' is gemarkeerd.](../media/tenant-enterprise-app.png)

3. Selecteer **Gebruikers en groepen**. Mogelijk ziet u dat de beheerder die toestemming heeft verleend voor de toepassing al wordt vermeld met de rol **Standaardtoegang** toegewezen. Dit is niet voldoende voor het maken van een Windows Virtual Desktop-tenant. Ga verder met het volgen van deze instructies om de **TenantCreator**-rol aan een gebruiker toe te voegen.

   > [!div class="mx-imgBorder"]
   > ![Een schermopname van de gebruikers en groepen die zijn toegewezen voor het beheren van de bedrijfstoepassing 'Windows Virtual Desktop'. Op de schermopname ziet u slechts één toewijzing, die is ingesteld op 'Standaardtoegang'.](../media/tenant-default-access.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** op het tabblad **Toewijzing toevoegen**.
5. Zoek naar een gebruikersaccount dat uw Windows Virtual Desktop-tenant maakt. Voor het gemak kan dit het globale beheerdersaccount zijn.
   - Als u een Microsoft Identity Provider als contosoadmin@live.com of contosoadmin@outlook.com gebruikt, kunt u zich mogelijk niet aanmelden bij Windows Virtual Desktop. U kunt het beste een domeinspecifiek account gebruiken, zoals admin@contoso.com of admin@contoso.onmicrosoft.com.

   > [!div class="mx-imgBorder"]
   > ![Een schermopname van het selecteren van een gebruiker om toe te voegen als 'TenantCreator'.](../media/tenant-assign-user.png)

   > [!NOTE]
   > U moet een gebruiker (of een groep die een gebruiker bevat) selecteren die afkomstig is uit dit Azure Active Directory-exemplaar. U kunt geen gastgebruiker (B2B) of een service-principal kiezen.

6. Selecteer het gebruikersaccount, kies de knop **Selecteren** en selecteer **Toewijzen**.
7. Controleer op de pagina **Windows Virtual Desktop - Gebruikers en groepen** of er een nieuw item wordt weergegeven met de rol **TenantCreator** toegewezen aan de gebruiker die de Windows Virtual Desktop-tenant gaat maken.

   > [!div class="mx-imgBorder"]
   > ![Een schermopname van de gebruikers en groepen die zijn toegewezen voor het beheren van de bedrijfstoepassing 'Windows Virtual Desktop'. De schermopname bevat nu een tweede vermelding van een gebruiker die is toegewezen aan de rol 'TenantCreator'.](../media/tenant-tenant-creator-added.png)

Voordat u doorgaat met het maken van uw Windows Virtual Desktop-tenant, hebt u twee gegevens nodig:

   - Uw tenant-id van Azure Active Directory (of **Directory-id**)
   - De id van uw Azure-abonnement

Ga als volgt te werk om uw tenant-id van Azure Active Directory (of **Directory-id**) op te zoeken:
1. Zoek en selecteer in dezelfde sessie van [Azure-portal](https://portal.azure.com) de optie **Azure Active Directory**.

   > [!div class="mx-imgBorder"]
   > ![Een schermopname van de zoekresultaten voor 'Azure Active Directory' in Azure Portal. Het zoekresultaat onder 'Services' wordt gemarkeerd.](../media/tenant-search-azure-active-directory.png)

2. Schuif omlaag totdat u de optie **Eigenschappen** vindt en selecteer deze.
3. Zoek naar **Directory-id** en selecteer vervolgens het klembordpictogram. Plak deze op een handige locatie, zodat u deze later kunt gebruiken als de waarde voor **AadTenantId**.

   > [!div class="mx-imgBorder"]
   > ![Een schermopname van de Azure Active Directory-eigenschappen. De muisaanwijzer wijst het klembordpictogram voor 'Directory-id' aan om te kopiëren en plakken.](../media/tenant-directory-id.png)

Ga als volgt te werk om uw Azure-abonnements-id op te zoeken:
1. Zoek en selecteer **Abonnementen** in dezelfde sessie van [Azure-portal](https://portal.azure.com).

   > [!div class="mx-imgBorder"]
   > ![Een schermopname van de zoekresultaten voor 'Azure Active Directory' in Azure Portal. Het zoekresultaat voor "Services" is gemarkeerd.](../media/tenant-search-subscription.png)

2. Selecteer het Azure-abonnement dat u wilt gebruiken om servicemeldingen voor Windows Virtual Desktop te ontvangen.
3. Zoek naar **Abonnement-id** en beweeg de muisaanwijzer over de waarde totdat er een klembordpictogram wordt weergegeven. Selecteer het klembordpictogram en plak het op een handige locatie, zodat u het later kunt gebruiken als de waarde voor **AzureSubscriptionId**.

   > [!div class="mx-imgBorder"]
   > ![Een schermopname van de eigenschappen van het Azure-abonnement. De muisaanwijzer wijst het klembordpictogram voor 'Abonnement-id' aan om te kopiëren en plakken.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Een Windows Virtual Desktop-tenant maken

Nu u de machtigingen voor de Windows Virtual Desktop-service om een query uit te voeren op Azure Active Directory hebt verleend en de TenantCreator-rol aan een gebruikersaccount hebt toegewezen, kunt u een virtuele Windows Virtual Desktop-tenant maken.

[Download en importeer eerst de Windows Virtual Desktop-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw PowerShell-sessie als u dat nog niet hebt gedaan.

Meld u aan bij Windows Virtual Desktop met behulp van het TenantCreator-gebruikersaccount met deze cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Daarna maakt u een nieuwe Windows Virtual Desktop-tenant die is gekoppeld aan de Azure Active Directory-tenant:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Vervang de waarden tussen haakjes door de waarden die relevant zijn voor uw organisatie en tenant. De naam die u voor uw nieuwe Windows Virtual Desktop-tenant hebt gekozen, moet wereldwijd uniek zijn. Stel bijvoorbeeld dat u de TenantCreator voor Windows Virtual Desktop bent voor de Contoso-organisatie. De cmdlet die u dan zou uitvoeren, zou er als volgt uitzien:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Het is een goed idee om beheerderstoegang toe te wijzen aan een tweede gebruiker voor het geval u ooit bent geblokkeerd of op vakantie bent, en iemand in uw afwezigheid als tenantbeheerder moet optreden. Als u beheerderstoegang wilt toewijzen aan een tweede gebruiker, voert u de volgende cmdlet uit waarbij u `<TenantName>` en `<Upn>` vervangt door de naam van uw tenant en de UPN van de tweede gebruiker.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Volgende stappen
Nadat u uw tenant hebt gemaakt, moet u een service-principal maken in Azure Active Directory en er een rol aan toewijzen in Windows Virtual Desktop. Met de service-principal kunt u de Azure Marketplace-oplossing voor Windows Virtual Desktop om een hostgroep te maken, implementeren. Voor meer informatie over hostgroepen gaat u verder met de zelfstudie over het maken van een hostgroep in Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Service-principals en roltoewijzingen maken met PowerShell](create-service-principal-role-powershell.md)
