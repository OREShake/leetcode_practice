## 1. [392. Является ли подпоследовательностью](https://leetcode.com/problems/is-subsequence/)

### Условие задачи

Даны две строки `s` и `t`, определите, является ли `s` подпоследовательностью `t`. Строка является подпоследовательностью другой, если все её символы можно найти в той же последовательности в другой строке, не обязательно подряд.

**Примеры:**

```
Пример 1:
Вход: s = "abc", t = "ahbgdc"
Выход: true
Объяснение: Символы 'a', 'b', 'c' появляются в строке t в правильном порядке.

Пример 2:
Вход: s = "axc", t = "ahbgdc"
Выход: false
Объяснение: Символ 'x' отсутствует в t, поэтому s не является подпоследовательностью.

Пример 3:
Вход: s = "", t = "ahbgdc"
Выход: true
Объяснение: Пустая строка является подпоследовательностью любой строки.
```

**Ограничения:**
- `0 <= s.length <= 100`
- `0 <= t.length <= 10^4`
- Строки состоят только из строчных букв.

**Дополнительное требование:**
- Можно ли оптимизировать решение для множества запросов `s` на одной строке `t`?

### Решения

#### 1. Два указателя (Two Pointer Approach)

**Идея:** Используем два указателя: один для строки `s`, другой для строки `t`. Если символы совпадают, продвигаем указатель `s`. Указатель `t` движется всегда.

**Интуиция:** Если мы можем пройти всю строку `s`, находя её символы в `t` в правильном порядке, то `s` — подпоследовательность.

```python
def isSubsequence(s: str, t: str) -> bool:
    pointer_s, pointer_t = 0, 0
    
    while pointer_s < len(s) and pointer_t < len(t):
        if s[pointer_s] == t[pointer_t]:
            pointer_s += 1
        pointer_t += 1
    
    return pointer_s == len(s)
```

**Временная сложность:** O(n)  
- n — длина строки `t`, так как проходим по ней один раз.

**Пространственная сложность:** O(1)  
- Используются только два указателя.

#### 2. Бинарный поиск для множества запросов (Follow-up: Binary Search)

**Идея:** Для множества запросов создаем словарь с индексами символов в `t` и используем бинарный поиск для нахождения следующего подходящего индекса.

**Интуиция:** Предобработка `t` позволяет быстро находить индексы символов, что ускоряет проверку для множества строк `s`.

```python
from bisect import bisect_left
from collections import defaultdict

def isSubsequence(s: str, t: str) -> bool:
    char_indices = defaultdict(list)
    for index, char in enumerate(t):
        char_indices[char].append(index)
    
    prev_index = -1
    for char in s:
        indices = char_indices[char]
        position = bisect_left(indices, prev_index + 1)
        if position == len(indices):
            return False
        prev_index = indices[position]
    
    return True
```

**Временная сложность:**  
- Предобработка: O(n), где n — длина `t`.  
- Запрос: O(m log k), где m — длина `s`, k — среднее количество вхождений символа в `t`.

**Пространственная сложность:** O(n)  
- Храним индексы символов `t` в словаре.

---
## 2. [125. Валидный палиндром](https://leetcode.com/problems/valid-palindrome/)

### Условие задачи

Дана строка `s`, определите, является ли она палиндромом, учитывая только буквенно-цифровые символы и игнорируя регистр. Палиндром — это строка, которая читается одинаково слева направо и справа налево.

**Примеры:**

```
Пример 1:
Вход: s = "A man, a plan, a canal: Panama"
Выход: true
Объяснение: После очистки получается "amanaplanacanalpanama", что является палиндромом.

Пример 2:
Вход: s = "race a car"
Выход: false
Объяснение: После очистки — "raceacar", не палиндром.
```

**Ограничения:**
- `1 <= s.length <= 2 * 10^5`
- Строка `s` содержит ASCII-символы.

### Решения

#### 1. Два указателя с созданием строки (Two-Pointer with String Builder)

**Идея:** Очищаем строку от неалфавитно-цифровых символов, приводим к нижнему регистру, затем проверяем палиндром с помощью двух указателей.

**Интуиция:** Предобработка строки упрощает сравнение символов с обоих концов.

```python
def isPalindrome(s: str) -> bool:
    filtered_chars = [char.lower() for char in s if char.isalnum()]
    
    left, right = 0, len(filtered_chars) - 1
    
    while left < right:
        if filtered_chars[left] != filtered_chars[right]:
            return False
        left, right = left + 1, right - 1
    
    return True
```

**Временная сложность:** O(n)  
- Один проход для фильтрации, один для проверки палиндрома.

**Пространственная сложность:** O(n)  
- Храним очищенную строку.

#### 2. Оптимизированные два указателя (без дополнительной памяти)

**Идея:** Проверяем палиндром непосредственно в исходной строке, пропуская неалфавитно-цифровые символы.

