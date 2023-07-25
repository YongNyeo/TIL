# LCS (Longest Common Subsequence) 가장 긴 공통 부분 수열

우선 LCS 문제는 두가지 분류로 나뉜다.

1. 가장 긴 공통 문자열(Substring)
2. 가장 긴 공통 부분 수열

#### aebcd 와 cabce 라는 비교 문자열이 있을때, 1은 공통된 구간이 연속으로 이뤄져서 'bc'지만, 2는 부분 수열로 끊어져도 괜찮아서 'abc'가 된다.

<img width="739" alt="스크린샷 2023-07-25 오후 2 15 51" src="https://github.com/YongNyeo/TIL/assets/109174778/80e3fefa-ca92-470f-b2fe-70d292033fba">

추가 예시를 보면 이해가 간다.

---
#### 더 간단히 구현되는 1번부터 보자.

<img width="372" alt="스크린샷 2023-07-25 오후 2 24 02" src="https://github.com/YongNyeo/TIL/assets/109174778/c15b4166-fbcc-4a12-a30c-c1b3aac548be">

- 가장긴 공통 문자열의 경우 결과값이 길어지려면 반드시 이전 글자에서 연결되어야한다.
- 따라서 글자가 같다면 왼쪽 위의 숫자 +1이고, 글자가 다르다면 숫자를 0부터 다시 시작해야 한다.
- 최장 길이 값이 좌표 끝에서 끝난다는 보장이 없다.

        for (int i = 1; i <= a.length(); i++) {
            for (int j = 1; j < b.length(); j++) {
                    map[i][j] = map[i-1][j-1]+1;
                    cnt = Math.max(map[i][j],cnt);
                }
             }
         }



---

#### 2. 가장 긴 공통 부분 수열

<img width="369" alt="스크린샷 2023-07-25 오후 2 27 26" src="https://github.com/YongNyeo/TIL/assets/109174778/1454e3fa-1441-4ea2-9147-84f8c2268571">

- 1번과 2번의 공통점은 같은 글자일때 왼쪽 위의 숫자+1이 된다는점이다.
- ⭐ 차이점은 다른 글자일때 이전 최장 문자열의 길이를 이어간다는것이다.
- 부분 수열을 구할때는 이전과 달라도 그 이전 정보를 갖고 이어가야하므로 대각선 값이 아닌 위 칸 또는 왼쪽 칸의 값을 가져간다.


        for (int i = 1; i <= a.length(); i++) {
            for (int j = 1; j <= b.length(); j++) {
                if (a.charAt(i - 1) == b.charAt(j - 1)) {
                    map[i][j] = map[i-1][j-1]+1;
                }
                else { //다르다면
                    map[i][j] = Math.max(map[i - 1][j], map[i][j - 1]);
                }
            }
        }


📘Reference
- [링크1](https://velog.io/@emplam27/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-LCS-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-Longest-Common-Substring%EC%99%80-Longest-Common-Subsequence) 
