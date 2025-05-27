```shell
# language-pack-ko 패키지 설치
apt-get install language-pack-ko

# 로케일 설치
locale-gen ko_KR.UTF-8
  
# 패키지 재설정
dpkg-reconfigure locales

# ko_KR.UTF-8 UTF-8 찾아 번호 입력
# (290 또는 299)
  
# ko_KR.UTF-8 이 써져 있는 번호 입력

# 환경변수 
export LANGUAGE=ko_KR.UTF-8
export LANG=ko_KR.UTF-8

# 적용
locale

# .bashrc 에 추가해 자동 실행 적용
nano ~/.bashrc

export LANGUAGE=ko_KR.UTF-8
export LANG=ko_KR.UTF-8

source ~/.bashrc
```