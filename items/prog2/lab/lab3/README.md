# Лабораторная работа #3

## Вступление

Вам предстоит выполнить 10 заданий: написать код под каждое

## Работа

### Задание 1
Вывести на экран в виде таблицы (протабулировать)
значения функций `sin(x)`, `(e^x)/(x * lg(x))` для значений x из интервала
`[π/15..pi]`, меняющихся с шагом `π/15`. 
Для столбца со значениями аргумента нужно использовать представление с фиксированной точкой,
ширина столбца - *10* позици, точность - *5* знаков после запятой. Для
столбца со значениями функции: экспоненциальное представление,
ширина *15* позиций, точность *7* знаков.

```java
import java.util.Map;
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedHashMap;

public class Task1 {

    private static String makeTable(Map<String, String[]> data, int width) {
        List<List<String>> columns = new ArrayList<>();

        for (Map.Entry<String, String[]> entry : data.entrySet()) {
            List<String> column = new ArrayList<>();
            column.add(entry.getKey());
            column.add("-".repeat(width));
            column.addAll(Arrays.asList(entry.getValue()));
            columns.add(column);
        }

        StringBuilder table = new StringBuilder();
        int row_count = columns.get(0).size();

        for (int i = 0; i < row_count; i++) {
            for (List<String> column : columns) {
                table.append(String.format("| %-" + width + "s ", column.get(i)));
            }
            table.append("|\n");
        }
        return table.toString();
    }

    public static void main(String[] args) {
        LinkedHashMap<String, String[]> table_data = new LinkedHashMap<>();

        // ----------------- Arguments -----------------
        double step = Math.PI / 15.0;
        double end = Math.PI;
        List<Double> values = new ArrayList<>();

        for (double value = Math.PI / 15.0; value <= end; value += step) {
            values.add(value);
        }
        table_data.put("x", values.stream().map(val -> String.format("%.5f", val)).toArray(String[]::new));

        // ----------------- sin(x) -----------------
        table_data.put(
                "sin(x)", values.stream().map(Math::sin).map(val -> String.format("%.7e", val)).toArray(String[]::new)
        );

        // ------------ (e^x)/(x * lg(x)) -----------
        table_data.put("(e^x)/(x * lg(x))", values.stream().map(val -> {
            if (val == 0.0) {
                return "0.0";
            }
            return String.format("%.7e", Math.exp(val) / (val * Math.log(val)));
        }).toArray(String[]::new));

        System.out.println(makeTable(table_data, 15));
    }
}

```

##### Вывод
```bash
| x               | sin(x)          | (e^x)/(x * lg(x)) |
| --------------- | --------------- | --------------- |
| 0,20944         | 2,0791169e-01   | -3,7657531e+00  |
| 0,41888         | 4,0673664e-01   | -4,1708308e+00  |
| 0,62832         | 5,8778525e-01   | -6,4197073e+00  |
| 0,83776         | 7,4314483e-01   | -1,5583972e+01  |
| 1,04720         | 8,6602540e-01   | 5,9006089e+01   |
| 1,25664         | 9,5105652e-01   | 1,2239682e+01   |
| 1,46608         | 9,9452190e-01   | 7,7235842e+00   |
| 1,67552         | 9,9452190e-01   | 6,1768502e+00   |
| 1,88496         | 9,5105652e-01   | 5,5118960e+00   |
| 2,09440         | 8,6602540e-01   | 5,2447599e+00   |
| 2,30383         | 7,4314483e-01   | 5,2074608e+00   |
| 2,51327         | 5,8778525e-01   | 5,3299753e+00   |
| 2,72271         | 4,0673664e-01   | 5,5814962e+00   |
| 2,93215         | 2,0791169e-01   | 5,9501124e+00   |
| 3,14159         | 5,6655389e-16   | 6,4346282e+00   |
```
##### Немного слов о коде
Для отображения до 5ти знаков после запятой использовался метод `String.format("%.5f", val)`, 
для экспоненциального представления - `String.format("%.7e", val)`. Выравнивание по левому краю и ограничение ширины строк: 
`String.format("| %-" + width + "s ", column.get(i))`.

