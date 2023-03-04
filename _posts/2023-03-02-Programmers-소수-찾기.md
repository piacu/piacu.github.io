---

title: "[Java] 프로그래머스: 소수 찾기"
author: piacu
date: 2023-03-02 20:04:00 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers, java]

---

###### 문제 설명

한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.

각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.

##### 제한사항

- numbers는 길이 1 이상 7 이하인 문자열입니다.
- numbers는 0~9까지 숫자만으로 이루어져 있습니다.
- "013"은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.

##### 입출력 예

| numbers | return |
| ------- | ------ |
| "17"    | 3      |
| "011"   | 2      |

##### 입출력 예 설명

예제 #1
[1, 7]으로는 소수 [7, 17, 71]를 만들 수 있습니다.

예제 #2
[0, 1, 1]으로는 소수 [11, 101]를 만들 수 있습니다.

- 11과 011은 같은 숫자로 취급합니다.

### 풀이 방법

```java
import java.util.*;

class Solution {
    static Set<Integer> set;
    public int solution(String numbers) {
        set = new HashSet<>();
        char[] chs = numbers.toCharArray();
        
        for(int i=0; i<chs.length; i++){
            permutation(0, i+1, chs, new char[i+1], new boolean[chs.length]);  
        }
        
        return set.size();
    }
    public void permutation(int N, int max, char[] in, char[] out, boolean[] v){
        if(N == out.length){
            String str = "";
            for(int i=0; i<out.length; i++){
                str += out[i];
            }
            
            int num = Integer.parseInt(str);
            
            if(num<2) return;
            for(int i=2; i<num; i++){
                if(num % i == 0) return;
            }
            
            set.add(num);
            return;
        }
        
        for(int i=0; i<in.length; i++){
            if(!v[i]){
                v[i] = true;
                out[N] = in[i];
                permutation(N+1, max, in, out, v);
                v[i] = false;
            }
        }
    }
}
```

깔끔하지 못하게 푼 문제다.

가지고 있는 N개의 카드 중 1~N개를 골라 만들 수 있는 수 중에서 소수인 수를 골라야 하는 문제이다.

수를 만들기 위해 순열(순서가 중요하기 때문)을 썼다가, 1개부터 N개까지를 골라야 함에 따라 부분집합인가?를 고민하기도 해서 시간이 오래 걸렸던 문제이다.

결국에는 N개 중 1개를 선택하는 순열부터 N개를 선택하는 순열까지 for문으로 돌린 후에 완전탐색을 사용해 소수를 구하였다.

게다가 마지막 설명 중 11, 011은 같은 숫자로 취급합니다. 에서 중복제거를 해야 한다고 느꼈고, 중복이 불가한 HashSet을 사용해 중복을 제거한 후 HashSet 내의 개수를 세서 결과값을 리턴했다.



p.s.

코드가 지저분하게 느껴진다. 순열 조합 부분집합에 대해 다시 공부하며 조금 더 깔끔한 코드를 짜도록 해야겠다.

소수를 구하기 위해서는 에라토스테네스의 체를 쓰는 게 성능이 좋다고 한다.
