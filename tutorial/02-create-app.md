<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5a438-101">Ouvrez Visual Studio, puis sélectionnez **créer un nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="5a438-101">Open Visual Studio, and select **Create a new project**.</span></span> <span data-ttu-id="5a438-102">Dans la boîte de dialogue **créer un nouveau projet** , choisissez **application mobile (Xamarin. Forms)**, puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="5a438-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

![Boîte de dialogue créer un nouveau projet dans Visual Studio 2019](images/new-project-dialog.png)

<span data-ttu-id="5a438-104">Dans la boîte de dialogue **configurer un nouveau projet** , entrez `GraphTutorial` le **nom du projet** et le nom de la **solution**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="5a438-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5a438-105">Assurez-vous d’entrer exactement le même nom pour le projet Visual Studio spécifié dans ces instructions d’atelier.</span><span class="sxs-lookup"><span data-stu-id="5a438-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="5a438-106">Le nom du projet Visual Studio devient partie intégrante de l’espace de noms dans le code.</span><span class="sxs-lookup"><span data-stu-id="5a438-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="5a438-107">Le code à l’intérieur de ces instructions dépend de l’espace de noms correspondant au nom de projet Visual Studio spécifié dans ces instructions.</span><span class="sxs-lookup"><span data-stu-id="5a438-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="5a438-108">Si vous utilisez un nom de projet différent, le code n’est pas compilé, sauf si vous ajustez tous les espaces de noms pour qu’ils correspondent au nom de projet Visual Studio que vous entrez lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="5a438-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

![Boîte de dialogue Configurer un nouveau projet de Visual Studio 2019](images/configure-new-project-dialog.png)

<span data-ttu-id="5a438-110">Dans la boîte de dialogue **nouvelle application** multiplateforme, sélectionnez le modèle **vierge** , puis sélectionnez les plateformes que vous souhaitez \*\*\*\* créer sous plateformes.</span><span class="sxs-lookup"><span data-stu-id="5a438-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="5a438-111">Sélectionnez **OK** pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="5a438-111">Select **OK** to create the solution.</span></span>

![Nouvelle boîte de dialogue d’application multiplateforme Visual Studio 2019](images/new-cross-platform-app-dialog.png)

<span data-ttu-id="5a438-113">Avant de poursuivre, installez des packages NuGet supplémentaires que vous utiliserez plus tard.</span><span class="sxs-lookup"><span data-stu-id="5a438-113">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="5a438-114">[Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) pour gérer l’authentification Azure ad et la gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="5a438-114">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="5a438-115">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="5a438-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="5a438-116">Sélectionnez **outils > gestionnaire de package NuGet > console Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="5a438-116">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="5a438-117">Dans la console Gestionnaire de package, entrez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="5a438-117">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 3.0.8 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.15.0 -Project GraphTutorial
```

## <a name="design-the-app"></a><span data-ttu-id="5a438-118">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="5a438-118">Design the app</span></span>

<span data-ttu-id="5a438-119">Commencez par mettre à jour `App` la classe pour ajouter des variables afin de suivre l’état d’authentification et l’utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="5a438-119">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span> <span data-ttu-id="5a438-120">Dans l' **Explorateur de solutions**, développez le projet **GraphTutorial** , puis développez le fichier **app. Xaml** .</span><span class="sxs-lookup"><span data-stu-id="5a438-120">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="5a438-121">Ouvrez le fichier **app.Xaml.cs** et ajoutez les instructions `using` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="5a438-121">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

<span data-ttu-id="5a438-122">Ensuite, ajoutez l' `INotifyPropertyChanged` interface à la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="5a438-122">Next, add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

```cs
public partial class App : Application, INotifyPropertyChanged
```

<span data-ttu-id="5a438-123">À présent, ajoutez les propriétés suivantes `App` à la classe.</span><span class="sxs-lookup"><span data-stu-id="5a438-123">Now add the following properties to the `App` class.</span></span>

```cs
// Is a user signed in?
private bool isSignedIn;
public bool IsSignedIn
{
    get { return isSignedIn; }
    set
    {
        isSignedIn = value;
        OnPropertyChanged("IsSignedIn");
        OnPropertyChanged("IsSignedOut");
    }
}

public bool IsSignedOut { get { return !isSignedIn; } }

// The user's display name
private string userName;
public string UserName
{
    get { return userName; }
    set
    {
        userName = value;
        OnPropertyChanged("UserName");
    }
}

// The user's email address
private string userEmail;
public string UserEmail
{
    get { return userEmail; }
    set
    {
        userEmail = value;
        OnPropertyChanged("UserEmail");
    }
}

