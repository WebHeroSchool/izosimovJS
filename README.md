# Практика хорошего кода

В качестве ответа на задание о **best practices** написания понятного и правильного кода, я приведу в пример несколько пунктов, которые, на мой скромный взгляд, помогают в работе.

---
## 1. Когда *let*, когда *var*, когда *const*?

> var – старая версия объявления
>> let, const – новая спецификация

Очевидно, переменные объявляются по-разному не просто так. В новой спецификации были введены способы объявления переменных *let* и *const*. И, по сути, можно подумать, что использование в проекте *let* и *const*, если не исключает использование *var*, то делает его бессмысленным, хотя в некотором смысле её использование более гибкое ~~и привычное~~.
Тем не менее, сравнивая практику применения этих способов, можно сделать короткие выводы.
Если с *const* всё более понятно: переменная объявляется со значением, которое невозможно будет переопределить - это вызовет ошибку. А также этот способ позволяет сразу определить, сколько памяти потребуется для хранения значения переменной.
Но в случае с *let* и *var* всё не так однозначно. Во многом в JS нельзя сказать, что что-то использовать категорически не стоит, а что-то прям-таки обязательно. Каждый инструмент нужно использовать тогда, когда это наиболее оправдано.

*let* и *var* имеют между собой несколько отличий.

| let | var |
| ------ | ----------- |
| Видна только в рамках блока `{...}`, в котором объявлена | Видна везде в функции |
| До объявления их не существует | Существуют и до объявления и равны `undefined`|
| Нельзя повторно объявлять | Можно повторно объявлять |
| При использовании в цикле, для каждой итерации создаётся своя переменная | Одна на все итерации цикла и видна даже после цикла |

Но при использовании асинхронности, например, вызывая функцию ***setTimeout***, переменная *var* ~~утонет в пучине этого болота~~ будет просто переопределяться на каждой итерации и "потеряет" значения, в то время как *let*, создавая на каждой итерации новую переменную, сохраняет значения.

```js
for (var i = 1; i < 10; i++) {
    setTimeout(function() {
        console.log(i);
    }, 0);
}
```

Вне зависимости от того, каким способом пользоваться, стоит учитывать эти моменты в работе.

---

## 2. Неправильное использование тернарных операторов.

Часто в попытке упростить код в качестве укороченного варианта условного оператора `if` используют ***условный (тернарный) оператор***.

Логика простая:

`условие ? выражение1 : выражение2`

Однако, *важно* помнить: оператор возвращает значение выражения1, если условие верно, *и* значение выражения2 в противном случае.
Плохой практикой считается, если один оператор что-то возвращает, а другой ничего.

Хорошо было бы так:
```js
var money = 100;
var status = money > 99 ? 'Rich' : 'Poor';
console.log(status);
```
---

## 3. Названия переменных.

Было бы ~~просто свинство~~ некорректно называть переменные, не в соответствии с их смысловой нагрузкой и с плохой читаемостью.

Переменные типа `lol`, `kek`, `mashina` - не вызовут радости у ваших коллег и разработчиков, которые в будущем будут работать с вашим кодом. :trollface:

Хорошей практикой было бы использвать, например:

* `isCold = true` - для булевых значений
* `getSum = function(){}` - для функций
* `const SOME_CONSTANT` - для констант
* `let myName` - camelCase для обычной переменной

---

## 4. Комментирование кода.

Комментарии позволяют сделать код более понятным для восприятия и пояснения логики программы.

Для этого они должны быть ёмкими, существенными, не должны содержать совсем уж очевидные вещи и, разумеется, флуд и излишние комментрии типа "ну наверное тут должно быть так".

***Комментарии должны пояснять непонятные моменты***, трудные или спорные ситуации.

Такое лучше не делать:

```js
/* Принимаем в качестве параметров 2 числа,
складываем их и возвращаем результат */

getSum = (a, b) => a + b;
```

В комментарии следует указать, какое решение вы пытались найти и почему вы решили, что оно не подходит в данной ситуации или не работает.

```js
/* не используйте глобальную функцию isFinite(),
потому что она возвращает true для
нулевых значений */

Number.isFinite(value);
```
---

## 5. Пользование встроенными функциями языка.

В JS есть стандартные встроенные методы объектов. Они намного оптимизированнее, занимают меньше объёма кода, чем в случае, если эти методы писать собственноручно.

Например, использование методов *forEach*, *map*, *filter*, *reduce*, *every*, *some* лучше скажутся на производительности кода и его понимании.

Например, можно использовать метод *sort*, очевидно, намного эффективнее ~~если только ты не сошел с ума~~, чем написание своей функции сортировки.

---

## 6. Особенности использования стрелочных функций.