**Интуиция:** Указатели движутся навстречу друг другу, игнорируя неподходящие символы, что экономит память.

```python
def isPalindrome(s: str) -> bool:
    left, right = 0, len(s) - 1
    
    while left < right:
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        
        if s[left].lower() != s[right].lower():
            return False
        
        left += 1
        right -= 1
        
    return True
```

**Временная сложность:** O(n)  
- Каждый символ обрабатывается не более двух раз.

**Пространственная сложность:** O(1)  
- Используются только указатели.

---
## 3. [14. Наидлиннейший общий префикс](https://leetcode.com/problems/longest-common-prefix/)

### Условие задачи

Дан массив строк `strs`, найдите наидлиннейший общий префикс среди всех строк. Если общего префикса нет, верните пустую строку.

**Примеры:**

```
Пример 1:
Вход: strs = ["flower","flow","flight"]
Выход: "fl"
Объяснение: "fl" — общий префикс для всех строк.

Пример 2:
Вход: strs = ["dog","racecar","car"]
Выход: ""
Объяснение: Нет общего префикса.
```

**Ограничения:**
- `1 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- Строки содержат только строчные английские буквы.

### Решения

#### 1. Горизонтальное сканирование (Horizontal Scanning)

**Идея:** Берем первую строку как начальный префикс и сравниваем с остальными, укорачивая префикс при несовпадении.

**Интуиция:** Постепенно сокращаем префикс, пока он не станет общим для всех строк.

```python
def longestCommonPrefix(strs):
    if not strs:
        return ""
    
    prefix = strs[0]
    
    for s in strs[1:]:
        while not s.startswith(prefix):
            prefix = prefix[:-1]
            if not prefix:
                return ""
    
    return prefix
```

**Временная сложность:** O(S)  
- S — сумма длин всех строк, так как проверяем префикс для каждой строки.

**Пространственная сложность:** O(1)  
- Используется только переменная для префикса.

#### 2. Вертикальное сканирование (Vertical Scanning)

**Идея:** Сравниваем символы на одной позиции во всех строках, пока не найдем несовпадение.

**Интуиция:** Поскольку префикс общий, достаточно проверить символы по позициям.

```python
def longestCommonPrefix(strs):
    if not strs:
        return ""
    
    for i in range(len(strs[0])):
        char = strs[0][i]
        for s in strs[1:]:
            if i >= len(s) or s[i] != char:
                return strs[0][:i]
    
    return strs[0]
```

**Временная сложность:** O(S)  
- S — сумма длин всех строк.

**Пространственная сложность:** O(1)  
- Без дополнительной памяти.

#### 3. Разделяй и властвуй (Divide and Conquer)

**Идея:** Делим массив строк на две части, находим префиксы для каждой, затем объединяем.

**Интуиция:** Рекурсивное разбиение упрощает задачу, находя общий префикс для подгрупп.

```python
def longestCommonPrefix(strs):
    if not strs:
        return ""
    
    def common_prefix(left, right):
        min_len = min(len(left), len(right))
        for i in range(min_len):
            if left[i] != right[i]:
                return left[:i]
        return left[:min_len]
    
    def longest_common_prefix_recursive(l, r):
        if l == r:
            return strs[l]
        mid = (l + r) // 2
        lcp_left = longest_common_prefix_recursive(l, mid)
        lcp_right = longest_common_prefix_recursive(mid + 1, r)
        return common_prefix(lcp_left, lcp_right)
    
    return longest_common_prefix_recursive(0, len(strs) - 1)
