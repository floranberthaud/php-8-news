---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: assets/background.jpg
title: Nouveautés PHP
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
favicon: assets/favicon.ico
lineNumbers: true
---

# Nouveautés de PHP 8

jusqu'à PHP 8.5

---
layout: cover
background: '#4F5B93'
---

# ![](/assets/logo_php8.svg){width=400px}

---

# Arguments nommés

Spécifiez uniquement les paramètres requis, omettant ceux optionnels  
Les arguments sont indépendants de l'ordre et auto-documentés.

Exemple :
````md magic-move
```php
// PHP < 8.0
htmlspecialchars($string, ENT_COMPAT | ENT_HTML401, 'UTF-8', false);

$defaultAddress = $this->appTourService->getFavoriteAddress('user', null, true);
$this->appTourService->addNewTask($tour, 'S', $start, 1, $defaultAddress, null, null, null, $vehicle_set, true);
$this->appTourService->addNewTask($tour, 'F', $end, 2, $defaultAddress, null, null, null, $vehicle_set, true);
```

```php
// PHP 8.0
htmlspecialchars($string, double_encode: false);

$defaultAddress = $this->appTourService->getFavoriteAddress('user', onCreateTour: true);
$this->appTourService->addNewTask($tour, 'S', $start, 1, $defaultAddress, vehicleSet: $vehicle_set, autoLock: true);
$this->appTourService->addNewTask($tour, 'F', $end, 2, $defaultAddress, vehicleSet: $vehicle_set, autoLock: true);
```
````

<v-click>

## ⚠️ [Attention]{style="color:orange"} :
Message dans les Release Notes de Laravel 8 et supérieur :
> "Les arguments nommés ne sont pas couverts par les directives de rétro-compatibilité de Laravel.  
Nous pourrions choisir de renommer les arguments des fonctions si nécessaire afin d'améliorer le code source de Laravel.  
Par conséquent, l'utilisation d'arguments nommés lors de l'appel de méthodes Laravel doit être effectuée avec prudence et en gardant à l'esprit que les noms des paramètres pourraient changer à l'avenir."
</v-click>

<!-- ---

# Attributs 

Au lieux d'annotations PHPDoc, vous pouvez désormais utiliser les métadonnées structurés avec la syntaxe native de PHP.

Exemple :
````md magic-move
```php
// PHP < 8.0
class PostsController
{
    /**
     * @Route("/api/posts/{id}", methods={"GET"})
     */
    public function get($id) { /* ... */ }
}
```

```php
// PHP 8.0
class PostsController
{
    #[Route("/api/posts/{id}", methods: ["GET"])]
    public function get($id) { /* ... */ }
}
```
```` -->

---

# Promotion de propriétés de constructeur

Moins de code redondant pour définir et initialiser les propriétés.

Exemple :
````md magic-move
```php
// PHP < 8.0
class Point {
    public float $x;
    public float $y;
    public float $z;

    public function __construct(
        float $x = 0.0,
        float $y = 0.0,
        float $z = 0.0
    ) {
        $this->x = $x;
        $this->y = $y;
        $this->z = $z;
    }
}
```

```php
// PHP 8.0
class Point {
    public function __construct(
        public float $x = 0.0,
        public float $y = 0.0,
        public float $z = 0.0,
    ) {}
}
```
````

---

# Types d'union

Au lieu d'annotation PHPDoc pour une combinaison de type, vous pouvez utiliser les déclarations de types d'union native qui sont validées lors de l'exécution.

Exemple :
````md magic-move
```php
// PHP < 8.0
class Number {
    /** @var int|float */
    private $number;

    /**
     * @param float|int $number
     */
    public function __construct($number) {
        $this->number = $number;
    }
}

new Number('NaN'); // Ok
```

```php
// PHP 8.0
class Number {
    public function __construct(
        private int|float $number
    ) {}
}

new Number('NaN'); // TypeError
```
````

---

# Expression match

