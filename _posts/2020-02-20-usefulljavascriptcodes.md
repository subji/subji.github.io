---
title: "자주 찾아보는? 사용되는 자바스크립트 코드 모음 (Useful Javascript code)"
date: 2020-02-20
layout: single
classes: wide
categories: posts
tags: ["javascript"]
---

## | 자주 찾아보는 자바스크립트 코드 모음이다.
---
### **Javascript 버전**

```javascript
/**
 * 숫자값이 정수인지 여부를 반환하는 함수
 * @param { Number } value : 숫자 값
 * @return { Boolean } : 정수(true), 정수 아닐 경우(false)
 */
function isInt(value) {
  let parse = parseFloat(value);
  return !isNaN(value) && (parse | 0) === parse;
}
/**
 * 정수부에 3자리 마다 ',' 를 체크해주는 함수
 * @param { Integer } value : 정수 또는 실수 값
 * @return { String } : ',' 가 적용된 실수 또는 정수 문자열 값
 */
function addComma(value) {
  if (!isInt(value)) {
    let integer = value | 0;
    let floater = (value - integer).toFixed(2).toString().substring(1);
    return addComma(integer) + floater;
  } else {
    return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
  }
}
/** 
 * 윤년 계산 메소드
 * @param { Integer } year : Date 객체의 년도
 */
Date.isLeafYear = function (year) {
  return (((year % 4 === 0) && (year % 100 !== 0)) || (year % 400 === 0));
};
/**
 * 윤년 계산 인터페이스
 */
Date.prototype.isLeafYear = function () {
  return Date.isLeafYear(this.getFullYear());
};
/**
 * 윤년을 포함한 해당 년도의 각 월수를 반환하는 메소드
 * @param { Integer } year : Date 객체의 년도
 * @param { Integer } month : Date 객체의 월 (0-11)
 */
Date.getDaysInMonth = function (year, month) {
  return [31, (Date.isLeafYear(year) ? 29 : 28), 31, 30, 31, 30, 31, 31, 30, 31, 30, 31][month];
};
/**
 * 윤년을 포함한 해당 년도의 각 월수를 반환하는 인터페이스
 * @param { Integer } year : Date 객체의 년도
 * @param { Integer } month : Date 객체의 월 (0-11)
 */
Date.prototype.getDaysInMonth = function (year, month) {
  return Date.getDaysInMonth(this.getFullYear(), this.getMonth());
};
/**
 * Date 객체에서 N 개월만큼을 이동하는 메소드
 * @param { Integer } n : 변경할 개월 수
 */
Date.prototype.changeMonth = function (n) {
  let day = this.getDate();
  this.setDate(1);
  this.setMonth(this.getMonth() + n);
  this.setDate(Math.min(day, this.getDaysInMonth()));
  return this;
};
Date.prototype.changeWeek = function (n) {
  let day = this.getDate();
  this.setDate(day + (7 * n));
  return this;
};
/**
 * YYYY(구분자) MM(구분자) DD 와 같은 형태로 날짜를 반환하는 메소드, 기본 구분자는 '-'
 * @param { String } delimiter : 구분자
 */
Date.prototype.formatDate = function (delimiter) {
  let d = (this.getDate() > 9 ? '' : '0') + this.getDate();
  let m = ((this.getMonth() + 1) > 9 ? '' : '0') + (this.getMonth() + 1);
  delimiter = delimiter || '';
  return this.getFullYear() + delimiter + m + delimiter + d;
};

/**
 * YYYY-MM-DD hh24:mi:ss 변환
 */
Date.prototype.formatDateYMDHMS = function () {
  let d = (this.getDate() > 9 ? '' : '0') + this.getDate();
  let m = ((this.getMonth() + 1) > 9 ? '' : '0') + (this.getMonth() + 1);
  let h = (this.getHours() > 9 ? '' : '0') + this.getHours();
  let mi = (this.getMinutes() > 9 ? '' : '0') + this.getMinutes();
  let s = (this.getSeconds() > 9 ? '' : '0') + this.getSeconds();
  return this.getFullYear() + "-" + m + "-" + d + " " + h + ":" + mi + ":" + s;
};


/**
 * 날짜 변경 함수
 * @param { Date } beforeDate : 기준 날짜
 * @param { int } addNumber : 더하거나 뺄 값
 * @param { String } type : 년(y) 월(m) 일(d) 
 */
function dateAddDel(beforeDate, addNumber, type) {
  var yy = parseInt(beforeDate.substr(0, 4), 10);
  var mm = parseInt(beforeDate.substr(5, 2), 10);
  var dd = parseInt(beforeDate.substr(8), 10);

  if (type == "d") { //일
    d = new Date(yy, mm - 1, dd + addNumber);
  } else if (type == "m") { //월
    d = new Date(yy, mm - 1, dd + (addNumber * 31));
  } else if (type == "y") { //년
    d = new Date(yy + addNumber, mm - 1, dd);
  }

  yy = d.getFullYear(); // 19를 2019로 변경
  mm = d.getMonth() + 1;
  mm = (mm < 10) ? '0' + mm : mm; //월 변경  +1 하는 이유는 자바스크립트에서 0이 1월이라 
  dd = d.getDate();
  dd = (dd < 10) ? '0' + dd : dd; //10일 이전이면 숫자 자릿수 맞추기 위함

  return yy + '-' + mm + '-' + dd;
}

/**
 * 해당 달의 첫째날로 변환
 */
Date.prototype.getFirstDay = function () {
  return new Date(this.getFullYear(), this.getMonth(), 1);
};
/**
 * 해당 달의 마지막날 반환
 */
Date.prototype.getLastDay = function () {
  return new Date(this.getFullYear(), this.getMonth() + 1, 0);
};
/**
 * 날짜의 주차를 반환하는 메소드
 * @param { String } dowOffset 
 */
Date.prototype.getWeek = function (dowOffset) {
  /*getWeek() was developed by Nick Baicoianu at MeanFreePath: http://www.meanfreepath.com */
  dowOffset = typeof (dowOffset) == 'int' ? dowOffset : 0; //default dowOffset to zero 
  let newYear = new Date(this.getFullYear(), 0, 1);
  let day = newYear.getDay() - dowOffset; //the day of week the year begins on day = (day >= 0 ? day : day + 7); 
  let dayNum = Math.floor((this.getTime() - newYear.getTime() - (this.getTimezoneOffset() - newYear.getTimezoneOffset()) * 60000) / 86400000) + 1;
  let weekNum; //if the year starts before the middle of a week 
  if (day < 4) {
    weekNum = Math.floor((dayNum + day - 1) / 7) + 1;
    if (weekNum > 52) {
      let nYear = new Date(this.getFullYear() + 1, 0, 1);
      let nDay = nYear.getDay() - dowOffset;
      nDay = nDay >= 0 ? nDay : nDay + 7; /*if the next year starts before the middle of the week, it is week #1 of that year*/
      weekNum = nDay < 4 ? 1 : 53;
    }
  } else {
    weekNum = Math.floor((dayNum + day - 1) / 7);
  }
  return weekNum;
};
/**
 * 날짜의 일단위 변경 함수
 */
Date.prototype.changeDate = function (num) {
  this.setDate(this.getDate() + num);
  return this;
};
/**
 * 해당 날짜가 속한 주의 일요일 ~ 토요일 날짜를 문자열로 반환하는 함수
 */
Date.prototype.getFirstAndLastOfWeek = function (precision) {
  let day = this.getDay();
  let sunday = 0 - day;
  let saturday = 6 - day;

  let first = new Date(this.getFullYear(), this.getMonth(), this.getDate() + sunday);
  let last = new Date(this.getFullYear(), this.getMonth(), this.getDate() + saturday);

  if (precision) {
    first = this > first ? this : first;
    last = new Date() < last ? new Date() : last;
  }

  return {
    first: first,
    last: last
  };
};
/**
 * YYYYMMDD 형태의 문자열 날짜를 Date 객체로 변환하는 함수
 * @param { String } dateStr 
 */
function dateParser(dateStr) {
  dateStr = dateStr.replace(/\W/g, '');
  let y = dateStr.substring(0, 4);
  let m = dateStr.substring(4, 6);
  let d = dateStr.substring(6, 8);
  return new Date(y, m - 1, d);
}
/**
 * 문자열의 가로, 세로 길이를 반환하는 함수
 * @param { Integer } fontSize
 */
String.prototype.getMeasureText = function (fontSize) {
  let $temp = document.getElementById('measureTextWidth');
  let type = typeof (fontSize);
  if (type === 'undefined' || type === 'boolean' || type === 'object' || type === 'function' || type === 'symbol') {
    throw Error('지원하지 않는 형식입니다.');
  }
  fontSize = '' + fontSize;
  $temp.innerText = this;
  $temp.style.fontSize = fontSize.indexOf('em') > -1 ? fontSize : (fontSize + 'em');
  
  return {
    width: ($temp.clientWidth + 1),
    height: ($temp.clientHeight + 1)
  };
}
/**
 * 공백 전체 제거
 */
String.prototype.trimAll = function () {
  return this.replace(/(\s+)/g, '');
};
/**
 * 종성 받침을 반환하는 함수 (으로 / 로 결정을 도와줌)
 * false 는 '로', true 는 '으로'
 * @param {*} word 
 */
function checkBatchimEnding(word) {
  const t = [
    '', 'ㄱ', 'ㄲ', 'ㄳ', 'ㄴ', 'ㄵ', 'ㄶ',
    'ㄷ', 'ㄹ', 'ㄺ', 'ㄻ', 'ㄼ', 'ㄽ', 'ㄾ',
    'ㄿ', 'ㅀ', 'ㅁ', 'ㅂ', 'ㅄ', 'ㅅ', 'ㅆ',
    'ㅇ', 'ㅈ', 'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ'
  ];

  if (typeof word !== 'string') {
    return null;
  }

  let lastLetter = word[word.length - 1];
  let unicode = lastLetter.charCodeAt(0);

  if (unicode < 44032 || unicode > 55203) {
    return null;
  }

  let endChar = t[(unicode - 44032) % 28];
  let addChar = (endChar !== '' && endChar !== 'ㄹ') ? '은' : '는';

  return addChar;
}
/**
 * 기간 별 날짜 문자열을 반환하는 함수
 * @param {*} period 
 */
function getDateStringByPeriod(str, period, options) {
  if (period === 0) {
    return dateParser(str).formatDate('-');
  } else if (period === 1) {
    let date = dateParser(str);
    let weeks = date.getFirstAndLastOfWeek(options.precision);
    return weeks.first.formatDate('-') + ' ~ ' + weeks.last.formatDate('-');
  } else if (period === 2) {
    let result = null;
    if (str.length < 7) {
      result = str.substring(0, 4) + '-' + str.substring(4, 6);
    } else {
      let date = dateParser(str).formatDate('');
      result = date.substring(0, 4) + '-' + date.substring(4, 6);
    }

    return result;
  }
}
/**
 * 우클릭 & 복사 방지 함수
 */
function preventPointerEvents() {
  $('.prevent-pointer-events')
    .mousedown(function (e) {
      if (e.button == 2) {
        alert('내용을 복사 할 수 없습니다.');
        return false;
      } else {
        return true;
      }
    })
    .on('contextmenu', function () {
      return false;
    })
    .on('dragstart', function () {
      return false;
    })
    .on('selectstart', function () {
      return false;
    });
}
/**
 * Canvas DataURL 을 Blob 으로 변경하는 함수
 * @param {*} dataUrl 
 */
function dataURLtoBlob(dataUrl) {
  let arr = dataUrl.split(',');
  let mime = arr[0].match(/:(.*?);/)[1];
  let bstr = atob(arr[1]);
  let n = bstr.length;
  let u8arr = new Uint8Array(n);

  while (n--) {
    u8arr[n] = bstr.charCodeAt(n);
  }

  return new Blob([u8arr], {
    type: mime
  });
}
/**
 * target (input or textarea, ...) 의 값을 클립보드에 복사하는 함수
 * @param {*} target 
 * @param {*} callback 
 */
function setClipBoard(value, callback) {
  try {
    let text = document.createElement('textarea');
    document.body.appendChild(text);
    text.value = value;
    text.select();
    document.execCommand('copy');
    document.body.removeChild(text);
    return callback(true);
  } catch (e) {
    return callback(null);
  }
}
/**
 * 쿠키 설정 함수
 * @param {*} key 
 * @param {*} value 
 * @param {*} expireDays 
 */
function setCookie (name, value, expireDays) {
  let expire = new Date();
  expire.setDate(expire.getDate() + expireDays);
  let cookieString = escape(value);
  cookieString += '; path=/';
  cookieString += (expireDays === null ? '' : '; expires=' + expire.toUTCString());
  document.cookie = name + '=' + cookieString;
}
/**
 * 쿠키 값을 가져오는 함수
 * @param {*} name 
 */
function getCookie (name) {
  let result = null;

  document.cookie.split(';').forEach(function (cookie)  {
    let split = cookie.split('=');
    let n = split[0].replace(/ /g, '');
    let v = unescape(split[1]);

    if (n === name) {
      result = v;
    }
  });

  return result;
}
/**
 * 을/를 포맷 함수
 * @param {*} text 
 */
function formatJosa (text)  {
  let lastChar = text.charAt(text.length - 1);

  let lmn = ['l', 'm', 'n'];
  let afhiorsuvwxyz = ['a', 'f', 'h', 'i', 'o', 'r', 's', 'u', 'v', 'w', 'x', 'y', 'z'];
  let bcdegkpt = ['b', 'c', 'd', 'e', 'g', 'k', 'p', 't'];
  let bckpt = ['b', 'c', 'k', 'p', 't'];
  let deg = ['d', 'e', 'g'];
  let aeiou = ['a', 'e', 'i', 'o', 'u'];

  let lastIndex = '';

   if (lastChar < 0xAC00 || lastChar > 0xD7A3) {
     if ((lastChar > 0x0041 && lastChar < 0x005A) || (lastChar > 0x0061 && lastChar < 0x007A)) {
       if (lmn.indexOf(lastChar) > -1) {
         lastIndex = "을";
       } else if (afhiorsuvwxyz.indexOf(lastChar) > -1) {
         lastIndex = "를";
       } else if (bcdegkpt.indexOf(lastChar) > -1) {
         if (text.length() < 2) {
           lastIndex = "를";
         } else {
           let beforeLastChar = text.charAt(text.length - 2);

           if (bckpt.indexOf(lastChar) > -1) {
             if (lastChar == 'k' && beforeLastChar == 'c') {
               lastIndex = "을";
             } else if (lastChar == 'b' && beforeLastChar == 'm') {
               lastIndex = "을";
             } else if (aeiou.indexOf(beforeLastChar) > -1) {
               lastIndex = "을";
             } else {
               lastIndex = "를";
             }
           } else if (deg.indexOf(lastChar) > -1) {
             if (lastChar == 'e' && beforeLastChar == 'l') {
               lastIndex = "을";
             } else if (lastChar == 'e' && beforeLastChar == 'n') {
               lastIndex = "을";
             } else if (lastChar == 'e' && beforeLastChar == 'm') {
               lastIndex = "을";
             } else if (lastChar == 'g' && beforeLastChar == 'n') {
               lastIndex = "을";
             } else if (lastChar == 'd' && aeiou.indexOf(beforeLastChar) > -1) {
               lastIndex = "을";
             } else if (lastChar == 'g' && aeiou.indexOf(beforeLastChar) > -1) {
               lastIndex = "을";
             } else {
               lastIndex = "를";
             }
           }
         }
       }
     } else {
       lastIndex = "을/를";
     }
   } else {
     // 0 보다 크면 받침이 있으므로 "을"
     // 0 보다 작거나 같으면 "를"
     lastIndex = (lastChar - 0xAC00) % 28 > 0 ? "을" : "를";
   }

   return lastIndex;
}
/**
 * 쿠키를 삭제하는 함수
 * @param {*} name 
 */
function deleteCookie (name)  {
  document.cookie = name + '= ; expires=' + new Date().toUTCString() + '; path=/';
}

function checkIe () {
  let agent = navigator.userAgent.toLowerCase();

  if ((navigator.appName == 'Netscape' && agent.indexOf('trident') != -1) || (agent.indexOf("msie") != -1)) {
    return true;
  } else {
    return false;
  }
}

```

