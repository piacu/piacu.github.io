---

title: "[Java] 프로그래머스: 프린터"
author: piacu
date: 2023-02-15 23:11:00 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers, java]

---

###### 문제 설명

일반적인 프린터는 인쇄 요청이 들어온 순서대로 인쇄합니다. 그렇기 때문에 중요한 문서가 나중에 인쇄될 수 있습니다. 이런 문제를 보완하기 위해 중요도가 높은 문서를 먼저 인쇄하는 프린터를 개발했습니다. 이 새롭게 개발한 프린터는 아래와 같은 방식으로 인쇄 작업을 수행합니다.

```
1. 인쇄 대기목록의 가장 앞에 있는 문서(J)를 대기목록에서 꺼냅니다.
2. 나머지 인쇄 대기목록에서 J보다 중요도가 높은 문서가 한 개라도 존재하면 J를 대기목록의 가장 마지막에 넣습니다.
3. 그렇지 않으면 J를 인쇄합니다.
```

예를 들어, 4개의 문서(A, B, C, D)가 순서대로 인쇄 대기목록에 있고 중요도가 2 1 3 2 라면 C D A B 순으로 인쇄하게 됩니다.

내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 알고 싶습니다. 위의 예에서 C는 1번째로, A는 3번째로 인쇄됩니다.

현재 대기목록에 있는 문서의 중요도가 순서대로 담긴 배열 priorities와 내가 인쇄를 요청한 문서가 현재 대기목록의 어떤 위치에 있는지를 알려주는 location이 매개변수로 주어질 때, 내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 return 하도록 solution 함수를 작성해주세요.

##### 제한사항

- 현재 대기목록에는 1개 이상 100개 이하의 문서가 있습니다.
- 인쇄 작업의 중요도는 1~9로 표현하며 숫자가 클수록 중요하다는 뜻입니다.
- location은 0 이상 (현재 대기목록에 있는 작업 수 - 1) 이하의 값을 가지며 대기목록의 가장 앞에 있으면 0, 두 번째에 있으면 1로 표현합니다.

##### 입출력 예

| priorities         | location | return |
| ------------------ | -------- | ------ |
| [2, 1, 3, 2]       | 2        | 1      |
| [1, 1, 9, 1, 1, 1] | 0        | 5      |

##### 입출력 예 설명

예제 #1

문제에 나온 예와 같습니다.

예제 #2

6개의 문서(A, B, C, D, E, F)가 인쇄 대기목록에 있고 중요도가 1 1 9 1 1 1 이므로 C D E F A B 순으로 인쇄합니다.

### 풀이 방법

```java
import java.util.*;

class Solution {
    class Print{
        int num;
        int loc;
        
        Print(int num, int loc){
            this.num = num;
            this.loc = loc;
        }
    }
    
    public int solution(int[] priorities, int location) {
        int answer = 0;
        Queue<Print> q = new LinkedList<>();
        List<Integer> list = new ArrayList<>();
        
        for(int i=0; i<priorities.length; i++){
            q.add(new Print(priorities[i], i));
            list.add(priorities[i]);
        }
        
        list.sort(Comparator.reverseOrder());
        
        while(true){
            Print now = q.poll();
            
            if(now.num == list.get(answer)){
                answer++;
                if(now.loc == location) return answer;
            } else {
                q.add(now);
            }
        }
        
    }
}
```

프린터의 출력 순서에 대한 문제이다.

n번째 문서는 몇 번째로 출력 되는가? 에 대해 구하는 문제인데, 다음에 출력 될 문서보다 큰 수가 큐 내에 존재한다면 해당 수가 출력 될 때까지 다시 큐 뒷부분에 넣게 된다.

이를 풀기 위해서 queue에다가 모든 수를 넣고(n번째 문서의 location을 알기 위해 Print 클래스를 만들었다.) 

또한 리스트에도 넣은 후 내림차순 정렬을 하여 큰 숫자부터 정렬하였다.

이후에 무한 반복 while 문을 돌려 queue에서 하나씩 뽑은 후 list와 같은 숫자이면 큐에서 삭제하고 참조하는 리스트의 요소를 하나 뒤로 넘기고, 다른 숫자라면 다시 queue에 넣는 방식으로 해결했다.

queue를 쓰지 않고 수학적인 방법으로 풀려고 엄청난 시간을 해맸는데 정답은 queue였다...
