## 1. [706. Разработка хэш-таблицы](https://leetcode.com/problems/design-hashmap/)

### Условие задачи

Создайте хэш-таблицу с операциями `put`, `get` и `remove`. Реализация не должна использовать встроенные библиотеки хэш-таблиц.

- `put(key, value)`: Вставляет или обновляет значение по ключу.
- `get(key)`: Возвращает значение по ключу или -1, если ключ отсутствует.
- `remove(key)`: Удаляет ключ и его значение.

**Примеры:**

```
Вход: ["MyHashMap", "put", "put", "get", "get", "put", "get", "remove", "get"]
       [[], [1, 1], [2, 2], [1], [3], [2, 1], [2], [2], [2]]
Выход: [null, null, null, 1, -1, null, 1, null, -1]
Объяснение:
MyHashMap hashMap = new MyHashMap();
hashMap.put(1, 1); // хэш-таблица: [[1,1]]
hashMap.put(2, 2); // хэш-таблица: [[1,1], [2,2]]
hashMap.get(1);    // возвращает 1
hashMap.get(3);    // возвращает -1 (ключ отсутствует)
hashMap.put(2, 1); // хэш-таблица: [[1,1], [2,1]] (обновление)
hashMap.get(2);    // возвращает 1
hashMap.remove(2); // хэш-таблица: [[1,1]]
hashMap.get(2);    // возвращает -1 (ключ удален)
```

**Ограничения:**
- `0 <= key, value <= 10^6`
- Максимум 10^4 вызовов операций `put`, `get`, `remove`.

### Решения

#### 1. Цепочки с использованием списков (Chaining with Lists)

**Идея:** Используем массив списков, где каждый список хранит пары ключ-значение для разрешения коллизий.

**Интуиция:** Простая хэш-функция (например, `key % size`) определяет индекс, а список в этом индексе хранит все пары, попавшие в него из-за коллизий.

```python
class MyHashMap:
    def __init__(self):
        self.size = 1000
        self.table = [[] for _ in range(self.size)]

    def _hash(self, key):
        return key % self.size

    def put(self, key: int, value: int) -> None:
        hash_key = self._hash(key)
        for pair in self.table[hash_key]:
            if pair[0] == key:
                pair[1] = value
                return
        self.table[hash_key].append([key, value])

    def get(self, key: int) -> int:
        hash_key = self._hash(key)
        for pair in self.table[hash_key]:
            if pair[0] == key:
                return pair[1]
        return -1

    def remove(self, key: int) -> None:
        hash_key = self._hash(key)
        for i, pair in enumerate(self.table[hash_key]):
            if pair[0] == key:
                del self.table[hash_key][i]
                return
```

**Временная сложность:**  
- `put`, `get`, `remove`: O(N/K) в среднем, где N — количество ключей, K — размер таблицы. В худшем случае O(N) при множественных коллизиях.

**Пространственная сложность:** O(N + K)  
- N — количество пар ключ-значение, K — размер таблицы.

#### 2. Открытая адресация с линейным зондированием (Open Addressing with Linear Probing)

**Идея:** Храним все элементы непосредственно в массиве, разрешая коллизии поиском следующего свободного слота.

**Интуиция:** При коллизии переходим к следующему индексу, пока не найдем свободный слот или нужный ключ.

```python
class MyHashMap:
    def __init__(self):
        self.size = 10000
        self.table = [(-1, -1)] * self.size

    def _hash(self, key):
        return key % self.size

    def put(self, key: int, value: int) -> None:
        index = self._hash(key)
        while self.table[index] != (-1, -1) and self.table[index][0] != key:
            index = (index + 1) % self.size
        self.table[index] = (key, value)

    def get(self, key: int) -> int:
        index = self._hash(key)
        while self.table[index] != (-1, -1):
            if self.table[index][0] == key:
                return self.table[index][1]
            index = (index + 1) % self.size
        return -1

    def remove(self, key: int) -> None:
        index = self._hash(key)
        while self.table[index] != (-1, -1):
            if self.table[index][0] == key:
                self.table[index] = (-1, -1)
                return
            index = (index + 1) % self.size
```

**Временная сложность:**  
- `put`, `get`, `remove`: O(1) в среднем, O(N) в худшем случае из-за кластеризации.

**Пространственная сложность:** O(K)  
- K — размер таблицы.

---
## 2. [1189. Максимальное количество слов "balloon"](https://leetcode.com/problems/maximum-number-of-balloons/)

### Условие задачи

Дана строка `text`, определите, сколько раз можно составить слово "balloon" из её символов. Каждый символ можно использовать не более одного раза.

**Примеры:**

