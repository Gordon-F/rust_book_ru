% Язык программирования Rust

Добро пожаловать! Эта книга обучает основным принципам работы с языком
программирования [Rust][rust]. Rust - это системный язык программирования,
внимание которого сосредоточено на трёх задачах: безопасность, скорость и
параллелизм. Он решает эти задачи без сборщика мусора, что делает его полезным в
ряде случаев, когда использование других языков было бы не целесообразно: при
встраивании в другие языки, при написании программ с особенными
пространственными и временными требованиями, при написании низкоуровневого кода,
такого как драйверы устройств и операционные системы. Во время компиляции
Rust делает ряд проверок безопасности. За счёт этого не появляется накладных
расходов во время выполнения приложения и устраняются все гонки данных.
Это даёт Rust'у преимущество над другими языками программирования, имеющими
аналогичную направленность. Rust также направлен на достижение ‘абстракции с
нулевой стоимостью‘, хотя некоторые из этих абстракций ведут себя как в языках
высокого уровня. Даже тогда Rust по-прежнему обеспечивает точный контроль, как
делал бы язык низкого уровня.

[rust]: http://rust-lang.org

Книга "Язык программирования Rust" делится на семь разделов. Это введение
является первым из них. Затем идут:

* [C чего начать][gs] - Настройка компьютера для разработки на Rust.
* [Изучение Rust][lr] - Обучение программированию на Rust на примере небольших
проектов.
* [Эффективное использование Rust][er] - Понятия более высокого уровня для
написания качественного код на Rust.
* [Синтаксис и семантика][ss] - Каждая концепция Rust разбивается на небольшие
кусочки.
* [Нестабильные фичи Rust][nr] - Передовые фичи, которые пока не добавлены в
стабильную сборку.
* [Глоссарий][gl] - Ссылки на термины, используемые в книге.

[gs]: getting-started.html
[lr]: learn-rust.html
[er]: effective-rust.html
[ss]: syntax-and-semantics.html
[nr]: nightly-rust.html
[gl]: glossary.html

После прочтения этого введения, в зависимости от ваших предпочтений, вы можете
продолжить дальнейшее изучение либо в направлении ’Изучение Rust’, либо в
направлении ’Синтаксис и семантика’. Если вы предпочитаете изучить язык на
примере реального проекта, лучшим выбором будет раздел ’Изучение Rust’.
Раздел ’Синтаксис и семантика’ подойдёт тем, кто предпочитает тщательно изучить
каждую концепцию языка отдельно, перед тем как двигаться дальше.
Большое количество перекрёстных ссылок соединяет эти части воедино.

## Краткое введение в Rust

Чем же Rust может заинтересовать вас? Давайте рассмотрим несколько небольших
примеров кода, чтобы продемонстрировать некоторые из его сильных сторон.

Основная концепция, которая делает Rust уникальным, называется ‘владение’.
Рассмотрим следующий небольшой пример:

```rust
fn main() {
    let mut x = vec!["Hello", "world"];
}
```

Эта программа создаёт [привязку переменной][var] с именем `x`. Значением этого
связывания является `Vec<T>`, ‘вектор‘, который мы создаём с помощью
[макроса][macro], определённого в стандартной библиотеке. Этот макрос называется
`vec`, и при его вызове используется символ `!`. Это следует из общего принципа
Rust: делать вещи явными. Макрос может делать значительно более сложные вещи,
чем вызовов функций, и поэтому они визуально отличаются. Символ `!` также
помогает при парсинге, что облегчает написание инструментов, а это тоже важно.

Мы использовали `mut`, чтобы сделать `x` изменяемой: в Rust по умолчанию
привязки являются неизменяемыми. Дальше в примере мы будем изменять этот вектор.

Стоит также отметить, что здесь нам не нужно указывать тип: несмотря на то, что
Rust является статически типизированным, нам не нужно явно указывать тип. Rust
может выводить типы, что балансирует мощь статической типизации с
многословностью указания типов.

Rust предпочитает выделять память в стеке, а не в куче: `x` находится
непосредственно в стеке. Однако тип `Vec<T>` выделяет пространство для
элементов вектора в куче. Если вы не знакомы с различиями этих двух видов
выделения памяти, вы пока что можете просто проигнорировать эту информацию
или же ознакомиться с разделом [‘Стек и Куча‘][heap]. Как системный язык
программирования, Rust даёт вам возможность контролировать выделение памяти, но
не будем забегать вперёд, мы только начинаем изучение языка.

[var]: variable-bindings.html
[macro]: macros.html
[heap]: the-stack-and-the-heap.html

