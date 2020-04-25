class: middle, center

# Rust Básico #

---
class: middle, center

## Variables y expresiones ##

---

### Variables ###

- Las variables se crean con `let`:

```rust
let x = 17;
```

- El compilador es capaz de detectar el tipo según el contexto. Si no es posible, hay que añadir anotaciones de tipo.

```rust
let x: i16 = 17;
```

- Las variables son inmutables por defecto:

```rust
let x = 5;
x += 1; // error: reasignando calor a la variable inmutable x
let mut y = 5;
y += 1; // OK!
```

---

### Expresiones ###

- (Casi) Todo es una expresión: algo que devuelve un valor. Excepción: las referencias no son expresiones.
- El tipo "None" o "Null" se llama "unidad", que se escribe `()`. Además es el tipo de retorno predeterminado.
- Descarta el valor de una expresión agregando un punto y coma. Ahora devuelve `()`.

```rust
fn foo() -> i32 { 5 }
fn bar() -> () { () }
fn baz() -> () { 5; }
fn qux()       { 5; }
```

---

### Expresiones II ###

- Como todo es una expresión, podemos vincular muchas cosas a variables:

```rust
let x = -5;
let y = if x > 0 { "greater" } else { "less" };
println!("x = {} is {} than zero", x, y);
```

- Además, `" {} "` es el operador de interpolación de cadenas (más básico) de Rust similar a `printf`'s `"%s"` in C/C++.

---

### Comentarios ###

```rust
/// Los comentarios de triple barra son de tipo docstring.
///
/// `rustdoc` usa los comentarios docstring para generar
/// documentación, y admite el formato ** Markdown **.
fn foo() {
    // Los comentarios de doble barra son normales.

    /* Bloquear comentarios
     * también existe /* y se puede anidar */
     */
}
```

---
class: middle, center

## Tipos ##

---

### Tipos primitivos ###

- `bool`: `true` y `false`.
- `char`: los `char`s son Unicode.

- Numéricos: especifican signo y tamaño.
  - `i8`, `i16`, `i32`, `i64`, `isize`
  - `u8`, `u16`, `u32`, `u64`, `usize`
  - `f32`, `f64`
  - `isize` & `usize` son el tamaño de los punteros (y por lo tanto tienen
            tamaño dependiente de la máquina)

- Arrays, slices, `str`, tuplas.
- Funciones.

---

### Arrays ###

- Los arrays son genéricamente del tipo `[T; N]`.
  - N es una _constante_ en tiempo de compilación. Los arrays no pueden cambiar de tamaño.
  - El acceso a la matriz se verifica en tiempo de ejecución.
- Los arrays sen indexan con `[]` como otros lenguajes:
  - `arr[3]` te da el cuarto elemento de `arr`

```rust
let arr1 = [1, 2, 3]; // (array de 3 elementos)
let arr2 = [2; 32];   // (array de 32 `2`s)
```

---

### Slices ###

- Es una "vista" de un array por referencia.
- Genéricamente son del tipo `&[T]`.
- No se crea directamente, sino que se toman prestadas de otras variables.
- Mutables o inmutables.

```rust
let arr = [0, 1, 2, 3, 4, 5];
let total_slice = &arr;         // Slice all of `arr`
let total_slice = &arr[..];     // Same, but more explicit
let partial_slice = &arr[2..5]; // [2, 3, 4]
```

---

### Strings ###

- Dos tipos de strings: `String` y `&str`.
- `String` es un vector de caracteres guardado en el heap y que puede crecer.
- `&str` son de tipo slice, tiene un tamaño fijo, y no puede ser modificado.
- Literales como `"foo"` son del tipo `&str`.

```rust
let s: &str = "galaxy";
let s2: String = "galaxy".to_string();
let s3: String = String::from("galaxy");
let s4: &str = &s3;
```

---

### Tuplas ###

- Listas heterogéneas, ordenadas, de tamaño fijo.
- A los valores se acceden por indice: `foo.0`, `foo.1`, etc.
- Se puede des-estructurar usando `let`.

```rust
let foo: (i32, char, f64) = (72, 'H', 5.1);
let (x, y, z) = (72, 'H', 5.1);
let (a, b, c) = foo; // a = 72, b = 'H', c = 5.1
```

---

### `Vec<T>` ###

- Un tipo de biblioteca estándar: no necesita importar nada.
- Un `Vec` es un array dinámico que se guarda en la pila de memoria.
  - (Java -> `ArrayList`, C++ -> `std::vector`, etc.)
- `<T>` denota un tipo genérico.
- Para crear `Vec` se usa `Vec::new()` or el macro `vec!`.

