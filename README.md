# 📚 Algoritma ve Mülakat Soruları Çalışma Notları

Bu dosya, sık karşılaşılan algoritma problemlerinin, veri yapıları operasyonlarının ve mülakat sorularının çözümlerini derlemektedir. 

## 📑 İçindekiler

- [Diziler ve Alt Diziler (Arrays & Subarrays)](#diziler-ve-alt-diziler-arrays--subarrays)
  - [Longest Consecutive Sequence](#1-longest-consecutive-sequence)
  - [Two Sum (Sıralı Dizi İçin)](#2-two-sum-sıralı-dizi-i̇çin)
  - [En Uzun Alt Dizi (Toplamı K'yı Geçmeyen)](#3-en-uzun-alt-dizi-toplamı-kyı-geçmeyen)
  - [Subarray Sum Equals K](#4-subarray-sum-equals-k)
  - [Merge Intervals (Aralıkları Birleştirme)](#5-merge-intervals-aralıkları-birleştirme)
  - [Remove Duplicates (In-place)](#6-remove-duplicates-in-place)
- [String Manipülasyonu (Strings)](#string-manipülasyonu-strings)
  - [Group Anagrams](#7-group-anagrams)
  - [Palindrome Permutation](#8-palindrome-permutation)
  - [String Palindrome O(n)](#9-string-palindrome-on)
  - [Longest Substring Without Repeating Characters](#10-longest-substring-without-repeating-characters)
  - [Find the Index of the First Occurrence in a String](#11-find-the-index-of-the-first-occurrence-in-a-string)
  - [Minimum Window Substring](#12-minimum-window-substring)
  - [Longest Word](#13-longest-word)
  - [String Reverse](#14-string-reverse)
  - [Questions Marks](#15-questions-marks)
  - [Find Intersection](#16-find-intersection)
  - [Codeland Username Validation](#17-codeland-username-validation)
  - [String Scramble](#18-string-scramble)
- [Ağaçlar ve Matematik (Trees & Math)](#ağaçlar-ve-matematik-trees--math)
  - [Binary Search](#19-binary-search)
  - [Tree Constructor](#20-tree-constructor)
  - [Bracket Match](#21-bracket-match)
  - [Bracket Combinations (Catalan Sayıları)](#22-bracket-combinations-catalan-sayıları)
- [React](#react)
  - [React Toggle Component](#23-react-toggle-component)

---

## Diziler ve Alt Diziler (Arrays & Subarrays)

### 1. Longest Consecutive Sequence
Ardışık sayıların oluşturduğu en uzun dizinin uzunluğunu bulur.

```java
public static int longestConsecutive(int[] arr) {
    if(arr == null || arr.length == 0){
        return 0;
    }
    Set<Integer> numSet = new HashSet<>();
    for(int num: arr){
        numSet.add(num);
    }
    int maxLength = 0;
    for(int num : numSet){
        if(!numSet.contains(num-1)){
            int currentNum = num;
            int consectiveNum = 1;
            while(numSet.contains(currentNum+1)){
                currentNum += 1;
                consectiveNum += 1;
            }
            maxLength = Math.max(maxLength, consectiveNum);
        }
    }
    return maxLength;
}
```

### 2. Two Sum (Sıralı Dizi İçin)
Sana artan sırada sıralanmış bir tam sayı dizisi ve bir target sayısı veriliyor. İki pointer tekniği kullanılır.

```java
public int[] twoSum(int[] numbers, int target) {
    int left = 0;
    int right = numbers.length - 1;

    while(left < right){
        int currentSum = numbers[left] + numbers[right];
        if(currentSum == target){
            return new int[]{left + 1, right + 1};
        } else if(currentSum < target){
            left++;
        } else {
            right--;
        }
    }

    return new int[]{-1, -1};
}
```

### 3. En Uzun Alt Dizi (Toplamı K'yı Geçmeyen)
Sliding Window tekniği kullanılarak toplamı k değerini aşmayan en uzun alt dizinin uzunluğunu bulur.

```java
public int findMaxSubarray(int[] nums, int k) {
    int left = 0;
    int currentSum = 0;
    int maxLength = 0;

    for(int right = 0; right < nums.length; ++right){
        currentSum += nums[right];

        if(currentSum > k){
            currentSum -= nums[left];
            ++left;
        }

        maxLength = Math.max(maxLength, right - left + 1);
    }

    return maxLength;
}
```

### 4. Subarray Sum Equals K
Eğer soruda "subarray sum equals K" (toplamı K olan alt diziler) gibi bir şey geçiyorsa, genellikle **Prefix Sum** ile **Hashmap**'i birlikte kullanman gerekir. (Negatif sayılar varsa Sliding Window çalışmaz, bu yöntem kullanılır).

```java
public int[] twoSum2(int[] nums, int target) {
    int n = nums.length;
    Map<Integer, Integer> map = new HashMap<>();

    for(int i=0; i<n; ++i){
        int ans = target - nums[i];

        if(map.containsKey(ans)){
            return new int[]{map.get(ans), i};
        }

        map.put(nums[i], i);
    }
    return new int[]{};
}
```

### 5. Merge Intervals (Aralıkları Birleştirme)
Zaman aralıklarından (veya sayı aralıklarından) oluşan bir listede birbiriyle çakışan (overlapping) aralıkları birleştirmenizi ister. Örneğin: `[[1,3], [2,6], [8,10]]` dizisinde `[1,3]` ve `[2,6]` çakışır çünkü 2 sayısı 1 ile 3 arasındadır. Bunları birleştirip `[1,6]` yaparız.

```java
public int[][] merge(int[][] intervals) {
    if (intervals.length <= 1) return intervals;

    // 1. Aralıkları başlangıç (start) zamanlarına göre sıralıyoruz - O(n log n)
    Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

    List<int[]> merged = new ArrayList<>();

    // İlk aralığı başlangıç olarak alalım
    int[] currentInterval = intervals[0];
    merged.add(currentInterval);

    for (int[] nextInterval : intervals) {
        int currentEnd = currentInterval[1];
        int nextStart = nextInterval[0];
        int nextEnd = nextInterval[1];

        // 2. Eğer çakışma varsa (Sonraki başlangıç, mevcut bitişten küçükse)
        if (nextStart <= currentEnd) {
            // Mevcut aralığın bitişini en büyük olanla güncelle
            currentInterval[1] = Math.max(currentEnd, nextEnd);
        } else {
            // Çakışma yoksa, sonraki aralığı listeye ekle ve onu "mevcut" yap
            currentInterval = nextInterval;
            merged.add(currentInterval);
        }
    }

    // Listeyi tekrar matrise çevirip döndür
    return merged.toArray(new int[merged.size()][]);
}
```

### 6. Remove Duplicates (In-place)
Orijinal diziyi değiştirerek yapmanı istiyorlarsa (In-place), **Two Pointers** tekniğini kullanmalısın. *Not: Bu yöntem dizinin sıralı olmasını gerektirir.*

```java
public static int remove(int[] nums) {
    if (nums.length == 0) return 0;

    int i = 0; // Benzersiz elemanların yerleşeceği indeks

    for (int j = 1; j < nums.length; j++) {
        if (nums[j] != nums[i]) {
            i++;
            nums[i] = nums[j];
        }
    }
    return i + 1; // Yeni dizinin uzunluğunu döndürür
}
```

---

## String Manipülasyonu (Strings)

### 7. Group Anagrams
Anagram olan kelimeleri gruplar.

```java
public List<List<String>> groupAnagrams(String[] strs) {
    if (strs == null || strs.length == 0) return new ArrayList<>();
    // Key: Sıralanmış kelime, Value: Orijinal kelimelerin listesi
    Map<String, List<String>> map = new HashMap<>();
    
    for (String s : strs) {
        // 1. Kelimeyi harflerine ayır ve alfabetik sırala
        char[] chars = s.toCharArray();
        Arrays.sort(chars);
        String key = String.valueOf(chars);
        
        // 2. Eğer bu anahtar map'te yoksa yeni bir liste oluştur
        if (!map.containsKey(key)) {
            map.put(key, new ArrayList<>());
        }
        // 3. Orijinal kelimeyi ilgili listenin içine ekle
        map.get(key).add(s);
    }
    // Map'teki tüm listeleri bir liste içinde döndür
    return new ArrayList<>(map.values());
}
```

### 8. Palindrome Permutation
Bir string'in karakterlerinin yerini değiştirerek (permutation) bir palindrom oluşturulabilir mi?

```java
public static boolean canFormPalindrome(String s) {
    if (s == null) return false;

    Set<Character> charSet = new HashSet<>();

    for (char c : s.toCharArray()) {
        // Eğer karakter zaten set içindeyse, çiftini bulduk demektir, çıkarıyoruz.
        if (charSet.contains(c)) {
            charSet.remove(c);
        } else {
            // Karakter yoksa ekliyoruz (şu an için tek).
            charSet.add(c);
        }
    }

    // Bir palindromda en fazla 1 tane "eşi olmayan" karakter olabilir.
    return charSet.size() <= 1;
}
```

### 9. String Palindrome O(n)

```java
public static boolean isPalindrome(String s) {
    if (s == null) return false;

    // Küçük-büyük harf duyarlılığını kaldırmak ve sadece harf/rakamları almak için (Opsiyonel)
    // s = s.toLowerCase().replaceAll("[^a-z0-9]", "");

    int left = 0;
    int right = s.length() - 1;

    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false; // Eşleşme bozulduğu an çık
        }
        left++;
        right--;
    }
    return true;
}
```

### 10. Longest Substring Without Repeating Characters

```java
public static int lengthOfLongestSubstring(String s) {
    if (s == null || s.length() == 0) return 0;

    // Key: Karakter, Value: Karakterin en son görüldüğü İNDİS
    Map<Character, Integer> map = new HashMap<>();
    int maxLength = 0;
    int left = 0; // Pencerenin başlangıcı

    for (int right = 0; right < s.length(); right++) {
        char currentChar = s.charAt(right);

        // 1. Eğer karakteri daha önce gördüysek ve bu karakter mevcut pencerenin (left) içindeyse:
        if (map.containsKey(currentChar)) {
            // 'left' işaretçisini, çakışan karakterin bir sağındaki indise ZIPLAT
            // Math.max kullanma sebebi: left asla geriye gitmemeli!
            left = Math.max(left, map.get(currentChar) + 1);
        }

        // 2. Karakterin en güncel indisini kaydet/güncelle
        map.put(currentChar, right);

        // 3. Maksimum uzunluğu güncelle
        maxLength = Math.max(maxLength, right - left + 1);
    }

    return maxLength;
}
```

### 11. Find the Index of the First Occurrence in a String

```java
public int srts(String haystack, String needle) {
    int n = haystack.length(), m = needle.length();
    for (int i = 0; i <= n - m; i++) {
        if (haystack.substring(i, i + m).equals(needle)) {
            return i;
        }
    }
    return -1;
}
```

### 12. Minimum Window Substring

```java
public static String MinWindowSubstring(String[] strArr) {
    String N = strArr[0];
    String K = strArr[1];

    // 1. Hedef harflerin sayılarını bir diziye kaydedelim (İhtiyaç Listesi)
    int[] target = new int[128];
    for (char c : K.toCharArray()) target[c]++;

    // Mevcut penceredeki harf sayılarını tutan dizi
    int[] varPcb = new int[128];
    
    int left = 0, right = 0;
    int count = 0; // Kaç tane hedef harfi tam olarak bulduk?
    int minLen = Integer.MAX_VALUE;
    int startIndex = 0;

    // 2. SAĞ İŞARETÇİYİ İLERLET (Pencereyi Genişlet)
    while (right < N.length()) {
        char rChar = N.charAt(right);
        varPcb[rChar]++;

        // Eğer bu harf K'da varsa ve penceredeki miktarı ihtiyacı aşmadıysa
        if (target[rChar] > 0 && varPcb[rChar] <= target[rChar]) {
            count++;
        }

        // 3. SOL İŞARETÇİYİ İLERLET (Pencereyi Daralt)
        // Eğer aranan tüm harfler şu anki penceredeyse, en küçüğü bulmak için daraltmaya başla
        while (count == K.length()) {
            // Pencere uzunluğunu kontrol et ve en küçükse kaydet
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                startIndex = left;
            }

            char lChar = N.charAt(left);
            varPcb[lChar]--;

            // Eğer çıkardığımız harf kritikse (artık ihtiyacın altına düştüyse) count'u azalt
            if (target[lChar] > 0 && varPcb[lChar] < target[lChar]) {
                count--;
            }
            left++; // Pencerenin solunu sağa çek
        }
        right++; // Pencerenin sağını genişletmeye devam et
    }

    return minLen == Integer.MAX_VALUE ? "" : N.substring(startIndex, startIndex + minLen);
}
```

### 13. Longest Word

```java
public static String LongestWord(String sen) {
    String [] senArray = sen.split("[^a-z0-9]");
    int longestIndex = 0;
    String longestWord = "";

    for(int i=0; i<senArray.length; i++){
        if(senArray[i].length() > longestIndex){
            longestIndex = senArray[i].length();
            longestWord = senArray[i];
        }
    }
    return longestWord; 
}   
```

### 14. String Reverse

```java
// Yöntem 1: Manuel döngü
String reverse = "";
for (int i = str.length() - 1; i >= 0; i--){
    reverse += str.charAt(i);
}

// Yöntem 2: StringBuilder kullanımı (Daha performanslı)
return new StringBuilder(str).reverse().toString();
```

### 15. Questions Marks

```java
public static String QuestionsMarks(String str) {
    str = str.replaceAll("[a-z]", "");
    Pattern pattern = Pattern.compile("([0-9])([?])([?])([?])([0-9])");
    Pattern falseP1 = Pattern.compile("([0-9])([?])([?])([0-9])");
    Pattern falseP2 = Pattern.compile("([0-9])([?])([0-9])");
    Matcher falseMatchP1 = falseP1.matcher(str);
    Matcher falseMatchP2 = falseP2.matcher(str);
    Matcher matchPattern = pattern.matcher(str);
    
    if (falseMatchP1.find() || falseMatchP2.find()){
        return "false";
    }
    if (matchPattern.find()){
        return "true";
    }
    return "false";
}
```

### 16. Find Intersection

```java
public static String FindIntersection(String[] strArr) {
    String[] firstList = strArr[0].split(", ");
    String[] secondList = strArr[1].split(", ");

    HashSet<String> set = new HashSet<>();
    for (String num : firstList) {
        set.add(num);
    }

    List<String> intersection = new ArrayList<>();
    for(String num : secondList){
        if(set.contains(num)){
            intersection.add(num);
        }
    }

    if (intersection.isEmpty()) {
        return "false";
    }

    return String.join(",", intersection);
}
```

### 17. Codeland Username Validation

```java
public static boolean CodelandUsernameValidation(String str) {
    if (str.length() <= 4 || str.length() >= 25) {
      return false;
    }
    if (str.charAt(str.length() - 1) == '_') {
      return false;
    } 
    if (!Character.isLetter(str.charAt(0))) {
      return false;
    }
    for (int i = 0; i < str.length(); i++) {
      if (!Character.isLetterOrDigit(str.charAt(i)) && str.charAt(i) != '_') {
        return false;
      }
    }
    return true;
}
```

### 18. String Scramble
İki string'in birbirinin "scramble" versiyonu olup olmadığını kontrol eder.

```java
class Solution {
    Map<String, Boolean> mp = new HashMap<>();

    public boolean isScramble(String s1, String s2) {
        int n = s1.length();

        if (s2.length() != n) return false;
        if (s1.equals(s2)) return true;
        if (n == 1) return false;

        String key = s1 + " " + s2;

        if (mp.containsKey(key)) return mp.get(key);

        for (int i = 1; i < n; i++) {
            boolean withoutswap = (
                isScramble(s1.substring(0, i), s2.substring(0, i)) &&
                isScramble(s1.substring(i), s2.substring(i))
            );

            if (withoutswap) {
                mp.put(key, true);
                return true;
            }

            boolean withswap = (
                isScramble(s1.substring(0, i), s2.substring(n - i)) &&
                isScramble(s1.substring(i), s2.substring(0, n - i))
            );

            if (withswap) {
                mp.put(key, true);
                return true;
            }
        }

        mp.put(key, false);
        return false;
    }
}
```

---

## Ağaçlar ve Matematik (Trees & Math)

### 19. Binary Search
Sıralı bir dizide hedef elemanı logaritmik zamanda (O(log n)) bulur.

```java
public static int binarySearch(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;

    while (left <= right) {
        // (left + right) / 2 yerine taşma (overflow) riskine karşı bu kullanılır:
        int mid = left + (right - left) / 2;

        if (nums[mid] == target) {
            return mid; // Eleman bulundu, indisini döndür
        }

        if (nums[mid] < target) {
            left = mid + 1; // Sağ tarafa bak
        } else {
            right = mid - 1; // Sol tarafa bak
        }
    }

    return -1; // Eleman bulunamadı
}
```

### 20. Tree Constructor
Verilen string dizisinden geçerli bir ikili ağaç (binary tree) oluşturulup oluşturulamayacağını kontrol eder. Her ebeveynin en fazla 2 çocuğu olabilir.

```java
public static String TreeConstructor(String[] strArr) {
    Set<String> children = new HashSet<>();
    Map<String, Integer> parents = new HashMap<>();

    for (String node : strArr) {
      String[] values = node.replaceAll("[^0-9,]", "").split(",");
      children.add(values[0]);
      
      Integer count = parents.get(values[1]);
      if (count != null && count + 1 > 2) {
          return "false";
      } else {
          parents.put(values[1], (count == null ? 1 : ++count));
      }
    }
    return "" + (children.size() == strArr.length);
}
```

### 21. Bracket Match
Parantezlerin düzgün açılıp kapandığını kontrol eder.

```java
public static String BracketMatch(String str) {
    Stack<Character> stack = new Stack<>();

    for (int i = 0; i < str.length(); i++){
        char c = str.charAt(i);

        if(c == '('){
            stack.push(c);
        } else if(c == ')'){
            if(stack.empty()) return "0";
            else stack.pop();
        }
    }
    return "" + (stack.isEmpty() ? 1 : 0);
}
```

### 22. Bracket Combinations (Catalan Sayıları)
Geçerli parantez kombinasyonlarını (Catalan sayıları formülüyle) hesaplar.

```java
public static long[] factArr = new long[10001];

public static long fact(int f){
    if(f <= 1) return 1;
    if(factArr[f] == 0){
        factArr[f] = f * fact(f-1);
    }
    return factArr[f];
}

public static int BracketCombinations(int n) {
    long f1 = fact(2 * n);
    long f2 = fact(n + 1) * fact(n); 
    long div = f1 / f2;
    return (int)(div);
}
```

---

## React

### 23. React Toggle Component
React'te state yönetimi ile basit bir aç-kapa (toggle) butonu örneği.

```tsx
import { useState } from 'react';

export default function ToggleButton() {
  const [isOn, setIsOn] = useState(true);

  function handleClick() {
    // Mevcut durumun tersini set ediyoruz (true ise false, false ise true)
    setIsOn(!isOn); 
  }

  return (
    <button onClick={handleClick}>
        {isOn ? 'ON' : 'OFF'}
    </button>
  );
}
```
