
Boutique Diayma - Application E-commerce ASP.NET Core
Description du Projet
Ce projet est une application de commerce électronique développée avec ASP.NET Core MVC. Il a été créé dans le cadre d'un exercice de mise en conformité et de débogage pour se familiariser avec l'environnement de développement intégré (EDI), le workflow de débogage, la gestion de versions avec Git/GitHub et le déploiement d'applications .NET.

Configuration Requise
•	SDK .NET 10.0 (version mise à jour)
•	Visual Studio 2022 ou VS Code
•	Git et Compte GitHub

1. Informations sur la Solution (Tâches 2 & 3)
 Projets de la Solution
La solution Diayma.sln contient 1 projet principal :
•	Diayma (P2FixAnAppDotNetCode.csproj) - Application web ASP.NET Core MVC
Version SDK .NET
La version du Framework cible utilisée par le projet est : .NET 10.0 (net10.0).
Note : Le projet original a été migré de .NET Core 2.0 vers .NET 10.0 pour être compatible avec un SDK plus récent. Le fichier Diayma.csproj a été mis à jour en conséquence.

2. Bugs Identifiés et Corrigés (Tâche 6)
Deux bugs majeurs ont été identifiés dans le code et les corrections suivantes ont été appliquées :
Bug	Fichier / Méthode	Description du Problème	Solution Appliquée
Bug #1	Cart.cs / GetTotalValue()	Le total du panier ne multipliait pas le prix unitaire par la quantité de chaque produit.	Mise à jour pour inclure la multiplication par la quantité (x.Product.Price * x.Quantity).
Bug #2	Cart.cs / FindProductInCartLines	Risque de NullReferenceException si aucun produit n'était trouvé dans le panier.	Utilisation de l'opérateur Null-Conditional (?.) pour retourner null en toute sécurité si la ligne n'existe pas.
Code de la Correction pour Bug #1
Fichier : P2FixAnAppDotNetCode/Models/Cart.cs (Ligne 66)
C#
public double GetTotalValue()
{
    // Correction: Multiplie le prix par la quantité
    return GetCartLineList().Sum(x => x.Product.Price * x.Quantity);
}

3. Points d'Arrêt pour Débogage (Tâche 6)
Pour l'exploration du code et le débogage, les points d'arrêt ont été placés aux lignes suivantes, juste avant ou sur l'instruction clé :
Fichier	Ligne	Objectif du Point d'Arrêt
CartSummaryViewComponent	12	Débogage du composant affichant le résumé du panier.
ProductController	15	Débogage de l'injection des services et de l'accès aux produits.
OrderController	17	Débogage du traitement des commandes.
CartController	15	Débogage des opérations d'ajout/suppression dans le panier.
Startup	20	Débogage de la configuration initiale des services (Constructeur).
________________________________________
4. Flux d'Exécution : Affichage des Produits (Tâche 5)
Le flux détaillé des namespaces, classes et méthodes visités avant l'affichage des produits sur l'écran d'accueil (/) se déroule comme suit :
1.	Démarrage (Program)
o	P2FixAnAppDotNetCode : Program.Main() → Program.BuildWebHost()
o	P2FixAnAppDotNetCode : Startup (Constructeur, ligne 20) $\rightarrow$ Point d'arrêt
o	P2FixAnAppDotNetCode : Startup.ConfigureServices() (Enregistrement DI)
o	P2FixAnAppDotNetCode.Models.Repositories : ProductRepository (Constructeur) $\rightarrow$ GenerateProductData() (Initialisation des données)
o	P2FixAnAppDotNetCode : Startup.Configure() (Configuration du pipeline HTTP et du routage)
2.	Requête HTTP (/)
o	Le routage MVC mappe la requête par défaut (/) à : ProductController.Index()
o	P2FixAnAppDotNetCode.Controllers : ProductController (Constructeur, ligne 15) $\rightarrow$ Point d'arrêt
o	P2FixAnAppDotNetCode.Controllers : ProductController.Index() (Action)
3.	Récupération des Données
o	P2FixAnAppDotNetCode.Models.Services : ProductService.GetAllProducts()
o	P2FixAnAppDotNetCode.Models.Repositories : ProductRepository.GetAllProducts() (Filtre Stock > 0, Tri par Nom)
o	Retour de la liste des produits au ProductController.Index().
4.	Rendu de la Vue
o	ProductController.Index() retourne View(products).
o	Le moteur de vues Razor utilise : Views/Product/Index.cshtml
o	Le layout principal est inclus : Views/Shared/_Layout.cshtml
5.	Composants de Vue
o	Lors du rendu du layout, le composant de vue est appelé :
o	P2FixAnAppDotNetCode.Components : CartSummaryViewComponent (Constructeur, ligne 12) $\rightarrow$ Point d'arrêt
o	P2FixAnAppDotNetCode.Components : CartSummaryViewComponent.Invoke() (Affiche le résumé du panier)
6.	Réponse
o	Le HTML généré est envoyé au navigateur.
Mode de Débogage Recommandé	Description
Pas à pas détaillé (F11)	Pour suivre l'exécution en entrant dans chaque méthode (incluant les méthodes du framework).
Pas à pas principal (F10)	Pour exécuter ligne par ligne dans le contexte actuel sans entrer dans les appels de méthodes.
________________________________________
5. Déploiement (Tâche 7)
La solution a été déployée en un exécutable autonome Windows à l'aide de la commande dotnet publish.

