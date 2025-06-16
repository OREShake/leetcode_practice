## 1. [88. Слияние отсортированных массивов](https://leetcode.com/problems/merge-sorted-array/)

### Условие задачи

Даны два отсортированных массива целых чисел `nums1` и `nums2` длиной `m` и `n` соответственно, а также массив `nums1` с дополнительным местом для размещения всех элементов. Слейте `nums2` в `nums1`, чтобы получить отсортированный массив. Результат должен быть сохранен в `nums1`.

**Примеры:**

```
Пример 1:
Вход: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Выход: [1,2,2,3,5,6]
Объяснение: Сливаем массивы, сохраняя сортировку.

Пример 2:
Вход: nums1 = [1], m = 1, nums2 = [], n = 0
Выход: [1]
```

**Ограничения:**
- `nums1.length == m + n`
- `nums2.length == n`
- `0 <= m, n <= 200`
- `-10^9 <= nums1[i], nums2[i] <= 10^9`

### Решения

#### 1. Простой подход с двумя указателями (Naive Two-Pointer Approach)

**Идея:** Используем временный список для хранения объединенных элементов, сравнивая элементы из `nums1` и `nums2`.

**Интуиция:** Два указателя позволяют сохранять порядок, но требуется дополнительная память.

```python
def merge(nums1, m, nums2, n):
    merged = []
    i, j = 0, 0

    while i < m and j < n:
        if nums1[i] < nums2[j]:
            merged.append(nums1[i])
            i += 1
        else:
            merged.append(nums2[j])
            j += 1

    while i < m:
        merged.append(nums1[i])
        i += 1

    while j < n:
        merged.append(nums2[j])
        j += 1

    for k in range(m + n):
        nums1[k] = merged[k]
```

**Временная сложность:** O(m + n)  
- Один проход по обоим массивам.

**Пространственная сложность:** O(m + n)  
- Для временного списка.

#### 2. Слияние на месте с дополнительной памятью (In-place Merge with Extra Storage)

**Идея:** Копируем элементы `nums1` во временный массив и сливаем его с `nums2` обратно в `nums1`.

**Интуиция:** Уменьшаем дополнительную память по сравнению с первым подходом.

```python
def merge(nums1, m, nums2, n):
    nums1_copy = nums1[:m]
    p1, p2, k = 0, 0, 0

    while p1 < m and p2 < n:
        if nums1_copy[p1] < nums2[p2]:
            nums1[k] = nums1_copy[p1]
            p1 += 1
        else:
            nums1[k] = nums2[p2]
            p2 += 1
        k += 1

    while p1 < m:
        nums1[k] = nums1_copy[p1]
        p1 += 1
        k += 1

    while p2 < n:
        nums1[k] = nums2[p2]
        p2 += 1
        k += 1
```

**Временная сложность:** O(m + n)  
- Один проход по массивам.

**Пространственная сложность:** O(m)  
- Для копии первых `m` элементов `nums1`.

#### 3. Оптимальное слияние на месте с конца (Optimal In-place Merge from the End)

**Идея:** Сливаем массивы с конца, используя свободное место в `nums1`, чтобы избежать дополнительной памяти.

**Интуиция:** Заполнение с конца позволяет размещать большие элементы без перезаписи.

```python
def merge(nums1, m, nums2, n):
    p1, p2, end = m - 1, n - 1, m + n - 1

    while p1 >= 0 and p2 >= 0:
        if nums1[p1] > nums2[p2]:
            nums1[end] = nums1[p1]
            p1 -= 1
        else:
            nums1[end] = nums2[p2]
            p2 -= 1
        end -= 1

    while p2 >= 0:
        nums1[end] = nums2[p2]
        p2 -= 1
        end -= 1
```

**Временная сложность:** O(m + n)  
- Один проход по массивам.

**Пространственная сложность:** O(1)  
- Без дополнительной памяти.

---
## 2. [167. Две суммы II - Входной массив отсортирован](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

### Условие задачи

Дан отсортированный по неубыванию массив целых чисел `numbers` с индексацией с 1. Найдите два числа, сумма которых равна заданному `target`. Верните их индексы `[index1, index2]` (с 1), где `index1 < index2`. Гарантируется ровно одно решение.

**Примеры:**

```
Пример 1:
Вход: numbers = [2,7,11,15], target = 9
Выход: [1,2]
Объяснение: 2 + 7 = 9, индексы 0 и 1 (с 1: [1,2]).

Пример 2:
Вход: numbers = [2,3,4], target = 6
Выход: [1,3]
```

**Ограничения:**
- `2 <= numbers.length <= 3 * 10^4`
- `-1000 <= numbers[i] <= 1000`
- `-1000 <= target <= 1000`
- Массив отсортирован по неубыванию.
- Гарантируется ровно одно решение.

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем все возможные пары чисел, суммируя их и сравнивая с `target`.

**Интуиция:** Простой, но неэффективный подход из-за квадратичной сложности.

```python
def twoSum(numbers: List[int], target: int) -> List[int]:
    n = len(numbers)
    for i in range(n):
        for j in range(i + 1, n):
            if numbers[i] + numbers[j] == target:
                return [i + 1, j + 1]
```

**Временная сложность:** O(n²)  
- Проверка всех пар.

**Пространственная сложность:** O(1)  
- Без дополнительной памяти.

#### 2. Два указателя (Two Pointers)

**Идея:** Используем два указателя, начиная с начала и конца массива, и двигаем их в зависимости от суммы.

