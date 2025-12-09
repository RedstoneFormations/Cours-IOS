---
marp: true
theme: default
paginate: true
backgroundColor: #fff
---

# Formation iOS - Jour 1

## Environnement Xcode & Bases Swift

**Objectifs de la journée**

- Maîtriser Xcode
- Apprendre les fondamentaux Swift
- Comprendre l'architecture iOS
- Créer une app avec WebView

---

## Programme de la journée

1. **Installation et configuration Xcode** (30 min)
2. **Bases du langage Swift** (1h30)
3. **Structure d'une app iOS** (30 min)
4. **TP : Projet avec HTML local** (2h)
5. **Récapitulatif et exercices** (30 min)

---

# 1. Environnement Xcode

---

## Installation de Xcode

**Téléchargement**

- App Store → Rechercher "Xcode"
- Taille : ~12-15 GB
- Temps d'installation : 30-60 min

**Première configuration**

- Lancer Xcode
- Accepter la licence
- Installer les composants additionnels
- Installer les simulateurs iOS

---

## Interface Xcode - Vue d'ensemble

```
┌────────────────────────────────────────────────────────┐
│  Toolbar (Build, Run, Stop)                            │
├──────────┬─────────────────────────┬────────────────────┤
│          │                         │                    │
│ Navigator│      Editor Area        │    Inspector       │
│ (Fichiers│   (Code, Interface)     │   (Propriétés)     │
│  Assets) │                         │                    │
│          │                         │                    │
├──────────┴─────────────────────────┴────────────────────┤
│  Debug Area (Console, Variables)                        │
└────────────────────────────────────────────────────────┘
```

---

## Navigator (Explorateur de projet)

**7 navigateurs disponibles** :

1. **Project** - Fichiers et dossiers
2. **Source Control** - Git
3. **Symbol** - Classes, fonctions
4. **Find** - Recherche dans le projet
5. **Issue** - Erreurs et warnings
6. **Test** - Tests unitaires
7. **Debug** - État de débogage

**Raccourci** : `Cmd + 1` à `Cmd + 7`

---

## Editor Area

**Modes d'édition** :

- **Code** - Éditeur de texte
- **Interface Builder** - Édition visuelle (Storyboard)
- **SwiftUI Preview** - Aperçu en temps réel

**Fonctionnalités** :

- Autocomplétion intelligente
- Jump to definition (`Cmd + Click`)
- Refactoring
- Snippets de code

---

## Debug Area

**Console**

- Affichage des `print()` et logs
- Messages d'erreur du runtime
- Commandes LLDB

**Variables**

- Inspection des variables en temps réel
- Modification des valeurs pendant le debug
- Watchpoints

**Raccourci** : `Cmd + Shift + Y`

---

## Inspector

**Types d'inspecteurs** :

1. **File** - Propriétés du fichier
2. **Quick Help** - Documentation
3. **Identity** - Classe, Module
4. **Attributes** - Propriétés UI
5. **Size** - Contraintes, taille
6. **Connections** - IBOutlets, IBActions

**Raccourci** : `Cmd + Option + 1` à `Cmd + Option + 6`

---

## Création d'un nouveau projet

**Étapes** :

1. File → New → Project (`Cmd + Shift + N`)
2. Choisir le template : **App**
3. Configurer le projet :
   - Product Name
   - Team (compte développeur)
   - Organization Identifier
   - Bundle Identifier (auto-généré)
   - Interface : SwiftUI ou Storyboard
   - Language : Swift

---

## Organisation du projet

**Structure recommandée** :

```
MonProjet/
├── App/
│   ├── AppDelegate.swift
│   └── Info.plist
├── Models/
│   └── User.swift
├── Views/
│   └── HomeView.swift
├── ViewModels/
│   └── HomeViewModel.swift
├── Managers/
│   └── NetworkManager.swift
├── Resources/
│   ├── Assets.xcassets
│   └── Localizable.strings
└── Utils/
    └── Extensions.swift
```