```
Пример 1:
Вход: text = "nlaebolko"
Выход: 1
Объяснение: Можно составить одно слово "balloon" (b, a, l, l, o, o, n).

Пример 2:
Вход: text = "loonbalxballoon"
Выход: 2
Объяснение: Можно составить два слова "balloon".
```

**Ограничения:**
- `1 <= text.length <= 10^4`
- Строка содержит только строчные английские буквы.

### Решения

#### 1. Подсчет частоты (Frequency Count)

**Идея:** Подсчитываем частоту каждого символа в строке и определяем минимальное количество слов "balloon" по лимитирующим символам.

**Интуиция:** Слово "balloon" требует 1 'b', 1 'a', 2 'l', 2 'o', 1 'n', поэтому минимальная частота (с учетом деления для 'l' и 'o') определяет результат.

```python
def maxNumberOfBalloons(text: str) -> int:
    freq = {}
    for char in text:
        freq[char] = freq.get(char, 0) + 1
    
    min_instances = float('inf')
    min_instances = min(min_instances, freq.get('b', 0))
    min_instances = min(min_instances, freq.get('a', 0))
    min_instances = min(min_instances, freq.get('l', 0) // 2)
    min_instances = min(min_instances, freq.get('o', 0) // 2)
    min_instances = min(min_instances, freq.get('n', 0))
    
    return min_instances
```

**Временная сложность:** O(n)  
- n — длина строки, один проход для подсчета частот.

**Пространственная сложность:** O(1)  
- Словарь хранит фиксированное количество символов.

#### 2. Использование Counter (Optimal Approach)

**Идея:** Используем `Counter` для подсчета частот, что упрощает код.

**Интуиция:** То же, что и в первом подходе, но с использованием встроенной библиотеки Python.

```python
from collections import Counter

def maxNumberOfBalloons(text: str) -> int:
    freq = Counter(text)
    b_count = freq['b']
    a_count = freq['a']
    l_count = freq['l'] // 2
    o_count = freq['o'] // 2
    n_count = freq['n']
    
    return min(b_count, a_count, l_count, o_count, n_count)
```

**Временная сложность:** O(n)  
- Один проход для создания Counter.

**Пространственная сложность:** O(1)  
- Фиксированный набор символов.

---
## 3. [1512. Количество хороших пар](https://leetcode.com/problems/number-of-good-pairs/)

### Условие задачи

Дан массив целых чисел `nums`, найдите количество "хороших пар". Хорошая пара — это пара индексов `(i, j)`, где `nums[i] == nums[j]` и `i < j`.

**Примеры:**

```
Пример 1:
Вход: nums = [1,2,3,1,1,3]
Выход: 4
Объяснение: Хорошие пары: (0,3), (0,4), (3,4), (2,5).

Пример 2:
Вход: nums = [1,1,1,1]
Выход: 6
Объяснение: Каждая пара из 4 элементов образует хорошую пару.
```

**Ограничения:**
- `1 <= nums.length <= 100`
- `1 <= nums[i] <= 100`

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем все возможные пары индексов и считаем те, где значения равны.

**ИнStuart:** Простой подход, но неэффективный из-за вложенных циклов.

```python
def numIdenticalPairs(nums):
    good_pairs = 0
    n = len(nums)
    
    for i in range(n):
        for j in range(i + 1, n):
            if nums[i] == nums[j]:
                good_pairs += 1
                
    return good_pairs
```

**Временная сложность:** O(n²)  
- Два вложенных цикла.

**Пространственная сложность:** O(1)  
- Только счетчик пар.

#### 2. Оптимизированный подход с использованием словаря

**Идея:** Подсчитываем частоту каждого числа, добавляя количество новых пар для каждой встречи числа.

**Интуиция:** Для числа с частотой `k` количество пар вычисляется как `k * (k-1) / 2`, что делается постепенно.

```python
from collections import defaultdict

def numIdenticalPairs(nums):
    count = defaultdict(int)
    good_pairs = 0
    
    for num in nums:
        good_pairs += count[num]
        count[num] += 1
        
    return good_pairs
```

**Временная сложность:** O(n)  
- Один проход по массиву.

**Пространственная сложность:** O(n)  
- Словарь для хранения частот.

---
## 4. [205. Изоморфные строки](https://leetcode.com/problems/isomorphic-strings/)

### Условие задачи

Даны две строки `s`  и `t`, определите, являются ли они изоморфными. Две строки изоморфны, если символы в `s` можно заменить, чтобы получить `t`. Все вхождения одного символа должны заменяться на один и тот же символ, и это отображение должно быть взаимно однозначным.

**Примеры:**

