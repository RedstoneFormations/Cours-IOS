# Cours SwiftUI : Interface et Gestion des States

## 📚 Table des matières

1. [Introduction à SwiftUI](#introduction-à-swiftui)
2. [@State - Le state local](#state---le-state-local)
3. [@Binding - Liaison bidirectionnelle](#binding---liaison-bidirectionnelle)
4. [@StateObject et @ObservedObject](#stateobject-et-observedobject)
5. [@EnvironmentObject - State partagé](#environmentobject---state-partagé)
6. [@AppStorage - Persistance simple](#appstorage---persistance-simple)
7. [Bonnes pratiques](#bonnes-pratiques)

---

## Introduction à SwiftUI

SwiftUI est le framework déclaratif d'Apple pour construire des interfaces utilisateur. Contrairement à UIKit (impératif), SwiftUI se base sur la description de **ce que** l'interface doit afficher plutôt que **comment** le faire.

### Principe fondamental : La vue est une fonction du state

```swift
Interface = f(State)
```

Quand le state change, SwiftUI recalcule automatiquement la vue.

### Votre première vue SwiftUI

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        Text("Bienvenue dans SwiftUI")
            .font(.title)
            .foregroundColor(.blue)
    }
}
```

---

## @State - Le state local

`@State` est utilisé pour gérer des **données locales et simples** dans une vue. C'est le property wrapper le plus basique.

### Caractéristiques

- ✅ Propriété **privée** à la vue
- ✅ Pour les types **valeur** (Int, String, Bool, struct)
- ✅ SwiftUI gère automatiquement le cycle de vie
- ✅ Déclenche un re-rendu quand la valeur change

### Exemple 1 : Compteur simple

```swift
struct CounterView: View {
    @State private var count = 0

    var body: some View {
        VStack(spacing: 20) {
            Text("Compteur : \(count)")
                .font(.largeTitle)

            HStack(spacing: 15) {
                Button("➖") {
                    count -= 1
                }

                Button("➕") {
                    count += 1
                }
            }
            .buttonStyle(.bordered)
        }
        .padding()
    }
}
```

### Exemple 2 : Toggle et TextField

```swift
struct FormView: View {
    @State private var username = ""
    @State private var isAgreed = false
    @State private var age = 18.0

    var body: some View {
        Form {
            Section("Informations") {
                TextField("Nom d'utilisateur", text: $username)

                Slider(value: $age, in: 0...100, step: 1) {
                    Text("Âge")
                }
                Text("Âge : \(Int(age)) ans")

                Toggle("J'accepte les conditions", isOn: $isAgreed)
            }

            Section {
                Text("Bonjour \(username), vous avez \(Int(age)) ans")
                    .opacity(username.isEmpty ? 0 : 1)
            }
        }
    }
}
```

### 💡 Astuce : Le dollar `$`

Le préfixe `$` crée un **binding** vers la variable. Quand vous passez `$username` à un TextField, vous lui donnez accès en lecture **et** écriture.

---

## @Binding - Liaison bidirectionnelle

`@Binding` permet à une vue **enfant** de modifier une variable détenue par sa vue **parente**.

### Caractéristiques

- ✅ Pas de propriété de la donnée
- ✅ Simple référence vers un @State parent
- ✅ Communication bidirectionnelle parent ↔️ enfant

### Exemple : Composant réutilisable

```swift
// Vue parente qui possède le state
struct ParentView: View {
    @State private var isOn = false
    @State private var volume = 50.0

    var body: some View {
        VStack(spacing: 30) {
            Text("État : \(isOn ? "Activé" : "Désactivé")")
                .font(.title2)

            // On passe un binding avec $
            ToggleButton(isOn: $isOn)

            Text("Volume : \(Int(volume))%")
            VolumeSlider(volume: $volume)
        }
        .padding()
    }
}

// Vue enfant qui reçoit un binding
struct ToggleButton: View {
    @Binding var isOn: Bool

    var body: some View {
        Button {
            isOn.toggle()
        } label: {
            Image(systemName: isOn ? "power.circle.fill" : "power.circle")
                .font(.system(size: 60))
                .foregroundColor(isOn ? .green : .gray)
        }
    }
}

struct VolumeSlider: View {
    @Binding var volume: Double

    var body: some View {
        VStack {
            Slider(value: $volume, in: 0...100)
                .tint(.orange)

            HStack {
                Button("Muet") { volume = 0 }
                Spacer()
                Button("Max") { volume = 100 }
            }
            .buttonStyle(.bordered)
        }
        .padding()
        .background(.ultraThinMaterial)
        .cornerRadius(12)
    }
}
```

---

## @StateObject et @ObservedObject

Pour gérer des **états complexes** et partagés, on utilise des classes conformes au protocole `ObservableObject`.

### @StateObject vs @ObservedObject

| Property Wrapper  | Utilisation          | Propriétaire                    |
| ----------------- | -------------------- | ------------------------------- |
| `@StateObject`    | Création de l'objet  | ✅ OUI - La vue possède l'objet |
| `@ObservedObject` | Réception de l'objet | ❌ NON - Reçu d'un parent       |

### Exemple : Gestionnaire de tâches

```swift
// 1. Modèle de données
struct Task: Identifiable {
    let id = UUID()
    var title: String
    var isCompleted: Bool
}

// 2. ViewModel - ObservableObject
class TaskViewModel: ObservableObject {
    // @Published déclenche les mises à jour de la vue
    @Published var tasks: [Task] = []

    func addTask(title: String) {
        let newTask = Task(title: title, isCompleted: false)
        tasks.append(newTask)
    }

    func toggleTask(_ task: Task) {
        if let index = tasks.firstIndex(where: { $0.id == task.id }) {
            tasks[index].isCompleted.toggle()
        }
    }

    func deleteTask(_ task: Task) {
        tasks.removeAll { $0.id == task.id }
    }
}

// 3. Vue principale avec @StateObject
struct TaskListView: View {
    @StateObject private var viewModel = TaskViewModel()
    @State private var newTaskTitle = ""

    var body: some View {
        NavigationStack {
            VStack {
                // Barre d'ajout
                HStack {
                    TextField("Nouvelle tâche", text: $newTaskTitle)
                        .textFieldStyle(.roundedBorder)

                    Button("Ajouter") {
                        viewModel.addTask(title: newTaskTitle)
                        newTaskTitle = ""
                    }
                    .buttonStyle(.borderedProminent)
                    .disabled(newTaskTitle.isEmpty)
                }
                .padding()

                // Liste des tâches
                List {
                    ForEach(viewModel.tasks) { task in
                        TaskRow(task: task, viewModel: viewModel)
                    }
                }
            }
            .navigationTitle("Mes tâches")
        }
    }
}

// 4. Vue enfant avec @ObservedObject
struct TaskRow: View {
    let task: Task
    @ObservedObject var viewModel: TaskViewModel

    var body: some View {
        HStack {
            Image(systemName: task.isCompleted ? "checkmark.circle.fill" : "circle")
                .foregroundColor(task.isCompleted ? .green : .gray)
                .onTapGesture {
                    viewModel.toggleTask(task)
                }

            Text(task.title)
                .strikethrough(task.isCompleted)

            Spacer()

            Button(role: .destructive) {
                viewModel.deleteTask(task)
            } label: {
                Image(systemName: "trash")
            }
        }
    }
}
```

### 🔑 Règle importante

- Utilisez `@StateObject` là où l'objet est **créé**
- Utilisez `@ObservedObject` quand l'objet est **passé** en paramètre

---

## @EnvironmentObject - State partagé

`@EnvironmentObject` permet de **partager un objet** dans toute la hiérarchie de vues sans le passer explicitement.

### Exemple : Système de thème global

```swift
// 1. Objet partagé
class ThemeManager: ObservableObject {
    @Published var isDarkMode = false
    @Published var accentColor: Color = .blue

    var backgroundColor: Color {
        isDarkMode ? .black : .white
    }

    var textColor: Color {
        isDarkMode ? .white : .black
    }
}

// 2. Vue racine - injection du @StateObject
struct RootView: View {
    @StateObject private var themeManager = ThemeManager()

    var body: some View {
        TabView {
            HomeView()
                .tabItem {
                    Label("Accueil", systemImage: "house")
                }

            SettingsView()
                .tabItem {
                    Label("Réglages", systemImage: "gear")
                }
        }
        // On injecte le ThemeManager dans l'environnement
        .environmentObject(themeManager)
    }
}

// 3. Vue enfant - réception via @EnvironmentObject
struct HomeView: View {
    @EnvironmentObject var themeManager: ThemeManager

    var body: some View {
        ZStack {
            themeManager.backgroundColor
                .ignoresSafeArea()

            VStack(spacing: 20) {
                Text("Bienvenue")
                    .font(.largeTitle)
                    .foregroundColor(themeManager.textColor)

                Text("Mode : \(themeManager.isDarkMode ? "Sombre" : "Clair")")
                    .foregroundColor(themeManager.accentColor)
            }
        }
    }
}

// 4. Vue de paramètres
struct SettingsView: View {
    @EnvironmentObject var themeManager: ThemeManager

    var body: some View {
        Form {
            Section("Apparence") {
                Toggle("Mode sombre", isOn: $themeManager.isDarkMode)

                ColorPicker("Couleur d'accent", selection: $themeManager.accentColor)
            }
        }
    }
}
```

### ⚠️ Attention

Si une vue utilise `@EnvironmentObject` mais que l'objet n'a pas été injecté, l'app **crash**. Pensez toujours à faire `.environmentObject(...)`.

---

## @AppStorage - Persistance simple

`@AppStorage` permet de sauvegarder automatiquement des valeurs simples dans `UserDefaults`.

### Exemple : Préférences utilisateur

```swift
struct UserPreferencesView: View {
    // Persisté automatiquement
    @AppStorage("username") private var username = ""
    @AppStorage("notificationsEnabled") private var notificationsEnabled = true
    @AppStorage("fontSize") private var fontSize = 16.0
    @AppStorage("lastLaunchDate") private var lastLaunchDate = Date()

    var body: some View {
        Form {
            Section("Profil") {
                TextField("Nom d'utilisateur", text: $username)
            }

            Section("Notifications") {
                Toggle("Activer les notifications", isOn: $notificationsEnabled)
            }

            Section("Affichage") {
                Slider(value: $fontSize, in: 12...24, step: 1) {
                    Text("Taille du texte")
                }
                Text("Aperçu : Bonjour !")
                    .font(.system(size: fontSize))
            }

            Section("Informations") {
                Text("Dernière visite : \(lastLaunchDate.formatted())")

                Button("Mettre à jour la date") {
                    lastLaunchDate = Date()
                }
            }
        }
        .navigationTitle("Préférences")
    }
}
```

### Types supportés

- `Bool`, `Int`, `Double`, `String`
- `URL`, `Data`
- `Date` (iOS 16+)

---

## Bonnes pratiques

### 1. Single Source of Truth (Une seule source de vérité)

```swift
// ❌ MAUVAIS - Duplication de données
struct BadView: View {
    @State private var username = "Alice"
    @State private var displayName = "Alice"  // Dupliqué !
}

// ✅ BON - Une seule source
struct GoodView: View {
    @State private var username = "Alice"

    var displayName: String {
        username.isEmpty ? "Anonyme" : username
    }
}
```

### 2. Hiérarchie des states

```
@AppStorage          → Persistance (UserDefaults)
@EnvironmentObject   → Global dans l'app
@StateObject         → Propriétaire du state complexe
@ObservedObject      → Observateur du state complexe
@Binding             → Liaison parent-enfant
@State               → State local simple
```

### 3. Performance : éviter les calculs dans body

```swift
// ❌ MAUVAIS
struct SlowView: View {
    @State private var items: [Item] = []

    var body: some View {
        List {
            // Calcul à chaque re-rendu !
            ForEach(items.filter { $0.isActive }.sorted()) { item in
                Text(item.name)
            }
        }
    }
}

// ✅ BON - Computed property
struct FastView: View {
    @State private var items: [Item] = []

    private var activeItems: [Item] {
        items.filter { $0.isActive }.sorted()
    }

    var body: some View {
        List(activeItems) { item in
            Text(item.name)
        }
    }
}
```

### 4. Décomposition en composants

```swift
// ✅ BON - Composants réutilisables et testables
struct ProductCard: View {
    let product: Product
    let onAddToCart: () -> Void

    var body: some View {
        VStack(alignment: .leading) {
            AsyncImage(url: product.imageURL)
            Text(product.name)
                .font(.headline)
            Text(product.price.formatted(.currency(code: "EUR")))
                .font(.subheadline)
            Button("Ajouter au panier", action: onAddToCart)
        }
    }
}
```

### 5. Preview avec différents states

```swift
#Preview("Mode clair") {
    TaskListView()
}

#Preview("Mode sombre") {
    TaskListView()
        .preferredColorScheme(.dark)
}

#Preview("Avec données") {
    let viewModel = TaskViewModel()
    viewModel.tasks = [
        Task(title: "Apprendre SwiftUI", isCompleted: true),
        Task(title: "Créer une app", isCompleted: false)
    ]
    return TaskListView()
}
```

---

## 📝 Résumé

| Property Wrapper     | Quand l'utiliser            | Exemple                     |
| -------------------- | --------------------------- | --------------------------- |
| `@State`             | Donnée locale simple        | Compteur, toggle            |
| `@Binding`           | Communication parent-enfant | Composant réutilisable      |
| `@StateObject`       | Créer un ViewModel          | Gestionnaire de données     |
| `@ObservedObject`    | Recevoir un ViewModel       | Vue enfant observant        |
| `@EnvironmentObject` | State global partagé        | Thème, utilisateur connecté |
| `@AppStorage`        | Sauvegarder des préférences | Settings, dark mode         |

### Flow de données typique

```
App (Root)
  @StateObject → UserSession
  .environmentObject(userSession)

  ↓

  MainView
    @EnvironmentObject var userSession
    @StateObject var viewModel

    ↓

    ChildView
      @ObservedObject var viewModel
      @State private var localState

      ↓

      GrandChildView
        @Binding var localState
```

---

## Navigation entre les vues

La navigation permet de passer d'un écran à un autre dans votre application. C'est comme un empilement de cartes : vous ajoutez une nouvelle carte (écran) sur la pile pour avancer, et vous enlevez la carte du dessus pour revenir en arrière.

### Les concepts de base

**NavigationStack** : C'est le conteneur principal qui gère la pile de navigation. Il doit englober toute votre hiérarchie de vues navigables.

**NavigationLink** : C'est le bouton/lien qui permet de naviguer vers une nouvelle vue.

**NavigationPath** : La pile qui contient l'historique de navigation (avancé).

### NavigationStack - Votre première navigation

`NavigationStack` remplace l'ancien `NavigationView` (iOS 16+). C'est le point de départ obligatoire pour toute navigation.

```swift
struct MainView: View {
    var body: some View {
        NavigationStack {
            VStack(spacing: 20) {
                Text("Écran principal")
                    .font(.largeTitle)

                NavigationLink("Aller à l'écran 2") {
                    SecondView()
                }
                .buttonStyle(.borderedProminent)
            }
            .navigationTitle("Accueil")
        }
    }
}

struct SecondView: View {
    var body: some View {
        VStack {
            Text("Écran 2")
                .font(.title)

            NavigationLink("Aller à l'écran 3") {
                ThirdView()
            }
        }
        .navigationTitle("Détails")
        .navigationBarTitleDisplayMode(.inline)
    }
}
```

### NavigationLink - Les différents types

Le `NavigationLink` est l'outil principal pour créer des liens de navigation. Il existe plusieurs façons de l'utiliser selon vos besoins.

#### 1. Navigation simple - La plus basique

La syntaxe la plus simple : un texte cliquable qui ouvre une nouvelle vue.

```swift
NavigationLink("Voir les détails") {
    DetailView()
}
```

**Quand l'utiliser** : Pour une navigation directe sans passer de données.

#### 2. Navigation avec des données - Le cas le plus fréquent

Dans la vraie vie, vous voulez souvent passer des informations à l'écran suivant (par exemple, afficher les détails d'un produit que l'utilisateur a cliqué).

```swift
struct Product: Identifiable {
    let id = UUID()
    let name: String
    let price: Double
}

struct ProductListView: View {
    let products = [
        Product(name: "iPhone", price: 999),
        Product(name: "MacBook", price: 1999),
        Product(name: "iPad", price: 599)
    ]

    var body: some View {
        NavigationStack {
            List(products) { product in
                NavigationLink(value: product) {
                    HStack {
                        Text(product.name)
                        Spacer()
                        Text("\(product.price, specifier: "%.2f")€")
                    }
                }
            }
            .navigationTitle("Produits")
            .navigationDestination(for: Product.self) { product in
                ProductDetailView(product: product)
            }
        }
    }
}

struct ProductDetailView: View {
    let product: Product

    var body: some View {
        VStack(spacing: 20) {
            Text(product.name)
                .font(.largeTitle)

            Text("\(product.price, specifier: "%.2f")€")
                .font(.title)
                .foregroundColor(.green)
        }
        .navigationTitle("Détail")
    }
}
```

### Navigation programmatique - Contrôler par code

Parfois, vous voulez déclencher une navigation **par code** plutôt que par un clic sur un lien. Par exemple :

- Naviguer après une validation de formulaire réussie
- Aller à plusieurs écrans d'un coup
- Revenir à l'écran d'accueil après une action

Pour cela, on utilise un `NavigationPath` qui représente la pile de navigation.

```swift
struct ProgrammaticNavigationView: View {
    @State private var path = NavigationPath()
    @State private var showDetails = false

    var body: some View {
        NavigationStack(path: $path) {
            VStack(spacing: 20) {
                Button("Aller à l'écran 1") {
                    path.append("Screen1")
                }

                Button("Aller directement à l'écran 3") {
                    path.append("Screen1")
                    path.append("Screen2")
                    path.append("Screen3")
                }

                Button("Retour à la racine") {
                    path.removeLast(path.count)
                }
                .disabled(path.isEmpty)

                Text("Profondeur: \(path.count)")
                    .foregroundColor(.secondary)
            }
            .navigationTitle("Navigation Pro")
            .navigationDestination(for: String.self) { screen in
                switch screen {
                case "Screen1":
                    Screen1View(path: $path)
                case "Screen2":
                    Screen2View(path: $path)
                case "Screen3":
                    Screen3View(path: $path)
                default:
                    Text("Écran inconnu")
                }
            }
        }
    }
}

struct Screen1View: View {
    @Binding var path: NavigationPath  // On reçoit le path en binding

    var body: some View {
        VStack(spacing: 20) {
            Text("Écran 1")
                .font(.largeTitle)

            Button("Suivant") {
                path.append("Screen2")  // On ajoute à la pile
            }
            .buttonStyle(.borderedProminent)
        }
        .navigationTitle("Écran 1")
    }
}

// Note : Les Screen2View et Screen3View suivent le même pattern
```

### Navigation avec Router - Architecture avancée

Dans une **vraie application**, naviguer en passant des strings (`"Screen1"`, `"Screen2"`) devient vite compliqué et sources d'erreurs. Le pattern **Router** centralise toute la logique de navigation dans une classe dédiée.

**Avantages :**

- ✅ Navigation type-safe (pas de risque de typo)
- ✅ Logique centralisée (facile à maintenir)
- ✅ Testable
- ✅ Permet de naviguer depuis n'importe quelle vue (via `@EnvironmentObject`)

**Le principe :** Créer un enum des destinations possibles, et un Router qui gère le NavigationPath.

```swift
https://pastebin.com/Nu0kX2B7
// 1. Définir les destinations possibles avec un enum
// Les 'associated values' permettent de passer des données
enum Destination: Hashable {
    case home
    case profile(User)        // Passe un objet User
    case settings
    case detail(id: String)   // Passe juste un ID
}

// 2. Router observable - La classe qui gère toute la navigation
class NavigationRouter: ObservableObject {
    @Published var path = NavigationPath()  // La pile de navigation

    // Ajouter une destination à la pile (avancer)
    func navigate(to destination: Destination) {
        path.append(destination)
    }

    // Retirer la dernière destination (reculer d'un écran)
    func navigateBack() {
        guard !path.isEmpty else { return }
        path.removeLast()
    }

    // Vider toute la pile (retour à l'écran principal)
    func navigateToRoot() {
        path.removeLast(path.count)
    }

    // Remplacer la destination actuelle par une nouvelle
    func replace(with destination: Destination) {
        path.removeLast()
        path.append(destination)
    }
}

// 3. Vue racine avec le router
struct AppRootView: View {
    @StateObject private var router = NavigationRouter()  // Créer le router UNE fois

    var body: some View {
        NavigationStack(path: $router.path) {  // Lier la pile au router
            HomeScreenView()  // Écran de départ
                .navigationDestination(for: Destination.self) { destination in
                    // Ce switch décide quelle vue afficher selon la destination
                    switch destination {
                    case .home:
                        HomeScreenView()
                    case .profile(let user):  // Récupère le User de l'enum
                        ProfileView(user: user)
                    case .settings:
                        SettingsView()
                    case .detail(let id):  // Récupère l'ID de l'enum
                        DetailView(id: id)
                    }
                }
        }
        .environmentObject(router)  // ⚠️ IMPORTANT : Injecter dans l'environnement
    }
}

// 4. Utilisation dans les vues enfants
struct HomeScreenView: View {
    @EnvironmentObject var router: NavigationRouter  // Récupère le router de l'environnement

    var body: some View {
        VStack(spacing: 20) {
            Text("Accueil")
                .font(.largeTitle)

            Button("Mon profil") {
                let user = User(name: "Alice", email: "alice@example.com")
                router.navigate(to: .profile(user))  // Navigation type-safe
            }

            Button("Paramètres") {
                router.navigate(to: .settings)
            }
        }
        .navigationTitle("Accueil")
    }
}

// Note : Aucun NavigationLink nécessaire ! Tout passe par le router.

struct ProfileView: View {
    let user: User  // Reçu via la navigation
    @EnvironmentObject var router: NavigationRouter

    var body: some View {
        VStack(spacing: 20) {
            Text(user.name)
                .font(.largeTitle)

            Text(user.email)
                .foregroundColor(.secondary)

            Button("Voir les détails") {
                router.navigate(to: .detail(id: "123"))  // Empile un nouvel écran
            }

            Button("Retour à l'accueil") {
                router.navigateToRoot()  // Vide toute la pile d'un coup
            }
            .buttonStyle(.bordered)
        }
        .navigationTitle("Profil")
    }
}

// 💡 Le router est accessible partout sans le passer manuellement !

struct User: Hashable {
    let id = UUID()
    let name: String
    let email: String
}

```

---

## Afficher du HTML dans SwiftUI

SwiftUI n'a pas de composant natif pour afficher du HTML riche (comme des pages d'aide, des notices, des articles formatés, etc.). Pour cela, on utilise **WKWebView**, qui est le moteur de rendu web d'Apple.

### Cas d'usage : Le projet TPCombat

Dans le projet TPCombat, on utilise du HTML pour afficher :

- 📖 **Une notice d'utilisation** (`notice.html`) - Explique les règles du jeu
- ❓ **Une page d'aide** (`help.html`) - Guide détaillé avec stratégies

**Pourquoi HTML plutôt que du SwiftUI natif ?**

- ✅ Contenu riche avec titres, listes, images, couleurs personnalisées
- ✅ Facile à mettre à jour sans recompiler l'app
- ✅ Peut être chargé depuis un serveur ou stocké localement
- ✅ Support complet du CSS pour un design pro

---

### Étape 1 : Créer le wrapper WKWebView

`WKWebView` est un composant UIKit, il faut donc le "wrapper" pour l'utiliser dans SwiftUI avec `UIViewRepresentable`.

**Voici le code du TPCombat expliqué ligne par ligne :**

```swift
import SwiftUI
import WebKit

struct WebView: UIViewRepresentable {
    let htmlFileName: String  // Sans l'extension .html
    
    func makeUIView(context: Context) -> WKWebView {
        WKWebView()
    }
    
    func updateUIView(_ webView: WKWebView, context: Context) {
        // Charger le fichier HTML du projet
        if let path = Bundle.main.path(forResource: htmlFileName, ofType: "html"),
           let htmlString = try? String(contentsOfFile: path, encoding: .utf8) {
            webView.loadHTMLString(htmlString, baseURL: nil)
        }
    }
}

// ✅ Utilisation
struct NoticeView: View {
    var body: some View {
        WebView(htmlFileName: "notice")  // Charge notice.html
            .navigationTitle("Notice")
    }
}
```

---

### 🔑 Bonnes pratiques à retenir

#### 1. Structure de fichiers recommandée

```

MonApp/
├── Views/
│ ├── NoticeView.swift
│ └── HelpView.swift
├── Resources/
│ ├── notice.html
│ └── help.html
└── Components/
└── WebView.swift (votre wrapper réutilisable)

```