```rust
// Explicit typing
let mut v1 = Vec::new();
v1.push(1);
v1.push(2);
v1.push(3);

let v2 = vec![1, 2, 3];
```

- Like arrays, vectors can be indexed with `[]`.

---

### Referencias ###

- Los *tipo* referencia empiezan con `&`: `&i32`.
- Se pueden tomar referencias con `&` (como C/C++) de otras variables.
- Las referencias se pueden _de-referenciar_ con `*` (como C/C++).
- Se garantiza que las referencias serán válidas a través de comprobaciones en tiempo de compilación!
- Esta garantía se realiza mediante "lifetimes".

```rust
let x = 12;
let ref_x = &x;
println!("{}", *ref_x); // 12
```

---
class: middle, center

## Control de flujo ##

---

### Sentencias if ###

```rust
if x > 0 {
    10
} else if x == 0 {
    0
} else {
    println!("Not greater than zero!");
    -10
}
```

- Pueden usarse para devolver un valor:

```rust
if x > 0 {
    10
} else {
    println!("Not greater than zero!");
    -10
}
```

---

### Bucles ###

- Los bucles vienen en tres formas: `while`, `loop`, and `for`.
  - `break` y `continue` existen como en la mayoría de los idiomas.

- `while` funciona exactamente como cabría esperar:

```rust
let mut x = 0;
while x < 100 {
    x += 1;
    println!("x: {}", x);
}
```

---

### loop ###

- `loop` es equivalente a `while true`.
  - Además, el compilador puede hacer optimizaciones sabiendo que es infinito.

```rust
let mut x = 0;
loop {
    x += 1;
    println!("x: {}", x);
}
```

---

### for ###

- `for` is the most different from most C-like languages
  - Usan _iterator expression_:
  - `n..m` crea un iterador de n a m(exclusivo).
  - Algunas estructuras de datos se pueden usar como iteradores, como los arrays y los `Vec`s.

```rust
// Bucles de 0 a 9.
for x in 0..10 {
    println!("{}", x);
}

let xs = [0, 1, 2, 3, 4];
// Recorre los elementos en un slice de `xs`.
for x in &xs {
    println!("{}", x);
}
```

---
class: middle, center

## Funciones ##

---

### Funciones I ###

```rust
fn foo(x: T, y: U, z: V) -> T {
    // ...
}
```

- `foo` is a function that takes three parameters:
  - `x` del tipo `T`
  - `y` del tipo `U`
  - `z` del tipo `V`
- `foo` devuelve un tipo `T`.

- Se deben definir explícitamente argumentos y los tipos de retorno.
  - El compilador es lo suficientemente inteligente como para resolver esto por ti, pero los diseñadores de Rust decidieron que era una mejor práctica forzar una función explícita mecanografía.

---

### Funciones II ###

- La expresión final en una función es su valor de retorno.
  - Usa `return` para devolver un valor _rápidamente_ de una función.

```rust
fn square(n: i32) -> i32 {
    n * n
}

fn squareish(n: i32) -> i32 {
    if n < 5 { return n; }
    n * n
}

fn square_bad(n: i32) -> i32 {
    n * n;
}
```

- ¡El último ejemplo no compilará!
  - ¿Por qué? Termina en punto y coma, por lo que se evalúa como `()`.

---

### Funciones Objeto ###

- Se pueden usar varias cosas como objetos de función:
  - Punteros de función.
  - Closures.
- Mucho más directo que los punteros de función en C:

```rust
let x: fn(i32) -> i32 = square;
```

- Se pueden pasar por referencia:

```rust
fn apply_twice(f: &Fn(i32) -> i32, x: i32) -> i32 {
    f(f(x))
}

// ...

let y = apply_twice(&square, 5);
```

---
class: middle, center

## Macros ##

---

### Que son los Macros ###

- Las macros son como funciones, pero se nombran con `!` al final.
- Se pueden hacer cosas muy poderosas con ellos.
  - ¡Realmente generan código en tiempo de compilación!
- Se llaman y usan como funciones.
- Puedes definirlos con `macro_rules! macro_name`.
- Debido a que son muy potentes, muchas utilidades comunes se definen como macros.

---

### `print!` & `println!` ###

- Sirven para imprimir cosas por `stdout`.
- Utiliza `{}` para la interpolación cadenas y `{:?}` para cosas de depuración.
  - Algunos tipos solo se pueden imprimir con `{:?}`, como los arrays y `Vec`s.

```rust
print!("{}, {}, {}", "foo", 3, true);
// => foo, 3, true
println!("{:?}, {:?}", "foo", [1, 2, 3]);
// => "foo", [1, 2, 3]
```

---

### `format!` ###