La nouvelle instruction match est similaire à switch et a les fonctionnalités suivantes :  
Match est une expression, signifiant que son résultat peut être enregistré dans une variable ou retourné.  
Les branches de match supportent uniquement les expressions d'une seule ligne, et n'a pas besoin d'une déclaration break.  
Match fait des comparaisons strictes.

Exemple :
````md magic-move
```php
// PHP < 8.0
switch (8.0) {
    case '8.0':
        $result = "Oh no!";
        break;
    case 8.0:
        $result = "This is what I expected";
        break;
}
echo $result;
//> Oh no!
```

```php
// PHP 8.0
echo match (8.0) {
    '8.0' => "Oh no!",
    8.0 => "This is what I expected",
};
//> This is what I expected
```
````

---

# Opérateur Nullsafe

Au lieu de faire des vérifications conditionnelles de `null`, vous pouvez utiliser une chaîne d'appel avec le nouvel opérateur nullsafe. Qui lorsque l'évaluation d'un élément de la chaîne échoue, l'exécution de la chaîne complète est terminée et la chaîne entière évalue à `null`.

Exemple :
````md magic-move
```php
// PHP < 8.0
$country =  null;

if ($session !== null) {
    $user = $session->user;

    if ($user !== null) {
        $address = $user->getAddress();

        if ($address !== null) {
            $country = $address->country;
        }
    }
}
```

```php
// PHP 8.0
$country = $session?->user?->getAddress()?->country;
```
````

---

# Comparaisons entre les chaînes de caractères et les nombres plus saines

Lors de la comparaison avec une chaîne numérique, PHP 8 utilise une comparaison de nombre. Sinon, il convertit le nombre à une chaîne de caractères et utilise une comparaison de chaîne de caractères.

Exemple :
````md magic-move
```php
// PHP < 8.0
0 == 'foobar' // true
```

```php
// PHP 8.0
0 == 'foobar' // false
```
````

---

# Erreurs de type cohérent pour les fonctions internes

La plupart des fonctions internes lancent désormais une exception `Error` si la validation du paramètre échoue.

Exemple :
````md magic-move
```php
// PHP < 8.0
strlen([]); // Warning: strlen() expects parameter 1 to be string, array given

array_chunk([], -1); // Warning: array_chunk(): Size parameter expected to be greater than 0
```

```php
// PHP 8.0
strlen([]); // TypeError: strlen(): Argument #1 ($str) must be of type string, array given

array_chunk([], -1); // ValueError: array_chunk(): Argument #2 ($length) must be greater than 0
```
````

---
layout: cover
background: '#4F5B93'
---

# ![](/assets/logo_php8_1.svg){width=400px}

---

# Enumerations

Use enum instead of a set of constants and get validation out of the box.

Exemple :
````md magic-move
```php
// PHP < 8.1
class Status
{
    const DRAFT = 'draft';
    const PUBLISHED = 'published';
    const ARCHIVED = 'archived';
}
function acceptStatus(string $status) {...}
```

```php
// PHP 8.1
enum Status
{
    case Draft;
    case Published;
    case Archived;
}
function acceptStatus(Status $status) {...}
```
````

---

# Readonly Properties

Readonly properties cannot be changed after initialization, i.e. after a value is assigned to them.  
They are a great way to model value objects and data-transfer objects.

Exemple :
````md magic-move
```php
// PHP < 8.1
class BlogData
{
    private Status $status;

    public function __construct(Status $status)
    {
        $this->status = $status;
    }

    public function getStatus(): Status
    {
        return $this->status;
    }
}
```

```php
// PHP 8.1
class BlogData
{
    public readonly Status $status;

    public function __construct(Status $status)
    {
        $this->status = $status;
    }
}
```
````

---

# First-class Callable Syntax

It is now possible to get a reference to any function – this is called first-class callable syntax.

Exemple :
````md magic-move
```php
// PHP < 8.1
$foo = [$this, 'foo'];

$fn = Closure::fromCallable('strlen');
```

```php
// PHP 8.1
$foo = $this->foo(...);

$fn = strlen(...);
```
````

---

# New in initializers

