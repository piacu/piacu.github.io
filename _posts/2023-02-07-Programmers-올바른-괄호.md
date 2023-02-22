---

title: "[Java] 프로그래머스: 올바른 괄호"
author: piacu
date: 2023-02-07 18:39:00 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers, java]

---

###### 문제 설명

괄호가 바르게 짝지어졌다는 것은 '(' 문자로 열렸으면 반드시 짝지어서 ')' 문자로 닫혀야 한다는 뜻입니다. 예를 들어

- "()()" 또는 "(())()" 는 올바른 괄호입니다.
- ")()(" 또는 "(()(" 는 올바르지 않은 괄호입니다.

'(' 또는 ')' 로만 이루어진 문자열 s가 주어졌을 때, 문자열 s가 올바른 괄호이면 true를 return 하고, 올바르지 않은 괄호이면 false를 return 하는 solution 함수를 완성해 주세요.

##### 제한사항

- 문자열 s의 길이 : 100,000 이하의 자연수
- 문자열 s는 '(' 또는 ')' 로만 이루어져 있습니다.

------

##### 입출력 예

| s        | answer |
| -------- | ------ |
| "()()"   | true   |
| "(())()" | true   |
| ")()("   | false  |
| "(()("   | false  |

##### 입출력 예 설명

입출력 예 #1,2,3,4
문제의 예시와 같습니다.

### 풀이 방법

```java
import java.util.*;

class Solution {
    boolean solution(String s) {
        char[] chars = s.toCharArray();
        Stack<Character> stack = new Stack<>();
        int slen = s.length();
        
        for(int i=0; i<slen; i++){
            if(!stack.isEmpty()){
                if(stack.peek() == 40 && chars[i] == 41){
                    stack.pop();
                    continue;
                }
            }
            stack.add(chars[i]);
        }

        return stack.isEmpty() ? true : false;
    }
}
```

stack을 이용한 풀이. 효율성 면에서 시간초과가 발생했다.

```java
class Solution {
    boolean solution(String s) {
        int sLen = s.length();
        int check = 0;
        
        for(int i=0; i<sLen; i++){
            if(check < 0) return false;
            
            if(s.charAt(i) == '(') {
                check++;
            } else if(s.charAt(i) == ')') {
                check--;
            }
        }

        return check == 0 ? true : false;
    }
}
```

단순 check 변수를 이용한 풀이. 왼쪽 괄호가 나왔을 때는 +가, 오른쪽 괄호가 나왔을 때는 -가 더해지도록 만들었다. 결과적으로 스택에 삽입, 삭제 등의 연산을 할 필요가 없어서 효율성이 완전 좋아졌다 !

#### 둘의 시간 차이

| ![stack](https://user-images.githubusercontent.com/26267376/217208571-85e5102f-e3c6-4acb-963b-b420ed4734a4.PNG) | ![check](https://user-images.githubusercontent.com/26267376/217208465-e4300e1b-debb-4f0e-9a73-d420716cc359.PNG) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| stack을 사용한 코드(효율성 면에서 시간초과)                  | check 변수를 사용한 코드(stack 이용보다 속도 빨라짐)         |
