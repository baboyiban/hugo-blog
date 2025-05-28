---
title: AI가 어려워하는 문제 1
date: 2025-05-15
time: 11:10
tags: []
---

# AI가 어려워하는 문제 1

## 문제 1

```js
'"Book/2024/9/\355\205\214\354\212\244\355\212\270 2.md" Book/Excalidraw/Drawing 2024-09-24 23.22.27.excalidraw.md Book/.obsidian/workspace.json decode-string.js sync-script.js'
```

이런 문자열이 있을 때 자바스크립트로 밑의 배열 처럼 만드는 방법을 알려줘

```js
[
  'Book/2024/9/테스트 2.md',
  'Book/Excalidraw/Drawing 2024-09-24 23.22.27.excalidraw.md',
  'Book/.obsidian/workspace.json',
  'decode-string.js',
  'sync-script.js'
]
```

---

실패
Claude Sonnet 3.5 0621
Claude Sonnet 3.5 1022
Claude Opus 3
Mistral Large 2
Gemini 1.5 Pro 002
Chat GPT 4
Chat GPT 4-mini
Chat GPT 4o
Gemini 1.5 Flash 002

성공
DeepSeek R1 - 2025.1.21
Chat GPT 4-turbo
Chat GPT o1-mini
Chat GPT o1-preview
Llama 3.1 405B
Llama 3.1 Nemotron 70B
Qwen 2.5 Coder 32B

기분 탓인지는 모르겠지만 o1-preview 보다 o1-mini 가 코딩 성능이 더 우세한 듯 하다.

## Models

### Chat GPT o1-preview

```js
/**
 * 옥탈 이스케이프 시퀀스를 포함한 문자열을 디코딩하여 UTF-8 문자열로 변환합니다.
 * @param {string} str - 옥탈 이스케이프 시퀀스를 포함한 입력 문자열
 * @returns {string} - 디코딩된 UTF-8 문자열
 */
function decodeOctalString(str) {
	// 바이트 배열을 저장할 배열 초기화
	let byteArray = [];

	// \xxx 패턴을 찾기 위한 정규 표현식 (000 ~ 377)
	const regex = /\\([0-3][0-7]{2})/g;
	let lastIndex = 0;
	let match;

	// 정규 표현식을 사용하여 \xxx 패턴을 순회
	while ((match = regex.exec(str)) !== null) {
		// 매치 이전의 문자열을 바이트로 변환하여 배열에 추가
		let before = str.slice(lastIndex, match.index);
		for (let char of before) {
			byteArray.push(char.charCodeAt(0));
		}

		// 매치된 \xxx를 옥탈수에서 디코딩하여 바이트 배열에 추가
		byteArray.push(parseInt(match[1], 8));

		// 다음 검색을 위한 인덱스 업데이트
		lastIndex = regex.lastIndex;
	}

	// 마지막 매치 이후의 문자열을 바이트로 변환하여 배열에 추가
	let remaining = str.slice(lastIndex);
	for (let char of remaining) {
		byteArray.push(char.charCodeAt(0));
	}

	// Uint8Array로 변환 후 UTF-8 디코딩
	let decoder = new TextDecoder("utf-8");
	let bytes = new Uint8Array(byteArray);
	return decoder.decode(bytes);
}

/**
 * 문자열에서 큰 따옴표를 제거합니다.
 * @param {string} input - 처리할 문자열
 * @returns {string} 따옴표가 제거된 문자열
 */
function removeQuotes(input) {
	return input.replace(/"([^"]*)"/g, "$1");
}

/**
 * 문자열을 파일 확장자를 기준으로 분리합니다.
 * @param {string} input - 처리할 문자열
 * @param {string[]} extensions - 파일 확장자 배열
 * @returns {string[]} 분리된 파일 경로 배열
 */
function splitByFileExtension(input, extensions) {
	let words = input.split(" ");
	let paths = [];
	let currentPath = "";

	words.forEach((word) => {
		if (currentPath) {
			currentPath += " " + word;
		} else {
			currentPath = word;
		}

		// 지정한 확장자로 끝나는지 확인
		if (extensions.some((ext) => currentPath.endsWith(ext))) {
			paths.push(currentPath);
			currentPath = "";
		}
	});

	return paths;
}

/**
 * 인코딩된 문자열을 디코딩하고 파일 확장자를 기준으로 문자열을 분리합니다.
 * @param {string} encodedString - 디코딩할 인코딩된 문자열
 * @returns {string[]} 분리된 파일 경로 배열
 */
function decodeString(encodedString) {
	// 입력된 문자열을 디코딩하고 큰 따옴표를 제거합니다.
	let decodedStr = removeQuotes(decodeOctalString(encodedString));

	// 파일 확장자를 기준으로 문자열을 분리하기 위해 사용할 확장자 목록입니다.
	const extensions = [".md", ".excalidraw.md", ".json", ".js"];

	// 디코딩된 문자열을 파일 확장자를 기준으로 분리합니다.
	const paths = splitByFileExtension(decodedStr, extensions);

	// 분리된 파일 경로 배열을 반환합니다.
	return paths;
}

const inputString =
	'"Book/2024/9/\355\205\214\354\212\244\355\212\270 2.md" Book/Excalidraw/Drawing 2024-09-24 23.22.27.excalidraw.md Book/.obsidian/workspace.json decode-string.js sync-script.js';

console.log(decodeString(inputString));
```