В ES6 появилась возможность использовать стрелочные функции. В некоторых особенностях их работы стоит разобраться, чтобы лучше понимать, когда их использовать стоит, а когда нет.

Стрелочные функции имеют ряд преимуществ. Например:

* *Упрощённый синтаксис*

`func = () => console.log('Привет');`

Всё равно что:

```js
const func = function() {
    return console.log('Привет');
}
```

* *Отсутсвие привязки контекста*

В классических выражениях функций *this* привязан к различным значениям, основанным на контексте, в котором он вызывается. Однако со стрелочными функциями *this* лексически связано. Это означает, что *this* используется из кода, содержащего функцию стрелки.

```js
var obj = {
    number: 10,
    counter: function counter() {
        setTimeout(() => {
            console.log(this.number)
        }, 1000)
    }
};
```

Стрелочные функции ES6 не могут быть привязаны к ключевому слову *this*, поэтому он будет лексически подниматься на область действия и использовать значение *this* в области, в которой он был определен.

И ряд недостатков, например:

* *Не стоит использовать стрелочные функции, например, в рекурсивном решении*

Если ваша функция должна ссылаться на саму себя в любой момент, это не сработает.

* *Не стоит использовать стрелочные функции в методах объекта*

```js
var man = {
    name: 'Вася',
    sayHi: () => {
        console.log(`Привет, ${this.name}`);
    }
}

man.sayHi();
```

Это связано с тем, что this не связано ни с чем, и наследует значение this из его родительской области.

Стрелочные функции лучше всего подходят когда требуется от this привязка к контексту, а не к самой функции.

---

## 7. Преимущество работы с classes.

Практически всё в JavaScript — это объекты. У каждого объекта есть прототип, от которого он наследует свойства и методы.

ООП в JS представлено условно специальными функциями-конструкторами и классами.

При создании нового instance (экземпляра) объекта, отзалупившегося от Component, метод this.render будет создаваться каждый раз заново.

```js
function Component(content) {
    this.content = content;
    this.first = "first";
    this.second = "second";
    this.render = () => {
        console.log(this.content);
    }
}
```

Поэтому кастомным объектам можно добавить функцию в свойство prototype, которая будет создана один раз и записана в `__proto__`. Таким образом, каждый раз, когда будет вызываться метод `render`, JS будет брать его из прототипа (`__proto__`).

```js
function Component(content) {
    this.content = content;
    this.first = "first";
    this.second = "second";
}
Component.prototype.render = function() {
    console.log(this.content);
}
```

При появлении в JS классов, эта строчка с присвоением Component.prototype.render = ... - оказалась не нужна, потому что это уже реализовано в самом class.

```js
class Component {
    constructor(content) {
        this.content = content;
        this.first = "first";
        this.second = "second";
    }

    render() {
        console.log(this.content);
    }
}
```

Также, при использовании классов при итерации по свойствам объекта, эти методы указаны не будут, потому что им сразу ставится флаг `enumerable`, в отличие от использования функций-конструкторов.

```js
class Component {
    constructor(content) {
        this.content = content;
        this.first = "first";
        this.second = "second";
    }

    render() {
        console.log(this.content);
    }
};

let instance = new Component("content");

for (i in instance) {
    console.log(i); //content, first, second
};
```

---

## 8. KISS / DRY.

Если проект прост, его легко понять. Разработать простой проект не так легко. Для всякой сколько-нибудь сложной программы окончательное решение получается в результате анализа огромного объема информации. Когда код хорошо спроектирован, кажется, что он и не мог быть иным, однако возможно, что его простота достигнута в результате напряженного умственного труда (и большого объема рефакторинга). Сделать простую вещь сложно. Если структура кода кажется очевидной, не надо думать, что это далось без труда.
Принцип проектирования ***KISS*** (keep it simple and straightforward) провозглашает, что простота кода – превыше всего, потому что простой код – наиболее понятный.
Непонятный код автоматически вызывает у человека ощущение того, что код сложный, так как его сложно понимать и модифицировать. При нарушении любого из этих принципов также нарушается и принцип KISS. Поэтому можно говорить, что KISS включает почти все остальные принципы проектирования.
Патерны проектирования описывают наиболее удачные, простые и понятные решения некоторых проблем. Если вы используете паттерн проектирования там, где нет проблемы, которую решает данный паттерн – то вы нарушаете KISS, внося ненужные усложнения в код. Если вы НЕ используете паттерн проектирования там, где есть проблема, соответствующая паттерну – то вы опять-таки нарушаете KISS, делая код сложнее, чем он мог бы быть.

*В проектировании следование принципу KISS выражается в том, что:*