Рассмотрим кусок java кода:
```java
table_data.put("(e^x)/(x * lg(x))", values.stream().map(val -> {
            if (val == 0.0) {
                return "0.0";
            }
            return String.format("%.7e", Math.exp(val) / (val * Math.log(val)));
        }).toArray(String[]::new));
```
В словарь `table_data` добавляется: ключ `(e^x)/(x * lg(x))` и значение: массив, который формируется след. образом:
1) `values.stream()` возвращает экземпляр `Stream` - коллекцию списка
2) `.map(...)` - метод вхождения, который будет выполнен для каждого элемента коллекции, например в данном случае выполнится код анонимной функции
3) `.toArray(String[]::new)` преобразует стрим в списокую модель. Мы передаем конструктор массива строк => ожидаем его получить.

В итоге получаем объект типа `String[]` как значение коюча `(e^x)/(x * lg(x))`.


### Задание 2
Реализовать алгоритм нахождения наибольшего
отрицательного элемента двумерного массива, содержащего
произвольное число строк и столбцов и, быть может, различное число
элементов в каждой строке.

```java
public class Task2 {

    public static void main(String[] args)
    {
        int[][] arr = { { 1, 2, -3, -1 }, { 4, -5, 6, -7 }, { 8, -9, 10 } };
        int maxNegative = Integer.MIN_VALUE;

        for (int[] ints : arr) {
            for (int anInt : ints) {
                if (maxNegative < anInt && anInt < 0) {
                    maxNegative = anInt;
                }
            }
        }

        if (maxNegative != Integer.MIN_VALUE) {
            System.out.println("Наибольший отрицательный элемент: " + maxNegative);
        } else {
            System.out.println("Отрицательные элементы отсутствуют в массиве");
        }
    }
}

```

##### Вывод
```bash
Наибольший отрицательный элемент: -1

```


### Задание 3
Написать приложение для реализации следующего
алгоритма: упорядочить по возрастанию элементы каждой строки
матрицы 3x3. Вывести матрицу на экран до сортировки и после.

```java
import java.util.Arrays;

public class Task3 {

    private static void printMatrix(int[][] matrix) {
        for (int[] ints : matrix) {
            System.out.println(Arrays.toString(ints));
        }
    }

    public static void main(String[] args) {
        int[][] matrix = {
                {1, 4, 3},
                {5, 6, 7},
                {9, -1, 1},
        };

        System.out.println("Исходная матрица:");
        printMatrix(matrix);

        for (int[] ints : matrix) {
            Arrays.sort(ints);
        }

        System.out.println("\nМатрица после сортировки:");
        printMatrix(matrix);

    }
}

```

##### Вывод
```bash
Исходная матрица:
[1, 4, 3]
[5, 6, 7]
[9, -1, 1]

Матрица после сортировки:
[1, 3, 4]
[5, 6, 7]
[-1, 1, 9]

```


### Задание 4
Создать класс с методом, реализующим следующий
алгоритм: на плоскости размещены две окружности, центр первой в
координатах *x1*, *y1* и ее радиус *r1*, а центр и радиус другой в *x2*, *y2*, *r2*,
конкретные значения передаются методу через его аргументы или
хранятся в полях класса. Определить пересекаются ли окружности в
одной точке (касаются), в двух точках, совпадают, не пересекаются и ни
одна из окружностей не является вложенной в другую, вторая
окружность вложена в первую, первая вложена во вторую. Метод
должен возвращать целое значение, соответствующее возникшей
ситуации. Протестировать работу метода, запрашивая у пользователя
(ввод с клавиатуры) параметры окружностей и выводя сообщение о их
взаимоположении.
Java поддерживает перечислимые типы. В определении типа задаются имена,
которые и являются допустимыми значениями для переменных этого типа.
Значения перечислимого типа можно сравнивать друг с другом. Например,

```java
enum MyColor { White, Black, Red, Green, Blue }
public class Test {
    public static void main(String[] args) {
        MyColor c = MyColor.Red;
        System.out.println( c ); // Red
        System.out.println( MyColor.Green ); // Green

        if(c == MyColor.Red)
            System.out.println("Red color");
        else System.out.println("Unknown color");
    }
}
```

