---
ms.openlocfilehash: c954903f38d48bcca4c534f4d0cfbe1605cbf7f6
ms.sourcegitcommit: 6341ad07cd5b03269e7fd20cd3212e48baee7c07
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "49942147"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e65b0-101">Dans cette section, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="e65b0-101">In this section you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="e65b0-102">Pour cette application, vous allez utiliser la bibliothèque [cliente Microsoft Graph pour .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e65b0-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="e65b0-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="e65b0-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="e65b0-104">Commencez par créer un contrôleur pour les affichages calendrier.</span><span class="sxs-lookup"><span data-stu-id="e65b0-104">Start by creating a new controller for calendar views.</span></span>

1. <span data-ttu-id="e65b0-105">Ajoutez un nouveau fichier nommé **CalendarController.cs** dans le répertoire **./Controllers** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e65b0-105">Add a new file named **CalendarController.cs** in the **./Controllers** directory and add the following code.</span></span>

    ```csharp
    using GraphTutorial.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Web;
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using TimeZoneConverter;

    namespace GraphTutorial.Controllers
    {
        public class CalendarController : Controller
        {
            private readonly GraphServiceClient _graphClient;
            private readonly ILogger<HomeController> _logger;

            public CalendarController(
                GraphServiceClient graphClient,
                ILogger<HomeController> logger)
            {
                _graphClient = graphClient;
                _logger = logger;
            }
        }
    }
    ```

1. <span data-ttu-id="e65b0-106">Ajoutez les fonctions suivantes à la `CalendarController` classe pour obtenir l’affichage Calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e65b0-106">Add the following functions to the `CalendarController` class to get the user's calendar view.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="GetCalendarViewSnippet":::

    <span data-ttu-id="e65b0-107">Réfléchissez à ce que fait le `GetUserWeekCalendar` code.</span><span class="sxs-lookup"><span data-stu-id="e65b0-107">Consider what the code in `GetUserWeekCalendar` does.</span></span>

    - <span data-ttu-id="e65b0-108">Il utilise le fuseau horaire de l’utilisateur pour obtenir les valeurs de date/heure de début et de fin UTC de la semaine.</span><span class="sxs-lookup"><span data-stu-id="e65b0-108">It uses the user's time zone to get UTC start and end date/time values for the week.</span></span>
    - <span data-ttu-id="e65b0-109">Elle interroge l’affichage [](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) Calendrier de l’utilisateur pour obtenir tous les événements se s’erdant entre la date et l’heure de début et de fin.</span><span class="sxs-lookup"><span data-stu-id="e65b0-109">It queries the user's [calendar view](/graph/api/calendar-list-calendarview?view=graph-rest-1.0) to get all events that fall between the start and end date/times.</span></span> <span data-ttu-id="e65b0-110">L’utilisation d’un [](/graph/api/user-list-events?view=graph-rest-1.0) affichage Calendrier au lieu de répertorier des événements développe les événements périodiques, renvoyant toutes les occurrences qui se produisent dans la fenêtre de temps spécifiée.</span><span class="sxs-lookup"><span data-stu-id="e65b0-110">Using a calendar view instead of [listing events](/graph/api/user-list-events?view=graph-rest-1.0) expands recurring events, returning any occurrences that happen in the specified time window.</span></span>
    - <span data-ttu-id="e65b0-111">Il utilise `Prefer: outlook.timezone` l’en-tête pour obtenir les résultats dans le fuseau horaire de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e65b0-111">It uses the `Prefer: outlook.timezone` header to get results back in the user's timezone.</span></span>
    - <span data-ttu-id="e65b0-112">Il permet de limiter les champs qui reviennent uniquement à ceux `Select` utilisés par l’application.</span><span class="sxs-lookup"><span data-stu-id="e65b0-112">It uses `Select` to limit the fields that come back to just those used by the app.</span></span>
    - <span data-ttu-id="e65b0-113">Il utilise `OrderBy` pour trier les résultats dans l’ordre chronologique.</span><span class="sxs-lookup"><span data-stu-id="e65b0-113">It uses `OrderBy` to sort the results chronologically.</span></span>
    - <span data-ttu-id="e65b0-114">Il utilise une `PageIterator` page à travers la collection [d’événements.](/graph/sdks/paging)</span><span class="sxs-lookup"><span data-stu-id="e65b0-114">It uses a `PageIterator` to [page through the events collection](/graph/sdks/paging).</span></span> <span data-ttu-id="e65b0-115">Cela gère le cas où l’utilisateur a plus d’événements sur son calendrier que la taille de page demandée.</span><span class="sxs-lookup"><span data-stu-id="e65b0-115">This handles the case where the user has more events on their calendar than the requested page size.</span></span>