```
Пример 1:
Вход: s = "egg", t = "add"
Выход: true
Объяснение: 'e' → 'a', 'g' → 'd', отображение однозначное.

Пример 2:
Вход: s = "foo", t = "bar"
Выход: false
Объяснение: 'f' → 'b', но 'o' не может отобразиться однозначно.
```

**Ограничения:**
- `1 <= s.length <= 5 * 10^4`
- `t.length == s.length`
- Строки содержат ASCII-символы.

### Решения

#### 1. Полный перебор с двумя словарями (Brute Force Mapping)

**Идея:** Используем два словаря для отображения символов `s` в `t` и обратно, проверяя их согласованность.

**Интуиция:** Проверяем, что каждый символ отображается уникально в обоих направлениях.

```python
def isIsomorphic(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False

    map_s_to_t = {}
    map_t_to_s = {}
    
    for c1, c2 in zip(s, t):
        if (c1 in map_s_to_t and map_s_to_t[c1] != c2) or (c2 in map_t_to_s and map_t_to_s[c2] != c1):
            return False
        
        map_s_to_t[c1] = c2
        map_t_to_s[c2] = c1
        
    return True
```

**Временная сложность:** O(n)  
- Один проход по строкам.

**Пространственная сложность:** O(n)  
- Два словаря для хранения отображений.

#### 2. Один словарь с двумя отображениями (One-to-One Mapping with Two Hash Maps)

**Идея:** То же, что и первый подход, но с оптимизированной проверкой отображений.

**Интуиция:** Разделяем проверку отображений на два отдельных шага для ясности.

```python
def isIsomorphic(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False

    map_s_to_t, map_t_to_s = {}, {}
    
    for c1, c2 in zip(s, t):
        if c1 in map_s_to_t:
            if map_s_to_t[c1] != c2:
                return False
        else:
            map_s_to_t[c1] = c2
        
        if c2 in map_t_to_s:
            if map_t_to_s[c2] != c1:
                return False
        else:
            map_t_to_s[c2] = c1
    
    return True
```

**Временная сложность:** O(n)  
- Один проход по строкам.

**Пространственная сложность:** O(n)  
- Два словаря.

#### 3. Кодирование символов одним словарем (Single Hash Map with Character Encoding)

**Идея:** Кодируем символы по их первому появлению в виде индексов и сравниваем полученные последовательности.

**Интуиция:** Если структуры кодирования совпадают, строки изоморфны.

```python
def isIsomorphic(s: str, t: str) -> bool:
    def encode(string):
        mapping = {}
        encoded = []
        for index, char in enumerate(string):
            if char not in mapping:
                mapping[char] = index
            encoded.append(mapping[char])
        return encoded
    
    return encode(s) == encode(t)
```

**Временная сложность:** O(n)  
- Один проход для кодирования каждой строки.

**Пространственная сложность:** O(n)  
- Храним списки кодирования.

---
## 5. [383. Записка с выкупом](https://leetcode.com/problems/ransom-note/)

### Условие задачи

Даны две строки `ransomNote` и `magazine`. Определите, можно ли составить `ransomNote` из символов `magazine`, используя каждый символ не более одного раза.

**Примеры:**

```
Пример 1:
Вход: ransomNote = "a", magazine = "b"
Выход: false

Пример 2:
Вход: ransomNote = "aa", magazine = "aab"
Выход: true
```

**Ограничения:**
- `1 <= ransomNote.length, magazine.length <= 10^5`
- Строки содержат только строчные буквы.

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем наличие каждого символа `ransomNote` в `magazine`, удаляя использованные символы.

**Интуиция:** Простой подход, но неэффективный из-за повторного поиска символов.

```python
def canConstruct(ransomNote: str, magazine: str) -> bool:
    magazine_list = list(magazine)
    for char in ransomNote:
        if char in magazine_list:
            magazine_list.remove(char)
        else:
            return False
    return True
```

**Временная сложность:** O(n²)  
- Поиск и удаление символов для каждого символа `ransomNote`.

**Пространственная сложность:** O(n)  
- Список символов `magazine`.

#### 2. Подсчет с использованием словаря (HashMap / Count Array)

**Идея:** Подсчитываем частоту символов в `magazine` и проверяем, достаточно ли их для `ransomNote`.

**Интуиция:** Словарь позволяет быстро проверить наличие достаточного количества символов.

```python
from collections import defaultdict

def canConstruct(ransomNote: str, magazine: str) -> bool:
    char_count = defaultdict(int)
    
    for char in magazine:
        char_count[char] += 1
    
    for char in ransomNote:
        if char_count[char] <= 0:
            return False
        char_count[char] -= 1
    
    return True
```

