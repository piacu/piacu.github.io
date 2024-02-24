---

layout: post
title: "[Java] 프로그래머스: 기능개발"
tags: [algorithm, programmers, java]

---

###### 문제 설명

프로그래머스 팀에서는 기능 개선 작업을 수행 중입니다. 각 기능은 진도가 100%일 때 서비스에 반영할 수 있습니다.

또, 각 기능의 개발속도는 모두 다르기 때문에 뒤에 있는 기능이 앞에 있는 기능보다 먼저 개발될 수 있고, 이때 뒤에 있는 기능은 앞에 있는 기능이 배포될 때 함께 배포됩니다.

먼저 배포되어야 하는 순서대로 작업의 진도가 적힌 정수 배열 progresses와 각 작업의 개발 속도가 적힌 정수 배열 speeds가 주어질 때 각 배포마다 몇 개의 기능이 배포되는지를 return 하도록 solution 함수를 완성하세요.

##### 제한 사항

- 작업의 개수(progresses, speeds배열의 길이)는 100개 이하입니다.
- 작업 진도는 100 미만의 자연수입니다.
- 작업 속도는 100 이하의 자연수입니다.
- 배포는 하루에 한 번만 할 수 있으며, 하루의 끝에 이루어진다고 가정합니다. 예를 들어 진도율이 95%인 작업의 개발 속도가 하루에 4%라면 배포는 2일 뒤에 이루어집니다.

##### 입출력 예

| progresses               | speeds             | return    |
| ------------------------ | ------------------ | --------- |
| [93, 30, 55]             | [1, 30, 5]         | [2, 1]    |
| [95, 90, 99, 99, 80, 99] | [1, 1, 1, 1, 1, 1] | [1, 3, 2] |

##### 입출력 예 설명

입출력 예 #1
첫 번째 기능은 93% 완료되어 있고 하루에 1%씩 작업이 가능하므로 7일간 작업 후 배포가 가능합니다.
두 번째 기능은 30%가 완료되어 있고 하루에 30%씩 작업이 가능하므로 3일간 작업 후 배포가 가능합니다. 하지만 이전 첫 번째 기능이 아직 완성된 상태가 아니기 때문에 첫 번째 기능이 배포되는 7일째 배포됩니다.
세 번째 기능은 55%가 완료되어 있고 하루에 5%씩 작업이 가능하므로 9일간 작업 후 배포가 가능합니다.

따라서 7일째에 2개의 기능, 9일째에 1개의 기능이 배포됩니다.

입출력 예 #2
모든 기능이 하루에 1%씩 작업이 가능하므로, 작업이 끝나기까지 남은 일수는 각각 5일, 10일, 1일, 1일, 20일, 1일입니다. 어떤 기능이 먼저 완성되었더라도 앞에 있는 모든 기능이 완성되지 않으면 배포가 불가능합니다.

따라서 5일째에 1개의 기능, 10일째에 3개의 기능, 20일째에 2개의 기능이 배포됩니다.

※ 공지 - 2020년 7월 14일 테스트케이스가 추가되었습니다.

### 풀이 방법

```java
import java.util.*;

class Solution {
    public int[] solution(int[] progresses, int[] speeds) {
        List<Integer> list = new ArrayList<>();
        int index = 0;
        
        while(index < progresses.length){
            for(int i=index; i<progresses.length; i++){
                progresses[i] += speeds[i];
            }
            
            int count = 0;
            
            if(progresses[index] >= 100){
                for(int j=index; j<progresses.length; j++){
                    if(progresses[j] < 100) break;
                    
                    index++;
                    count++;
                }
                list.add(count);
            }
        }

        int[] answer = list.stream().mapToInt(Integer::intValue).toArray();
        
        return answer;
    }
}
```

배열로 주어진 각 progresses의 index마다 해당되는 speeds가 있는데 한 사이클을 돌 때마다 progresses[index]는 speeds[index]씩 커진다.

이 때 progresses가 100 이상 된다면 배포가 가능한데, 해당 이전의 index가 100이 되지 않는다면 이전의 index 전부 100 이상이 될 때까지 배포할 수 없다.



이를 풀기 위해 index를 반복문 외부에 만들어서 progresses[index]가 100 이상이 될 때, 이후의 100 이상 되는 요소를 모두 찾고 결과리스트에 저장하게 했다.



결과는 int[] 형태로 반환해야 했기에 결과리스트를 stream으로 answer 배열에 담아 리턴하게 했다.

반복문 또는 stream을 쓰지 않고 해결할 수 있는 방법이 있을 지 고민스럽다.
