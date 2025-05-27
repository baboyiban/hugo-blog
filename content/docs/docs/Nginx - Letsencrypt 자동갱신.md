---
title: Letsencrypt 자동갱신
description: Letsencrypt 자동갱신
---

```bash
cd /bin
sudo vim letsencrypt.sh
```

```bash
#!/bin/sh
sudo service nginx stop
sudo certbot renew > ~/server/certbot/le_renew.log
sudo fuser -k 80/tcp
sudo service nginx start
```

```bash
sudo chmod +x letsencrypt.sh
```

```bash
sudo crontab -e
```

```bash
30 4 * * 0 letsencrypt.sh
```

```bash
sudo service cron start
```

# 참고사이트
https://www.owl-dev.me/blog/42
https://devlog.jwgo.kr/2019/04/16/how-to-lets-encrypt-ssl-renew/