**Временная сложность:** O(n + m)  
- n — длина `magazine`, m — длина `ransomNote`.

**Пространственная сложность:** O(n)  
- Словарь для хранения частот.

#### 3. Использование Counter (Counter from Collections)

**Идея:** Используем `Counter` для подсчета частот и сравнения.

**Интуиция:** Упрощает подсчет частот с помощью встроенной библиотеки.

```python
from collections import Counter

def canConstruct(ransomNote: str, magazine: str) -> bool:
    ransom_counter = Counter(ransomNote)
    magazine_counter = Counter(magazine)
    
    for char, count in ransom_counter.items():
        if magazine_counter[char] < count:
            return False
    return True
```

**Временная сложность:** O(n + m)  
- Создание Counter и сравнение частот.

**Пространственная сложность:** O(1)  
- Фиксированный набор символов (строчные буквы).

---
## 6. [219. Наличие дубликатов II](https://leetcode.com/problems/contains-duplicate-ii/)

### Условие задачи

Дан массив целых чисел `nums` и целое число `k`, определите, существуют ли два одинаковых элемента с индексами, разница между которыми не превышает `k`.

**Примеры:**

```
Пример 1:
Вход: nums = [1,2,3,1], k = 3
Выход: true
Объяснение: nums[0] = nums[3] = 1, разница индексов 3 ≤ k.

Пример 2:
Вход: nums = [1,2,3,1,2,3], k = 2
Выход: false
Объяснение: Нет одинаковых элементов с разницей индексов ≤ k.
```

**Ограничения:**
- `1 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `0 <= k <= 10^5`

### Решения

#### 1. Полный перебор (Brute Force)

**Идея:** Проверяем все пары элементов в пределах `k` индексов.

**Интуиция:** Простой, но неэффективный подход из-за вложенных циклов.

```python
def containsNearbyDuplicate(nums, k):
    n = len(nums)
    for i in range(n):
        for j in range(i + 1, min(i + k + 1, n)):
            if nums[i] == nums[j]:
                return True
    return False
```

**Временная сложность:** O(n * k)  
- В худшем случае проверяем k элементов для каждого.

**Пространственная сложность:** O(1)  
- Только счетчики циклов.

#### 2. Хэш-таблица (Hash Map)

**Идея:** Используем словарь для хранения последнего индекса каждого числа, проверяя разницу индексов.

**Интуиция:** Хэш-таблица позволяет быстро находить предыдущее вхождение числа.

```python
def containsNearbyDuplicate(nums, k):
    num_index_map = {}
    for index, number in enumerate(nums):
        if number in num_index_map and index - num_index_map[number] <= k:
            return True
        num_index_map[number] = index
    return False
```

**Временная сложность:** O(n)  
- Один проход по массиву.

**Пространственная сложность:** O(n)  
- Словарь для хранения индексов.

## 7. [49. Группировка анаграмм](https://leetcode.com/problems/group-anagrams/)

### Условие задачи

Дан массив строк `strs`, сгруппируйте анаграммы вместе. Анаграммы — это строки, содержащие одинаковые символы с одинаковой частотой, но в разном порядке. Верните список групп анаграмм.

**Примеры:**

```
Пример 1:
Вход: strs = ["eat","tea","tan","ate","nat","bat"]
Выход: [["bat"],["nat","tan"],["ate","eat","tea"]]
Объяснение: "eat", "tea", "ate" — анаграммы; "tan", "nat" — анаграммы; "bat" — отдельная группа.

Пример 2:
Вход: strs = [""]
Выход: [[""]]
Объяснение: Пустая строка образует группу сама по себе.
```

**Ограничения:**
- `1 <= strs.length <= 10^4`
- `0 <= strs[i].length <= 100`
- `strs[i]` содержит только строчные английские буквы.

### Решения

#### 1. Сортировка и хэш-таблица (Using Sorting and Hash Map)

**Идея:** Сортируем каждую строку, чтобы использовать отсортированную строку как ключ в словаре для группировки анаграмм.

**Интуиция:** Анаграммы при сортировке дают одинаковый результат, что позволяет объединить их под одним ключом.

```python
def groupAnagrams(strs):
    anagrams = {}
    
    for s in strs:
        sorted_str = tuple(sorted(s))
        if sorted_str not in anagrams:
            anagrams[sorted_str] = []
        anagrams[sorted_str].append(s)
    
    return list(anagrams.values())
