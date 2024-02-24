---

layout: post
title: "[Java] 프로그래머스: H-Index"
tags: [algorithm, programmers, java]

---

###### 문제 설명

H-Index는 과학자의 생산성과 영향력을 나타내는 지표입니다. 어느 과학자의 H-Index를 나타내는 값인 h를 구하려고 합니다. 위키백과[1](https://school.programmers.co.kr/learn/courses/30/lessons/42747#fn1)에 따르면, H-Index는 다음과 같이 구합니다.

어떤 과학자가 발표한 논문 `n`편 중, `h`번 이상 인용된 논문이 `h`편 이상이고 나머지 논문이 h번 이하 인용되었다면 `h`의 최댓값이 이 과학자의 H-Index입니다.

어떤 과학자가 발표한 논문의 인용 횟수를 담은 배열 citations가 매개변수로 주어질 때, 이 과학자의 H-Index를 return 하도록 solution 함수를 작성해주세요.

##### 제한사항

- 과학자가 발표한 논문의 수는 1편 이상 1,000편 이하입니다.
- 논문별 인용 횟수는 0회 이상 10,000회 이하입니다.

##### 입출력 예

| citations       | return |
| --------------- | ------ |
| [3, 0, 6, 1, 5] | 3      |

##### 입출력 예 설명

이 과학자가 발표한 논문의 수는 5편이고, 그중 3편의 논문은 3회 이상 인용되었습니다. 그리고 나머지 2편의 논문은 3회 이하 인용되었기 때문에 이 과학자의 H-Index는 3입니다.

##### 문제가 잘 안풀린다면😢

힌트가 필요한가요? [코딩테스트 연습 힌트 모음집]으로 오세요! → [클릭](https://school.programmers.co.kr/learn/courses/14743?itm_content=lesson42747)

※ 공지 - 2019년 2월 28일 테스트 케이스가 추가되었습니다.

### 풀이 방법

```java
import java.util.*;

class Solution {
    public int solution(int[] citations) {
        int answer = 0;
        
        Arrays.sort(citations);
        
        for(int i=citations.length-1; i>=0; i--){
            answer++;
            
            if(answer > citations[i]) {
                answer--;
                break;
            }
        }
        
        return answer;
    }
}
```

인용 횟수를 나타내는 citations 배열을 정렬한 후에 내림차순으로 실행하기 위해 citations의 반대편부터 참조하는 반복문을 돌린다.

논문의 수(answer)의 값은 하나씩 올라가는 데, 논문의 수가 인용 횟수(citations[i])보다 작아질 때까지 수행하며 if(answer > citations[i]) 에서는 인용 횟수보다 논문 횟수가 작아진 것이기 때문에 1을 빼준 최댓값을 리턴하면 답이 나오게 된다.
