<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b18dc-101">Ouvrez Visual Studio, puis sélectionnez **fichier _GT_ nouveau projet >**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-101">Open Visual Studio, and select **File > New > Project**.</span></span> <span data-ttu-id="b18dc-102">Dans la boîte de dialogue **nouveau projet** , procédez comme suit:</span><span class="sxs-lookup"><span data-stu-id="b18dc-102">In the **New Project** dialog, do the following:</span></span>

1. <span data-ttu-id="b18dc-103">Sélectionnez **Visual C# _GT_ multiplateforme**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-103">Select **Visual C# > Cross-Platform**.</span></span>
1. <span data-ttu-id="b18dc-104">Sélectionnez **application mobile (Xamarin. Forms)**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-104">Select **Mobile App (Xamarin.Forms)**.</span></span>
1. <span data-ttu-id="b18dc-105">Entrez **GraphTutorial** pour le nom du projet.</span><span class="sxs-lookup"><span data-stu-id="b18dc-105">Enter **GraphTutorial** for the Name of the project.</span></span>

![Boîte de dialogue créer un nouveau projet dans Visual Studio 2017](images/new-project-dialog.png)

> [!IMPORTANT]
> <span data-ttu-id="b18dc-107">Assurez-vous d'entrer exactement le même nom pour le projet Visual Studio spécifié dans ces instructions d'atelier.</span><span class="sxs-lookup"><span data-stu-id="b18dc-107">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="b18dc-108">Le nom du projet Visual Studio devient partie intégrante de l'espace de noms dans le code.</span><span class="sxs-lookup"><span data-stu-id="b18dc-108">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="b18dc-109">Le code à l'intérieur de ces instructions dépend de l'espace de noms correspondant au nom de projet Visual Studio spécifié dans ces instructions.</span><span class="sxs-lookup"><span data-stu-id="b18dc-109">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="b18dc-110">Si vous utilisez un nom de projet différent, le code n'est pas compilé, sauf si vous ajustez tous les espaces de noms pour qu'ils correspondent au nom de projet Visual Studio que vous entrez lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="b18dc-110">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

<span data-ttu-id="b18dc-111">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-111">Select **OK**.</span></span> <span data-ttu-id="b18dc-112">Dans la boîte de dialogue **nouvelle application** multiplateforme, sélectionnez le modèle **vierge** et assurez-vous que la sélection de **stratégie de partage de code** est **.NET standard**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-112">In the **New Cross Platform App** dialog, select the **Blank** template, and ensure that the **Code Sharing Strategy** selection is **.NET Standard**.</span></span> <span data-ttu-id="b18dc-113">Si vous envisagez d'ignorer une plateforme spécifique, vous pouvez la désélectionner \*\*\*\* sous plateformes.</span><span class="sxs-lookup"><span data-stu-id="b18dc-113">If you plan to skip a specific platform, you can unselect it now under **Platforms**.</span></span> <span data-ttu-id="b18dc-114">Sélectionnez **OK** pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="b18dc-114">Select **OK** to create the solution.</span></span>