1. <span data-ttu-id="e65b0-116">Ajoutez la fonction suivante à la `CalendarController` classe pour implémenter une vue temporaire des données renvoyées.</span><span class="sxs-lookup"><span data-stu-id="e65b0-116">Add the following function to the `CalendarController` class to implement a temporary view of the returned data.</span></span>

    ```csharp
    // Minimum permission scope needed for this view
    [AuthorizeForScopes(Scopes = new[] { "Calendars.Read" })]
    public async Task<IActionResult> Index()
    {
        try
        {
            var userTimeZone = TZConvert.GetTimeZoneInfo(
                User.GetUserGraphTimeZone());
            var startOfWeek = CalendarController.GetUtcStartOfWeekInTimeZone(
                DateTime.Today, userTimeZone);

            var events = await GetUserWeekCalendar(startOfWeek);

            // Return a JSON dump of events
            return new ContentResult {
                Content = _graphClient.HttpProvider.Serializer.SerializeObject(events),
                ContentType = "application/json"
            };
        }
        catch (ServiceException ex)
        {
            if (ex.InnerException is MicrosoftIdentityWebChallengeUserException)
            {
                throw;
            }

            return new ContentResult {
                Content = $"Error getting calendar view: {ex.Message}",
                ContentType = "text/plain"
            };
        }
    }
    ```

1. <span data-ttu-id="e65b0-117">Démarrez l’application, connectez-vous, puis cliquez sur le lien **Calendrier** dans la barre de navigation.</span><span class="sxs-lookup"><span data-stu-id="e65b0-117">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="e65b0-118">Si tout fonctionne, vous devriez voir une image mémoire JSON des événements dans le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e65b0-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="e65b0-119">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="e65b0-119">Display the results</span></span>

<span data-ttu-id="e65b0-120">Vous pouvez désormais ajouter une vue pour afficher les résultats de façon plus parlante.</span><span class="sxs-lookup"><span data-stu-id="e65b0-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

### <a name="create-view-models"></a><span data-ttu-id="e65b0-121">Créer des modèles d’affichage</span><span class="sxs-lookup"><span data-stu-id="e65b0-121">Create view models</span></span>

1. <span data-ttu-id="e65b0-122">Créez un fichier nommé **CalendarViewEvent.cs** dans le répertoire **./Models** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e65b0-122">Create a new file named **CalendarViewEvent.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewEvent.cs" id="CalendarViewEventSnippet":::

1. <span data-ttu-id="e65b0-123">Créez un fichier nommé **DailyViewModel.cs** dans le répertoire **./Models** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e65b0-123">Create a new file named **DailyViewModel.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/DailyViewModel.cs" id="DailyViewModelSnippet":::

1. <span data-ttu-id="e65b0-124">Créez un fichier nommé **CalendarViewModel.cs** dans le répertoire **./Models** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e65b0-124">Create a new file named **CalendarViewModel.cs** in the **./Models** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Models/CalendarViewModel.cs" id="CalendarViewModelSnippet":::

### <a name="create-views"></a><span data-ttu-id="e65b0-125">Créer des affichages</span><span class="sxs-lookup"><span data-stu-id="e65b0-125">Create views</span></span>

1. <span data-ttu-id="e65b0-126">Créez un répertoire nommé **Calendrier** dans le répertoire **./Views.**</span><span class="sxs-lookup"><span data-stu-id="e65b0-126">Create a new directory named **Calendar** in the **./Views** directory.</span></span>

1. <span data-ttu-id="e65b0-127">Créez un fichier nommé **_DailyEventsPartial.cshtml** dans le répertoire **./Views/Calendar** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e65b0-127">Create a new file named **_DailyEventsPartial.cshtml** in the **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/_DailyEventsPartial.cshtml" id="DailyEventsPartialSnippet":::

1. <span data-ttu-id="e65b0-128">Créez un fichier nommé **Index.cshtml** dans le répertoire **./Views/Calendar** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="e65b0-128">Create a new file named **Index.cshtml** in the **./Views/Calendar** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/Index.cshtml" id="CalendarIndexSnippet":::

### <a name="update-calendar-controller"></a><span data-ttu-id="e65b0-129">Mettre à jour le contrôleur de calendrier</span><span class="sxs-lookup"><span data-stu-id="e65b0-129">Update calendar controller</span></span>

1. <span data-ttu-id="e65b0-130">Ouvrez **./Controllers/CalendarController.cs** et remplacez la fonction `Index` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="e65b0-130">Open **./Controllers/CalendarController.cs** and replace the existing `Index` function with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="IndexSnippet":::

1. <span data-ttu-id="e65b0-131">Démarrez l’application, connectez-vous, puis cliquez sur le lien **Calendrier**.</span><span class="sxs-lookup"><span data-stu-id="e65b0-131">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="e65b0-132">L’application doit désormais afficher un tableau des événements.</span><span class="sxs-lookup"><span data-stu-id="e65b0-132">The app should now render a table of events.</span></span>

    ![Capture d’écran du tableau des événements](./images/add-msgraph-01.png)
