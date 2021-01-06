---
title: "Java 월 주차 구하기"
date: 2021-01-06
categories: posts
tags: ["java"]
---

# 월 주차 계산 방법
---
우리나라 기준으로 월 주차는 한 주의 시작을 월요일로 한다. 매달 첫째주는 이전달의 마지막주이냐 이번달의 첫째주이냐가 특히 고민이다. 간단하게 확인하는 방법이 있다. 해당 주에 목요일로 판단할 수 있다. 

예를들어 다음 그림에 있는 첫째주는 9월의 마지막 주일까 10월의 첫째주 일까?

![사진1](https://subji.github.io/assets/images/2021-01-06-javagetweeknumberinkr_1.png)

정답은 10월의 첫째주이다. 앞서 말한대로 10월 1일이 목요일부터 시작함으로써 10월에 첫째주가 되는 것이다. 반대로 9월 30일이 목요일이면 9월의 마지막주가 되는것이다. 
사실 목요일로 구분하는건 굉장히 간단한 방법이다. 정확한 방법은 첫주에서 4일이상을 차지하는 달의 주로 결정되는 것이다. 즉 월요일 부터 한주가 시작하는 우리나라의 경우 목요일부터 일요일까지 4일이 되므로 간단하게 목요일로 결정 할 수 있는 것이다.

그럼 내 나름대로 이리저리 월의 주차를 계산하는 코드를 작성해보았다.

```java
public class CalendarUtil {

  /**
   * 요청 날짜의 달의 1일의 주를 찾아서 1일이 가리키는 요일을 확인해 주차 차감 값 계산
   * 1일이 목요일(5) 보다 클 경우 이전 달의 마지막 주 이므로 이번 달은 2주차가 1주가 된다. 그래서 1을 빼줘야 한다. 리턴 = 1
   * 1일이 목요일(5) 보다 작으면 즉 월~목 일 경우 이번 달의 첫째주 이므로 값을 빼주지 않아도 된다. 리턴 = 0
   * @param year
   * @param month
   * @param day
   * @return
   */
  public static int subWeekNumberIsFirstDayAfterThursday(int year, int month, int day)  {
    Calendar calendar = Calendar.getInstance(Locale.KOREA);
    calendar.setFirstDayOfWeek(Calendar.MONDAY);
    calendar.set(year, month - 1, day);
    calendar.set(Calendar.DAY_OF_MONTH, 1);

    int weekOfDay = calendar.get(Calendar.DAY_OF_WEEK);

    if ((weekOfDay >= Calendar.MONDAY) && (weekOfDay <= Calendar.THURSDAY)) {
      return 0;
    } else {
      return 1;
    }
  }

  /**
   * 요청 날짜의 마지막 주에서 요청 날짜의 마지막 날짜가 월~수 사이에 위치하는지 확인
   * 마지막 날짜가 월~수 일 경우 다음 달의 첫째주가 된다. 그러므로 월을 + 1 해줘야 한다. 리턴 = 1
   * 마지막 날짜가 목요일 이상일 경우 이번달의 마지막주가 된다. 월을 + 1 해주지 않아도 된다. 리턴 = 0
   * @param year
   * @param month
   * @param day
   * @return
   */
  public static int addMonthIsLastDayBeforeThursday(int year, int month, int day) {
    Calendar calendar = Calendar.getInstance(Locale.KOREA);
    calendar.setFirstDayOfWeek(Calendar.MONDAY);
    calendar.set(year, month - 1, day);

    int currentWeekNumber = calendar.get(Calendar.WEEK_OF_MONTH);
    int maximumWeekNumber = calendar.getActualMaximum(Calendar.WEEK_OF_MONTH);

    if (currentWeekNumber == maximumWeekNumber) {
      calendar.set(year, month - 1, calendar.getActualMaximum(Calendar.DAY_OF_MONTH));
      int maximumDayOfWeek = calendar.get(Calendar.DAY_OF_WEEK);

      if (maximumDayOfWeek < Calendar.THURSDAY && maximumDayOfWeek > Calendar.SUNDAY) {
        return 1;
      } else {
        return 0;
      }
    } else {
      return 0;
    }
  }

  /**
   * 해당 날짜의 주차를 반환
   * @param year
   * @param month
   * @param day
   * @return
   */
  public static String getCurrentWeekOfMonth(int year, int month, int day)  {
    int subtractFirstWeekNumber = subWeekNumberIsFirstDayAfterThursday(year, month, day);
    int subtractLastWeekNumber = addMonthIsLastDayBeforeThursday(year, month, day);

	// 요청 날짜의 마지막 주가 다음달의 첫째주로 결정되었을 경우(subtractLastWeekNumber = 1) 
	// 날짜를 다음달의 1일로 설정
	// subtractFirstWeekNumber 값이 이전 요청 날짜에 대한 값이 설정되어있다. 아래 조건문 안으로 진입한 경우
	// 마지막 주가 다음달의 첫째주가 되었으므로 주차에서 차감되는 값이 없어야 한다. 즉 subtractFirstWeekNumber = 0 이 되어야 한다.
    if (subtractLastWeekNumber > 0) {
      day = 1;
      subtractFirstWeekNumber = 0;
    }

    Calendar calendar = Calendar.getInstance(Locale.KOREA);
    calendar.setFirstDayOfWeek(Calendar.MONDAY);
    calendar.setMinimalDaysInFirstWeek(1);
    calendar.set(year, month - (1 - subtractLastWeekNumber), day);

    int dayOfWeekForFirstDayOfMonth = calendar.get(Calendar.WEEK_OF_MONTH) - subtractFirstWeekNumber;

    return (calendar.get(Calendar.MONTH) + 1) + "," + dayOfWeekForFirstDayOfMonth;
  }

}
```
