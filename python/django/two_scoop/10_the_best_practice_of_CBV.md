# 클래스 기반 뷰의 모범적인 이용
장고는 클래스 기반 뷰를 작성하는 표준화된 방법을 제공함. 장고의 뷰는 요청 객체를 받고 응답 객체를 반환하는 내장 함수다. 함수 기반 뷰에서는 뷰 함수 자체가 내장 함수이고, 클래스 기반 뷰에서는 뷰 클래스가 내장 함수를 반환하는 as_view() 클래스 메서드를 제공한다. django.views.generic.View에서 해당 메커니즘이 구현되며, 모든 클래서 기반 뷰는 이 클래스를 직간접적으로 상속 받아 이용한다.
또한 장고는 요즘 대부분의 엡 프로젝트에서 이용되는 제네릭 클래스 기반 뷰를 제공하며 그 장점을 최대한 살리고 있다.
모든 뷰에 대한 체계가 잡히기 전에는 큰 혼란이 있었지만 현재는 여러 패키지 덕분에 많이 해결된 상태다. 