## 1. [3. Наидлиннейшая подстрока без повторяющихся символов](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

### Условие задачи

Дана строка `s`, найдите длину наидлиннейшей подстроки без повторяющихся символов.

**Примеры:**

```
Пример 1:
Вход: s = "abcabcbb"
Выход: 3
Объяснение: Подстрока "abc" — наидлиннейшая без повторений, длина 3.

Пример 2:
Вход: s = "bbbbb"
Выход: 1
Объяснение: Подстрока "b" — наидлиннейшая, длина 1.

Пример 3:
Вход: s = "pwwkew"
Выход: 3
Объяснение: Подстрока "wke" — наидлиннейшая, длина 3.
```

**Ограничения:**
- `0 <= s.length <= 5 * 10^4`
- `s` содержит английские буквы, цифры, символы и пробелы.

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем каждую возможную подстроку на наличие уникальных символов.

**Интуиция:** Простой подход, но неэффективный из-за кубической сложности.

```python
def lengthOfLongestSubstring(s: str) -> int:
    n = len(s)
    max_length = 0
    
    for i in range(n):
        seen_chars = set()
        current_length = 0
        
        for j in range(i, n):
            if s[j] in seen_chars:
                break
            seen_chars.add(s[j])
            current_length += 1
        
        max_length = max(max_length, current_length)
    
    return max_length
```

**Временная сложность:** O(n³)  
- Генерация подстрок O(n²), проверка уникальности O(n).

**Пространственная сложность:** O(min(n, m))  
- m — размер алфавита (для множества).

#### 2. Скользящее окно с множеством (Sliding Window with Set)

**Идея:** Используем скользящее окно и множество для отслеживания уникальных символов.

**Интуиция:** При обнаружении дубликата сужаем окно, удаляя символы с начала.

```python
def lengthOfLongestSubstring(s: str) -> int:
    n = len(s)
    max_length = 0
    seen_chars = set()
    i = 0
    
    for j in range(n):
        while s[j] in seen_chars:
            seen_chars.remove(s[i])
            i += 1
        seen_chars.add(s[j])
        max_length = max(max_length, j - i + 1)
    
    return max_length
```

**Временная сложность:** O(n)  
- Каждый символ добавляется и удаляется не более одного раза.

**Пространственная сложность:** O(min(n, m))  
- Для множества символов.

#### 3. Оптимизированное скользящее окно с хэш-таблицей (Sliding Window with HashMap)

**Идея:** Используем словарь для хранения последнего индекса каждого символа, чтобы сразу перемещать начало окна при дубликате.

**Интуиция:** Прямой переход к следующему индексу после дубликата ускоряет процесс.

```python
def lengthOfLongestSubstring(s: str) -> int:
    char_index_map = {}
    max_length = 0
    i = 0
    
    for j, char in enumerate(s):
        if char in char_index_map:
            i = max(char_index_map[char] + 1, i)
        max_length = max(max_length, j - i + 1)
        char_index_map[char] = j
    
    return max_length
```

**Временная сложность:** O(n)  
- Один проход по строке.

**Пространственная сложность:** O(min(n, m))  
- Для словаря.

---
## 2. [424. Наидлиннейшая подстрока с заменой символов](https://leetcode.com/problems/longest-repeating-character-replacement/)

### Условие задачи

Дана строка `s` и целое число `k`. Вы можете заменить до `k` символов в строке на любой другой символ. Найдите длину наидлиннейшей подстроки, содержащей одинаковые символы после замен.

**Примеры:**

```
Пример 1:
Вход: s = "ABAB", k = 2
Выход: 4
Объяснение: Заменяем два 'B' на 'A' или наоборот, получая "AAAA" или "BBBB".

Пример 2:
Вход: s = "AABABBA", k = 1
Выход: 4
Объяснение: Заменяем один 'A' на 'B' в подстроке "ABBA", получая "BBBB".
```