**Интуиция:** Сортировка массива позволяет эффективно искать пару чисел.

```python
def twoSum(numbers: List[int], target: int) -> List[int]:
    left, right = 0, len(numbers) - 1
    
    while left < right:
        current_sum = numbers[left] + numbers[right]
        
        if current_sum == target:
            return [left + 1, right + 1]
        elif current_sum < target:
            left += 1
        else:
            right -= 1
```

**Временная сложность:** O(n)  
- Один проход по массиву.

**Пространственная сложность:** O(1)  
- Только указатели.

#### 3. Бинарный поиск (Binary Search)

**Идея:** Для каждого элемента ищем дополнение до `target` с помощью бинарного поиска.

**Интуиция:** Сортировка массива позволяет использовать бинарный поиск для ускорения.

```python
def twoSum(numbers: List[int], target: int) -> List[int]:
    for i in range(len(numbers)):
        complement = target - numbers[i]
        left, right = i + 1, len(numbers) - 1
        
        while left <= right:
            mid = (left + right) // 2
            if numbers[mid] == complement:
                return [i + 1, mid + 1]
            elif numbers[mid] < complement:
                left = mid + 1
            else:
                right = mid - 1
```

**Временная сложность:** O(n * log n)  
- Бинарный поиск для каждого элемента.

**Пространственная сложность:** O(1)  
- Без дополнительной памяти.

---
## 3. [11. Контейнер с наибольшим количеством воды](https://leetcode.com/problems/container-with-most-water/)

### Условие задачи

Дан массив `height` длиной `n`, где `height[i]` — высота линии на позиции `i`. Найдите две линии, которые вместе с осью X образуют контейнер, содержащий наибольшее количество воды. Верните максимальную площадь контейнера.

**Примеры:**

```
Пример 1:
Вход: height = [1,8,6,2,5,4,8,3,7]
Выход: 49
Объяснение: Линии на позициях 1 (высота 8) и 8 (высота 7) образуют контейнер с площадью min(8,7) * (8-1) = 49.

Пример 2:
Вход: height = [1,1]
Выход: 1
```

**Ограничения:**
- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем все возможные пары линий, вычисляя площадь контейнера.

**Интуиция:** Простой подход, но неэффективный из-за квадратичной сложности.

```python
def maxArea(height):
    max_area = 0
    n = len(height)
    
    for i in range(n):
        for j in range(i + 1, n):
            area = min(height[i], height[j]) * (j - i)
            max_area = max(max_area, area)
    
    return max_area
```

**Временная сложность:** O(n²)  
- Проверка всех пар линий.

**Пространственная сложность:** O(1)  
- Без дополнительной памяти.

#### 2. Два указателя (Two Pointers)

**Идея:** Используем два указателя, начиная с краев массива, и двигаем указатель с меньшей высотой, чтобы максимизировать площадь.

**Интуиция:** Движение меньшей линии может увеличить площадь за счет потенциально большей высоты.

```python
def maxArea(height):
    left, right = 0, len(height) - 1
    max_area = 0
    
    while left < right:
        width = right - left
        area = min(height[left], height[right]) * width
        max_area = max(max_area, area)
        
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
        
    return max_area
```

**Временная сложность:** O(n)  
- Один проход по массиву.

**Пространственная сложность:** O(1)  
- Только указатели.

---
## 4. [15. Три суммы](https://leetcode.com/problems/3sum/)

### Условие задачи

Дан массив целых чисел `nums`, найдите все уникальные тройки `[nums[i], nums[j], nums[k]]`, такие что `nums[i] + nums[j] + nums[k] == 0` и `i != j != k`. Верните список всех таких троек.

**Примеры:**

```
Пример 1:
Вход: nums = [-1,0,1,2,-1,-4]
Выход: [[-1,-1,2],[-1,0,1]]
Объяснение: Тройки, сумма которых равна 0: [-1,0,1] и [-1,-1,2].

Пример 2:
Вход: nums = []
Выход: []
```

**Ограничения:**
- `0 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем все возможные тройки чисел и добавляем те, чья сумма равна 0, в множество для уникальности.

**Интуиция:** Простой, но крайне неэффективный подход из-за кубической сложности.

```python
def threeSum(nums):
    nums.sort()
    triplets = set()

    for i in range(len(nums) - 2):
        for j in range(i + 1, len(nums) - 1):
            for k in range(j + 1, len(nums)):
                if nums[i] + nums[j] + nums[k] == 0:
                    triplet = (nums[i], nums[j], nums[k])
                    triplets.add(triplet)

    return list(triplets)
```

**Временная сложность:** O(n³)  
- Проверка всех троек.

**Пространственная сложность:** O(m)  
- m — количество уникальных троек.

#### 2. Сортировка и два указателя (Sorting and Two-Pointer Technique)

**Идея:** Сортируем массив, фиксируем одно число и используем два указателя для поиска оставшейся пары.

**Интуиция:** Сортировка позволяет избежать дубликатов и эффективно искать пары.

```python
def threeSum(nums):
    nums.sort()
    triplets = []

    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        
        left, right = i + 1, len(nums) - 1
        while left < right:
            current_sum = nums[i] + nums[left] + nums[right]
            
            if current_sum == 0:
                triplets.append([nums[i], nums[left], nums[right]])
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
            elif current_sum < 0:
                left += 1
            else:
                right -= 1

    return triplets
```

**Временная сложность:** O(n²)  
- Сортировка O(n * log n) и два указателя O(n²).

**Пространственная сложность:** O(m)  
- m — количество уникальных троек.