### Commande de Publication
```bash
dotnet publish -c Release -r win-x64 --self-contained true -p:PublishSingleFile=true
```

**Paramètres de la commande :**
- `-c Release` : Compile en configuration optimisée.
- `-r win-x64` : Cible un runtime Windows 64-bit.
- `--self-contained true` : Inclut le runtime .NET, permettant l'exécution sans installation préalable du SDK.
- `-p:PublishSingleFile=true` : Empaquette l'application dans un seul fichier exécutable.

### Emplacement de l'Exécutable

**Fichier exécutable principal :**
```
P2FixAnAppDotNetCode\bin\Release\net10.0\win-x64\publish\Diayma.exe
```

### Fichiers à Inclure dans le Drive

Pour que l'application fonctionne correctement, les fichiers suivants doivent être inclus dans le lien Drive :

**Fichiers essentiels :**
1. **`Diayma.exe`** - Exécutable principal (contient le runtime .NET intégré)
2. **`wwwroot/`** - Dossier complet contenant les fichiers statiques (CSS, JavaScript, images, bibliothèques)
3. **`appsettings.json`** - Fichier de configuration de l'application
4. **`web.config`** - Configuration IIS (optionnel mais recommandé)

**Structure recommandée pour le partage :**
```
Diayma-Release.zip
├── Diayma.exe
├── appsettings.json
├── web.config
└── wwwroot/
    ├── css/
    ├── js/
    ├── images/
    └── lib/
```

### 🔗 Lien de l'Exécutable

L'exécutable est disponible dans le dossier de publication et partagé via :
- Lien Google Drive :https://drive.google.com/file/d/1X0B_ebkQHlwUhqTmYPFx_TXhNpLkiekY/view?usp=drive_link


### Instructions d'Utilisation

1. Téléchargez tous les fichiers depuis le Drive (ou décompressez le ZIP)
2. Placez tous les fichiers dans le même dossier
3. Double-cliquez sur **`Diayma.exe`** pour lancer l'application
4. L'application démarre un serveur web local (généralement sur `http://localhost:6000`)
5. Ouvrez votre navigateur à l'adresse affichée dans la console

**Note :** L'exécutable est autonome et ne nécessite pas l'installation du SDK .NET sur l'ordinateur cible.

6. Fonctionnalités Optionnelles (Tâche 8)
a) Ajout du Support de la Langue Wolof
•	La langue Wolof (wo) a été ajoutée.
•	Les fichiers de ressources Wolof (.wo.resx) ont été créés et traduits.
•	La configuration de localisation dans Startup.cs a été mise à jour pour supporter cette nouvelle culture.
b) Commits Significatifs
Au moins 3 commits significatifs ont été réalisés pour documenter les changements importants :
1.	feat: Upgrade to .NET 10.0 SDK : Mise à jour du framework cible et des dépendances.
2.	fix: Corrected cart total calculation and NullRef issue : Implémentation des deux corrections de bugs (Cart.cs).
3.	feat: Added Wolof (wo) language support and localization : Ajout des ressources et configuration pour la nouvelle langue.
c) Dépôt sur Google Classrooms
•	✅ Le lien vers le dépôt GitHub a été déposé sur Google Classrooms.

## 7. Structure du Projet

### Architecture du Projet

Le projet suit l'architecture MVC (Model-View-Controller) avec une séparation claire des responsabilités :