![Nouvelle boîte de dialogue d'application multiplateforme Visual Studio 2017](images/new-cross-platform-app-dialog.png)

<span data-ttu-id="b18dc-116">Avant de poursuivre, installez des packages NuGet supplémentaires que vous utiliserez plus tard.</span><span class="sxs-lookup"><span data-stu-id="b18dc-116">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="b18dc-117">[Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) pour gérer l'authentification Azure ad et la gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="b18dc-117">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="b18dc-118">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b18dc-118">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="b18dc-119">Sélectionnez **Outils _GT_ gestionnaire de package NuGet _GT_ console Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-119">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="b18dc-120">Dans la console Gestionnaire de package, entrez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="b18dc-120">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial
Install-Package Xamarin.Android.Support.Compat -Version 27.0.2.1 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial.Android
Install-Package Microsoft.Identity.Client -Version 2.7.0 -Project GraphTutorial.iOS
Install-Package Microsoft.Graph -Version 1.12.0 -Project GraphTutorial
```

## <a name="design-the-app"></a><span data-ttu-id="b18dc-121">Concevoir l'application</span><span class="sxs-lookup"><span data-stu-id="b18dc-121">Design the app</span></span>

<span data-ttu-id="b18dc-122">Commencez par mettre à jour `App` la classe pour ajouter des variables afin de suivre l'état d'authentification et l'utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="b18dc-122">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span> <span data-ttu-id="b18dc-123">Dans l' **Explorateur de solutions**, développez le projet **GraphTutorial** , puis développez le fichier **app. Xaml** .</span><span class="sxs-lookup"><span data-stu-id="b18dc-123">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="b18dc-124">Ouvrez le fichier **app.Xaml.cs** et ajoutez les instructions `using` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="b18dc-124">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

```cs
using System.ComponentModel;
using System.IO;
using System.Reflection;
using System.Threading.Tasks;
```

<span data-ttu-id="b18dc-125">Ensuite, ajoutez l' `INotifyPropertyChanged` interface à la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="b18dc-125">Next, add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

```cs
public partial class App : Application, INotifyPropertyChanged
```

<span data-ttu-id="b18dc-126">À présent, ajoutez les propriétés suivantes `App` à la classe.</span><span class="sxs-lookup"><span data-stu-id="b18dc-126">Now add the following properties to the `App` class.</span></span>

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

<span data-ttu-id="b18dc-127">À présent, ajoutez les fonctions suivantes `App` à la classe.</span><span class="sxs-lookup"><span data-stu-id="b18dc-127">Now add the following functions to the `App` class.</span></span> <span data-ttu-id="b18dc-128">Les `SignIn`fonctions `SignOut`, et `GetUserInfo` sont simplement des espaces réservés pour l'instant.</span><span class="sxs-lookup"><span data-stu-id="b18dc-128">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

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

<span data-ttu-id="b18dc-129">La `GetUserPhoto` fonction renvoie une photo par défaut pour le moment.</span><span class="sxs-lookup"><span data-stu-id="b18dc-129">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="b18dc-130">Vous pouvez soit fournir votre propre fichier ici, soit télécharger celui utilisé dans l'exemple à partir de [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span><span class="sxs-lookup"><span data-stu-id="b18dc-130">You can either supply your own file here, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="b18dc-131">Copiez le fichier dans `./GraphTutorial/GraphTutorial` le répertoire.</span><span class="sxs-lookup"><span data-stu-id="b18dc-131">Copy the file to the `./GraphTutorial/GraphTutorial` directory.</span></span> <span data-ttu-id="b18dc-132">Cliquez avec le bouton droit sur le projet **GraphTutorial** dans l' **Explorateur de solutions** et choisissez **Ajouter**, puis **élément existant...**. Sélectionnez le `no-profile-pic.png` fichier, puis choisissez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-132">Right-click the **GraphTutorial** project in **Solution Explorer** and choose **Add**, then **Existing Item...**. Select the `no-profile-pic.png` file and choose **Add**.</span></span> <span data-ttu-id="b18dc-133">À présent, cliquez avec le bouton droit sur le fichier dans l' **Explorateur de solutions** , puis choisissez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-133">Now right-click the file in **Solution Explorer** and choose **Properties**.</span></span> <span data-ttu-id="b18dc-134">Dans la fenêtre **Propriétés** , modifiez l'action de **génération** en **ressource incorporée**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-134">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

![Capture d'écran de la fenêtre Propriétés pour le fichier PNG](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="b18dc-136">Navigation de l'application</span><span class="sxs-lookup"><span data-stu-id="b18dc-136">App navigation</span></span>

<span data-ttu-id="b18dc-137">Ensuite, définissez la page principale de l'application sur une [page maître/détails](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span><span class="sxs-lookup"><span data-stu-id="b18dc-137">Next, change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="b18dc-138">Cela permet de fournir un menu de navigation permettant de basculer entre l'affichage dans l'application.</span><span class="sxs-lookup"><span data-stu-id="b18dc-138">This will provide a navigation menu to switch between view in the app.</span></span>

<span data-ttu-id="b18dc-139">Ouvrez le fichier **MainPage. Xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="b18dc-139">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

#### <a name="implement-the-menu"></a><span data-ttu-id="b18dc-140">Implémenter le menu</span><span class="sxs-lookup"><span data-stu-id="b18dc-140">Implement the menu</span></span>

<span data-ttu-id="b18dc-141">Commencez par créer un modèle pour représenter les éléments de menu.</span><span class="sxs-lookup"><span data-stu-id="b18dc-141">Start by creating a model to represent the menu items.</span></span> <span data-ttu-id="b18dc-142">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis choisissez **Ajouter**, puis **nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-142">Right-click the **GraphTutorial** project and choose **Add**, then **New Folder**.</span></span> <span data-ttu-id="b18dc-143">Nommez le `Models`dossier.</span><span class="sxs-lookup"><span data-stu-id="b18dc-143">Name the folder `Models`.</span></span>

<span data-ttu-id="b18dc-144">Cliquez avec le bouton droit sur le dossier **modèles** , choisissez **Ajouter**, puis **classe..**.. Nommez la `NavMenuItem` classe, puis choisissez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-144">Right-click the **Models** folder and choose **Add**, then **Class...**. Name the class `NavMenuItem` and choose **Add**.</span></span> <span data-ttu-id="b18dc-145">Ouvrez le fichier **NavMenuItem.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="b18dc-145">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="b18dc-146">À présent, ajoutez la page de menu.</span><span class="sxs-lookup"><span data-stu-id="b18dc-146">Now add the menu page.</span></span> <span data-ttu-id="b18dc-147">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `MenuPage`la page.</span><span class="sxs-lookup"><span data-stu-id="b18dc-147">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="b18dc-148">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-148">Choose **Add**.</span></span> <span data-ttu-id="b18dc-149">Ouvrez le fichier **MenuPage. Xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="b18dc-149">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="b18dc-150">Maintenant, développez **MenuPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MenuPage.Xaml.cs** .</span><span class="sxs-lookup"><span data-stu-id="b18dc-150">Now, expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="b18dc-151">Remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="b18dc-151">Replace its contents with the following.</span></span>

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
        MainPage RootPage { get => Application.Current.MainPage as MainPage; }
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

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="b18dc-152">Implémenter la page d'accueil</span><span class="sxs-lookup"><span data-stu-id="b18dc-152">Implement the welcome page</span></span>

<span data-ttu-id="b18dc-153">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `WelcomePage`la page.</span><span class="sxs-lookup"><span data-stu-id="b18dc-153">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="b18dc-154">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-154">Choose **Add**.</span></span> <span data-ttu-id="b18dc-155">Ouvrez le fichier **WelcomePage. Xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="b18dc-155">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

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

<span data-ttu-id="b18dc-156">Maintenant, développez **WelcomePage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **WelcomePage.Xaml.cs** .</span><span class="sxs-lookup"><span data-stu-id="b18dc-156">Now, expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="b18dc-157">Ajoutez la fonction suivante à la classe `WelcomePage`.</span><span class="sxs-lookup"><span data-stu-id="b18dc-157">Add the following function to the `WelcomePage` class.</span></span>

```cs
private void OnSignIn(object sender, EventArgs e)
{
    (App.Current.MainPage as MasterDetailPage).IsPresented = true;
}
```

#### <a name="add-calendar-page"></a><span data-ttu-id="b18dc-158">Ajouter une page de calendrier</span><span class="sxs-lookup"><span data-stu-id="b18dc-158">Add calendar page</span></span>

<span data-ttu-id="b18dc-159">Maintenant, ajoutez une page de calendrier.</span><span class="sxs-lookup"><span data-stu-id="b18dc-159">Now add a calendar page.</span></span> <span data-ttu-id="b18dc-160">Il s'agit uniquement d'un espace réservé pour l'instant.</span><span class="sxs-lookup"><span data-stu-id="b18dc-160">This will just be a placeholder for now.</span></span> <span data-ttu-id="b18dc-161">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `CalendarPage`la page.</span><span class="sxs-lookup"><span data-stu-id="b18dc-161">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="b18dc-162">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-162">Choose **Add**.</span></span>

<span data-ttu-id="b18dc-163">Laissez la page ajoutée telle quelle pour le moment.</span><span class="sxs-lookup"><span data-stu-id="b18dc-163">Leave the added page as-is for now.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="b18dc-164">Mettre à jour le code MainPage-behind</span><span class="sxs-lookup"><span data-stu-id="b18dc-164">Update MainPage code-behind</span></span>

<span data-ttu-id="b18dc-165">Maintenant que toutes les pages sont en place, mettez à jour le code-behind pour **MainPage. Xaml**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-165">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span> <span data-ttu-id="b18dc-166">Développez **MainPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MainPage.Xaml.cs** et remplacez l'intégralité de son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="b18dc-166">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

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

<span data-ttu-id="b18dc-167">Enregistrez toutes vos modifications.</span><span class="sxs-lookup"><span data-stu-id="b18dc-167">Save all of your changes.</span></span> <span data-ttu-id="b18dc-168">Cliquez avec le bouton droit sur le projet que vous souhaitez exécuter (Android, iOS ou UWP), puis choisissez **définir comme projet de démarrage**.</span><span class="sxs-lookup"><span data-stu-id="b18dc-168">Right-click the project that you want to run (Android, iOS, or UWP) and choose **Set as StartUp Project**.</span></span> <span data-ttu-id="b18dc-169">Appuyez sur **F5** ou sélectionnez **débogage > démarrer** le débogage dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b18dc-169">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

![Captures d'écran des versions Android, iOS et UWP de l'application](./images/welcome-page.png)