```

**Временная сложность:** O(S)  
- S — сумма длин всех строк.

**Пространственная сложность:** O(m * log n)  
- m — длина кратчайшей строки, n — количество строк, из-за рекурсивного стека.

#### 4. Бинарный поиск (Binary Search)

**Идея:** Используем бинарный поиск для определения длины префикса, проверяя, является ли префикс данной длины общим.

**Интуиция:** Бинарный поиск по длине префикса сокращает количество проверок.

```python
def longestCommonPrefix(strs):
    if not strs:
        return ""
    
    min_length = min(len(s) for s in strs)
    
    def is_common_prefix(length):
        prefix = strs[0][:length]
        return all(s.startswith(prefix) for s in strs[1:])
    
    low, high = 0, min_length
    while low <= high:
        mid = (low + high) // 2
        if is_common_prefix(mid):
            low = mid + 1
        else:
            high = mid - 1
    
    return strs[0][:(low + high) // 2]
```

**Временная сложность:** O(S * log minLen)  
- S — сумма длин всех строк, minLen — длина кратчайшей строки.

**Пространственная сложность:** O(1)  
- Без дополнительной памяти.

---
## 4. [6. Зигзаг-преобразование](https://leetcode.com/problems/zigzag-conversion/)

### Условие задачи

Дана строка `s` и количество строк `numRows`. Преобразуйте строку в зигзаг-узор, записывая её по строкам, а затем прочитайте результат построчно.

**Примеры:**

```
Пример 1:
Вход: s = "PAYPALISHIRING", numRows = 3
Выход: "PAHNAPLSIHRYING"
Объяснение:
P   A   H   N
A P L S I I G
Y   I   R
Результат чтения построчно: "PAHNAPLSIHRYING"

Пример 2:
Вход: s = "PAYPALISHIRING", numRows = 4
Выход: "PINALSIGYAHRPI"
Объяснение:
P     I    N
A   L S  I G
Y A   H R
P     I
```

**Ограничения:**
- `1 <= s.length <= 1000`
- `1 <= numRows <= 1000`
- Строка `s` содержит английские буквы, запятые и точки.

### Решения

#### 1. Симуляция зигзаг-узора (Simulate Zigzag Pattern)

**Идея:** Симулируем процесс записи символов в строки зигзаг-узора, отслеживая текущую строку и направление.

**Интуиция:** Перемещаемся вниз и вверх по строкам, добавляя символы в соответствующие строки.

```python
def convert(s: str, numRows: int) -> str:
    if numRows == 1 or numRows >= len(s):
        return s

    rows = [''] * numRows
    current_row, step = 0, 1

    for char in s:
        rows[current_row] += char
        if current_row == 0:
            step = 1
        elif current_row == numRows - 1:
            step = -1
        current_row += step

    return ''.join(rows)
```

**Временная сложность:** O(n)  
- n — длина строки, один проход.

**Пространственная сложность:** O(n)  
- Храним символы в массиве строк.

#### 2. Оптимизация с вычислением цикла (Cycle Calculation)

**Идея:** Используем повторяющийся цикл зигзага для прямого определения позиций символов.

**Интуиция:** Каждый цикл длиной `2 * numRows - 2` определяет, какие символы попадают в каждую строку.

```python
def convert(s: str, numRows: int) -> str:
    if numRows == 1 or numRows >= len(s):
        return s

    result = []
    cycle_len = 2 * numRows - 2

    for i in range(numRows):
        for j in range(i, len(s), cycle_len):
            result.append(s[j])
            if i != 0 and i != numRows - 1 and j + cycle_len - 2 * i < len(s):
                result.append(s[j + cycle_len - 2 * i])

    return ''.join(result)
```

**Временная сложность:** O(n)  
- Каждый символ обрабатывается один раз.

**Пространственная сложность:** O(n)  
- Храним результат в списке.

---
## 5. [151. Разворот слов в строке](https://leetcode.com/problems/reverse-words-in-a-string/)

### Условие задачи

Дана строка `s`, переверните порядок слов в ней. Слово — это последовательность символов без пробелов. Удалите лишние пробелы, чтобы слова разделялись ровно одним пробелом, без пробелов в начале и конце.

**Примеры:**

```
Пример 1:
Вход: s = "the sky is blue"
Выход: "blue is sky the"

Пример 2:
Вход: s = "  hello world  "
Выход: "world hello"
Объяснение: Лишние пробелы удалены.
```

**Ограничения:**
- `1 <= s.length <= 10^4`
- Строка содержит английские буквы, цифры и пробелы.
- Хотя бы одно слово присутствует.

### Решения

#### 1. Использование встроенных методов (Built-in Split and Reverse)

**Идея:** Разделяем строку на слова, переворачиваем их порядок и объединяем с одним пробелом.

**Интуиция:** Встроенные методы Python упрощают задачу, но требуют дополнительной памяти.

```python
def reverseWords(s: str) -> str:
    words = s.split()
    reversed_words = words[::-1]
    return ' '.join(reversed_words)
```

**Временная сложность:** O(n)  
- n — длина строки, для разделения, разворота и объединения.

**Пространственная сложность:** O(n)  
- Храним список слов.

#### 2. Разворот на месте (In-place Reversal)

**Идея:** Удаляем лишние пробелы, преобразуем строку в список символов, разворачиваем весь список, затем каждое слово.

**Интуиция:** Развороты на месте минимизируют память, но требуют аккуратной работы с символами.

```python
def reverseWords(s: str) -> str:
    def reverse_list(chars, start, end):
        while start < end:
            chars[start], chars[end] = chars[end], chars[start]
            start, end = start + 1, end - 1

    char_list = list(s.strip())
    reverse_list(char_list, 0, len(char_list) - 1)

    n = len(char_list)
    start = 0
    while start < n:
        while start < n and char_list[start] == ' ':
            start += 1
        end = start
        while end < n and char_list[end] != ' ':
            end += 1
        reverse_list(char_list, start, end - 1)
        start = end

    return ' '.join(''.join(char_list).split())
```

**Временная сложность:** O(n)  
- Несколько проходов по строке.

**Пространственная сложность:** O(n)  
- В Python строки неизменяемы, поэтому используем список символов.