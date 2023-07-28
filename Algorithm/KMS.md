# KMS 문자열 알고리즘
오늘 Kms를 처음 보았다. 문자열 비교 알고리즘 문제를 푸는데 아무리 생각해도 완전탐색으로는 못풀고 기존 dp방식으로도 어려워보이고, 최대한 건너띄어야한다는 생각만 했는데

답은 kms알고리즘 처럼 이미 비교한 문자열에 대해서는 다시 반복하는 일 없이 건너띄는 방식이다.

---

    "ABCD....~~~....FDCDEA" 라는 문자열 안에서 "FSA..~~..DAS"가 몇번 나오는지 찾아라
내가 마주한 문제였다. 내가 기존에 아는 문자열 찾는 방식으로는 탐색할 문자열(N)과 찾아야할 문자열(M)을 돌면 O(NxM)이 나올수 밖에 없다. 하지만 KMS 방식은 O(N+M)으로 해결한다.

![스크린샷 2023-07-27 오후 9 30 40](https://github.com/YongNyeo/TIL/assets/109174778/89099bb3-f829-4ed1-9f15-4b0fc5e60ba5)

기존의 방식은 다음과 같이 인덱스를 한칸씩 옮겨가며 모든 케이스를 체크한다.

### 💡 KMS는 어떻게?

![스크린샷 2023-07-27 오후 9 34 21](https://github.com/YongNyeo/TIL/assets/109174778/0976a3d4-bc74-466a-ad75-2d6f62628510)

첫 케이스를 확인후 다음 칸(인덱스)로 옮기는것이 아니라, 첫 케이스에서 다음에 어디로 가야 효율적인지 체크한 후 그 칸수만큼 넘어간다.

#### ➡️ 처음

![IMG_7E5C5C39700E-1](https://github.com/YongNyeo/TIL/assets/109174778/be479370-fbb3-4662-905f-b931a39c6739)

먼저  접두사와 접미사 개념을 이용해야한다. 앞뒤가 같은 부분을 알아야 우리가 몇 칸을 건너띄어갈지 알수 있다. 

해당 예시는 ABACABA 라는 문자열이 겹쳤던 경우에 케이스를 나눠 접두사 접미사를 이용하는것이다. 하지만 매 계산할때마다 접두사 접미사를 구해서 최대중복 개수를 계산할수는 없다.

<img width="620" alt="스크린샷 2023-07-28 오후 5 31 20" src="https://github.com/YongNyeo/TIL/assets/109174778/21457078-0f5a-48b5-8e58-abce4a255266">

그래서 이러한 테이블을 이용할것이다. A가 겹치는 경우부터 ABACABAAC 모두 겹치는 각각의 케이스마다 접두,접미가 최대로 길어지는 경우에 우리가 몇칸씩 띄어가야하는지 계산한 테이블이다.

<img width="570" alt="스크린샷 2023-07-28 오후 5 30 45" src="https://github.com/YongNyeo/TIL/assets/109174778/91a62738-3fd5-453b-bec8-da1504c5df1f">

일치하는 길이-최대 경계너비(접두 접미가 같은 최대 길이) == 이동해야할 거리 가 된다.

- ###  겹치는 부분마다 이동해야할 거리를 작성하는 코드

            int[] pi = new int[m];
            int answer = 0;
            int idx = 0; // 일치한 글자 수

            for (int i = 1; i < m; i++) {
                // 맞는 위치가 나올 때까지 idx - 1칸의 공통 부분 위치로 이동
                while (idx > 0 && word.charAt(i) != word.charAt(idx))
                    idx = pi[idx - 1];

                // 맞을 경우 i 길이 문자열의 공통 길이는 idx위치 +1
                if (word.charAt(i) == word.charAt(idx)) {
                    idx += 1;
                    pi[i] = idx;
                }
            }

- ### 출발해서 맞는 위치가 나올때까지 이동하는 코드

            idx = 0;
            for (int i = 0; i < n; i++) {
                // 맞는 위치가 나올 때까지 j - 1칸의 공통 부분 위치로 이동
                while (idx > 0 && content.charAt(i) != word.charAt(idx)) {
                    idx = pi[idx - 1];
                }
                // 맞는 경우
                if (content.charAt(i) == word.charAt(idx)) {
                    if (idx == m - 1) {
                        answer++;
                        idx = pi[idx];
                    }
                    // 맞았지만 패턴이 끝나지 않았다면 idx를 하나 증가
                    else
                        idx++;
                }
            }


---

### 📘 Reference
- https://velog.io/@uuuouuo/kmp
- https://chanhuiseok.github.io/posts/algo-14/#-%EB%AC%B8%EC%9E%90%EC%97%B4%EC%9D%84-%EA%B2%80%EC%83%89%ED%95%98%EB%A0%A4%EB%A9%B4--%ED%8C%A8%ED%84%B4-%EB%A7%A4%EC%B9%AD

개인적으로 최근에 배운 알고리즘중에 가장 어려웠다. 코드로 흡수하는데 몇시간은 걸린듯.....
