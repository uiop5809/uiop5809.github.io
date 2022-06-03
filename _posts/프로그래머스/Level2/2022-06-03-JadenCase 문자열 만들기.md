---
layout: single
title: "[프로그래머스][Level2] 피보나치 수 c++"

categories:
  - Level2

tags:
  - 알고리즘
  - 프로그래머스
  - c++

author_profile: true # 왼쪽부분 프로필을 띄울건지

header:
  overlay_image: https://images.unsplash.com/photo-1501785888041-af3ef285b470?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1350&q=80
  overlay_filter: 0.5 # 투명도

# table of contents
toc: true # 오른쪽 부분에 목차를 자동 생성해준다.
toc_label: " table of content" # toc 이름 설정
toc_icon: "bars" # 아이콘 설정
toc_sticky: true # 마우스 스크롤과 함께 내려갈 것인지 설정
---

문제 링크 [https://programmers.co.kr/learn/courses/30/lessons/12945](https://programmers.co.kr/learn/courses/30/lessons/12945)

## 문제

JadenCase란 모든 단어의 첫 문자가 대문자이고, 그 외의 알파벳은 소문자인 문자열입니다. 단, 첫 문자가 알파벳이 아닐 때에는 이어지는 알파벳은 소문자로 쓰면 됩니다. (첫 번째 입출력 예 참고)
문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.

## 제한사항

- s는 길이 1 이상 200 이하인 문자열입니다.
- s는 알파벳과 숫자, 공백문자(" ")로 이루어져 있습니다.
- 숫자는 단어의 첫 문자로만 나옵니다.
- 숫자로만 이루어진 단어는 없습니다.
- 공백문자가 연속해서 나올 수 있습니다.

## 풀이 과정

다른 문제와 차이점은 str가 공백일 때도 벡터에 넣어준다는 점이다.  
그 이유는 공백문자가 연속해서 나올 수도 있기 때문이다.

일단 처음에 다 소문자로 바꿔준 뒤, 벡터에 넣은 값 인덱스 0값이 소문자라면 대문자로 바꿔준다. 그리고 마지막 인덱스를 제외하고 뒷부분에 공백을 붙여준다.

```c++
#include <string>
#include <vector>

using namespace std;

string solution(string s) {
    string answer = "";
    for(int i = 0; i < s.size(); i++){
        if('A' <= s[i] && s[i] <= 'Z') s[i] += 32;
    }
    vector<string> v;
    string str = "";
    for(int i = 0; i < s.size(); i++){
        if(s[i] == ' '){
            v.push_back(str);
            str = "";
        }
        else str += s[i];
    }
    v.push_back(str);

    for(int i = 0; i < v.size(); i++){
        if('a' <= v[i][0] && v[i][0] <= 'z') v[i][0] -= 32;
        answer += v[i];
        if(i != v.size() - 1) answer += ' ';
    }
    return answer;
}
```
