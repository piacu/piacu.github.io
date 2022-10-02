###### 문제 설명

0 또는 양의 정수가 주어졌을 때, 정수를 이어 붙여 만들 수 있는 가장 큰 수를 알아내 주세요.

예를 들어, 주어진 정수가 [6, 10, 2]라면 [6102, 6210, 1062, 1026, 2610, 2106]를 만들 수 있고, 이중 가장 큰 수는 6210입니다.

0 또는 양의 정수가 담긴 배열 numbers가 매개변수로 주어질 때, 순서를 재배치하여 만들 수 있는 가장 큰 수를 문자열로 바꾸어 return 하도록 solution 함수를 작성해주세요.

##### 제한 사항

- numbers의 길이는 1 이상 100,000 이하입니다.
- numbers의 원소는 0 이상 1,000 이하입니다.
- 정답이 너무 클 수 있으니 문자열로 바꾸어 return 합니다.

##### 입출력 예

| numbers           | return    |
| ----------------- | --------- |
| [6, 10, 2]        | "6210"    |
| [3, 30, 34, 5, 9] | "9534330" |



### 풀이 방법

```java
import java.util.*;

class Solution {
    public String solution(int[] numbers) {
        String[] strNums = new String[numbers.length];
        
        for(int i=0; i<strNums.length; i++){
            strNums[i] = Integer.toString(numbers[i]);
        }
        
        // comparator 기본 사용법
        // Comparator<String> comp = (o1, o2) -> (o2 + o1).compareTo(o1 + o2);
        // Arrays.sort(strNums, comp);

        // lambda, comparator 사용법
        // Arrays.sort(strNums, new Comparator<String>() {
        //     @Override
        //     public int compare(String o1, String o2){
        //         return (o2 + o1).compareTo(o1 + o2);
        //     }
        // });

        // 람다식 사용
        Arrays.sort(strNums, (a, b) -> {
            return (b + a).compareTo(a + b);
            }
        );
        
        if(strNums[0].equals("0")) return "0";
        
        return String.join("", strNums);
    }
}

```

처음에는 순열로 구현했다가 테케 정도만 맞고 다 틀렸다ㅠㅠ

하지만 이 문제는 순열이 아닌 정렬!

자바에서는 comparator를 사용해서 쉽게 정렬을 구현할 수 있다.