```

**Временная сложность:** O(n * m * log m)  
- n — количество строк, m — максимальная длина строки, сортировка строки занимает O(m * log m).

**Пространственная сложность:** O(n * m)  
- Для хранения ключей и групп анаграмм.

#### 2. Подсчет символов и хэш-таблица (Using Counting and Hash Map)

**Идея:** Создаем кортеж из частот символов для каждой строки и используем его как ключ для группировки.

**Интуиция:** Анаграммы имеют одинаковую частоту символов, что позволяет использовать её как уникальный идентификатор.

```python
def groupAnagrams(strs):
    anagrams = {}
    
    for s in strs:
        count = [0] * 26
        for char in s:
            count[ord(char) - ord('a')] += 1
        count_tuple = tuple(count)
        if count_tuple not in anagrams:
            anagrams[count_tuple] = []
        anagrams[count_tuple].append(s)
    
    return list(anagrams.values())
```

**Временная сложность:** O(n * m)  
- n — количество строк, m — максимальная длина строки, подсчет символов занимает O(m).

**Пространственная сложность:** O(n * m)  
- Для хранения ключей и групп анаграмм.

---
## 8. [535. Кодирование и декодирование TinyURL](https://leetcode.com/problems/encode-and-decode-tinyurl/)

### Условие задачи

Реализуйте систему для кодирования длинного URL в короткий и декодирования короткого URL обратно в длинный. Короткий URL должен быть уникальным для каждого длинного URL.

**Примеры:**

```
Вход: longUrl = "https://leetcode.com/problems/design-tinyurl"
Выход: "http://tinyurl.com/4e9iAk"
Объяснение: Кодируем длинный URL в короткий, затем декодируем обратно.
```

**Ограничения:**
- `1 <= longUrl.length <= 10^4`
- `longUrl` — валидный URL.

### Решения

#### 1. Хэш-таблица с случайными ключами (Hash Map with Randomly Generated Keys)

**Идея:** Генерируем случайный ключ фиксированной длины и сохраняем отображение ключа на длинный URL.

**Интуиция:** Случайные ключи обеспечивают уникальность с низкой вероятностью коллизий.

```python
import random
import string

class Codec:
    def __init__(self):
        self.url_dict = {}
        self.base_url = "http://tinyurl.com/"
        self.key_length = 6
        self.charset = string.ascii_letters + string.digits

    def _generate_key(self):
        return ''.join(random.choice(self.charset) for _ in range(self.key_length))

    def encode(self, longUrl: str) -> str:
        key = self._generate_key()
        while key in self.url_dict:
            key = self._generate_key()
        self.url_dict[key] = longUrl
        return self.base_url + key

    def decode(self, shortUrl: str) -> str:
        key = shortUrl.replace(self.base_url, "")
        return self.url_dict.get(key, "")
```

**Временная сложность:**  
- Кодирование: O(1) в среднем, O(n) в худшем случае из-за коллизий.
- Декодирование: O(1).

**Пространственная сложность:** O(n)  
- n — количество уникальных URL.

#### 2. Кодирование Base62 (Base62 Encoding)

**Идея:** Присваиваем каждому URL уникальный ID и кодируем его в Base62 для создания короткого URL.

**Интуиция:** Base62 позволяет компактно представлять большие числа, минимизируя длину ключа.

```python
class Codec:
    def __init__(self):
        self.id_to_url = {}
        self.url_to_id = {}
        self.base_url = "http://tinyurl.com/"
        self.charset = string.ascii_letters + string.digits
        self.counter = 0

    def _encode_id(self, id):
        if id == 0:
            return self.charset[0]
        base62 = []
        while id:
            id, rem = divmod(id, 62)
            base62.append(self.charset[rem])
        return ''.join(reversed(base62))

    def _decode_id(self, base62_str):
        id = 0
        for char in base62_str:
            id = id * 62 + self.charset.index(char)
        return id

    def encode(self, longUrl: str) -> str:
        if longUrl in self.url_to_id:
            id = self.url_to_id[longUrl]
        else:
            self.counter += 1
            id = self.counter
            self.url_to_id[longUrl] = id
            self.id_to_url[id] = longUrl
        return self.base_url + self._encode_id(id)

    def decode(self, shortUrl: str) -> str:
        base62_str = shortUrl.replace(self.base_url, "")
        id = self._decode_id(base62_str)
        return self.id_to_url.get(id, "")
```

**Временная сложность:**  
- Кодирование: O(log n) из-за преобразования в Base62.
- Декодирование: O(log n).

**Пространственная сложность:** O(n)  
- Для хранения отображений.

---
## 9. [767. Переупорядочивание строки](https://leetcode.com/problems/reorganize-string/)

### Условие задачи

Дана строка `s`, переупорядочите её так, чтобы одинаковые символы не стояли рядом. Если это невозможно, верните пустую строку.

**Примеры:**

```
Пример 1:
Вход: s = "aab"
Выход: "aba"

