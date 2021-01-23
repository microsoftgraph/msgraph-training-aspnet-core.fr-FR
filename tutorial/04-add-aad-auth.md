---
ms.openlocfilehash: a024fb533c552563da6c9179301e16a2e1d09d5f
ms.sourcegitcommit: 6341ad07cd5b03269e7fd20cd3212e48baee7c07
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "49942161"
---
<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cette opération est obligatoire pour obtenir le jeton d’accès OAuth nécessaire pour appeler l’API Microsoft Graph. Dans cette étape, vous allez configurer la [bibliothèque Microsoft.Identity.Web.](https://www.nuget.org/packages/Microsoft.Identity.Web/)

> [!IMPORTANT]
> Pour éviter de stocker l’ID d’application et la secret dans la source, vous utiliserez [.NET Secret Manager](/aspnet/core/security/app-secrets) pour stocker ces valeurs. Le Gestionnaire de secret est uniquement à des fins de développement, les applications de production doivent utiliser un gestionnaire de secret approuvé pour stocker les secrets.

1. Ouvrez **./appsettings.jset** remplacez son contenu par ce qui suit.

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. Ouvrez votre CLI dans le répertoire où se trouve **GraphTutorial.csproj,** puis exécutez les commandes suivantes, en remplaçant votre ID d’application par le portail Azure et votre `YOUR_APP_ID` secret d’application. `YOUR_APP_SECRET`

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a>Implémentation de la connexion

Commencez par ajouter les services de plateforme Microsoft Identity à l’application.

1. Créez un fichier nommé **GraphConstants.cs** dans le répertoire **./Graph** et ajoutez le code suivant.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. Ouvrez **le fichier ./Startup.cs** et ajoutez les `using` instructions suivantes en haut du fichier.

    ```csharp
    using Microsoft.AspNetCore.Authentication.OpenIdConnect;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc.Authorization;
    using Microsoft.Identity.Web;
    using Microsoft.Identity.Web.UI;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.Graph;
    using System.Net;
    using System.Net.Http.Headers;
    ```

1. Remplacez la fonction `ConfigureServices` existante par ce qui suit.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services
            // Use OpenId authentication
            .AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
            // Specify this is a web app and needs auth code flow
            .AddMicrosoftIdentityWebApp(Configuration)
            // Add ability to call web API (Graph)
            // and get access tokens
            .EnableTokenAcquisitionToCallDownstreamApi(options => {
                Configuration.Bind("AzureAd", options);
            }, GraphConstants.Scopes)
            // Use in-memory token cache
            // See https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization
            .AddInMemoryTokenCaches();

        // Require authentication
        services.AddControllersWithViews(options =>
        {
            var policy = new AuthorizationPolicyBuilder()
                .RequireAuthenticatedUser()
                .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
        })
        // Add the Microsoft Identity UI pages for signin/out
        .AddMicrosoftIdentityUI();
    }
    ```

1. Dans la `Configure` fonction, ajoutez la ligne suivante au-dessus de la `app.UseAuthorization();` ligne.

    ```csharp
    app.UseAuthentication();
    ```

1. Ouvrez **./Controllers/HomeController.cs** et remplacez son contenu par ce qui suit.

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using System.Diagnostics;
    using System.Threading.Tasks;

    namespace GraphTutorial.Controllers
    {
        public class HomeController : Controller
        {
            ITokenAcquisition _tokenAcquisition;
            private readonly ILogger<HomeController> _logger;

            // Get the ITokenAcquisition interface via
            // dependency injection
            public HomeController(
                ITokenAcquisition tokenAcquisition,
                ILogger<HomeController> logger)
            {
                _tokenAcquisition = tokenAcquisition;
                _logger = logger;
            }

            public async Task<IActionResult> Index()
            {
                // TEMPORARY
                // Get the token and display it
                try
                {
                    string token = await _tokenAcquisition
                        .GetAccessTokenForUserAsync(GraphConstants.Scopes);
                    return View().WithInfo("Token acquired", token);
                }
                catch (MicrosoftIdentityWebChallengeUserException)
                {
                    return Challenge();
                }
            }

            public IActionResult Privacy()
            {
                return View();
            }

            [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
            public IActionResult Error()
            {
                return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
            }

            [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
            [AllowAnonymous]
            public IActionResult ErrorWithMessage(string message, string debug)
            {
                return View("Index").WithError(message, debug);
            }
        }
    }
    ```

1. Enregistrez vos modifications et démarrez le projet. Connectez-vous avec votre compte Microsoft.

