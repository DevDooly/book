# Vim

## Settings
```
:set ai			# autoindent, 윗라인과 같이 들여쓰기

:set si			# 코딩시 문법(if, for...) 등 다음라인에 들여쓰기

:set paste	# 원본 그대로 붙여넣기 하고싶을대

:set ts=4		# tabstop, tab 키 space 간격

:set sw=4		# shiftwidth, :set si 의 들여쓰기 깊이

:set et			# expandtab, tab to space

:set encoding=cp949|utf8 # 인코딩 타입 설정

:set t_ti=t_te=	# vi 종료시에도 편집 유지

:set ruler	# 우측하단에 컬럼위치 표시, 없앨때는 noruler
```

기본 설정으로 사용시, .vimrc 작성

```
set ts=4
set si
set ai
set sw=4
set encoding=cp494
```

## sed 

특정 문자열 포함 라인 제거

```
:g/ STRING /d
```

특정 문자열 없는 라인 제거

```
:v/ STRING /d
```

특정 문자열 치환

```
:%s/ STRING / STRING /g
```

공백 라인 제거
```
:g/^$/d
```

## sort

정렬
```
:sort			# 기본 정렬

:sort!		# 역순

:sort n		# 숫자 기준 정렬

:sort u 	# 정렬, 중복제거

:sort nu 	# n, u옵션 둘 다 사용

:sort i		# 대소문자 구분x
```

optional
```
:%!sort -k2		# 두번째 필드 기준으로 정렬

:%!sort -k2n	# n 옵션 추가 (숫자 정렬)
```
컬럼 기준은 공백(space)