Ранее мы упоминали, что ‘владение‘ является основной новой концепцией в Rust.
В терминологии Rust, `x` ‘владеет‘ вектором. Это означает, что как только `x`
выходит из области видимости, выделенная для вектора память будет освобождена.
Когда это будет происходить, определяется средствами компилятора Rust, а не
через механизмы, такие как сборщик мусора. Другими словами, в Rust вы не
вызываете функции вроде `malloc` и `free` самостоятельно: компилятор статически
определяет, когда нужно выделить или освободить память, и вставляет эти вызовы
самостоятельно. Человек может совершить ошибку при использовании этих вызовов,
а компилятор - никогда.

Давайте добавим ещё одну строку в наш пример:

```rust
fn main() {
    let mut x = vec!["Hello", "world"];

    let y = &x[0];
}
```

Мы создаём ещё одну привязку, `y`. В этом случае, `y` является ‘ссылкой‘ на
первый элемент вектора. Ссылки в Rust похожи на указатели в других языках, но с
дополнительными проверками безопасности на этапе компиляции. Ссылки
взаимодействуют с системой прав владения при помощи [‘заимствования’][borrowing].
Ссылки заимствуют то, на что они указывают, а не получают права владения им.
Разница в том, что при заимствовании, ссылка не освобождает основную память,
когда выходит за пределы области видимости. Если бы это было не так, то память
освобождалась два раза, что плохо!

[borrowing]: references-and-borrowing.html

Давайте добавим третью строку. На первый взгляд в коде нет ничего такого, но он
вызывает ошибку компиляции:

```rust,ignore
fn main() {
    let mut x = vec!["Hello", "world"];

    let y = &x[0];

    x.push("foo");
}
```

`push` - это метод, который добавляет ещё один элемент в конец вектора. Когда мы
пытаемся скомпилировать эту программу, то получаем ошибку:

```text
error: cannot borrow `x` as mutable because it is also borrowed as immutable
    x.push(4);
    ^
note: previous borrow of `x` occurs here; the immutable borrow prevents
subsequent moves or mutable borrows of `x` until the borrow ends
    let y = &x[0];
             ^
note: previous borrow ends here
fn main() {

}
^
```

Вот так! Компилятор Rust в некоторых случаях выдаёт достаточно подробные ошибки,
и это как раз один из таких случаев. Как объясняется в ошибке, мы не можем
изменить связывание (не можем вызвать метод `push`). Это потому, что у нас уже
есть ссылка на элемент вектора, `y`. Изменять вектор, пока существует другая
ссылка на него, опасно, потому что можно сделать ссылку недействительной. В
данном конкретном случае, когда мы создаём вектор, у нас есть выделенное
пространство памяти только для трёх элементов. Добавление четвёртого элемента
будет означать выделение нового блока памяти для всех этих элементов, копирование
старых значений и обновление внутреннего указателя на эту память. Всё это работает
просто отлично. Проблема заключается в том, что `y` не будет обновлена, из-за
чего мы получим ‘зависший указатель‘. И это плохо. В этом случае любое
использование `y` будет означать ошибку, и поэтому компилятор поймал её для нас.

Так как же нам решить эту проблему? Есть два подхода, которые мы можем
использовать. Первый заключается в создании копии вместо ссылки:

```rust
fn main() {
    let mut x = vec!["Hello", "world"];

    let y = x[0].clone();

    x.push("foo");
}
```

По умолчанию, Rust использует [семантику перемещения][move], поэтому, если мы
хотим сделать копию некоторых данных, мы должны вызывать метод `clone()`. В этом
примере `y` больше не является ссылкой на вектор, хранящийся в `x`, но является
копией его первого элемента, `"Hello"`. Теперь, когда у нас больше нет ссылки,
метод `push()` работает просто отлично.

[move]: move-semantics.html

Если мы все же хотим ссылку, то следует использовать другой вариант: убедиться,
что наша ссылка выходит из области видимости прежде чем мы попытаемся сделать
изменения. Это выглядит примерно так:

```rust
fn main() {
    let mut x = vec!["Hello", "world"];

    {
        let y = &x[0];
    }

    x.push("foo");
}
```

Мы создали внутреннюю область видимости с помощью дополнительных фигурных
скобок. `y` выйдет за пределы этой области видимости до вызова метода `push()`,
и поэтому все будет хорошо.

Концепция права владения хороша не только для предотвращения повисших
указателей, но также всей совокупности связанных с этим проблем, таких как:
недействительность итератора, параллелизм и многое другое.