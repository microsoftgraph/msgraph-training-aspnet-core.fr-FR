---
ms.openlocfilehash: a024fb533c552563da6c9179301e16a2e1d09d5f
ms.sourcegitcommit: 6341ad07cd5b03269e7fd20cd3212e48baee7c07
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "49942161"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ad652-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="ad652-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="ad652-102">Cette opération est obligatoire pour obtenir le jeton d’accès OAuth nécessaire pour appeler l’API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ad652-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="ad652-103">Dans cette étape, vous allez configurer la [bibliothèque Microsoft.Identity.Web.](https://www.nuget.org/packages/Microsoft.Identity.Web/)</span><span class="sxs-lookup"><span data-stu-id="ad652-103">In this step you will configure the [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) library.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ad652-104">Pour éviter de stocker l’ID d’application et la secret dans la source, vous utiliserez [.NET Secret Manager](/aspnet/core/security/app-secrets) pour stocker ces valeurs.</span><span class="sxs-lookup"><span data-stu-id="ad652-104">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="ad652-105">Le Gestionnaire de secret est uniquement à des fins de développement, les applications de production doivent utiliser un gestionnaire de secret approuvé pour stocker les secrets.</span><span class="sxs-lookup"><span data-stu-id="ad652-105">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

1. <span data-ttu-id="ad652-106">Ouvrez **./appsettings.jset** remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="ad652-106">Open **./appsettings.json** and replace its contents with the following.</span></span>

    :::code language="json" source="../demo/GraphTutorial/appsettings.json" highlight="2-6":::

1. <span data-ttu-id="ad652-107">Ouvrez votre CLI dans le répertoire où se trouve **GraphTutorial.csproj,** puis exécutez les commandes suivantes, en remplaçant votre ID d’application par le portail Azure et votre `YOUR_APP_ID` secret d’application. `YOUR_APP_SECRET`</span><span class="sxs-lookup"><span data-stu-id="ad652-107">Open your CLI in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="ad652-108">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="ad652-108">Implement sign-in</span></span>

<span data-ttu-id="ad652-109">Commencez par ajouter les services de plateforme Microsoft Identity à l’application.</span><span class="sxs-lookup"><span data-stu-id="ad652-109">Start by adding the Microsoft Identity platform services to the application.</span></span>

1. <span data-ttu-id="ad652-110">Créez un fichier nommé **GraphConstants.cs** dans le répertoire **./Graph** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="ad652-110">Create a new file named **GraphConstants.cs** in the **./Graph** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphConstants.cs" id="GraphConstantsSnippet":::

1. <span data-ttu-id="ad652-111">Ouvrez **le fichier ./Startup.cs** et ajoutez les `using` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="ad652-111">Open the **./Startup.cs** file and add the following `using` statements to the top of the file.</span></span>

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

1. <span data-ttu-id="ad652-112">Remplacez la fonction `ConfigureServices` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="ad652-112">Replace the existing `ConfigureServices` function with the following.</span></span>

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

1. <span data-ttu-id="ad652-113">Dans la `Configure` fonction, ajoutez la ligne suivante au-dessus de la `app.UseAuthorization();` ligne.</span><span class="sxs-lookup"><span data-stu-id="ad652-113">In the `Configure` function, add the following line above the `app.UseAuthorization();` line.</span></span>

    ```csharp
    app.UseAuthentication();
    ```

1. <span data-ttu-id="ad652-114">Ouvrez **./Controllers/HomeController.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="ad652-114">Open **./Controllers/HomeController.cs** and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="ad652-115">Enregistrez vos modifications et démarrez le projet.</span><span class="sxs-lookup"><span data-stu-id="ad652-115">Save your changes and start the project.</span></span> <span data-ttu-id="ad652-116">Connectez-vous avec votre compte Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ad652-116">Login with your Microsoft account.</span></span>