### **Typescript 버전**

```typescript
/**
 * 숫자값이 정수인지 여부를 반환하는 함수
 * @param { Number } value : 숫자 값
 * @return { Boolean } : 정수(true), 정수 아닐 경우(false)
 */
function isInt (value:any): boolean {
    const parse: number = parseFloat(value);
    return !isNaN(value) && (parse | 0) === parse;
}
/**
 * 정수부에 3자리 마다 ',' 를 체크해주는 함수
 * @param { Integer } value : 정수 또는 실수 값
 * @return { String } : ',' 가 적용된 실수 또는 정수 문자열 값
 */
function addComma (value:any): string {
  if (!isInt(value)) {
    let integer:number = value | 0;
    let floater:string = (value - integer).toFixed(2).toString().substring(1);
    return addComma(integer) + floater;
  } else {
    return value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
  }
}
/** 
 * 윤년 계산 메소드
 * @param { Integer } year : Date 객체의 년도
 */
Date.isLeafYear = function (year) {
  return (((year % 4 === 0) && (year % 100 !== 0)) || (year % 400 === 0));
};
/**
 * 윤년 계산 인터페이스
 */
Date.prototype.isLeafYear = function () {
  return Date.isLeafYear(this.getFullYear());
};
/**
 * 윤년을 포함한 해당 년도의 각 월수를 반환하는 메소드
 * @param { Integer } year : Date 객체의 년도
 * @param { Integer } month : Date 객체의 월 (0-11)
 */
Date.getDaysInMonth = function (year, month) {
  return [31, (Date.isLeafYear(year) ? 29 : 28), 31, 30, 31, 30, 31, 31, 30, 31, 30, 31][month];
};
/**
 * 윤년을 포함한 해당 년도의 각 월수를 반환하는 인터페이스
 * @param { Integer } year : Date 객체의 년도
 * @param { Integer } month : Date 객체의 월 (0-11)
 */
Date.prototype.getDaysInMonth = function (year, month) {
  return Date.getDaysInMonth(this.getFullYear(), this.getMonth());
};
/**
 * Date 객체에서 N 개월만큼을 이동하는 메소드
 * @param { Integer } n : 변경할 개월 수
 */
Date.prototype.changeMonth = function (n) {
  let day = this.getDate();
  this.setDate(1);
  this.setMonth(this.getMonth() + n);
  this.setDate(Math.min(day, this.getDaysInMonth()));
  return this;
};
/**
 * Date 객체에서 N 주 만큼을 이동하는 메소드
 * @param { Integer } n : 변경할 개월 수
 */
Date.prototype.changeWeek = function (n) {
  let day = this.getDate();
  this.setDate(day + (7 * n));
  return this;
};
/**
 * YYYY(구분자) MM(구분자) DD 와 같은 형태로 날짜를 반환하는 메소드, 기본 구분자는 '-'
 * @param { String } delimiter : 구분자
 */
Date.prototype.formatDate = function (delimiter) {
  let d = (this.getDate() > 9 ? '' : '0') + this.getDate();
  let m = ((this.getMonth() + 1) > 9 ? '' : '0') + (this.getMonth() + 1);
  delimiter = delimiter || '';
  return this.getFullYear() + delimiter + m + delimiter + d;
};

/**
 * YYYY-MM-DD hh24:mi:ss 변환
 */
Date.prototype.formatDateYMDHMS = function () {
  let d = (this.getDate() > 9 ? '' : '0') + this.getDate();
  let m = ((this.getMonth() + 1) > 9 ? '' : '0') + (this.getMonth() + 1);
  let h = (this.getHours() > 9 ? '' : '0') + this.getHours();
  let mi = (this.getMinutes() > 9 ? '' : '0') + this.getMinutes();
  let s = (this.getSeconds() > 9 ? '' : '0') + this.getSeconds();
  return this.getFullYear() + "-" + m + "-" + d + " " + h + ":" + mi + ":" + s;
};

/**
 * 날짜 변경 함수
 * @param { Date } beforeDate : 기준 날짜
 * @param { int } addNumber : 더하거나 뺄 값
 * @param { String } type : 년(y) 월(m) 일(d) 
 */
function dateAddDel(beforeDate, addNumber, type) {
  var yy = parseInt(beforeDate.substr(0, 4), 10);
  var mm = parseInt(beforeDate.substr(5, 2), 10);
  var dd = parseInt(beforeDate.substr(8), 10);

  if (type == "d") { //일
    d = new Date(yy, mm - 1, dd + addNumber);
  } else if (type == "m") { //월
    d = new Date(yy, mm - 1, dd + (addNumber * 31));
  } else if (type == "y") { //년
    d = new Date(yy + addNumber, mm - 1, dd);
  }

  yy = d.getFullYear(); // 19를 2019로 변경
  mm = d.getMonth() + 1;
  mm = (mm < 10) ? '0' + mm : mm; //월 변경  +1 하는 이유는 자바스크립트에서 0이 1월이라 
  dd = d.getDate();
  dd = (dd < 10) ? '0' + dd : dd; //10일 이전이면 숫자 자릿수 맞추기 위함

  return yy + '-' + mm + '-' + dd;
}

/**
 * 해당 달의 첫째날로 변환
 */
Date.prototype.getFirstDay = function () {
  return new Date(this.getFullYear(), this.getMonth(), 1);
};
/**
 * 해당 달의 마지막날 반환
 */
Date.prototype.getLastDay = function () {
  return new Date(this.getFullYear(), this.getMonth() + 1, 0);
};
/**
 * 날짜의 주차를 반환하는 메소드
 * @param { String } dowOffset 
 */
Date.prototype.getWeek = function (dowOffset) {
  /*getWeek() was developed by Nick Baicoianu at MeanFreePath: http://www.meanfreepath.com */
  dowOffset = typeof (dowOffset) == 'int' ? dowOffset : 0; //default dowOffset to zero 
  let newYear = new Date(this.getFullYear(), 0, 1);
  let day = newYear.getDay() - dowOffset; //the day of week the year begins on day = (day >= 0 ? day : day + 7); 
  let dayNum = Math.floor((this.getTime() - newYear.getTime() - (this.getTimezoneOffset() - newYear.getTimezoneOffset()) * 60000) / 86400000) + 1;
  let weekNum; //if the year starts before the middle of a week 
  if (day < 4) {
    weekNum = Math.floor((dayNum + day - 1) / 7) + 1;
    if (weekNum > 52) {
      let nYear = new Date(this.getFullYear() + 1, 0, 1);
      let nDay = nYear.getDay() - dowOffset;
      nDay = nDay >= 0 ? nDay : nDay + 7; /*if the next year starts before the middle of the week, it is week #1 of that year*/
      weekNum = nDay < 4 ? 1 : 53;
    }
  } else {
    weekNum = Math.floor((dayNum + day - 1) / 7);
  }
  return weekNum;
};
/**
 * 날짜의 일단위 변경 함수
 */
Date.prototype.changeDate = function (num) {
  this.setDate(this.getDate() + num);
  return this;
};
/**
 * 해당 날짜가 속한 주의 일요일 ~ 토요일 날짜를 문자열로 반환하는 함수
 */
Date.prototype.getFirstAndLastOfWeek = function (precision) {
  let day = this.getDay();
  let sunday = 0 - day;
  let saturday = 6 - day;

  let first = new Date(this.getFullYear(), this.getMonth(), this.getDate() + sunday);
  let last = new Date(this.getFullYear(), this.getMonth(), this.getDate() + saturday);

  if (precision) {
    first = this > first ? this : first;
    last = new Date() < last ? new Date() : last;
  }

  return {
    first: first,
    last: last
  };
};
/**
 * YYYYMMDD 형태의 문자열 날짜를 Date 객체로 변환하는 함수
 * @param { String } dateStr 
 */
function dateParser(dateStr) {
  dateStr = dateStr.replace(/\W/g, '');
  let y = dateStr.substring(0, 4);
  let m = dateStr.substring(4, 6);
  let d = dateStr.substring(6, 8);
  return new Date(y, m - 1, d);
}
/**
 * 문자열의 가로, 세로 길이를 반환하는 함수
 * @param { Integer } fontSize
 */
String.prototype.getMeasureText = function (fontSize) {
  let $temp = document.getElementById('measureTextWidth');
  let type = typeof (fontSize);
  if (type === 'undefined' || type === 'boolean' || type === 'object' || type === 'function' || type === 'symbol') {
    throw Error('지원하지 않는 형식입니다.');
  }
  fontSize = '' + fontSize;
  $temp.innerText = this;
  $temp.style.fontSize = fontSize.indexOf('em') > -1 ? fontSize : (fontSize + 'em');
  
  return {
    width: ($temp.clientWidth + 1),
    height: ($temp.clientHeight + 1)
  };
}
/**
 * 공백 전체 제거
 */
String.prototype.trimAll = function () {
  return this.replace(/(\s+)/g, '');
};
/**
 * 종성 받침을 반환하는 함수 (으로 / 로 결정을 도와줌)
 * false 는 '로', true 는 '으로'
 * @param {*} word 
 */
function checkBatchimEnding(word) {
  const t = [
    '', 'ㄱ', 'ㄲ', 'ㄳ', 'ㄴ', 'ㄵ', 'ㄶ',
    'ㄷ', 'ㄹ', 'ㄺ', 'ㄻ', 'ㄼ', 'ㄽ', 'ㄾ',
    'ㄿ', 'ㅀ', 'ㅁ', 'ㅂ', 'ㅄ', 'ㅅ', 'ㅆ',
    'ㅇ', 'ㅈ', 'ㅊ', 'ㅋ', 'ㅌ', 'ㅍ', 'ㅎ'
  ];

  if (typeof word !== 'string') {
    return null;
  }

  let lastLetter = word[word.length - 1];
  let unicode = lastLetter.charCodeAt(0);

  if (unicode < 44032 || unicode > 55203) {
    return null;
  }

  let endChar = t[(unicode - 44032) % 28];
  let addChar = (endChar !== '' && endChar !== 'ㄹ') ? '은' : '는';

  return addChar;
}
/**
 * 기간 별 날짜 문자열을 반환하는 함수
 * @param {*} period 
 */
function getDateStringByPeriod(str, period, options) {
  if (period === 0) {
    return dateParser(str).formatDate('-');
  } else if (period === 1) {
    let date = dateParser(str);
    let weeks = date.getFirstAndLastOfWeek(options.precision);
    return weeks.first.formatDate('-') + ' ~ ' + weeks.last.formatDate('-');
  } else if (period === 2) {
    let result = null;
    if (str.length < 7) {
      result = str.substring(0, 4) + '-' + str.substring(4, 6);
    } else {
      let date = dateParser(str).formatDate('');
      result = date.substring(0, 4) + '-' + date.substring(4, 6);
    }

    return result;
  }
}
/**
 * 우클릭 & 복사 방지 함수
 */
function preventPointerEvents() {
  $('.prevent-pointer-events')
    .mousedown(function (e) {
      if (e.button == 2) {
        alert('내용을 복사 할 수 없습니다.');
        return false;
      } else {
        return true;
      }
    })
    .on('contextmenu', function () {
      return false;
    })
    .on('dragstart', function () {
      return false;
    })
    .on('selectstart', function () {
      return false;
    });
}
/**
 * Canvas DataURL 을 Blob 으로 변경하는 함수
 * @param {*} dataUrl 
 */
function dataURLtoBlob(dataUrl) {
  let arr = dataUrl.split(',');
  let mime = arr[0].match(/:(.*?);/)[1];
  let bstr = atob(arr[1]);
  let n = bstr.length;
  let u8arr = new Uint8Array(n);

  while (n--) {
    u8arr[n] = bstr.charCodeAt(n);
  }

  return new Blob([u8arr], {
    type: mime
  });
}
/**
 * target (input or textarea, ...) 의 값을 클립보드에 복사하는 함수
 * @param {*} target 
 * @param {*} callback 
 */
function setClipBoard(value, callback) {
  try {
    let text = document.createElement('textarea');
    document.body.appendChild(text);
    text.value = value;
    text.select();
    document.execCommand('copy');
    document.body.removeChild(text);
    return callback(true);
  } catch (e) {
    return callback(null);
  }
}
/**
 * 쿠키 설정 함수
 * @param {*} key 
 * @param {*} value 
 * @param {*} expireDays 
 */
function setCookie (name, value, expireDays) {
  let expire = new Date();
  expire.setDate(expire.getDate() + expireDays);
  let cookieString = escape(value);
  cookieString += '; path=/';
  cookieString += (expireDays === null ? '' : '; expires=' + expire.toUTCString());
  document.cookie = name + '=' + cookieString;
}
/**
 * 쿠키 값을 가져오는 함수
 * @param {*} name 
 */
function getCookie (name) {
  let result = null;

  document.cookie.split(';').forEach(function (cookie)  {
    let split = cookie.split('=');
    let n = split[0].replace(/ /g, '');
    let v = unescape(split[1]);

    if (n === name) {
      result = v;
    }
  });

  return result;
}
/**
 * 을/를 포맷 함수
 * @param {*} text 
 */
function formatJosa (text)  {
  let lastChar = text.charAt(text.length - 1);

  let lmn = ['l', 'm', 'n'];
  let afhiorsuvwxyz = ['a', 'f', 'h', 'i', 'o', 'r', 's', 'u', 'v', 'w', 'x', 'y', 'z'];
  let bcdegkpt = ['b', 'c', 'd', 'e', 'g', 'k', 'p', 't'];
  let bckpt = ['b', 'c', 'k', 'p', 't'];
  let deg = ['d', 'e', 'g'];
  let aeiou = ['a', 'e', 'i', 'o', 'u'];

  let lastIndex = '';

   if (lastChar < 0xAC00 || lastChar > 0xD7A3) {
     if ((lastChar > 0x0041 && lastChar < 0x005A) || (lastChar > 0x0061 && lastChar < 0x007A)) {
       if (lmn.indexOf(lastChar) > -1) {
         lastIndex = "을";
       } else if (afhiorsuvwxyz.indexOf(lastChar) > -1) {
         lastIndex = "를";
       } else if (bcdegkpt.indexOf(lastChar) > -1) {
         if (text.length() < 2) {
           lastIndex = "를";
         } else {
           let beforeLastChar = text.charAt(text.length - 2);

           if (bckpt.indexOf(lastChar) > -1) {
             if (lastChar == 'k' && beforeLastChar == 'c') {
               lastIndex = "을";
             } else if (lastChar == 'b' && beforeLastChar == 'm') {
               lastIndex = "을";
             } else if (aeiou.indexOf(beforeLastChar) > -1) {
               lastIndex = "을";
             } else {
               lastIndex = "를";
             }
           } else if (deg.indexOf(lastChar) > -1) {
             if (lastChar == 'e' && beforeLastChar == 'l') {
               lastIndex = "을";
             } else if (lastChar == 'e' && beforeLastChar == 'n') {
               lastIndex = "을";
             } else if (lastChar == 'e' && beforeLastChar == 'm') {
               lastIndex = "을";
             } else if (lastChar == 'g' && beforeLastChar == 'n') {
               lastIndex = "을";
             } else if (lastChar == 'd' && aeiou.indexOf(beforeLastChar) > -1) {
               lastIndex = "을";
             } else if (lastChar == 'g' && aeiou.indexOf(beforeLastChar) > -1) {
               lastIndex = "을";
             } else {
               lastIndex = "를";
             }
           }
         }
       }
     } else {
       lastIndex = "을/를";
     }
   } else {
     // 0 보다 크면 받침이 있으므로 "을"
     // 0 보다 작거나 같으면 "를"
     lastIndex = (lastChar - 0xAC00) % 28 > 0 ? "을" : "를";
   }

   return lastIndex;
}
/**
 * 쿠키를 삭제하는 함수
 * @param {*} name 
 */
function deleteCookie (name)  {
  document.cookie = name + '= ; expires=' + new Date().toUTCString() + '; path=/';
}

function checkIe () {
  let agent = navigator.userAgent.toLowerCase();

  if ((navigator.appName == 'Netscape' && agent.indexOf('trident') != -1) || (agent.indexOf("msie") != -1)) {
    return true;
  } else {
    return false;
  }
}
```