// The user's profile photo
private ImageSource userPhoto;
public ImageSource UserPhoto
{
    get { return userPhoto; }
    set
    {
        userPhoto = value;
        OnPropertyChanged("UserPhoto");
    }
}
```

<span data-ttu-id="5a438-124">À présent, ajoutez les fonctions suivantes `App` à la classe.</span><span class="sxs-lookup"><span data-stu-id="5a438-124">Now add the following functions to the `App` class.</span></span> <span data-ttu-id="5a438-125">Les `SignIn`fonctions `SignOut`, et `GetUserInfo` sont simplement des espaces réservés pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="5a438-125">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

```cs
public async Task SignIn()
{
    await GetUserInfo();

    IsSignedIn = true;
}

public async Task SignOut()
{
    UserPhoto = null;
    UserName = string.Empty;
    UserEmail = string.Empty;
    IsSignedIn = false;
}

private async Task GetUserInfo()
{
    UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
    UserName = "Adele Vance";
    UserEmail = "adelev@contoso.com";
}

private Stream GetUserPhoto()
{
    // Return the default photo
    return Assembly.GetExecutingAssembly().GetManifestResourceStream("GraphTutorial.no-profile-pic.png");
}
```

<span data-ttu-id="5a438-126">La `GetUserPhoto` fonction renvoie une photo par défaut pour le moment.</span><span class="sxs-lookup"><span data-stu-id="5a438-126">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="5a438-127">Vous pouvez soit fournir votre propre fichier ici, soit télécharger celui utilisé dans l’exemple à partir de [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span><span class="sxs-lookup"><span data-stu-id="5a438-127">You can either supply your own file here, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="5a438-128">Copiez le fichier dans `./GraphTutorial/GraphTutorial` le répertoire.</span><span class="sxs-lookup"><span data-stu-id="5a438-128">Copy the file to the `./GraphTutorial/GraphTutorial` directory.</span></span> <span data-ttu-id="5a438-129">Cliquez avec le bouton droit sur le projet **GraphTutorial** dans l' **Explorateur de solutions** et sélectionnez **Ajouter**, puis **élément existant...**. Sélectionnez le `no-profile-pic.png` fichier et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="5a438-129">Right-click the **GraphTutorial** project in **Solution Explorer** and select **Add**, then **Existing Item...**. Select the `no-profile-pic.png` file and select **Add**.</span></span> <span data-ttu-id="5a438-130">À présent, cliquez avec le bouton droit sur le fichier dans l' **Explorateur de solutions** , puis sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="5a438-130">Now right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="5a438-131">Dans la fenêtre **Propriétés** , modifiez l’action de **génération** en **ressource incorporée**.</span><span class="sxs-lookup"><span data-stu-id="5a438-131">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

![Capture d’écran de la fenêtre Propriétés pour le fichier PNG](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="5a438-133">Navigation de l’application</span><span class="sxs-lookup"><span data-stu-id="5a438-133">App navigation</span></span>

<span data-ttu-id="5a438-134">Ensuite, définissez la page principale de l’application sur une [page maître/détails](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span><span class="sxs-lookup"><span data-stu-id="5a438-134">Next, change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="5a438-135">Cela permet de fournir un menu de navigation permettant de basculer entre l’affichage dans l’application.</span><span class="sxs-lookup"><span data-stu-id="5a438-135">This will provide a navigation menu to switch between view in the app.</span></span>

<span data-ttu-id="5a438-136">Ouvrez le fichier **MainPage. Xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="5a438-136">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial"
             x:Class="GraphTutorial.MainPage">

    <MasterDetailPage.Master>
        <local:MenuPage/>
    </MasterDetailPage.Master>

    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:WelcomePage/>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>

</MasterDetailPage>
```

#### <a name="implement-the-menu"></a><span data-ttu-id="5a438-137">Implémenter le menu</span><span class="sxs-lookup"><span data-stu-id="5a438-137">Implement the menu</span></span>

<span data-ttu-id="5a438-138">Commencez par créer un modèle pour représenter les éléments de menu.</span><span class="sxs-lookup"><span data-stu-id="5a438-138">Start by creating a model to represent the menu items.</span></span> <span data-ttu-id="5a438-139">Cliquez avec le bouton droit sur le projet **GraphTutorial** et sélectionnez **Ajouter**, puis **nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="5a438-139">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="5a438-140">Nommez le `Models`dossier.</span><span class="sxs-lookup"><span data-stu-id="5a438-140">Name the folder `Models`.</span></span>

<span data-ttu-id="5a438-141">Cliquez avec le bouton droit sur le dossier **modèles** , sélectionnez **Ajouter**, puis **classe..**.. Nommez la `NavMenuItem` classe et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="5a438-141">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span> <span data-ttu-id="5a438-142">Ouvrez le fichier **NavMenuItem.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="5a438-142">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