Objects can now be used as default parameter values, static variables, and global constants.

Exemple :
````md magic-move
```php
// PHP < 8.1
class Service
{
    private Logger $logger;

    public function __construct(
        ?Logger $logger = null,
    ) {
        $this->logger = $logger ?? new NullLogger();
    }
}
```

```php
// PHP 8.1
class Service
{
    private Logger $logger;

    public function __construct(
        Logger $logger = new NullLogger(),
    ) {
        $this->logger = $logger;
    }
}
```
````

---

# Pure Intersection Types

Use intersection types when a value needs to satisfy multiple type constraints at the same time.  
It is not currently possible to mix intersection and union types together such as `A&B|C`.

Exemple :
````md magic-move
```php
// PHP < 8.1
function count_and_iterate(Iterator $value) {
    if (!($value instanceof Countable)) {
        throw new TypeError('value must be Countable');
    }

    foreach ($value as $val) {
        echo $val;
    }

    count($value);
}
```

```php
// PHP 8.1
function count_and_iterate(Iterator&Countable $value) {
    foreach ($value as $val) {
        echo $val;
    }

    count($value);
}
```
````

<!-- ---

# Never return type

A function or method declared with the `never` type indicates that it will not return a value and will either throw an exception or end the script’s execution with a call of `die()`, `exit()`, `trigger_error()`, or something similar.

Exemple :
````md magic-move
```php
// PHP < 8.1
function redirect(string $uri) {
    header('Location: ' . $uri);
    exit();
}

function redirectToLoginPage() {
    redirect('/login');
    echo 'Hello'; // <- dead code
}
```

```php
// PHP 8.1
function redirect(string $uri): never {
    header('Location: ' . $uri);
    exit();
}

function redirectToLoginPage(): never {
    redirect('/login');
    echo 'Hello'; // <- dead code detected by static analysis
}
```
```` -->

---

# Final class constants

It is possible to declare final class constants, so that they cannot be overridden in child classes.

Exemple :
````md magic-move
```php
// PHP < 8.1
class Foo
{
    public const XX = "foo";
}

class Bar extends Foo
{
    public const XX = "bar"; // No error
}
```

```php
// PHP 8.1
class Foo
{
    final public const XX = "foo";
}

class Bar extends Foo
{
    public const XX = "bar"; // Fatal error
}
```
````

---

# Explicit Octal numeral notation

It is now possible to write octal numbers with the explicit `0o` prefix.

Exemple :
````md magic-move
```php
// PHP < 8.1
016 === 16; // false because `016` is octal for `14` and it's confusing
016 === 14; // true
```

```php
// PHP 8.1
0o16 === 16; // false — not confusing with explicit notation
0o16 === 14; // true
```
````

<!-- ---

# Fibers

Fibers are primitives for implementing lightweight cooperative concurrency. They are a means of creating code blocks that can be paused and resumed like Generators, but from anywhere in the stack. Fibers themselves don't magically provide concurrency, there still needs to be an event loop. However, they allow blocking and non-blocking implementations to share the same API.  
Fibers allow getting rid of the boilerplate code previously seen with `Promise::then()` or Generator based coroutines. Libraries will generally build further abstractions around Fibers, so there's no need to interact with them directly.

Exemple :
````md magic-move
```php
// PHP < 8.1
$httpClient->request('https://example.com/')
        ->then(function (Response $response) {
            return $response->getBody()->buffer();
        })
        ->then(function (string $responseBody) {
            print json_decode($responseBody)['code'];
        });
```

```php
// PHP 8.1
$response = $httpClient->request('https://example.com/');
print json_decode($response->getBody()->buffer())['code'];
```
```` -->

---

# Array unpacking support for string-keyed arrays

PHP supported unpacking inside arrays through the spread operator before, but only if the arrays had integer keys. Now it is possible to unpack arrays with string keys too.

Exemple :
````md magic-move
```php
// PHP < 8.1
$arrayA = ['a' => 1];
$arrayB = ['b' => 2];

$result = array_merge(['a' => 0], $arrayA, $arrayB);

// ['a' => 1, 'b' => 2]
```

