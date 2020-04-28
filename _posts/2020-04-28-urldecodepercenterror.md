---
title: "URLDecoder: Illegal hex characters in escape (%) pattern"
date: 2020-04-28
categories: posts
tags: ["javascript"]
---

Ajax 요청을 보내던 중 "URLDecoder: Illegal hex characters in escape (%) pattern" 라는 에러가 발생했다.

무슨 에러인가 하고 보니 % 과 16진수 문자열에 관한 내용인거 같다.

찾아보니 URL Decode 과정에서는 % 뒤에 오는 문자열을 16진수로 인식하는데 이 문자열이 변환이 되지 않았을때 발생하는 오류라고 한다.

URL 에서 %가 뭍은 문자열을 Escape 문자열이라고 한다.

왜 Escape 문자열을 사용하는건가? 라는 질문에는 다음과 같이 간단하게 답할 수 있다.

예를들어 GET 방식으로 파라미터를 보낼 때 다음과 같이 파라미터 값에 공백 값이 하나 있다고 치자

ex) ?key=valu e 

이 경우 요청을 받는 곳에서는 온전하게 "valu e" 라는 값을 받아오지 못한다. 즉 URL 에 포함된 특수한 문자들에 대해선 인식을 하지 못하는 것이다.

그래서 특수문자를 사용했다는것을 알려주어야 하는데, 이때 사용하는게 Escape 문자이다.

위 예에서 "valu e" 를 인식해주기 공백을 나타내는 Escape 문자열인 "%20" 을 공백부분에 바꿔넣는다.

ex) ?key=valu%20e

그러면 URL Decoder 에서는 자연스레 공백으로 인식할 것이다.

에러인 "%" 에 대해서도 아래 표에서 나왔듯이 "%25" 로 바꿔주면 에러가 나지 않을 것이다.

<h3><b> Escape 특수문자 표 </b> </h3>

<table>
  <tr>
    <th>공백</th>
    <td>%20</td>
    <th>`</th>
    <td>%60</td>
    <th>-</th>
    <td>%2D</td>
    <th><</th>
    <td>%3C</td>
  </tr>
  <tr>
    <th>[</th>
    <td>%5B</td>
    <th>~</th>
    <td>%7E</td>
    <th>_</th>
    <td>%5F</td>
    <th>></th>
    <td>%3E</td>
  </tr>
  <tr>
    <th>]</th>
    <td>%5D</td>
    <th>!</th>
    <td>%21</td>
    <th>=</th>
    <td>%3D</td>
    <th>.</th>
    <td>%2E</td>
  </tr>
  <tr>
    <th>{</th>
    <td>%7B</td>
    <th>@</th>
    <td>%40</td>
    <th>+</th>
    <td>%2B</td>
    <th>,</th>
    <td>%2C</td>
  </tr>
  <tr>
    <th>}</th>
    <td>%7D</td>
    <th>#</th>
    <td>%23</td>
    <th>\</th>
    <td>%5C</td>
    <th>?</th>
    <td>%3F</td>
  </tr>
  <tr>
    <th>(</th>
    <td>%28</td>
    <th>$</th>
    <td>%24</td>
    <th>|</th>
    <td>%7C</td>
  </tr>
  <tr>
    <th>)</th>
    <td>%29</td>
    <th>%</th>
    <td>%25</td>
    <th>/</th>
    <td>%2F</td>
  </tr>
  <tr>
    <th></th>
    <td></td>
    <th>^</th>
    <td>%5E</td>
    <th>:</th>
    <td>%3A</td>
  </tr>
  <tr>
    <th></th>
    <td></td>
    <th>&</th>
    <td>%26</td>
    <th>;</th>
    <td>%3B</td>
  </tr>
  <tr>
    <th></th>
    <td></td>
    <th>*</th>
    <td>%2A</td>
    <th>'</th>
    <td>%27</td>
  </tr>
  <tr>
    <th></th>
    <td></td>
    <th></th>
    <td></td>
    <th>"</th>
    <td>%22</td>
  </tr>
</table>