```cs
namespace GraphTutorial.Models
{
    public enum MenuItemType
    {
        Welcome,
        Calendar
    }

    public class NavMenuItem
    {
        public MenuItemType Id { get; set; }

        public string Title { get; set; }
    }
}
```

<span data-ttu-id="5a438-143">À présent, ajoutez la page de menu.</span><span class="sxs-lookup"><span data-stu-id="5a438-143">Now add the menu page.</span></span> <span data-ttu-id="5a438-144">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `MenuPage`la page.</span><span class="sxs-lookup"><span data-stu-id="5a438-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="5a438-145">Cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="5a438-145">Select **Add**.</span></span> <span data-ttu-id="5a438-146">Ouvrez le fichier **MenuPage. Xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="5a438-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.UseSafeArea="true"
             Title="Menu"
             x:Class="GraphTutorial.MenuPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout VerticalOptions="Start" HorizontalOptions="Center">
            <StackLayout x:Name="UserArea" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Sign in to get started"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Medium"
                       Margin="10,20,10,20" />
                <Button Text="Sign in"
                        Clicked="OnSignIn"
                        HorizontalOptions="Center" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Image Source="{Binding Path=UserPhoto, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       Margin="0,20,0,10" />
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Small" />
                <Label Text="{Binding Path=UserEmail, Source={x:Static Application.Current}}"
                       HorizontalOptions="Center"
                       FontAttributes="Italic" />
                <Button Text="Sign out"
                        Margin="0,20,0,20"
                        Clicked="OnSignOut"
                        HorizontalOptions="Center" />
                <ListView x:Name="ListViewMenu"
                          HasUnevenRows="True"
                          HorizontalOptions="Start">
                    <ListView.ItemTemplate>
                        <DataTemplate>
                            <ViewCell>
                                <Grid Padding="10">
                                    <Label Text="{Binding Title}" FontSize="20"/>
                                </Grid>
                            </ViewCell>
                        </DataTemplate>
                    </ListView.ItemTemplate>
                </ListView>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="5a438-147">Maintenant, développez **MenuPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MenuPage.Xaml.cs** .</span><span class="sxs-lookup"><span data-stu-id="5a438-147">Now, expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="5a438-148">Remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="5a438-148">Replace its contents with the following.</span></span>