```
Diayma/
├── Diayma.sln                          # Fichier solution Visual Studio
│
└── P2FixAnAppDotNetCode/               # Projet principal
    ├── Diayma.csproj                   # Fichier de configuration du projet
    ├── Program.cs                      # Point d'entrée de l'application
    ├── Startup.cs                      # Configuration des services et middleware
    │
    ├── Controllers/                    # Contrôleurs MVC
    │   ├── CartController.cs          # Gestion du panier (ajout, suppression)
    │   ├── LanguageController.cs      # Changement de langue
    │   ├── OrderController.cs         # Gestion des commandes
    │   └── ProductController.cs       # Affichage des produits
    │
    ├── Models/                         # Modèles de données
    │   ├── Cart.cs                    # Modèle du panier d'achat
    │   ├── ICart.cs                   # Interface du panier
    │   ├── Order.cs                   # Modèle de commande
    │   ├── Product.cs                 # Modèle de produit
    │   │
    │   ├── Repositories/              # Couche d'accès aux données
    │   │   ├── IOrderRepository.cs
    │   │   ├── IProductRepository.cs
    │   │   ├── OrderRepository.cs
    │   │   └── ProductRepository.cs  # Gestion des produits en mémoire
    │   │
    │   ├── Services/                  # Couche de services métier
    │   │   ├── ILanguageService.cs
    │   │   ├── IOrderService.cs
    │   │   ├── IProductService.cs
    │   │   ├── LanguageService.cs    # Gestion de la localisation
    │   │   ├── OrderService.cs      # Logique métier des commandes
    │   │   └── ProductService.cs    # Logique métier des produits
    │   │
    │   └── ViewModels/               # Modèles de vue
    │       └── LanguageViewModel.cs
    │
    ├── Views/                         # Vues Razor
    │   ├── _ViewImports.cshtml       # Imports globaux
    │   ├── _ViewStart.cshtml         # Configuration de démarrage des vues
    │   │
    │   ├── Cart/
    │   │   └── Index.cshtml          # Vue du panier
    │   │
    │   ├── Home/
    │   │   └── Index.cshtml          # Page d'accueil
    │   │
    │   ├── Order/
    │   │   ├── Completed.cshtml      # Confirmation de commande
    │   │   └── Index.cshtml          # Formulaire de commande
    │   │
    │   ├── Product/
    │   │   └── Index.cshtml          # Liste des produits
    │   │
    │   └── Shared/                   # Vues partagées
    │       ├── _Layout.cshtml        # Layout principal
    │       ├── _ValidationScriptsPartial.cshtml
    │       └── Components/
    │           ├── CartSummary/
    │           │   └── Default.cshtml # Résumé du panier
    │           └── LanguageSelector/
    │               └── Default.cshtml # Sélecteur de langue
    │
    ├── Components/                    # Composants de vue (ViewComponents)
    │   ├── CartSummaryViewComponent.cs
    │   └── LanguageSelectorViewComponent.cs
    │
    ├── Resources/                     # Fichiers de ressources (localisation)
    │   ├── Controllers/
    │   │   └── OrderController.*.resx # Ressources pour OrderController
    │   │
    │   ├── Models/
    │   │   └── ViewModels/
    │   │       ├── LanguageViewModel.*.resx
    │   │       └── Order.*.resx       # Messages de validation
    │   │
    │   └── Views/
    │       ├── Cart/
    │       │   └── Index.*.resx      # Traductions de la vue panier
    │       ├── Order/
    │       │   ├── Completed.*.resx
    │       │   └── Index.*.resx
    │       ├── Product/
    │       │   └── Index.*.resx      # Traductions de la vue produits
    │       └── Shared/
    │           └── Components/
    │               ├── CartSummary/
    │               │   └── Default.*.resx
    │               └── LanguageSelector/
    │                   └── Default.*.resx
    │       # * = .en.resx, .fr.resx, .wo.resx (Anglais, Français, Wolof)
    │
    ├── wwwroot/                      # Fichiers statiques
    │   ├── css/                      # Feuilles de style
    │   ├── js/                       # Scripts JavaScript
    │   ├── images/                   # Images (bannières)
    │   └── lib/                      # Bibliothèques tierces
    │       ├── bootstrap/            # Framework CSS Bootstrap
    │       ├── fontawesome/         # Icônes
    │       ├── jquery/              # Bibliothèque jQuery
    │       ├── jquery-validation/   # Validation de formulaires
    │       └── tether/              # Utilitaires
    │
    ├── Properties/
    │   ├── launchSettings.json      # Configuration de lancement
    │   └── PublishProfiles/         # Profils de publication
    │
    ├── appsettings.json              # Configuration de l'application
    └── appsettings.Development.json  # Configuration de développement
```