+ не имеет смысла реализовывать дополнительные функции, которые не будут использоваться вовсе или их использование крайне маловероятно, как правило, большинству пользователей достаточно базового функционала, а усложнение только вредит удобству приложения;

+ не стоит перегружать интерфейс теми опциями, которые не будут нужны большинству пользователей, гораздо проще предусмотреть для них отдельный «расширенный» интерфейс (или вовсе отказаться от данного функционала);

+ бессмысленно делать реализацию сложной бизнес-логики, которая учитывает абсолютно все возможные варианты поведения системы, пользователя и окружающей среды, — во-первых, это просто невозможно, а во-вторых, такая фанатичность заставляет собирать «звездолёт», что чаще всего иррационально с коммерческой точки зрения.

*В программировании следование принципу KISS можно описать так:*

+ не имеет смысла беспредельно увеличивать уровень абстракции, надо уметь вовремя остановиться;

+ бессмысленно закладывать в проект избыточные функции «про запас», которые может быть когда-нибудь кому-либо понадобятся (тут скорее правильнее подход согласно принципу YAGNI);

+ не стоит подключать огромную библиотеку, если вам от неё нужна лишь пара функций;

+ декомпозиция чего-то сложного на простые составляющие — это архитектурно верный подход (тут KISS перекликается с DRY);

+ абсолютная математическая точность или предельная детализация нужны не всегда — большинство систем создаются не для запуска космических шаттлов, данные можно и нужно обрабатывать с той точностью, которая достаточна для качественного решения задачи, а детализацию выдавать в нужном пользователю объёме, а не в максимально возможном объёме.

Следование принципу программирования ***«DRY»*** позволяет добиться высокой сопровождаемости проекта: простоты внесения изменений и качественного тестирования.

Если код не дублируется, то для изменения логики достаточно внесения исправлений всего в одном месте и проще тестировать одну (пусть и более сложную) функцию, а не набор из десятков однотипных. Следование принципу *DRY* всегда приводит к декомпозиции сложных алгоритмов на простые функции. А декомпозиция сложных операций на более простые (и повторно используемые) значительно упрощает понимание программного кода. Повторное использование функций, вынесенных из сложных алгоритмов, позволяет сократить время разработки и тестирования новой функциональности.

В рамках одного программного класса (или модуля) следовать *DRY* и не повторяться обычно достаточно просто. Также не требует титанических усилий делать это в рамках небольших проектов, где все разработчики «владеют» всем кодом системы. А вот в больших проектах ситуация с *DRY* несколько сложнее — повторы чаще всего появляются из-за отсутствия у разработчиков целостной картины или несогласованности действий в рамках команды. Следовать принципу «don’t repeat yourself» в рамках больших проектов не так просто, как это может показаться на первый взгляд. От разработчиков требуется тщательное планирование архитектуры, а от архитектора или тимлида требуется наличие видения системы в целом и чёткая постановка задач разработчикам.

В пректировании *DRY* тоже имеет место — доступ к конкретному функционалу должен быть доступен в одном месте, унифицирован и сгруппирован по какому-либо принципу, а не «разбросан» по системе в произвольных вариациях.

---

## 9. Использование === вместо ==.

В JavaScript существует два разных типа операций сравния: === / !== и == / !=. Считается хорошим тоном всегда использовать первую пару для сравнения.

Если два операнда одного типа и значения, то === вернет true, а !== false.

```js
""           ===   "0"           // false
0            ===   ""            // false
0            ===   "0"           // false
false        ===   "false"       // false
false        ===   "0"           // false
false        ===   undefined     // false
false        ===   null          // false
null         ===   undefined     // false
" \t\r\n"    ===   0             // false
```

## 10. Не используйте короткую запись.

Технически можно писать код без фигурных скобок и точек с запятой. Большинство браузеров корректно воспримет следующий код:

```js
if(someVariableExists)  
   x = false
```

А этот?

```js
if(someVariableExists)  
   x = false  
   anotherFunctionCall();
```

Кто-то может посчитать что это эквивалентно следующему:

```js
if(someVariableExists) {  
   x = false;  
   anotherFunctionCall();  
}
```

И он будет неправ. Потому что на самом деле для компилятора это выглядит так:

```js
if(someVariableExists) {  
   x = false;  
}  
anotherFunctionCall();
```

Как вы заметили отступ маскирует функционал фигурных скобок. Излишне говорить, что это ужасная практика, которую следует избегать любой ценой. Единственное где вы можете опустить использование скобок это в однострочных выражениях, но даже это вызывает кучу споров.

```js
if(2 + 2 === 4) return 'nicely done';
```

---

&#x1F539;
WebHero
&#x1F538;

![workHard](https://cs8.pikabu.ru/post_img/2017/03/06/6/1488792846146085519.gif)
