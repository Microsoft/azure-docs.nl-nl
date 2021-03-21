---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007992"
---
| Taal/Framework | Project op<br/>GitHub                                                                 | Pakket                                                                                | Slaag<br/>helpen                           | Gebruikers aanmelden                                            | Toegang tot Web-Api's                                                 | Algemeen beschikbaar (GA) *of*<br/>Open bare preview<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Snelstartgids](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
| Knooppunt               | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-knoop punt](https://www.npmjs.com/package/@azure/msal-node)  | [Snelstartgids](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Snelstartgids](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![Bibliotheek kan geen ID-tokens aanvragen voor gebruikers aanmelding.][n] | ![De bibliotheek kan toegangs tokens aanvragen voor beveiligde web-Api's.][y] | Algemene beschikbaarheid                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [aanvullende gebruiks voorwaarden voor Microsoft Azure previews][preview-tos] zijn van toepassing op bibliotheken in de *open bare preview*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/