需要你更新docker 镜像

1. 切换到分支feat-r2
2. `make up` 更新依赖
3. `yarn build`
4. 读取 .dockerversion，获取当前docker 版本号为，并自增版本号+1，置为 $DCVERSION
5. `docker build -t kencin/outline-r2:$DCVERSION .`
6. `docker push kencin/outline-r2:$DCVERSION`
7. 更新 .dockerversion