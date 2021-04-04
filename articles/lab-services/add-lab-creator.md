---
title: Een gebruiker toevoegen als een Lab-Maker in Azure Lab Services
description: In dit artikel wordt beschreven hoe u een gebruiker toevoegt aan de rol Lab Creator voor een Lab-account in Azure Lab Services. De Lab-makers kunnen Labs maken binnen dit lab-account.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a6c89483744f943926f126701d28988358627a27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435957"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Lab-makers toevoegen aan een Lab-account in Azure Lab Services
In dit artikel wordt beschreven hoe u gebruikers toevoegt als Lab-makers aan een Lab-account in Azure Lab Services. Deze toepassingen kunnen vervolgens Labs maken in het lab-account. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Micro soft-gebruikers account toevoegen aan rol Lab Creator
Om een leslokaallab in een labaccount in te kunnen stellen, moet de gebruiker lid zijn van de rol **Labmaker** in het labaccount. Het account dat u hebt gebruikt voor het maken van het lab-account wordt automatisch toegevoegd aan deze rol. Als u van plan bent een leslokaallab te maken met hetzelfde gebruikersaccount, kunt u deze stap overslaan. Als u een ander gebruikersaccount wilt gebruiken om een leslokaallab te maken, voert u de volgende stappen uit: 

Als u machtigingen wilt toekennen aan docenten om labs te maken voor hun lessen, voegt u hen toe aan de rol **Labmaker**:

1. Selecteer op de pagina **Lab-account** de optie **Toegangsbeheer (IAM)** en klik op **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Access Control -> Knop Roltoewijzing toevoegen](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Selecteer op de pagina **Roltoewijzing toevoegen** de optie **Labmaker** als **Rol**. Selecteer de gebruiker die u wilt toevoegen aan de rol Labmaker en selecteer **Opslaan**. 

    ![Labmaker toevoegen](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Als u een niet-Microsoft-account gebruiker toevoegt als een Lab-Maker, raadpleegt u de sectie [een niet-Microsoft-account gebruiker toevoegen als een Lab-Maker](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Een niet-Microsoft-account gebruiker toevoegen als een Lab-Maker
Als u een gebruiker wilt toevoegen als een Lab-Maker, gebruikt u hun e-mail accounts. De volgende typen e-mail accounts kunnen worden gebruikt:

- Een e-mail account dat wordt verschaft door de Azure Active Directory van uw universiteit (AAD).
- Een micro soft-e-mail account, zoals `@outlook.com` ,, `@hotmail.com` `@msn.com` of `@live.com` .
- Een niet-micro soft-e-mail account, zoals het abonnement van Yahoo of Google. Deze typen accounts moeten echter worden gekoppeld aan een Microsoft-account.
- Een GitHub-account. Dit account moet worden gekoppeld aan een Microsoft-account.

### <a name="using-a-non-microsoft-email-account"></a>Een niet-micro soft-e-mail account gebruiken
Lab-makers/docenten kunnen niet-micro soft-e-mail accounts gebruiken om zich te registreren bij een leslokaal Lab en zich aan te melden.  Voor de aanmelding bij de Lab Services-portal moeten docenten eerst een Microsoft-account maken dat is gekoppeld aan een niet-micro soft-e-mail adres.

Veel docenten hebben mogelijk al een Microsoft-account gekoppeld aan hun e-mail adressen van andere leveranciers dan micro soft. Docenten hebben bijvoorbeeld al een Microsoft-account als ze hun e-mail adres hebben gebruikt met andere producten of services van micro soft, zoals Office, Skype, OneDrive of Windows.  

Wanneer docenten zich aanmelden bij de Lab Services-portal, wordt ze gevraagd hun e-mail adres en wacht woord op te sturen. Als de docent probeert zich aan te melden met een niet-Microsoft-account waaraan geen Microsoft-account is gekoppeld, wordt het volgende fout bericht weer gegeven: 

![Foutbericht](./media/how-to-configure-student-usage/cant-find-account.png)

Als u zich wilt aanmelden voor een Microsoft-account, moeten docenten het gaan [http://signup.live.com](http://signup.live.com) .  


### <a name="using-a-github-account"></a>Een GitHub-account gebruiken
Docenten kunnen ook een bestaand GitHub-account gebruiken om zich te registreren bij een leslokaal Lab en zich aan te melden. Als aan de docent al een Microsoft-account is gekoppeld aan hun GitHub-account, kunnen ze zich aanmelden en hun wacht woord opgeven, zoals in de vorige sectie wordt weer gegeven. Als ze hun GitHub-account nog niet aan een Microsoft-account hebben gekoppeld, moeten ze **aanmeldings opties** selecteren:

![Koppeling voor aanmeldings opties](./media/how-to-configure-student-usage/signin-options.png)

Selecteer **Aanmelden met github** op de pagina **aanmeldings opties** .

![Aanmelden met GitHub-koppeling](./media/how-to-configure-student-usage/signin-github.png)

Ten slotte wordt er gevraagd om een Microsoft-account te maken dat is gekoppeld aan het GitHub-account. Dit gebeurt automatisch wanneer de docent **volgende** selecteert.  De docent wordt dan onmiddellijk aangemeld en is verbonden met het leslokaal Lab.


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker, Access Labs](how-to-use-classroom-lab.md)
