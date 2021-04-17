---
ms.openlocfilehash: 05fc91667f34262c6b510c0e1464d3e5fad339bc
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107590063"
---
| Taal/framework | Project op<br/>GitHub                                                                                                    | Pakket                                                                      | Krijgen<br/>Begon                             | Gebruikers aanmelden                                         | Toegang tot web-API's                                                 | Algemeen beschikbaar (GA) *of*<br/>Openbare preview<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Openbare preview                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Zelfstudie](../articles/active-directory/develop/tutorial-v2-angular.md)| ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Algemene beschikbaarheid                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Openbare preview                                               |
| Javascript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Zelfstudie](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Algemene beschikbaarheid                                                           |
|Javascript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Zelfstudie](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Algemene beschikbaarheid                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Openbare preview                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Aanvullende gebruiksvoorwaarden voor Microsoft Azure zijn][preview-tos] van toepassing op bibliotheken in openbare *preview.*

<sup>2</sup> [Codestroom met alleen][auth-code-flow] PCKE auth (aanbevolen). 

<sup>3</sup> [Alleen impliciete toekenningsstroom.][implicit-flow]

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md