---

## Assets & Ressources

**Assets.xcassets**

- Images (@1x, @2x, @3x)
- Icône de l'app
- Couleurs du thème
- Fichiers JSON de données

**Autres ressources**

- HTML, CSS, JS
- Fichiers audio/vidéo
- Polices personnalisées
- Fichiers de configuration

---

## Info.plist

**Fichier de configuration principal**

- Bundle ID, version, build
- Permissions (caméra, localisation, etc.)
- URL schemes
- Configurations de sécurité
- Orientations supportées

**Important** : Configurer les permissions requises avec descriptions claires

---

# 2. Bases du langage Swift

---

## Pourquoi Swift ?

**Avantages** :

- ✅ Langage moderne et sûr
- ✅ Syntaxe claire et concise
- ✅ Performance native
- ✅ Typage fort avec inférence
- ✅ Gestion automatique de la mémoire (ARC)
- ✅ Interopérabilité avec Objective-C

**Open Source** : swift.org

---

## Types primitifs

```swift
// Entiers
let age: Int = 25
let petitNombre: Int8 = 127
let grandNombre: Int64 = 9223372036854775807

// Décimaux
let prix: Double = 19.99  // 64 bits (précis)
let temperature: Float = 23.5  // 32 bits

// Booléens
let estActif: Bool = true
let estConnecte: Bool = false

// Caractères et chaînes
let lettre: Character = "A"
let message: String = "Bonjour Swift !"
```

---

## Inférence de type

```swift
// Swift devine le type automatiquement
let nombre = 42              // Int
let decimal = 3.14           // Double
let texte = "Hello"          // String
let vrai = true              // Bool

// Mais on peut être explicite
let nombreExplicite: Int = 42
let decimalExplicite: Double = 3.14

// Conversion de type
let entier = 10
let flottant = Double(entier)  // Conversion explicite
```

---

## Collections - Tableaux

```swift
// Création
var fruits: [String] = ["Pomme", "Banane", "Orange"]
var nombres = [1, 2, 3, 4, 5]  // Inférence de type [Int]
var vide: [Int] = []

// Opérations
fruits.append("Fraise")         // Ajouter
fruits.insert("Kiwi", at: 0)    // Insérer à une position
fruits.remove(at: 1)            // Supprimer
let premier = fruits[0]         // Accès par index
let compte = fruits.count       // Nombre d'éléments

// Itération
for fruit in fruits {
    print(fruit)
}
```

---

## Collections - Dictionnaires

```swift
// Création
var ages: [String: Int] = [
    "Alice": 25,
    "Bob": 30,
    "Charlie": 28
]

// Opérations
ages["Diana"] = 27              // Ajouter/Modifier
let ageAlice = ages["Alice"]    // Optional Int?
ages.removeValue(forKey: "Bob") // Supprimer

// Itération
for (nom, age) in ages {
    print("\(nom) a \(age) ans")
}
```

---

## Collections - Sets

```swift
// Création (pas de doublons, pas d'ordre)
var couleurs: Set<String> = ["Rouge", "Vert", "Bleu"]
var nombres: Set = [1, 2, 3, 2, 1]  // Devient {1, 2, 3}

// Opérations
couleurs.insert("Jaune")
couleurs.remove("Rouge")
let contient = couleurs.contains("Vert")  // true

// Opérations d'ensembles
let set1: Set = [1, 2, 3]
let set2: Set = [3, 4, 5]
let union = set1.union(set2)           // {1, 2, 3, 4, 5}
let intersection = set1.intersection(set2)  // {3}
```

---

## Optionnels

```swift
// Type optionnel : peut contenir une valeur ou nil
var nom: String? = "Alice"
var age: Int? = nil

// Déballage sécurisé (if let)
if let nomValide = nom {
    print("Bonjour \(nomValide)")
} else {
    print("Pas de nom")
}

// Guard let (sortie anticipée)
func saluer(nom: String?) {
    guard let nomValide = nom else {
        print("Nom invalide")
        return
    }
    print("Bonjour \(nomValide)")
}
```

