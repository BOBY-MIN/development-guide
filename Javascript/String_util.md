
### UTF-8 변환하여 byte 길이 구하기
```
function getByteLengthUtf8(str) {

	if(!isNull(str)) {
		// str 전체 탐색되면 charCodeAt에서 실패나면 for문 종료
		for(b=i=0;c=str.charCodeAt(i++);b+=c>>11?3:c>>7?2:1);
		return b;
	} else {
		return 0;
	}
}
```

* 출처 : https://gist.github.com/mathiasbynens/1010324

***
