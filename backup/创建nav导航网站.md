效果
[具体效果点这里 https://nav3.cn](https://nav3.cn/)

特性
三不需：无需数据库、无需服务器、无需成本

发现导航 的理念就是做一款无需依赖后端服务既简单又方便，没有繁杂的配置和数据库等配置概念, 做到开箱即用。


1、打开 https://github.com/xjh22222228/nav 点击右上角Fork到自己仓库

<img width="2082" height="822" alt="Image" src="https://github.com/user-attachments/assets/ba921dd5-4ac0-4498-a2c7-74ab3380b0a9" />
2、确认

<img width="1954" height="1288" alt="Image" src="https://github.com/user-attachments/assets/228ec390-cf39-46a1-be30-6fd72b1bfa2c" />

3、创建 Token 后续访问后台需要, [打开 https://github.com/settings/tokens/new]输入 TOKEN，过期时间选为不过期， 权限全部勾选，TOKEN 只要不泄露是安全的。

<img width="1634" height="1912" alt="Image" src="https://github.com/user-attachments/assets/76156d1d-e18e-486e-b325-b2f12d3fa0e8" />

复制并保存TOKEN，如果这一步不保存就查看不了

<img width="1898" height="904" alt="Image" src="https://github.com/user-attachments/assets/f7dd8017-57f2-484c-b31b-f6abf58dc124" />

4、[打开 https://github.com/你的用户名/nav/settings/secrets/actions/new]把链接的用户名改成你的Github

Name填写 TOKEN 大写， Secret 填写刚刚保存的 TOKEN

<img width="1716" height="892" alt="Image" src="https://github.com/user-attachments/assets/36afc49d-ee6b-4a8f-b656-922beb083e35" />

5、开启Action

<img width="2626" height="1208" alt="Image" src="https://github.com/user-attachments/assets/30020dc9-088f-41e2-848d-4f30ab6acff9" />

6、编辑根目录 nav.config.yaml 文件，只需要修改仓库地址，把ID改成Github ID

<img width="1668" height="1258" alt="Image" src="https://github.com/user-attachments/assets/11de25ea-0c1d-4b82-9db6-7f549622eaef" />

7、这个时候Github Action 会自动启动
<img width="2396" height="744" alt="Image" src="https://github.com/user-attachments/assets/d384acf6-ae02-4b37-a79f-74c0207c45a3" />

8、完成！坐等2分钟，打开链接 [https://你的用户名.github.io/nav](https://xn--6qqv7i14ofosyrb.github.io/nav)

后台
项目包含后台，不需要额外部署，只需要将链接后面增加 system => https://nav3.cn/system
