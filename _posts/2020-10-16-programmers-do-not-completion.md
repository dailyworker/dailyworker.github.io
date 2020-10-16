---
title: "[PS 프로그래머스] - 완주하지 못한 선수"
date: 2020-10-16 18:16:00 +0900
tags:
  - ProblemSolving
  - Java
  - Algorithm
---

## STEP 1. 문제

수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.

마라톤에 참여한 선수들의 이름이 담긴 배열 participant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.

### 제한사항

- 마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
- completion의 길이는 participant의 길이보다 1 작습니다.
- 참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
- **참가자 중에는 동명이인이 있을 수 있습니다.**

## STEP 2. 구현

### 초기 아이디어

:point_right: **Completion과 Participant의 차집합을 구한 후 동명이인 처리를 따로 하자!**

1. 입력 배열 2개를 HashSet으로 변경하여 중복을 제거한다.
2. Participant를 Set으로 만들었을 때 배열의 크기보다 작으면 동명이인 존재
    - 차집합이 존재한다면 → 동명이인 존재 X
    - 차집합이 존재하지 않는다면 → 동명이인 존재 O
3. 차집합이 존재하면 해당 Set을 `String.join(",")`하여 리턴하면 된다.
4. 차집합이 존재하지 않는다면, `Paritipant, Completion` 오름차순 정렬 후 원소를 비교하여 다른 값이 존재하는 부분에 리턴 
    - 다른 값이 존재하지 않으면 맨 마지막 값 동명이인이라는 뜻

```java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
	    Set<String> answer = new HashSet<String>();
        Set<String> partSet = new HashSet<String>(Arrays.asList(participant));
        Set<String> compSet = new HashSet<String>(Arrays.asList(completion));
			
        answer = getDifference(partSet, compSet);		
        
        if(answer.size() == 0){
            return getDuplicated(participant, completion);
        }
        return String.join(",", answer);
    }
		
    public Set<String> getDifference(Set<String> partSet, Set<String> compSet){
        Set<String> copySet = new HashSet<String>();
        copySet.addAll(partSet);	
        copySet.removeAll(compSet);
        return copySet;
    }

    public String getDuplicated(String[] participant, String[] completion){
        Arrays.sort(participant);
        Arrays.sort(completion);
        String answer = "";
        
        for(int i = 0; i < participant.length - 1; i++){
            if(Arrays.asList(participant[i]).contains(completion[i])){
                continue;
            } else {
                return answer = participant[i];
            } 
        }     
        return answer = participant[participant.length - 1];
    }
}
```

### 더 효율적인 아이디어

굳이, 차집합을 구할 필요가 있을까?!

무슨 뜻이냐면, 아주 단순하게 오름차순으로 정렬한 뒤에 다른 값만 리턴해도 되지 않을까이다. 

이게 가능한 이유는 다음과 같다.

1. **완주하지 못한 선수는 단 1명**이다.
2. 또한, **Participant와 Completion의 원소 개수의 차이는 단 1개**이다.
3. 따라서 오름차순으로 두 개의 배열을 정렬한 뒤에 모든 인덱스를 비교하면서 다른 값을 리턴하면 그 인원이 완주하지 못한 선수이다. 
    - 배열의 차가 1이므로 `length - 1`만큼 돌면된다.
    - `!participant.equals(completion)` 라면 완주못한 사람이다.
        - 만일 `length - 1`만큼 인덱스를 순회했는데 없다면 마지막 인덱스가 완주 못한 사람이다.

따라서, 아래와 같이 차집합을 구하는 부분을 없애도 결과는 맞다고 나온다.

```java
class Solution {
    public String solution(String[] participant, String[] completion) {
        return getDuplicated(participant, completion);
    }
		
    public String getDuplicated(String[] participant, String[] completion){
           Arrays.sort(participant);
           Arrays.sort(completion);
           String answer = "";
          
           for(int i = 0; i < participant.length - 1; i++){
               if(Arrays.asList(participant[i]).contains(completion[i])){
                   continue;
               } else {
                   return answer = participant[i];
               } 
           }     
           return answer = participant[participant.length - 1];
    }
}
```

### 다른사람의 아이디어

해당 문제는 프로그래머스의 레벨1 연습문제 중에 카테고리가 **해시**로 되어있다. 나는 위와 같이 구현했지만, 많은 사람들이 `HashMap`을 활용하여 푼 것을 알 수 있었다.

JSON을 처리할 때만 써봤었지 이 문제를 보고 바로 `HashMap`을 사용한다는 아이디어는 미처 착안하지 못했기에 적어본다.

일단 HashMap은 Key, Value의 쌍으로 저장이된다.

따라서 HashMap을 이용한 아이디어는 다음과 같을 것이다.

**Participant를 Map에 넣고 Value를 1로 초기화한 후에 Completion를 넣으면서 Value를 다시 0으로 초기화한다. 즉, 0인 경우에 완주 못 한 선수인 것이다.**

```java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        String answer = "";
        HashMap<String, Integer> map = new HashMap<>();
        /***
            * 해시 맵을 생성한 뒤에 participant 배열을 해시 맵에 put하는 부분
            * 여기서, 해시 맵에 넣으려고 하는 키가 없다면 getOrDefault() 메소드를 활용하여 default value로 0으로 초기화 
        */
        for (String player : participant) 
            map.put(player, map.getOrDefault(player, 0) + 1);
        /***
            * 위와 동일하게 completion 배열을 해시 맵에 put하는 부분
            * 여기서 이미 동일한 키가 존재하는 값을 해시 맵에 put하려고 할 때, value의 값을 -1 시킨다.
        */
        for (String winner : completion)  
            map.put(winner, map.get(winner) - 1);
        /***
            * 위의 로직에 따라서 0이 아닌 값을 가진 키가 완주하지 못한 선수이다.
        */
        for (String key : map.keySet()) {
            if (map.get(key) != 0){
                answer = key;
            }
        }
        return answer;
    }
}
```

## STEP 3. 회고

앞으로 꾸준히 문제를 더 많이 풀어봐야 할 것 같다.