Пример 2:
Вход: s = "aaab"
Выход: ""
Объяснение: Нельзя переупорядочить, так как 'a' встречается слишком часто.
```

**Ограничения:**
- `1 <= s.length <= 500`
- `s` содержит только строчные английские буквы.

### Решения

#### 1. Жадный алгоритм с максимальной кучей (Greedy with Max Heap)

**Идея:** Используем максимальную кучу для выбора символов с наибольшей частотой, размещая их через один.

**Интуиция:** Частые символы должны быть равномерно распределены, чтобы избежать соседства.

```python
import heapq
from collections import Counter

def reorganizeString(s: str) -> str:
    char_count = Counter(s)
    max_heap = [(-freq, char) for char, freq in char_count.items()]
    heapq.heapify(max_heap)
    
    result = []
    
    while len(max_heap) > 1:
        freq1, char1 = heapq.heappop(max_heap)
        freq2, char2 = heapq.heappop(max_heap)
        
        result.extend([char1, char2])
        
        if freq1 + 1 < 0:
            heapq.heappush(max_heap, (freq1 + 1, char1))
        if freq2 + 1 < 0:
            heapq.heappush(max_heap, (freq2 + 1, char2))
    
    if max_heap:
        freq, char = heapq.heappop(max_heap)
        if freq + 1 < 0:
            return ""
        result.append(char)
    
    return "".join(result)
```

**Временная сложность:** O(n * log k)  
- n — длина строки, k — количество уникальных символов.

**Пространственная сложность:** O(k)  
- Для кучи и счетчика.

#### 2. Подсчет и сортировка (Count and Sort)

**Идея:** Сортируем символы по частоте и размещаем их в четных и нечетных позициях.

**Интуиция:** Частые символы размещаются через один, чтобы избежать соседства.

```python
from collections import Counter

def reorganizeString(s: str) -> str:
    char_count = Counter(s)
    sorted_chars = sorted(char_count, key=lambda x: -char_count[x])
    
    res = [''] * len(s)
    index = 0
    
    for char in sorted_chars:
        count = char_count[char]
        if count > (len(s) + 1) // 2:
            return ""
        for _ in range(count):
            if index >= len(s):
                index = 1
            res[index] = char
            index += 2
    
    return ''.join(res)
```

**Временная сложность:** O(n + k * log k)  
- n — длина строки, k — количество уникальных символов.

**Пространственная сложность:** O(n)  
- Для результата и счетчика.

---
## 10. [128. Наидлиннейшая последовательная последовательность](https://leetcode.com/problems/longest-consecutive-sequence/)

### Условие задачи

Дан неотсортированный массив целых чисел `nums`, найдите длину наидлиннейшей последовательности последовательных чисел.

**Примеры:**

```
Пример 1:
Вход: nums = [100,4,200,1,3,2]
Выход: 4
Объяснение: Последовательность [1,2,3,4] имеет длину 4.

Пример 2:
Вход: nums = [0,3,7,2,5,8,4,6,0,1]
Выход: 9
```

**Ограничения:**
- `0 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`

### Решения

#### 1. Сортировка и проверка последовательности (Sort and Check Consecutivity)

**Идея:** Сортируем массив и подсчитываем длину последовательных последовательностей.

**Интуиция:** После сортировки последовательные числа будут рядом, что упрощает подсчет.

```python
def longestConsecutive(nums):
    if not nums:
        return 0

    nums.sort()
    longest_streak = 1
    current_streak = 1

    for i in range(1, len(nums)):
        if nums[i] == nums[i - 1]:
            continue
        elif nums[i] == nums[i - 1] + 1:
            current_streak += 1
        else:
            longest_streak = max(longest_streak, current_streak)
            current_streak = 1
    
    return max(longest_streak, current_streak)
```

**Временная сложность:** O(n * log n)  
- Сортировка занимает O(n * log n).

**Пространственная сложность:** O(1)  
- Без учета пространства для сортировки.

#### 2. Оптимизированный подход с использованием множества (Using HashSet)

**Идея:** Используем множество для проверки наличия чисел и строим последовательности, начиная только с минимальных чисел.

**Интуиция:** Проверяем только начала последовательностей, что позволяет избежать лишних вычислений.

```python
def longestConsecutive(nums):
    num_set = set(nums)
    longest_streak = 0

    for num in num_set:
        if num - 1 not in num_set:
            current_num = num
            current_streak = 1

            while current_num + 1 in num_set:
                current_num += 1
                current_streak += 1

            longest_streak = max(longest_streak, current_streak)

    return longest_streak