##### Решение
```java
import java.util.Scanner;

public class Task4 {

    private static class Circle {
        public int x;
        public int y;
        public int radius;

        public Circle(int x, int y, int radius) {
            this.x = x;
            this.y = y;
            this.radius = radius;
        }
    }

    private static int DefineResult(Circle circle1, Circle circle2){
        double distance = Math.sqrt(Math.pow(circle1.x - circle2.x, 2) + Math.pow(circle1.y - circle2.y, 2));
        if (distance == 0 && circle1.radius == circle2.radius){
            return 1;
        } else if (distance == circle1.radius + circle2.radius){
            return 2;
        } else if (distance < circle1.radius + circle2.radius){
            return 3;
        } else if (distance + circle1.radius == circle2.radius){
            return 4;
        } else if (distance + circle2.radius == circle1.radius){
            return 5;
        } else
            return 6;
    }

    public static void main(String[] args) {
        int x;
        int y;
        int radius;

        Scanner scanner = new Scanner(System.in);
        System.out.println("Введите координаты первой окружности: ");
        System.out.print("x: ");
        x = scanner.nextInt();
        System.out.print("y: ");
        y = scanner.nextInt();
        System.out.print("Радиус: ");
        radius = scanner.nextInt();
        Circle circle1 = new Circle(x, y, radius);

        System.out.println("\nВведите координаты второй окружности: ");
        System.out.print("x: ");
        x = scanner.nextInt();
        System.out.print("y: ");
        y = scanner.nextInt();
        System.out.print("Радиус: ");
        radius = scanner.nextInt();
        Circle circle2 = new Circle(x, y, radius);


        int result = DefineResult(circle1, circle2);

        switch (result) {
            case 2 -> System.out.println("Касаются");
            case 3 -> System.out.println("Пересекаются в двух точках");
            case 1 -> System.out.println("Совпадают");
            case 6 -> System.out.println("Не пересекаются");
            case 4 -> System.out.println("Первая окружность вложена во вторую");
            case 5 -> System.out.println("Вторая окружность вложена в первую");
            default -> System.out.println("Неизвестный результат");
        }
    }
}


```

##### Вывод
```bash
Введите координаты первой окружности: 
x: 0
y: 0
Радиус: 5

Введите координаты второй окружности: 
x: 0
y: 0
Радиус: 5
Совпадают

```


### Задание 5
Измените класс из предыдущего задания так, чтобы в нем
был определен подходящий перечислимый тип, и чтобы метод
возвращал значение этого перечислимого типа.

```java
import java.util.Scanner;

public class Task5 {

    private static class Circle {
        public int x;
        public int y;
        public int radius;

        public Circle(int x, int y, int radius) {
            this.x = x;
            this.y = y;
            this.radius = radius;
        }
    }

    enum ResultType {
        TOUCHING,  // касаются
        INTERSECTING_AT_TWO_POINTS,  // пересекаются в двух точках
        COINCIDENT,  // совпадают
        NON_INTERSECTING,  // не пересекаются
        FIRST_CIRCLE_EMBEDDED,  // первая окружность вложена во вторую
        SECOND_CIRCLE_EMBEDDED  // вторая окружность вложена в первую
    }

    private static ResultType DefineResult(Circle circle1, Circle circle2){
        double distance = Math.sqrt(Math.pow(circle1.x - circle2.x, 2) + Math.pow(circle1.y - circle2.y, 2));
        if (distance == 0 && circle1.radius == circle2.radius){
            return ResultType.COINCIDENT;
        } else if (distance == circle1.radius + circle2.radius){
            return ResultType.TOUCHING;
        } else if (distance < circle1.radius + circle2.radius){
            return ResultType.INTERSECTING_AT_TWO_POINTS;
        } else if (distance + circle1.radius == circle2.radius){
            return ResultType.FIRST_CIRCLE_EMBEDDED;
        } else if (distance + circle2.radius == circle1.radius){
            return ResultType.SECOND_CIRCLE_EMBEDDED;
        } else
            return ResultType.NON_INTERSECTING;
    }

    public static void main(String[] args) {
        int x;
        int y;
        int radius;

        Scanner scanner = new Scanner(System.in);
        System.out.println("Введите координаты первой окружности: ");
        System.out.print("x: ");
        x = scanner.nextInt();
        System.out.print("y: ");
        y = scanner.nextInt();
        System.out.print("Радиус: ");
        radius = scanner.nextInt();
        Circle circle1 = new Circle(x, y, radius);

        System.out.println("\nВведите координаты второй окружности: ");
        System.out.print("x: ");
        x = scanner.nextInt();
        System.out.print("y: ");
        y = scanner.nextInt();
        System.out.print("Радиус: ");
        radius = scanner.nextInt();
        Circle circle2 = new Circle(x, y, radius);


        ResultType result = DefineResult(circle1, circle2);
        
        switch (result) {
            case TOUCHING -> System.out.println("Касаются");
            case INTERSECTING_AT_TWO_POINTS -> System.out.println("Пересекаются в двух точках");
            case COINCIDENT -> System.out.println("Совпадают");
            case NON_INTERSECTING -> System.out.println("Не пересекаются");
            case FIRST_CIRCLE_EMBEDDED -> System.out.println("Первая окружность вложена во вторую");
            case SECOND_CIRCLE_EMBEDDED -> System.out.println("Вторая окружность вложена в первую");
            default -> System.out.println("Неизвестный результат");
        }
    }
}
```