---

## Optionnels (suite)

```swift
// Opérateur nil coalescing (??)
let nom: String? = nil
let nomDefaut = nom ?? "Anonyme"  // "Anonyme"

// Optional chaining
class Personne {
    var adresse: Adresse?
}
class Adresse {
    var ville: String = ""
}

let personne = Personne()
let ville = personne.adresse?.ville  // nil (pas de crash)

// Force unwrap (⚠️ Dangereux si nil)
let nom: String? = "Alice"
let nomForce = nom!  // OK mais crash si nil
```

---

## Constantes vs Variables

```swift
// let = CONSTANTE (immutable, recommandé par défaut)
let pi = 3.14159
let appName = "MonApp"
// pi = 3.14  ❌ Erreur de compilation

// var = VARIABLE (mutable, si nécessaire)
var compteur = 0
var message = "Bonjour"
compteur += 1  // ✅ OK
message = "Au revoir"  // ✅ OK

// Règle d'or : Toujours utiliser 'let' sauf si modification nécessaire
```

---

## Fonctions - Syntaxe de base

```swift
// Fonction simple
func direBonjour() {
    print("Bonjour !")
}

// Avec paramètres
func saluer(nom: String) {
    print("Bonjour \(nom)")
}

// Avec valeur de retour
func additionner(a: Int, b: Int) -> Int {
    return a + b
}

// Appel
direBonjour()
saluer(nom: "Alice")
let resultat = additionner(a: 5, b: 3)  // 8
```

---

## Fonctions - Paramètres

```swift
// Labels externes et internes
func calculer(de debut: Int, a fin: Int) -> Int {
    return fin - debut
}
calculer(de: 5, a: 10)  // de/a = labels externes

// Omission du label externe avec _
func afficher(_ message: String) {
    print(message)
}
afficher("Hello")  // Pas besoin du label

// Valeurs par défaut
func saluer(nom: String = "Invité") {
    print("Bonjour \(nom)")
}
saluer()           // "Bonjour Invité"
saluer(nom: "Bob") // "Bonjour Bob"
```

---

## Fonctions - Types avancés

```swift
// Paramètres variadiques
func moyenne(_ nombres: Double...) -> Double {
    let somme = nombres.reduce(0, +)
    return somme / Double(nombres.count)
}
moyenne(1, 2, 3, 4, 5)  // 3.0

// Paramètres inout (modifiables)
func doubler(_ nombre: inout Int) {
    nombre *= 2
}
var valeur = 5
doubler(&valeur)  // valeur = 10

// Fonctions comme paramètres
func appliquer(_ operation: (Int, Int) -> Int, a: Int, b: Int) -> Int {
    return operation(a, b)
}
```

---

## Closures

```swift
// Syntaxe complète
let addition = { (a: Int, b: Int) -> Int in
    return a + b
}

// Syntaxe raccourcie
let multiplication = { (a: Int, b: Int) in a * b }

// Utilisation
let resultat = addition(5, 3)  // 8

// Closures avec tableaux
let nombres = [1, 2, 3, 4, 5]
let doubles = nombres.map { $0 * 2 }        // [2, 4, 6, 8, 10]
let pairs = nombres.filter { $0 % 2 == 0 }  // [2, 4]
let somme = nombres.reduce(0, +)            // 15
```

---

## Structures de contrôle - If/Else

```swift
let temperature = 25

if temperature < 0 {
    print("Il gèle")
} else if temperature < 20 {
    print("Il fait frais")
} else if temperature < 30 {
    print("Il fait bon")
} else {
    print("Il fait chaud")
}

// If avec let (déballage optionnel)
let nom: String? = "Alice"
if let nomValide = nom, nomValide.count > 0 {
    print("Bonjour \(nomValide)")
}
```

