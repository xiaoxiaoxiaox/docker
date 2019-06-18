FROM mysql:5.7
LABEL maintainer="1605882811@qq.com"

# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY ./conf.d/my.cnf /etc/mysql/conf.d/my.cnf

# mysql backup scripts using crontab
# COPY ./scripts/ /data/mysql/backup/scripts/
# RUN crontab /data/mysql/backup/scripts/crontabfile
# RUN service cron start

CMD ["mysqld"]

EXPOSE 3306