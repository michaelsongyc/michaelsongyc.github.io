# Mac下Mysql重置密码



具体的解决方案如下：
1. 苹果->系统偏好设置->最下边点mysql 在弹出页面中 关闭mysql服务（点击stop mysql server）
2. 进入终端输入：cd /usr/local/mysql/bin/
3. 回车后 登录管理员权限 sudo su
4. 输入mac的管理员密码
5. 回车后输入以下命令来禁止mysql验证功能 ./mysqld_safe --skip-grant-tables &
6. 回车后mysql会自动重启（偏好设置中mysql的状态会变成running）
7. 输入命令 ./mysql
8. 输入命令 FLUSH PRIVILEGES;
9. 输入命令 SET PASSWORD FOR 'root'@'localhost' = PASSWORD('你的新密码');
至此，密码已经修改成功，可以登录啦！
