---

title: "[Java] 프로그래머스: 모의고사"
author: piacu
date: 2023-02-28 17:32:00 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers, java]

---

- ###### 문제 설명

  수포자는 수학을 포기한 사람의 준말입니다. 수포자 삼인방은 모의고사에 수학 문제를 전부 찍으려 합니다. 수포자는 1번 문제부터 마지막 문제까지 다음과 같이 찍습니다.

  1번 수포자가 찍는 방식: 1, 2, 3, 4, 5, 1, 2, 3, 4, 5, ...
  2번 수포자가 찍는 방식: 2, 1, 2, 3, 2, 4, 2, 5, 2, 1, 2, 3, 2, 4, 2, 5, ...
  3번 수포자가 찍는 방식: 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, 3, 3, 1, 1, 2, 2, 4, 4, 5, 5, ...

  1번 문제부터 마지막 문제까지의 정답이 순서대로 들은 배열 answers가 주어졌을 때, 가장 많은 문제를 맞힌 사람이 누구인지 배열에 담아 return 하도록 solution 함수를 작성해주세요.

  ##### 제한 조건

  - 시험은 최대 10,000 문제로 구성되어있습니다.
  - 문제의 정답은 1, 2, 3, 4, 5중 하나입니다.
  - 가장 높은 점수를 받은 사람이 여럿일 경우, return하는 값을 오름차순 정렬해주세요.

  ##### 입출력 예

  | answers     | return  |
  | ----------- | ------- |
  | [1,2,3,4,5] | [1]     |
  | [1,3,2,4,2] | [1,2,3] |

  ##### 입출력 예 설명

  입출력 예 #1

  - 수포자 1은 모든 문제를 맞혔습니다.
  - 수포자 2는 모든 문제를 틀렸습니다.
  - 수포자 3은 모든 문제를 틀렸습니다.

  따라서 가장 문제를 많이 맞힌 사람은 수포자 1입니다.

  입출력 예 #2

  - 모든 사람이 2문제씩을 맞췄습니다.

### 풀이 방법

```java
import java.util.*;

class Solution {
    public int[] solution(int[] answers) {
        List<Integer> answer = new ArrayList<>();
        int[] first = {1,2,3,4,5};
        int[] second = {2,1,2,3,2,4,2,5};
        int[] third = {3,3,1,1,2,2,4,4,5,5};
        
        int[] idx = new int[3];
        int[] res = new int[3];
        
        for(int i=0; i<answers.length; i++){
            int ans = answers[i];
            idx[0] %= 5;
            idx[1] %= 8;
            idx[2] %= 10;
            
            if(first[idx[0]++] == ans) res[0]++;
            if(second[idx[1]++] == ans) res[1]++;
            if(third[idx[2]++] == ans) res[2]++;
            
        }
        
        int tmpRes = Math.max(res[0], Math.max(res[1], res[2]));
        
        for(int i=0; i<3; i++){
            if(tmpRes == res[i]) answer.add(i+1);
        }
        
        int[] returnArr = new int[answer.size()];
        
        for(int i=0; i<returnArr.length; i++){
            returnArr[i] = answer.get(i);
        }
        
        return returnArr;
    }
}
```

수포자 3인방이 각자 찍는 방식이 다른 데, 특정 수열로 찍는다고 한다.(기계인가?)

첫째 수포자는 [ 1,2,3,4,5 ]
둘째 수포자는 [ 2,1,2,3,2,4,2,5 ]
셋째 수포자는 [ 3,3,1,1,2,2,4,4,5,5 ]

위 순서대로 찍는다.

여기서 답이 주어졌을 때 제일 많이 맞춘 사람에 대해 리턴하면 된다.

각각의 사람에게 index를 주고 문제 수만큼 for문을 계속 돌린 다음에 정답을 주고 맞으면 카운트를 셌다.

이후에는 Math.max() 메소드를 사용해 가장 많이 맞춘 사람을 골라내고, 해당 인원(들)을 리스트에 넣었다.(최대 점수 인원이 몇 명인지 몰라서 배열 크기를 특정할 수 없다.)

리스트의 크기를 센 다음에 배열에 순서대로 넣고 리턴하면 끝!
