# SWExpertAcademy - 단어가 등장하는 횟수_D4

## - 문제내용

책의 내용이 주어질 때, 특정 단어가 등장하는 횟수를 구하라.

## - 문제풀이

이 문제를 풀기위해서는 해시를 사용한다.
다음과 같은 성질을 가지는 함수 f를 해시라고 한다.

주로 문자열을 변수로 가지고, 답이 0부터 N까지의 함수이다.
여러번 실행 해도 같은 결과가 나온다.
다른 값을 넣으면 다른 값이 나올 확률이 높다.
물론 해시 충돌을 방지하기 위해 체이닝 등의 기법을 사용하면 된다.
처음부터 충돌 확률이 낮게끔 해시 코드를 리턴하는게 가장 좋다.

체이닝 말고 다른 방법은 해시 코드를 두개를 반환해서, 두개 모두 같아야 같은 입력이다라고 판단하는 방법도 있다.

또한 해시 함수 f의 return값을 unsigned long long을 쓰면 값이 범위를 넘어갈 때 자연스레 2^64를 mod 값으로 사용하게 된다. (물론 Java에는 없으므로, long으로 대체해서 사용하자.)

다음 코드는 SW Expert Academy에 있는 Reference Code이다.

```java
import java.util.Scanner;
 
 
class Hashtable
{
    class Hash {
        String key;
        String data;
    }
 
    int capacity;
    Hash tb[];
     
    public Hashtable(int capacity){
        this.capacity = capacity;
        tb = new Hash[capacity];
        for (int i = 0; i < capacity; i++){
            tb[i] = new Hash();
        }
    }
     
    private int hash(String str)
    {
        int hash = 5381;
         
        for (int i = 0; i < str.length(); i++)
        {
            int c = (int)str.charAt(i);
            hash = ((hash << 5) + hash) + c;
        }
        if (hash < 0) hash *= -1;
        return hash % capacity;
    }
     
    public String find(String key){
        int h = hash(key);
        int cnt = capacity;
        while(tb[h].key != null && (--cnt) != 0)
        {
            if (tb[h].key.equals(key)){
                return tb[h].data;
            }
            h = (h + 1) % capacity;
        }
        return null;
    }
     
    boolean add(String key, String data)
    {
        int h = hash(key);
        while(tb[h].key != null)
        {
            if (tb[h].key.equals(key)){
                return false;
            }
            h = (h + 1) % capacity;
        }
        tb[h].key = key;
        tb[h].data = data;
        return true;
    }
}
```

이 문제에 해시를 쓰기 위해서는, 우리가 찾고자하는 단어의 길이만큼의 부분문자열에 대한 해시코드를 구해서 단어 해시코드와 동일한지 확인해야한다.

그럼 여기서 단어 길이만큼의 부분문자열을 빠르게 구할 수 있어야하는데, 이때 해시값을 응용하면 된다.
만약 해시 함수가 다음과 같이 구한다면,
f(String) = char * d^L-1 + ... + char

f(ABCDE) = A * d^4 + B * d^3 + C * d^2 + D * d^1 + E
f(ABCDEF) = A * d^5 + B * d^4 + C * d^3 + D * d^2 + E * d^1 + F

f(BCDEF) = f(ABCDEF) - A * d^5

위의 방법을 사용하면, 맨 처음 해시 값을 구할 때만 O(N), 그 다음부터는 O(1)만큼 구할 수 있다.



### + 추가 문제

KMP로도 풀 수 있다.