---

## Structures de contrôle - Switch

```swift
let jour = 3

switch jour {
case 1:
    print("Lundi")
case 2:
    print("Mardi")
case 3...5:
    print("Milieu de semaine")
case 6, 7:
    print("Week-end")
default:
    print("Jour invalide")
}

// Pas besoin de break (pas de fall-through par défaut)
// Pour fall-through explicite : utiliser 'fallthrough'
```

---

## Switch - Pattern Matching

```swift
// Switch avec tuples
let point = (0, 0)
switch point {
case (0, 0):
    print("Origine")
case (_, 0):
    print("Sur l'axe X")
case (0, _):
    print("Sur l'axe Y")
case (-2...2, -2...2):
    print("Dans le carré")
default:
    print("Ailleurs")
}

// Switch avec types
let valeur: Any = 42
switch valeur {
case let nombre as Int:
    print("Entier : \(nombre)")
case let texte as String:
    print("Texte : \(texte)")
default:
    print("Autre type")
}
```

---

## Boucles - For-In

```swift
// Boucle sur un range
for i in 1...5 {
    print(i)  // 1, 2, 3, 4, 5
}

for i in 1..<5 {
    print(i)  // 1, 2, 3, 4
}

// Boucle sur une collection
let fruits = ["Pomme", "Banane", "Orange"]
for fruit in fruits {
    print(fruit)
}

// Avec index
for (index, fruit) in fruits.enumerated() {
    print("\(index): \(fruit)")
}
```

---

## Boucles - While

```swift
// While classique
var compteur = 0
while compteur < 5 {
    print(compteur)
    compteur += 1
}

// Repeat-while (do-while)
var nombre = 0
repeat {
    print(nombre)
    nombre += 1
} while nombre < 5

// Continue et break
for i in 1...10 {
    if i % 2 == 0 { continue }  // Sauter les pairs
    if i > 7 { break }          // Arrêter à 7
    print(i)  // 1, 3, 5, 7
}
```

---

## Structures (Struct)

```swift
struct Point {
    var x: Int
    var y: Int

    // Méthode
    func distance() -> Double {
        return sqrt(Double(x * x + y * y))
    }

    // Méthode mutating (modifie la struct)
    mutating func deplacer(dx: Int, dy: Int) {
        x += dx
        y += dy
    }
}

// Utilisation
var point = Point(x: 3, y: 4)
print(point.distance())  // 5.0
point.deplacer(dx: 1, dy: 1)
print(point.x)  // 4
```

---

## Struct - Caractéristiques