##### Вывод
```bash
Введите координаты первой окружности: 
x: 0
y: 0
Радиус: 5

Введите координаты второй окружности: 
x: 0
y: 0
Радиус: 5
Совпадают
```


### Задание 6
Написать метод, реализующий алгоритм численного
интегрирования левыми прямоугольниками. В программе-тесте
протабулировать функцию `y(x) = e^x - x^3` на интервале, который задает
пользователь (например, от 0 до 4) с постоянным шагом так, чтобы
получить значения аргумента x и функции y в *101* точке (два массива).
Передать массивы методу интегрирования. Посчитать значение
интеграла аналитически и сравнить результат, возвращаемый методом
с точным значением.

```java

```

##### Вывод
```bash

```


### Задание 7
Написать приложение для преобразования целого числа из
десятичного представления в представление в заданной системе
счисления (от 2 до 8 включительно). Для проверки правильности работы
программы используйте методы классов-оболочек `toString(value, base)`,
где `value` - преобразуемое десятичное значение, base - основание
системы счисления, в которую нужно переводить `value`. 
Например,`String val3 = Integer.toString( 12, 3 ); // 12` 
в троичную систему счисления
(в своем алгоритме использовать `toString` нельзя!).

```java

```

##### Вывод
```bash

```


### Задание 8
Реализуйте вычисление значения полинома n-й степени по
схеме Горнера. 
Суть схемы в том, что запись полинома

`P(x) = an * x`

`n + an-1 * x`

`n-1 + … + a0` преобразуется в

`P(x) = (…((an* x + an-1) * x + an-2) * x + …) * x + a0`

Полином в программе представляется массивом его коэффициентов
(массив из n+1 элемента), а сами вычисления выполняются в цикле так,
что в начале `P = an * x + an-1`, а затем на каждой итерации `P = P * x + ai`.

```java

```

##### Вывод
```bash

```


### Задание 9
Сформируйте регулярное выражение для проверки того,
содержит ли заданная строка представление одиннадцатизначного
(федерального) телефонного номера российского оператора связи. В
начале должны быть либо символы +7, либо 8. Кроме того, номер может
содержать в определенных позициях пробелы, дефисы, круглые скобки.
Например, правильными будут следующие варианты

```
+79043781661      +7 904 378 1661 +7 904 378 16 61
+7-904-378-16-61  +7(904)3781661  +7(904) 378-16 61
89043781661       8 904 378-16-61
```
Круглыми скобками могут быть выделены только три цифры после
префикса +7 или 8. Пробелы могут быть после префикса, после трех
цифр, следующих за префиксом, после следующих трех цифр, после
следующих двух цифр. На тех же позициях могут встречаться дефисы.
Проверьте корректность полученного регулярного выражения на
приведенных выше примерах и для других вариантов (допустимых и не
допустимых) представления телефонных номеров.
2) Сформируйте регулярное выражение для проверки того, содержится
ли где-то в заданной строке представление семизначного
(муниципального) телефонного номера Ростова-на-Дону. Номер
должен начинаться с цифры 2 или 3, эта цифра может быть отделена
от остальной части номера пробелом или дефисом (а может быть и не
отделена ничем), далее каждая из трех пар цифр также может
отделяться пробелом или дефисом. Например, содержимое следующей
строки должно соответствовать построенному регулярному выражению
"Мои номера `220-30-40` и `8904-378-16-61` не считая служебных"

```java

```

##### Вывод
```bash

```



### Задание 10
Используйте регулярные выражения из предыдущего
задания для того, чтобы извлечь из строки
"Мои номера `220-30-40` и `8904-378-16-61` не считая служебных"
все содержащиеся в ней номера телефонов.

```java

```

##### Вывод
```bash

```

*Авторство: **Бояршинов Н.О***