```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

using GraphTutorial.Models;

namespace GraphTutorial
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MenuPage : ContentPage
    {
        MainPage RootPage => Application.Current.MainPage as MainPage;
        List<NavMenuItem> menuItems;

        public MenuPage ()
        {
            InitializeComponent ();

            // Add items to the menu
            menuItems = new List<NavMenuItem>
            {
                new NavMenuItem {Id = MenuItemType.Welcome, Title="Home" },
                new NavMenuItem {Id = MenuItemType.Calendar, Title="Calendar" }
            };
            ListViewMenu.ItemsSource = menuItems;

            // Initialize the selected item
            ListViewMenu.SelectedItem = menuItems[0];

            // Handle the ItemSelected event to navigate to the
            // selected page
            ListViewMenu.ItemSelected += async (sender, e) =>
            {
                if (e.SelectedItem == null)
                    return;

                var id = (int)((NavMenuItem)e.SelectedItem).Id;
                await RootPage.NavigateFromMenu(id);
            };
        }

        private async void OnSignOut(object sender, EventArgs e)
        {
            var signout = await DisplayAlert("Sign out?", "Do you want to sign out?", "Yes", "No");
            if (signout)
            {
                await (Application.Current as App).SignOut();
            }
        }

        private async void OnSignIn(object sender, EventArgs e)
        {
            await (Application.Current as App).SignIn();
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="5a438-149">Visual Studio signale les erreurs dans **MenuPage.Xaml.cs**.</span><span class="sxs-lookup"><span data-stu-id="5a438-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="5a438-150">Ces erreurs seront résolues au cours d’une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="5a438-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="5a438-151">Implémenter la page d’accueil</span><span class="sxs-lookup"><span data-stu-id="5a438-151">Implement the welcome page</span></span>

<span data-ttu-id="5a438-152">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `WelcomePage`la page.</span><span class="sxs-lookup"><span data-stu-id="5a438-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="5a438-153">Cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="5a438-153">Select **Add**.</span></span> <span data-ttu-id="5a438-154">Ouvrez le fichier **WelcomePage. Xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="5a438-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             Title="Home"
             x:Class="GraphTutorial.WelcomePage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="UWP" Value="10, 10, 10, 10" />
        </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Graph Xamarin Tutorial App"
                       HorizontalOptions="Center"
                       FontAttributes="Bold"
                       FontSize="Large"
                       Margin="10,20,10,20" />

            <!-- Signed out UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedOut, Source={x:Static Application.Current}}">
                <Label Text="Please sign in to get started"
                       HorizontalOptions="Center"
                       FontSize="Medium"
                       Margin="10,0,10,20"/>
                <Button Text="Sign in"
                        HorizontalOptions="Center"
                        Clicked="OnSignIn" />
            </StackLayout>

            <!-- Signed in UI -->
            <StackLayout IsVisible="{Binding Path=IsSignedIn, Source={x:Static Application.Current}}">
                <Label Text="{Binding Path=UserName, Source={x:Static Application.Current}, StringFormat='Welcome \{0\}!'}"
                       HorizontalOptions="Center"
                       FontSize="Medium"/>
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

<span data-ttu-id="5a438-155">Maintenant, développez **WelcomePage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **WelcomePage.Xaml.cs** .</span><span class="sxs-lookup"><span data-stu-id="5a438-155">Now, expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="5a438-156">Ajoutez la fonction suivante à la classe `WelcomePage`.</span><span class="sxs-lookup"><span data-stu-id="5a438-156">Add the following function to the `WelcomePage` class.</span></span>

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a><span data-ttu-id="5a438-157">Ajouter une page de calendrier</span><span class="sxs-lookup"><span data-stu-id="5a438-157">Add calendar page</span></span>

<span data-ttu-id="5a438-158">Maintenant, ajoutez une page de calendrier.</span><span class="sxs-lookup"><span data-stu-id="5a438-158">Now add a calendar page.</span></span> <span data-ttu-id="5a438-159">Il s’agit uniquement d’un espace réservé pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="5a438-159">This will just be a placeholder for now.</span></span> <span data-ttu-id="5a438-160">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `CalendarPage`la page.</span><span class="sxs-lookup"><span data-stu-id="5a438-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="5a438-161">Cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="5a438-161">Select **Add**.</span></span>

<span data-ttu-id="5a438-162">Laissez la page ajoutée telle quelle pour le moment.</span><span class="sxs-lookup"><span data-stu-id="5a438-162">Leave the added page as-is for now.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="5a438-163">Mettre à jour le code MainPage-behind</span><span class="sxs-lookup"><span data-stu-id="5a438-163">Update MainPage code-behind</span></span>

<span data-ttu-id="5a438-164">Maintenant que toutes les pages sont en place, mettez à jour le code-behind pour **MainPage. Xaml**.</span><span class="sxs-lookup"><span data-stu-id="5a438-164">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span> <span data-ttu-id="5a438-165">Développez **MainPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MainPage.Xaml.cs** et remplacez l’intégralité de son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="5a438-165">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

```cs
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Xamarin.Forms;
using GraphTutorial.Models;

namespace GraphTutorial
{
    public partial class MainPage : MasterDetailPage
    {
        Dictionary<int, NavigationPage> MenuPages = new Dictionary<int, NavigationPage>();

        public MainPage()
        {
            InitializeComponent();

            MasterBehavior = MasterBehavior.Popover;

            // Load the welcome page at start
            MenuPages.Add((int)MenuItemType.Welcome, (NavigationPage)Detail);
        }

        // Navigate to the selected page
        public async Task NavigateFromMenu(int id)
        {
            if (!MenuPages.ContainsKey(id))
            {
                switch (id)
                {
                    case (int)MenuItemType.Welcome:
                        MenuPages.Add(id, new NavigationPage(new WelcomePage()));
                        break;
                    case (int)MenuItemType.Calendar:
                        MenuPages.Add(id, new NavigationPage(new CalendarPage()));
                        break;
                }
            }

            var newPage = MenuPages[id];

            if (newPage != null && Detail != newPage)
            {
                Detail = newPage;

                if (Device.RuntimePlatform == Device.Android)
                    await Task.Delay(100);

                IsPresented = false;
            }
        }
    }
}
```

<span data-ttu-id="5a438-166">Enregistrez toutes vos modifications.</span><span class="sxs-lookup"><span data-stu-id="5a438-166">Save all of your changes.</span></span> <span data-ttu-id="5a438-167">Cliquez avec le bouton droit sur le projet que vous souhaitez exécuter (Android, iOS ou UWP) et sélectionnez **définir comme projet de démarrage**.</span><span class="sxs-lookup"><span data-stu-id="5a438-167">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="5a438-168">Appuyez sur **F5** ou sélectionnez déboguer **> démarrer** le débogage dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5a438-168">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

![Captures d’écran des versions Android, iOS et UWP de l’application](./images/welcome-page.png)