1. <span data-ttu-id="ad652-117">Examinez l’invite de consentement.</span><span class="sxs-lookup"><span data-stu-id="ad652-117">Examine the consent prompt.</span></span> <span data-ttu-id="ad652-118">La liste des autorisations correspond à la liste des étendues d’autorisations configurées dans **./Graph/GraphConstants.cs**.</span><span class="sxs-lookup"><span data-stu-id="ad652-118">The list of permissions correspond to list of permissions scopes configured in **./Graph/GraphConstants.cs**.</span></span>

    - <span data-ttu-id="ad652-119">**Conservez l’accès aux** données à qui vous avez accordé l’accès : ( ) Cette autorisation est demandée par MSAL afin de récupérer les jetons `offline_access` d’actualisation.</span><span class="sxs-lookup"><span data-stu-id="ad652-119">**Maintain access to data you have given it access to:** (`offline_access`) This permission is requested by MSAL in order to retrieve refresh tokens.</span></span>
    - <span data-ttu-id="ad652-120">**Connectez-vous et lisez votre** profil : ( ) Cette autorisation permet à l’application d’obtenir le profil et la photo de profil de `User.Read` l’utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="ad652-120">**Sign you in and read your profile:** (`User.Read`) This permission allows the app to get the logged-in user's profile and profile photo.</span></span>
    - <span data-ttu-id="ad652-121">**Lisez les paramètres de votre** boîte aux lettres : ( ) Cette autorisation permet à l’application de lire les paramètres de boîte aux lettres de l’utilisateur, y compris le fuseau horaire et `MailboxSettings.Read` le format horaire.</span><span class="sxs-lookup"><span data-stu-id="ad652-121">**Read your mailbox settings:** (`MailboxSettings.Read`) This permission allows the app to read the user's mailbox settings, including time zone and time format.</span></span>
    - <span data-ttu-id="ad652-122">**Avoir un accès total à** vos calendriers : ( ) Cette autorisation permet à l’application de lire des événements sur le calendrier de l’utilisateur, d’ajouter de nouveaux événements et de modifier des `Calendars.ReadWrite` événements existants.</span><span class="sxs-lookup"><span data-stu-id="ad652-122">**Have full access to your calendars:** (`Calendars.ReadWrite`) This permission allows the app to read events on the user's calendar, add new events, and modify existing ones.</span></span>

    ![Capture d’écran de l’invite de consentement de la plateforme d’identités Microsoft](./images/add-aad-auth-03.png)

    <span data-ttu-id="ad652-124">Pour plus d’informations sur le consentement, voir [Comprendre les expériences de consentement d’application Azure AD.](/azure/active-directory/develop/application-consent-experience)</span><span class="sxs-lookup"><span data-stu-id="ad652-124">For more information regarding consent, see [Understanding Azure AD application consent experiences](/azure/active-directory/develop/application-consent-experience).</span></span>

1. <span data-ttu-id="ad652-125">Consentement aux autorisations demandées.</span><span class="sxs-lookup"><span data-stu-id="ad652-125">Consent to the requested permissions.</span></span> <span data-ttu-id="ad652-126">Le navigateur vous redirige vers l’application, affichant le jeton.</span><span class="sxs-lookup"><span data-stu-id="ad652-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="ad652-127">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="ad652-127">Get user details</span></span>

<span data-ttu-id="ad652-128">Une fois que l’utilisateur a ouvert une session, vous pouvez obtenir ses informations à partir de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ad652-128">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="ad652-129">Ouvrez **./Graph/GraphClaimsPrincipalExtensions.cs** et remplacez tout son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="ad652-129">Open **./Graph/GraphClaimsPrincipalExtensions.cs** and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphClaimsPrincipalExtensions.cs" id="GraphClaimsExtensionsSnippet":::

1. <span data-ttu-id="ad652-130">Ouvrez **./Startup.cs** et remplacez la ligne `.AddMicrosoftIdentityWebApp(Configuration)` existante par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="ad652-130">Open **./Startup.cs** and replace the existing `.AddMicrosoftIdentityWebApp(Configuration)` line with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddSignInSnippet":::

    <span data-ttu-id="ad652-131">Considérez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="ad652-131">Consider what this code does.</span></span>

    - <span data-ttu-id="ad652-132">Il ajoute un handler d’événements pour `OnTokenValidated` l’événement.</span><span class="sxs-lookup"><span data-stu-id="ad652-132">It adds an event handler for the `OnTokenValidated` event.</span></span>
        - <span data-ttu-id="ad652-133">Il utilise `ITokenAcquisition` l’interface pour obtenir un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="ad652-133">It uses the `ITokenAcquisition` interface to get an access token.</span></span>
        - <span data-ttu-id="ad652-134">Il appelle Microsoft Graph pour obtenir le profil et la photo de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad652-134">It calls Microsoft Graph to get the user's profile and photo.</span></span>
        - <span data-ttu-id="ad652-135">Il ajoute les informations Graph à l’identité de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad652-135">It adds the Graph information to the user's identity.</span></span>

1. <span data-ttu-id="ad652-136">Ajoutez l’appel de fonction suivant après `EnableTokenAcquisitionToCallDownstreamApi` l’appel et avant `AddInMemoryTokenCaches` l’appel.</span><span class="sxs-lookup"><span data-stu-id="ad652-136">Add the following function call after the `EnableTokenAcquisitionToCallDownstreamApi` call and before the `AddInMemoryTokenCaches` call.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="AddGraphClientSnippet":::

    <span data-ttu-id="ad652-137">Cela rend un **GraphServiceClient** authentifié disponible pour les contrôleurs via l’injection de dépendance.</span><span class="sxs-lookup"><span data-stu-id="ad652-137">This will make an authenticated **GraphServiceClient** available to controllers via dependency injection.</span></span>

1. <span data-ttu-id="ad652-138">Ouvrez **./Controllers/HomeController.cs** et remplacez `Index` la fonction par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="ad652-138">Open **./Controllers/HomeController.cs** and replace the `Index` function with the following.</span></span>

    ```csharp
    public IActionResult Index()
    {
        return View();
    }
    ```

1. <span data-ttu-id="ad652-139">Supprimez toutes les références `ITokenAcquisition` à la **classe HomeController.**</span><span class="sxs-lookup"><span data-stu-id="ad652-139">Remove all references to `ITokenAcquisition` in the **HomeController** class.</span></span>