```php
// PHP 8.1
$arrayA = ['a' => 1];
$arrayB = ['b' => 2];

$result = ['a' => 0, ...$arrayA, ...$arrayB];

// ['a' => 1, 'b' => 2]
```
````

---
layout: cover
background: '#4F5B93'
---

# ![](/assets/logo_php8_2.svg){width=400px}

---

# Classes en lecture seule

Exemple :
````md magic-move
```php
// PHP < 8.2
class BlogData
{
    public readonly string $title;

    public readonly Status $status;

    public function __construct(string $title, Status $status)
    {
        $this->title = $title;
        $this->status = $status;
    }
}
```

```php
// PHP 8.2
readonly class BlogData
{
    public string $title;

    public Status $status;

    public function __construct(string $title, Status $status)
    {
        $this->title = $title;
        $this->status = $status;
    }
}
```
````

---

# Types FDN (forme normale disjonctive)

Les types FDN permettent de combiner des types union et intersection, en suivant une règle stricte: lorsque des types union et intersection sont combinés, les types intersection doivent être groupés entre parenthèses.

Exemple :
````md magic-move
```php
// PHP < 8.2
class Foo {
    public function bar(mixed $entity) {
        if ((($entity instanceof A) && ($entity instanceof B)) || ($entity === null)) {
            return $entity;
        }

        throw new Exception('Invalid entity');
    }
}
```

```php
// PHP 8.2
class Foo {
    public function bar((A&B)|null $entity) {
        return $entity;
    }
}
```
````

<!-- ---

# Permettre `null`, `false`, et `true` comme types stand-alone

Exemple :
````md magic-move
```php
// PHP < 8.2
class Falsy
{
    public function almostFalse(): bool { /* ... */ *}

    public function almostTrue(): bool { /* ... */ *}

    public function almostNull(): string|null { /* ... */ *}
}
```

```php
// PHP 8.2
class Falsy
{
    public function alwaysFalse(): false { /* ... */ *}

    public function alwaysTrue(): true { /* ... */ *}

    public function alwaysNull(): null { /* ... */ *}
}
```
```` -->

---

# Constantes dans les traits

Il n'est pas possible d'accéder à une constante par le nom du trait, mais il est cependant possible d'y accéder par la classe utilisant ce trait.

Exemple :
```php
// PHP 8.2
trait Foo
{
    public const CONSTANT = 1;
}

class Bar
{
    use Foo;
}

var_dump(Bar::CONSTANT); // 1
var_dump(Foo::CONSTANT); // Error
```

---

# Propriétés dynamiques désormais obsolètes

Afin d'éviter des erreurs, la création des propriétés dynamiques est obsolète, sauf si la classe contient l'attribut `#[\AllowDynamicProperties]`. `stdClass` autorise les propriétés dynamiques.  
L'utilisation des méthodes magiques `__get`/`__set` n'est pas affectée par ce changement.

Exemple :
````md magic-move
```php
// PHP < 8.2
class User
{
    public $name;
}

$user = new User();
$user->last_name = 'Doe';

$user = new stdClass();
$user->last_name = 'Doe';
```

```php
// PHP 8.2
class User
{
    public $name;
}

$user = new User();
$user->last_name = 'Doe'; // Deprecated notice

$user = new stdClass();
$user->last_name = 'Doe'; // Still allowed
```
````

---
layout: cover
background: '#4F5B93'
---

# ![](/assets/logo_php8_3.svg){width=400px}

---

# New `json_validate()` function

`json_validate()` allows to check if a string is syntactically valid JSON, while being more efficient than `json_decode()`.

Exemple :
````md magic-move
```php
// PHP < 8.3
function json_validate(string $string): bool {
    json_decode($string);

    return json_last_error() === JSON_ERROR_NONE;
}

var_dump(json_validate('{ "test": { "foo": "bar" } }')); // true
```

```php
// PHP 8.3
var_dump(json_validate('{ "test": { "foo": "bar" } }')); // true
```
````

