FROM node
LABEL maintainer="1605882811@qq.com"

# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#node切换国内源
RUN npm config set registry http://registry.npm.taobao.org