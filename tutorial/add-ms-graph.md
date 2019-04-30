<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4405c-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l'application.</span><span class="sxs-lookup"><span data-stu-id="4405c-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="4405c-102">Pour cette application, vous allez utiliser la [bibliothèque cliente Microsoft Graph pour .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="4405c-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="4405c-103">Obtenir des événements de calendrier à partir d'Outlook</span><span class="sxs-lookup"><span data-stu-id="4405c-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="4405c-104">Commencez par mettre à jour le fichier **CalendarPage. Xaml** dans le projet **GraphTutorial** .</span><span class="sxs-lookup"><span data-stu-id="4405c-104">Start by updating the **CalendarPage.xaml** file in the **GraphTutorial** project.</span></span> <span data-ttu-id="4405c-105">Ouvrez le fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="4405c-105">Open the file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             Title="Calendar"
             x:Class="GraphTutorial.CalendarPage">
    <ContentPage.Content>
        <StackLayout>
            <Editor x:Name="JSONResponse"
                    IsSpellCheckEnabled="False"
                    HorizontalOptions="FillAndExpand"
                    VerticalOptions="FillAndExpand"/>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="4405c-106">Ouvrez `CalendarPage.xaml.cs` et ajoutez les instructions `using` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="4405c-106">Open `CalendarPage.xaml.cs` and add the following `using` statements at the top of the file.</span></span>

```cs
using Newtonsoft.Json;
using Microsoft.Graph;
using System.ComponentModel;
using System.Collections.ObjectModel;
```

<span data-ttu-id="4405c-107">Ajoutez ensuite le code suivant pour obtenir les événements de l'utilisateur et afficher la réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="4405c-107">Then add the following code to get the user's events and display the JSON response.</span></span>

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Get the events
    var events = await App.GraphClient.Me.Events.Request()
        .Select("subject,organizer,start,end")
        .OrderBy("createdDateTime DESC")
        .GetAsync();

    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
}
```

<span data-ttu-id="4405c-108">Examinez le contenu du `OnAppearing` code.</span><span class="sxs-lookup"><span data-stu-id="4405c-108">Consider what the code in `OnAppearing` is doing.</span></span>

- <span data-ttu-id="4405c-109">L'URL qui sera appelée est `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="4405c-109">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="4405c-110">La `Select` fonction limite les champs renvoyés pour chaque événement à ceux que l'affichage utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="4405c-110">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="4405c-111">La `OrderBy` fonction trie les résultats en fonction de la date et de l'heure de leur création, avec l'élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="4405c-111">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="4405c-112">Vous pouvez maintenant exécuter l'application, vous connecter et cliquer sur l'élément de navigation **calendrier** dans le menu.</span><span class="sxs-lookup"><span data-stu-id="4405c-112">You can now run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="4405c-113">Vous devriez voir un dump JSON des événements sur le calendrier de l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4405c-113">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="4405c-114">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="4405c-114">Display the results</span></span>

<span data-ttu-id="4405c-115">À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale.</span><span class="sxs-lookup"><span data-stu-id="4405c-115">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="4405c-116">Commencez par créer un [convertisseur de valeur de liaison](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) pour convertir les valeurs [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) renvoyées par Microsoft Graph dans les formats de date et d'heure attendus par l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="4405c-116">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span> <span data-ttu-id="4405c-117">Cliquez avec le bouton droit sur le dossier des **modèles** dans le projet **GraphTutorial** , puis sélectionnez **nouveau**, puis **classe..**.. Nommez la `GraphDateTimeTimeZoneConverter` classe, puis choisissez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="4405c-117">Right-click the **Models** folder in the **GraphTutorial** project and choose **New**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and choose **Add**.</span></span> <span data-ttu-id="4405c-118">Remplacez tout le contenu du fichier par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="4405c-118">Replace the entire contents of the file with the following.</span></span>

```cs
using Microsoft.Graph;
using System;
using System.Globalization;
using Xamarin.Forms;

namespace GraphTutorial.Models
{
    class GraphDateTimeTimeZoneConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            if (value is DateTimeTimeZone date)
            {
                // Resolve the time zone
                var timezone = TimeZoneInfo.FindSystemTimeZoneById(date.TimeZone);
                // Parse method assumes local time, which may not be the case
                var parsedDateAsLocal = DateTimeOffset.Parse(date.DateTime);
                // Determine the offset from UTC time for the specific date
                // Making this call adjusts for DST as appropriate
                var tzOffset = timezone.GetUtcOffset(parsedDateAsLocal.DateTime);
                // Create a new DateTimeOffset with the specific offset from UTC
                var correctedDate = new DateTimeOffset(parsedDateAsLocal.DateTime, tzOffset);
                // Return the local date time string
                return $"{correctedDate.LocalDateTime.ToShortDateString()} {correctedDate.LocalDateTime.ToShortTimeString()}";
            }

            return string.Empty;
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }
}
```

<span data-ttu-id="4405c-119">Ensuite, remplacez tout le contenu de `CalendarPage.xaml` par par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="4405c-119">Next, replace the entire contents of `CalendarPage.xaml` with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial.Models"
             Title="Calendar"
             x:Class="GraphTutorial.CalendarPage">
    <ContentPage.Resources>
        <local:GraphDateTimeTimeZoneConverter x:Key="DateConverter" />
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout>
            <ListView x:Name="CalendarList"
                      VerticalOptions="StartAndExpand"
                      Margin="10,10,10,10">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Margin="10,10,10,10">
                                <Label Text="{Binding Path=Subject}"
                                       FontAttributes="Bold"
                                       FontSize="Medium" />
                                <Label Text="{Binding Path=Organizer.EmailAddress.Name}"
                                       FontSize="Small" />
                                <StackLayout Orientation="Horizontal">
                                    <Label Text="{Binding Path=Start, Converter={StaticResource DateConverter}}"
                                       FontSize="Micro" />
                                    <Label Text="to"
                                           FontSize="Micro" />
                                    <Label Text="{Binding Path=End, Converter={StaticResource DateConverter}}"
                                       FontSize="Micro" />
                                </StackLayout>
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="4405c-120">Cela remplace le `Editor` par un `ListView`.</span><span class="sxs-lookup"><span data-stu-id="4405c-120">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="4405c-121">Le `DataTemplate` utilisé pour afficher chaque élément utilise l `GraphDateTimeTimeZoneConverter` 'pour convertir `Start` les `End` propriétés et de l'événement en chaîne.</span><span class="sxs-lookup"><span data-stu-id="4405c-121">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span> <span data-ttu-id="4405c-122">Maintenant, `CalendarPage.xaml.cs` ouvrez et supprimez les lignes suivantes `OnAppearing` de la fonction.</span><span class="sxs-lookup"><span data-stu-id="4405c-122">Now open `CalendarPage.xaml.cs` and remove the following lines from the `OnAppearing` function.</span></span>

```cs
// Temporary
JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
```

<span data-ttu-id="4405c-123">À leur place, ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="4405c-123">In their place, add the following code.</span></span>

```cs
// Add the events to the list view
CalendarList.ItemsSource = events.CurrentPage.ToList();
```

<span data-ttu-id="4405c-124">Exécutez l'application, connectez-vous, puis cliquez sur l'élément de navigation **calendrier** .</span><span class="sxs-lookup"><span data-stu-id="4405c-124">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="4405c-125">Vous devriez voir la liste des événements dont les valeurs de **début** et de **fin** sont mises en forme.</span><span class="sxs-lookup"><span data-stu-id="4405c-125">You should see the list of events with the **Start** and **End** values formatted.</span></span>

![Capture d'écran du tableau des événements](./images/calendar-page.png)