**Type valeur** (copié lors de l'affectation)

```swift
var point1 = Point(x: 1, y: 2)
var point2 = point1  // Copie complète
point2.x = 10
print(point1.x)  // 1 (inchangé)
print(point2.x)  // 10
```

**Avantages** :

- Sécurité (pas de références partagées)
- Performance (optimisation par le compilateur)
- Thread-safe par défaut
- Initialisation automatique

**Utilisation** : Données simples, modèles, value types

---

## Classes

```swift
class Personne {
    var nom: String
    var age: Int

    // Initializer obligatoire
    init(nom: String, age: Int) {
        self.nom = nom
        self.age = age
    }

    // Méthode
    func sePresenter() {
        print("Je m'appelle \(nom) et j'ai \(age) ans")
    }

    // Deinitializer (nettoyage)
    deinit {
        print("\(nom) est supprimé")
    }
}
```

---

## Classes - Caractéristiques

**Type référence** (partagé lors de l'affectation)

```swift
let personne1 = Personne(nom: "Alice", age: 25)
let personne2 = personne1  // Même référence
personne2.age = 30
print(personne1.age)  // 30 (modifié aussi)
```

**Fonctionnalités** :

- Héritage
- Type casting
- Deinitializers
- Référence counting (ARC)

**Utilisation** : Objets complexes, identité, héritage nécessaire

---

## Struct vs Class - Quand utiliser ?

**Utiliser Struct quand** :

- Données simples et petites
- Pas besoin d'héritage
- Comportement valeur souhaité
- Thread-safety importante

**Utiliser Class quand** :

- Besoin d'héritage
- Identité de l'objet importante
- Partage de références nécessaire
- Interopérabilité Objective-C

**Règle Swift** : Préférer struct par défaut, classe si nécessaire

---

## Propriétés calculées

```swift
struct Rectangle {
    var largeur: Double
    var hauteur: Double

    // Propriété calculée (read-only)
    var aire: Double {
        return largeur * hauteur
    }

    // Propriété calculée (get/set)
    var perimetre: Double {
        get {
            return 2 * (largeur + hauteur)
        }
        set {
            let moitie = newValue / 4
            largeur = moitie
            hauteur = moitie
        }
    }
}
```

---

## Property Observers

```swift
struct Compteur {
    var valeur: Int = 0 {
        willSet {
            print("Va changer de \(valeur) à \(newValue)")
        }
        didSet {
            print("A changé de \(oldValue) à \(valeur)")
            if valeur > 100 {
                valeur = 100  // Limite
            }
        }
    }
}

var compteur = Compteur()
compteur.valeur = 50
// Va changer de 0 à 50
// A changé de 0 à 50
```

---

## Enumerations

```swift
enum Direction {
    case nord
    case sud
    case est
    case ouest
}

// Syntaxe courte
enum Resultat {
    case succes, erreur, enAttente
}

// Avec valeurs associées
enum CodeBarre {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}

// Avec raw values
enum Planete: Int {
    case mercure = 1, venus, terre, mars
}
```

---

## Enumerations - Switch

```swift
let direction = Direction.nord

switch direction {
case .nord:
    print("Vers le nord")
case .sud:
    print("Vers le sud")
case .est:
    print("Vers l'est")
case .ouest:
    print("Vers l'ouest")
}

// Avec valeurs associées
let code = CodeBarre.qrCode("ABCDEFG")
switch code {
case .upc(let a, let b, let c, let d):
    print("UPC: \(a) \(b) \(c) \(d)")
case .qrCode(let contenu):
    print("QR Code: \(contenu)")
}
```

---

# 3. Structure d'une app iOS

---

## Architecture MVC

```
┌──────────────────────────────────────┐
│           VIEW                       │
│  (Interface utilisateur)             │
│  SwiftUI View / UIViewController     │
└───────────┬──────────────────────────┘
            │ ↕ User Actions / Updates
┌───────────┴──────────────────────────┐
│        CONTROLLER                    │
│  (Logique de présentation)           │
│  ViewModel / ViewController           │
└───────────┬──────────────────────────┘
            │ ↕ Data / Business Logic
┌───────────┴──────────────────────────┐
│          MODEL                       │
│  (Données et logique métier)         │
│  Struct / Class / Manager            │
└──────────────────────────────────────┘
```

---

## Architecture MVVM (Recommandée avec SwiftUI)

```
┌──────────────────────────────────────┐
│           VIEW                       │
│     (Interface SwiftUI)              │
│  @State, @Binding, @StateObject      │
└───────────┬──────────────────────────┘
            │ ↕ Binding / ObservableObject
┌───────────┴──────────────────────────┐
│        VIEW MODEL                    │
│  (Logique de présentation)           │
│  @Published, ObservableObject        │
└───────────┬──────────────────────────┘
            │ ↕ Data Operations
┌───────────┴──────────────────────────┐
│          MODEL                       │
│  (Données pures)                     │
│  Struct / Codable                    │
└──────────────────────────────────────┘
```

---

## Cycle de vie - Application

```swift
@main
struct MonApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

// Événements principaux:
// 1. Launch          - App démarre
// 2. Active          - App au premier plan
// 3. Inactive        - App temporairement inactive
// 4. Background      - App en arrière-plan
// 5. Suspended       - App suspendue (peut être terminée)
// 6. Terminated      - App fermée
```

---

## Cycle de vie - View (SwiftUI)

```swift
struct MaView: View {
    var body: some View {
        Text("Hello")
            .onAppear {
                // Vue apparaît à l'écran
                print("View did appear")
            }
            .onDisappear {
                // Vue disparaît de l'écran
                print("View did disappear")
            }
            .task {
                // Tâche async lancée à l'apparition
                await chargerDonnees()
            }
    }
}
```

---

## Navigation - SwiftUI

```swift
// Navigation avec NavigationStack (iOS 16+)
struct ContentView: View {
    var body: some View {
        NavigationStack {
            List {
                NavigationLink("Page 1") {
                    DetailView(titre: "Page 1")
                }
                NavigationLink("Page 2") {
                    DetailView(titre: "Page 2")
                }
            }
            .navigationTitle("Menu")
        }
    }
}

struct DetailView: View {
    let titre: String
    var body: some View {
        Text(titre)
            .navigationTitle(titre)
    }
}
```

---

## Navigation - Présentation modale

```swift
struct ContentView: View {
    @State private var afficherModal = false

    var body: some View {
        Button("Afficher Modal") {
            afficherModal = true
        }
        .sheet(isPresented: $afficherModal) {
            ModalView()
        }
    }
}

struct ModalView: View {
    @Environment(\.dismiss) var dismiss

    var body: some View {
        VStack {
            Text("Vue Modale")
            Button("Fermer") {
                dismiss()
            }
        }
    }
}
```

---

## Composants SwiftUI essentiels

**Layout** :

- `VStack`, `HStack`, `ZStack` - Piles verticales/horizontales/superposées
- `List` - Liste scrollable
- `ScrollView` - Zone défilante
- `Grid` - Grille

**Controls** :

- `Button`, `Toggle`, `Slider`, `Stepper`
- `TextField`, `TextEditor`
- `Picker`, `DatePicker`

**Display** :

- `Text`, `Image`, `Label`
- `ProgressView`, `Gauge`

---

## SwiftUI vs UIKit

| SwiftUI               | UIKit             |
| --------------------- | ----------------- |
| Déclaratif            | Impératif         |
| Moderne (2019+)       | Legacy (2008+)    |
| State-driven          | Delegate-driven   |
| Moins de code         | Plus de contrôle  |
| Preview en temps réel | Simulateur requis |
| iOS 13+               | iOS 2+            |

**Recommandation** : SwiftUI pour les nouvelles apps, UIKit pour compatibilité ou contrôle fin

---

# 4. TP Pratique

## Application avec WebView

---

## Objectif du TP

**Créer une app iOS qui** :

1. Affiche un menu principal
2. Navigue vers une vue WebView
3. Charge une page HTML locale
4. Affiche le contenu HTML stylisé

**Durée** : 2 heures

**Technologies** :

- SwiftUI pour l'interface
- WKWebView pour afficher HTML
- Bundle pour charger les ressources

---

## Étape 1 : Créer le projet

1. Ouvrir Xcode
2. File → New → Project
3. Choisir **App** (iOS)
4. Configuration :
   - Product Name : `WebViewApp`
   - Team : Votre compte
   - Organization Identifier : `com.votreorg`
   - Interface : **SwiftUI**
   - Language : **Swift**
5. Choisir l'emplacement et créer

---

## Étape 2 : Créer le fichier HTML

1. File → New → File
2. Choisir **Empty** file
3. Nommer : `index.html`
4. **Important** : Cocher la Target Membership

```html
<!DOCTYPE html>
<html lang="fr">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>Ma première page</title>
		<style>
			/* Styles dans la slide suivante */
		</style>
	</head>
	<body>
		<!-- Contenu dans la slide suivante -->
	</body>
</html>
```

---

## Étape 2 : HTML - Styles

```css
body {
	font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
	padding: 20px;
	background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
	color: white;
	margin: 0;
}

h1 {
	text-align: center;
	font-size: 2em;
	margin-bottom: 30px;
}

.card {
	background: rgba(255, 255, 255, 0.1);
	padding: 20px;
	border-radius: 10px;
	margin: 20px 0;
	backdrop-filter: blur(10px);
}
```

---

## Étape 2 : HTML - Contenu

```html
<body>
	<h1>🎉 Bienvenue dans ma première app iOS</h1>

	<div class="card">
		<h2>📱 Jour 1 - Formation iOS</h2>
		<p>Cette page HTML est chargée localement depuis l'application.</p>
		<p>Nous utilisons WKWebView pour afficher du contenu web dans iOS.</p>
	</div>

	<div class="card">
		<h2>✨ Fonctionnalités</h2>
		<ul>
			<li>Chargement HTML local</li>
			<li>CSS moderne avec gradients</li>
			<li>Responsive design</li>
			<li>Interface native iOS</li>
		</ul>
	</div>
</body>
```

---

## Étape 3 : Créer HTMLView (SwiftUI Wrapper)

Créer un nouveau fichier Swift : `HTMLView.swift`

```swift
import SwiftUI
import WebKit

struct HTMLView: UIViewRepresentable {
    let htmlFileName: String

    func makeUIView(context: Context) -> WKWebView {
        let webView = WKWebView()
        webView.backgroundColor = .clear
        webView.isOpaque = false
        return webView
    }

    func updateUIView(_ webView: WKWebView, context: Context) {
        loadHTML(in: webView)
    }

    // Suite sur la prochaine slide...
}
```

---

## Étape 3 : HTMLView - Chargement HTML

```swift
struct HTMLView: UIViewRepresentable {
    // ... code précédent

    private func loadHTML(in webView: WKWebView) {
        guard let htmlPath = Bundle.main.path(
            forResource: htmlFileName,
            ofType: "html"
        ) else {
            print("❌ Fichier HTML non trouvé")
            return
        }

        do {
            let htmlString = try String(
                contentsOfFile: htmlPath,
                encoding: .utf8
            )
            let baseURL = Bundle.main.resourceURL
            webView.loadHTMLString(htmlString, baseURL: baseURL)
        } catch {
            print("❌ Erreur de lecture : \(error)")
        }
    }
}
```

---

## Étape 4 : Créer ContentView

```swift
import SwiftUI

struct ContentView: View {
    @State private var showWebView = false

    var body: some View {
        NavigationStack {
            VStack(spacing: 30) {
                Image(systemName: "globe")
                    .font(.system(size: 80))
                    .foregroundStyle(.blue)

                Text("WebView App")
                    .font(.largeTitle)
                    .fontWeight(.bold)

                Text("Appuyez sur le bouton pour afficher la page HTML locale")
                    .multilineTextAlignment(.center)
                    .foregroundStyle(.secondary)
                    .padding(.horizontal)

                // Suite sur la prochaine slide...
            }
        }
    }
}
```

---

## Étape 4 : ContentView - Navigation

```swift
struct ContentView: View {
    // ... code précédent

    var body: some View {
        NavigationStack {
            VStack(spacing: 30) {
                // ... contenu précédent

                Button {
                    showWebView = true
                } label: {
                    Label("Ouvrir la page HTML", systemImage: "doc.text.fill")
                        .font(.headline)
                        .foregroundStyle(.white)
                        .padding()
                        .frame(maxWidth: .infinity)
                        .background(.blue)
                        .cornerRadius(10)
                }
                .padding(.horizontal)
            }
            .navigationTitle("Menu Principal")
            .sheet(isPresented: $showWebView) {
                WebViewModal()
            }
        }
    }
}
```

---

## Étape 5 : Créer WebViewModal

```swift
struct WebViewModal: View {
    @Environment(\.dismiss) var dismiss

    var body: some View {
        NavigationStack {
            HTMLView(htmlFileName: "index")
                .navigationTitle("Page HTML")
                .navigationBarTitleDisplayMode(.inline)
                .toolbar {
                    ToolbarItem(placement: .topBarTrailing) {
                        Button("Fermer") {
                            dismiss()
                        }
                    }
                }
        }
    }
}
```

---

## Étape 6 : Tester l'application

**Sur simulateur** :

1. Choisir un simulateur (iPhone 15 Pro)
2. Cliquer sur le bouton Run (Cmd + R)
3. Attendre le build et le lancement
4. Tester la navigation

**Vérifications** :

- ✅ Le menu s'affiche correctement
- ✅ Le bouton ouvre la modale
- ✅ La page HTML s'affiche avec les styles
- ✅ Le bouton "Fermer" fonctionne

---

## Résolution de problèmes courants

**HTML ne se charge pas** :

- Vérifier la Target Membership du fichier HTML
- Vérifier le nom du fichier (sans extension dans le code)
- Regarder la console pour les erreurs

**Styles CSS ne s'appliquent pas** :

- Vérifier la syntaxe CSS
- Tester le HTML dans un navigateur d'abord

**Crash au lancement** :

- Vérifier les imports (WebKit)
- Vérifier les optionnels

---

## Extensions possibles (Bonus)

**1. Navigation multiple** :

```swift
// Ajouter plusieurs pages HTML
NavigationLink("Page 2") {
    HTMLView(htmlFileName: "page2")
}
```

**2. Indicateur de chargement** :

```swift
@State private var isLoading = true

ProgressView()
    .opacity(isLoading ? 1 : 0)
```

**3. Communication JavaScript ↔ Swift** :

```swift
// Exécuter du JavaScript depuis Swift
webView.evaluateJavaScript("document.title") { result, error in
    // ...
}
```

---

## Extensions possibles (Bonus suite)

**4. Barre de navigation personnalisée** :

```swift
.toolbar {
    ToolbarItem(placement: .topBarLeading) {
        Button(action: goBack) {
            Image(systemName: "chevron.left")
        }
    }
    ToolbarItem(placement: .topBarTrailing) {
        Button(action: reload) {
            Image(systemName: "arrow.clockwise")
        }
    }
}
```

**5. Gestion de l'orientation** :

```swift
.supportedOrientations(.all)
```

---

# 5. Récapitulatif

---

## Ce que nous avons appris

**Xcode** :

- ✅ Installation et configuration
- ✅ Interface et navigation
- ✅ Création et gestion de projet

**Swift** :

- ✅ Types de données et collections
- ✅ Fonctions et closures
- ✅ Structures de contrôle
- ✅ Structures et classes

**iOS** :

- ✅ Architecture app (MVC/MVVM)
- ✅ SwiftUI basics
- ✅ Navigation et cycle de vie
- ✅ Intégration WebView

---

## Bonnes pratiques à retenir

**Code** :

- Utiliser `let` par défaut, `var` si nécessaire
- Nommer les variables de manière explicite
- Gérer les optionnels avec sécurité
- Commenter le code complexe

**Organisation** :

- Structure de dossiers logique
- Séparation des responsabilités (MVVM)
- Un fichier = une responsabilité

**Performance** :

- Préférer struct à class quand possible
- Éviter les force unwrap (!)
- Utiliser lazy loading si nécessaire

---

## Ressources pour aller plus loin

**Documentation** :

- [Swift.org](https://swift.org/documentation/) - Doc officielle Swift
- [Apple Developer](https://developer.apple.com/documentation/) - Doc iOS
- [SwiftUI Tutorials](https://developer.apple.com/tutorials/swiftui) - Tutoriels interactifs

**Communauté** :

- [Swift Forums](https://forums.swift.org)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/swift)
- [r/swift](https://reddit.com/r/swift)

**Vidéos** :

- WWDC Sessions (Apple)
- Hacking with Swift (Paul Hudson)
- SwiftUI Lab