```

**Временная сложность:** O(n)  
- Каждый элемент обрабатывается не более двух раз.

**Пространственная сложность:** O(n)  
- Для хранения множества.

---
## 11. [659. Разбиение массива на последовательные подпоследовательности](https://leetcode.com/problems/split-array-into-consecutive-subsequences/)

### Условие задачи

Дан отсортированный массив целых чисел `nums`, определите, можно ли разбить его на подпоследовательности, каждая из которых состоит из не менее чем трех последовательных чисел.

**Примеры:**

```
Пример 1:
Вход: nums = [1,2,3,3,4,5]
Выход: true
Объяснение: [1,2,3], [3,4,5]

Пример 2:
Вход: nums = [1,2,3,4,4,5]
Выход: false
```

**Ограничения:**
- `1 <= nums.length <= 10^4`
- Массив отсортирован по возрастанию.

### Решения

#### 1. Простой метод подсчета (Straightforward Counting Method)

**Идея:** Подсчитываем частоту чисел и пытаемся формировать подпоследовательности, начиная с минимальных чисел.

**Интуиция:** Используем жадный подход, добавляя числа к существующим подпоследовательностям или создавая новые.

```python
def isPossible(nums):
    from collections import defaultdict

    freq = defaultdict(int)
    subs = defaultdict(int)

    for num in nums:
        freq[num] += 1

    for num in nums:
        if freq[num] == 0:
            continue

        if subs[num - 1] > 0:
            subs[num - 1] -= 1
            subs[num] += 1
        elif freq[num + 1] > 0 and freq[num + 2] > 0:
            freq[num + 1] -= 1
            freq[num + 2] -= 1
            subs[num + 2] += 1
        else:
            return False

        freq[num] -= 1

    return True
```

**Временная сложность:** O(n)  
- Один проход по массиву.

**Пространственная сложность:** O(n)  
- Для словарей частот и подпоследовательностей.

#### 2. Оптимизированный жадный подход (Optimized Greedy Approach)

**Идея:** Используем два словаря: один для частоты чисел, другой для отслеживания подпоследовательностей, требующих следующего числа.

**Интуиция:** Жадный выбор минимизирует количество новых подпоследовательностей.

```python
def isPossible(nums):
    from collections import defaultdict

    freq = defaultdict(int)
    appendfreq = defaultdict(int)

    for num in nums:
        freq[num] += 1

    for num in nums:
        if freq[num] == 0:
            continue
        elif appendfreq[num - 1] > 0:
            appendfreq[num - 1] -= 1
            appendfreq[num] += 1
        elif freq[num + 1] > 0 and freq[num + 2] > 0:
            freq[num + 1] -= 1
            freq[num + 2] -= 1
            appendfreq[num + 2] += 1
        else:
            return False
        freq[num] -= 1

    return True
```

**Временная сложность:** O(n)  
- Один проход по массиву.

**Пространственная сложность:** O(n)  
- Для словарей.

---
## 12. [792. Количество соответствующих подпоследовательностей](https://leetcode.com/problems/number-of-matching-subsequences/)

### Условие задачи

Даны строка `s` и массив строк `words`, определите, сколько слов из `words` являются подпоследовательностями строки `s`.

**Примеры:**

```
Пример 1:
Вход: s = "abcde", words = ["a","bb","acd","ace"]
Выход: 3
Объяснение: "a", "acd", "ace" являются подпоследовательностями.

Пример 2:
Вход: s = "dsahjpjauf", words = ["ahjpjau","ja","ahbwzg","auj"]
Выход: 2
```

**Ограничения:**
- `1 <= s.length <= 5 * 10^4`
- `1 <= words.length <= 5000`
- `1 <= words[i].length <= 50`
- Все строки содержат только строчные буквы.

### Решения

#### 1. Полный перебор с двумя указателями (Brute Force With Two Pointers)

**Идея:** Для каждого слова проверяем, является ли оно подпоследовательностью `s`, используя два указателя.

**Интуиция:** Простой подход, но неэффективный из-за многократного сканирования `s`.

```python
def numMatchingSubseq(s, words):
    def isSubsequence(word, s):
        i = 0
        for char in s:
            if i < len(word) and word[i] == char:
                i += 1
            if i == len(word):
                return True
        return i == len(word)
    
    count = 0
    for word in words:
        if isSubsequence(word, s):
            count += 1
    return count
```

**Временная сложность:** O(n * m)  
- n — длина `s`, m — суммарная длина слов.

**Пространственная сложность:** O(1)  
- Без учета входных данных.

#### 2. Частота с картами (Frequency with Maps)

**Идея:** Сохраняем индексы символов в `s` и используем бинарный поиск для проверки подпоследовательностей.

**Интуиция:** Предобработка `s` ускоряет проверку слов.

```python
from collections import defaultdict
from bisect import bisect_left