1. Examinez l’invite de consentement. La liste des autorisations correspond à la liste des étendues d’autorisations configurées dans **./Graph/GraphConstants.cs**.

    - **Conservez l’accès aux** données à qui vous avez accordé l’accès : ( ) Cette autorisation est demandée par MSAL afin de récupérer les jetons `offline_access` d’actualisation.
    - **Connectez-vous et lisez votre** profil : ( ) Cette autorisation permet à l’application d’obtenir le profil et la photo de profil de `User.Read` l’utilisateur connecté.
    - **Lisez les paramètres de votre** boîte aux lettres : ( ) Cette autorisation permet à l’application de lire les paramètres de boîte aux lettres de l’utilisateur, y compris le fuseau horaire et `MailboxSettings.Read` le format horaire.
    - **Avoir un accès total à** vos calendriers : ( ) Cette autorisation permet à l’application de lire des événements sur le calendrier de l’utilisateur, d’ajouter de nouveaux événements et de modifier des `Calendars.ReadWrite` événements existants.

    ![Capture d’écran de l’invite de consentement de la plateforme d’identités Microsoft](./images/add-aad-auth-03.png)

    Pour plus d’informations sur le consentement, voir [Comprendre les expériences de consentement d’application Azure AD.](/azure/active-directory/develop/application-consent-experience)

1. Consentement aux autorisations demandées. Le navigateur vous redirige vers l’application, affichant le jeton.

### <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Une fois que l’utilisateur a ouvert une session, vous pouvez obtenir ses informations à partir de Microsoft Graph.

1. Ouvrez **./Graph/GraphClaimsPrincipalExtensions.cs** et remplacez tout son contenu par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. Ouvrez **./Startup.cs** et remplacez la ligne `.AddMicrosoftIdentityWebApp(Configuration)` existante par le code suivant.

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    Considérez ce que fait ce code.

    - Il ajoute un handler d’événements pour `OnTokenValidated` l’événement.
        - Il utilise `ITokenAcquisition` l’interface pour obtenir un jeton d’accès.
        - Il appelle Microsoft Graph pour obtenir le profil et la photo de l’utilisateur.
        - Il ajoute les informations Graph à l’identité de l’utilisateur.

1. Ajoutez l’appel de fonction suivant après `EnableTokenAcquisitionToCallDownstreamApi` l’appel et avant `AddInMemoryTokenCaches` l’appel.

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    Cela rend un **GraphServiceClient** authentifié disponible pour les contrôleurs via l’injection de dépendance.

1. Ouvrez **./Controllers/HomeController.cs** et remplacez `Index` la fonction par ce qui suit.

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. Supprimez toutes les références `ITokenAcquisition` à la **classe HomeController.**

1. Enregistrez vos modifications, démarrez l’application et traversez le processus de signature. Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes bien inscrit.

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. Cliquez sur l’avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **de** connexion. Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

> [!TIP]
> Si vous ne voyez pas votre nom d’utilisateur sur la page d’accueil et que le nom et l’e-mail de l’avatar d’utilisation sont manquants après avoir apporté ces modifications, dé connectez-vous et connectez-vous.

## <a name="storing-and-refreshing-tokens"></a>Stockage et actualisation des jetons

À ce stade, votre application dispose d’un jeton d’accès, qui est envoyé dans l’en-tête des `Authorization` appels d’API. Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.

Cependant, ce jeton est de courte durée. Le jeton expire une heure après son émission. C’est là que le jeton d’actualisation devient utile. Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans obliger l’utilisateur à se reconnecter.

Étant donné que l’application utilise la bibliothèque Microsoft.Identity.Web, vous n’avez pas besoin d’implémenter de logique de stockage ou d’actualisation de jeton.

L’application utilise le cache de jetons en mémoire, ce qui est suffisant pour les applications qui n’ont pas besoin de rendre persistants les jetons au redémarrage de l’application. Les applications de production peuvent utiliser à la place les [options de cache](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) distribué dans la bibliothèque Microsoft.Identity.Web.

La `GetAccessTokenForUserAsync` méthode gère l’expiration et l’actualisation des jetons pour vous. Il vérifie d’abord le jeton mis en cache et, s’il n’a pas expiré, il le renvoie. S’il a expiré, il utilise le jeton d’actualisation mis en cache pour en obtenir un nouveau.

Le **GraphServiceClient que** les contrôleurs obtiennent via l’injection de dépendances sera préconfiguré avec un fournisseur d’authentification qui vous `GetAccessTokenForUserAsync` est utilisé.
