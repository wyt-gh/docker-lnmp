#基于原始的mysql:5,7镜像
FROM mysql:8.0
#支持中文的utf-8编码集
ENV LANG=C.UTF-8
# 初始化密码
ENV MYSQL_ROOT_PASSWORD=root
# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone