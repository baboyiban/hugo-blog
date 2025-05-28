---
title: AI가 어려워하는 문제 2
date: 2024-09-28
time: 15:16
tags: []
---

# AI가 어려워하는 문제 2

**질문**

```tsx
import { Nav } from "@/interfaces/nav";
import Link from "next/link";

export default function DesktopNavbarMenu({ navData }: { navData: Nav }) {
  return (
    <div className="hidden md:visible md:block h-full">
      <ul className="h-full flex">
        {navData.children?.map((item) => (
          <li
            key={item.path}
            className="px-2 h-full flex items-center group cursor-pointer"
          >
            <p className="text-base">{item.name}</p>
            <div className="absolute left-0 top-[3rem] bg-white/80 backdrop-blur-2xl backdrop-saturate-200 w-full group-hover:grid group-hover:visible hidden grid-col-[30px]">
              <div className="max-w-[80rem] w-full max-h-[calc(100dvh-3rem)] m-auto px-4 py-16 overflow-x-auto">
                {renderDesktopNavbarMenu(item)}
              </div>
            </div>
          </li>
        ))}
      </ul>
    </div>
  );
}

function renderDesktopNavbarMenu(nav: Nav) {
  return (
    <div className="text-center flex flex-row gap-8 justify-center">
      {(nav.children ?? []).filter((children) => children.type === "item")
        .length > 0 && (
        <ul className="flex flex-col">
          {(nav.children ?? [])
            .filter((children) => children.type === "item")
            .map((children) => (
              <li key={children.path} className="">
                <Link href={children.path} className="bold p-1 cursor-pointer">
                  {children.name}
                </Link>
              </li>
            ))}
        </ul>
      )}
      {(nav.children ?? []).filter((children) => children.type === "category")
        .length > 0 && (
        <ul>
          {(nav.children ?? [])
            .filter((children) => children.type === "category")
            .map((children) => (
              <li key={children.path} className="">
                <p className="mb-4 text-base">{children.name}</p>
                {renderDesktopNavbarMenu(children)}
              </li>
            ))}
        </ul>
      )}
    </div>
  );
}
```

왜 cursor-pointer가 안먹힐까?

## 해결

정답은 li 태그에 있는 cursor-pointer 속성을 제거하는 것이다 그리고 p 요소나 Link 요소에 cursor-pointer를 적용하니 의도하는 대로 되었다

## 모델

claude sonnet 3.5 실패
claude opus 3 해결

deepseek chat 2.5 해결

copilot chat gpt 4o 실패

chat gpt 4o 해결
chat gpt o1-mini 해결

gemini 1.5 pro 002 실패
