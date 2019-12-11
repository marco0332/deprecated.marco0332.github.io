# Programmers LV2 - 주식가격

## 코드

```java
class Solution {
    public int[] solution(int[] prices) {
        int[] answer = new int[prices.length];
        int befo = 0;
        for(int index=0; index<prices.length; index++) {
            if(befo > prices[index]) {
                fill(answer, prices, prices[index], index);
            }
            befo = prices[index];
        }
        int index = prices.length - 1;
        int cur = index;
        while(cur > -1) {
            if(answer[cur] == 0) {
                answer[cur] = index-cur;
            }
            cur--;
        }
        
        return answer;
    }
    
    public void fill(int[] answer, int[] prices, int rule, int index) {
        int cur = index-1;
        while(cur > -1 && prices[cur] > rule) {
            if(answer[cur] == 0) {
                answer[cur] = index-cur;
            }
            cur--;
        }
    }
}
```

## 다른 사람 풀이 참고

```java
class Solution {
    public int[] solution(int[] prices) {
        int[] answer = new int[prices.length];

        for(int i = 0; i < prices.length; i++)
        {
            for(int j=i+1; j < prices.length; j++)
            {
                if(prices[i] > prices[j])
                {
                    answer[i] = j-i;
                    break;
                }
                else
                    answer[i] = j-i;
            }
        }

        return answer;
    }
}
```