def numMatchingSubseq(s, words):
    char_indices = defaultdict(list)
    
    for index, char in enumerate(s):
        char_indices[char].append(index)
    
    count = 0
    for word in words:
        prev_index = -1
        found = True
        
        for char in word:
            if char not in char_indices:
                found = False
                break
            
            next_pos = bisect_left(char_indices[char], prev_index + 1)
            if next_pos == len(char_indices[char]):
                found = False
                break
            
            prev_index = char_indices[char][next_pos]
        
        if found:
            count += 1
    return count
```

**Временная сложность:** O(n + w * log m)  
- n — длина `s`, w — суммарная длина слов, m — средняя частота символов.

**Пространственная сложность:** O(n)  
- Для хранения индексов.

#### 3. Индексированные символы с бинарным поиском (Indexed Characters with Binary Search)

**Идея:** То же, что и второй подход, но с выделенной функцией проверки подпоследовательности.

**Интуиция:** Модульность кода упрощает понимание.

```python
from collections import defaultdict
from bisect import bisect_left

def numMatchingSubseq(s, words):
    char_indices = defaultdict(list)
    
    for index, char in enumerate(s):
        char_indices[char].append(index)
    
    def isSubsequence(word):
        prev_position = -1
        for char in word:
            if char not in char_indices:
                return False
            
            idx = bisect_left(char_indices[char], prev_position + 1)
            if idx == len(char_indices[char]):
                return False
            
            prev_position = char_indices[char][idx]
        
        return True
    
    return sum(isSubsequence(word) for word in words)
```

**Временная сложность:** O(n + w * log n)  
- Аналогично второму подходу.

**Пространственная сложность:** O(n)  
- Для хранения индексов.

---
## 13. [1525. Количество хороших способов разделить строку](https://leetcode.com/problems/number-of-good-ways-to-split-a-string/)

### Условие задачи

Дана строка `s`, найдите количество способов разделить её на две непустые подстроки, такие что количество уникальных символов в левой и правой подстроках равно.

**Примеры:**

```
Пример 1:
Вход: s = "aacaba"
Выход: 2
Объяснение: Разделения на "aa|caba" и "aac|aba" дают равное количество уникальных символов.

Пример 2:
Вход: s = "abcd"
Выход: 1
Объяснение: Разделение на "ab|cd" дает 2 уникальных символа в каждой части.
```

**Ограничения:**
- `1 <= s.length <= 10^5`
- `s` содержит только строчные английские буквы.

### Решения

#### 1. Полный перебор (Brute Force Approach)

**Идея:** Проверяем каждую точку разделения, подсчитывая уникальные символы в левой и правой частях.

**Интуиция:** Простой подход, но неэффективный из-за многократного подсчета.

```python
def numSplits(s: str) -> int:
    result = 0
    n = len(s)

    for i in range(1, n):
        left_unique = set(s[:i])
        right_unique = set(s[i:])

        if len(left_unique) == len(right_unique):
            result += 1
    
    return result
```

**Временная сложность:** O(n²)  
- Подсчет уникальных символов для каждой точки разделения.

**Пространственная сложность:** O(n)  
- Для хранения множеств.

#### 2. Оптимизированные префиксные и суффиксные массивы (Optimized Prefix and Suffix Frequency Arrays)

**Идея:** Используем префиксные и суффиксные массивы для подсчета уникальных символов, избегая повторных вычислений.

**Интуиция:** Предобработка строки позволяет быстро сравнивать количество уникальных символов.

```python
from collections import defaultdict

def numSplits(s: str) -> int:
    left_count = [0] * len(s)
    right_count = [0] * len(s)

    prefix_char_count = defaultdict(int)
    suffix_char_count = defaultdict(int)
    
    unique_chars_left = 0
    for i in range(len(s)):
        char = s[i]
        if prefix_char_count[char] == 0:
            unique_chars_left += 1
        prefix_char_count[char] += 1
        left_count[i] = unique_chars_left
    
    unique_chars_right = 0
    for i in range(len(s) - 1, -1, -1):
        char = s[i]
        if suffix_char_count[char] == 0:
            unique_chars_right += 1
        suffix_char_count[char] += 1
        right_count[i] = unique_chars_right

    result = 0
    for i in range(1, len(s)):
        if left_count[i - 1] == right_count[i]:
            result += 1
    
    return result
```

**Временная сложность:** O(n)  
- Два прохода для создания массивов и один для сравнения.

**Пространственная сложность:** O(1)  
- Фиксированный размер словарей для английского алфавита.