**Ограничения:**
- `1 <= s.length <= 10^5`
- `s` содержит только заглавные английские буквы.
- `0 <= k <= s.length`

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем каждую подстроку, подсчитываем частоты символов и проверяем, можно ли сделать её однородной с `k` заменами.

**Интуиция:** Простой, но крайне неэффективный подход.

```python
def characterReplacement(s: str, k: int) -> int:
    max_length = 0
    n = len(s)
    
    for i in range(n):
        freq = {}
        for j in range(i, n):
            freq[s[j]] = freq.get(s[j], 0) + 1
            max_freq = max(freq.values())
            if (j - i + 1) - max_freq <= k:
                max_length = max(max_length, j - i + 1)
    
    return max_length
```

**Временная сложность:** O(n³)  
- Генерация подстрок O(n²), подсчет частот O(n).

**Пространственная сложность:** O(1)  
- Словарь ограничен 26 буквами.

#### 2. Скользящее окно с фиксированным размером (Sliding Window with Fixed Window Size)

**Идея:** Проверяем окна фиксированного размера, подсчитывая, можно ли сделать их однородными с `k` заменами.

**Интуиция:** Уменьшает сложность, но всё ещё не оптимально.

```python
def characterReplacement(s: str, k: int) -> int:
    max_length = 0
    n = len(s)
    
    for window_size in range(1, n + 1):
        max_freq = 0
        freq = {}
        for i in range(n):
            freq[s[i]] = freq.get(s[i], 0) + 1
            if i - window_size >= 0:
                freq[s[i - window_size]] -= 1
                if freq[s[i - window_size]] == 0:
                    del freq[s[i - window_size]]
            max_freq = max(max_freq, freq.get(s[i], 0))
            if (window_size - max_freq) <= k:
                max_length = max(max_length, window_size)
    
    return max_length
```

**Временная сложность:** O(n²)  
- Проверка всех размеров окон.

**Пространственная сложность:** O(1)  
- Словарь ограничен 26 буквами.

#### 3. Оптимизированное скользящее окно (Optimal Sliding Window)

**Идея:** Динамически расширяем окно, пока количество замен не превышает `k`, и обновляем максимальную длину.

**Интуиция:** Отслеживание максимальной частоты символа позволяет эффективно управлять окном.

```python
def characterReplacement(s: str, k: int) -> int:
    max_length = 0
    max_freq = 0
    freq = {}
    left = 0
    
    for right in range(len(s)):
        char = s[right]
        freq[char] = freq.get(char, 0) + 1
        max_freq = max(max_freq, freq[char])
        
        while (right - left + 1) - max_freq > k:
            freq[s[left]] -= 1
            left += 1
        
        max_length = max(max_length, right - left + 1)
        
    return max_length
```

**Временная сложность:** O(n)  
- Каждый символ обрабатывается не более двух раз.

**Пространственная сложность:** O(1)  
- Словарь ограничен 26 буквами.

---
## 3. [209. Минимальный размер подмассива с заданной суммой](https://leetcode.com/problems/minimum-size-subarray-sum/)

### Условие задачи

Дан массив положительных целых чисел `nums` и целое число `target`, найдите минимальную длину подмассива, сумма которого не менее `target`. Если такого подмассива нет, верните 0.

**Примеры:**

```
Пример 1:
Вход: target = 7, nums = [2,3,1,2,4,3]
Выход: 2
Объяснение: Подмассив [4,3] имеет сумму 7 и минимальную длину 2.

Пример 2:
Вход: target = 4, nums = [1,4,4]
Выход: 1
Объяснение: Подмассив [4] имеет сумму 4 и длину 1.
```

**Ограничения:**
- `1 <= target <= 10^9`
- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^4`

### Решения

#### 1. Полный перебор (Brute-force Approach)

**Идея:** Проверяем каждый возможный подмассив, подсчитывая сумму, и обновляем минимальную длину, если сумма не менее `target`.

**Интуиция:** Простой, но неэффективный подход.

```python
def minSubArrayLen(target, nums):
    n = len(nums)
    min_length = float('inf')

    for start in range(n):
        sum = 0
        for end in range(start, n):
            sum += nums[end]
            if sum >= target:
                min_length = min(min_length, end - start + 1)
                break
    
    return 0 if min_length == float('inf') else min_length