---
layout: cover
background: '#4F5B93'
---

# ![](/assets/logo_php8_4.svg){width=400px}

---

# Hooks de propriété

Les hooks de propriété offrent un support pour les propriétés calculées, compréhensibles nativement par les IDE et les outils d'analyse statique, sans avoir besoin d'écrire des commentaires docblock susceptibles de devenir obsolètes. De plus, ils permettent un pré- ou post-traitement fiable des valeurs, sans avoir à vérifier l'existence d'un getter ou d'un setter correspondant dans la classe.

Exemple :
````md magic-move
```php
// PHP < 8.4
class Locale
{
    private string $countryCode;
    public function __construct(string $countryCode)
    {
        $this->setCountryCode($countryCode);
    }
    public function getCountryCode(): string
    {
        return $this->countryCode;
    }
    public function setCountryCode(string $countryCode): void
    {
        $this->countryCode = strtoupper($countryCode);
    }
}
```

```php
// PHP 8.4
class Locale
{
    public string $countryCode
    {
        set (string $countryCode) {
            $this->countryCode = strtoupper($countryCode);
        }
    }

    public function __construct(string $languageCode, string $countryCode)
    {
        $this->countryCode = $countryCode;
    }
}
```
````

---

# Nouvelles fonctions `array_*()`

Les nouvelles fonctions `array_find()`, `array_find_key()`, `array_any()` et `array_all()` sont désormais disponibles.

Exemple :
````md magic-move
```php
// PHP < 8.4
$animal = null;
foreach (['dog', 'cat', 'cow', 'duck', 'goose'] as $value) {
    if (str_starts_with($value, 'c')) {
        $animal = $value;
        break;
    }
}

var_dump($animal); // string(3) "cat"
```

```php
// PHP 8.4
$animal = array_find(
    ['dog', 'cat', 'cow', 'duck', 'goose'],
    static fn(string $value): bool => str_starts_with($value, 'c'),
);

var_dump($animal); // string(3) "cat"
```
````

---

# `new MyClass()->method()` sans parenthèses

Les propriétés et méthodes d'un objet nouvellement instancié peuvent désormais être accessibles sans entourer l'expression `new` entre parenthèses.

Exemple :
````md magic-move
```php
// PHP < 8.4
class PhpVersion
{
    public function getVersion(): string
    {
        return 'PHP 8.3';
    }
}

var_dump((new PhpVersion())->getVersion());
```

```php
// PHP 8.4
class PhpVersion
{
    public function getVersion(): string
    {
        return 'PHP 8.4';
    }
}

var_dump(new PhpVersion()->getVersion());
```
````

---
layout: cover
background: '#4F5B93'
---

# ![](/assets/logo_php8_5.svg){width=400px}

---

# Pipe Operator

The pipe operator allows chaining function calls together without dealing with intermediary variables. This enables replacing many "nested calls" with a chain that can be read forwards, rather than inside-out.

Exemple :
````md magic-move
```php
// PHP < 8.5
$title = ' PHP 8.5 Released ';

$slug = strtolower(
    str_replace('.', '',
        str_replace(' ', '-',
            trim($title)
        )
    )
);

var_dump($slug);
// string(15) "php-85-released"
```

```php
// PHP 8.5
$title = ' PHP 8.5 Released ';

$slug = $title
    |> trim(...)
    |> (fn($str) => str_replace(' ', '-', $str))
    |> (fn($str) => str_replace('.', '', $str))
    |> strtolower(...);

var_dump($slug);
// string(15) "php-85-released"
```
````

---

# `array_first()` and `array_last()` functions

The `array_first()` and `array_last()` functions return the first or last value of an array, respectively. If the array is empty, null is returned (making it easy to compose with the `??` operator)

Exemple :
````md magic-move
```php
// PHP < 8.5
$lastEvent = $events === []
    ? null
    : $events[array_key_last($events)];
```

```php
// PHP 8.5
$lastEvent = array_last($events);
```
````

---
layout: end
---

👋
