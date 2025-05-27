```shell
# 실행 중인 컨테이너 일괄 정지
docker stop $(docker ps -aq)

# 모든 컨테이너 삭제 (실행 중이던 정지된 것 모두)
docker rm -f $(docker ps -aq)

# 모든 도커 이미지 삭제
docker rmi -f $(docker images -q)

# 사용하지 않는 네트워크, 캐시, 볼륨 등 전체 정리
docker system prune -a --volumes
```

```
docker stop $(docker ps -aq)
docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -aq)
docker volume rm -f $(docker volume ls -q)
docker network rm $(docker network ls -q)
docker system prune -af --volumes
```