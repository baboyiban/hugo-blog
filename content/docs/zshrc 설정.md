---
title: zshrc 설정
date: 2024-10-16
time: 15:25
tags: []
---

# zshrc 설정

```zsh
# ~/.zshrc 설정

# 기존 테마 설정
# ZSH_THEME="agnoster"

# 테마 수정
ZSH_THEME="agnoster"

# 컴퓨터 이름 제거
prompt_context() {
  if [[ "$USER" != "$DEFAULT_USER" || -n "$SSH_CLIENT" ]]; then
    prompt_segment black default "%(!.%{%F{yellow}%}.)$USER"
  fi
}
```
