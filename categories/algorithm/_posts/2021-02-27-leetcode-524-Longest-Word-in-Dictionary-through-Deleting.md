# leetcode(524) - Longest Word in Dictionary through Deleting (Medium)

## - Problem

Given a string s and a string array dictionary, return the longest string in the dictionary that can be formed by deleting some of the given string characters. If there is more than one possible result, return the longest word with the smallest lexicographical order. If there is no possible result, return the empty string.

## - Example
Input: s = "abpcplea", dictionary = ["ale","apple","monkey","plea"]
Output: "apple"

Input: s = "abpcplea", dictionary = ["a","b","c"]
Output: "a"

## - Answer

이 문제는 두 가지 조건을 만족하면 된다.
- subsequence
- the longest word with the smallest lexicographical order

따라서 `dictionary` 배열을 탐색하면서 주어진 `s`에 subsequence 하면서, 동시에 가장 긴 단어 중 사전순으로 앞선 것을 결과로 내보내면 된다.
전체 dictionary를 탐색하는 비용 N (dictionary.length), 각 단어의 subsequence 유무를 판별하기 위한 비용 M (s.length) 만큼 소모한다.

코드로 표현하면 다음과 같다.

```typescript
function isSubsequence(x: string, y: string): boolean {
  let count = 0;
  for(let i=0; i<y.length && count<x.length; i++) {
    if(x[count] === y[i]) count++;
  }
  return count === x.length;
};

function findLongestWord(s: string, dictionary: string[]): string {
  return dictionary.reduce((prev, next) => {
    if(isSubsequence(next, s)) {
      if(next.length > prev.length || next.length === prev.length && next.localeCompare(prev) < 0)
        return next;
    }
    return prev;
  }, "");
};
```