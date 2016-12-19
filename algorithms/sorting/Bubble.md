## Введение
Самый простой вариант сортировки массива.
В данном случае мы используем два вложенных цикла.

### Алгоритм
Идея заключается в том, что мы во внутреннем цикле идем по массиву и как бы "поднимаем" наибольший элемент вверх.
После этого мы еще раз идем по массиву, но уже не до конца, так как в конце уже самый большой элемент и так.
И так мы делаем n раз, где n - длина массива.
Т.е у нас n итераций получается за каждую из которых мы "поднимаем" элементы вверх.
Получается, что у нас элементы массива наибольшие всплывают, как пузырьки.

### Производительность
Сортировка пузырьком крайне медленная, сложность -  `О(n^2)`, из-за двух вложенных циклов.

Мы можем улучшить ее. так как если у нас, например, массив почти отсортирован, то нам нет смысла ждать все n итераций. Для этого можно завести счетчик swap-ов(или обычный boolean флаг), т.е счетчик перестановок - который будет сигнализировать о том, были ли во время итерации "всплытия", если нет, то массив уже отсортирован, а значит можно выходить из цикла.

### Реализации
Ниже представлены две реализации с массивом int-ов.
Для чуть лучшей производительности можно ввести флаг, сигнализирующий о том, что
массив уже отсортирован, это поможет нам сократить кол-во итераций
в массивах типа: [1, 2, 5, 4, 6, 7].
```java
public class BubbleSort {
    /**
     * Simple bubble sort example.
     * @param arr array with elements.
     */
    public static void bubbleSort(int[] arr) {
        int len = arr.length;
        for (int i = 0; i < len - 1; i++) {
            for (int j = 0; j < len - i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    int tmp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = tmp;
                }
            }
        }
    }

    /**
     * Simple bubble sort, but with flag for performance optimization.
     * @param arr array with elements.
     */
    public static void bubbleSortWithFlag(int[] arr) {
        final int len = arr.length;
        boolean flag;

        for (int i = 0; i < len - 1; i++) {
            flag = false;
            for (int j = 0; j < len - i - 1; j++) {
                if (arr[j] > arr[j + 1]) {
                    int tmp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = tmp;
                    flag = true;
                }
            }
            if (!flag) break;
        }
    }
}

```