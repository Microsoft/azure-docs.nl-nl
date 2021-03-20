---
title: Aanmeldings fout voor Azure dev tools voor onderwijs oplossen
description: Hierin worden de acties beschreven die een student moet ondernemen als er een fout bericht wordt weer gegeven wanneer u zich aanmeldt bij Azure dev tools voor onderwijs.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096651"
---
# <a name="troubleshooting-student-login-issues"></a>Problemen met aanmelding bij studenten oplossen
Om toegang te krijgen tot Azure dev tools voor onderwijs moet een gebruiker een micro soft-account (MSA) hebben. Studenten worden automatisch omgeleid naar het maken van een MSA als hun account niet al een MSA is of aan een MSA is gekoppeld. Als uw domein is gekoppeld aan Active Directory, worden alle accounts onder dat domein al beschouwd als MSA.

Als een student zich probeert aan te melden en het volgende fout bericht ontvangt, gebruikt u een van de hieronder beschreven oplossingen.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Foutbericht bij aanmelden." border="false":::

Er zijn twee oplossingen: Maak een nieuwe Microsoft-account of gebruik een bestaande Microsoft-account.

## <a name="create-a-new-microsoft-account"></a>Een nieuwe Microsoft-account maken
### <a name="use-a-university-email-address"></a>E-mail adres van de Universiteit gebruiken
Als u zich aanmeldt met een e-mail adres van de University (bijvoorbeeld `John.Smith@university.edu` ), moet u een Microsoft-account met dat e-mail adres maken. Het maken van een account is gratis en duurt slechts enkele minuten. Als u een Microsoft-account hebt, kunt u zich automatisch aanmelden bij al uw micro soft-producten met één gebruikers naam en wacht woord.

### <a name="use-a-personal-email-address"></a>Een persoonlijk e-mail adres gebruiken
Als u zich aanmeldt met een persoonlijk e-mail adres (bijvoorbeeld `John.Smith@email.com` ), maar ook een e-mail adres van de Universiteit hebt, kunt u het e-mail adres van de Universiteit gebruiken. Als u zich eerder hebt aangemeld bij de webstore van uw instituut met een persoonlijk e-mail adres of als u geen e-mail adres van de Universiteit hebt, moet u een Microsoft-account maken of koppelen aan uw persoonlijke e-mail adres.

## <a name="use-an-existing-microsoft-account"></a>Een bestaande Microsoft-account gebruiken
Als een student een bestaande Microsoft-account heeft (bijvoorbeeld Xbox), kunnen zij die account koppelen aan een Azure dev tools-account.

1. Ga naar https://account.microsoft.com.
1. Meld u aan met uw Microsoft-account referenties.
1. Selecteer **uw gegevens** in het bovenste lint menu.

1. Klik op **beheren hoe u zich aanmeldt bij micro soft**. U wordt gevraagd om uw identiteit te verifiëren. U ontvangt een e-mail met een beveiligings code.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="Aanmelden beheren." border="false":::

1. Voer de beveiligings code per e-mail in.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Aanmeldings code invoeren." border="false":::!

1. Klik op **E-mail toevoegen** aan uw account en voer uw e-mail adres van uw universiteit in.
De volgende keer dat u zich aanmeldt, kunt u uw e-mail adres van de University gebruiken om toegang te krijgen tot uw Azure dev tools voor onderwijs.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Beheer hoe u zich aanmeldt bij micro soft." border="false":::

## <a name="next-steps"></a>Volgende stappen
- [Veelgestelde vragen](program-faq.md)

- [Ondersteuningsopties](program-support.md)
