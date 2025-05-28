---
title: CSS 초기 세팅
date: 2025-05-19
time: 15:37
tags: []
---

# CSS 초기 세팅

```css
*,
*::before,
*::after {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

body {
	font-family: 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif;
}

button {
	background: none;
	color: inherit;
	border: none;
	cursor: pointer;
	outline: inherit;
}

a {
	color: inherit;
	text-decoration: none;
}

li {
	list-style: none;
}

input:focus {
	outline: none;
}
```