```

**Временная сложность:** O(n²)  
- Генерация подмассивов O(n²).

**Пространственная сложность:** O(1)  
- Только переменные.

#### 2. Скользящее окно (Sliding Window Approach)

**Идея:** Используем скользящее окно, расширяя его до достижения суммы не менее `target`, затем сужая для минимизации длины.

**Интуиция:** Динамическое управление окном минимизирует вычисления.

```python
def minSubArrayLen(target, nums):
    n = len(nums)
    min_length = float('inf')
    left = 0
    current_sum = 0

    for right in range(n):
        current_sum += nums[right]

        while current_sum >= target:
            min_length = min(min_length, right - left + 1)
            current_sum -= nums[left]
            left += 1
    
    return 0 if min_length == float('inf') else min_length
```

**Временная сложность:** O(n)  
- Каждый элемент добавляется и удаляется не более одного раза.

**Пространственная сложность:** O(1)  
- Только переменные.

---
## 4. [1004. Максимум последовательных единиц III](https://leetcode.com/problems/max-consecutive-ones-iii/)

### Условие задачи

Дан бинарный массив `A` и целое число `K`. Вы можете заменить до `K` нулей на единицы. Найдите максимальную длину подмассива, содержащего только единицы после замен.

**Примеры:**

```
Пример 1:
Вход: A = [1,1,1,0,0,0,1,1,1,1,0], K = 2
Выход: 6
Объяснение: Заменяем два нуля в [1,1,1,0,0,1,1,1,1] на единицы, получая [1,1,1,1,1,1].

Пример 2:
Вход: A = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], K = 3
Выход: 10
```

**Ограничения:**
- `1 <= A.length <= 10^5`
- `A[i]` равно 0 или 1.
- `0 <= K <= A.length`

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем каждый подмассив, подсчитывая количество нулей, и обновляем максимальную длину, если нулей не более `K`.

**Интуиция:** Простой, но неэффективный подход.

```python
def longestOnes(A, K):
    max_consecutive_ones = 0
    n = len(A)
    
    for start in range(n):
        zeros_count = 0
        for end in range(start, n):
            if A[end] == 0:
                zeros_count += 1
            if zeros_count <= K:
                max_consecutive_ones = max(max_consecutive_ones, end - start + 1)
            else:
                break

    return max_consecutive_ones
```

**Временная сложность:** O(n²)  
- Генерация подмассивов O(n²).

**Пространственная сложность:** O(1)  
- Только переменные.

#### 2. Скользящее окно (Sliding Window)

**Идея:** Используем скользящее окно, расширяя его и уменьшая `K` при встрече нулей, сужая окно, если `K` становится отрицательным.

**Интуиция:** Управление количеством нулей позволяет найти максимальное окно.

```python
def longestOnes(A, K):
    left = 0
    for right in range(len(A)):
        if A[right] == 0:
            K -= 1
        if K < 0:
            if A[left] == 0:
                K += 1
            left += 1
            
    return right - left + 1
```

**Временная сложность:** O(n)  
- Один проход по массиву.

**Пространственная сложность:** O(1)  
- Только переменные.

#### 3. Оптимизированное скользящее окно (Optimized Sliding Window)

**Идея:** Отслеживаем количество нулей в окне, сужая окно только при превышении `K`.

**Интуиция:** Явное отслеживание нулей упрощает логику.

```python
def longestOnes(A, K):
    left = right = 0
    zeros_count = 0

    while right < len(A):
        if A[right] == 0:
            zeros_count += 1
        
        if zeros_count > K:
            if A[left] == 0:
                zeros_count -= 1
            left += 1

        right += 1

    return right - left
```

**Временная сложность:** O(n)  
- Каждый элемент обрабатывается не более двух раз.

**Пространственная сложность:** O(1)  
- Только переменные.