- Utiliza la interpolación de cadenas al estilo `println!` para crear `String`s formateadas.

```rust
let fmted = format!("{}, {:x}, {:?}", 12, 155, Some("Hello"));
// fmted == "12, 9b, Some("Hello")"
```

---

### `panic!(msg)` ###

- Sale de la tarea actual con el mensaje dado.
- ¡No hagas esto a la ligera! Es mejor manejar e informar errores explícitamente.

```rust
if x < 0 {
    panic!("Oh noes!");
}
```

---

### `assert!` & `assert_eq!` ###

- `assert!(condition)` panics si `condition` es `false`.
- `assert_eq!(left, right)` panics si `left != right`.
- Útil para probar y detectar condiciones ilegales.

```rust
#[test]
fn test_something() {
    let actual = 1 + 2;
    assert!(actual == 3);
    assert_eq!(3, actual);
}
```

---

### Otros ###

- `unreachable!()`

```rust
if false {
    unreachable!();
}
```

- `unimplemented!()`

```rust
fn sum(x: Vec<i32>) -> i32 {
    // TODO
    unimplemented!();
}
```

---
class: middle, center

## Pattern Matching ##

---

### Declaraciones Match ###

```rust
let x = 3;

match x {
    1 => println!("one fish"),  // <- coma requerida
    2 => {
        println!("two fish");
        println!("two fish");
    },  // <- coma opcional al usar llaves
    _ => println!("no fish for you"), // caso "por defecto"
}
```

- `match` toma una expresión (`x`) y se ramifica en una lista de declaraciones `valor => expresión`.
- Cada coincidencia se evalúa en una expresión.
    - Al igual que `if`, todos los casos deben evaluar al mismo tipo.
- `_` se usa comúnmente como comodín por defecto.

---

## Sentencias Match complejas ###

```rust
let x = 3;
let y = -3;

match (x, y) {
    (1, 1) => println!("one"),
    (2, j) => println!("two, {}", j),
    (_, 3) => println!("three"),
    (i, j) if i > 5 && j < 0 => println!("On guard!"),
    (_, _) => println!(":<"),
}
```

- La expresión coincidente puede ser cualquier expresión (valor l), incluidas tuplas y llamadas a funciones.
  - `match` puede definir variables. `_` se usa para descartar una variable.
- Debes escribir todas las coincidencia para que compile.
- Usa guardias `if` para restringir a ciertas condiciones.
- Los patrones pueden volverse muy complejos.

---
class: middle, center

## Herramientas de desarrollo ##

---

### Rustc ###

- El compilador de Rust es `rustc`.
- Ejecuta `rustc your_program.rs` para compilar en un ejecutable `your_program`.
  - Cosas como las advertencias están habilitadas de forma predeterminada.
  - La salida devuelta contiene información detallada de mucha utilidad.
- `rustc` no necesita ser llamado una vez para cada archivo como en C.
  - El árbol de dependencias es inferido del la declaración de módulos en el código (empezando por `main.rs` o `lib.rs`).
- Sin embargo, por lo general usarás `cargo`, administrador de paquetes y sistema de compilación de Rust.

---

### Cargo ###

- Es el administrador de paquetes y sistema de compilación de Rust.
- Crea un nuevo proyecto:
  - `cargo new project_name` (libraría)
  - `cargo new project_name --bin` (ejecutable)
- Construye tu proyecto: `cargo build`
- Ejecute los test: `cargo test`

---

### Cargo.toml ###

- Cargo utiliza el archivo `Cargo.toml` para declarar y administrar las dependencias y los metadatos del proyecto.
    - TOML es un formato simple similar a INI.

```toml
[package]
name = "Rust"
version = "0.1.0"
authors = ["Ferris <cis198@seas.upenn.edu>"]

[dependencies]
uuid = "0.1"
rand = "0.3"

[profile.release]
opt-level = 3
debug = false
```

---

### `cargo test` ###

- Una prueba es cualquier función anotada con `#[test]`.
- `cargo test`ejecutará todas las funciones anotadas en su proyecto.
- Cualquier función que se ejecute sin fallar (`panic!`ing) es correcta.
- Usa `assert!` (O `assert_eq!`) para verificar las condiciones (y `panic!` en caso de fallo).

```rust
#[test]
fn it_works() {
    // ...
}
```

---

### Soporte de IDEs ###

- Los desarrolladores pueden trabajar con código de Rust desde la línea de comandos o usando.
- Rust dispone de plugins para los editores más importantes:
  - VS Code
  - Sublime Text 3
  - Atom
  - IntelliJ IDEA
  - Eclipse
  - Vim
- Además, es posible crear una integración nueva usando el servidor de Rust Language.