1. <span data-ttu-id="ad652-140">Enregistrez vos modifications, démarrez l’application et traversez le processus de signature.</span><span class="sxs-lookup"><span data-stu-id="ad652-140">Save your changes, start the app, and go through the sign-in process.</span></span> <span data-ttu-id="ad652-141">Vous devez revenir sur la page d’accueil, mais l’interface utilisateur doit changer pour indiquer que vous êtes bien inscrit.</span><span class="sxs-lookup"><span data-stu-id="ad652-141">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Capture d’écran de la page d’accueil après la connexion](./images/add-aad-auth-01.png)

1. <span data-ttu-id="ad652-143">Cliquez sur l’avatar de l’utilisateur dans le coin supérieur droit pour accéder au lien **de** connexion.</span><span class="sxs-lookup"><span data-stu-id="ad652-143">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="ad652-144">Le fait de cliquer sur **Se déconnecter** réinitialise la session et vous ramène à la page d’accueil.</span><span class="sxs-lookup"><span data-stu-id="ad652-144">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Capture d’écran du menu déroulant avec le lien de déconnexion](./images/add-aad-auth-02.png)

> [!TIP]
> <span data-ttu-id="ad652-146">Si vous ne voyez pas votre nom d’utilisateur sur la page d’accueil et que le nom et l’e-mail de l’avatar d’utilisation sont manquants après avoir apporté ces modifications, dé connectez-vous et connectez-vous.</span><span class="sxs-lookup"><span data-stu-id="ad652-146">If you do not see your user name on the home page and the use avatar dropdown is missing name and email after making these changes, sign out and sign back in.</span></span>

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="ad652-147">Stockage et actualisation des jetons</span><span class="sxs-lookup"><span data-stu-id="ad652-147">Storing and refreshing tokens</span></span>

<span data-ttu-id="ad652-148">À ce stade, votre application dispose d’un jeton d’accès, qui est envoyé dans l’en-tête des `Authorization` appels d’API.</span><span class="sxs-lookup"><span data-stu-id="ad652-148">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="ad652-149">Il s’agit du jeton qui permet à l’application d’accéder à Microsoft Graph pour le compte de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ad652-149">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="ad652-150">Cependant, ce jeton est de courte durée.</span><span class="sxs-lookup"><span data-stu-id="ad652-150">However, this token is short-lived.</span></span> <span data-ttu-id="ad652-151">Le jeton expire une heure après son émission.</span><span class="sxs-lookup"><span data-stu-id="ad652-151">The token expires an hour after it is issued.</span></span> <span data-ttu-id="ad652-152">C’est là que le jeton d’actualisation devient utile.</span><span class="sxs-lookup"><span data-stu-id="ad652-152">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="ad652-153">Le jeton d’actualisation permet à l’application de demander un nouveau jeton d’accès sans obliger l’utilisateur à se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="ad652-153">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="ad652-154">Étant donné que l’application utilise la bibliothèque Microsoft.Identity.Web, vous n’avez pas besoin d’implémenter de logique de stockage ou d’actualisation de jeton.</span><span class="sxs-lookup"><span data-stu-id="ad652-154">Because the app is using the Microsoft.Identity.Web library, you do not have to implement any token storage or refresh logic.</span></span>

<span data-ttu-id="ad652-155">L’application utilise le cache de jetons en mémoire, ce qui est suffisant pour les applications qui n’ont pas besoin de rendre persistants les jetons au redémarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="ad652-155">The app uses the in-memory token cache, which is sufficient for apps that do not need to persist tokens when the app restarts.</span></span> <span data-ttu-id="ad652-156">Les applications de production peuvent utiliser à la place les [options de cache](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) distribué dans la bibliothèque Microsoft.Identity.Web.</span><span class="sxs-lookup"><span data-stu-id="ad652-156">Production apps may instead use the [distributed cache options](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization) in the Microsoft.Identity.Web library.</span></span>

<span data-ttu-id="ad652-157">La `GetAccessTokenForUserAsync` méthode gère l’expiration et l’actualisation des jetons pour vous.</span><span class="sxs-lookup"><span data-stu-id="ad652-157">The `GetAccessTokenForUserAsync` method handles token expiration and refresh for you.</span></span> <span data-ttu-id="ad652-158">Il vérifie d’abord le jeton mis en cache et, s’il n’a pas expiré, il le renvoie.</span><span class="sxs-lookup"><span data-stu-id="ad652-158">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="ad652-159">S’il a expiré, il utilise le jeton d’actualisation mis en cache pour en obtenir un nouveau.</span><span class="sxs-lookup"><span data-stu-id="ad652-159">If it is expired, it uses the cached refresh token to obtain a new one.</span></span>

<span data-ttu-id="ad652-160">Le **GraphServiceClient que** les contrôleurs obtiennent via l’injection de dépendances sera préconfiguré avec un fournisseur d’authentification qui vous `GetAccessTokenForUserAsync` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="ad652-160">The **GraphServiceClient** that controllers get via dependency injection will be pre-configured with an authentication provider that uses `GetAccessTokenForUserAsync` for you.</span></span>