### Chat GPT o1-mini

```js
// 주어진 문자열
const input =
	'"Book/2024/9/\355\205\214\354\212\244\355\212\270 2.md" Book/Excalidraw/Drawing 2024-09-24 23.22.27.excalidraw.md Book/.obsidian/workspace.json decode-string.js sync-script.js';

/**
 * 이스케이프된 옥탈 시퀀스를 디코딩하는 함수
 * @param {string} str - 이스케이프된 문자열
 * @returns {string} - 디코딩된 문자열
 */
function decodeEscapedString(str) {
	const bytes = [];

	for (let i = 0; i < str.length; ) {
		if (str[i] === "\\") {
			// 다음 세 문자가 옥탈 숫자인지 확인
			const octal = str.substr(i + 1, 3);

			if (/^[0-7]{3}$/.test(octal)) {
				bytes.push(parseInt(octal, 8));
				i += 4; // \ + 3자리 숫자
				continue;
			}
		} // 이스케이프된 시퀀스가 아니면 현재 문자의 유니코드 값을 추가

		bytes.push(str.charCodeAt(i));

		i++;
	} // UTF-8로 디코딩

	return new TextDecoder("utf-8").decode(new Uint8Array(bytes));
}

/**
 * 파일 확장자를 확인하는 함수
 * @param {string} str - 확인할 문자열
 * @returns {boolean} - 확장자가 있는지 여부
 */
function hasFileExtension(str) {
	return /\.[a-zA-Z0-9]+$/.test(str);
}

/**
 * 입력 문자열을 배열로 변환하는 함수
 * @param {string} str - 입력 문자열
 * @returns {string[]} - 변환된 배열
 */
function parseStringToArray(str) {
	const regex = /"([^"]*)"|(\S+)/g;
	const tokens = [];
	let match;

	while ((match = regex.exec(str)) !== null) {
		// match[1]은 따옴표로 묶인 부분, match[2]는 그렇지 않은 부분
		tokens.push(match[1] || match[2]);
	}

	const decodedTokens = tokens.map((token) => decodeEscapedString(token));
	const result = [];
	let current = [];

	for (let token of decodedTokens) {
		if (hasFileExtension(token)) {
			current.push(token);
			result.push(current.join(" "));
			current = [];
		} else {
			current.push(token);
		}
	} // 남아있는 토큰이 있을 경우 추가

	if (current.length > 0) {
		result.push(current.join(" "));
	}

	return result;
}

// 변환 실행
const result = parseStringToArray(input);

console.log(result);
```

### Qwen 2.5 Coder 32B

```js
const inputString = '"Book/2024/9/\355\205\214\354\212\244\355\212\270 2.md" Book/Excalidraw/Drawing 2024-09-24 23.22.27.excalidraw.md Book/.obsidian/workspace.json decode-string.js sync-script.js';

// 먼저 문자열을 배열로 나눔 (공백 기준)
let items = inputString.split(' ');

let result = [];
let currentPath = '';
let inQuotes = false;

items.forEach((item, index) => {
		if (item.startsWith('"')) {
				// 큰따옴표로 시작하는 경우 큰따옴표 제거 후 현재 경로로 설정
				currentPath = item.slice(1);
				inQuotes = true;
				if (item.endsWith('"')) {
						// 큰따옴표로 끝나는 경우 배열에 추가
						currentPath = decodeURIComponent(escape(currentPath));
						result.push(currentPath);
						currentPath = '';
						inQuotes = false;
				}
		} else if (item.endsWith('"')) {
				// 큰따옴표로 끝나는 경우 큰따옴표 제거 후 배열에 추가
				currentPath += ' ' + item.slice(0, -1);
				currentPath = decodeURIComponent(escape(currentPath));
				result.push(currentPath);
				currentPath = '';
				inQuotes = false;
		} else if (inQuotes) {
				// 큰따옴표로 둘러싸인 문자열의 나머지 부분
				currentPath += ' ' + item;
		} else {
				// 파일 확장자가 있는 경우 배열에 추가
				if (/\.[^.]*$/.test(item)) {
						// 이전에 처리한 경로가 있다면 결합
						if (currentPath) {
								result.push(currentPath + ' ' + item);
								currentPath = '';
						} else {
								result.push(item);
						}
				} else {
						// 파일 확장자가 없는 경우 현재 경로로 설정
						if (currentPath) {
								currentPath += ' ' + item;
						} else {
								currentPath = item;
						}
				}
		}
});

// 마지막으로 처리한 경로가 있다면 배열에 추가
if (currentPath) {
		result.push(currentPath);
}

console.log(result);
```
