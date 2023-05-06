# JSTL

<br>

JSTL의 자바서버 페이지 표준 태그 라이브러리(JavaServer Pages Standard Tag Library)의 줄인말이다.

JSTL은 JavaEE 기반의 웹 어플리케이션 개발 플랫폼을 위한 컴포넌트 모음이다. JSTL은 XML데이터 처리와 조건문, 반복문 등과 같은 일을 처리하기 위한 JSP 태그 라이브러리를 추가하여 JSP 사양을 확장했다.

JSTL을 사용함으로써 JSP 페이지 내에서 자바 코드를 바로 사용하지 않고 로직을 내장하는 효율적인 방법을 제공한다. 표준화된 태그 셋을 사용함으로써 코드의 유지보수와 응용 소프트웨어 코드와 사용자 인터페이스간의 관심사의 분리를 용이하게 한다.

### JSTL 사용법

<br>

JSTL은 라이브러리이기 때문에 사용하기전에 core를 header 태그 안에 추가해주어야 한다.

```
<% @taglib uri="http://java.sun.com/jstl/core" prefix="c" %>
```

core추가 후 사용법은 다음과 같다.

```
<c:if test=""></c:if>
<c:forEach items=""></c:forEach>
```

JSTL core에 있는 태그 종류와 기능은 다음과 같다.

<br>

|태그명|설명|
|-----|----|
|<c:set>|변수명에 값을 할당|
|<c:out>|변수 값을 출력|
|<c:if>|조건식에 해당하는 블럭과 사용될 scope 설정|
|<c:choose>|다른 언어의 switch와 비슷|
|<c:when>|switch문의 case에 해당|
|<c:otherwise>|switch문의 default에 해당|
|<c:forEach>|다른언어의 loop문 items 속성에 배열을 할당할 수 있음|

<br>


# EL

EL은 표현언어(Expression Language)의 준말이다. EL은 자바 스크립트에서 확정된 Xpath에서 힌트를 얻어 만들어진언어로 값이 없는 변수, 즉 null 에 관대하고 데이터 형 변환을 자동으로 해주는 특징이 있다. 따라서 EL을 사용하면 값이 null이거나 형변환등에 신경을 쓸 필요없이 서버로 전송해서 사용할 수 있다.

<br>


### EL 사용법

<br>

```
${name}
```

EL의 내장 객체의 종류는 다음과 같다.

<br>


|객체명|설명|
|-----|----|
|${pageScope}|page Scope에 접근하기 위한 객체|
|${requestScope}|request Scope에 접근하기 위한 객체|
|${sessionScope}|session Scope에 접근하기 위한 객체|
|${applicationScope}|application Scope에 접근하기 위한 객체|
|${param}|파라미터 값을 가져오기 위한 객체|
|${header}|헤더 값을 가져오기 위한 객체|
|${cookie}|쿠키 값을 가져오기 위한 객체|
|${initParam}|JSP 초기 파라미터를 가져오기 위한 객체|
|${pageContext}|pageContext 객체에 접근하기 위한 객체|

<br>


### JSTL을 사용하는 이유

<br>


JSTL + EL의 조합을 사용함으로써 기존 스크립틀릿을 사용했을 때보다 훨씬 간결하고 가독성 좋은 HTML 코드를 작성할 수 있다. 1부터 10까지 출력하는 예제를 통해 두 방법의 코드를 비교해보자.

<br>


#### 1. 스크립틀릿을 사용한 방법

<br>


```
<html>
<body>
    <%for (int a= 1; a<=10; a++>) { %>
        <%= a%><br/>
    <%}%>

    <%= request.getAttribute("person").getAddress().getCity() %>
</body>
</html>
```

위와같이 스크립틀릿과 HTML 태그들을함깨 사용하는 것을 지양해야 하는 이유는 가독성 문제 때문이다. 여러 개발자가 공동으로 작업을 진행할 때에 위와같이 작성하게 되면 해당 코드를 이해하기 위해서는 java를 알아야 하기 때문에 협업하기에 불친절하다고 할 수 있다.

<br>


#### 2. JSTL + EL을 사용한 방법

<br>


```
<% @taglib uri="http://java.sun.com/jstl/core" prefix="c" %>

<html>
<body>
    <c:forEach var="i" begin="1" end="10" step="1">
    <c:out value="${i}"/>
    <br/>
    </c:forEach>

    ${person.address.city}
 </body>
</html>
```

JSTL + EL로 사용하게 되면 java코드와 "<% %>" 기호 없이 오로지 태그로만 구성되어 있기 때문에 가독성이 좋아지고 협업하는 사람들이 이해하기 쉬워진다.

서버에서 넘어온 parameter를 꺼내는 방법에서도 스크립틀릿을 사용했을 때보다 보다 간결하게 하용할 수 있다.

<br>

예를 들어 java 코드 int num = 100; 을 jstl을 사용하여 표현하게 되면 <c:set var="num" value="100">이 된다.


# 참조
[유튜브] [https://usefultoknow.tistory.com/entry/JSTLJSP-Standard-Tag-Library%EC%9D%B4%EB%9E%80](https://usefultoknow.tistory.com/entry/JSTLJSP-Standard-Tag-Library%EC%9D%B4%EB%9E%80)

[블로그][https://daesuni.github.io/jstl/](https://daesuni.github.io/jstl/)
